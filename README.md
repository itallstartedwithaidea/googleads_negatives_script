// 🛑 Stop Wasting Ad Spend: Auto-Block Irrelevant Search Terms in Google Ads
// Author: @startwithaidea | https://itallstartedwithaidea.com

function main() {
  const CAMPAIGN_NAME = 'Your Campaign Name Here';
  const TRIGGER_WORDS = ['buy', 'buyer', 'buying', 'sell', 'seller', 'selling', 'cheap', 'free', 'discount', 'used', 'second hand', 'refurbished'];
  const DAYS_TO_ANALYZE = 30;
  const ADD_AT_CAMPAIGN_LEVEL = true;

  Logger.log('Starting negative keyword automation...');
  Logger.log(`Analyzing ${DAYS_TO_ANALYZE} days of data for campaign: ${CAMPAIGN_NAME}`);

  try {
    const REPORT = AdsApp.report(`
      SELECT Query, CampaignName, AdGroupId, AdGroupName, Impressions, Clicks, Cost
      FROM SEARCH_QUERY_PERFORMANCE_REPORT
      WHERE CampaignName = '${CAMPAIGN_NAME}'
        AND Impressions > 0
      DURING LAST_${DAYS_TO_ANALYZE}_DAYS
    `);

    const existingNegatives = getExistingNegatives(CAMPAIGN_NAME, ADD_AT_CAMPAIGN_LEVEL);

    let addedCount = 0;
    let skippedCount = 0;
    let totalCostSaved = 0;

    const rows = REPORT.rows();
    while (rows.hasNext()) {
      const row = rows.next();
      const query = row['Query'].toLowerCase().trim();
      const adGroupId = row['AdGroupId'];
      const adGroupName = row['AdGroupName'];
      const cost = parseFloat(row['Cost']) || 0;

      const containsTriggerWord = TRIGGER_WORDS.some(word => query.includes(word));
      const isAlreadyNegative = existingNegatives.has(query);

      if (containsTriggerWord && !isAlreadyNegative) {
        if (ADD_AT_CAMPAIGN_LEVEL) {
          addNegativeAtCampaignLevel(CAMPAIGN_NAME, query);
          Logger.log(`✓ Added campaign negative: [${query}] - Saved $${cost.toFixed(2)}`);
        } else {
          addNegativeAtAdGroupLevel(adGroupId, query, adGroupName);
          Logger.log(`✓ Added ad group negative: [${query}] in ${adGroupName} - Saved $${cost.toFixed(2)}`);
        }
        addedCount++;
        totalCostSaved += cost;
      } else if (isAlreadyNegative) {
        skippedCount++;
      }
    }

    Logger.log(`\n=== SUMMARY ===`);
    Logger.log(`Negative keywords added: ${addedCount}`);
    Logger.log(`Duplicates skipped: ${skippedCount}`);
    Logger.log(`Estimated future cost savings: $${totalCostSaved.toFixed(2)}`);

  } catch (error) {
    Logger.log(`❌ Error: ${error.message}`);
  }
}

function getExistingNegatives(campaignName, campaignLevel) {
  const existingNegatives = new Set();
  try {
    const campaign = AdsApp.campaigns().withCondition(`Name = '${campaignName}'`).get().next();

    if (campaignLevel) {
      const negatives = campaign.negativeKeywords().get();
      while (negatives.hasNext()) {
        const neg = negatives.next();
        existingNegatives.add(neg.getText().replace(/\[|\]/g, '').toLowerCase());
      }
    } else {
      const adGroups = campaign.adGroups().get();
      while (adGroups.hasNext()) {
        const adGroup = adGroups.next();
        const negatives = adGroup.negativeKeywords().get();
        while (negatives.hasNext()) {
          const neg = negatives.next();
          existingNegatives.add(neg.getText().replace(/\[|\]/g, '').toLowerCase());
        }
      }
    }
  } catch (error) {
    Logger.log(`Warning: Could not retrieve existing negatives - ${error.message}`);
  }
  return existingNegatives;
}

function addNegativeAtCampaignLevel(campaignName, query) {
  const campaign = AdsApp.campaigns().withCondition(`Name = '${campaignName}'`).get().next();
  campaign.createNegativeKeyword(`[${query}]`);
}

function addNegativeAtAdGroupLevel(adGroupId, query, adGroupName) {
  const adGroup = AdsApp.adGroups().withIds([parseInt(adGroupId)]).get().next();
  adGroup.createNegativeKeyword(`[${query}]`);
}
