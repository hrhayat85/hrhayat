# HR.HAYAT — Dynamic Portfolio & Firebase Admin Panel

A complete, full-stack, dynamic personal portfolio website and private Admin Dashboard crafted for **HR.HAYAT** using **pure HTML5, CSS3, Vanilla JavaScript (ES Modules), and Firebase (Modular SDK v10+)**.

The visual design strictly preserves the iconic **Carrd Neo-Brutalist / Retro-Pop** aesthetic:
- **Bright Pink outer background** (`#FF83C8`) with crisp diagonal decorative stripes.
- **Large centered white content panel** (`#FFFFFF`) with thin black borders (`2.5px solid #000000`).
- **Black offset box shadows** (`4px 4px 0px #000000` to `6px 6px 0px #000000`).
- **Circular profile avatar** with a thick black border and live status indicator.
- **Colorful rectangular action buttons** (Pink, Blue, Orange).
- **Interactive Image Lightbox** with animations and keyboard navigation.
- **Zero hardcoded demo content**: all public content dynamically loads from Firebase Firestore and Storage, with clean empty states when no content has been uploaded.

---

## 📁 Project Architecture & File Structure

```text
HR-HAYAT-PORTFOLIO/
│
├── index.html            # Public dynamic portfolio website
├── login.html            # Secure Admin Login portal
├── admin.html            # Private Admin Dashboard (Protected route)
├── robots.txt            # Search engine crawler instructions
├── sitemap.xml           # XML sitemap for SEO
│
├── css/
│   ├── style.css         # Public portfolio Neo-Brutalist stylesheet
│   ├── admin.css         # Admin dashboard responsive stylesheet
│   └── login.css         # Admin login card stylesheet
│
├── js/
│   ├── firebase-config.js # Centralized Firebase Modular SDK initialization & Admin UID config
│   ├── main.js           # Public site controller: Firestore real-time listeners & UI sync
│   ├── auth.js           # Admin authentication, session persistence & route guards
│   ├── admin.js          # Admin dashboard UI coordinator & tab management
│   ├── profile.js        # Profile, About Me, Social Links & Settings CRUD
│   ├── gallery.js        # Gallery image upload with progress bar, Storage & Firestore sync
│   ├── projects.js       # Projects showcase CRUD with thumbnail upload
│   └── skills.js         # Skills collection CRUD and sorting
│
├── firebase/
│   ├── firestore.rules   # Cloud Firestore security rules (Admin UID allowlist)
│   └── storage.rules     # Firebase Storage security rules (MIME & size validation)
│
└── README.md             # Complete setup and deployment documentation
```

---

## 🛠️ Step-by-Step Firebase Setup Guide

Follow these steps to connect your portfolio website to your own Firebase project:

### Step 1: Create a Firebase Project
1. Navigate to the [Firebase Console](https://console.firebase.google.com/).
2. Click **Add project** (or **Create a project**).
3. Name your project (e.g., `hrhayat-portfolio`).
4. Google Analytics is optional; complete the setup wizard.

### Step 2: Register a Web App & Copy Configuration
1. In your Firebase project overview, click the **Web icon (`</>`)** to add a web app.
2. Enter an app nickname (e.g., `HR.HAYAT Web`).
3. You will receive a configuration object resembling this:
   ```javascript
   const firebaseConfig = {
     apiKey: "AIzaSy...",
     authDomain: "hrhayat-portfolio.firebaseapp.com",
     projectId: "hrhayat-portfolio",
     storageBucket: "hrhayat-portfolio.appspot.com",
     messagingSenderId: "1234567890",
     appId: "1:1234567890:web:abcdef..."
   };
   ```

### Step 3: Insert Your Configuration in `js/firebase-config.js`
1. Open [`js/firebase-config.js`](file:///e:/my%20ai/js/firebase-config.js).
2. Replace the placeholder values in `FIREBASE_CONFIG` with your real keys:
   ```javascript
   export const FIREBASE_CONFIG = {
     apiKey: "YOUR_ACTUAL_API_KEY",
     authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
     projectId: "YOUR_PROJECT_ID",
     storageBucket: "YOUR_PROJECT_ID.appspot.com",
     messagingSenderId: "YOUR_SENDER_ID",
     appId: "YOUR_APP_ID"
   };
   ```

### Step 4: Enable Firebase Authentication
1. In the Firebase Console sidebar, select **Build > Authentication**.
2. Click **Get Started**.
3. Under the **Sign-in method** tab, choose **Email/Password**.
4. Enable **Email/Password** and click **Save**.
5. Switch to the **Users** tab and click **Add user**.
6. Enter your personal admin email and a strong password.
7. Click **Add user**.
8. In the table, copy your new user's **User UID** (a 28-character unique identifier).

### Step 5: Configure Admin UID Authorization
To ensure that only your authorized account can modify portfolio content:
1. Open [`js/firebase-config.js`](file:///e:/my%20ai/js/firebase-config.js).
2. Paste your UID into `ADMIN_UIDS`:
   ```javascript
   export const ADMIN_UIDS = [
     'PASTE_YOUR_COPIED_ADMIN_UID_HERE'
   ];
   ```
3. Open [`firebase/firestore.rules`](file:///e:/my%20ai/firebase/firestore.rules) and [`firebase/storage.rules`](file:///e:/my%20ai/firebase/storage.rules).
4. Update the UID in the `isAdmin()` function:
   ```javascript
   function isAdmin() {
     return request.auth != null && (
       request.auth.uid in [
         "PASTE_YOUR_COPIED_ADMIN_UID_HERE"
       ]
     );
   }
   ```

### Step 6: Enable Cloud Firestore & Deploy Rules
1. In the Firebase Console, go to **Build > Firestore Database**.
2. Click **Create database**.
3. Choose your database location (e.g., `asia-southeast1` or `us-central1`).
4. Select **Start in production mode** and finish setup.
5. In the Firestore **Rules** tab, paste the complete contents of [`firebase/firestore.rules`](file:///e:/my%20ai/firebase/firestore.rules) and click **Publish**.

### Step 7: Enable Firebase Storage & Deploy Rules
1. In the Firebase Console, go to **Build > Storage**.
2. Click **Get Started**.
3. Choose your Storage location and click **Done**.
4. In the Storage **Rules** tab, paste the complete contents of [`firebase/storage.rules`](file:///e:/my%20ai/firebase/storage.rules) and click **Publish**.

---

## 🔒 Security Architecture

1. **Public Read-Only Access**:
   Visitors to your public portfolio can read published data from the Firestore collections (`profile`, `skills`, `gallery`, `projects`, `socialLinks`, `settings`).
2. **Server-Enforced Authorization**:
   Any create, update, or delete operation is validated at the Firebase database level via `request.auth.uid in [...]`. Unauthorized visitors cannot write or tamper with data even if they inspect frontend network calls.
3. **Storage Protection**:
   File uploads are restricted to authentic admin accounts, limited to 5MB per file, and validated against standard image MIME types (`image/*`).
4. **No Service Account Private Keys**:
   The web application uses client-safe public API keys. No private service account keys are ever exposed in frontend files.

---

## ⚡ Using the Admin Dashboard

1. Open `/login.html` (or click the **⚡ Admin** link in the footer of `index.html`).
2. Enter your Admin Email and Password.
3. Upon successful authentication, you will be redirected to `/admin.html`.

### Available Dashboard Features:
- **Dashboard Overview**:
  - View real-time counters for Total Projects, Gallery Photos, Skills Listed, and Active Social Links.
  - **🌱 Seed Initial HR.HAYAT Profile**: Click this button once on a fresh database to automatically initialize default profile data, about paragraphs, contact info, and social links in Firestore.
- **Profile Management**:
  - Upload a circular profile avatar (stored in Firebase Storage).
  - Update Brand Title (`HR.HAYAT`), Tagline, Location, Hero Introduction, and Direct Contact Buttons.
- **About Me Management**:
  - Edit section title, summary, and enter multiple structured paragraphs (separated by empty lines).
- **Skills Management**:
  - Add skills with custom icons or emojis (e.g., ⚡, 💻, 🎨) and custom sort orders.
  - Edit or delete skills dynamically.
- **Gallery Manager**:
  - Drag and drop or multi-select images to upload with a live progress bar.
  - Enter photo titles and category tags.
  - Permanently delete photos (automatically removes both the Storage file and the Firestore document).
- **Project Management**:
  - Add projects with custom thumbnails, descriptions, live demo links, and GitHub links.
  - Edit or delete existing projects.
- **Social Media Management**:
  - Toggle and customize URLs for Instagram, Facebook, TikTok, X (Twitter), Pinterest, LinkedIn, YouTube, GitHub, and Email.
  - Empty or disabled platforms will not appear on the public website.
- **Settings & SEO**:
  - Customize website title, SEO meta descriptions, favicon, and copyright footer text.

---

## 🚀 Deployment Instructions

### Option 1: Firebase Hosting (Recommended)
1. Install Firebase CLI (if not already installed):
   ```bash
   npm install -g firebase-tools
   ```
2. Log in to your Firebase account:
   ```bash
   firebase login
   ```
3. Initialize hosting in this project directory:
   ```bash
   firebase init hosting
   ```
   - Select your existing Firebase project.
   - Set public directory to `.` (the current directory).
   - Configure as single-page app: **No**.
   - Overwrite `index.html`: **No**.
4. Deploy your website, Firestore rules, and Storage rules:
   ```bash
   firebase deploy
   ```

### Option 2: GitHub Pages
1. Push this repository to GitHub.
2. In your repository settings, go to **Pages**.
3. Under **Build and deployment > Branch**, select `main` and `/ (root)`.
4. Click **Save**. Your site will be live at `https://<username>.github.io/<repo>/`.
5. In the Firebase Console under **Authentication > Settings > Authorized domains**, add your GitHub Pages domain (`<username>.github.io`).

---

## 💡 Troubleshooting & FAQs

- **"Setup Required" alert on login page:**
  This indicates that `js/firebase-config.js` still contains placeholder strings like `YOUR_API_KEY_HERE`. Update it with your Firebase project keys.
- **"Account is not authorized as an administrator":**
  Ensure that the UID in the Firebase Authentication user table matches the UID pasted into `ADMIN_UIDS` in `js/firebase-config.js`.
- **Public gallery displays "No photos uploaded yet.":**
  This is the intended clean empty state. Once you upload photos through the Admin Panel (`/admin.html`), they will immediately appear on the public portfolio.

---

© 2026 HR.HAYAT. Built with HTML5, CSS3, Vanilla JS & Firebase.
