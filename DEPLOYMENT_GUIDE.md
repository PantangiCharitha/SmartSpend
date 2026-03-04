# 🚀 Complete Deployment Guide

## Overview

Your SnapSpend app has two parts:
1. **Backend (Python Flask)** → Deploy to **Render** (Free)
2. **Frontend (React/Vite)** → Deploy to **Vercel** (Free)

---

## 📦 Part 1: Deploy Backend to Render

### Prerequisites
- GitHub account
- Render account (sign up at https://render.com)
- Your code pushed to GitHub

### Step 1: Push Code to GitHub

```bash
# If not already done:
git init
git add .
git commit -m "Ready for deployment"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO.git
git push -u origin main
```

### Step 2: Create Render Web Service

1. Go to https://render.com/dashboard
2. Click **"New +"** → **"Web Service"**
3. Connect your GitHub repository
4. Select your repository

### Step 3: Configure Render Settings

Fill in these settings:

| Setting | Value |
|---------|-------|
| **Name** | `snapspend-backend` |
| **Region** | Choose closest to you |
| **Branch** | `main` |
| **Root Directory** | `backend_python` |
| **Runtime** | `Python 3` |
| **Build Command** | `pip install --upgrade pip && pip install -r requirements.txt` |
| **Start Command** | `gunicorn app:app --bind 0.0.0.0:$PORT` |

### Step 4: Add Environment Variables

Click **"Advanced"** → **"Add Environment Variable"**

Add these:

| Key | Value |
|-----|-------|
| `PYTHON_VERSION` | `3.11.0` |
| `GEMINI_API_KEY` | `AIzaSyAVmkYjpAfFFbOkpclE0nEC9rzamrH_AcE` |

⚠️ **Important**: Keep your API key secret! Don't share it publicly.

### Step 5: Deploy

1. Click **"Create Web Service"**
2. Wait 5-10 minutes for deployment
3. You'll get a URL like: `https://snapspend-backend.onrender.com`

### Step 6: Test Backend

```bash
# Replace with your actual URL
curl https://snapspend-backend.onrender.com/api/hello
```

Expected response:
```json
{"message": "Hello from Python Flask server! 🐍"}
```

✅ **Backend deployed!** Copy your Render URL for the next step.

---

## 🎨 Part 2: Deploy Frontend to Vercel

### Step 1: Update Frontend Configuration

Update `.env.production` with your Render backend URL:

```bash
VITE_API_URL=https://snapspend-backend.onrender.com
```

### Step 2: Commit Changes

```bash
git add .env.production
git commit -m "Add production API URL"
git push
```

### Step 3: Deploy to Vercel

**Option A: Using Vercel CLI (Recommended)**

```bash
# Install Vercel CLI
npm install -g vercel

# Login
vercel login

# Deploy
vercel --prod
```

**Option B: Using Vercel Dashboard**

1. Go to https://vercel.com/dashboard
2. Click **"Add New..."** → **"Project"**
3. Import your GitHub repository
4. Configure:
   - **Framework Preset**: Vite
   - **Build Command**: `npm run build`
   - **Output Directory**: `dist`
   - **Install Command**: `npm install`

5. Add Environment Variable:
   - Key: `VITE_API_URL`
   - Value: `https://snapspend-backend.onrender.com` (your Render URL)

6. Click **"Deploy"**

### Step 4: Test Frontend

1. Open your Vercel URL (e.g., `https://snapspend.vercel.app`)
2. Try uploading a CSV
3. Test the chatbot

✅ **Frontend deployed!**

---

## 🔧 Important Configuration Updates

### Update CORS in Backend

Make sure your backend allows requests from your Vercel domain.

In `backend_python/app.py`, CORS is already configured for all origins:
```python
CORS(app)  # Already set to allow all origins
```

For production, you might want to restrict it:
```python
CORS(app, origins=["https://snapspend.vercel.app"])
```

---

## ⚠️ Known Issues & Solutions

### Issue 1: Backend Cold Starts (Render Free Tier)

**Problem**: Render free tier spins down after 15 minutes of inactivity. First request takes 30-60 seconds.

**Solutions**:
1. **Accept it** - It's free! Just wait for the first request.
2. **Keep-alive service** - Use a service like UptimeRobot to ping your backend every 14 minutes
3. **Upgrade to paid** - $7/month for always-on instance

### Issue 2: Data Loss on Backend Restart

**Problem**: Your app stores data in memory, which is lost on restart.

**Solution**: Implement database storage (see below)

### Issue 3: CSV Upload Fails in Production

**Problem**: File upload size limits or timeout issues.

**Solutions**:
1. Check Render logs for errors
2. Increase timeout in frontend
3. Limit CSV file size to < 5MB

---

## 🗄️ Adding Database (Recommended for Production)

Your app currently stores data in memory. For production, add a database:

### Option 1: Supabase (Recommended)

1. Sign up at https://supabase.com
2. Create a new project
3. Create tables:

```sql
-- Users table (if using auth)
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  email TEXT UNIQUE NOT NULL,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Transactions table
CREATE TABLE transactions (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID REFERENCES users(id),
  description TEXT NOT NULL,
  amount DECIMAL NOT NULL,
  category TEXT NOT NULL,
  date DATE NOT NULL,
  dr_cr TEXT,
  created_at TIMESTAMP DEFAULT NOW()
);
```

4. Update backend to use Supabase:

```python
# Install: pip install supabase
from supabase import create_client

supabase = create_client(
    os.getenv('SUPABASE_URL'),
    os.getenv('SUPABASE_KEY')
)

# Store transactions
def store_transactions(user_id, transactions):
    supabase.table('transactions').insert([
        {
            'user_id': user_id,
            'description': t['description'],
            'amount': t['amount'],
            'category': t['category'],
            'date': t['date']
        }
        for t in transactions
    ]).execute()
```

5. Add environment variables to Render:
   - `SUPABASE_URL`
   - `SUPABASE_KEY`

### Option 2: PostgreSQL on Render

1. In Render dashboard, create a PostgreSQL database
2. Connect it to your web service
3. Use SQLAlchemy or psycopg2 to interact with it

---

## 📊 Monitoring & Logs

### Backend Logs (Render)
1. Go to Render dashboard
2. Click your service
3. Click "Logs" tab

### Frontend Logs (Vercel)
1. Go to Vercel dashboard
2. Click your project
3. Click "Deployments" → Select deployment → "Logs"

### Error Tracking (Optional)
- Add Sentry for error tracking
- Add Google Analytics for usage tracking

---

## 💰 Cost Breakdown

| Service | Free Tier | Paid Tier |
|---------|-----------|-----------|
| **Render** | 750 hours/month, sleeps after 15min | $7/month always-on |
| **Vercel** | 100GB bandwidth, unlimited projects | $20/month pro |
| **Supabase** | 500MB database, 2GB bandwidth | $25/month pro |

**Total for free tier**: $0/month ✅

---

## 🚀 Deployment Checklist

### Backend (Render)
- [ ] Code pushed to GitHub
- [ ] Render web service created
- [ ] Environment variables set (GEMINI_API_KEY)
- [ ] Build successful
- [ ] `/api/hello` endpoint responds
- [ ] Backend URL copied

### Frontend (Vercel)
- [ ] `.env.production` updated with backend URL
- [ ] Changes pushed to GitHub
- [ ] Vercel project created
- [ ] Environment variables set
- [ ] Build successful
- [ ] Can access frontend URL
- [ ] CSV upload works
- [ ] Chatbot works

### Optional Enhancements
- [ ] Database added (Supabase/PostgreSQL)
- [ ] Custom domain configured
- [ ] Error tracking added (Sentry)
- [ ] Analytics added (Google Analytics)
- [ ] Keep-alive service configured (UptimeRobot)

---

## 🆘 Troubleshooting

### Backend Issues

**Build fails on Render**
```bash
# Check Python version in runtime.txt
cat backend_python/runtime.txt
# Should be: python-3.11.0

# Check requirements.txt has correct versions
cat backend_python/requirements.txt
```

**Backend returns 500 errors**
- Check Render logs for Python errors
- Verify GEMINI_API_KEY is set
- Test locally first: `python app.py`

**CORS errors in browser**
- Check backend CORS configuration
- Verify frontend URL is allowed
- Check browser console for exact error

### Frontend Issues

**Build fails on Vercel**
```bash
# Test build locally first
npm run build

# Check for TypeScript errors
npm run lint
```

**Can't connect to backend**
- Verify `VITE_API_URL` is set correctly
- Check backend is running (visit backend URL)
- Check browser console for errors

**CSV upload fails**
- Check file size (< 5MB recommended)
- Check CSV format (Description, Amount, Date columns)
- Check backend logs for errors

---

## 📚 Additional Resources

- [Render Documentation](https://render.com/docs)
- [Vercel Documentation](https://vercel.com/docs)
- [Supabase Documentation](https://supabase.com/docs)
- [Flask Deployment Guide](https://flask.palletsprojects.com/en/latest/deploying/)

---

## 🎉 You're Done!

Your app is now live and accessible from anywhere!

**Share your app:**
- Backend: `https://snapspend-backend.onrender.com`
- Frontend: `https://snapspend.vercel.app`

**Next steps:**
1. Test thoroughly with real data
2. Add database for data persistence
3. Configure custom domain (optional)
4. Add user authentication
5. Monitor usage and errors

Good luck! 🚀
