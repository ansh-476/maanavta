# Maanavta Hitaay Seva Chatra — website + owner records

- The website is hosted on **GitHub Pages** (free).
- Resident records are stored in a free **Firebase** database (Google), so they are
  permanent and show up on every device where the owner signs in.
- The owner signs in with an **ID + passcode**. Nothing else is ever asked for.

```
index.html   the whole site (public pages + owner workspace)
images/      photos used on the site
.nojekyll    tells GitHub Pages to serve the files as-is
```

## One-time setup (about 15 minutes)

### 1. Create a Firebase project
1. Go to https://console.firebase.google.com and sign in with a Google account.
2. **Create a project** → name it `maanavta-records` → you can turn Google Analytics off → Create.

### 2. Get the config and paste it into the page
1. In the project, click the **web icon `</>`** (Add app) → nickname `website` → Register app.
2. Firebase shows a `firebaseConfig` block. Copy the three values **apiKey**, **authDomain**, **projectId**.
3. Open `index.html` in any text editor, find the **OWNER SETTINGS** block right after
   `<script>` near the bottom, and paste the three values in place of the `PASTE_…` text.

### 3. Create the owner login
1. Left menu → **Build → Authentication** → Get started → **Email/Password** → Enable → Save.
2. **Users** tab → **Add user**:
   - Email: `owner@maanavta.org` (this is Owner ID `owner`; the page adds `@maanavta.org` automatically)
   - Password: choose a strong passcode (8+ characters). This is what you type on the site.
3. Click **Add user**.

To use a different ID, create the user as `yourid@maanavta.org` and sign in with `yourid`.

### 4. Create the database and lock it to the owner
1. Left menu → **Build → Firestore Database** → Create database → choose a location
   near you (e.g. `asia-south1`, Mumbai) → **Start in production mode** → Create.
2. **Rules** tab → replace everything with the following, then **Publish**:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /records/{doc} {
      allow read, write: if request.auth != null
        && request.auth.token.email == 'owner@maanavta.org';
    }
  }
}
```
(If you used a different owner email in step 3, put that email here.)

### 5. Allow your website address
Authentication → **Settings** → **Authorized domains** → Add domain →
`your-username.github.io` (replace with your GitHub username).

### 6. Publish the website on GitHub
1. GitHub → **New repository** → e.g. `maanavta-site` → Public → Create.
2. Upload `index.html` (the edited one), `.nojekyll` and the `images/` folder.
3. Repository → **Settings → Pages** → Source: *Deploy from a branch* → `main` / `/ (root)` → Save.
4. The site is live at `https://your-username.github.io/maanavta-site/` after a minute.

## Everyday use
- Open the site → **Owner records** → Owner ID `owner` + passcode → add residents.
- Every change is saved within a second; the badge in the sidebar shows *Saved*.
- You stay signed in on that device until you press **Sign out**. Do sign out on shared computers.
- Opening the site on another device and signing in shows the same records.
- **Account & storage** lets you change the passcode. **Backup & export** has JSON/CSV
  downloads and restore.
- Forgot the passcode? Firebase console → Authentication → Users → ⋮ → Reset password,
  or delete the user and add it again with a new password.

## Notes
- Firebase's free plan is far more than a home of this size will ever use. No card is needed.
- The Firebase config values in `index.html` are not secrets; the rules in step 4 are
  what protect the records. Do not skip that step.
- Replacing the photos: put your own photos in `images/` with the same file names, and
  remove the "representative image" wording and the credits line in `index.html`.
