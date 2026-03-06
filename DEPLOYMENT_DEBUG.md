# 🔍 Deployment Debugging Guide

## Common Deployment Issues & Fixes

---

## Issue 1: Backend Not Responding

### Symptoms:
- Frontend shows "Failed to fetch"
- Backend URL returns 404 or timeout
- CSV upload fails

### Debug Steps:

**1. Check if backend is actually running**

Visit your backend URL directly:
```
https://YOUR-APP.onrender.com/api/hello
```

**Expected response:**
```json
{"message": "Hello from Python Flask server! 🐍"}
```

**If you get 404 or "Not Found":**
- Backend didn't deploy correctly
- Check Render logs (see below)

**If you get timeout:**
- Backend is sleeping (Render free tier)
- Wait 30-60 seconds and try again
- First request after sleep is always slow

**2. Check Render Logs**

1. Go to https://render.com/dashboard
2. Click your service
3. Click "Logs" tab
4. Look for errors

**Common errors:**

**Error: `ModuleNotFoundError: No module named 'flask'`**
```
Fix: Check build command includes:
pip install --upgrade pip && pip install -r requirements.txt
```

**Error: `ModuleNotFoundError: No module named 'pandas'`**
```
Fix: Make sure requirements.txt includes pandas
```

**Error: `Port already in use`**
```
Fix: Make sure start command uses $PORT:
gunicorn app:app --bind 0.0.0.0:$PORT
```

**Error: `GEMINI_API_KEY not found`**
```
Fix: Add GEMINI_API_KEY to environment variables in Render
```

---

## Issue 2: Frontend Can't Connect to Backend

### Symptoms:
- Frontend loads but CSV upload fails
- Console shows CORS errors
- "Failed to fetch" errors

### Debug Steps:

**1. Check Frontend Environment Variable**

In Vercel dashboard:
1. Go to your project
2. Click "Settings" → "Environment Variables"
3. Check `VITE_API_URL` is set correctly

**Should be:**
```
VITE_API_URL=https://YOUR-APP.onrender.com
```

**NOT:**
```
VITE_API_URL=http://localhost:5000  ❌
VITE_API_URL=  ❌ (empty)
```

**2. Check Browser Console**

Press F12 in browser, go to Console tab.

**If you see:**
```
Failed to fetch
```
→ Backend is not responding or wrong URL

**If you see:**
```
CORS policy: No 'Access-Control-Allow-Origin' header
```
→ Backend CORS not configured (but it should be!)

**If you see:**
```
net::ERR_NAME_NOT_RESOLVED
```
→ Wrong backend URL in environment variable

**3. Redeploy Frontend**

After fixing environment variables:
1. Go to Vercel dashboard
2. Click "Deployments"
3. Click "..." on latest deployment
4. Click "Redeploy"

---

## Issue 3: CSV Upload Fails

### Symptoms:
- Upload button doesn't work
- "No file uploaded" error
- Upload times out

### Debug Steps:

**1. Check File Size**
- Render free tier has limits
- Keep CSV under 5MB
- Try with smaller file first

**2. Check CSV Format**

Your CSV MUST have these columns:
```csv
Description,Amount,Date,DR/CR
Swiggy Order,450,2024-01-15,DR
Salary,50000,2024-01-01,CR
```

**3. Check Backend Logs**

Look for:
```
[FILE] File upload received
[OK] Loaded X rows from CSV
```

If you see errors, they'll tell you what's wrong.

**4. Test with Sample CSV**

Use one of the sample files from `backend/data/`:
- `sampleCSV.csv`
- `test_student_budget.csv`

---

## Issue 4: Chatbot Not Working

### Symptoms:
- Chatbot says "No data loaded"
- Chatbot gives generic answers
- Chatbot errors

### Debug Steps:

**1. Upload CSV First**
- Chatbot needs data to work
- Upload CSV in Transaction Analyzer
- Then try chatbot

**2. Check Data Endpoint**

Visit:
```
https://YOUR-APP.onrender.com/api/check-data
```

**Should return:**
```json
{
  "loaded": true,
  "count": 45,
  "message": "Data is loaded!"
}
```

**If loaded is false:**
- CSV wasn't uploaded successfully
- Backend restarted (data lost)
- Upload CSV again

**3. Check Gemini API Key**

In Render dashboard:
1. Go to Environment Variables
2. Check `GEMINI_API_KEY` is set
3. Verify it's the correct key

**4. Check Backend Logs**

Look for:
```
[CHAT] AI Chat question received
[AI] Processing question: ...
[OK] AI response generated
```

---

## Issue 5: Backend Keeps Sleeping (Render Free Tier)

### Symptoms:
- First request takes 30-60 seconds
- Backend stops responding after 15 minutes
- Users complain about slowness

### Solutions:

**Option 1: Accept It (Free)**
- It's normal for free tier
- Just wait for first request
- Tell users to be patient

**Option 2: Keep-Alive Service (Free)**
1. Sign up at https://uptimerobot.com
2. Create new monitor
3. Set URL: `https://YOUR-APP.onrender.com/api/hello`
4. Set interval: 14 minutes
5. This pings your backend to keep it awake

**Option 3: Upgrade to Paid ($7/month)**
- Always-on instance
- No cold starts
- Better performance

---

## Issue 6: Data Disappears

### Symptoms:
- Uploaded CSV data is gone
- Have to re-upload after some time
- Chatbot forgets data

### Cause:
Your app stores data in memory. When backend restarts, data is lost.

### Solutions:

**Short-term: Accept it**
- Re-upload CSV when needed
- It's a limitation of in-memory storage

**Long-term: Add Database**

See `DEPLOYMENT_GUIDE.md` for instructions on adding Supabase database.

---

## Quick Diagnostic Commands

**Test Backend:**
```bash
# Replace with your actual URL
curl https://YOUR-APP.onrender.com/api/hello
```

**Test Data Status:**
```bash
curl https://YOUR-APP.onrender.com/api/check-data
```

**Test CSV Upload:**
```bash
curl -X POST https://YOUR-APP.onrender.com/analyze-transactions \
  -F "csvFile=@backend/data/sampleCSV.csv"
```

---

## Complete Checklist

### Backend (Render)
- [ ] Service is running (not "Build failed")
- [ ] `/api/hello` endpoint responds
- [ ] Environment variables set correctly
- [ ] Build command is correct
- [ ] Start command uses `$PORT`
- [ ] Logs show no errors

### Frontend (Vercel)
- [ ] Build successful
- [ ] `VITE_API_URL` environment variable set
- [ ] Points to correct backend URL
- [ ] No CORS errors in console
- [ ] Can load homepage

### Integration
- [ ] Frontend can reach backend
- [ ] CSV upload works
- [ ] Data displays in charts
- [ ] Chatbot responds

---

## Still Not Working?

### Collect This Information:

1. **Backend URL**: _______________________________
2. **Frontend URL**: _______________________________
3. **Error message**: _______________________________
4. **Browser console errors**: (screenshot)
5. **Render logs**: (copy last 50 lines)
6. **Vercel logs**: (copy last 50 lines)

### Common Quick Fixes:

**Try these in order:**

1. **Redeploy backend**
   - Go to Render dashboard
   - Click "Manual Deploy" → "Deploy latest commit"

2. **Redeploy frontend**
   - Go to Vercel dashboard
   - Click "Deployments" → "Redeploy"

3. **Clear browser cache**
   - Press Ctrl+Shift+Delete
   - Clear cache and cookies
   - Refresh page

4. **Try different browser**
   - Sometimes browser cache causes issues
   - Try incognito/private mode

5. **Check environment variables again**
   - Render: GEMINI_API_KEY, PYTHON_VERSION
   - Vercel: VITE_API_URL

6. **Wait for backend to wake up**
   - If using free tier, first request takes time
   - Wait 60 seconds and try again

---

## Need More Help?

Share these details:
- Your backend URL
- Your frontend URL  
- Exact error message
- Screenshots of errors
- Render logs
- Vercel logs

I can help debug specific issues!
