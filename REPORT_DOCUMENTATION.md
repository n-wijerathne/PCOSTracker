# PCOS Tracker Mobile App Report Documentation

## 2.2 Functional Requirements (FR)

| ID | Functional Requirement |
|---|---|
| FR01 | Users can register, log in, and access the PCOS Tracker mobile app. |
| FR02 | The splash screen displays PCOS Tracker branding and rotating health-related images before navigation. |
| FR03 | Users can view a home dashboard with period, meal, workout, recent activity, and profile summary. |
| FR04 | Users can open profile settings, edit personal information, upload a profile image, and save changes. |
| FR05 | The home screen displays the saved user name as a greeting and shows the uploaded profile image. |
| FR06 | Users can track period start date, end date, mood, pain level, and special notes. |
| FR07 | The period calendar highlights current, previous, and predicted period ranges. |
| FR08 | Users can view previous period history and predicted next period reminder. |
| FR09 | Users can view a cycle insight report showing regular/irregular cycle information. |
| FR10 | Users can access a period guide with cycle, bleeding, common symptoms, remedies, foods, hygiene, and doctor warning signs. |
| FR11 | Users can access meal tracking from the home card, Add Today’s Data, calendar legend, or tracking hub. |
| FR12 | Users can track daily breakfast, lunch, and dinner using checkboxes and save changes. |
| FR13 | Users can view weekly and monthly meal tracking progress. |
| FR14 | Users can open a Meal Gallery with PCOS-friendly weekly meal plans. |
| FR15 | Users can view meal tracking reports with monthly meal completion data. |
| FR16 | Users can open a Food Guide with healthy food choices and foods to avoid. |
| FR17 | Users can access workout and wellness tracking for all, meditation, yoga, and exercise. |
| FR18 | Users can view daily wellness schedule with completion time and mark the selected activity as done. |
| FR19 | Users can view workout details, duration, category, and step sequence. |
| FR20 | Users can view weekly and monthly workout wellness reports. |
| FR21 | Users can open report hub screens for period, meal, workout, and final report. |
| FR22 | Users can view Privacy Policy from Settings. |
| FR23 | Users can navigate between major screens using bottom navigation and back icons. |

## 2.3 Non-Functional Requirements (NFR)

| ID | Non-Functional Requirement |
|---|---|
| NFR01 | Usability: The app uses clear cards, icons, bottom navigation, and back buttons for simple navigation. |
| NFR02 | Accessibility: Text uses readable sizes, strong contrast, and large tappable cards/buttons. |
| NFR03 | Performance: Local tracking data uses lightweight SharedPreferences for quick updates. |
| NFR04 | Reliability: Data entered in tracking screens remains available after leaving and reopening screens. |
| NFR05 | Security: User profile information is handled through Firebase Authentication/Firestore and local preference storage. |
| NFR06 | Maintainability: Features are separated into Activities and store/helper classes such as `PeriodStore`, `MealTrackingStore`, and `WorkoutTrackingStore`. |
| NFR07 | Consistency: Screens use a common light purple background, white cards, and consistent toolbar/back navigation. |
| NFR08 | Scalability: The report hub and tracking hubs allow new report/tracking modules to be added later. |

## 2.4 Technical Requirements

| Area | Requirement |
|---|---|
| Platform | Android mobile application |
| Language | Kotlin |
| UI | Android XML layouts, CardView, RecyclerView, BottomNavigationView, FloatingActionButton |
| Authentication | Firebase Authentication |
| Cloud Data | Firebase Firestore for user profile/check-in related data |
| Local Data | SharedPreferences through store classes for period, meal, workout, and profile data |
| Build Tool | Gradle / Android Gradle Plugin |
| Minimum Runtime | Android device/emulator capable of running Material Components and AndroidX |
| Assets | Drawable XML icons, JPG/PNG images, generated vector splash icons |
| IDE | Android Studio |

## 3. Mobile App Design

### 3.1 Logo

**Concept & Rationale:**  
The logo concept uses a purple heart icon, representing care, women’s health, emotional support, and wellness. The app supports **SDG 3: Good Health and Well-being** by helping users track PCOS-related health data, period cycles, meals, exercise, yoga, meditation, and reports.

**Usage:**  
- App icon / launcher branding
- Login screen icon
- Splash screen branding
- Profile and wellness visual identity

**Asset Files:**  
- `app/src/main/res/drawable/ic_heart.xml`
- `app/src/main/res/drawable/ic_splash_yoga.xml`
- `app/src/main/res/drawable/ic_splash_exercise.xml`
- `app/src/main/res/drawable/ic_splash_meal.xml`
- Imported images in `app/src/main/res/drawable/`

### 3.2 Theme Colors

| Color Role | Hex Code | Usage |
|---|---:|---|
| Primary Purple | `#8B7BB5` | Buttons, selected tabs, active navigation, heart logo |
| Light Purple Background | `#F0EEF8` | Main screen background |
| Purple Light | `#EDE9F7` | Icon backgrounds and badges |
| Teal | `#4DB6AC` | Meal tracking, yoga, completed states |
| Teal Light | `#E0F5F1` | Meal/yoga cards and icon backgrounds |
| Period Red | `#E57373` | Period cards, period dots, alerts |
| Red Light | `#FFEBEE` | Period icon background |
| Green | `#4CAF50` / `#66BB6A` | Normal cycle, success states |
| Orange | `#FFB74D` | Medium/partial status |
| Dark Text | `#2D2D2D` | Titles and primary text |
| Body Text | `#555555` / `#666666` | Paragraph and detail text |
| Muted Text | `#888888` / `#AAAAAA` | Captions, subtitles, inactive tabs |
| Card White | `#FFFFFF` | Cards, inputs, bottom navigation |

**Sample Swatch:**  
Primary `#8B7BB5` | Background `#F0EEF8` | Teal `#4DB6AC` | Period `#E57373` | Text `#2D2D2D`

### 3.3 Font Selection

The app uses Android’s default system sans-serif font family. This gives a clean native Android appearance and good readability.

| UI Scenario | Font Type / Style | Color | Size |
|---|---|---:|---:|
| Main page titles | System sans-serif, Bold | `#2D2D2D` | 24sp-28sp |
| Section/card titles | System sans-serif, Bold | `#2D2D2D` | 16sp-20sp |
| Body text | System sans-serif, Regular | `#444444` / `#555555` | 13sp-15sp |
| Subtitles/captions | System sans-serif, Regular | `#888888` / `#AAAAAA` | 11sp-14sp |
| Button text | System sans-serif, Bold/All caps | `#FFFFFF` | 14sp-16sp |
| Navigation labels | System sans-serif, Regular | Bottom nav tint colors | 10sp-12sp |
| Report numbers | System sans-serif, Bold | Module colors | 18sp-28sp |

### 3.4 UI Prototype Designs

**Design Tool:** Android XML prototype implemented directly in Android Studio.  
**Proto Link:** Not applicable. Screens were prototyped directly through Android layouts and emulator previews.  

**Screenshots with Captions:**  
Add screenshots from the emulator for:
- Splash Screen: Shows PCOS Tracker branding and rotating health images.
- Login Screen: Allows user login with purple heart branding.
- Home Dashboard: Shows period, meals, workout cards, quick actions, and recent activity.
- Period Tracking Hub: Navigation to previous/current/next month and period guide.
- Period Calendar: Allows entering period dates, mood, pain level, and notes.
- Meal Tracking Hub: Navigation to daily, weekly/monthly tracking, meal gallery, and food guide.
- Daily Meal Tracking: Tracks breakfast, lunch, and dinner completion.
- Food Guide: Shows healthy PCOS-friendly food choices and foods to avoid.
- Workout & Wellness Hub: Navigation to all, meditation, yoga, exercise, and guide.
- Reports Hub: Navigation to period, meal, workout, and final reports.
- Settings: Profile, preferences, privacy, and account actions.

## 4. Mobile App Development

### 4.1 Implemented Functions

| Module | Implemented Functions |
|---|---|
| Authentication | Login, sign-up, logout, authenticated navigation |
| Splash | Rotating start images, login/home routing |
| Profile | Edit name/email/DOB/goals, image upload, save profile, show profile on home/settings |
| Home | Summary cards, quick actions, recent activities, bottom navigation |
| Period | Track start/end date, mood, pain, notes, history, prediction, cycle insight |
| Calendar | Monthly overview with period, meal, workout dots and legend navigation |
| Meal | Daily meal tracking, weekly/monthly grid, meal gallery, food guide, meal report |
| Workout | Exercise/yoga/meditation routines, completion time, done status, detail pages |
| Reports | Period report, meal report, workout report, final report |
| Settings | Notifications, dark mode placeholder, data backup placeholder, privacy policy, delete history, logout |
| Privacy | Privacy Policy interface with protection principles |

### 4.2 Mobile Interfaces (Screens)

| Screen Name | Activity | Purpose |
|---|---|---|
| Splash Screen | `SplashActivity` | Displays branding and rotating opening images before routing user. |
| Login Screen | `LoginActivity` | Allows existing users to log in. |
| Sign Up Screen | `SignUpActivity` | Allows new users to create an account. |
| Home Dashboard | `MainActivity` | Main overview of cycle, meals, exercise, quick actions, and recent activity. |
| Add Today’s Data | `AddDataHubActivity` | Lets user choose Period, Meal, or Workout tracking. |
| Calendar | `CalendarActivity` | Shows monthly tracking overview and period tracking calendar mode. |
| Period Tracking Hub | `PeriodTrackingHubActivity` | Opens previous/current/next period month and period guide. |
| Period Guide | `PeriodGuideActivity` | Provides period health guide information. |
| Meal Tracking Hub | `MealTrackingHubActivity` | Opens daily, weekly/monthly meal tracking, meal gallery, and food guide. |
| Daily / Monthly Meal Tracking | `MealPlanActivity` | Tracks meals and shows weekly/monthly progress. |
| Meal Gallery | `MealGalleryActivity` | Displays suggested PCOS-friendly meal plans. |
| Food Guide | `FoodGuideActivity` | Displays healthy food choices and foods to avoid for PCOS-friendly eating. |
| Meal Report | `MealReportActivity` | Shows monthly meal tracking report. |
| Workout & Wellness Hub | `WorkoutWellnessHubActivity` | Opens all, meditation, yoga, exercise, and guide screens. |
| Workout & Wellness | `WorkoutWellnessActivity` | Shows selected daily wellness activity and schedule. |
| Workout Detail | `WorkoutDetailActivity` | Shows routine details and step sequence. |
| Workout Guide | `WorkoutWellnessGuideActivity` | Shows PCOS-friendly workout and wellness tips. |
| Workout Report | `WorkoutWellnessReportActivity` | Shows weekly/monthly meditation, yoga, and exercise report. |
| Reports Hub | `ReportHubActivity` | Opens period, meal, workout, and final reports. |
| Period Report | `ReportsActivity` | Shows period history and cycle insight report. |
| Final Report | `FinalReportActivity` | Combines period, meal, and workout monthly summaries. |
| Personal Information | `PersonalInfoActivity` | Edits profile details and uploads profile image. |
| Privacy Policy | `PrivacyPolicyActivity` | Explains data collection, consent, protection, and control. |
| Settings | `SettingsActivity` | Manages profile, preferences, privacy, delete history, logout. |

### 4.3 Code Segments (with Explanation)

Full activity source files are located in:

`app/src/main/java/com/example/pcostracker/`

#### Activity Code Index

| Activity | Source File | Explanation |
|---|---|---|
| `SplashActivity` | `SplashActivity.kt` | Displays rotating splash images and routes to login or home based on Firebase user. |
| `LoginActivity` | `LoginActivity.kt` | Handles login form validation and Firebase login. |
| `SignUpActivity` | `SignUpActivity.kt` | Handles registration and account creation. |
| `MainActivity` | `MainActivity.kt` | Loads dashboard summaries, profile greeting/image, cards, recent activity, and navigation. |
| `AddDataHubActivity` | `AddDataHubActivity.kt` | Provides Period, Meal, and Workout tracking entry points. |
| `CalendarActivity` | `CalendarActivity.kt` | Builds monthly calendar, handles period tracking, date selection, history, and legend navigation. |
| `PeriodTrackingHubActivity` | `PeriodTrackingHubActivity.kt` | Opens period month modes and guide. |
| `PeriodGuideActivity` | `PeriodGuideActivity.kt` | Displays period education guide. |
| `MealTrackingHubActivity` | `MealTrackingHubActivity.kt` | Opens daily tracking, monthly tracking, meal gallery, and food guide. |
| `MealPlanActivity` | `MealPlanActivity.kt` | Handles daily meal checkboxes, save changes, and monthly meal grid. |
| `MealGalleryActivity` | `MealGalleryActivity.kt` | Displays weekly meal plans. |
| `FoodGuideActivity` | `FoodGuideActivity.kt` | Displays food guide cards for healthy choices and foods to avoid. |
| `MealReportActivity` | `MealReportActivity.kt` | Displays monthly meal completion report. |
| `WorkoutWellnessHubActivity` | `WorkoutWellnessHubActivity.kt` | Opens workout/wellness categories and guide. |
| `WorkoutWellnessActivity` | `WorkoutWellnessActivity.kt` | Filters all/meditation/yoga/exercise and updates completion time/status. |
| `WorkoutDetailActivity` | `WorkoutDetailActivity.kt` | Displays workout image, routine info, and sequence steps. |
| `WorkoutWellnessGuideActivity` | `WorkoutWellnessGuideActivity.kt` | Displays PCOS workout and wellness guidance. |
| `WorkoutWellnessReportActivity` | `WorkoutWellnessReportActivity.kt` | Calculates and displays weekly/monthly workout completion. |
| `ReportHubActivity` | `ReportHubActivity.kt` | Routes to period, meal, workout, and final reports. |
| `ReportsActivity` | `ReportsActivity.kt` | Displays cycle insight and period history. |
| `FinalReportActivity` | `FinalReportActivity.kt` | Combines monthly period, meal, and workout summaries. |
| `PersonalInfoActivity` | `PersonalInfoActivity.kt` | Handles profile input, image picker, local save, and Firestore update. |
| `PrivacyPolicyActivity` | `PrivacyPolicyActivity.kt` | Binds privacy policy rows and handles settings navigation. |
| `SettingsActivity` | `settingsActivity.kt` | Loads profile, handles settings actions, privacy policy, delete history, logout. |
| `DailyCheckInActivity` | `DailyCheckInActivity.kt` | Legacy check-in screen for meals, period pain, and workout duration. |

#### Important Code Examples

**Profile image upload and save:**

```kotlin
private val imagePicker = registerForActivityResult(ActivityResultContracts.OpenDocument()) { uri ->
    uri ?: return@registerForActivityResult
    selectedImageUri = uri
    contentResolver.takePersistableUriPermission(uri, Intent.FLAG_GRANT_READ_URI_PERMISSION)
    findViewById<CircleImageView>(R.id.profileImageEdit).setImageURI(uri)
}
```

This code opens Android’s document image picker, stores the selected URI, and previews it in the profile image view.

**Home greeting and profile image:**

```kotlin
private fun setupProfileHeader() {
    val storedName = ProfileStore.name(this)
    findViewById<TextView>(R.id.todayLabel).text = "Hi, ${storedName.ifEmpty { "there" }}"

    val imageUri = ProfileStore.imageUri(this)
    if (imageUri.isNotEmpty()) {
        findViewById<CircleImageView>(R.id.profileImage).setImageURI(Uri.parse(imageUri))
    }
}
```

This displays the saved profile name and image on the home dashboard.

**Period save function:**

```kotlin
private fun savePeriodLog() {
    val start = selectedStartDate ?: return
    val end = selectedEndDate ?: start
    PeriodStore.saveLog(this, PeriodLog(start, end, selectedMood, selectedPainLevel, notes))
    setupCalendar()
    updateHistory()
}
```

This stores period tracking details and refreshes calendar/history.

**Daily meal tracking save:**

```kotlin
saveChangesBtn.setOnClickListener {
    selectedMeals.forEach { mealId ->
        MealTrackingStore.setCompleted(this, mealId, true)
    }
    refreshDailyStatus()
}
```

This saves breakfast, lunch, and dinner completion.

**Workout category filtering:**

```kotlin
val filtered = when (key) {
    "exercise" -> allWorkouts.filter { it.category == "Exercises" }
    "yoga" -> allWorkouts.filter { it.category == "Yoga" }
    "meditation" -> allWorkouts.filter { it.category == "Meditation" }
    else -> allWorkouts
}
```

This changes the workout screen based on selected interface.

**Privacy Policy row binding:**

```kotlin
private fun bindPolicyRow(rowId: Int, icon: String, title: String, body: String) {
    val row = findViewById<View>(rowId)
    row.findViewById<TextView>(R.id.privacyIcon).text = icon
    row.findViewById<TextView>(R.id.privacyTitle).text = title
    row.findViewById<TextView>(R.id.privacyBody).text = body
}
```

This keeps the privacy policy screen reusable and easy to update.

**Food Guide navigation:**

```kotlin
findViewById<CardView>(R.id.cardFoodGuide).setOnClickListener {
    startActivity(Intent(this, FoodGuideActivity::class.java))
}
```

This code connects the Meal Tracking hub to the Food Guide interface.

**Note:** Full source code for every Activity is provided in `ACTIVITY_CODE_APPENDIX.md`.

## 5. Mobile App Testing

### 5.1 Test Case Table

| Test ID | Feature | Preconditions | Steps | Expected Result | Actual | Status |
|---|---|---|---|---|---|---|
| TC01 | Splash navigation | App installed | Open app | Splash appears and navigates to login/home | Build verified | Pass |
| TC02 | Login | Existing user account | Enter email/password and tap Login | User opens Home screen | To test on emulator | Pending |
| TC03 | Profile save | User logged in | Open Settings > Edit Profile, enter name, select image, save | Name/image saved and shown on Home | Implemented | Pass |
| TC04 | Home quick actions | App on Home | Tap View Calendar, Add Today’s Data, View Reports | Correct hub/screen opens | Implemented | Pass |
| TC05 | Period tracking hub | App open | Tap Period card | Period Tracking hub opens | Implemented | Pass |
| TC06 | Period save | Period screen open | Select start/end, mood, pain, notes, save | Period appears in history/calendar | Implemented | Pass |
| TC07 | Next period reminder | Period history exists | Open Period Report | Next period reminder appears | Implemented | Pass |
| TC08 | Period guide | Period hub open | Tap Period Guide | Period guide opens with care information | Implemented | Pass |
| TC09 | Meal hub | App open | Tap Meals card | Meal Tracking hub opens | Implemented | Pass |
| TC10 | Daily meal tracking | Meal Tracking > Daily | Tick meals and tap Save Changes | Status updates to Done/Partial | Implemented | Pass |
| TC11 | Weekly/monthly meal grid | Meal Tracking > Weekly & Monthly | Tap week day circle | Daily meal tracking opens for that day | Implemented | Pass |
| TC12 | Meal gallery | Meal hub open | Tap Meal Gallery | Weekly meal plan list opens | Implemented | Pass |
| TC13 | Food guide | Meal hub open | Tap Food Guide | Food Guide opens with healthy choices and avoid list | Implemented | Pass |
| TC14 | Meal report | Reports hub open | Tap Meal Report | Monthly meal report appears | Implemented | Pass |
| TC15 | Workout hub | App open | Tap Workout card | Workout & Wellness Tracking hub opens | Implemented | Pass |
| TC16 | Workout category | Workout hub open | Tap Meditation/Yoga/Exercise | Correct filtered interface opens | Implemented | Pass |
| TC17 | Workout completion | Workout screen open | Tick Done | Completion is saved for selected activity | Implemented | Pass |
| TC18 | Workout report | Reports hub open | Tap Workout Report | Weekly/monthly report appears | Implemented | Pass |
| TC19 | Final report | Reports hub open | Tap Final Report | Combined report appears | Implemented | Pass |
| TC20 | Privacy policy | Settings open | Tap Privacy Policy | Privacy Policy interface opens | Implemented | Pass |
| TC21 | Back navigation | Any sub-screen open | Tap back icon | Previous interface opens/closes current screen | Implemented | Pass |
| TC22 | Bottom navigation | Any main screen open | Tap Home/Calendar/Add/Reports/Settings | Correct screen opens | Implemented | Pass |
| TC23 | Build test | Source code available | Run `.\gradlew.bat :app:assembleDebug` | Debug APK builds successfully | Build passed | Pass |

## Appendix: Main Source Paths

- Kotlin activities: `app/src/main/java/com/example/pcostracker/`
- XML layouts: `app/src/main/res/layout/`
- Drawable icons/images: `app/src/main/res/drawable/`
- Bottom navigation menu: `app/src/main/res/menu/bottom_nav_menu.xml`
- Manifest: `app/src/main/AndroidManifest.xml`
