# Axios Webpack 5 Compatibility Fix

## Issue

When running the main website, you may encounter multiple errors related to axios and webpack 5:

```
Module not found: Error: Can't resolve '../utils.js' in axios
Module not found: Error: Can't resolve 'http' in axios
Module not found: Error: Can't resolve 'https' in axios
Module not found: Error: Can't resolve 'stream' in axios
```

## Root Cause

Newer versions of axios (1.7.0+) have compatibility issues with webpack 5 used by Create React App. Webpack 5 removed automatic polyfills for Node.js core modules, and newer axios versions try to import these modules.

## Solution Applied

The project has been configured to use **axios version 1.6.0**, which is stable and compatible with React's webpack configuration.

## Verification

Check your axios version:

```bash
npm list axios
```

Should show:
```
└── axios@1.6.0
```

## If You Still See Errors

### Option 1: Reinstall Dependencies (Recommended)

```bash
# Remove node_modules and package-lock.json
rmdir /s /q node_modules
del package-lock.json

# Reinstall with correct version
npm install
```

### Option 2: Manual Fix

```bash
# Uninstall current axios
npm uninstall axios

# Install specific version
npm install axios@1.6.0
```

### Option 3: Clear Cache

```bash
# Clear npm cache
npm cache clean --force

# Remove node_modules
rmdir /s /q node_modules

# Reinstall
npm install
```

## Why Not Use Latest Axios?

While axios 1.7.x+ has new features, it requires additional webpack configuration that conflicts with Create React App's default setup. Using axios 1.6.0:

- ✅ Works out of the box with CRA
- ✅ No webpack configuration needed
- ✅ Stable and well-tested
- ✅ Has all features needed for this project
- ✅ No polyfill issues

## Alternative Solutions (Not Recommended)

If you absolutely need the latest axios, you would need to:

1. **Eject from Create React App** (irreversible):
   ```bash
   npm run eject
   ```

2. **Configure webpack fallbacks** in `webpack.config.js`:
   ```javascript
   resolve: {
     fallback: {
       "http": false,
       "https": false,
       "stream": false,
       "util": false,
       "zlib": false,
       "url": false,
       "assert": false
     }
   }
   ```

3. **Or use CRACO** to override webpack config without ejecting

**However, this is NOT recommended** as it adds complexity and potential issues.

## For Admin Dashboard

The admin dashboard uses Vite instead of webpack, so it doesn't have this issue. It can use any axios version.

Current versions:
- Main Website: axios@1.6.0 (locked)
- Admin Dashboard: axios@^1.6.0 (can upgrade)
- Backend: No axios (uses native Node.js)

## Testing After Fix

1. Start the main website:
   ```bash
   npm start
   ```

2. Check for errors in terminal

3. Open http://localhost:3000

4. Check browser console (F12) - should be no axios errors

5. Test API calls by browsing products

## Related Files

- `package.json` - axios version locked to 1.6.0
- `src/services/api.js` - uses axios for API calls
- `src/pages/Cart.jsx` - uses axios for orders
- `src/pages/Home.jsx` - uses axios for products

## Prevention

To prevent this issue in the future:

1. **Don't upgrade axios** in the main website without testing
2. **Lock the version** in package.json (no ^ or ~)
3. **Test after any dependency updates**
4. **Check compatibility** before upgrading

## Summary

✅ **Fixed**: axios downgraded to 1.6.0
✅ **Stable**: No webpack polyfill issues
✅ **Working**: All API calls function correctly
✅ **Locked**: Version won't auto-upgrade

The main website should now start without errors!
