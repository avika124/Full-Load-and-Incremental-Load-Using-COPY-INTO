Azure Data Factory - Countries & Customer Data Pipelines
Project Overview
This Azure Data Factory project contains automated pipelines for:

Fetching country data from REST Countries API
Processing customer and product data with conditional logic
Automated scheduling and parameter passing between pipelines

Architecture
Pipeline Structure
├── Countries_Data_Pipeline          # Main countries data pipeline
├── Customer_Data_Pipeline           # Customer data processing
├── Product_Data_Child_Pipeline      # Child pipeline for product data
├── Linked Services/
│   ├── RestCountriesAPI            # REST API connection
│   ├── DatabaseConnection          # SQL Database
│   └── ADLSConnection              # Azure Data Lake Storage
└── Datasets/
    ├── RestCountriesDataset        # REST API dataset
    ├── CustomerDataset             # Customer table dataset
    ├── ProductDataset              # Product table dataset
    └── JSONOutputDataset           # JSON file output
    
Pipeline Details
1. Countries Data Pipeline
Purpose: Fetch data for 5 countries from REST Countries API and save as JSON files
Countries: India, US, UK, China, Russia
API Endpoint: https://restcountries.com/v3.1/name/{country}
Output: Individual JSON files named by country (e.g., india.json, us.json)
Schedule: Runs twice daily at 12:00 AM and 12:00 PM IST
Activities:

ForEach Loop (countries array)
Copy Activity (REST API to JSON file)

2. Customer Data Pipeline
Purpose: Copy customer data from database to ADLS based on record count
Trigger Condition: Only runs if customer record count > 500
Activities:

Lookup (get customer count)
If Condition (check count > 500)
Copy Activity (DB to ADLS)
Execute Pipeline (call child pipeline)

Parameter Passed: Customer record count to child pipeline
3. Product Data Child Pipeline
Purpose: Copy product data if customer count exceeds 600
Trigger Condition: Runs only if customer count > 600 (passed as parameter)
Activities:

If Condition (check parameter > 600)
Copy Activity (product table to ADLS)

Setup Instructions
Prerequisites

Azure Data Factory instance
Azure Data Lake Storage Gen2
SQL Database with customer and product tables
Git repository (Azure DevOps or GitHub)

Git Integration

Connect ADF to your Git repository
Set collaboration branch to main
Create feature branches for development
Use pull requests for code reviews

Deployment Steps

Create Linked Services:

REST Countries API
SQL Database connection
ADLS Gen2 storage


Create Datasets:

REST API dataset with country parameter
Database datasets for customer/product tables
JSON output dataset for country files


Build Pipelines:

Countries pipeline with ForEach loop
Customer pipeline with conditional logic
Product child pipeline with parameters


Configure Triggers:

Schedule trigger for countries pipeline (12 AM & 12 PM IST)
Customer pipeline triggered manually or by other events



Configuration Parameters
Countries Pipeline
json{
  "countries": ["india", "us", "uk", "china", "russia"]
}
Customer Pipeline
json{
  "minimumRecordCount": 500
}
Product Child Pipeline
json{
  "customerCountThreshold": 600
}
Monitoring & Troubleshooting
Key Metrics to Monitor

Pipeline success/failure rates
API response times
Data transfer volumes
Record counts processed

Common Issues

API Rate Limits: REST Countries API may have rate limiting
Network Connectivity: Ensure ADF can reach external APIs
Storage Permissions: Verify ADLS write permissions
Parameter Passing: Check parameter types and values between pipelines

Logging

Monitor pipeline runs in ADF Studio
Check activity-level logs for detailed error messages
Set up Azure Monitor alerts for pipeline failures

Maintenance
Regular Tasks

Monitor storage usage in ADLS
Review pipeline performance metrics
Update API endpoints if changed
Validate data quality in output files

Version Control

Use Git branches for all changes
Create pull requests for code reviews
Tag releases for production deployments
Maintain environment-specific configurations

Security Considerations

Use Azure Key Vault for sensitive connection strings
Implement proper RBAC for ADF resources
Encrypt data at rest in ADLS
Use managed identities where possible
