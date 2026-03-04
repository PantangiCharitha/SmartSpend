# ✅ Quick Deployment Checklist

## Before You Start

- [ ] Code is working locally
- [ ] Backend runs without errors
- [ ] CSV upload works locally
- [ ] Chatbot works locally
- [ ] GitHub account ready
- [ ] Render account created (https://render.com)
- [ ] Vercel account created (https://vercel.com)

---

## Step 1: Push to GitHub (5 minutes)

```bash
git init
git add .
git commit -m "Ready for deployment"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/snapspend.git
git push -u origin main
```

- [ ] Code pushed to GitHub
- [ ] Repository is public or connected to Render/Vercel

---

## Step 2: Deploy Backend to Render (10 minutes)

### 2.1 Create Web Service
1. Go to https://render.com/dashboard
2. Click "New +" → "Web Service"
3. Connect GitHub repository

- [ ] Repository connected

### 2.2 Configure Settings
```
Name: snapspend-backend
Root Directory: backend_python
Build Command: pip install --upgrade pip && pip install -r requirements.txt
Start Command: gunicorn app:app --bind 0.0.0.0:$PORT
```

- [ ] Settings configured

### 2.3 Add Environment Variables
```
PYTHON_VERSION = 3.11.0
GEMINI_API_KEY = AIzaSyAVmkYjpAfFFbOkpclE0nEC9rzamrH_AcE
```

- [ ] Environment variables added

### 2.4 Deploy
- [ ] Clicked "Create Web Service"
- [ ] Build completed successfully
- [ ] Service is live

### 2.5 Test Backend
```bash
curl https://YOUR-APP.onrender.com/api/hello
```

- [ ] Backend responds correctly
- [ ] Copied backend URL: `_______________________________`

---

## Step 3: Update Frontend Config (2 minutes)

Edit `.env.production`:
```
VITE_API_URL=https://YOUR-APP.onrender.com
```

```bash
git add .env.production
git commit -m "Add production API URL"
git push
```

- [ ] `.env.production` updated
- [ ] Changes pushed to GitHub

---

## Step 4: Deploy Frontend to Vercel (5 minutes)

### Option A: Vercel CLI
```bash
npm install -g vercel
vercel login
vercel --prod
```

### Option B: Vercel Dashboard
1. Go to https://vercel.com/dashboard
2. Click "Add New..." → "Project"
3. Import GitHub repository
4. Configure:
   - Framework: Vite
   - Build Command: `npm run build`
   - Output Directory: `dist`
5. Add environment variable:
   - `VITE_API_URL` = `https://YOUR-APP.onrender.com`
6. Click "Deploy"

- [ ] Frontend deployed
- [ ] Build successful
- [ ] Copied frontend URL: `_______________________________`

---

## Step 5: Test Everything (5 minutes)

### Test Backend
- [ ] Visit: `https://YOUR-APP.onrender.com/api/hello`
- [ ] Returns: `{"message": "Hello from Python Flask server! 🐍"}`

### Test Frontend
- [ ] Visit your Vercel URL
- [ ] Homepage loads correctly
- [ ] Can navigate to Transaction Analyzer
- [ ] Can upload CSV file
- [ ] Charts display correctly
- [ ] Can navigate to Chatbot
- [ ] Chatbot responds to questions

---

## Common Issues

### ❌ Backend build fails
**Fix**: Check `backend_python/runtime.txt` has `python-3.11.0`

### ❌ Frontend can't connect to backend
**Fix**: Verify `VITE_API_URL` in Vercel environment variables

### ❌ CORS errors
**Fix**: Backend CORS is already configured for all origins

### ❌ CSV upload fails
**Fix**: 
1. Check backend logs in Render
2. Verify CSV format (Description, Amount, Date)
3. Try smaller file (< 5MB)

### ❌ Backend is slow (first request)
**Normal**: Render free tier sleeps after 15 minutes. First request takes 30-60 seconds.

---

## 🎉 Success!

Your app is now live at:
- **Backend**: https://YOUR-APP.onrender.com
- **Frontend**: https://YOUR-APP.vercel.app

---

## Next Steps (Optional)

- [ ] Add custom domain
- [ ] Set up database (Supabase)
- [ ] Add user authentication
- [ ] Configure keep-alive service (UptimeRobot)
- [ ] Add error tracking (Sentry)
- [ ] Add analytics (Google Analytics)

---

## 📝 Notes

**Backend URL**: _______________________________

**Frontend URL**: _______________________________

**Deployment Date**: _______________________________

**Issues Encountered**: 




---

**Total Time**: ~30 minutes
**Cost**: $0 (using free tiers)
