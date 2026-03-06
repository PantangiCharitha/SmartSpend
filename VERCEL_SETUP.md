# 🚀 Vercel Setup Instructions

Your backend URL: `https://smartspend-ofnk.onrender.com`

---

## Step 1: Go to Vercel

Open: https://vercel.com/dashboard

---

## Step 2: Import Your Project (if not already done)

**If you already have the project deployed:**
- Skip to Step 3

**If this is your first time:**
1. Click **"Add New..."** → **"Project"**
2. Click **"Import Git Repository"**
3. Find your repo: `PantangiCharitha/SmartSpend`
4. Click **"Import"**
5. Configure:
   - **Framework Preset**: Vite
   - **Build Command**: `npm run build`
   - **Output Directory**: `dist`
   - **Install Command**: `npm install`

---

## Step 3: Add Environment Variable

1. Go to your project in Vercel
2. Click **"Settings"** tab
3. Click **"Environment Variables"** in left sidebar
4. Click **"Add New"**

**Enter EXACTLY this:**

```
Key: VITE_API_URL
Value: https://smartspend-ofnk.onrender.com
```

5. Select **"Production"** ✅
6. Click **"Save"**

---

## Step 4: Redeploy

1. Click **"Deployments"** tab
2. Find the latest deployment
3. Click the **"..."** menu (three dots)
4. Click **"Redeploy"**
5. Wait 1-2 minutes

---

## Step 5: Test Backend First

Before testing frontend, make sure backend is ready:

Open this in browser:
```
https://smartspend-ofnk.onrender.com/api/hello
```

**Should see:**
```json
{"message": "Hello from Python Flask server! 🐍"}
```

**If you see "Service Unavailable":**
- Backend is still deploying or sleeping
- Wait 60 seconds and try again
- First request after sleep takes 30-60 seconds

**If you see this JSON message** ✅ → Backend is ready!

---

## Step 6: Test Your App

1. Open your Vercel URL
2. Go to **Transaction Analyzer**
3. Upload a CSV file
4. Should work! ✅

---

## 🔍 Debugging

**If upload still fails:**

1. **Check browser console (F12)**
   - Should see: `🌐 Uploading to: https://smartspend-ofnk.onrender.com/analyze-transactions`
   - Should NOT see: `http://localhost:5000`

2. **Clear browser cache**
   - Press Ctrl+Shift+Delete
   - Clear cache
   - Refresh page

3. **Verify environment variable**
   - Go to Vercel Settings → Environment Variables
   - Check `VITE_API_URL` is set correctly
   - Make sure it's for "Production" environment

4. **Redeploy again**
   - Sometimes takes 2 deployments for env vars to work
   - Go to Deployments → Redeploy

---

## ✅ Success Checklist

- [ ] Backend deployed on Render
- [ ] Backend URL responds with JSON at `/api/hello`
- [ ] Environment variable added to Vercel
- [ ] Frontend redeployed
- [ ] Browser cache cleared
- [ ] CSV upload works
- [ ] Data displays in charts
- [ ] Chatbot works

---

## 🎉 When It Works

You should be able to:
- Upload CSV files
- See transaction charts
- Use the chatbot
- View budget nudges
- Check insights

All without "Failed to fetch" errors!

---

**Your backend URL**: `https://smartspend-ofnk.onrender.com`

**Remember**: First request after 15 minutes of inactivity takes 30-60 seconds (Render free tier limitation).
