# 🔧 Fix Your Deployment - "Failed to Fetch" Error

## The Problem

Your frontend is trying to connect to `http://localhost:5000`, but that doesn't exist in production!

The error message says "Backend is running on port 5000" - but that's checking for LOCAL backend, not your deployed Render backend.

---

## ✅ The Fix (5 minutes)

### Step 1: Get Your Render Backend URL

1. Go to https://render.com/dashboard
2. Click on your backend service
3. Copy the URL at the top (looks like: `https://snapspend-backend-xxxx.onrender.com`)

**Your backend URL**: `_______________________________`

---

### Step 2: Add Environment Variable in Vercel

1. Go to https://vercel.com/dashboard
2. Click on your project (`smart-spend-21aixiv82-pantangichanthas-projects`)
3. Click **"Settings"** tab
4. Click **"Environment Variables"** in left sidebar
5. Click **"Add New"**

**Add this:**
```
Key: VITE_API_URL
Value: https://YOUR-RENDER-URL.onrender.com
```

**Example:**
```
Key: VITE_API_URL
Value: https://snapspend-backend-abc123.onrender.com
```

⚠️ **Important**: 
- NO trailing slash at the end
- Use HTTPS (not HTTP)
- Use your actual Render URL

6. Select **"Production"** environment
7. Click **"Save"**

---

### Step 3: Redeploy Frontend

After adding the environment variable:

1. Go to **"Deployments"** tab in Vercel
2. Click the **"..."** menu on the latest deployment
3. Click **"Redeploy"**
4. Wait 1-2 minutes for deployment to complete

---

### Step 4: Test Again

1. Open your Vercel URL: `https://smart-spend-21aixiv82-pantangichanthas-projects.vercel.app`
2. Go to Transaction Analyzer
3. Try uploading a CSV
4. It should work now! ✅

---

## 🔍 How to Verify It's Fixed

### Check 1: Backend is Responding

Open this URL in your browser (replace with your actual Render URL):
```
https://YOUR-RENDER-URL.onrender.com/api/hello
```

**Should see:**
```json
{"message": "Hello from Python Flask server! 🐍"}
```

**If you see this** ✅ - Backend is working!

**If you get 404 or timeout** ❌ - Backend has issues (see below)

---

### Check 2: Frontend is Using Correct URL

1. Open your Vercel app
2. Press **F12** (open browser console)
3. Go to **Console** tab
4. Try uploading a CSV
5. Look for this line:
   ```
   🌐 Uploading to: https://YOUR-RENDER-URL.onrender.com/analyze-transactions
   ```

**Should NOT see:**
```
🌐 Uploading to: http://localhost:5000/analyze-transactions  ❌
```

---

## ⚠️ Common Issues

### Issue 1: Backend Returns 404

**Cause**: Backend didn't deploy correctly on Render

**Fix**:
1. Go to Render dashboard
2. Check if service is "Live" (green)
3. Click "Logs" tab
4. Look for errors
5. If you see errors, click "Manual Deploy" → "Deploy latest commit"

---

### Issue 2: Backend Times Out (Takes Forever)

**Cause**: Render free tier sleeps after 15 minutes of inactivity

**Fix**: 
- Wait 30-60 seconds for backend to wake up
- Try again
- First request after sleep is always slow
- This is normal for free tier!

---

### Issue 3: Still Getting "Failed to Fetch"

**Possible causes:**

1. **Environment variable not set correctly**
   - Go back to Vercel settings
   - Check `VITE_API_URL` is set
   - Make sure it's for "Production" environment
   - Redeploy after changing

2. **Browser cache**
   - Press Ctrl+Shift+Delete
   - Clear cache and cookies
   - Refresh page
   - Or try incognito mode

3. **Wrong backend URL**
   - Make sure URL is HTTPS (not HTTP)
   - Make sure no trailing slash
   - Make sure it's your actual Render URL

---

## 📋 Quick Checklist

- [ ] Got Render backend URL
- [ ] Added `VITE_API_URL` to Vercel environment variables
- [ ] Selected "Production" environment
- [ ] Saved the variable
- [ ] Redeployed frontend
- [ ] Waited for deployment to complete
- [ ] Tested backend URL directly (should return JSON)
- [ ] Tested CSV upload on frontend
- [ ] Checked browser console (should show correct URL)

---

## 🎯 Expected Result

After fixing:

1. ✅ Backend URL test returns JSON message
2. ✅ Frontend console shows correct backend URL
3. ✅ CSV upload works
4. ✅ Data displays in charts
5. ✅ Chatbot works

---

## 🆘 Still Not Working?

### Collect this info:

1. **Your Render backend URL**: _______________________________
2. **Your Vercel frontend URL**: _______________________________
3. **What happens when you visit backend URL directly?**
4. **Screenshot of browser console (F12)**
5. **Screenshot of Vercel environment variables**

### Quick Debug:

**Test backend directly:**
```bash
# Replace with your actual Render URL
curl https://YOUR-RENDER-URL.onrender.com/api/hello
```

**Check Vercel environment variables:**
1. Vercel dashboard → Your project → Settings → Environment Variables
2. Screenshot it
3. Make sure `VITE_API_URL` is there

---

## 💡 Pro Tip

After deployment, always:
1. Test backend URL directly first
2. Then test frontend
3. Check browser console for errors
4. Check that frontend is using correct backend URL

---

**The fix is simple: Just add the environment variable in Vercel and redeploy!** 🚀
