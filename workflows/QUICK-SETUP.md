# Quick Setup Guide - Client Segmentation Workflow

## 🚀 5-Minute Setup

### Step 1: Import Workflow (2 min)
1. Open your n8n instance: `https://retailbox-n8n.zeabur.app`
2. Click **"Add workflow"** → **"Import from File"**
3. Upload: `client-segmentation.json`
4. Click **"Save"**

### Step 2: Configure Credentials (2 min)

#### Monday.com API
1. Go to Monday.com → Your Profile → Admin → API
2. Generate new API token
3. Copy token
4. In n8n workflow:
   - Click any Monday.com node
   - Select **"Create New Credential"**
   - Paste API token
   - Test connection
   - Save

#### Anthropic API
1. Go to https://console.anthropic.com/
2. Navigate to **API Keys**
3. Create new key
4. Copy key
5. In n8n workflow:
   - Click **"Anthropic Chat Model"** node
   - Select **"Create New Credential"**
   - Paste API key
   - Save

### Step 3: Test Run (1 min)
1. Edit **"Monday.com - Get Items"** node
2. Change `limit` to `1`
3. Click **"Test workflow"** button (top right)
4. Wait ~10 seconds
5. Check execution log - should see green success
6. Verify Monday.com item was updated

## ✅ Verification Checklist

After setup, verify:
- [ ] Workflow imported successfully
- [ ] Monday.com credential connected (green check)
- [ ] Anthropic credential connected (green check)
- [ ] Test run completed without errors
- [ ] Monday.com item updated with:
  - [ ] Tier (A/B/C)
  - [ ] Priority (High/Medium/Low)
  - [ ] Recommended Package
  - [ ] Estimated Deal Size
  - [ ] Industry
  - [ ] Company Size
  - [ ] Follow-up Message

## 🎯 First Production Run

Once verified:
1. Change limit back to `25`
2. Click **"Execute Workflow"** (Manual Trigger)
3. Monitor execution (~10-15 min for 25 items)
4. Review results in Monday.com

## ⚙️ Configuration Options

### For Testing
```json
{
  "limit": 1,           // Test with 1 item
  "batchSize": 1        // Process sequentially
}
```

### For Production
```json
{
  "limit": 25,          // Process 25 at a time
  "batchSize": 1        // Keep sequential
}
```

### For Full Processing
```json
{
  "limit": 100,         // Or remove limit entirely
  "batchSize": 1
}
```

## 🐛 Quick Troubleshooting

### Error: "401 Unauthorized"
**Problem**: Invalid API token
**Fix**:
1. Regenerate Monday.com API token
2. Update credential in n8n
3. Test connection

### Error: "Rate limit exceeded"
**Problem**: Too many API calls
**Fix**:
1. Reduce `limit` to 10-15
2. Add 1-2 second delay between batches

### Error: "Invalid JSON response"
**Problem**: AI response format issue
**Fix**:
1. Check Anthropic API key is valid
2. Verify model is `claude-sonnet-4.5-20250929`
3. Review system prompt in AI Agent node

### No Items Processed
**Problem**: Filter not matching items
**Fix**:
1. Check Board ID: `18373072729`
2. Verify filter: `color_mkxkdqhd = [17]`
3. Ensure items exist with status [17]

## 📊 Expected Results

### After 1 Item
- Execution time: ~8-10 seconds
- Monday.com updated: 1 item
- Columns populated: 7

### After 25 Items
- Execution time: ~10-15 minutes
- Monday.com updated: 25 items
- Success rate: ~95%+

### After 603 Items (Full)
- Execution time: ~30-45 minutes
- Monday.com updated: 603 items
- Success rate: ~95%+

## 🔄 Run Schedule (Optional)

To automate:
1. Replace **Manual Trigger** with **Schedule Trigger**
2. Set schedule: Daily at 2 AM
3. Save and activate workflow

## 📞 Need Help?

- **Documentation**: See `CLIENT-SEGMENTATION-README.md`
- **n8n Docs**: https://docs.n8n.io/
- **Community**: https://community.n8n.io/

---

**Time to Production**: ~5 minutes
**Difficulty**: Beginner-Friendly
**Status**: Ready to Use ✅
