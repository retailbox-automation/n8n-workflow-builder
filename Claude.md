# n8n Workflow Builder Project

## 📋 Project Overview

This project provides a comprehensive MCP (Model Context Protocol) server and toolkit for programmatic creation and management of n8n automation workflows. It enables AI assistants and developers to build, modify, and manage n8n workflows without direct user intervention through the n8n REST API.

## 🎯 Purpose

Create an intelligent workflow builder that can:
- Generate n8n workflows from natural language descriptions
- Validate workflow structures before creation
- Manage workflow lifecycle (CRUD operations)
- Monitor execution history and performance
- Provide smart suggestions for node configurations
- Handle complex workflow patterns (conditionals, loops, error handling)

## 🏗️ Architecture

### Core Components

```
n8n-workflow-builder/
├── src/
│   ├── api/           # n8n API client and wrappers
│   ├── builders/      # Workflow generation logic
│   ├── validators/    # Schema validation and node checking
│   ├── tools/         # MCP tools implementation
│   └── utils/         # Helper functions
├── docs/
│   ├── api.md         # API documentation
│   ├── nodes.md       # Available nodes reference
│   └── examples.md    # Usage examples
├── examples/
│   ├── simple/        # Basic workflow examples
│   ├── advanced/      # Complex workflow patterns
│   └── templates/     # Reusable workflow templates
└── tests/
    ├── unit/          # Unit tests
    └── integration/   # Integration tests
```

## 🔑 Key Features

### 1. Workflow Creation & Management
- **Programmatic Creation**: Build workflows from JSON definitions
- **CRUD Operations**: Create, Read, Update, Delete workflows
- **Activation Control**: Enable/disable workflow execution
- **Filtering**: List workflows by status, tags, or name

### 2. Node Management
- **Node Discovery**: List all available nodes in n8n instance
- **Validation**: Pre-validate node types before workflow creation
- **Suggestions**: Recommend similar nodes when invalid types detected
- **Documentation**: Access node documentation and parameters

### 3. Execution Monitoring
- **History Tracking**: Retrieve historical executions
- **Status Analysis**: Track success/failure rates
- **Detailed Data**: Analyze execution data per workflow step
- **Performance Metrics**: Monitor workflow performance

### 4. Validation Systems
- **Schema Validation**: Zod schema validation for inputs/outputs
- **Node Type Checking**: Verify nodes exist in n8n instance
- **Connection Validation**: Ensure proper node connections
- **Expression Validation**: Validate n8n expressions syntax

## 📊 n8n Workflow Structure

### Workflow JSON Schema

```json
{
  "name": "Workflow Name",
  "nodes": [
    {
      "id": "unique-node-id",
      "name": "Node Display Name",
      "type": "n8n-nodes-base.httpRequest",
      "typeVersion": 4.2,
      "position": [250, 300],
      "parameters": {
        "url": "https://api.example.com",
        "method": "GET"
      },
      "credentials": {},
      "disabled": false
    }
  ],
  "connections": {
    "source-node-name": {
      "main": [
        [
          {
            "node": "target-node-name",
            "type": "main",
            "index": 0
          }
        ]
      ]
    }
  },
  "settings": {
    "executionOrder": "v1",
    "saveDataErrorExecution": "all",
    "saveDataSuccessExecution": "all",
    "saveManualExecutions": true
  }
}
```

### Node Structure Components

#### Required Fields
- `id`: Unique identifier for the node
- `name`: Display name in the workflow
- `type`: Node type (e.g., "n8n-nodes-base.httpRequest")
- `typeVersion`: Version of the node type
- `position`: [x, y] coordinates on canvas
- `parameters`: Node-specific configuration

#### Optional Fields
- `credentials`: Credential references for authentication
- `disabled`: Whether node is disabled (boolean)
- `notes`: Documentation notes
- `continueOnFail`: Continue workflow on node failure
- `retryOnFail`: Retry on failure (boolean)
- `maxTries`: Maximum retry attempts
- `waitBetweenTries`: Wait time between retries (ms)

## 🔧 Available Node Categories

### 1. Core Nodes (Logic & Control)
- **Triggers**: Webhook, Schedule, Email, HTTP Request
- **Conditionals**: IF, Switch, Filter
- **Data Processing**: Set, Code, Function, Function Item
- **Flow Control**: Merge, Split, Loop Over Items
- **Error Handling**: Error Trigger, Stop and Error

### 2. Integration Nodes (400+)
- **Communication**: Slack, Discord, Telegram, Email
- **Databases**: PostgreSQL, MySQL, MongoDB, Redis
- **Cloud Storage**: Google Drive, Dropbox, AWS S3
- **CRM**: Salesforce, HubSpot, Monday.com
- **Development**: GitHub, GitLab, Jira
- **AI/ML**: OpenAI, Claude, Pinecone, Langchain

### 3. AI Cluster Nodes
- **AI Agents**: Conversational agents, tool calling
- **Vector Stores**: Pinecone, Qdrant, Weaviate
- **Language Models**: OpenAI, Anthropic, Cohere
- **Embeddings**: Text embedding generators
- **Memory**: Conversation memory, buffer memory

## 🎨 Workflow Patterns

### 1. Simple Linear Flow
```
Trigger → Process Data → Send Notification
```

### 2. Conditional Branching
```
Trigger → IF Condition
         ├─ True → Action A
         └─ False → Action B
```

### 3. Data Transformation Pipeline
```
Trigger → Fetch Data → Transform → Validate → Save to DB
```

### 4. Error Handling Pattern
```
Trigger → Try Operation → On Success: Continue
                        → On Error: Error Handler → Notify Admin
```

### 5. Loop Processing
```
Trigger → Get Items → Loop Over Items → Process Each → Aggregate Results
```

### 6. Parallel Processing
```
Trigger → Split Data
         ├─ Process A → Result A
         ├─ Process B → Result B
         └─ Process C → Result C
         → Merge Results
```

## 🔐 API Configuration

### Environment Variables

```bash
# Required
N8N_API_URL=https://your-n8n-instance.com
N8N_API_KEY=your-api-key-here

# Optional
MCP_MODE=stdio
LOG_LEVEL=error
DISABLE_CONSOLE_OUTPUT=true
```

### API Authentication

The n8n REST API uses API key authentication:
- Header: `X-N8N-API-KEY: your-api-key`
- Generate keys in n8n Settings → API

### Key API Endpoints

```
GET    /api/v1/workflows              # List workflows
GET    /api/v1/workflows/:id          # Get workflow details
POST   /api/v1/workflows              # Create workflow
PUT    /api/v1/workflows/:id          # Update workflow
DELETE /api/v1/workflows/:id          # Delete workflow
PATCH  /api/v1/workflows/:id          # Activate/deactivate

GET    /api/v1/executions             # List executions
GET    /api/v1/executions/:id         # Get execution details
DELETE /api/v1/executions/:id         # Delete execution

GET    /api/v1/nodes                  # List available nodes
GET    /api/v1/nodes/:type            # Get node details
```

## 🛠️ MCP Tools Implementation

### Workflow Tools
- `n8n_create_workflow` - Create new workflow
- `n8n_get_workflow` - Get workflow by ID
- `n8n_update_workflow` - Update existing workflow
- `n8n_delete_workflow` - Delete workflow
- `n8n_list_workflows` - List all workflows
- `n8n_activate_workflow` - Activate workflow
- `n8n_deactivate_workflow` - Deactivate workflow

### Validation Tools
- `n8n_validate_workflow` - Validate workflow structure
- `n8n_validate_nodes` - Check node types exist
- `n8n_validate_connections` - Verify connections
- `n8n_validate_expressions` - Validate n8n expressions

### Node Discovery Tools
- `n8n_list_nodes` - List available nodes
- `n8n_get_node_info` - Get node documentation
- `n8n_search_nodes` - Search nodes by keyword
- `n8n_get_node_parameters` - Get node parameter schema

### Execution Tools
- `n8n_list_executions` - List executions
- `n8n_get_execution` - Get execution details
- `n8n_trigger_workflow` - Manually trigger workflow
- `n8n_retry_execution` - Retry failed execution

### Builder Tools
- `n8n_build_from_description` - Generate workflow from text
- `n8n_suggest_nodes` - Suggest nodes for task
- `n8n_generate_template` - Create workflow template
- `n8n_optimize_workflow` - Optimize existing workflow

## 📚 Best Practices

### 1. Workflow Design
- **Modular Design**: Break complex workflows into sub-workflows
- **Error Handling**: Always include error handling nodes
- **Naming Convention**: Use clear, descriptive node names
- **Documentation**: Add sticky notes for complex logic
- **Testing**: Test workflows manually before activating

### 2. Performance Optimization
- **Token Management**: Use concise mode for large workflows
- **Data Filtering**: Filter data early in the workflow
- **Pagination**: Handle large datasets with pagination
- **Caching**: Use workflow data pinning for testing
- **Async Operations**: Use webhook responses for long operations

### 3. Security
- **Credentials**: Never hardcode credentials in workflows
- **Validation**: Validate all external inputs
- **Error Messages**: Don't expose sensitive data in errors
- **Access Control**: Use n8n's sharing and permissions
- **API Keys**: Rotate API keys regularly

### 4. Maintainability
- **Version Control**: Back up workflows to Git
- **Change History**: Use workflow version history
- **Testing**: Create test workflows for development
- **Documentation**: Document complex expressions and logic
- **Templates**: Create reusable templates for common patterns

## 🎓 Development Guidelines

### Node Creation Process
1. List available nodes with `n8n_list_nodes`
2. Search for specific functionality with `n8n_search_nodes`
3. Get node documentation with `n8n_get_node_info`
4. Configure node parameters
5. Validate node configuration
6. Add to workflow

### Workflow Creation Process
1. Define workflow purpose and triggers
2. Plan data flow and transformations
3. Select appropriate nodes
4. Design error handling strategy
5. Create workflow JSON structure
6. Validate workflow schema
7. Test in development environment
8. Deploy to production

### Debugging Workflow Issues
1. Check workflow validation errors
2. Review node configurations
3. Verify node connections
4. Test with pinned data
5. Check execution history
6. Review error logs
7. Use sticky notes for documentation

## 🔄 Integration Patterns

### Webhook Integration
```json
{
  "type": "n8n-nodes-base.webhook",
  "parameters": {
    "httpMethod": "POST",
    "path": "my-webhook",
    "responseMode": "onReceived"
  }
}
```

### Database Operations
```json
{
  "type": "n8n-nodes-base.postgres",
  "parameters": {
    "operation": "executeQuery",
    "query": "SELECT * FROM users WHERE active = true"
  }
}
```

### API Calls
```json
{
  "type": "n8n-nodes-base.httpRequest",
  "parameters": {
    "url": "https://api.example.com/data",
    "method": "GET",
    "authentication": "genericCredentialType",
    "sendHeaders": true
  }
}
```

### Conditional Logic
```json
{
  "type": "n8n-nodes-base.if",
  "parameters": {
    "conditions": {
      "boolean": [
        {
          "value1": "={{ $json.status }}",
          "value2": "active"
        }
      ]
    }
  }
}
```

## 📖 Reference Documentation

### Official Resources
- **n8n Documentation**: https://docs.n8n.io/
- **n8n API Reference**: https://docs.n8n.io/api/
- **n8n Community**: https://community.n8n.io/
- **n8n GitHub**: https://github.com/n8n-io/n8n

### Related Projects
- **spences10/mcp-n8n-builder**: MCP server for workflow building
- **@makafeli/n8n-workflow-builder**: NPM package for workflow management
- **n8n-workflows GitHub repos**: Community workflow collections

### Learning Resources
- n8n Quick Start Guide
- Level One & Level Two Courses
- Workflow Templates Library
- Community Forum Examples

## 🚀 Getting Started

### 1. Setup n8n Instance
```bash
# Self-hosted
docker run -it --rm --name n8n -p 5678:5678 -v ~/.n8n:/home/node/.n8n n8nio/n8n

# Or use n8n Cloud
# https://n8n.io/cloud
```

### 2. Generate API Key
- Navigate to Settings → API
- Create new API key
- Save securely

### 3. Configure Environment
```bash
export N8N_API_URL=https://your-instance.com
export N8N_API_KEY=your-key-here
```

### 4. Install Dependencies
```bash
npm install
# or
yarn install
```

### 5. Run Development Server
```bash
npm run dev
```

## 🎯 Project Goals

### Phase 1: Foundation (Current)
- [x] Research n8n documentation
- [x] Create project structure
- [x] Document workflow JSON schema
- [ ] Implement basic API client
- [ ] Create node discovery tools

### Phase 2: Core Features
- [ ] Implement workflow CRUD operations
- [ ] Add validation system
- [ ] Create execution monitoring
- [ ] Build MCP tools interface

### Phase 3: AI Integration
- [ ] Natural language to workflow conversion
- [ ] Smart node suggestions
- [ ] Auto-optimization features
- [ ] Template generation

### Phase 4: Advanced Features
- [ ] Sub-workflow management
- [ ] Version control integration
- [ ] Performance analytics
- [ ] Collaborative editing

## 🤝 Contributing

This project is designed to be extended with:
- New workflow patterns
- Additional node types support
- Enhanced validation rules
- Performance optimizations
- Documentation improvements

## 📝 Notes

### Token Management Considerations
n8n workflows can be "token monsters" due to:
- Complex nested JSON structures
- Extensive node parameters
- Detailed connection definitions
- Large execution data

**Strategies:**
- Use concise mode for listings
- Request specific workflow sections
- Implement pagination
- Cache frequently used data
- Break complex workflows into smaller pieces

### Known Limitations
- Maximum node count per workflow
- Expression complexity limits
- API rate limiting
- Workflow execution timeouts
- Memory constraints for large datasets

---

**Project Status**: Active Development
**Last Updated**: 2025-11-14
**n8n Instance**: https://your-n8n-instance.com
**Current Workflows**: 35 (23 active, 12 inactive)
