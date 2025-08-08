# Full-Stack Deployment Template (Railway + GitHub Pages)
## ✅ BATTLE-TESTED: FlowCRM Architecture

**CRITICAL**: Use localStorage JWT tokens, NOT HttpOnly cookies for cross-domain authentication!

## Quick Setup Checklist

### Backend (Railway)
```bash
# 1. Initialize
railway login
railway init [project-name]
railway add postgresql

# 2. Configure Environment
railway variables set NODE_ENV=production
railway variables set JWT_SECRET=your-super-secret-32-char-key
railway variables set CORS_ORIGIN=https://yourusername.github.io

# 3. Deploy
git push origin main
```

### Frontend (GitHub Pages)
```bash
# 1. Install dependencies
npm install gh-pages --save-dev

# 2. Configure package.json
{
  "homepage": "https://yourusername.github.io/ProjectName",
  "scripts": {
    "build:production": "set \"VITE_API_URL=https://your-railway-url.up.railway.app/api\" && vite build",
    "predeploy": "npm run build:production", 
    "deploy": "gh-pages -d dist"
  }
}

# 3. Configure vite.config.js
{
  base: '/ProjectName/',
}

# 4. Deploy
npm run deploy
```

## 🔐 CRITICAL: Authentication Architecture

### ❌ NEVER Use HttpOnly Cookies (Cross-Domain Issues)
```javascript
// DON'T DO THIS - Causes SameSite cookie errors
res.cookie('authToken', token, { 
  httpOnly: true,
  sameSite: 'strict'  // ← Blocks cross-domain requests
});
```

### ✅ ALWAYS Use localStorage JWT Tokens
```javascript
// Backend: Return token in response body
app.post('/auth/login', async (req, res) => {
  const token = jwt.sign({ userId: user.id }, JWT_SECRET);
  res.json({ token, user });  // ← Client stores token
});

// Frontend: Store and send token
localStorage.setItem('authToken', token);
headers: { Authorization: `Bearer ${token}` }
```

## Essential Files

### Backend Authentication Setup:
```javascript
// server.js - CORS Configuration
const corsOptions = {
  origin: ['https://yourusername.github.io', 'http://localhost:5173'],
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'DELETE', 'OPTIONS'],
  allowedHeaders: ['Content-Type', 'Authorization']
};

// middleware/auth.js - JWT Middleware (NOT cookies)
const authMiddleware = (req, res, next) => {
  const token = req.headers.authorization?.split(' ')[1]; // Bearer TOKEN
  
  if (!token) {
    return res.status(401).json({ error: 'No token provided' });
  }
  
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;
    next();
  } catch (error) {
    res.status(401).json({ error: 'Invalid token' });
  }
};
```

### Frontend Authentication Setup:
```javascript
// utils/tokenStorage.js
export const tokenStorage = {
  getToken: () => localStorage.getItem('authToken'),
  setToken: (token) => localStorage.setItem('authToken', token),
  removeToken: () => localStorage.removeItem('authToken'),
  hasToken: () => !!localStorage.getItem('authToken')
};

// utils/api.js - Automatic token injection
api.interceptors.request.use((config) => {
  const token = tokenStorage.getToken();
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

// Handle 401 errors
api.interceptors.response.use(
  (response) => response.data,
  (error) => {
    if (error.response?.status === 401) {
      tokenStorage.removeToken();
      window.location.href = '/ProjectName/login';
    }
    return Promise.reject(error);
  }
);
```

## 🚨 Critical Pitfalls (Learn from FlowCRM)

### 1. Cookie Cross-Domain Issues
**Error**: `Cookie "authToken" rejected because it is in a cross-site context`
**Solution**: Use localStorage JWT tokens instead of cookies

### 2. Windows Build Script Issues  
**Error**: `set VITE_API_URL=url && command` adds space to URL
**Solution**: Use quotes: `set "VITE_API_URL=url" && command`

### 3. CORS Preflight Failures
**Error**: `blocked by CORS policy`
**Solution**: Configure CORS to allow GitHub Pages origin and Authorization header

### 4. SameSite Restrictions
**Error**: `SameSite=Lax` blocks cross-domain cookies
**Solution**: Don't use cookies - use localStorage + Bearer tokens

## ✅ Success Indicators (FlowCRM Tested)
- ✅ No "Cookie rejected" errors in console
- ✅ No "SameSite" warnings
- ✅ Login from GitHub Pages stores JWT token
- ✅ API requests include `Authorization: Bearer <token>`
- ✅ 401 errors properly clear token and redirect
- ✅ Page refresh preserves authentication state

## 🎯 Production Examples

**Successful Implementation**: [FlowCRM](https://github.com/costasford/FlowCRM)
- Frontend: https://costasford.github.io/FlowCRM
- Backend: https://flowcrm-production-1465.up.railway.app
- Authentication: localStorage JWT (works perfectly across domains)

This template is based on real production experience - follow exactly to avoid authentication pitfalls!