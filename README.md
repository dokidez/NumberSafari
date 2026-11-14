# Number Safari

An Android counting game for kindergarten children (about ages 4–6). Kids count fruits, stars and animals on screen, choose the right number, and earn stars and safari ranks as they play. Each child can have their own account on the same phone.

> **Final Project: Smart Community Solutions – Android App Development**
> Team: Destacamento & Daanoy, BSIT 3 OTP

---

## The problem it solves

Young children learn to count best through play, but many learning apps are hard to follow for pre-readers, need constant internet, or are not built for several children sharing one device. Number Safari uses large pictures and big buttons, reads questions aloud, works offline, and keeps a separate progress record for every child.

**Target users:** kindergarten pupils, their parents and their teachers.

---

## Features

**Accounts**
- Welcome page with separate **Log in** and **Create account** screens, plus **Play as guest**
- Several accounts on one device, each with its own name, avatar, stars and history
- Choose an explorer avatar when registering
- Show/hide password, forgot-password email, and clear error messages
- A guest can upgrade to a real account and keep the stars already earned
- Sign out with a confirmation (extra warning for guests)

**Game**
- Three categories: **Fruits**, **Stars**, **Animals**
- Three levels: **Easy (1–5)**, **Medium (1–10)**, **Hard (1–20)**
- 10 questions per round, four answer choices each
- A star is earned only for a correct answer on the first try, without using a hint
- Progress bar, score chip, sound effects, vibration feedback and answer animations

**Sensors and helpers**
- **Accelerometer** – shake the phone for a hint (removes two wrong answers)
- **Microphone** – say the number out loud (uses the phone's speech recognizer)
- **Text-to-speech** – the question is read aloud, with a Listen button
- A Hint chip is also provided as a tap alternative to shaking

**Progress**
- Home dashboard with greeting, avatar, rank and quick-play tiles
- Safari ranks: Safari Cub (0 stars), Explorer (20), Ranger (50), Safari Hero (100), Safari Legend (200)
- My Progress screen with total stars, rounds played, best score and a history of every round
- Profile screen with rank progress and settings (sound effects, read aloud)

---

## Technology

| Area | Used |
|---|---|
| IDE / language | Android Studio, Java |
| Min / target SDK | 24 / 37 |
| Local database | SQLite (`SQLiteOpenHelper`) |
| Cloud | Firebase Authentication (email/password and anonymous), Cloud Firestore |
| Device features | Accelerometer, microphone (speech recognizer), text-to-speech, haptics, tone generator |
| UI | XML layouts, AppCompat, custom drawables, edge-to-edge screens |

### How the data works

- **SQLite** is always written first, so the game works with no internet.
  - `questions` – 60 seeded questions (3 categories × numbers 1–20)
  - `results` – one row per finished round (user, category, score, total, time, synced flag)
- **Firestore** receives each round when the phone is online: `users/{uid}/results/{playedAt}`. Rounds saved offline upload the next time the Home screen opens.
- Each result is tied to the signed-in user's ID, which is how several accounts share one phone without mixing progress.

---

## Project structure

```
app/src/main/java/com/example/numbersafari/
├── data/    DatabaseHelper, GameRepository        (SQLite)
├── model/   Question, GameResult
├── ui/      Splash, Welcome, Login, Register, Main (home), Choose,
│            Game, Result, Progress, Profile        (screens)
└── util/    InsetsUtil, ShakeDetector, SoundFx, SyncManager, UserSession,
             Prefs, Ranks, Stats, NumberWords, AuthErrors, UiUtil
app/src/main/res/   layouts, drawables, strings, colors, launcher icons
```

---

## Install the app (APK)

1. Download `app-debug.apk` from the **Releases** page of this repository.
2. Copy it to an Android phone (version 7.0 or newer).
3. Open the file and allow **Install unknown apps** when asked.
4. If Play Protect shows a warning, choose **Install anyway**. This is normal for apps that are not from the Play Store.
5. If an older build of Number Safari is installed, uninstall it first.

---

## Run from source

1. Clone the repository and open it in Android Studio.
2. Create a Firebase project at https://console.firebase.google.com and register an Android app with the package name `com.example.numbersafari`.
3. Download `google-services.json` and place it in the `app/` folder (next to `app/build.gradle.kts`).
   *This file is not included in the public repository.*
4. In Firebase, enable **Authentication → Sign-in method → Email/Password** and **Anonymous**.
5. Create a **Firestore Database** and publish these security rules:
   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /users/{userId}/results/{resultId} {
         allow read, write: if request.auth != null && request.auth.uid == userId;
       }
     }
   }
   ```
6. Sync Gradle, then run the app on a phone or emulator.

The SQLite database needs no setup. It is created and filled with questions on first launch.

---

## How we tested

| Area | What we checked |
|---|---|
| Accounts | Create account, log in, wrong password, forgot password, guest, guest upgrade, sign out, switching between two accounts |
| Game | All three categories and levels, right and wrong answers, hint, a full 10-question round, result screen |
| Data | Rounds appear in My Progress; each account sees only its own rounds; rounds appear in Firestore |
| Offline | Played in airplane mode, then reconnected and confirmed the rounds uploaded |
| Sensors | Shake hint, voice answer, read aloud, sound on/off |
| Device | Installed the APK on a physical Android phone |

---

## Known limitations

- Voice answers and read-aloud need Google's speech services on the phone. If they are missing, the app shows a message and the buttons still work.
- Signing out of a **guest** session means that guest's stars can no longer be reached. Creating an account first keeps them.
- The app is portrait-only and the questions are in English.
- The APK is a debug build, signed with the default Android debug key.

---

## Team and roles

| Member | Responsibilities |
|---|---|
| Destacamento | *(edit: for example UI design, layouts and testing)* |
| Daanoy | *(edit: for example database, Firebase and coding)* |

---

## Screenshots

*(Add your images to a `screenshots/` folder and link them here.)*

| Welcome | Home | Game |
|---|---|---|
| ![Welcome](screenshots/welcome.png) | ![Home](screenshots/home.png) | ![Game](screenshots/game.png) |

| Result | My Progress | Profile |
|---|---|---|
| ![Result](screenshots/result.png) | ![Progress](screenshots/progress.png) | ![Profile](screenshots/profile.png) |

---

## Privacy

Number Safari has no ads and no tracking. It stores a player name, an email address (for account sign-in) and game scores. Sign-in and cloud sync are handled by Firebase. The avatar choice and settings are stored only on the phone.
