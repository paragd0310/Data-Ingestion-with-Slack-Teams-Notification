# Data-Ingestion-with-Slack-Teams-Notification

![image](https://github.com/user-attachments/assets/f27a6fb8-ecda-4337-8807-18bafa5d7ba8)


## Overview
This scenario demonstrates how to incrementally ingest data from Azure SQL Database to Azure Data Lake Storage Gen2 using Azure Data Factory. Upon successful completion of the data load, a Slack notification is sent.

---

##  Objective
- Ingest data from Azure SQL table to ADLS Gen2 (CSV format)
- Send Slack notifications based on success/failure of ingestion

---

##  Azure Resources Used
| Resource Name        | Type                    |
|----------------------|-------------------------|
| bootcamp-rg          | Resource Group          |
| bootcampadf99        | Azure Data Factory      |
| bootcampdatalake99   | ADLS Gen2 Storage Acct  |
| bootcampdb99         | Azure SQL Server        |
| bootcampdb9999       | Azure SQL Database      |

---

## SQL Table + Sample Data
```sql
CREATE TABLE SalesData (
    SaleID INT PRIMARY KEY,
    ProductName NVARCHAR(100),
    Quantity INT,
    SaleAmount DECIMAL(10, 2),
    SaleDate DATE
);

INSERT INTO SalesData VALUES
(1, 'Apples', 20, 50.00, '2024-03-01'),
(2, 'Oranges', 15, 37.50, '2024-03-02'),
(3, 'Bananas', 30, 45.00, '2024-03-03');
```

---

##  Linked Services
- **AzureSqlLS**: Linked to Azure SQL DB
- **AzureStorageLS**: Linked to ADLS Gen2

---

##  Datasets
- **AzureSqlTable1**: Source (Azure SQL SalesData table)
- **DelimitedText1**: Sink (CSV in ADLS Gen2 at `raw/sales/dbo.SalesData.txt`)

---

## Pipeline: Ingest_SalesData_With_Notification
### Activities:
1. **Copy_SalesData**
   - Source: Azure SQL Table (SalesData)
   - Sink: ADLS Gen2 (`raw/sales/dbo.SalesData.txt`)

2. **NotifySlack (Web Activity)**
   - URL: Slack Webhook URL
   - Method: POST
   - Body:
     ```json
     {
       "text": "ADF pipeline successfully copied SalesData to ADLS Gen2."
     }
     ```
   - Headers:
     ```
     Content-Type: application/json
     ```

---

##  Issues Faced & Fixes
| Issue | Fix |
|-------|-----|
| ADLS file path not valid | Ensure a folder is mentioned (`raw/sales/filename.txt`) |
| Slack webhook failed (`no_service`) | Switched from Inline to Linked Service in Web Activity |

---

##  Git Integration
- GitHub Repo: `Incremental-Data-Loading-and-Notifications`
- Branch: `main`
- Publish branch: `adf_publish`

---

##  ARM Template (Optional)
Exported via ADF Studio for redeployment and CI/CD:
- `ARMTemplateForFactory.json`
- `ARMTemplateParametersForFactory.json`

---

##  Next Steps
- Add filter logic to ingest only new records (Incremental Load)
- Schedule pipeline trigger
- Monitor via ADF logs and Slack alerts

---

##  Output
- File exported: `dbo.SalesData.txt` in ADLS Gen2
- Slack alert sent successfully

