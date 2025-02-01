# React Deployment Guide

## Deployment Preparation
<!-- 
Documentation:
- Create React App Deployment: https://create-react-app.dev/docs/deployment
- Vercel Documentation: https://vercel.com/docs
- Environment Variables: https://create-react-app.dev/docs/adding-custom-environment-variables
-->

### 1. Build Optimization
```bash
# Production build command
npm run build

# Analyze bundle size
npm install --save-dev source-map-explorer
```

```json
// package.json
{
  "scripts": {
    "analyze": "source-map-explorer 'build/static/js/*.js'",
    "build": "react-scripts build",
    "postbuild": "npm run analyze"
  }
}
```

### 2. Environment Configuration
```env
# .env.development
REACT_APP_API_URL=http://localhost:3000
REACT_APP_DEBUG=true

# .env.production
REACT_APP_API_URL=https://api.production.com
REACT_APP_DEBUG=false
```

```jsx
// Usage in code
const apiUrl = process.env.REACT_APP_API_URL;
const isDebug = process.env.REACT_APP_DEBUG === 'true';
```

## Vercel Deployment

### 1. Initial Setup
```bash
# Install Vercel CLI
npm install -g vercel

# Login to Vercel
vercel login

# Initialize project
vercel init
```

### 2. Configuration
```json
// vercel.json
{
  "version": 2,
  "builds": [
    {
      "src": "package.json",
      "use": "@vercel/static-build",
      "config": { "distDir": "build" }
    }
  ],
  "routes": [
    {
      "src": "/static/(.*)",
      "headers": { "cache-control": "public, max-age=31536000, immutable" },
      "dest": "/static/$1"
    },
    { "src": "/favicon.ico", "dest": "/favicon.ico" },
    { "src": "/manifest.json", "dest": "/manifest.json" },
    { "src": "/(.*)", "dest": "/index.html" }
  ]
}
```

### 3. Deployment Commands
```bash
# Deploy to preview
vercel

# Deploy to production
vercel --prod

# List deployments
vercel ls

# Remove deployment
vercel remove <deployment-id>
```

## CI/CD Setup

### 1. GitHub Actions
```yaml
# .github/workflows/deploy.yml
name: Deploy
on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      
      - name: Setup Node.js
        uses: actions/setup-node@v2
        with:
          node-version: '16'
          
      - name: Install dependencies
        run: npm ci
        
      - name: Run tests
        run: npm test
        
      - name: Build
        run: npm run build
        
      - name: Deploy to Vercel
        uses: amondnet/vercel-action@v20
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.ORG_ID}}
          vercel-project-id: ${{ secrets.PROJECT_ID}}
          vercel-args: '--prod'
```

## Performance Monitoring

### 1. Web Vitals Setup
```jsx
// src/reportWebVitals.js
import { getCLS, getFID, getLCP } from 'web-vitals';

const reportWebVitals = (onPerfEntry) => {
  if (onPerfEntry && onPerfEntry instanceof Function) {
    getCLS(onPerfEntry);
    getFID(onPerfEntry);
    getLCP(onPerfEntry);
  }
};

export default reportWebVitals;
```

### 2. Error Tracking
```jsx
// src/errorBoundary.js
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    // Log to error tracking service
    console.error('Error:', error);
    console.error('Error Info:', errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return <h1>Something went wrong.</h1>;
    }
    return this.props.children;
  }
}
```

## Security Considerations

### 1. Security Headers
```json
// vercel.json
{
  "headers": [
    {
      "source": "/(.*)",
      "headers": [
        {
          "key": "X-Content-Type-Options",
          "value": "nosniff"
        },
        {
          "key": "X-Frame-Options",
          "value": "DENY"
        },
        {
          "key": "X-XSS-Protection",
          "value": "1; mode=block"
        }
      ]
    }
  ]
}
```

### 2. Environment Variables Protection
```jsx
// Only expose necessary variables to the client
const publicEnvVars = {
  apiUrl: process.env.REACT_APP_API_URL,
  version: process.env.REACT_APP_VERSION
};

// Don't expose sensitive data
// ❌ Bad
const apiKey = process.env.REACT_APP_API_KEY;

// ✅ Good - Use backend proxy
const apiEndpoint = '/api/protected-route';
```

## Deployment Checklist

1. **Pre-deployment**
   - [ ] Run all tests
   - [ ] Check bundle size
   - [ ] Verify environment variables
   - [ ] Update version number

2. **Deployment**
   - [ ] Create production build
   - [ ] Deploy to staging
   - [ ] Run smoke tests
   - [ ] Deploy to production

3. **Post-deployment**
   - [ ] Verify all routes
   - [ ] Check performance metrics
   - [ ] Monitor error rates
   - [ ] Verify analytics

## Additional Resources
- [Create React App Deployment](https://create-react-app.dev/docs/deployment)
- [Vercel Documentation](https://vercel.com/docs)
- [GitHub Actions](https://docs.github.com/en/actions)
- [Web Vitals](https://web.dev/vitals/)
- [Security Headers](https://securityheaders.com) 