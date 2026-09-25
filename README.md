# Qorenext CRM MCP

> Company hierarchy analysis and address verification — via MCP

Qorenext CRM MCP is a [Model Context Protocol](https://modelcontextprotocol.io) server that enables Claude and other AI clients to perform company hierarchy analysis and address verification directly in chat.

---
## Prerequisites:
•	Node.js and npm installed (npm is used to install the connector package).  
•	A QoreNext CRM api key.  
•	An active product subscription is required to use Qore MCP.  
•	Administrator or standard access to edit files in your user profile.

## Get your API key

Sign up at **https://qorenext-app.azurewebsites.net/signup** to get your `QORENEXT_API_KEY`.

## Steps to get your API Key
1. Sign up at **https://qorenext-app.azurewebsites.net/signup**. 
2. Select the CRM On-Demand Data Quality & Enrichment product. 
3. Buy your plan - An active product subscription is required to use Qore MCP.
4. Open Profile → APIs. 
5. Click Generate API Key. 
6. Give your API key a name and click Create. 
7. Copy the API key for use in Claude. 
---

## How To Connect:

### A) Claude Code
```bash
claude mcp add --transport http qorenext-mcp \
  "https://mcp.qorenext.com/crm" \
  --header "X-API-Key: YOUR_API_KEY"
```
### B) Claude Desktop

## Prerequisites:
•	Claude Desktop installed on Windows.  
•	Node.js and npm installed (npm is used to install the connector package).  
•	A QoreNext CRM api key.  
•	An active product subscription is required to use Qore MCP.  
•	Administrator or standard access to edit files in your user profile.  

## Install Node.js  
- Download and install the **LTS version** from [nodejs.org](https://nodejs.org/).
- The installation includes **npm** and **npx**.
- Open **Terminal** (Mac) or **Command Prompt/PowerShell** (Windows).
- Verify the installation: `bash below commands  
node -v  
npx -v  

Both commands should display a version number.  
If you see "command not found", restart your terminal and try again.  


### ## Steps to Setup the Claude Desktop

### 1. Open Claude Desktop  
Launch the **Claude Desktop** application on your computer.  

### 2. Open Settings
Go to:
**Menu → File → Settings**

### 3. Open Developer Settings
In the Settings window, select **Developer** from the left-side menu.  

### 4. Open the Config File

Click **Edit Config** to open the `claude_desktop_config.json` file.  
Remove the existing command in the file and paste the configuration provided below in Json.

**Windows default location:**  
C:\Users\<YourUsername>\AppData\Roaming\Claude\claude_desktop_config.json or `~/Library/Application Support/Claude/claude_desktop_config.json` (Mac)

### 5. Add the QoreNext MCP Server
Add the following configuration to claude_desktop_config.json and replace qore_xxxxx with your real API key.

```json
{
  "mcpServers": {
    "qorenext-mcp-crm": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://mcp.qorenext.com/crm",
        "--header",
        "X-API-Key:qore_xxxxx"
      ]
    }
  }
}
```
Watch your JSON syntax
Make sure the JSON braces and commas match exactly — one missing comma will break the file.

### 6. Save the file
Save your changes (Ctrl+S) and close the editor.

### 7. Restart Claude Desktop
Fully quit Claude Desktop by closing it, exiting it from the system tray/taskbar, and using Task Manager to End Task for any remaining Claude processes running in the background; then reopen Claude Desktop.

### C) Cursor / Windsurf
Add to `.cursor/mcp.json` or `.windsurf/mcp.json`:

```json
{
  "mcpServers": {
    "qorenext-mcp-crm": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://mcp.qorenext.com/crm",
        "--header",
        "X-API-Key:qore_xxxxx"
      ]
    }
  }
}
```

---

## Tools

| Tool | Auth | Description |
|---|---|---|
| `health_check` | ❌ Public | Verify server is running, get version info |
| `submit_address_verification` | ✅ API Key | Submit companies for address verification and validation |
| `submit_hierarchy_creation` | ✅ API Key | Submit companies for corporate hierarchy analysis, including top-parent and subsidiary relationships |
| `submit_duplicates` | ✅ API Key | Submit CRM account records from JSON, CSV, or Excel files for duplicate detection |
| `get_request_status` | ✅ API Key | Poll the status and retrieve results for address verification, hierarchy analysis, or duplicate-detection requests |

---

### `health_check`
Verify the server is running. No API key required.
```
check if Qorenext CRM MCP is running
```

---

### `submit_address_verification`
Submit companies for address verification and legitimacy validation.

**Required per entity:** `companyName`, `country`, `address`

**Optional:** `crmid`, `website`

```
verify Acme Corp at 123 Main Street, New York, USA
validate Apple Inc at 1 Apple Park Way, Cupertino, USA
check Samsung at Samsung Tower, Seoul, South Korea

```

---

### `submit_hierarchy_creation`
Submit companies for corporate hierarchy analysis, including top-parent and subsidiary relationships to identify parent-subsidiary relationships.

**Required per entity:** `companyName`, `country`

**Optional:** `crmid`, `address`, `website`

```
create hierarchy for Acme Corp from USA
analyze organizational structure for Apple Inc from USA
map corporate relationships for Samsung from South Korea
```

---

### `submit_duplicates`
Submit duplicate account records from files (JSON or CSV) for deduplication detection.

**Required:** `file_content`, `file_type`

**Optional:** `file_name`

**Supported File Types:**
- `json` - JSON array format
- `csv` - Comma-separated values with headers

**Required Fields (per record in CSV and Excel files):**
- `crmAccountId` - Account identifier
- `crmAccountName` - Account/Company name  
- `addressLine1` - Street address
- `country` - Country name

**Optional Fields:**
- `addressLine2` - Suite, apt, unit, etc.
- `city` - City name
- `stateProvince` - State or Province
- `postalCode` - ZIP/Postal code
- `website` - Website URL

**File Format Requirements:**

**JSON Array:**
```json
[
  {
    "crmAccountId": "CRM-00001",
    "crmAccountName": "Acme Technologies",
    "addressLine1": "123 Main Street",
    "addressLine2": "Suite 100",
    "city": "New York",
    "stateProvince": "NY",
    "country": "United States",
    "postalCode": "10001",
    "website": "www.acmetech.com"
  },
  {
    "crmAccountId": "CRM-00002",
    "crmAccountName": "Blue Ridge Software",
    "addressLine1": "456 Oak Avenue",
    "country": "United States"
  }
]
```

**CSV with Headers:**
```
crmAccountId,crmAccountName,addressLine1,city,stateProvince,country,postalCode,website
CRM-00001,Acme Technologies,123 Main Street,New York,NY,United States,10001,www.acmetech.com
CRM-00002,Blue Ridge Software,456 Oak Avenue,Los Angeles,CA,United States,90001,www.blueridgesoftware.com
```

**Excel Workbook:**
- First row: Column headers
- Data rows: Account records
- Required columns: crmAccountId, crmAccountName, addressLine1, country
- Optional columns: addressLine2, city, stateProvince, postalCode, website

**Examples:**
```
submit deduplication for CSV file with 50 company records
detect duplicates from Excel file of CRM accounts
process JSON file to find similar companies
```

**Returns:**
- `data`: Number of duplicate pairs detected
- `message`: Description of results
- `details`: Record count and duplicate pair count

**Example Response (Status 200):**
```json
{
  "success": true,
  "status_code": 200,
  "data": 66,
  "message": "Successfully detected 66 duplicate pairs from 8 records",
  "details": {
    "records_submitted": 8,
    "duplicate_pairs_found": 66,
    "file_name": "accounts.csv",
    "file_type": "csv"
  }
}
```

---

### `get_request_status`
Poll a CRM processing request for status and results.

**Parameter:** `request_id` (int)

**Status values:** `PENDING` · `PROCESSING` · `COMPLETE` · `FAILED`

```
get status of request 1001
check if CRM request 5042 is complete
```

---

## Example workflow

**Address Verification:**
```
You:    Verify Apple Inc at 1 Apple Park Way, Cupertino, USA.

Claude: [calls submit_address_verification]
        ✅ Submitted. Entity ID: 1042

You:    Get status of request 1042

Claude: [calls get_request_status]
        Status: COMPLETE
        Address Verified: ✅ Yes
        Business Status: ACTIVE

# ✅ Address Verification Result
 
## Apple Inc.
 
**Submitted**ddress:** 1 Apple Park Way, Cupertino, USA
 
| Field                  | Submitted                          | Verified                         |
|------------------------|------------------------------------|----------------------------------|
| Address                | 1 Apple Park Way, Cupertino, USA   | 1 Apple Park Way, Cupertino, USA |
| City |                 | Cupertino                          | Cupertino                        |
| State                  | California                         | California                       |
| Zip                    | 95014                              | 95014                            |
| Country                | United States                      | United States                    |
 
## Verification Status
 
✅ **Address verified successfully.**
 
The submitted address matches the verified address.
 
**Status:** Confirmed
```

**Hierarchy Creation:**
```
You:    Create hierarchy for Acme Corp from USA.

Claude: [calls submit_hierarchy_creation]
        ✅ Submitted. Entity ID: 1043

You:    Get status of request 1043

Claude: [calls get_request_status]
        Status: COMPLETE

# Output Files

# 🌍 Corporate Hierarchy Report
 
## Acme Corp
 
### Top Parent
 
| Role/Level | Entity Name | Parent ID | Country | City | State/Province | Address Line 1 | Postal Code | SIC | NAICS |
|------------|-------------|-----------|---------|------|----------------|----------------|-------------|-----|-------|
| Top Parent | Acme Corp | 101 | United States | Milwaukee | Wisconsin | 5445 N 27th St | 53209 | 3421 | 332215 |
 
### Subsidiaries
 
| Entity Name | Country | City | State/Province | Address |
|-------------|---------|------|----------------|---------|
| Acme United (Asia Pacific) Limited | Hong Kong | Kwun Tong | - | 57 Hung To Road Nanyang Plaza, Unit 2101 21/F |
| Acme United China Limited | China | Guangzhou | Guangdong | Zhongshanliu Rd, Unit 1206-1207 12/F Newpoly Tower #2 |
| Acme United Europe GmbH | Germany | Solingen | - | Junkerstraße 13-15 |
| Acme United Limited | United States | Shelton | Connecticut | 1 Waterview Drive, Suite 200 |
| Acme United (APAC) Pte. Ltd. | Singapore | Singapore | - | 9 Raffles Place Republic Plaza, #26-01 |
| Acme United Netherlands Cooperatie U.A. | Netherlands | Maastricht | Limburg | Kruisdonk 66 |
 
## Contact Information
 
| Entity Name | Phone Number |
|-------------|--------------|
| Acme United (Asia Pacific) Limited | 85231677878 |
| Acme United Europe GmbH | 49212232450 |
| Acme United Limited | 8008352263 |
 
## Summary
 
- **Top Parent:** Acme Corp
- **Parent ID:** 101
- **Total Entities:** 7
- **Subsidiaries:** 6
- **Countries Covered:** United States, Hong Kong, China, Germany, Singapore, Netherlands
- **SIC Code:** 3421
- **NAICS Code:** 332215
 
## Verification Status
 
✅ **Corporate hierarchy extracted successfully.**
 
The hierarchy contains one top parent entity and six subsidiary entities linked to Parent ID **101**.
 
**Status:** Confirmed
```

---

# CRM Deduplication Submission
 
You: Submit deduplication file CRM_Dedupe_TestSet.csv
 
Claude: [calls submit_duplicates]
         ✅ Submitted Successfully
 
         File: CRM_Dedupe_TestSet.csv
         Request ID: 203
         Records Submitted: 500
 
You: Get status of request 203
 
Claude: [calls get_request_status]
 
         Status: COMPLETE
         Records Accepted: ✅ 500
         Records Rejected: 0

# Output Files
 
After processing the CRM data, the tool generates two output files.
 
## 1. Detailed Matching Results File
 
This file contains the result for **every CRM record** submitted in the input file.
 
It shows whether each record was matched with another record based on company name and address.
 
| Column | Meaning |
|---------|---------|
| Group No | Number assigned to records that belong to the same possible duplicate group. |
| Parent ID | The main or selected CRM record ID for that group. |
| Matched CRM ID | The CRM record ID being checked. |
| CRM Account Name | Company name from the CRM. |
| Address | Address of the CRM account. |
| Verdict | Match result: **Strong Match**, **Partial Match**, or **No Match**. |
| Score | Similarity score between the company name and address. Higher scores indicate a closer match. |
 
### Verdict Meanings
 
- **Strong Match** - Records are highly likely to be duplicates.
- **Partial Match** - Records are similar and may need review.
- **No Match** - No duplicate match was found for that record.
 
### Example
 
| Group No | Parent ID | Matched CRM ID | CRM Account Name | Address | Verdict | Score |
|----------|-----------|----------------|------------------|----------|---------|-------|
| 1 | CRM3001B | CRM3001B | MetroVale Tech Partners Ltd. | 143 E Indl Pkwy | No Match | 0 |
| 2 | CRM3002B | CRM3002B | Northstar Logic Information Sys. Limited | 180 Mt Pleasant Rd | No Match | 0 |
| 3 | CRM3002A | CRM3002A | Northstar Logic Information Systems Limited | 180 Mt Pleasant Rd | Strong Match | 95 |
| 3 | CRM3002A | CRM3002B | Northstar Logic Information Sys. Limited | 180 Mt Pleasant Rd | Strong Match | 95 |
 
---
 
## 2. Unique Records File
 
This file provides a cleaned list of records to keep.
 
For every duplicate group, it includes only **one selected CRM record**. If a group has strong or partial duplicate matches, the remaining records in that group are not repeated in this file.
 
All records with a **No Match** verdict are also included because they are already unique.
 
For example, if one group contains two partial matches and one strong match, this file will show only one selected record from that group.
 
| Column | Meaning |
|---------|---------|
| Group No | The group number assigned during matching. |
| Matched CRM ID | The selected unique CRM record ID. |
| CRM Account Name | Company name of the selected record. |
| Address | Address of the selected record. |
 
### Example
 
| Group No | Matched CRM ID | CRM Account Name | Address |
|----------|----------------|------------------|----------|
| 1 | DD011 | Northstar Analytics Pvt Ltd | India 42 Park View Road, Sector 18, Noida, Dist. Gautam Buddha Nagar, UP 201301 |
| 2 | DD012 | BluePeak Technologies Inc | United States 1250 Market Street, Suite 400, San Francisco, CA 94102 |
| 3 | DD022 | GREENFIELD LOGISTICS LLC | United States 8800 Westheimer Road, Houston, Texas 77063 |
 
---
 
## Summary
 
- Use the **Detailed Matching Results File** to review all matching decisions.
- Use the **Unique Records File** when you need one record per duplicate group, along with all records that did not match any other records.

## Support

- Issues: https://github.com/QoreNext/qorenext-crm-mcp/issues
- Email: support@qorenext.com
- Docs: https://qorenext.com

---

## License

MIT
