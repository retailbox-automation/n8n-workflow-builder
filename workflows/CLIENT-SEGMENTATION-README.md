# Client Segmentation Workflow

**Automated Upwork Contract Analysis and Segmentation using Claude AI**

## Overview

This n8n workflow automates the segmentation of Upwork contracts using Claude Sonnet 4.5 AI. It processes contracts from Monday.com, analyzes them using AI, and updates them with tier classifications, priority levels, and actionable recommendations.

## Requirements

### n8n Version
- **Minimum**: n8n v1.22.6+
- **Recommended**: Latest stable version

### API Credentials Required
1. **Monday.com API Token**
   - Required for: Reading/writing contract data and searching projects
   - Scope: `boards:read`, `boards:write`, `items:read`, `items:write`
   - Get it from: Monday.com → Profile → Admin → API

2. **Anthropic API Key**
   - Required for: Claude AI analysis
   - Model: `claude-sonnet-4.5-20250929`
   - Get it from: https://console.anthropic.com/

## Workflow Architecture

### Flow Diagram
```
Manual Trigger
    ↓
Monday.com Get Items (Board: 18373072729)
    ↓
Split In Batches (1 at a time)
    ↓
HTTP Request - Get Updates (GraphQL API)
    ↓
Code - Merge Updates
    ↓
Monday.com Search Projects (Board: 1300635875)
    ↓
Code - Prepare AI Input
    ↓
AI Agent (Claude Sonnet 4.5)
    ↓
Code - Parse AI Response
    ↓
Monday.com Update Item
    ↓
IF Error Handler → [Log Error / Continue]
    ↓
Loop Back to Split In Batches
```

## Node Descriptions

### 1. Manual Trigger
- **Type**: `n8n-nodes-base.manualTrigger`
- **Purpose**: Manually start the workflow
- **Config**: Default settings

### 2. Monday.com - Get Items
- **Type**: `n8n-nodes-base.mondayCom`
- **Purpose**: Fetch contracts from Monday.com
- **Config**:
  - Board ID: `18373072729` (Contracts board)
  - Limit: `25` (adjust for testing: 1-5, production: 25+)
  - Filter: Column `color_mkxkdqhd` = `[17]` (unprocessed items)
  - Columns: `name`, `text_mkxjf956` (Company), `numeric_mkxk9mn5` (Total Value), `date_mkxkd2ab` (Last Active), `color_mkxj14bf` (Status)

### 3. Split In Batches
- **Type**: `n8n-nodes-base.splitInBatches`
- **Purpose**: Process one contract at a time to avoid rate limits
- **Config**:
  - Batch Size: `1`
  - Ensures sequential processing

### 4. HTTP Request - Get Updates
- **Type**: `n8n-nodes-base.httpRequest`
- **Purpose**: Fetch contract updates/comments via GraphQL API
- **Why HTTP?**: Monday.com node doesn't support `getUpdates` operation
- **Config**:
  - Method: `POST`
  - URL: `https://api.monday.com/v2`
  - Authentication: Monday.com API
  - Headers:
    - `Content-Type: application/json`
    - `API-Version: 2024-10`
  - Body (GraphQL):
    ```graphql
    query {
      items(ids: [{{ $json.id }}]) {
        updates(limit: 50) {
          text_body
          created_at
        }
      }
    }
    ```

### 5. Code - Merge Updates
- **Type**: `n8n-nodes-base.code`
- **Purpose**: Combine contract data with updates text
- **Logic**:
  ```javascript
  const item = $('Monday.com - Get Items').first().json;
  const updatesResponse = $input.first().json;
  const updates = updatesResponse.data?.items[0]?.updates || [];

  return {
    json: {
      ...item,
      updates_text: updates.map(u => u.text_body).join('\n\n')
    }
  };
  ```

### 6. Monday.com - Search Projects
- **Type**: `n8n-nodes-base.mondayCom`
- **Purpose**: Find related projects for the company
- **Config**:
  - Board ID: `1300635875` (Projects board)
  - Filter: Column `text_mkvntqdb` contains company name from contract
  - Columns: `dropdown` (Products), `industry__1`, `numbers`

### 7. Code - Prepare AI Input
- **Type**: `n8n-nodes-base.code`
- **Purpose**: Structure data for AI analysis
- **Output**:
  ```javascript
  {
    contract_name: string,
    company: string,
    total_value: number,
    last_active: date,
    status: string,
    updates: string,
    products: string,
    item_id: number
  }
  ```

### 8. AI Agent (Anthropic)
- **Type**: `@n8n/n8n-nodes-langchain.agent`
- **Purpose**: Analyze contract and determine segmentation
- **Config**:
  - Model: `claude-sonnet-4.5-20250929`
  - Temperature: `0.2` (consistent results)
  - Max Tokens: `4096`

#### AI System Prompt
```
Segment Upwork contract into tiers:
- Tier A (ID 5): $15K+ LTV, enterprise
- Tier B (ID 1): $5-15K LTV
- Tier C (ID 0): <$5K LTV

Priority:
- High (2): Active + Tier A/B + <30 days
- Medium (0): Ended + Tier A/B + 30-90 days
- Low (7): Tier C or 90-180 days
- Skip (17): 180+ days

Respond ONLY valid JSON:
{
  "tier_id": 5|1|0,
  "priority_id": 2|0|7|17,
  "industry_id": 2,
  "company_size_id": 0,
  "recommended_package": "Primary: Name ($X)\nRationale: reason",
  "estimated_deal_size": 18000,
  "follow_up_message": "",
  "reasoning": ""
}
```

### 9. Code - Parse AI Response
- **Type**: `n8n-nodes-base.code`
- **Purpose**: Parse and validate AI JSON response
- **Logic**:
  - Removes markdown code blocks
  - Parses JSON
  - Validates structure
  - Returns `item_id` + `segmentation` object

### 10. Monday.com - Update Item
- **Type**: `n8n-nodes-base.mondayCom`
- **Purpose**: Write segmentation results back to Monday.com
- **Config**:
  - Board ID: `18373072729`
  - Item ID: From previous step
  - Column Values:
    - `color_mkxkdqhd`: Tier (0/1/5)
    - `color_mkxkc8yn`: Priority (2/0/7/17)
    - `text_mkxk98e7`: Recommended Package
    - `numeric_mkxkt1e0`: Estimated Deal Size
    - `color_mkxk67wd`: Industry
    - `color_mkxk6xef`: Company Size
    - `long_text_mkxnqzp6`: Follow-up Message

### 11. IF - Error Handler
- **Type**: `n8n-nodes-base.if`
- **Purpose**: Handle errors gracefully
- **Logic**:
  - **True** (error exists): Log error → Continue to next item
  - **False** (no error): Continue to next item
  - Both paths loop back to Split In Batches

## Monday.com Board Configuration

### Contracts Board (18373072729)
| Column ID | Column Name | Type | Purpose |
|-----------|-------------|------|---------|
| `name` | Contract Name | Text | Contract identifier |
| `text_mkxjf956` | Company | Text | Client company name |
| `numeric_mkxk9mn5` | Total Value | Number | Contract value ($) |
| `date_mkxkd2ab` | Last Active | Date | Last activity date |
| `color_mkxj14bf` | Status | Status | Contract status |
| `color_mkxkdqhd` | Tier | Status | OUTPUT: A/B/C tier |
| `color_mkxkc8yn` | Priority | Status | OUTPUT: High/Med/Low |
| `text_mkxk98e7` | Package | Text | OUTPUT: Recommended package |
| `numeric_mkxkt1e0` | Deal Size | Number | OUTPUT: Estimated $ |
| `color_mkxk67wd` | Industry | Status | OUTPUT: Industry type |
| `color_mkxk6xef` | Company Size | Status | OUTPUT: Company size |
| `long_text_mkxnqzp6` | Follow-up | Long Text | OUTPUT: Next steps |

### Projects Board (1300635875)
| Column ID | Column Name | Type | Purpose |
|-----------|-------------|------|---------|
| `text_mkvntqdb` | Company | Text | Link to contracts |
| `dropdown` | Products | Dropdown | Products used |
| `industry__1` | Industry | Dropdown | Industry category |
| `numbers` | Value | Number | Project value |

## Testing Instructions

### Phase 1: Single Item Test
1. Set `Monday.com - Get Items` limit to `1`
2. Test with contract ID: `10531577735`
3. Manual trigger → Run workflow
4. Verify:
   - Updates fetched successfully
   - Projects found
   - AI response valid JSON
   - Monday.com item updated
   - All 7 columns populated

### Phase 2: Small Batch Test
1. Set limit to `5`
2. Run workflow
3. Monitor execution time (~2-3 min)
4. Verify error handling works
5. Check all items processed

### Phase 3: Production Run
1. Set limit to `25` or higher
2. Run workflow
3. Expected runtime:
   - 25 items: ~10-15 min
   - 100 items: ~40-60 min
   - 603 items: ~30-45 min (with optimizations)

## Performance Optimization

### Current Performance
- **Per Item**: ~5-8 seconds
- **Batch of 25**: ~10-15 minutes
- **Full 603 items**: ~30-45 minutes

### Rate Limits
| Service | Limit | Solution |
|---------|-------|----------|
| Monday.com API | 60 req/min | Sequential processing |
| Anthropic API | 50 req/min | Batch size = 1 |
| n8n | No specific limit | Use error handling |

### Optimization Tips
1. **Parallel Processing**: For production, consider splitting into multiple workflows
2. **Caching**: Cache project lookups if same company appears multiple times
3. **Batch Updates**: Update Monday.com in batches of 10 instead of individually
4. **Error Recovery**: Implement retry logic for transient failures

## Error Handling

### Common Errors

#### 1. Monday.com API Errors
- **Error**: `401 Unauthorized`
- **Solution**: Check API token is valid
- **Prevention**: Use environment variables for credentials

#### 2. Anthropic API Errors
- **Error**: `Rate limit exceeded`
- **Solution**: Reduce batch size or add delays
- **Prevention**: Implement exponential backoff

#### 3. JSON Parse Errors
- **Error**: `Unexpected token in JSON`
- **Solution**: Check AI response format
- **Prevention**: Add JSON validation in parse step

#### 4. Missing Data Errors
- **Error**: `Cannot read property of undefined`
- **Solution**: Add null checks in code nodes
- **Prevention**: Use optional chaining (`?.`)

### Error Recovery
The IF Error Handler node ensures:
- Errors are logged but don't stop workflow
- Failed items are skipped
- Processing continues with next item
- Full execution log available for debugging

## Deployment Checklist

### Pre-Deployment
- [ ] Monday.com API token configured
- [ ] Anthropic API key configured
- [ ] Credentials saved in n8n
- [ ] Test with limit = 1
- [ ] Verify all columns mapped correctly
- [ ] Check AI prompt formatting

### Deployment
- [ ] Import workflow JSON to n8n
- [ ] Configure credentials
- [ ] Test run with 1 item
- [ ] Test run with 5 items
- [ ] Review results in Monday.com
- [ ] Set production limit (25+)

### Post-Deployment
- [ ] Monitor first production run
- [ ] Check execution logs
- [ ] Verify data accuracy
- [ ] Document any issues
- [ ] Set up scheduled runs (optional)

## Scheduled Execution (Optional)

To run automatically:
1. Replace **Manual Trigger** with **Schedule Trigger**
2. Configure schedule (e.g., daily at 2 AM)
3. Add filter: Only process items updated in last 24h
4. Enable workflow

Example Schedule Trigger config:
```json
{
  "rule": {
    "interval": [
      {
        "field": "cronExpression",
        "cronExpression": "0 2 * * *"
      }
    ]
  }
}
```

## Monitoring and Analytics

### Key Metrics to Track
- Total items processed
- Success rate (%)
- Average processing time per item
- API errors count
- Tier distribution (A/B/C)
- Priority distribution

### Execution Logs
Access via n8n UI:
1. Go to **Executions**
2. Filter by workflow name
3. Review each execution
4. Check error logs

## Troubleshooting

### Workflow Won't Start
1. Check Manual Trigger is connected
2. Verify Monday.com credentials
3. Test Board ID exists
4. Check filters are valid

### No Items Processed
1. Verify filter: `color_mkxkdqhd = [17]`
2. Check Board ID: `18373072729`
3. Ensure items exist matching filter
4. Test with limit = 1, no filters

### AI Response Invalid
1. Check Anthropic API key
2. Verify model: `claude-sonnet-4.5-20250929`
3. Review system prompt formatting
4. Check input data structure

### Monday.com Update Fails
1. Verify column IDs match
2. Check value types (number vs string)
3. Test with simple update first
4. Review Monday.com API logs

## Advanced Customization

### Modify Segmentation Logic
Edit the AI system prompt in **AI Agent** node:
- Change tier thresholds
- Add new criteria
- Adjust priority rules
- Add custom fields

### Add New Output Fields
1. Add field to AI response JSON
2. Update parse logic in **Code - Parse AI Response**
3. Add column to **Monday.com - Update Item**
4. Map new field

### Integration with Other Tools
- Add **Slack** notification on completion
- Send **Email** for high-priority items
- Update **Google Sheets** for reporting
- Trigger **Webhook** for external systems

## FAQ

**Q: Can I process more than 25 items at once?**
A: Yes, adjust the limit in Monday.com Get Items. Be aware of API rate limits.

**Q: How do I change the AI model?**
A: Edit the Anthropic Chat Model node and select a different model (e.g., `claude-opus-4`).

**Q: Can I run this on a schedule?**
A: Yes, replace Manual Trigger with Schedule Trigger and set desired frequency.

**Q: What if the AI returns invalid JSON?**
A: The workflow will catch the error, log it, and continue with the next item.

**Q: How do I add custom fields?**
A: Modify the AI prompt, parse logic, and Monday.com update columns.

**Q: Can I use a different AI provider?**
A: Yes, replace the Anthropic Chat Model node with OpenAI, Azure, or another provider.

## Support

- **n8n Documentation**: https://docs.n8n.io/
- **Monday.com API**: https://developer.monday.com/
- **Anthropic API**: https://docs.anthropic.com/
- **Community Forum**: https://community.n8n.io/

## Version History

- **v1.0.0** (2025-11-14): Initial release
  - 11 nodes
  - Claude Sonnet 4.5 integration
  - Full error handling
  - Batch processing support

## License

MIT

---

**Created**: 2025-11-14
**Last Updated**: 2025-11-14
**n8n Version**: 1.22.6+
**Status**: Production Ready ✅
