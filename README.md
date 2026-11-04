# PCOS Tracker 🌸

**A Polycystic Ovary Syndrome (PCOS) health management app for Android**

PCOS Tracker brings period tracking, meal tracking, workout tracking and health reports into one app, so women living with PCOS no longer need several unrelated apps to understand their condition. The project supports **UN Sustainable Development Goal 3: Good Health and Well-being**.

Built as the group mini project for **IT31043 – Mobile Application Development** (Group 05), BSc (Hons) Information Technology, Horizon Campus, Academic Year 2025/2026.

![Kotlin](https://img.shields.io/badge/Kotlin-Android-7F52FF?logo=kotlin&logoColor=white)
![Firebase](https://img.shields.io/badge/Firebase-Auth%20%7C%20Firestore-FFCA28?logo=firebase&logoColor=black)
![Min SDK](https://img.shields.io/badge/minSdk-28-green)

---

## 🔗 Project Links

| Resource | Link |
|---|---|
| 🎨 **Figma Prototype** | [View the interactive prototype](https://www.figma.com/proto/VGDCrvrkN8t0AcnIZeKjVt/PCOS-Tracker?node-id=90-4275&starting-point-node-id=90%3A4275&show-proto-sidebar=1&t=V5aQYcnyCktFcAhC-1) |
| 📦 **APK File** | [Download from Google Drive](https://drive.google.com/drive/folders/1hWesLDe2mF_nBRrC_xRRvhxwgpk16bbe?usp=sharing) |
| 🎥 **Demo Video** | [Watch on Google Drive](https://drive.google.com/drive/folders/1vFheqgqvsqCZLoP59NnT7jPuzTiXiUFM?usp=sharing) |
| 💻 **GitHub Repository** | [savindirathwella333-create/PCOSTracker](https://github.com/savindirathwella333-create/PCOSTracker) |

---

## 🩺 Problem and Solution

PCOS affects an estimated 8 to 13% of women of reproductive age, and many cases go undiagnosed. Existing cycle and fitness apps are general purpose and do not connect PCOS symptoms with diet and lifestyle. Patients end up juggling several apps and struggle to give doctors a clear picture of their health.

**PCOS Tracker** puts the daily tracking a PCOS patient needs in one place and turns it into simple reports.

## ✨ Features

| Module | What it does |
|---|---|
| **Authentication** | Sign up, log in and log out with Firebase Authentication |
| **Profile** | Edit name, email and date of birth, upload a profile photo, show the profile on the home screen |
| **Home dashboard** | Summary cards, quick actions, recent activity and bottom navigation |
| **Period tracking** | Log start and end dates, mood, pain level and notes, with history, prediction and cycle insight |
| **Calendar** | Monthly overview with period, meal and workout markers and a legend |
| **Meal tracking** | Daily meal logging, weekly and monthly grid, meal gallery and PCOS food guide |
| **Workout** | Exercise, yoga and meditation routines with completion tracking |
| **Reports** | Period, meal, workout and combined monthly reports |
| **Settings and privacy** | Privacy policy, delete history, logout |

## 🛠 Tech Stack

- **Language:** Kotlin
- **IDE:** Android Studio
- **Backend:** Firebase Authentication, Cloud Firestore
- **Local storage:** SharedPreferences (JSON)
- **UI:** XML layouts, Material Components, RecyclerView, ConstraintLayout, CircleImageView
- **Other libraries:** Kizitonwose Calendar View, AndroidX Core Splashscreen
- **Build:** Gradle (Kotlin DSL), `minSdk 28`, `targetSdk 34`, `compileSdk 36`

---

## 👩‍💻 My Contribution

**E.N. Sandeepani Wijerathne (ITBIN-2313-0128)** owned the **account, profile, privacy and settings** side of the app.

### Screens delivered
1. **Login Screen**
2. **Personal Information** (edit profile and upload photo)
3. **Privacy Policy**
4. **Settings**

### What I built

**🔐 Authentication** (`LoginActivity.kt`, `SignUpActivity.kt`)
- Implemented email and password login and registration with **Firebase Authentication**.
- Added input validation for required fields, email format and a minimum password length of 6 characters, with inline error messages.
- Redirects users to the home dashboard after successful authentication.

**👤 Profile management** (`PersonalInfoActivity.kt`, `ProfileStore.kt`)
- Built the profile editor for name, email and date of birth.
- Added profile photo selection with Android's document picker (`ActivityResultContracts.OpenDocument`), using persistable URI permissions so the image is still available after the app restarts.
- Saved the profile locally with `SharedPreferences` and synced it to **Cloud Firestore** (`users/{uid}`, merge write). Existing Firestore values are loaded back into the form.
- Connected the saved profile to the home dashboard, which shows a personalised greeting and the profile image.

**🔒 Privacy Policy** (`PrivacyPolicyActivity.kt`, `item_privacy_policy_row.xml`)
- Designed the privacy screen covering data collection, consent, protection and user control.
- Built it from a reusable row layout and a binding function, so policy sections are easy to add or update.

**⚙️ Settings** (`settingsActivity.kt`)
- Loads the signed-in user's profile from Firestore.
- Built the **Delete History** action with a confirmation dialog, which removes the user's check-in records from Firestore in a single batch.
- Implemented **secure logout** with Firebase `signOut()` that clears the activity back stack so users cannot navigate back into the app.

### Skills demonstrated
Kotlin · Android Activities and Intents · Firebase Authentication · Cloud Firestore · SharedPreferences · Activity Result API · Form validation · Reusable UI components · Privacy-aware design

---

## 📱 Screenshots

| Login | Personal Information | Privacy Policy | Settings |
|:---:|:---:|:---:|:---:|
| ![Login](screenshots/login.png) | ![Profile](screenshots/personal_info.png) | ![Privacy](screenshots/privacy_policy.png) | ![Settings](screenshots/settings.png) |

## 📂 Project Structure

```
PCOSTracker/
├── app/
│   ├── google-services.json
│   ├── build.gradle.kts
│   └── src/main/
│       ├── AndroidManifest.xml
│       ├── java/com/example/pcostracker/   # Kotlin activities, adapters and stores
│       └── res/
│           ├── layout/                     # XML screens
│           ├── drawable/                   # Icons and images
│           └── menu/bottom_nav_menu.xml    # Bottom navigation
├── build.gradle.kts
└── settings.gradle.kts
```

Files from my modules: `LoginActivity.kt`, `SignUpActivity.kt`, `PersonalInfoActivity.kt`, `ProfileStore.kt`, `PrivacyPolicyActivity.kt`, `settingsActivity.kt`.

## 🚀 Getting Started

**Requirements:** Android Studio (recent version), JDK 11 or higher, and an Android emulator or device running Android 9 (API 28) or above.

```bash
# 1. Clone the repository
git clone https://github.com/savindirathwella333-create/PCOSTracker.git

# 2. Open the project in Android Studio and let Gradle sync

# 3. Run on an emulator or a connected device
```

Or skip the build and install the app directly from the [APK folder](https://drive.google.com/drive/folders/1hWesLDe2mF_nBRrC_xRRvhxwgpk16bbe?usp=sharing).

**Firebase setup:** the app uses Firebase Authentication and Cloud Firestore. To run it against your own Firebase project, create one, enable **Email/Password** sign-in and **Firestore**, then replace `app/google-services.json` with your own file.

## ✅ Testing

Manual test cases were documented for each module in the project report. Test cases for my modules:

| ID | Feature | Steps | Expected result | Status |
|---|---|---|---|---|
| TC22 | Login | Enter email and password, tap Login | User opens the Home screen | Pending emulator run |
| TC23 | Profile save | Settings → Edit Profile, enter name, select image, save | Name and image saved and shown on Home | Pass |
| TC24 | Privacy policy | Settings → Privacy Policy | Privacy Policy screen opens | Pass |

## 👥 Team

**Group 05, IT31043 Mobile Application Development**

| Student ID | Name |
|---|---|
| ITBIN-2313-0092 | Savindi Rathwella |
| ITBIN-2313-0088 | Malki Rajapaksha |
| ITBIN-2313-0105 | D. Bhagya Senarathna |
| ITBIN-2313-0128 | E.N. Sandeepani Wijerathne |

## 🔭 Limitations and Future Work

All data is currently entered manually. Planned improvements:

- Notifications and reminders
- AI based health suggestions
- Graphical trend analysis
- Cloud backup and sync for all tracked data
- Language selection support
- Wearable and fitness tracker integration
- Online consultations with doctors

## 📄 License

Developed for academic purposes at Horizon Campus.
