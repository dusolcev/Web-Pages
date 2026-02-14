# PagesHub Setup Guide

This project allows you to manage standalone HTML pages in a GitHub repository using a browser-based dashboard. It uses GitHub OAuth for secure authentication and a Cloudflare Worker proxy to handle token exchange.

## 1. Create a GitHub OAuth App

To enable "Login with GitHub", you need to create an OAuth App on GitHub:

1.  Go to **GitHub Settings** > **Developer settings** > **OAuth Apps**.
2.  Click **New OAuth App**.
3.  **Application Name:** PagesHub (or anything you like).
4.  **Homepage URL:** `https://dusolcev.github.io/Web-Pages/` (Update this if your URL is different).
5.  **Authorization callback URL:** `https://dusolcev.github.io/Web-Pages/index.html` (Must match the exact URL of your dashboard).
6.  Click **Register application**.
7.  Copy the **Client ID** (you'll need this later).
8.  Click **Generate a new client secret** and copy it (you'll need this for the Cloudflare Worker).

## 2. Deploy the Cloudflare Worker Proxy

GitHub OAuth requires a `client_secret` to exchange an authorization code for an access token. Since this is a static site, we use a Cloudflare Worker as a secure proxy.

1.  Sign in to [Cloudflare Dashboard](https://dash.cloudflare.com/).
2.  Go to **Workers & Pages** > **Create application** > **Create Worker**.
3.  Name it `gh-oauth-proxy`.
4.  Click **Deploy**.
5.  Click **Edit code** and replace the content with the following:

```javascript
addEventListener('fetch', event => {
  event.respondWith(handleRequest(event.request))
})

async function handleRequest(request) {
  // Handle CORS preflight
  if (request.method === 'OPTIONS') {
    return new Response(null, {
      headers: {
        'Access-Control-Allow-Origin': '*',
        'Access-Control-Allow-Methods': 'POST, OPTIONS',
        'Access-Control-Allow-Headers': 'Content-Type',
      }
    })
  }

  if (request.method !== 'POST') {
    return new Response('Method Not Allowed', { status: 405 })
  }

  try {
    const { code, client_id } = await request.json()

    // IMPORTANT: Replace this with your actual Client Secret from GitHub
    const client_secret = 'YOUR_GITHUB_CLIENT_SECRET'

    const response = await fetch('https://github.com/login/oauth/access_token', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Accept': 'application/json',
      },
      body: JSON.stringify({
        client_id,
        client_secret,
        code
      })
    })

    const result = await response.json()

    return new Response(JSON.stringify(result), {
      headers: {
        'Content-Type': 'application/json',
        'Access-Control-Allow-Origin': '*',
      }
    })
  } catch (error) {
    return new Response(JSON.stringify({ error: error.message }), {
      status: 500,
      headers: {
        'Content-Type': 'application/json',
        'Access-Control-Allow-Origin': '*',
      }
    })
  }
}
```

6.  **Crucial Step:** Replace `'YOUR_GITHUB_CLIENT_SECRET'` in the code with the secret you generated in Step 1.
7.  Click **Save and Deploy**.
8.  Copy the URL of your worker (e.g., `https://gh-oauth-proxy.your-subdomain.workers.dev`).

## 3. Configure the Dashboard

1.  Open your `index.html` dashboard in the browser.
2.  Click the **Settings icon** (cog) in the top right.
3.  Enter your **GitHub Username** and **Repository Name**.
4.  Enter the **GitHub OAuth Client ID** from Step 1.
5.  Enter the **Worker Proxy URL** from Step 2.
6.  Click **Save Config**.

## 4. Usage

1.  Click the **Login** button in the header.
2.  Authorize the app on GitHub.
3.  Once redirected back, you should see your GitHub profile picture and name.
4.  If you have push access to the repository, the **New Page**, **Edit**, and **Delete** buttons will appear.
