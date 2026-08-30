# Firebase Authentication Setup — Easy Appliance Hub

This site uses **Firebase Authentication** for:

- Email / Password
- Google
- Facebook
- Apple

Follow these steps once to make login work on a real domain.

---

## 1. Create a Firebase project

1. Go to [https://console.firebase.google.com](https://console.firebase.google.com)
2. Click **Add project** → name it (e.g. `easy-appliance-hub`)
3. Disable Google Analytics if you don’t need it → **Create project**

---

## 2. Register a Web app

1. In the project overview, click the **Web** icon (`</>`)
2. App nickname: `Easy Appliance Hub`
3. Do **not** check Firebase Hosting unless you want it
4. Click **Register app**
5. Copy the `firebaseConfig` object (apiKey, authDomain, projectId, etc.)

---

## 3. Paste config into the site

Open `index.html` and find this block near the bottom:

```js
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_PROJECT_ID.appspot.com",
  messagingSenderId: "YOUR_SENDER_ID",
  appId: "YOUR_APP_ID"
};
```

Replace every `YOUR_…` value with the values from the Firebase console.

---

## 4. Enable sign-in methods

In Firebase Console → **Build** → **Authentication** → **Sign-in method**:

| Method          | What to do |
|-----------------|------------|
| **Email/Password** | Enable → Save |
| **Google**         | Enable → set support email → Save |
| **Facebook**       | Enable → enter Facebook App ID + App Secret (from [developers.facebook.com](https://developers.facebook.com)) → Save. Also add the OAuth redirect URI shown in Firebase to your Facebook app. |
| **Apple**          | Enable → follow Apple Developer setup (Services ID, Key, Team ID). Required for production Apple Sign In. |

---

## 5. Authorize your domain

Authentication → **Settings** → **Authorized domains**

Add:

- `localhost` (already there for local testing)
- Your live domain, e.g. `easyappliancehub.com`
- If using Firebase Hosting: `your-project.web.app` and `your-project.firebaseapp.com`

Without this you will see `auth/unauthorized-domain`.

---

## 6. Test locally

Because the site is static HTML:

```bash
# From the folder that contains index.html
npx serve .
# or
python -m http.server 5500
```

Open `http://localhost:5500` (or the port shown).

1. Click **Account**
2. Sign up with email/password
3. Try **Continue with Google** (popup)
4. Confirm the nav button shows your name
5. Log out and log back in

---

## Troubleshooting

| Error | Fix |
|-------|-----|
| `auth/unauthorized-domain` | Add the current domain under Authorized domains |
| `auth/operation-not-allowed` | Enable that provider under Sign-in method |
| `auth/popup-blocked` | Allow popups; site falls back to redirect automatically |
| `auth/invalid-api-key` | Double-check `apiKey` in `firebaseConfig` |
| Facebook fails | App must be Live (or you must be a tester), and OAuth redirect URI must match Firebase |
| Apple fails | Apple Sign In needs a paid Apple Developer account and correct Services ID / Key |

---

## Security notes

- The Firebase **web API key is public by design**. Protect data with Firebase Security Rules, not by hiding the key.
- Restrict the key in Google Cloud Console (Application restrictions → HTTP referrers) to your domains if you want extra safety.
- Never put **service account** private keys in frontend code.

---

## Optional: Firebase Hosting

```bash
npm install -g firebase-tools
firebase login
firebase init hosting   # public directory = folder with index.html
firebase deploy
```

Your site will be live at `https://YOUR_PROJECT_ID.web.app`.

---

After config is pasted and providers are enabled, Google / Facebook / Apple / email login will work for real users.
