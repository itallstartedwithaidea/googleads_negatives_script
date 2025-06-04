# 🛑 Stop Wasting Ad Spend: Auto-Block Irrelevant Search Terms in Google Ads

Automatically identify and block irrelevant queries in your Google Ads Search campaigns using exact match negative keywords. This script is perfect for B2B and service-based advertisers who want to eliminate wasted spend from close variants like "buy" or "cheap" that slip through standard negatives.

---

## 📌 Why This Matters
Google’s close variants often allow irrelevant queries through. Negative keywords **do not** block these variants unless added as exact match. That’s where this script comes in.

---

## 🚀 What This Script Does
- Scans your search term reports for specified trigger words (e.g., "cheap", "buy")
- Checks if they’re already blocked
- Automatically adds them as **exact match negative keywords** at campaign or ad group level
- Saves you money and improves campaign relevance

---

## 🔧 Configuration
Inside the script, adjust the following variables:

```javascript
const CAMPAIGN_NAME = 'Your Campaign Name Here';
const TRIGGER_WORDS = ['buy', 'cheap', 'sell', 'used'];
const DAYS_TO_ANALYZE = 30;
const ADD_AT_CAMPAIGN_LEVEL = true; // Or false for ad group level
```

---

## 📥 How to Use It

### Step 1: Paste Script in Google Ads
- Go to Google Ads → Tools & Settings → Scripts
- Click the **+** to add a new script
- Paste this code

### Step 2: Configure Your Campaign Details
- Update the `CAMPAIGN_NAME`
- Add relevant `TRIGGER_WORDS`

### Step 3: Preview & Execute
- Click **Preview** to validate
- When ready, click **Run**

### Step 4: Schedule
- Automate it weekly or bi-weekly
- Monitor the logs and refine trigger terms monthly

---

## 📈 Pro Tips
- Run on high-spend campaigns first
- Avoid over-blocking: review before adding new trigger words
- Use logs to estimate cost savings

---

## 💬 Feedback or Issues?
- Submit an issue or improvement via pull request
- Follow [@startwithaidea](https://www.reddit.com/user/startwithaidea) for updates and tips

---

## 🧠 Credits
Built and refined by the PPC community. Enhanced for real-world use cases by [It All Started With A Idea](https://itallstartedwithaidea.com).

---

## 🔒 Permissions Required
- Read access to `SEARCH_QUERY_PERFORMANCE_REPORT`
- Permission to manage negative keywords

---

## 📜 License
MIT – use it, improve it, share it.
