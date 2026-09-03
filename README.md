# Qorenext CRM MCP

> Company hierarchy analysis and address verification — via MCP

Qorenext CRM MCP is a [Model Context Protocol](https://modelcontextprotocol.io) server that enables Claude and other AI clients to perform company hierarchy analysis and address verification directly in chat.

---

## Get your API key

Sign up at **https://qorenext-app.azurewebsites.net/signup** to get your `QORENEXT_API_KEY`.

## Steps to get your API Key
1. Sign up at **https://qorenext-app.azurewebsites.net/signup**. 
2. Select the CRM On-Demand Data Quality & Enrichment product. 
3. Choose your subscription plan. 
4. Open Profile → APIs. 
5. Click Generate API Key. 
6. Give your API key a name and click Create. 
7. Copy the API key for use in Claude. 
---

## Connect

### Claude Code
```bash
claude mcp add --transport http qorenext-mcp \
  "https://mcp.qorenext.com/crm" \
  --header "X-API-Key: YOUR_API_KEY"
```

### Claude Desktop
Edit `%APPDATA%\Claude\claude_desktop_config.json` (Windows)
or `~/Library/Application Support/Claude/claude_desktop_config.json` (Mac):

```json
{
  "mcpServers": {
    "qorenext": {
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

### Cursor / Windsurf
Add to `.cursor/mcp.json` or `.windsurf/mcp.json`:

```json
{
  "mcpServers": {
    "qorenext-mcp": {
      "url": "https://mcp.qorenext.com/crm",
      "headers": {
        "X-API-Key": "YOUR_API_KEY"
      }
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
Submit duplicate account records from files (JSON, CSV, or Excel) for deduplication detection.

**Required:** `file_content`, `file_type`

**Optional:** `file_name`

**Supported File Types:**
- `json` - JSON array format
- `csv` - Comma-separated values with headers
- `excel` or `xlsx` - Microsoft Excel workbooks

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
```

**Hierarchy Creation:**
```
You:    Create hierarchy for Acme Corp from USA.

Claude: [calls submit_hierarchy_creation]
        ✅ Submitted. Entity ID: 1043

You:    Get status of request 1043

Claude: [calls get_request_status]
        Status: COMPLETE
        Hierarchy: Acme Corp → Acme Holdings (USA) → GlobalCorp (UK)
```

---

## Support

- Issues: https://github.com/QoreNext/qorenext-crm-mcp/issues
- Email: support@qorenext.com
- Docs: https://qorenext.com

---

## License

MIT
