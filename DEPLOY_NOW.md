# 🚀 Deploy Now - Quick Commands

## Copy & Paste These Commands

### Step 1: Push to GitHub

```bash
# Initialize git (if not already done)
git init

# Add all files
git add .

# Commit
git commit -m "Ready for deployment"

# Set main branch
git branch -M main

# Add your GitHub repository (REPLACE WITH YOUR REPO URL)
git remote add origin https://github.com/YOUR_USERNAME/snapspend.git

# Push
git push -u origin main
```

---

### Step 2: Deploy Backend to Render

**Go to**: https://render.com/dashboard

**Click**: "New +" → "Web Service"

**Settings to copy-paste**:

```
Name: snapspend-backend
Root Directory: backend_python
Build Command: pip install --upgrade pip && pip install -r requirements.txt
Start Command: gunicorn app:app --bind 0.0.0.0:$PORT
```

**Environment Variables**:
```
PYTHON_VERSION = 3.11.0
GEMINI_API_KEY = AIzaSyAVmkYjpAfFFbOkpclE0nEC9rzamrH_AcE
```

**Click**: "Create Web Service"

**Wait**: 5-10 minutes

**Copy your backend URL**: `https://YOUR-APP.onrender.com`

---

### Step 3: Update Frontend Config

**Edit** `.env.production` file:

```bash
# Replace with your actual Render URL
VITE_API_URL=https://YOUR-APP.onrender.com
```

**Push changes**:

```bash
git add .env.production
git commit -m "Add production API URL"
git push
```

---

### Step 4: Deploy Frontend to Vercel

**Option A: Using CLI (Recommended)**

```bash
# Install Vercel CLI
npm install -g vercel

# Login to Vercel
vercel login

# Deploy to production
vercel --prod
```

**Option B: Using Dashboard**

**Go to**: https://vercel.com/dashboard

**Click**: "Add New..." → "Project"

**Import**: Your GitHub repository

**Settings**:
```
Framework Preset: Vite
Build Command: npm run build
Output Directory: dist
```

**Environment Variable**:
```
Key: VITE_API_URL
Value: https://YOUR-APP.onrender.com
```

**Click**: "Deploy"

---

### Step 5: Test Your Deployment

**Test Backend**:
```bash
# Replace with your actual URL
curl https://YOUR-APP.onrender.com/api/hello
```

**Expected**:
```json
{"message": "Hello from Python Flask server! 🐍"}
```

**Test Frontend**:
1. Open your Vercel URL in browser
2. Upload a CSV file
3. Check if data displays
4. Test chatbot

---

## 🎉 Done!

Your app is now live!

**Backend**: https://YOUR-APP.onrender.com
**Frontend**: https://YOUR-APP.vercel.app

---

## ⚠️ Important Notes

1. **First request is slow**: Render free tier sleeps after 15 minutes. First request takes 30-60 seconds.

2. **Data is temporary**: Your app stores data in memory. It's lost when backend restarts.

3. **API Key security**: Don't share your Gemini API key publicly!

4. **Free tier limits**:
   - Render: 750 hours/month
   - Vercel: 100GB bandwidth/month

---

## 🆘 Need Help?

Check these files:
- `DEPLOYMENT_GUIDE.md` - Complete guide
- `DEPLOY_CHECKLIST.md` - Step-by-step checklist
- `TROUBLESHOOTING.md` - Common issues

Or check the logs:
- **Render logs**: Dashboard → Your service → Logs
- **Vercel logs**: Dashboard → Your project → Deployments → Logs
