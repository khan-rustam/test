# 🚀 Render Deployment Checklist

Follow these steps to deploy your Cattle Breed project to Render.

## ✅ Pre-Deployment Checklist (Do Locally First)

- [ ] Test the app locally:
  ```bash
  pip install -r requirements.txt
  python main.py
  ```

- [ ] Test API endpoints locally (use Postman, curl, or the API docs at http://localhost:5000/docs)

- [ ] Make sure `.pth` model files are present:
  ```bash
  ls -lh *.pth
  ```

- [ ] Verify git history is clean:
  ```bash
  git status
  git log --oneline -5
  ```

## 📤 Upload to GitHub

```bash
# Stage all files
git add -A

# Commit with a message
git commit -m "chore: prepare for Render deployment"

# Push to GitHub
git push origin main
```

**If model files are too large (>100MB each):**
```bash
# Install Git LFS
git lfs install

# Track .pth files
git lfs track "*.pth"

# Commit and push
git add .gitattributes
git commit -m "chore: use Git LFS for model files"
git push origin main
```

## 🌐 Create Render Account

1. Go to https://render.com
2. Sign up with GitHub (recommended)
3. Authorize access to your repositories

## 🔧 Deploy to Render

### Option A: Using Render Dashboard (Recommended)

1. **Click "New +"** → **Web Service**
2. **Select your repository**
3. **Configure:**
   - Service Name: `cattlebreed-api` (or preferred name)
   - Environment: Python
   - Region: Choose closest to you
   - Branch: main
   - Root Directory: `.` (leave empty)
   - Build Command: `pip install --upgrade pip setuptools wheel && pip install -r requirements.txt`
   - Start Command: `uvicorn main:app --host 0.0.0.0 --port $PORT --workers 2`
   - Plan: Free (good for testing) or Starter+ for production

4. **Click "Create Web Service"** (Render will start deploying)

### Option B: Using render.yaml (Already Set Up)

Render will automatically detect `render.yaml` in your repo. Just push to GitHub and it will use the configuration there.

## 🔑 Set Environment Variables

In Render dashboard → **Environment**:

1. **JWT_SECRET** (Generate a secure key)
   ```bash
   python -c "import secrets; print(secrets.token_urlsafe(32))"
   ```
   Copy the output and paste in Render

2. **Other variables** (Render auto-populates RENDER_EXTERNAL_URL):
   - `BACKEND_URL` → Set to `${RENDER_EXTERNAL_URL}` or your custom domain
   - `PYTHON_VERSION` → `3.11`

## ⏳ Wait for Deployment

- Render will automatically:
  - Clone your repository
  - Install dependencies (may take 2-5 minutes)
  - Load your `.pth` model files
  - Start the FastAPI server

- Watch the **Logs** tab in Render dashboard for real-time output

## ✨ Test Your Deployed API

Once deployed, Render assigns a URL like `https://cattlebreed-api.onrender.com`

**Test endpoints:**

```bash
# 1. Register
curl -X POST https://cattlebreed-api.onrender.com/api/register \
  -H "Content-Type: application/json" \
  -d '{"name":"Test User","email":"test@example.com","password":"password123"}'

# 2. Login
curl -X POST https://cattlebreed-api.onrender.com/api/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"password123"}'

# 3. Get API Docs (interactive Swagger UI)
# Open: https://cattlebreed-api.onrender.com/docs
```

## 🐛 Troubleshooting

| Issue | Solution |
|-------|----------|
| **"ModuleNotFoundError: No module named..."** | Check `requirements.txt` has all imports. Re-deploy after updating. |
| **"Model file not found"** | Ensure `.pth` files are committed to git. Check logs for file paths. |
| ****Slow predictions** | Upgrade from free tier. Consider ONNX models or GPU tier. |
| **Database errors** | SQLite is ephemeral on free tier. Use Render Postgres for persistent storage. |
| **Deployment takes too long** | PyTorch is large (~600MB). This is normal on first deploy. |

## 🔄 Auto-Deploy on Updates

After the initial setup, every `git push` to `main` will automatically trigger a new deployment.

```bash
# Make changes locally
git add .
git commit -m "Fix prediction accuracy"
git push origin main

# ✅ Render automatically redeploys (check Logs tab)
```

## 📊 Monitor Your App

- **Logs:** See real-time server logs
- **Metrics:** CPU, RAM, requests per second
- **Alerts:** Get notified if your service goes down

## 🔗 Custom Domain (Optional)

In Render → Settings → Custom Domains:
- Add your domain (e.g., `api.yourdomain.com`)
- Follow DNS setup instructions

## 💡 Next Steps

### For Production:
1. Use PostgreSQL instead of SQLite
2. Set up CDN for static files
3. Use environment config for secrets (not hardcoded)
4. Monitor performance and optimize

### For API Integration:
- Share the deployed URL with your frontend team
- Set CORS headers in frontend to allow requests
- Use JWT tokens for authentication

---

**Questions?** 
- Render Docs: https://render.com/docs
- FastAPI Docs: https://fastapi.tiangolo.com
- PyTorch Docs: https://pytorch.org

**Deployed Successfully?** 🎉 Share the URL with your team!
