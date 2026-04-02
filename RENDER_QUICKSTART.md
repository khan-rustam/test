# 🚀 QUICK START: Deploy to Render in 5 Steps

## Step 1: Backup & Test Locally ✅

```bash
# Make sure everything works locally
python main.py

# Test the API
curl http://localhost:5000/docs
```

## Step 2: Push to GitHub 📤

```bash
git add -A
git commit -m "chore: prepare Render deployment"
git push origin main
```

## Step 3: Go to Render 🌐

1. Visit https://render.com
2. Click **"New"** → **"Web Service"**  
3. Select your GitHub repo

## Step 4: Configure in Render

| Field | Value |
|-------|-------|
| **Name** | `cattlebreed-api` |
| **Environment** | Python |
| **Region** | Choose yours |
| **Branch** | `main` |
| **Build Command** | `pip install --upgrade pip setuptools wheel && pip install -r requirements.txt` |
| **Start Command** | `uvicorn main:app --host 0.0.0.0 --port $PORT --workers 2` |
| **Plan** | Free |

## Step 5: Add Environment Variables 🔑

In Render dashboard → **Environment**, add:

```
JWT_SECRET=PASTE_HERE_A_SECURE_KEY
```

**Generate a secure key:**
```bash
python -c "import secrets; print(secrets.token_urlsafe(32))"
```

---

✨ **Click "Create Web Service"** and Render will deploy automatically!

**Your API URL will be:** `https://cattlebreed-api.onrender.com`

---

## ⚡ What's Included

- ✅ **render.yaml** - Render configuration
- ✅ **requirements.txt** - Updated dependencies
- ✅ **DEPLOYMENT.md** - Detailed guide
- ✅ **DEPLOYMENT_CHECKLIST.md** - Step-by-step checklist
- ✅ **GitHub Actions** - Auto-deploy on push (optional)

## 🧪 Test After Deployment

Open in browser: `https://cattlebreed-api.onrender.com/docs`

---

💡 **Need Help?** See `DEPLOYMENT_CHECKLIST.md` for troubleshooting
