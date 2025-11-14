# n8n Workflow Builder

> MCP Server and Toolkit for Programmatic n8n Workflow Creation

## 📚 Documentation

### Main Files
- **[Claude.md](./Claude.md)** - Complete project documentation (499 lines)
- **[ROADMAP.md](./ROADMAP.md)** - Development plan (9 phases)
- **[PROJECT_SUMMARY.md](./PROJECT_SUMMARY.md)** - Project overview

### Quick References
- **[Quick Links](./docs/quick-links.md)** - 80% ссылок которые вам нужны! 🔥
- **[Useful Resources](./docs/useful-resources.md)** - Complete resource library (246 lines)
- **[Best Practices](./docs/best-practices.md)** - Development guidelines (533 lines)

### Examples
- **[Simple Webhook](./examples/simple-webhook.json)** - Basic webhook example
- **[Conditional Flow](./examples/conditional-flow.json)** - IF/ELSE pattern

## 🚀 Quick Start

### 1. Essential Links (Start Here!)

**Must Read Documentation:**
- 📖 [n8n Documentation](https://docs.n8n.io/)
- 🔌 [Webhook Node](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.webhook/)
- 💻 [HTTP Request](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.httprequest/)
- 🔧 [Expressions](https://docs.n8n.io/code/expressions/)
- ⚠️ [Error Handling](https://docs.n8n.io/workflows/error-handling/)

**Useful Templates:**
- 🎨 [All Templates](https://n8n.io/workflows/)
- 🤖 [AI Workflows](https://n8n.io/workflows/?categories=AI)
- 💬 [Communication](https://n8n.io/workflows/?categories=Communication)

### 2. Setup Project

```bash
# Clone and setup
cd ~/n8n-workflow-builder
npm install

# Configure environment
cp .env.example .env
# Edit .env with your credentials:
# N8N_API_URL=https://your-n8n-instance.com
# N8N_API_KEY=your-api-key

# Run
npm start
```

### 3. Read Documentation

```bash
# Main project documentation
cat Claude.md

# Quick reference links
cat docs/quick-links.md

# All resources
cat docs/useful-resources.md

# Best practices
cat docs/best-practices.md
```

## 🎯 Features

✅ Programmatic workflow creation from JSON
✅ Natural language to workflow conversion (AI-powered)
✅ Full workflow CRUD operations
✅ Node validation and suggestions
✅ Execution monitoring and analytics
✅ MCP tools for AI assistant integration

## 📖 Key Resources by Topic

### For Beginners
1. **[Quickstart Guide](https://docs.n8n.io/try-it-out/)**
2. **[Create First Workflow](https://docs.n8n.io/workflows/create/)**
3. **[Level 1 Course](https://docs.n8n.io/courses/level-one/)**

### For Developers
1. **[API Reference](https://docs.n8n.io/api/)**
2. **[Expressions Guide](https://docs.n8n.io/code/expressions/)**
3. **[Code Node](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.code/)**

### Common Tasks
| Task | Documentation |
|------|--------------|
| Create API endpoint | [Webhook Node](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.webhook/) |
| Process data | [Set Node](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.set/) |
| Conditional logic | [IF Node](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.if/) |
| Database operations | [PostgreSQL](https://docs.n8n.io/integrations/builtin/app-nodes/n8n-nodes-base.postgres/) |
| Error handling | [Error Guide](https://docs.n8n.io/workflows/error-handling/) |

## 💡 Expressions Cheatsheet

```javascript
// Current date/time
{{ $now }}                    // ISO timestamp
{{ $today }}                  // Today's date

// Access data
{{ $json.fieldName }}         // From previous node
{{ $items.length }}           // Array length
{{ $items[0].json.name }}     // First item

// Conditions
{{ $json.status === 'active' ? 'Yes' : 'No' }}

// Environment
{{ $env.API_KEY }}            // From .env
```

## 🛠️ Project Structure

```
n8n-workflow-builder/
├── Claude.md                    # Main documentation
├── README.md                    # This file
├── ROADMAP.md                   # Development plan
├── PROJECT_SUMMARY.md           # Project overview
├── package.json                 # Node.js config
├── .env.example                 # Environment template
├── docs/
│   ├── quick-links.md          # 🔥 Quick reference
│   ├── useful-resources.md     # Complete resource library
│   └── best-practices.md       # Development guidelines
├── examples/
│   ├── simple-webhook.json     # Basic example
│   └── conditional-flow.json   # Advanced example
├── src/                         # Source code (TBD)
└── tests/                       # Tests (TBD)
```

## 🎓 Learning Path

1. **Week 1**: Read Quick Links → Try simple examples
2. **Week 2**: Complete Level 1 Course → Build basic workflows
3. **Week 3**: Study Best Practices → Implement patterns
4. **Week 4**: Review API docs → Build with API

## 🆘 Get Help

- **Documentation**: https://docs.n8n.io/
- **Community Forum**: https://community.n8n.io/
- **Discord**: https://discord.gg/n8n
- **GitHub Issues**: https://github.com/n8n-io/n8n/issues

## 📊 Current Status

**Instance**: https://your-n8n-instance.com
**Workflows**: 35 total (23 active)
**Phase**: Foundation Complete ✅
**Next**: Core API Client (Phase 2)

## 🔗 Important Links

| Resource | Link |
|----------|------|
| Documentation | https://docs.n8n.io/ |
| API Reference | https://docs.n8n.io/api/ |
| Templates | https://n8n.io/workflows/ |
| Community | https://community.n8n.io/ |
| YouTube | https://www.youtube.com/@n8n-io |
| GitHub | https://github.com/n8n-io/n8n |

## 📝 Notes

- All documentation is in Russian for better understanding
- Examples use real-world patterns
- Best practices based on official n8n recommendations
- MCP tools designed for AI assistant integration

## 🚀 Next Steps

1. Read [Quick Links](./docs/quick-links.md) for fast start
2. Review [Examples](./examples/) to understand patterns
3. Study [Best Practices](./docs/best-practices.md) before coding
4. Check [ROADMAP](./ROADMAP.md) for development plan

---

**Version**: 0.1.0  
**Last Updated**: 2025-11-14  
**License**: MIT

**Need help?** Check [docs/quick-links.md](./docs/quick-links.md) first!
