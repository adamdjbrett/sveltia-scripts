# svletia-scripts JCRT CMS Setup (Self-Hosted on Cloudflare)

This project uses **Sveltia CMS** on **Eleventy**. To avoid using Netlify,
 we use a custom **Cloudflare Worker** as an OAuth Proxy to handle GitHub authentication securely.

## 🛠 Prerequisites
Before you can log in to `https://jcrt.xmit.dev/admin/`, you need to set up your own 
authentication gate on Cloudflare.

---

## 1. Create GitHub OAuth App
1. Go to your [GitHub OAuth Settings](https://github.com/settings/developers).
2. Click **New OAuth App**.
3. Fill in the details:
   - **Application Name:** `JCRT CMS`
   - **Homepage URL:** `https://jcrt.xmit.dev`
   - **Authorization callback URL:** `https://[your-worker-name].[your-subdomain].workers.dev/callback`
4. Register the app and save the **Client ID** and **Client Secret**.

## 2. Deploy Auth Proxy to Cloudflare
The "engine" for the login lives on Cloudflare, not on your hosting server.
1. Log in to your **Cloudflare Dashboard**.
2. Go to **Workers & Pages** > **Create Worker**.
3. Name it `jcrt-auth` and click **Deploy**.
4. Click **Edit Code**, delete all default code, and **Paste** the content from `/worker.js` (attachment email).
5. Click **Save and Deploy**.

## 3. Add Environment Variables
To keep your GitHub credentials safe:
1. Inside your Worker dashboard, go to **Settings** > **Variables**.
2. Add these two **Environment Variables**:
   - `GITHUB_CLIENT_ID`: (Your GitHub Client ID)
   - `GITHUB_CLIENT_SECRET`: (Your GitHub Client Secret)
3. Click **Save and Deploy**.

## 4. Final Connection
1. Copy your **Worker URL** (e.g., `https://jcrt-auth.adam.workers.dev`).
2. Open `public/admin/config.yml` in this repo.
3. Update the `base_url` with your Worker URL:
   ```yaml
   backend:
     name: github
     repo: adamdjbrett/jcrt.org
     branch: main
     base_url: [https://jcrt-auth.adam.workers.dev](https://jcrt-auth.adam.workers.dev) # <-- Replace with your URL
     auth_endpoint: auth
