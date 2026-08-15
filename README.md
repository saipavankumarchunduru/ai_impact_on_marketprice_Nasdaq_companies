# ai_impact_on_marketprice_Nasdaq_companies
Analysis of trends in AI for Marketprice of stock value in different significant dates
Absolutely 👍 Below is a **GitHub-ready `README.md`** written as if you developed the project yourself. You can copy and paste it directly into GitHub.

````markdown
# 📈 AI Stock Impact Alert & Historical Analysis Workflow

An automated stock market analysis workflow built using **n8n**, **Marketstack API**, and **n8n Data Tables**.

The project analyzes historical stock price movements for major technology companies and identifies significant percentage changes. It is designed to help study large stock movements across different years and analyze potential market impact during important AI-related periods.

---

## 🚀 Project Overview

This project automatically:

1. Stores selected companies and alert thresholds.
2. Processes historical dates year by year.
3. Fetches historical stock market data from the Marketstack API.
4. Calculates the percentage price change for each stock.
5. Compares the calculated change against a configured threshold.
6. Generates an alert when the threshold is crossed.
7. Stores triggered alerts in an `alert_history` table.
8. Supports batch processing to avoid API rate-limit issues.

The workflow focuses on companies that have been significantly affected by developments in Artificial Intelligence, including:

- NVIDIA
- Tesla
- Apple
- Microsoft
- Amazon
- Alphabet / Google

---

# 🏗️ Architecture

```text
                    ┌─────────────────────┐
                    │   Schedule Trigger  │
                    │   / Manual Trigger  │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │   Get Company Data  │
                    │   n8n Data Table    │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │    Aggregate Data   │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Split Historical    │
                    │ Dates               │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │  Loop Over Items    │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │  Split Companies    │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Marketstack API     │
                    │ Historical EOD Data │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Calculate Percentage│
                    │ Change              │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ IF Threshold Check  │
                    └──────────┬──────────┘
                               │
                        TRUE   │
                               ▼
                    ┌─────────────────────┐
                    │ Prepare Alert Data  │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ alert_history Table │
                    └─────────────────────┘
````

---

# 🛠️ Technologies Used

| Technology             | Purpose                                       |
| ---------------------- | --------------------------------------------- |
| n8n                    | Workflow automation                           |
| Marketstack API        | Historical stock market data                  |
| n8n Data Tables        | Store company configuration and alert history |
| JavaScript Expressions | Calculations and dynamic field mapping        |
| HTTP Request           | Fetch stock market data                       |
| IF Nodes               | Evaluate alert conditions                     |
| Switch Nodes           | Route alerts based on alert type              |

---

# 📊 Companies Data Table

The workflow uses an n8n Data Table to store company configurations.

## Suggested Table Name

```text
companies
```

## Table Structure

| Column       | Description                  |
| ------------ | ---------------------------- |
| id           | Unique company record ID     |
| symbol       | Stock ticker symbol          |
| company_name | Company name                 |
| alert_type   | Type of alert                |
| target_value | Normal percentage threshold  |
| major_target | Major percentage threshold   |
| is_active    | Enable or disable monitoring |
| created_at   | Record creation timestamp    |
| updated_at   | Record update timestamp      |

---

## Example Company Configuration

| Symbol | Company              | Alert Type        | Target Value | Major Target |
| ------ | -------------------- | ----------------- | -----------: | -----------: |
| NVDA   | NVIDIA Corporation   | PERCENTAGE_CHANGE |            5 |           10 |
| TSLA   | Tesla                | PERCENTAGE_CHANGE |            5 |           10 |
| AAPL   | Apple Inc            | PERCENTAGE_CHANGE |            4 |            8 |
| MSFT   | Microsoft Corp       | PERCENTAGE_CHANGE |            4 |            8 |
| AMZN   | Amazon.com Inc       | PERCENTAGE_CHANGE |            4 |            8 |
| GOOGL  | Alphabet Inc Class A | PERCENTAGE_CHANGE |            4 |            8 |

---

# 📅 Historical Dates

Historical dates can be stored as an array and processed individually.

Example:

```json
[
  "2023-05-25",
  "2024-05-23",
  "2025-05-22"
]
```

The workflow uses a **Split Out** node to process each date individually.

Example:

```text
Date Array
    │
    ▼
Split Out
    │
    ├── 2023-05-25
    ├── 2024-05-23
    └── 2025-05-22
```

Each date is then processed for all active companies.

---

# 🔄 Workflow Processing

## Step 1: Get Active Companies

The workflow retrieves active company records from the Data Table.

Example condition:

```text
is_active = true
```

Only active companies continue through the workflow.

---

## Step 2: Aggregate Data

The company data and historical dates are prepared for processing.

The goal is to create a structure similar to:

```json
{
  "historical_dates": [
    "2023-05-25",
    "2024-05-23",
    "2025-05-22"
  ],
  "companies": [
    {
      "symbol": "NVDA",
      "target_value": 5,
      "major_target": 10
    }
  ]
}
```

---

## Step 3: Split Historical Dates

The historical date array is split into individual workflow items.

Example:

```text
2023-05-25
2024-05-23
2025-05-22
```

---

## Step 4: Loop Through Dates

The **Loop Over Items** node processes each historical date.

This allows the workflow to analyze stock performance year by year.

---

## Step 5: Split Companies

The company array is split so that each company becomes an individual workflow item.

Example:

```text
NVDA
TSLA
AAPL
MSFT
AMZN
GOOGL
```

Each company is processed independently.

---

# 🌐 Marketstack API Integration

The workflow uses the Marketstack EOD endpoint.

Example request parameters:

```json
{
  "symbols": "NVDA",
  "date_from": "2023-05-25",
  "date_to": "2023-05-25"
}
```

The API returns historical End-of-Day market information.

Example response:

```json
{
  "open": 304.10,
  "high": 312.50,
  "low": 300.20,
  "close": 307.50,
  "volume": 45000000
}
```

---

# 📈 Percentage Change Calculation

The workflow calculates the percentage change between the opening and closing price.

Formula:

```text
Percentage Change =
((Close Price - Open Price) / Open Price) × 100
```

n8n expression:

```javascript
{{
  (($json.close - $json.open) / $json.open) * 100
}}
```

Example:

```text
Open Price  = 100
Close Price = 107

Percentage Change =
((107 - 100) / 100) × 100

Percentage Change = 7%
```

---

# 🚨 Alert Logic

The workflow compares the calculated percentage change with the configured threshold.

## Normal Alert

```text
percentage_change >= target_value
```

Example:

```text
Target Value = 5%
Actual Change = 7%

7 >= 5

Result: TRUE
```

The workflow generates an alert.

---

## Major Movement Threshold

A second threshold is stored using:

```text
major_target
```

Example:

```text
target_value = 5%
major_target = 10%
```

This allows future expansion of the workflow.

Possible logic:

```text
percentage_change >= major_target
```

Example:

```text
Actual Change = 12%

12 >= 10

Result: Major Market Movement
```

---

# 🔀 Switch Node

The workflow uses a Switch node to support multiple alert types.

Supported alert type:

```text
PERCENTAGE_CHANGE
```

The architecture can also be extended to support:

```text
PRICE_ABOVE
PRICE_BELOW
PERCENTAGE_CHANGE
```

Current project focus:

```text
PERCENTAGE_CHANGE
```

---

# 📝 Prepare Percentage Alert

When the IF node returns `TRUE`, the workflow prepares the alert data.

Example fields:

```text
alert_triggered
alert_message
alert_type
alert_value
actual_value
```

Example configuration:

### alert_triggered

```text
true
```

### alert_type

```text
PERCENTAGE_CHANGE
```

### alert_value

```javascript
{{ $json.percentage_change }}
```

### actual_value

```javascript
{{ $json.percentage_change }}
```

### alert_message

```javascript
{{
  $json.symbol +
  " changed by " +
  $json.percentage_change.toFixed(2) +
  "% on " +
  $json.historical_date
}}
```

Example generated alert:

```text
NVDA changed by 7.35% on 2023-05-25
```

---

# 🗃️ Alert History

Triggered alerts are stored in a separate Data Table.

## Table Name

```text
alert_history
```

## Suggested Fields

| Field        | Description                  |
| ------------ | ---------------------------- |
| id           | Unique alert ID              |
| symbol       | Stock ticker                 |
| alert_type   | Type of alert                |
| target_value | Normal threshold             |
| major_target | Major movement threshold     |
| actual_value | Calculated percentage change |
| message      | Alert description            |
| status       | Alert status                 |
| triggered_at | Timestamp of alert           |

Example record:

```json
{
  "symbol": "NVDA",
  "alert_type": "PERCENTAGE_CHANGE",
  "target_value": 5,
  "major_target": 10,
  "actual_value": 7.35,
  "message": "NVDA changed by 7.35% on 2023-05-25",
  "status": "TRIGGERED",
  "triggered_at": "2026-08-15T15:30:00"
}
```

---

# ⏱️ Batch Processing and Rate Limit Handling

To avoid exceeding API limits, companies can be processed in batches.

Example configuration:

```text
Batch Size: 2 companies
Wait Time: 2 minutes
```

Processing flow:

```text
6 Companies

        │
        ▼

Batch 1
NVDA
TSLA

        │
        ▼

Wait 2 Minutes

        │
        ▼

Batch 2
AAPL
MSFT

        │
        ▼

Wait 2 Minutes

        │
        ▼

Batch 3
AMZN
GOOGL
```

This design helps reduce the risk of API rate-limit errors.

---

# 🔐 Environment Variables

Sensitive information such as API keys should not be hardcoded in the workflow.

Recommended configuration:

```text
MARKETSTACK_API_KEY=your_api_key_here
```

The API key should be stored securely using:

* n8n Credentials
* Environment Variables
* Secret Management

Example:

```text
access_key = {{ $env.MARKETSTACK_API_KEY }}
```

Do not commit API keys to GitHub.

---

# ▶️ How to Run

## 1. Import the Workflow

Import the n8n workflow JSON file.

```text
n8n → Workflows → Import from File
```

---

## 2. Create Data Tables

Create the following tables:

```text
companies
alert_history
```

---

## 3. Add Company Records

Insert the companies and their percentage thresholds.

Example:

```text
NVDA → Target: 5 → Major Target: 10
TSLA → Target: 5 → Major Target: 10
AAPL → Target: 4 → Major Target: 8
MSFT → Target: 4 → Major Target: 8
AMZN → Target: 4 → Major Target: 8
GOOGL → Target: 4 → Major Target: 8
```

---

## 4. Configure Marketstack API

Add your API key securely.

---

## 5. Configure Historical Dates

Update the historical date array.

Example:

```json
[
  "2023-05-25",
  "2024-05-23",
  "2025-05-22"
]
```

---

## 6. Execute the Workflow

The workflow can be started manually or automatically.

### Manual

```text
Execute Workflow
```

### Automated

Use an n8n Schedule Trigger.

Example:

```text
Run daily
Run weekly
Run monthly
```

---

# 📂 Suggested Repository Structure

```text
ai-stock-impact-analysis/
│
├── README.md
│
├── workflows/
│   └── stock-impact-workflow.json
│
├── data/
│   ├── companies.json
│   └── historical-dates.json
│
├── docs/
│   ├── architecture.md
│   └── workflow-explanation.md
│
├── screenshots/
│   └── workflow.png
│
├── .env.example
│
└── LICENSE
```

---

# 🔮 Future Improvements

Planned improvements for the project include:

* [ ] Separate normal and major market movement alerts
* [ ] Add `major_target` IF condition
* [ ] Support price above alerts
* [ ] Support price below alerts
* [ ] Add email notifications
* [ ] Add Slack notifications
* [ ] Add Telegram alerts
* [ ] Create a Power BI dashboard
* [ ] Store data in PostgreSQL
* [ ] Add AI news sentiment analysis
* [ ] Correlate stock movements with AI-related news events
* [ ] Add historical trend visualization
* [ ] Add automatic yearly date generation
* [ ] Add more AI-related companies

---

# 🎯 Project Objective

The main objective of this project is to build a practical automation system that can analyze historical stock movements of major technology companies and identify significant percentage changes.

The project combines:

```text
Workflow Automation
        +
API Integration
        +
Data Processing
        +
Conditional Logic
        +
Historical Market Analysis
```

It also demonstrates how **n8n can be used to build real-world data automation workflows without requiring a traditional application backend**.

---

# 📚 Key Concepts Demonstrated

This project demonstrates practical knowledge of:

* Workflow automation using n8n
* REST API integration
* HTTP GET requests
* Query parameters
* JSON data processing
* Data transformation
* Arrays and objects
* Looping in n8n
* Split Out nodes
* Aggregate nodes
* Loop Over Items
* Switch nodes
* IF conditions
* JavaScript expressions
* Percentage calculations
* Data Tables
* Batch processing
* Wait nodes
* API rate-limit handling
* Scheduled workflows
* Environment variables and secret management

---

# ⚠️ Disclaimer

This project is created for educational and analytical purposes only.

The generated alerts and historical stock analysis should not be considered financial advice or investment recommendations.

Always perform independent research before making financial decisions.

---

# 📄 License

This project is licensed under the **MIT License**.

You are free to use, modify, and distribute this project under the terms of the MIT License.

See the `LICENSE` file for more information.

---

# 👨‍💻 Author

Created as a personal project to explore:

* Artificial Intelligence
* Workflow Automation
* API Integration
* Data Engineering Concepts
* Historical Stock Market Analysis
* AI Impact on Technology Companies

---

## ⭐ If You Like This Project


One small improvement I recommend before uploading to GitHub: replace any exposed Marketstack API key in your workflow/export with an environment variable or n8n credential. 🔐
