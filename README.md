# Steam Market Price Scraper

This is a Python-based tool that retrieves current pricing information from the **Steam Community Market** for selected game items, and saves the results into an Excel spreadsheet.

## Features

- Retrieves market data (lowest price, median price, and volume) from Steam using their API.
- Supports multiple games (via `appid`), like **Rust** and **CS:GO**.
- Saves data into an Excel file (`.xlsx`) for easy tracking and analysis.
- Uses a configuration file to simplify setup and changes.
- Handles request limits and failures gracefully.

---

## File Overview

### 1. `config.ini`
This file contains all configurable parameters including the Steam API URL, list of items to track, output Excel file name, and request behavior.

### 2. `main.py`
The main script that:
- Loads configuration
- Fetches item data from the Steam Market
- Creates or loads an Excel file
- Saves data to the file

---

## Installation & Requirements

**Dependencies** (install with `pip install -r requirements.txt` or manually):

```bash
requests
openpyxl
```

## Tips

- **Limit request frequency:** Steam might block or throttle you if you send too many requests too fast. A delay of `1` second is generally safe.
- **Back up your Excel file** before running the script if it contains data you don’t want to lose.
- **Modify item names carefully** — they must match **exactly** as shown on the [Steam Community Market](https://steamcommunity.com/market/).
- **You can change the `currency` value in the config** [More currency codes here](https://partner.steamgames.com/doc/store/pricing/currencies)
  - `1` = USD
  - `3` = EUR
  - `9` = NOK  

---

## Notes

### What happens if an item isn't found or fails?
It will print a "Failed" message in the console, and the Excel row will be filled with `-`.

How many items can I track?
There's no strict limit, but keep in mind:
- Steam may block you if requests are too frequent or excessive.
- A delay of 1 second is recommended for reliability.

Add items from different games
Just add another app ID matching the game ID which can be viewed ine the URL on the steam page of the game, and corresponding item list in the `my_items` dictionary in your config.

---

## 📤 Google Sheets Implementation (Alternative Idea)

If you prefer using **Google Sheets** instead of Excel, here's a basic idea for implementation:

### 📎 Concept

Use **Google Apps Script** (JavaScript) to fetch the same API data and populate your sheet.

### Example Snippet

```javascript
function getSteamItemData() {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  const items = [
    { appid: "252490", name: "Art Deco Door" },
    { appid: "730", name: "AK-47 | Red Laminate (Battle-Scarred)" }
  ];
  
  items.forEach((item, index) => {
    const url = `https://steamcommunity.com/market/priceoverview/?appid=${item.appid}&currency=9&market_hash_name=${encodeURIComponent(item.name)}`;
    const response = UrlFetchApp.fetch(url);
    const json = JSON.parse(response.getContentText());

    sheet.getRange(index + 2, 1).setValue(item.name);
    sheet.getRange(index + 2, 2).setValue(json.lowest_price || "-");
    sheet.getRange(index + 2, 3).setValue(json.volume || "-");
    sheet.getRange(index + 2, 4).setValue(json.median_price || "-");

    Utilities.sleep(2000); // Pause between requests
  });
}
```
