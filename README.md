# The Forty Hadith · شرح الأربعون حديثاً للناشئة

A mobile-friendly, gamified reading journey for young readers, built from the picture stories in *شرح الأربعون حديثاً للناشئة*.

**Play:** https://ahmad2b2-lang.github.io/The-Forty-Hadith/

## What's inside
- **Home:** the book cover, a button to start the journey, and a sign-in button.
- **Map (رحلة الأحاديث):** one level per story. Level 1 «جهاد النفس» is ready; levels 2–10 are marked «قريباً».
- **Reading:** pages sized to the reader's screen (no scrolling), the original illustrations, next/back buttons (or swipe), and tappable vocabulary words to collect.
- **Challenge:** questions at the end of each story; a correct first answer earns a star.
- **Settings:** text size, day/night mode, Noto or Arial font, show/hide tashkeel, and the account.
- **Accounts (optional):** players can register with a name, email and password (no email confirmation) and sign in on any device. Their progress, stars, collected words and settings are saved to Firestore.

Everything is in one file, `index.html`. Without accounts it also works offline when saved to a phone.

## Accounts and saving: how it works
- **Sign-in:** Firebase Authentication, email and password.
- **Saving:** Cloud Firestore, one document per player at `players/{uid}`:

| Field | What it holds |
|---|---|
| `displayName` | the name the player typed when registering |
| `totalStars`, `totalWords` | totals across all levels |
| `levels.1` | Level 1: `stars`, `rating` (0–3), `words`, `completed`, and the detail needed to resume (`ans`, `tries`, `found`, `at`) |
| `prefs` | text size, font, day/night, tashkeel |
| `createdAt`, `updatedAt` | server timestamps |

- Playing without an account still works; progress stays on that phone. When a guest registers or signs in, their progress on that phone is combined with the account's progress, so nothing earned is lost.
- Signing out clears the progress from that phone (it stays in the account), so the next child on a shared phone starts fresh.
- `firestore.rules` lets each signed-in player read and write only their own document.

## One-time Firebase setup
1. **Create the project.** Go to https://console.firebase.google.com, choose **Add project**, and follow the steps (Google Analytics is optional).
2. **Add a web app.** In **Project settings → General → Your apps**, choose the web icon `</>`, give it a name, and register it. Copy the `firebaseConfig` object it shows.
3. **Turn on email sign-in.** In **Build → Authentication → Get started → Sign-in method**, enable **Email/Password**. Leave "Email link" off.
4. **Create the database.** In **Build → Firestore Database → Create database**, pick a location near your players and start in **production mode**.
5. **Publish the rules.** In **Firestore Database → Rules**, replace everything with the contents of `firestore.rules` and choose **Publish**.
6. **Allow the GitHub Pages address.** In **Authentication → Settings → Authorized domains**, add `ahmad2b2-lang.github.io`.
7. **Connect the game.** In `index.html`, find `const FIREBASE_CONFIG=null;` and replace `null` with the `firebaseConfig` object from step 2. The sign-in button appears once this is set.

The `firebaseConfig` values (including `apiKey`) are meant to be public in a web app; the security rules are what protect players' data.
