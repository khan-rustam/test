# Deployment Guide - Render

## Overview
This is a FastAPI application that uses PyTorch for cattle breed and BCS (Body Condition Score) prediction. This guide covers deployment to Render.

## Prerequisites
- GitHub account with the repository pushed
- Render account (https://render.com)
- Git installed locally

## Step 1: Push to GitHub

```bash
git add .
git commit -m "Add deployment configuration"
git push origin main
```

**Important:** Make sure your `.pth` model files are tracked in git. If they're too large (>100MB), you may need to use Git LFS:

```bash
git lfs install
git lfs track "*.pth"
git add .gitattributes
git commit -m "Add Git LFS tracking for model files"
git push origin main
```

## Step 2: Create Render Service

1. Go to https://render.com and sign up/login
2. Click **"New +"** → **"Web Service"**
3. Connect your GitHub repository
4. Fill in the configuration:
   - **Name:** `cattlebreed-backend` (or your preferred name)
   - **Environment:** Python
   - **Region:** Choose closest to your users
   - **Branch:** `main`
   - **Build Command:** `pip install -r requirements.txt`
   - **Start Command:** `uvicorn main:app --host 0.0.0.0 --port $PORT`
   - **Plan:** Free (or upgrade for better performance)

## Step 3: Environment Variables

In the Render dashboard, go to **Environment** and set:

```
JWT_SECRET=generate-a-long-random-string-here
BACKEND_URL=https://your-app-name.onrender.com
DATABASE_URL=sqlite:///./database.db
PYTHON_VERSION=3.11
```

**Important:** Generate a secure JWT_SECRET. You can use:
```bash
python -c "import secrets; print(secrets.token_urlsafe(32))"
```

## Step 4: Deploy

Click **"Create Web Service"** and Render will automatically:
1. Clone your repository
2. Install dependencies
3. Start the application
4. Assign a URL like `https://cattlebreed-backend.onrender.com`

## Monitoring & Debugging

- **Logs:** View in Render dashboard under "Logs"
- **Common Issues:**
  - **Model files missing:** Ensure `.pth` files are committed to git
  - **ModuleNotFoundError:** Check `requirements.txt` includes all dependencies
  - **Database errors:** The database will be created automatically on first run

## Important Notes

### SQLite Database
- The SQLite database is ephemeral on Render's free tier
- For persistent storage, consider:
  - **Option 1:** Upgrade to Render's Paid tier with persistent disks
  - **Option 2:** Use PostgreSQL instead:
    ```python
    # In models.py, change:
    engine = create_engine(os.environ.get("DATABASE_URL"))
    ```
    And set `DATABASE_URL=postgresql://...` in Render environment

### Model Files
- The `.pth` files are large. If they exceed 100MB each:
  - Use Git LFS to track them
  - Or use object storage (AWS S3, GCS) and download them during build

### Performance
- PyTorch CPU inference on Render's free tier will be slow
- For better performance, upgrade to a paid instance
- Consider using ONNX format for faster inference

## API Endpoints

```
POST   /api/register           - Register new user
POST   /api/login              - Login user
POST   /api/predict            - Predict breed & BCS (requires auth)
GET    /api/history            - Get prediction history (requires auth)
DELETE /api/history            - Clear all history (requires auth)
DELETE /api/history/{log_id}   - Delete specific prediction
PUT    /api/profile            - Update user profile
POST   /api/profile/picture    - Upload profile picture
```

## Local Testing Before Deployment

```bash
# Install dependencies
pip install -r requirements.txt

# Run locally
python main.py

# Test API
curl -X POST http://localhost:5000/api/register \
  -H "Content-Type: application/json" \
  -d '{"name":"Test","email":"test@example.com","password":"pass123"}'
```

## SSL/HTTPS
Render automatically provides SSL certificates. Your app will be accessible at `https://your-app-name.onrender.com`

## Questions?
See Render docs: https://render.com/docs
