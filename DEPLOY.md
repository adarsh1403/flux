<!-- Step-by-step guide for deploying Flux completely free on Render and Vercel. -->

# Deploying Flux for Free ($0/month)

This guide walks you through deploying Flux with **Render** (for the backend) and **Vercel** (for the frontend). Both services provide free tiers with zero credit card required.

---

## Prerequisites

1. Your code pushed to a **GitHub repository**.
2. A **Google Gemini API Key** (from [Google AI Studio](https://aistudio.google.com/)).
3. A **GitHub Personal Access Token** (from [GitHub Token Settings](https://github.com/settings/tokens)).

---

## Step 1: Deploy the Backend on Render (Free)

1. Go to [render.com](https://render.com) and create an account (or sign in with GitHub).
2. Click **New +** in the top right and choose **Web Service**.
3. Select **Build and deploy from a Git repository** and pick your `flux` repository.
4. Configure the service settings:
   - **Name**: `flux-backend`
   - **Region**: Closest to you (e.g., `Oregon (US West)` or `Frankfurt`)
   - **Root Directory**: `backend`
   - **Runtime**: `Python 3`
   - **Build Command**: `pip install -r requirements.txt`
   - **Start Command**: `uvicorn main:app --host 0.0.0.0 --port $PORT`
   - **Instance Type**: Select **Free** ($0/month)
5. Scroll down to **Environment Variables** and add:
   - `PYTHON_VERSION` = `3.12.0`
   - `GEMINI_API_KEY` = `your_gemini_api_key`
   - `GEMINI_MODEL` = `gemini-2.5-flash`
   - `GITHUB_TOKEN` = `your_github_token`
   - `CORS_ORIGINS` = `*`
6. Click **Create Web Service**.
7. Render will build and launch your backend. Once deployed, note your service URL:
   `https://flux-backend-xxxx.onrender.com`
8. Verify it works by opening:
   `https://flux-backend-xxxx.onrender.com/api/health`
   You should see: `{"status":"healthy","app":"flux"}`

*(Note: Render free services sleep after 15 minutes of inactivity. When accessed after sleeping, the first request takes ~30–40 seconds to spin up.)*

---

## Step 2: Deploy the Frontend on Vercel (Free)

1. Go to [vercel.com](https://vercel.com) and sign in with GitHub.
2. Click **Add New...** -> **Project**.
3. Import your `flux` repository.
4. Under **Configure Project**:
   - **Framework Preset**: `Next.js` (automatically detected)
   - **Root Directory**: Click **Edit** and choose `frontend`
5. Expand the **Environment Variables** section and add:
   - **Name**: `NEXT_PUBLIC_BACKEND_URL`
   - **Value**: `https://flux-backend-xxxx.onrender.com` *(your Render backend URL from Step 1, without trailing slash)*
6. Click **Deploy**.
7. In about 60 seconds, Vercel will give you a live production URL (e.g. `https://flux-app.vercel.app`).

---

## Step 3: Tighten CORS (Recommended for Production)

Once you have your Vercel URL:
1. Go to your Render Dashboard -> `flux-backend` -> **Environment**.
2. Change `CORS_ORIGINS` from `*` to:
   `https://flux-app.vercel.app,http://localhost:3000`
3. Click **Save Changes** (Render will automatically redeploy).

---

## Alternative: Local / VPS 1-Command Deployment

If you want to run the full stack on your own computer or any Linux VM:

1. Copy `.env.example` to `.env` and fill in your keys.
2. Run:
   ```bash
   docker compose up -d --build
   ```
3. Access:
   - Frontend: `http://localhost:3000`
   - Backend API: `http://localhost:8000/docs`
