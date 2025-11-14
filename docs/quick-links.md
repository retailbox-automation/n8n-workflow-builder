# 🔗 Quick Links для n8n Builder

## 🚀 Быстрый старт

### Для начинающих
1. **Quickstart**: https://docs.n8n.io/try-it-out/
2. **First Workflow**: https://docs.n8n.io/workflows/create/
3. **Basic Concepts**: https://docs.n8n.io/workflows/

### Для продвинутых
1. **API Reference**: https://docs.n8n.io/api/
2. **Best Practices**: https://docs.n8n.io/workflows/optimizing/
3. **Advanced Patterns**: https://community.n8n.io/

## 📚 Самые полезные разделы документации

### Must Read
- ✅ **Expressions**: https://docs.n8n.io/code/expressions/
- ✅ **Error Handling**: https://docs.n8n.io/workflows/error-handling/
- ✅ **HTTP Request Node**: https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.httprequest/
- ✅ **Webhook**: https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.webhook/
- ✅ **Code Node**: https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.code/

## 🎯 По задачам

### Хотите создать API endpoint?
→ **Webhook Node**: https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.webhook/

### Нужно обработать данные?
→ **Set Node**: https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.set/
→ **Code Node**: https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.code/

### Работа с базами данных?
→ **PostgreSQL**: https://docs.n8n.io/integrations/builtin/app-nodes/n8n-nodes-base.postgres/
→ **MySQL**: https://docs.n8n.io/integrations/builtin/app-nodes/n8n-nodes-base.mysql/
→ **MongoDB**: https://docs.n8n.io/integrations/builtin/app-nodes/n8n-nodes-base.mongodb/

### Условная логика?
→ **IF Node**: https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.if/
→ **Switch Node**: https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.switch/

### Обработка ошибок?
→ **Error Handling Guide**: https://docs.n8n.io/workflows/error-handling/
→ **Error Trigger**: https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.errortrigger/

## 🔥 Популярные шаблоны

1. **Webhook → Database**: https://n8n.io/workflows/
2. **Email Processing**: https://n8n.io/workflows/?search=email
3. **Slack Notifications**: https://n8n.io/workflows/?search=slack
4. **AI Chatbot**: https://n8n.io/workflows/?search=ai+chat
5. **Data Sync**: https://n8n.io/workflows/?search=sync

## 💡 Expressions Шпаргалка

```javascript
// Текущая дата и время
{{ $now }}                    // ISO timestamp
{{ $now.toFormat('yyyy-MM-dd') }}  // Форматирование
{{ $today }}                  // Сегодняшний день

// Данные из предыдущей ноды
{{ $json.fieldName }}         // Простое поле
{{ $json["field-name"] }}     // Поле с дефисом
{{ $json.nested.field }}      // Вложенное поле

// Работа с массивами
{{ $items.length }}           // Длина массива
{{ $items[0].json.name }}     // Первый элемент
{{ $items.map(i => i.json.id) }}  // Маппинг

// Условия
{{ $json.status === 'active' ? 'Yes' : 'No' }}

// Environment variables
{{ $env.API_KEY }}            // Из .env файла

// Предыдущие ноды
{{ $('Node Name').item.json.field }}
```

## 🛠️ Часто используемые паттерны

### Pattern 1: Retry with Exponential Backoff
```json
{
  "continueOnFail": true,
  "retryOnFail": true,
  "maxTries": 3,
  "waitBetweenTries": 1000
}
```

### Pattern 2: Error Notification
```
HTTP Request (continueOnFail: true)
  ↓
IF (Check for error)
  ├─ True → Send Slack Notification
  └─ False → Continue
```

### Pattern 3: Batch Processing
```
Trigger
  ↓
Split In Batches (100 items)
  ↓
Process Items
  ↓
Loop Back
```

## 🆘 Где искать помощь

1. **Официальные docs**: https://docs.n8n.io/
2. **Community Forum**: https://community.n8n.io/
3. **Discord**: https://discord.gg/n8n
4. **GitHub Issues**: https://github.com/n8n-io/n8n/issues
5. **Stack Overflow**: https://stackoverflow.com/questions/tagged/n8n

## 🎓 Обучение

### Бесплатные курсы
- **Level 1**: https://docs.n8n.io/courses/level-one/
- **Level 2**: https://docs.n8n.io/courses/level-two/

### YouTube
- **Official Channel**: https://www.youtube.com/@n8n-io
- **Tutorials Playlist**: https://www.youtube.com/c/n8nio/playlists

## ⚡ Pro Tips

1. **Pin Data** для тестирования - не нужно каждый раз запускать workflow
2. **Use Set Node** для трансформации данных вместо Code когда возможно
3. **Error Workflow** - создайте один workflow для обработки всех ошибок
4. **Environment Variables** - используйте для API ключей и конфигов
5. **Sub-workflows** - разбивайте сложные workflows на модули

## 🔍 Поиск по документации

**Прямой поиск**: https://docs.n8n.io/search/

**По топикам**:
- Triggers: https://docs.n8n.io/integrations/builtin/trigger-nodes/
- Core Nodes: https://docs.n8n.io/integrations/builtin/core-nodes/
- App Nodes: https://docs.n8n.io/integrations/builtin/app-nodes/
- AI Nodes: https://docs.n8n.io/integrations/builtin/cluster-nodes/

---

**Сохраните эту страницу** - здесь 80% ссылок, которые вам понадобятся! 🎯
