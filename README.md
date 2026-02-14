# PagesHub Setup Guide

This project allows you to manage standalone HTML pages in a GitHub repository using a browser-based dashboard. It uses GitHub's **Device Authorization Flow** for secure authentication without requiring a backend server or worker proxy.

## 1. Create a GitHub OAuth App

To enable "Login with GitHub", you need to create an OAuth App on GitHub:

1.  Go to **GitHub Settings** > **Developer settings** > **OAuth Apps**.
2.  Click **New OAuth App**.
3.  **Application Name:** PagesHub (or anything you like).
4.  **Homepage URL:** `https://dusolcev.github.io/Web-Pages/`
5.  **Authorization callback URL:** `https://dusolcev.github.io/Web-Pages/index.html` (Note: This is not strictly used by the Device Flow, but required by GitHub).
6.  Click **Register application**.
7.  Copy the **Client ID**.
8.  **Crucial Step:** Scroll down to the **"Device flow"** section and click **"Enable Device Flow"**.

## 2. Configure the Dashboard

1.  Open your `index.html` dashboard in the browser.
2.  Click the **Settings icon** (cog) in the top right.
3.  Enter your **GitHub Username** and **Repository Name**.
4.  Enter the **GitHub OAuth Client ID** you copied in Step 1.
5.  Click **Save Config**.

## 3. Usage

1.  Click the **Login** button in the header.
2.  A modal will appear showing a **Device Code** (e.g., `ABCD-1234`).
3.  Click the **Open GitHub Login** button.
4.  Enter the code on GitHub's page and authorize the app.
5.  The dashboard will automatically detect when you've finished and log you in.
6.  If you have push access to the repository, the management buttons (**New Page**, **Edit**, **Delete**) will appear.
