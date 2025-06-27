# aem-mcp-handler-docs

# AEM MCP Handler

Advanced Adobe Experience Manager (AEM) request handler with intelligent search, multi-locale support, and comprehensive content management capabilities for AI integrations.

## 🚀 Features

- **Intelligent Search**: Advanced fuzzy matching and cross-section path generation
- **Multi-Locale Support**: Automatic discovery and search across language variants
- **Comprehensive AEM Operations**: Full CRUD operations for pages, components, and assets
- **Smart Path Resolution**: Automatic site structure discovery and path suggestions
- **Validation & Feedback Loops**: Built-in result validation and search optimization
- **Enterprise Ready**: Supports complex AEM architectures and workflows

## 📦 Installation

```bash
npm install @venkatesh/aem-mcp-handler
```

## 🔧 Quick Setup

### 1. Environment Configuration

Create a `.env` file in your project:

```bash
# AEM Instance Configuration
AEM_HOST=http://localhost:4502
AEM_USERNAME=admin
AEM_PASSWORD=admin

# Optional: Timeout settings
AEM_TIMEOUT=30000
```

### 2. Basic Usage

```typescript
import { MCPRequestHandler } from '@venkatesh/aem-mcp-handler';
import { AEMConnector } from '@venkatesh/aem-mcp-handler/aem-connector';

// Initialize the connector
const aemConnector = new AEMConnector({
  host: process.env.AEM_HOST || 'http://localhost:4502',
  username: process.env.AEM_USERNAME,
  password: process.env.AEM_PASSWORD,
  timeout: parseInt(process.env.AEM_TIMEOUT || '30000')
});

// Initialize the MCP handler
const mcpHandler = new MCPRequestHandler(aemConnector);

// Example: Search for content
const searchResults = await mcpHandler.handleRequest('search', {
  searchTerm: 'homepage',
  basePath: '/content/mysite'
});

console.log('Search Results:', searchResults);
```

## 📋 Available Methods

### Content Search & Discovery

```typescript
// Comprehensive content search with intelligent path discovery
await mcpHandler.handleRequest('search', {
  searchTerm: 'product page',
  basePath: '/content/mysite',
  limit: 10
});

// Enhanced page search with fuzzy matching
await mcpHandler.handleRequest('searchPages', {
  searchTerm: 'about us',
  basePath: '/content/mysite'
});

// List all pages in a site
await mcpHandler.handleRequest('listPages', {
  siteRoot: '/content/mysite',
  depth: 2,
  limit: 50
});
```

### Content Management

```typescript
// Get page or component content
await mcpHandler.handleRequest('getContent', {
  path: '/content/mysite/en/homepage',
  depth: 2
});

// Update component properties
await mcpHandler.handleRequest('updateComponent', {
  path: '/content/mysite/en/homepage/jcr:content/hero',
  properties: {
    title: 'New Hero Title',
    description: 'Updated description'
  }
});

// Validate component before update
await mcpHandler.handleRequest('validateComponent', {
  path: '/content/mysite/en/homepage/jcr:content/hero',
  properties: {
    title: 'New Title'
  }
});
```

### Asset Management

```typescript
// Search for assets
await mcpHandler.handleRequest('searchAssets', {
  searchTerm: 'logo',
  basePath: '/content/dam'
});

// Get asset metadata
await mcpHandler.handleRequest('getAssetMetadata', {
  assetPath: '/content/dam/images/logo.png'
});
```

### Workflow & Publishing

```typescript
// Get workflow status
await mcpHandler.handleRequest('getWorkflowStatus', {
  workflowId: 'workflow-123'
});

// Check page activation status
await mcpHandler.handleRequest('getActivationStatus', {
  pagePath: '/content/mysite/en/homepage'
});
```

## 🎯 Use Cases

### 1. Chatbot Integration

```typescript
import { MCPRequestHandler } from '@venkatesh/aem-mcp-handler';

class AEMChatbot {
  constructor(private mcpHandler: MCPRequestHandler) {}

  async handleUserQuery(userMessage: string) {
    // Extract intent and parameters from user message
    const intent = this.extractIntent(userMessage);
    
    switch (intent.type) {
      case 'search':
        return await this.mcpHandler.handleRequest('search', {
          searchTerm: intent.searchTerm,
          basePath: intent.basePath || '/content'
        });
        
      case 'update':
        return await this.mcpHandler.handleRequest('updateComponent', {
          path: intent.componentPath,
          properties: intent.properties
        });
        
      default:
        return { error: 'Intent not recognized' };
    }
  }
}
```

### 2. Content Migration Tool

```typescript
async function migrateContent(sourceInstance: MCPRequestHandler, targetInstance: MCPRequestHandler) {
  // Get all pages from source
  const pages = await sourceInstance.handleRequest('listPages', {
    siteRoot: '/content/oldsite',
    depth: 5
  });

  for (const page of pages.results) {
    // Get content from source
    const content = await sourceInstance.handleRequest('getContent', {
      path: page.path,
      depth: 3
    });

    // Create/update in target
    await targetInstance.handleRequest('updateComponent', {
      path: page.path.replace('/oldsite/', '/newsite/'),
      properties: content.properties
    });
  }
}
```

### 3. Multi-Locale Content Sync

```typescript
async function syncContentAcrossLocales(handler: MCPRequestHandler) {
  const masterContent = await handler.handleRequest('getContent', {
    path: '/content/mysite/language-masters/en/homepage'
  });

  const locales = ['de', 'fr', 'es', 'it'];
  
  for (const locale of locales) {
    await handler.handleRequest('updateComponent', {
      path: `/content/mysite/language-masters/${locale}/homepage`,
      properties: {
        ...masterContent.properties,
        // Keep locale-specific fields
        title: await translateTitle(masterContent.properties.title, locale)
      }
    });
  }
}
```

## 🔍 Advanced Search Features

The handler includes sophisticated search capabilities:

### Cross-Section Path Generation
Automatically discovers and searches across:
- Language masters (`/language-masters/en`, `/language-masters/de`)
- Country/locale combinations (`/us/en`, `/de/de`, `/ca/fr`)
- Direct locale paths (`/en`, `/fr`, `/es`)

### Fuzzy Matching
- Levenshtein distance calculation
- Term variation generation (camelCase, kebab-case, etc.)
- Similarity scoring and ranking

### Validation & Feedback
- Result validation with confidence scoring
- Automatic retry logic for low-confidence results
- Comprehensive search reporting

## 🛠️ Configuration Options

### AEM Connector Options

```typescript
const connector = new AEMConnector({
  host: 'https://author.mysite.com',
  username: 'service-user',
  password: 'secure-password',
  timeout: 30000,
  retryAttempts: 3,
  retryDelay: 1000
});
```

### Search Configuration

```typescript
await mcpHandler.handleRequest('search', {
  searchTerm: 'product',
  basePath: '/content/mysite',
  limit: 20,
  includeInactive: false,
  searchDepth: 5,
  fuzzyThreshold: 0.7
});
```

## 📊 Response Formats

### Search Response

```typescript
{
  success: true,
  results: [
    {
      path: '/content/mysite/en/products',
      title: 'Products Page',
      lastModified: '2024-01-15T10:30:00Z',
      score: 0.95
    }
  ],
  searchReport: {
    strategiesUsed: ['enhanced', 'fuzzy', 'cross-section'],
    pathsExplored: ['/content/mysite/en', '/content/mysite/de'],
    totalAttempts: 3,
    confidence: 0.89,
    coverage: 'comprehensive'
  },
  total: 1
}
```

### Content Response

```typescript
{
  success: true,
  content: {
    'jcr:primaryType': 'cq:Page',
    'jcr:content': {
      'jcr:title': 'Homepage',
      'sling:resourceType': 'mysite/components/page',
      // ... other properties
    }
  },
  metadata: {
    lastModified: '2024-01-15T10:30:00Z',
    lastModifiedBy: 'admin'
  }
}
```

## 🔒 Security Considerations

- Use service users with minimal required permissions
- Store credentials securely (environment variables, vault systems)
- Implement proper authentication for production instances
- Validate all input parameters to prevent injection attacks

## 🧪 Testing

```bash
# Run all tests
npm test

# Run with coverage
npm run test:coverage

# Run specific test suites
npm run test:unit
npm run test:integration
```

## 📈 Performance Tips

1. **Use specific base paths**: Narrow down search scope for better performance
2. **Set appropriate limits**: Avoid large result sets that may timeout
3. **Cache frequently accessed content**: Implement caching layer for repeated queries
4. **Use batch operations**: Group multiple updates when possible

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/new-feature`
3. Commit changes: `git commit -am 'Add new feature'`
4. Push to branch: `git push origin feature/new-feature`
5. Submit a Pull Request

## 📄 License

MIT License - see LICENSE file for details.

## 🆘 Support

- **Issues**: [GitHub Issues](https://github.com/venky7799/aem-mcp-handler-docs/issues)
- **Documentation**: [Full API Documentation](https://github.com/venky7799/aem-mcp-handler-docs)
- **Examples**: [Code Examples](https://github.com/venky7799/aem-mcp-handler-docs/tree/main/examples)

## 🔄 Migration Guide

### From Direct AEM API Calls

Replace direct axios calls:

```typescript
// Before
const response = await axios.get(`${aemHost}/content/mysite.2.json`);

// After  
const response = await mcpHandler.handleRequest('getContent', {
  path: '/content/mysite',
  depth: 2
});
```

### From Other AEM Libraries

The MCP Handler provides enhanced search and multi-locale support not available in basic AEM connectors, making it ideal for AI-powered applications and complex content management scenarios. 
