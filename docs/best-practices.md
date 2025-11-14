# n8n Workflow Builder - Best Practices

## 🎯 Design Principles

### 1. Single Responsibility
Each workflow should have one clear purpose. If a workflow does multiple unrelated things, split it into separate workflows connected via webhooks or sub-workflows.

**Good:**
```
Workflow: "Process New Customer Orders"
- Trigger on new order
- Validate order data
- Update inventory
- Send confirmation email
```

**Bad:**
```
Workflow: "Handle Everything"
- Process orders
- Generate reports
- Sync calendar
- Backup databases
```

### 2. Fail-Fast Validation
Validate inputs as early as possible in the workflow to avoid wasting resources on invalid data.

```json
{
  "nodes": [
    {"name": "Trigger"},
    {"name": "Validate Input", "type": "n8n-nodes-base.if"},
    {"name": "Stop on Error", "type": "n8n-nodes-base.stopAndError"},
    {"name": "Process Valid Data"}
  ]
}
```

### 3. Idempotency
Design workflows that can be safely retried without causing duplicate actions.

**Strategies:**
- Check if record exists before creating
- Use unique identifiers (UUID, timestamps)
- Implement deduplication logic
- Use upsert operations instead of insert

### 4. Error Handling
Always include error handling for critical operations.

**Pattern:**
```json
{
  "nodes": [
    {"name": "Try Operation", "continueOnFail": true},
    {"name": "IF Error Occurred"},
    {"name": "Error Handler"},
    {"name": "Notify Admin"},
    {"name": "Success Path"}
  ]
}
```

## 🏗️ Workflow Structure

### Node Naming Conventions
Use clear, descriptive names that indicate what each node does:

**Good Names:**
- "Fetch Customer from Database"
- "Calculate Order Total"
- "Send Confirmation Email"
- "Update Inventory Counts"

**Bad Names:**
- "HTTP Request"
- "Set"
- "Node1"
- "Function"

### Position and Layout
Organize nodes for readability:
- **Left to Right**: Main flow direction
- **Top to Bottom**: Conditional branches
- **Spacing**: 200px between nodes (standard)
- **Alignment**: Keep related nodes aligned

### Connection Guidelines
1. **Main connections**: Primary data flow
2. **Error connections**: Explicit error handling
3. **Avoid crossing**: Minimize connection crossings
4. **Clear paths**: Make data flow obvious

## 🔧 Node Configuration

### HTTP Request Node
```json
{
  "type": "n8n-nodes-base.httpRequest",
  "parameters": {
    "url": "{{ $env.API_URL }}/endpoint",
    "method": "POST",
    "authentication": "genericCredentialType",
    "sendHeaders": true,
    "headerParameters": {
      "parameters": [
        {"name": "Content-Type", "value": "application/json"}
      ]
    },
    "sendBody": true,
    "bodyParameters": {
      "parameters": [
        {"name": "data", "value": "={{ $json }}"}
      ]
    },
    "options": {
      "timeout": 30000,
      "retry": {
        "maxTries": 3,
        "waitBetweenTries": 1000
      }
    }
  }
}
```

**Best Practices:**
- Use environment variables for URLs
- Set appropriate timeouts
- Configure retries for network operations
- Handle rate limiting
- Log request/response for debugging

### Set Node (Data Transformation)
```json
{
  "type": "n8n-nodes-base.set",
  "parameters": {
    "mode": "manual",
    "assignments": {
      "assignments": [
        {
          "id": "unique-id",
          "name": "fieldName",
          "type": "string",
          "value": "={{ $json.sourceField }}"
        }
      ]
    }
  }
}
```

**Best Practices:**
- Use manual mode for explicit field mapping
- Provide unique IDs for each assignment
- Use expressions for dynamic values
- Keep transformations simple and readable

### IF Node (Conditionals)
```json
{
  "type": "n8n-nodes-base.if",
  "parameters": {
    "conditions": {
      "boolean": [
        {
          "value1": "={{ $json.status }}",
          "operation": "equal",
          "value2": "active"
        }
      ]
    }
  }
}
```

**Best Practices:**
- Use descriptive condition names
- Combine multiple conditions logically
- Handle both true and false paths
- Document complex conditions with sticky notes

## 📊 Data Management

### Working with Arrays
```javascript
// Loop over items
{{ $items.map(item => item.json) }}

// Filter items
{{ $items.filter(item => item.json.status === 'active') }}

// Get first item
{{ $items[0].json }}

// Count items
{{ $items.length }}
```

### Expressions Best Practices
1. **Keep it simple**: Complex logic belongs in Function nodes
2. **Use built-in functions**: `$now`, `$today`, `$env`
3. **Handle null/undefined**: Use optional chaining `?.`
4. **Format consistently**: Use spaces and proper syntax
5. **Test expressions**: Use workflow pinned data for testing

### Data Pinning
Use data pinning for development:
- Pin realistic test data
- Test edge cases
- Validate transformations
- Debug expressions
- Speed up development

## 🔐 Security

### Credential Management
- **Never hardcode**: Use credential system
- **Rotate regularly**: Update API keys periodically
- **Minimum permissions**: Use least privilege principle
- **Separate environments**: Dev/staging/prod credentials

### Input Validation
```javascript
// Validate required fields
if (!$json.email || !$json.name) {
  throw new Error('Missing required fields');
}

// Sanitize inputs
const sanitized = $json.input.trim().toLowerCase();

// Validate format
const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
if (!emailRegex.test($json.email)) {
  throw new Error('Invalid email format');
}
```

### Error Messages
- Don't expose sensitive data
- Log detailed errors internally
- Return generic messages to users
- Include error codes for tracking

## ⚡ Performance

### Optimization Strategies

#### 1. Batch Operations
Process multiple items in a single API call when possible:
```javascript
// Bad: Loop and call API for each item
items.forEach(item => callAPI(item));

// Good: Batch process
const batch = items.slice(0, 100);
callAPI(batch);
```

#### 2. Pagination
Handle large datasets with pagination:
```json
{
  "nodes": [
    {"name": "Fetch Page", "parameters": {
      "url": "={{ $env.API_URL }}?page={{ $json.page }}&limit=100"
    }},
    {"name": "Process Items"},
    {"name": "Check More Pages"},
    {"name": "Loop If More"}
  ]
}
```

#### 3. Caching
Cache frequently accessed data:
- Use workflow static data
- Implement Redis/database caching
- Set appropriate TTL values
- Clear cache when data changes

#### 4. Parallel Processing
Use split/merge for independent operations:
```
Trigger → Split
         ├─ Process A (parallel)
         ├─ Process B (parallel)
         └─ Process C (parallel)
         → Merge Results
```

### Resource Management
- Set execution timeouts
- Limit loop iterations
- Handle large files carefully
- Monitor memory usage
- Clean up temporary data

## 🧪 Testing

### Development Workflow
1. **Design**: Plan workflow on paper
2. **Build**: Create workflow with test data
3. **Pin Data**: Pin realistic test data
4. **Test**: Run manual executions
5. **Validate**: Check all branches and error paths
6. **Deploy**: Activate in production

### Test Coverage
Ensure you test:
- ✅ Happy path (expected flow)
- ✅ Error conditions
- ✅ Edge cases (empty data, null values)
- ✅ Invalid inputs
- ✅ Rate limits and timeouts
- ✅ Data transformations
- ✅ All conditional branches

### Debugging Tools
1. **Execution Data**: Review detailed execution logs
2. **Pinned Data**: Test with known inputs
3. **Sticky Notes**: Document expected behavior
4. **Console Logs**: Add logging in Function nodes
5. **Try/Catch**: Wrap risky operations

## 📚 Documentation

### Workflow Documentation
Every workflow should include:
- **Purpose**: What does it do?
- **Trigger**: How is it activated?
- **Dependencies**: External systems required
- **Credentials**: What credentials are needed?
- **Error Handling**: How errors are managed
- **Maintenance**: Update frequency, owner

### Sticky Notes Usage
Use sticky notes for:
- Complex logic explanation
- Important warnings
- Configuration requirements
- Known limitations
- TODO items

### Code Comments
In Function nodes:
```javascript
/**
 * Calculate order total with tax and shipping
 * @param {Object} order - Order object
 * @returns {number} Total amount
 */
function calculateTotal(order) {
  const subtotal = order.items.reduce((sum, item) =>
    sum + (item.price * item.quantity), 0);

  const tax = subtotal * 0.10; // 10% tax rate
  const shipping = order.weight > 5 ? 15.00 : 5.00;

  return subtotal + tax + shipping;
}
```

## 🔄 Version Control

### Workflow Versioning
- Use meaningful version names
- Document changes in each version
- Keep version history
- Tag production versions
- Backup before major changes

### Git Integration
Recommended structure:
```
workflows/
├── production/
│   ├── customer-onboarding.json
│   └── order-processing.json
├── development/
│   └── experimental-features.json
└── templates/
    └── webhook-template.json
```

## 🚀 Deployment

### Pre-Deployment Checklist
- [ ] All nodes configured correctly
- [ ] Credentials configured for target environment
- [ ] Environment variables set
- [ ] Error handling implemented
- [ ] Testing completed
- [ ] Documentation updated
- [ ] Backup of current version created
- [ ] Stakeholders notified

### Deployment Process
1. **Backup**: Export current workflow
2. **Import**: Import new version
3. **Configure**: Set environment-specific settings
4. **Test**: Run test execution
5. **Activate**: Enable workflow
6. **Monitor**: Watch first executions
7. **Validate**: Confirm expected behavior

### Rollback Plan
Always have a rollback strategy:
1. Keep previous version exported
2. Document rollback steps
3. Test rollback procedure
4. Communicate rollback process

## 📈 Monitoring

### Key Metrics
Track these metrics for each workflow:
- **Execution count**: How often it runs
- **Success rate**: % of successful executions
- **Average duration**: Time to complete
- **Error rate**: % of failed executions
- **Resource usage**: Memory, CPU utilization

### Alerting
Set up alerts for:
- Execution failures
- Unusual execution duration
- High error rates
- Resource limits reached
- Credential expiration

### Log Analysis
Regularly review:
- Error patterns
- Performance bottlenecks
- Usage trends
- User feedback
- System health

## 🎓 Common Patterns

### Pattern: API Retry Logic
```json
{
  "nodes": [
    {"name": "Try API Call", "continueOnFail": true, "maxTries": 3},
    {"name": "Check If Failed"},
    {"name": "Log Error"},
    {"name": "Use Fallback Data"}
  ]
}
```

### Pattern: Data Enrichment
```json
{
  "nodes": [
    {"name": "Trigger with Basic Data"},
    {"name": "Fetch Additional Details"},
    {"name": "Merge Data"},
    {"name": "Process Enriched Data"}
  ]
}
```

### Pattern: Multi-Step Validation
```json
{
  "nodes": [
    {"name": "Validate Format"},
    {"name": "Check Duplicates"},
    {"name": "Verify Business Rules"},
    {"name": "Process if Valid"},
    {"name": "Reject if Invalid"}
  ]
}
```

## 🔍 Troubleshooting

### Common Issues

#### Issue: Workflow Not Triggering
**Causes:**
- Workflow not activated
- Trigger misconfigured
- Webhook URL incorrect
- Credentials expired

**Solutions:**
- Check activation status
- Verify trigger configuration
- Test webhook manually
- Refresh credentials

#### Issue: Data Not Passing Between Nodes
**Causes:**
- No connection between nodes
- Empty output from previous node
- Incorrect data reference

**Solutions:**
- Verify connections
- Check execution data
- Test with pinned data
- Review expressions

#### Issue: Timeout Errors
**Causes:**
- Long-running operations
- Large dataset processing
- Network latency

**Solutions:**
- Increase timeout settings
- Implement pagination
- Use async operations
- Optimize queries

## 📖 Additional Resources

- [n8n Documentation](https://docs.n8n.io/)
- [n8n Community Forum](https://community.n8n.io/)
- [Workflow Templates](https://n8n.io/workflows/)
- [Video Tutorials](https://www.youtube.com/c/n8nio)

---

**Last Updated**: 2025-11-14
**Version**: 1.0
