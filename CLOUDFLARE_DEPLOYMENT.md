# Cloudflare Pages Deployment Guide

## Critical Fix Required

### Issue 1: Remove Deploy Command

**Problem**: Cloudflare Pages is trying to run `npx wrangler deploy` which fails.

**Solution**: 
1. Go to your Cloudflare Pages project settings
2. Navigate to **Builds & deployments** → **Build configuration**
3. Set the following required fields:
   - **Build command**: `npm run build`
   - **Deploy command**: `:` (required field - colon `:` is a bash no-op command that always succeeds)
   - **Path**: `dist` (CRITICAL: This must be `dist`, NOT `/` - this is where Astro outputs your built files)
   - **Non-production branch deploy command**: `:` (if required, otherwise leave empty)
4. Cloudflare Pages will automatically deploy the `dist` folder after build completes

**Why**: 
- The **Path** field is CRITICAL - it must be set to `dist` (NOT `/`). This tells Cloudflare Pages where to find your built files
- The **Deploy command** field is required, so use `:` (colon) - this is a bash no-op command that always succeeds and does nothing. Cloudflare Pages automatically deploys after build completes, so this command just satisfies the required field
- If `:` doesn't work, try `exit 0` as an alternative
- Do NOT use `true`, `echo ""`, or `npx wrangler deploy` - those can cause errors
- You should NOT use `wrangler deploy` or `npx wrangler versions upload` - those are for Cloudflare Workers, not Pages

### Issue 2: Build Configuration

**Required Build Settings**:
- **Build command**: `npm run build` ✅
- **Deploy command**: `:` ✅ (REQUIRED field - colon `:` is a bash no-op command that always succeeds)
- **Path**: `dist` ✅ (CRITICAL: Must be `dist`, NOT `/` - this is where Astro outputs built files)
- **Root directory**: `/` (this is correct for root of repository)
- **Node version**: 18 or higher (Cloudflare uses Node 22 by default)

**Alternative if `:` doesn't work**: Try `exit 0` as the deploy command

**Common Mistakes to Avoid**:
- ❌ Path set to `/` - This is WRONG! Must be `dist`
- ❌ Deploy command with `true` - This causes "internal error"! Use `:` instead
- ❌ Deploy command with `echo ""` - This can cause errors! Use `:` instead
- ❌ Deploy command with `npx wrangler deploy` - This is WRONG! Use `:` instead
- ❌ Version command with `npx wrangler versions upload` - This is WRONG! Remove it or use `:`

### Issue 3: Image Paths Fixed

✅ Fixed incorrect image path in `Header.astro`:
- Changed from: `../../public/images/btmlogo.webp` (WRONG)
- Changed to: `/images/btmlogo.webp` (CORRECT)

**Important**: Files in the `public/` folder must be referenced with absolute paths starting with `/`, not relative paths.

## Verification

After removing the deploy command, your deployment should:
1. ✅ Build successfully (already working)
2. ✅ Automatically deploy the `dist` folder
3. ✅ Serve CSS and images correctly

## Troubleshooting

If CSS/images still don't load:
1. Check browser console for 404 errors
2. Verify asset paths are absolute (starting with `/`)
3. Ensure `public/` folder files are included in build
4. Check Cloudflare Pages deployment logs for errors
