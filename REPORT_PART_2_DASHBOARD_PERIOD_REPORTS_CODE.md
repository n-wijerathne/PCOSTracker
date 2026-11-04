# Part 2 - Splash, Dashboard, Calendar, Period and Reports Screens

This section contains separate screen documentation and code for the requested report part.

## Splash Screen

**Purpose:** Shows PCOS Tracker branding and opening images before routing to login or home.

**Activity File:** `SplashActivity.kt`  
**Layout File:** `activity_splash.xml`

### Kotlin Activity Code

```kotlin
package com.example.pcostracker

import android.content.Intent
import android.os.Bundle
import android.os.Handler
import android.os.Looper
import android.widget.ImageView
import androidx.appcompat.app.AppCompatActivity
import com.google.firebase.auth.FirebaseAuth

class SplashActivity : AppCompatActivity() {

    private lateinit var auth: FirebaseAuth

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_splash)

        auth = FirebaseAuth.getInstance()
        val splashImage = findViewById<ImageView>(R.id.splashImage)
        val splashImages = listOf(
            R.drawable.opening_image_one,
            R.drawable.opening_image_two,
            R.drawable.ic_splash_meal
        )
        val handler = Handler(Looper.getMainLooper())
        splashImages.forEachIndexed { index, image ->
            handler.postDelayed({
                splashImage.setImageResource(image)
            }, index * 800L)
        }

        // Wait while the opening images rotate, then navigate.
        handler.postDelayed({
            val currentUser = auth.currentUser
            if (currentUser != null) {
                // User already logged in → go to Home
                startActivity(Intent(this, MainActivity::class.java))
            } else {
                // Not logged in → go to Login
                startActivity(Intent(this, LoginActivity::class.java))
            }
            finish()
        }, 2500)
    }
}

```

### XML Layout Code

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#F0EEF8">

    <!-- Circular image container -->
    <FrameLayout
        android:id="@+id/imageContainer"
        android:layout_width="200dp"
        android:layout_height="200dp"
        android:background="@drawable/circle_background"
        app:layout_constraintBottom_toTopOf="@+id/appName"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintVertical_chainStyle="packed"
        android:layout_marginBottom="32dp"/>

    <ImageView
        android:id="@+id/splashImage"
        android:layout_width="180dp"
        android:layout_height="180dp"
        android:src="@drawable/ic_splash_yoga"
        android:scaleType="fitCenter"
        app:layout_constraintBottom_toBottomOf="@+id/imageContainer"
        app:layout_constraintEnd_toEndOf="@+id/imageContainer"
        app:layout_constraintStart_toStartOf="@+id/imageContainer"
        app:layout_constraintTop_toTopOf="@+id/imageContainer"/>

    <TextView
        android:id="@+id/appName"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="PCOS Tracker"
        android:textSize="28sp"
        android:textStyle="bold"
        android:textColor="#2D2D2D"
        app:layout_constraintBottom_toTopOf="@+id/tagline"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/imageContainer"
        android:layout_marginBottom="8dp"/>

    <TextView
        android:id="@+id/tagline"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Balance your health, naturally"
        android:textSize="14sp"
        android:textColor="#888888"
        app:layout_constraintBottom_toTopOf="@+id/progressBar"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/appName"
        android:layout_marginBottom="48dp"/>

    <ProgressBar
        android:id="@+id/progressBar"
        style="@style/Widget.AppCompat.ProgressBar"
        android:layout_width="40dp"
        android:layout_height="40dp"
        android:indeterminateTint="#8B7BB5"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/tagline"
        android:layout_marginBottom="80dp"/>

</androidx.constraintlayout.widget.ConstraintLayout>

```

---

## Home Dashboard

**Purpose:** Shows health summary cards, quick actions, recent activity, profile image, and greeting.

**Activity File:** `MainActivity.kt`  
**Layout File:** `activity_main.xml`

### Kotlin Activity Code

```kotlin
package com.example.pcostracker

import android.content.Intent
import android.net.Uri
import android.os.Bundle
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import androidx.cardview.widget.CardView
import androidx.recyclerview.widget.LinearLayoutManager
import androidx.recyclerview.widget.RecyclerView
import com.google.android.material.bottomnavigation.BottomNavigationView
import java.text.SimpleDateFormat
import java.util.*

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        setupDate()
        setupProfileHeader()
        setupPeriodSummary()
        setupMealSummary()
        setupRecentActivity()
        setupQuickActions()
        setupBottomNav()
    }

    override fun onResume() {
        super.onResume()
        setupProfileHeader()
        setupPeriodSummary()
        setupMealSummary()
        setupRecentActivity()
    }

    private fun setupDate() {
        val dateFormat = SimpleDateFormat("EEEE, MMMM d", Locale.getDefault())
        val currentDate = dateFormat.format(Date())
        findViewById<TextView>(R.id.currentDate).text = currentDate
    }

    private fun setupProfileHeader() {
        val storedName = ProfileStore.name(this)
        val greetingName = storedName.ifEmpty { "there" }
        findViewById<TextView>(R.id.todayLabel).text = "Hi, $greetingName"

        val profileImage = findViewById<de.hdodenhof.circleimageview.CircleImageView>(R.id.profileImage)
        val imageUri = ProfileStore.imageUri(this)
        if (imageUri.isNotEmpty()) {
            try {
                profileImage.setImageURI(Uri.parse(imageUri))
            } catch (_: Exception) {
                profileImage.setImageResource(R.drawable.ic_profile_placeholder)
            }
        } else {
            profileImage.setImageResource(R.drawable.ic_profile_placeholder)
        }
    }

    private fun setupPeriodSummary() {
        val latest = PeriodStore.latestLog(this)
        val daysUntil = PeriodStore.daysUntilNextPeriod(this)

        findViewById<TextView>(R.id.periodDay).text = latest?.let {
            val today = SimpleDateFormat("yyyy-MM-dd", Locale.US).format(Date())
            if (today in it.startDate..it.endDate) {
                "Day ${PeriodStore.daysBetween(it.startDate, today) + 1}"
            } else {
                "${it.durationDays} days"
            }
        } ?: "Track"

        findViewById<TextView>(R.id.periodNext).text = when {
            daysUntil == null -> "Tap to start"
            daysUntil < 0 -> "Expected now"
            daysUntil == 0 -> "Expected today"
            else -> "Next in $daysUntil days"
        }
    }

    private fun setupMealSummary() {
        val completed = MealTrackingStore.completedCount(this)
        findViewById<TextView>(R.id.mealsKcal).text = "$completed/90"
    }

    private fun setupRecentActivity() {
        val recordedWeight = MealTrackingStore.endWeight(this)
            .ifEmpty { MealTrackingStore.startWeight(this) }
            .ifEmpty { "72.5" }
        val mealCompleted = MealTrackingStore.completedCount(this)
        val workoutPercent = WorkoutTrackingStore.totalPercent(this)
        val periodText = PeriodStore.latestLog(this)?.let { "${it.durationDays} day period logged" }
            ?: "Period tracking ready"
        val activities = listOf(
            ActivityItem("🍳", "Meals tracked: $mealCompleted/90", "Today"),
            ActivityItem("🧘", "Wellness progress: $workoutPercent%", "This month"),
            ActivityItem("🩸", periodText, "Cycle"),
            ActivityItem("⚖️", "Weight recorded: $recordedWeight kg", "6:45 AM")
        )

        val recyclerView = findViewById<RecyclerView>(R.id.recentActivityList)
        recyclerView.layoutManager = LinearLayoutManager(this)
        recyclerView.adapter = RecentActivityAdapter(activities)
    }

    private fun setupQuickActions() {
        findViewById<CardView>(R.id.cardCalendar).setOnClickListener {
            startActivity(Intent(this, CalendarActivity::class.java))
        }

        findViewById<CardView>(R.id.cardPeriod).setOnClickListener {
            startActivity(Intent(this, PeriodTrackingHubActivity::class.java))
        }

        findViewById<CardView>(R.id.cardMeals).setOnClickListener {
            startActivity(Intent(this, MealTrackingHubActivity::class.java))
        }

        findViewById<CardView>(R.id.cardWorkout).setOnClickListener {
            startActivity(Intent(this, WorkoutWellnessHubActivity::class.java))
        }

        findViewById<CardView>(R.id.cardAddData).setOnClickListener {
            startActivity(Intent(this, AddDataHubActivity::class.java))
        }

        findViewById<CardView>(R.id.cardReports).setOnClickListener {
            startActivity(Intent(this, ReportHubActivity::class.java))
        }

        findViewById<de.hdodenhof.circleimageview.CircleImageView>(R.id.profileImage).setOnClickListener {
            startActivity(Intent(this, PersonalInfoActivity::class.java))
        }
    }

    private fun setupBottomNav() {
        val bottomNav = findViewById<BottomNavigationView>(R.id.bottomNav)
        bottomNav.selectedItemId = R.id.nav_home

        bottomNav.setOnItemSelectedListener { item ->
            when (item.itemId) {
                R.id.nav_home -> true
                R.id.nav_calendar -> {
                    startActivity(Intent(this, CalendarActivity::class.java))
                    true
                }
                R.id.nav_add -> {
                    startActivity(Intent(this, AddDataHubActivity::class.java))
                    true
                }
                R.id.nav_reports -> {
                    startActivity(Intent(this, ReportHubActivity::class.java))
                    true
                }
                R.id.nav_settings -> {
                    startActivity(Intent(this, SettingsActivity::class.java))
                    true
                }
                else -> false
            }
        }
    }
}

```

### XML Layout Code

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.coordinatorlayout.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#F0EEF8">

    <androidx.core.widget.NestedScrollView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:paddingBottom="80dp"
        android:clipToPadding="false">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="20dp">

            <!-- Header -->
            <androidx.constraintlayout.widget.ConstraintLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="20dp">

                <LinearLayout
                    android:id="@+id/dateContainer"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:orientation="vertical"
                    app:layout_constraintStart_toStartOf="parent"
                    app:layout_constraintTop_toTopOf="parent">

                    <TextView
                        android:id="@+id/todayLabel"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="Hi"
                        android:textSize="12sp"
                        android:textColor="#888888"
                        android:letterSpacing="0.1"/>

                    <TextView
                        android:id="@+id/currentDate"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="Monday, January 12"
                        android:textSize="22sp"
                        android:textStyle="bold"
                        android:textColor="#2D2D2D"/>
                </LinearLayout>

                <de.hdodenhof.circleimageview.CircleImageView
                    android:id="@+id/profileImage"
                    android:layout_width="44dp"
                    android:layout_height="44dp"
                    android:src="@drawable/ic_profile_placeholder"
                    app:layout_constraintEnd_toEndOf="parent"
                    app:layout_constraintTop_toTopOf="parent"
                    app:layout_constraintBottom_toBottomOf="parent"/>

            </androidx.constraintlayout.widget.ConstraintLayout>

            <!-- Stats Cards Row -->
            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:orientation="horizontal"
                android:layout_marginBottom="24dp">

                <!-- Period Card -->
                <androidx.cardview.widget.CardView
                    android:id="@+id/cardPeriod"
                    android:layout_width="0dp"
                    android:layout_height="100dp"
                    android:layout_weight="1"
                    android:layout_marginEnd="8dp"
                    app:cardCornerRadius="16dp"
                    app:cardElevation="0dp"
                    app:cardBackgroundColor="#FDE8E8">

                    <LinearLayout
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:orientation="vertical"
                        android:gravity="center"
                        android:padding="8dp">

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="🩸"
                            android:textSize="20sp"/>

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="PERIOD"
                            android:textSize="10sp"
                            android:textColor="#E57373"
                            android:letterSpacing="0.1"/>

                        <TextView
                            android:id="@+id/periodDay"
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="Day 3"
                            android:textSize="16sp"
                            android:textStyle="bold"
                            android:textColor="#E57373"/>

                        <TextView
                            android:id="@+id/periodNext"
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="Next in 25 days"
                            android:textSize="9sp"
                            android:textColor="#E57373"/>
                    </LinearLayout>
                </androidx.cardview.widget.CardView>

                <!-- Meals Card -->
                <androidx.cardview.widget.CardView
                    android:id="@+id/cardMeals"
                    android:layout_width="0dp"
                    android:layout_height="100dp"
                    android:layout_weight="1"
                    android:layout_marginEnd="8dp"
                    app:cardCornerRadius="16dp"
                    app:cardElevation="0dp"
                    app:cardBackgroundColor="#E8F5E9">

                    <LinearLayout
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:orientation="vertical"
                        android:gravity="center"
                        android:padding="8dp">

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="🍽️"
                            android:textSize="20sp"/>

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="MEALS"
                            android:textSize="10sp"
                            android:textColor="#66BB6A"
                            android:letterSpacing="0.1"/>

                        <TextView
                            android:id="@+id/mealsKcal"
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="1,420"
                            android:textSize="16sp"
                            android:textStyle="bold"
                            android:textColor="#66BB6A"/>

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="kcal"
                            android:textSize="9sp"
                            android:textColor="#66BB6A"/>
                    </LinearLayout>
                </androidx.cardview.widget.CardView>

                <!-- Exercise Card -->
                <androidx.cardview.widget.CardView
                    android:id="@+id/cardWorkout"
                    android:layout_width="0dp"
                    android:layout_height="100dp"
                    android:layout_weight="1"
                    app:cardCornerRadius="16dp"
                    app:cardElevation="0dp"
                    app:cardBackgroundColor="#EDE7F6">

                    <LinearLayout
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:orientation="vertical"
                        android:gravity="center"
                        android:padding="8dp">

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="💪"
                            android:textSize="20sp"/>

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="EXERCISES"
                            android:textSize="10sp"
                            android:textColor="#8B7BB5"
                            android:letterSpacing="0.1"/>

                        <TextView
                            android:id="@+id/exerciseType"
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="Workout"
                            android:textSize="16sp"
                            android:textStyle="bold"
                            android:textColor="#8B7BB5"/>

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="&amp; Meditation"
                            android:textSize="9sp"
                            android:textColor="#8B7BB5"/>
                    </LinearLayout>
                </androidx.cardview.widget.CardView>

            </LinearLayout>

            <!-- Quick Actions Label -->
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="QUICK ACTIONS"
                android:textSize="12sp"
                android:textColor="#888888"
                android:letterSpacing="0.1"
                android:layout_marginBottom="12dp"/>

            <!-- View Calendar -->
            <androidx.cardview.widget.CardView
                android:id="@+id/cardCalendar"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="10dp"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp"
                app:cardBackgroundColor="#FFFFFF">

                <androidx.constraintlayout.widget.ConstraintLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:padding="16dp">

                    <TextView
                        android:id="@+id/calendarIcon"
                        android:layout_width="40dp"
                        android:layout_height="40dp"
                        android:text="📅"
                        android:textSize="20sp"
                        android:gravity="center"
                        android:background="@drawable/icon_bg_purple_light"
                        app:layout_constraintStart_toStartOf="parent"
                        app:layout_constraintTop_toTopOf="parent"
                        app:layout_constraintBottom_toBottomOf="parent"/>

                    <LinearLayout
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:orientation="vertical"
                        android:layout_marginStart="12dp"
                        app:layout_constraintStart_toEndOf="@id/calendarIcon"
                        app:layout_constraintTop_toTopOf="parent"
                        app:layout_constraintBottom_toBottomOf="parent"
                        app:layout_constraintEnd_toStartOf="@+id/arrowCalendar">

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="View Calendar"
                            android:textSize="15sp"
                            android:textStyle="bold"
                            android:textColor="#2D2D2D"/>

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="Track your monthly cycle"
                            android:textSize="12sp"
                            android:textColor="#AAAAAA"/>
                    </LinearLayout>

                    <TextView
                        android:id="@+id/arrowCalendar"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="›"
                        android:textSize="24sp"
                        android:textColor="#CCCCCC"
                        app:layout_constraintEnd_toEndOf="parent"
                        app:layout_constraintTop_toTopOf="parent"
                        app:layout_constraintBottom_toBottomOf="parent"/>

                </androidx.constraintlayout.widget.ConstraintLayout>
            </androidx.cardview.widget.CardView>

            <!-- Add Today's Data (highlighted) -->
            <androidx.cardview.widget.CardView
                android:id="@+id/cardAddData"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="10dp"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp"
                app:cardBackgroundColor="#8B7BB5">

                <androidx.constraintlayout.widget.ConstraintLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:padding="16dp">

                    <TextView
                        android:id="@+id/addIcon"
                        android:layout_width="40dp"
                        android:layout_height="40dp"
                        android:text="+"
                        android:textSize="24sp"
                        android:textColor="#8B7BB5"
                        android:gravity="center"
                        android:background="@drawable/icon_bg_white"
                        app:layout_constraintStart_toStartOf="parent"
                        app:layout_constraintTop_toTopOf="parent"
                        app:layout_constraintBottom_toBottomOf="parent"/>

                    <LinearLayout
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:orientation="vertical"
                        android:layout_marginStart="12dp"
                        app:layout_constraintStart_toEndOf="@id/addIcon"
                        app:layout_constraintTop_toTopOf="parent"
                        app:layout_constraintBottom_toBottomOf="parent"
                        app:layout_constraintEnd_toStartOf="@+id/arrowAdd">

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="Add Today's Data"
                            android:textSize="15sp"
                            android:textStyle="bold"
                            android:textColor="#FFFFFF"/>

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="Log meals, symptoms &amp; more"
                            android:textSize="12sp"
                            android:textColor="#DDD6F3"/>
                    </LinearLayout>

                    <TextView
                        android:id="@+id/arrowAdd"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="›"
                        android:textSize="24sp"
                        android:textColor="#FFFFFF"
                        app:layout_constraintEnd_toEndOf="parent"
                        app:layout_constraintTop_toTopOf="parent"
                        app:layout_constraintBottom_toBottomOf="parent"/>

                </androidx.constraintlayout.widget.ConstraintLayout>
            </androidx.cardview.widget.CardView>

            <!-- View Reports -->
            <androidx.cardview.widget.CardView
                android:id="@+id/cardReports"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="24dp"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp"
                app:cardBackgroundColor="#FFFFFF">

                <androidx.constraintlayout.widget.ConstraintLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:padding="16dp">

                    <TextView
                        android:id="@+id/reportsIcon"
                        android:layout_width="40dp"
                        android:layout_height="40dp"
                        android:text="📊"
                        android:textSize="20sp"
                        android:gravity="center"
                        android:background="@drawable/icon_bg_purple_light"
                        app:layout_constraintStart_toStartOf="parent"
                        app:layout_constraintTop_toTopOf="parent"
                        app:layout_constraintBottom_toBottomOf="parent"/>

                    <LinearLayout
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:orientation="vertical"
                        android:layout_marginStart="12dp"
                        app:layout_constraintStart_toEndOf="@id/reportsIcon"
                        app:layout_constraintTop_toTopOf="parent"
                        app:layout_constraintBottom_toBottomOf="parent"
                        app:layout_constraintEnd_toStartOf="@+id/arrowReports">

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="View Reports"
                            android:textSize="15sp"
                            android:textStyle="bold"
                            android:textColor="#2D2D2D"/>

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="Analyze your health trends"
                            android:textSize="12sp"
                            android:textColor="#AAAAAA"/>
                    </LinearLayout>

                    <TextView
                        android:id="@+id/arrowReports"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="›"
                        android:textSize="24sp"
                        android:textColor="#CCCCCC"
                        app:layout_constraintEnd_toEndOf="parent"
                        app:layout_constraintTop_toTopOf="parent"
                        app:layout_constraintBottom_toBottomOf="parent"/>

                </androidx.constraintlayout.widget.ConstraintLayout>
            </androidx.cardview.widget.CardView>

            <!-- Recent Activity Label -->
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="RECENT ACTIVITY"
                android:textSize="12sp"
                android:textColor="#888888"
                android:letterSpacing="0.1"
                android:layout_marginBottom="12dp"/>

            <!-- Recent Activity List -->
            <androidx.recyclerview.widget.RecyclerView
                android:id="@+id/recentActivityList"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:nestedScrollingEnabled="false"/>

        </LinearLayout>
    </androidx.core.widget.NestedScrollView>

    <!-- Bottom Navigation -->
    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bottomNav"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom"
        android:background="#FFFFFF"
        app:menu="@menu/bottom_nav_menu"
        app:itemIconTint="@color/bottom_nav_color"
        app:itemTextColor="@color/bottom_nav_color"
        app:labelVisibilityMode="labeled"/>

</androidx.coordinatorlayout.widget.CoordinatorLayout>

```

---

## Add Today's Data

**Purpose:** Lets users choose Period, Meal, or Workout tracking.

**Activity File:** `AddDataHubActivity.kt`  
**Layout File:** `activity_add_data_hub.xml`

### Kotlin Activity Code

```kotlin
package com.example.pcostracker

import android.content.Intent
import android.os.Bundle
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import androidx.cardview.widget.CardView
import com.google.android.material.bottomnavigation.BottomNavigationView

class AddDataHubActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_add_data_hub)

        findViewById<CardView>(R.id.cardAddPeriod).setOnClickListener {
            startActivity(Intent(this, PeriodTrackingHubActivity::class.java))
        }
        findViewById<CardView>(R.id.cardAddMeal).setOnClickListener {
            startActivity(Intent(this, MealTrackingHubActivity::class.java))
        }
        findViewById<CardView>(R.id.cardAddWorkout).setOnClickListener {
            startActivity(Intent(this, WorkoutWellnessHubActivity::class.java))
        }

        findViewById<TextView>(R.id.backBtn).setOnClickListener { finish() }
        setupNavigation()
    }

    private fun setupNavigation() {
        val bottomNav = findViewById<BottomNavigationView>(R.id.bottomNav)
        bottomNav.selectedItemId = R.id.nav_add
        bottomNav.setOnItemSelectedListener { item ->
            when (item.itemId) {
                R.id.nav_home -> { startActivity(Intent(this, MainActivity::class.java)); true }
                R.id.nav_calendar -> { startActivity(Intent(this, CalendarActivity::class.java)); true }
                R.id.nav_add -> true
                R.id.nav_reports -> { startActivity(Intent(this, ReportHubActivity::class.java)); true }
                R.id.nav_settings -> { startActivity(Intent(this, SettingsActivity::class.java)); true }
                else -> false
            }
        }
    }
}

```

### XML Layout Code

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.coordinatorlayout.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#F0EEF8">

    <androidx.core.widget.NestedScrollView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:paddingBottom="80dp">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="20dp">

            <TextView android:id="@+id/backBtn" android:layout_width="44dp" android:layout_height="44dp"
                android:gravity="center" android:text="‹" android:textColor="#2D2D2D" android:textSize="28sp"/>

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_marginBottom="18dp"
                android:text="Add Today's Data"
                android:textColor="#2D2D2D"
                android:textSize="26sp"
                android:textStyle="bold"/>

            <androidx.cardview.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="150dp"
                android:layout_marginBottom="16dp"
                app:cardCornerRadius="18dp"
                app:cardElevation="0dp">

                <FrameLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent">

                    <ImageView
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:scaleType="centerCrop"
                        android:src="@drawable/add_data_pcos_image" />

                    <View
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:background="@drawable/gradient_overlay_dark" />

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_gravity="bottom|start"
                        android:layout_margin="18dp"
                        android:text="Log your daily health data"
                        android:textColor="#FFFFFF"
                        android:textSize="20sp"
                        android:textStyle="bold" />
                </FrameLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView android:id="@+id/cardAddPeriod"
                android:layout_width="match_parent" android:layout_height="86dp" android:layout_marginBottom="12dp"
                app:cardBackgroundColor="#FFFFFF" app:cardCornerRadius="16dp" app:cardElevation="0dp">
                <TextView android:layout_width="match_parent" android:layout_height="match_parent"
                    android:gravity="center_vertical" android:paddingStart="18dp" android:text="Period"
                    android:textColor="#2D2D2D" android:textSize="18sp" android:textStyle="bold"/>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView android:id="@+id/cardAddMeal"
                android:layout_width="match_parent" android:layout_height="86dp" android:layout_marginBottom="12dp"
                app:cardBackgroundColor="#FFFFFF" app:cardCornerRadius="16dp" app:cardElevation="0dp">
                <TextView android:layout_width="match_parent" android:layout_height="match_parent"
                    android:gravity="center_vertical" android:paddingStart="18dp" android:text="Meal"
                    android:textColor="#2D2D2D" android:textSize="18sp" android:textStyle="bold"/>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView android:id="@+id/cardAddWorkout"
                android:layout_width="match_parent" android:layout_height="86dp"
                app:cardBackgroundColor="#FFFFFF" app:cardCornerRadius="16dp" app:cardElevation="0dp">
                <TextView android:layout_width="match_parent" android:layout_height="match_parent"
                    android:gravity="center_vertical" android:paddingStart="18dp" android:text="Workout"
                    android:textColor="#2D2D2D" android:textSize="18sp" android:textStyle="bold"/>
            </androidx.cardview.widget.CardView>
        </LinearLayout>
    </androidx.core.widget.NestedScrollView>

    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bottomNav"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom"
        android:background="#FFFFFF"
        app:itemIconTint="@color/bottom_nav_color"
        app:itemTextColor="@color/bottom_nav_color"
        app:labelVisibilityMode="labeled"
        app:menu="@menu/bottom_nav_menu"/>
</androidx.coordinatorlayout.widget.CoordinatorLayout>

```

---

## Calendar

**Purpose:** Displays monthly tracking overview and period calendar editing mode.

**Activity File:** `CalendarActivity.kt`  
**Layout File:** `activity_calendar.xml`

### Kotlin Activity Code

```kotlin
package com.example.pcostracker

import android.app.DatePickerDialog
import android.content.Intent
import android.os.Bundle
import android.widget.Button
import android.widget.EditText
import android.widget.ImageButton
import android.view.View
import android.widget.TextView
import android.widget.Toast
import androidx.activity.addCallback
import androidx.appcompat.app.AppCompatActivity
import androidx.recyclerview.widget.GridLayoutManager
import androidx.recyclerview.widget.RecyclerView
import com.google.android.material.bottomnavigation.BottomNavigationView
import com.google.android.material.floatingactionbutton.FloatingActionButton
import java.text.SimpleDateFormat
import java.util.*

class CalendarActivity : AppCompatActivity() {

    private var currentCalendar = Calendar.getInstance()
    private var selectedStartDate: String? = null
    private var selectedEndDate: String? = null
    private var selectedMood = "Happy"
    private var selectedPainLevel = "Low"
    private var selectingEndDate = false
    private var periodMode = false
    private val dateFormat = SimpleDateFormat("yyyy-MM-dd", Locale.US)

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_calendar)
        periodMode = intent.getBooleanExtra("periodMode", false)
        currentCalendar.add(Calendar.MONTH, intent.getIntExtra("monthOffset", 0))

        setupCalendar()
        setupPeriodTracker()
        setupMode()
        setupLegendActions()
        setupNavigation()
        setupBackNavigation()

        findViewById<FloatingActionButton>(R.id.fabAdd).setOnClickListener {
            startActivity(Intent(this, AddDataHubActivity::class.java))
        }

        findViewById<androidx.constraintlayout.widget.ConstraintLayout>(R.id.clearCalendarBtn)
            .setOnClickListener {
                PeriodStore.clearLogs(this)
                selectedStartDate = null
                selectedEndDate = null
                updatePeriodForm()
                setupCalendar()
                Toast.makeText(this, "Period history cleared", Toast.LENGTH_SHORT).show()
            }
    }

    private fun setupCalendar() {
        updateMonthHeader()

        val days = generateDaysForMonth()
        val recyclerView = findViewById<RecyclerView>(R.id.calendarGrid)
        recyclerView.layoutManager = GridLayoutManager(this, 7)
        recyclerView.adapter = CalendarAdapter(days) { day ->
            if (day.dayNumber > 0) {
                selectCalendarDay(day.dayNumber)
            }
        }

        findViewById<ImageButton>(R.id.prevMonth).setOnClickListener {
            currentCalendar.add(Calendar.MONTH, -1)
            setupCalendar()
        }

        findViewById<ImageButton>(R.id.nextMonth).setOnClickListener {
            currentCalendar.add(Calendar.MONTH, 1)
            setupCalendar()
        }
    }

    private fun updateMonthHeader() {
        val format = SimpleDateFormat("MMMM yyyy", Locale.getDefault())
        findViewById<TextView>(R.id.monthYearText).text = format.format(currentCalendar.time)
    }

    private fun generateDaysForMonth(): List<CalendarDay> {
        val days = mutableListOf<CalendarDay>()
        val cal = currentCalendar.clone() as Calendar
        cal.set(Calendar.DAY_OF_MONTH, 1)
        val periodLogs = PeriodStore.getLogs(this)
        val predictedLog = PeriodStore.predictedNextLog(this)

        val firstDayOfWeek = cal.get(Calendar.DAY_OF_WEEK) - 1
        val totalDays = cal.getActualMaximum(Calendar.DAY_OF_MONTH)
        val today = Calendar.getInstance().get(Calendar.DAY_OF_MONTH)
        val isCurrentMonth = currentCalendar.get(Calendar.MONTH) == Calendar.getInstance().get(Calendar.MONTH)
                && currentCalendar.get(Calendar.YEAR) == Calendar.getInstance().get(Calendar.YEAR)

        // Empty cells before first day
        repeat(firstDayOfWeek) { days.add(CalendarDay(0, false, emptyList())) }

        // Actual days
        for (day in 1..totalDays) {
            val isToday = isCurrentMonth && day == today
            val date = dateForDay(day)
            val periodLog = PeriodStore.logForDate(periodLogs, date)
            val mealDayIndex = MealTrackingStore.dayIndexForDate(this, date)
            val mealStatus = mealDayIndex?.let { MealTrackingStore.dayStatus(this, it) }
            val hasWorkout = WorkoutTrackingStore.categories.any { category ->
                WorkoutTrackingStore.isDone(this, category, date)
            }
            val isPeriodDay = periodLog != null
            val isPredictedPeriodDay = predictedLog?.let { date in it.startDate..it.endDate } == true
            val periodSummary = when {
                periodLog != null -> "${periodLog.painLevel.take(1)} ${periodLog.mood.take(1)}"
                mealStatus == "done" -> "Meals"
                mealStatus == "partial" -> "Meal"
                hasWorkout -> "Workout"
                isPredictedPeriodDay -> "Possible"
                else -> ""
            }
            val dots = mutableListOf<String>()
            if (isPeriodDay || isPredictedPeriodDay) dots.add("red")
            if (hasWorkout) dots.add("purple")
            when (mealStatus) {
                "done" -> dots.add("teal")
                "partial" -> dots.add("orange")
            }
            days.add(CalendarDay(day, isToday, dots, isPeriodDay, isPredictedPeriodDay, periodSummary))
        }

        return days
    }

    private fun setupPeriodTracker() {
        findViewById<Button>(R.id.selectStartDateBtn).setOnClickListener {
            showPeriodDatePicker(selectingStartDate = true)
        }

        findViewById<Button>(R.id.selectEndDateBtn).setOnClickListener {
            showPeriodDatePicker(selectingStartDate = false)
        }

        val moodViews = mapOf(
            "Happy" to findViewById<TextView>(R.id.moodHappy),
            "Neutral" to findViewById<TextView>(R.id.moodNeutral),
            "Sad" to findViewById<TextView>(R.id.moodSad)
        )
        val painViews = mapOf(
            "Low" to findViewById<TextView>(R.id.painLow),
            "Medium" to findViewById<TextView>(R.id.painMedium),
            "High" to findViewById<TextView>(R.id.painHigh)
        )

        fun refreshChoices() {
            moodViews.forEach { (mood, view) ->
                view.setTextColor(android.graphics.Color.parseColor(if (mood == selectedMood) "#8B7BB5" else "#888888"))
            }
            painViews.forEach { (pain, view) ->
                view.setTextColor(android.graphics.Color.parseColor(if (pain == selectedPainLevel) "#E57373" else "#888888"))
            }
        }

        moodViews.forEach { (mood, view) ->
            view.setOnClickListener {
                selectedMood = mood
                refreshChoices()
            }
        }
        painViews.forEach { (pain, view) ->
            view.setOnClickListener {
                selectedPainLevel = pain
                refreshChoices()
            }
        }

        findViewById<Button>(R.id.savePeriodBtn).setOnClickListener { savePeriodLog() }
        refreshChoices()
        updatePeriodForm()
        updateHistory()
    }

    private fun setupMode() {
        val periodVisibility = if (periodMode) View.VISIBLE else View.GONE
        findViewById<TextView>(R.id.calendarBackBtn).visibility = View.VISIBLE
        findViewById<androidx.cardview.widget.CardView>(R.id.periodTrackingCard).visibility = periodVisibility
        findViewById<androidx.cardview.widget.CardView>(R.id.periodHistoryCard).visibility = periodVisibility
        findViewById<androidx.constraintlayout.widget.ConstraintLayout>(R.id.clearCalendarBtn).visibility = periodVisibility
        findViewById<androidx.cardview.widget.CardView>(R.id.calendarLegendCard).visibility =
            if (periodMode) View.GONE else View.VISIBLE
    }

    private fun setupBackNavigation() {
        findViewById<TextView>(R.id.calendarBackBtn).setOnClickListener {
            if (periodMode) returnToPeriodTracking() else finish()
        }
        onBackPressedDispatcher.addCallback(this) {
            if (periodMode) {
                returnToPeriodTracking()
            } else {
                finish()
            }
        }
    }

    private fun returnToPeriodTracking() {
        startActivity(Intent(this, PeriodTrackingHubActivity::class.java))
        finish()
    }

    private fun setupLegendActions() {
        findViewById<android.widget.LinearLayout>(R.id.legendPeriodRow).setOnClickListener {
            startActivity(Intent(this, PeriodTrackingHubActivity::class.java))
        }
        findViewById<android.widget.LinearLayout>(R.id.legendMealRow).setOnClickListener {
            startActivity(Intent(this, MealTrackingHubActivity::class.java))
        }
        findViewById<android.widget.LinearLayout>(R.id.legendWorkoutRow).setOnClickListener {
            startActivity(Intent(this, WorkoutWellnessHubActivity::class.java))
        }
    }

    private fun selectCalendarDay(dayNumber: Int) {
        if (!periodMode) {
            return
        }

        val selectedDate = dateForDay(dayNumber)
        val logs = PeriodStore.getLogs(this)
        val existingLog = PeriodStore.logForDate(logs, selectedDate)
        val predictedLog = PeriodStore.predictedNextLog(this)

        if (existingLog != null) {
            loadLogForEditing(existingLog)
            Toast.makeText(this, "Loaded saved period for editing", Toast.LENGTH_SHORT).show()
            return
        }

        if (predictedLog != null && selectedDate in predictedLog.startDate..predictedLog.endDate) {
            loadLogForEditing(predictedLog)
            Toast.makeText(this, "Loaded predicted period. Edit and save to confirm.", Toast.LENGTH_SHORT).show()
            return
        }

        if (selectedStartDate == null || !selectingEndDate) {
            selectedStartDate = selectedDate
            selectedEndDate = selectedDate
            selectingEndDate = true
        } else {
            if (selectedDate < selectedStartDate!!) {
                selectedEndDate = selectedStartDate
                selectedStartDate = selectedDate
            } else {
                selectedEndDate = selectedDate
            }
            selectingEndDate = false
        }
        updatePeriodForm()
    }

    private fun showPeriodDatePicker(selectingStartDate: Boolean) {
        val seedDate = when {
            selectingStartDate && selectedStartDate != null -> selectedStartDate
            !selectingStartDate && selectedEndDate != null -> selectedEndDate
            else -> dateFormat.format(currentCalendar.time)
        }
        val cal = Calendar.getInstance().apply {
            time = dateFormat.parse(seedDate!!)!!
        }

        DatePickerDialog(
            this,
            { _, year, month, day ->
                val selected = Calendar.getInstance().apply {
                    set(Calendar.YEAR, year)
                    set(Calendar.MONTH, month)
                    set(Calendar.DAY_OF_MONTH, day)
                }
                val date = dateFormat.format(selected.time)
                if (selectingStartDate) {
                    selectedStartDate = date
                    if (selectedEndDate == null || selectedEndDate!! < date) {
                        selectedEndDate = date
                    }
                } else {
                    if (selectedStartDate != null && date < selectedStartDate!!) {
                        selectedEndDate = selectedStartDate
                        selectedStartDate = date
                    } else {
                        selectedEndDate = date
                    }
                }
                currentCalendar.time = selected.time
                setupCalendar()
                updatePeriodForm()
            },
            cal.get(Calendar.YEAR),
            cal.get(Calendar.MONTH),
            cal.get(Calendar.DAY_OF_MONTH)
        ).show()
    }

    private fun loadLogForEditing(log: PeriodLog) {
        selectedStartDate = log.startDate
        selectedEndDate = log.endDate
        selectedMood = log.mood
        selectedPainLevel = log.painLevel
        selectingEndDate = false
        findViewById<EditText>(R.id.specialNotesInput).setText(
            if (log.notes == "Predicted period") "" else log.notes
        )
        setupPeriodTracker()
        updatePeriodForm()
    }

    private fun savePeriodLog() {
        val start = selectedStartDate
        val end = selectedEndDate
        if (start == null || end == null) {
            Toast.makeText(this, "Select period start and end dates", Toast.LENGTH_SHORT).show()
            return
        }

        val notes = findViewById<EditText>(R.id.specialNotesInput).text.toString().trim()
        PeriodStore.saveLog(this, PeriodLog(start, end, selectedMood, selectedPainLevel, notes))
        Toast.makeText(this, "Period saved", Toast.LENGTH_SHORT).show()
        setupCalendar()
        updateHistory()
    }

    private fun updatePeriodForm() {
        findViewById<TextView>(R.id.selectedStartDate).text =
            selectedStartDate?.let { PeriodStore.formatDisplay(it) } ?: "Tap start date"
        findViewById<TextView>(R.id.selectedEndDate).text =
            selectedEndDate?.let { PeriodStore.formatDisplay(it) } ?: "Tap end date"
    }

    private fun updateHistory() {
        val logs = PeriodStore.getLogs(this)
        val nextPeriod = PeriodStore.nextPeriodStart(this)?.let { PeriodStore.formatDisplay(it) } ?: "Add history"
        findViewById<TextView>(R.id.nextPeriodText).text = "Next period reminder: $nextPeriod"
        val predicted = PeriodStore.predictedNextLog(this)
        val predictionText = predicted?.let {
            "\nPredicted duration: ${PeriodStore.formatDisplay(it.startDate)} - ${PeriodStore.formatDisplay(it.endDate)} (${it.durationDays} days)"
        } ?: ""
        findViewById<TextView>(R.id.historyText).text =
            if (logs.isEmpty()) {
                "No previous months saved yet."
            } else {
                logs.take(6).joinToString("\n\n") {
                    "${PeriodStore.formatDisplay(it.startDate)} - ${PeriodStore.formatDisplay(it.endDate)} (${it.durationDays} days)\nMood: ${it.mood}   Pain: ${it.painLevel}\n${it.notes.ifEmpty { "No special notes" }}"
                } + predictionText
            }
    }

    private fun dateForDay(day: Int): String {
        val cal = currentCalendar.clone() as Calendar
        cal.set(Calendar.DAY_OF_MONTH, day)
        return dateFormat.format(cal.time)
    }

    private fun setupNavigation() {
        val bottomNav = findViewById<BottomNavigationView>(R.id.bottomNav)
        bottomNav.selectedItemId = R.id.nav_calendar
        bottomNav.setOnItemSelectedListener { item ->
            when (item.itemId) {
                R.id.nav_home -> { startActivity(Intent(this, MainActivity::class.java)); true }
                R.id.nav_calendar -> true
                R.id.nav_add -> { startActivity(Intent(this, AddDataHubActivity::class.java)); true }
                R.id.nav_reports -> { startActivity(Intent(this, ReportHubActivity::class.java)); true }
                R.id.nav_settings -> {
                    startActivity(Intent(this, SettingsActivity::class.java))
                    true
                }
                else -> false
            }
        }
    }
}

```

### XML Layout Code

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.coordinatorlayout.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#F0EEF8">

    <androidx.core.widget.NestedScrollView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:paddingBottom="80dp"
        android:clipToPadding="false">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="20dp">

            <!-- Month Header -->
            <androidx.constraintlayout.widget.ConstraintLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="16dp">

                <TextView
                    android:id="@+id/calendarBackBtn"
                    android:layout_width="44dp"
                    android:layout_height="44dp"
                    android:gravity="center"
                    android:text="&lt;"
                    android:textColor="#2D2D2D"
                    android:textSize="24sp"
                    android:visibility="gone"
                    app:layout_constraintBottom_toBottomOf="parent"
                    app:layout_constraintStart_toStartOf="parent"
                    app:layout_constraintTop_toTopOf="parent"/>

                <TextView
                    android:id="@+id/monthYearText"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="January 2026"
                    android:textSize="24sp"
                    android:textStyle="bold"
                    android:textColor="#2D2D2D"
                    app:layout_constraintStart_toEndOf="@id/calendarBackBtn"
                    app:layout_constraintTop_toTopOf="parent"
                    app:layout_constraintBottom_toBottomOf="parent"/>

                <LinearLayout
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:orientation="horizontal"
                    app:layout_constraintEnd_toEndOf="parent"
                    app:layout_constraintTop_toTopOf="parent"
                    app:layout_constraintBottom_toBottomOf="parent">

                    <ImageButton
                        android:id="@+id/prevMonth"
                        android:layout_width="36dp"
                        android:layout_height="36dp"
                        android:src="@drawable/ic_chevron_left"
                        android:background="@drawable/nav_button_bg"
                        android:layout_marginEnd="8dp"
                        android:contentDescription="Previous month"/>

                    <ImageButton
                        android:id="@+id/nextMonth"
                        android:layout_width="36dp"
                        android:layout_height="36dp"
                        android:src="@drawable/ic_chevron_right"
                        android:background="@drawable/nav_button_bg"
                        android:contentDescription="Next month"/>
                </LinearLayout>

            </androidx.constraintlayout.widget.ConstraintLayout>

            <!-- Day of Week Headers -->
            <GridLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:columnCount="7"
                android:layout_marginBottom="4dp">

                <TextView android:layout_width="0dp" android:layout_columnWeight="1"
                    android:text="S" android:gravity="center" android:textSize="12sp" android:textColor="#AAAAAA"/>
                <TextView android:layout_width="0dp" android:layout_columnWeight="1"
                    android:text="M" android:gravity="center" android:textSize="12sp" android:textColor="#AAAAAA"/>
                <TextView android:layout_width="0dp" android:layout_columnWeight="1"
                    android:text="T" android:gravity="center" android:textSize="12sp" android:textColor="#AAAAAA"/>
                <TextView android:layout_width="0dp" android:layout_columnWeight="1"
                    android:text="W" android:gravity="center" android:textSize="12sp" android:textColor="#AAAAAA"/>
                <TextView android:layout_width="0dp" android:layout_columnWeight="1"
                    android:text="T" android:gravity="center" android:textSize="12sp" android:textColor="#AAAAAA"/>
                <TextView android:layout_width="0dp" android:layout_columnWeight="1"
                    android:text="F" android:gravity="center" android:textSize="12sp" android:textColor="#AAAAAA"/>
                <TextView android:layout_width="0dp" android:layout_columnWeight="1"
                    android:text="S" android:gravity="center" android:textSize="12sp" android:textColor="#AAAAAA"/>
            </GridLayout>

            <!-- Calendar Grid -->
            <androidx.recyclerview.widget.RecyclerView
                android:id="@+id/calendarGrid"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="20dp"
                android:nestedScrollingEnabled="false"/>

            <androidx.cardview.widget.CardView
                android:id="@+id/periodTrackingCard"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="16dp"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp"
                app:cardBackgroundColor="#FFFFFF">

                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:orientation="vertical"
                    android:padding="16dp">

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="Period Tracking"
                        android:textSize="18sp"
                        android:textStyle="bold"
                        android:textColor="#2D2D2D"
                        android:layout_marginBottom="12dp"/>

                    <LinearLayout
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:orientation="horizontal"
                        android:layout_marginBottom="12dp">

                        <Button
                            android:id="@+id/selectStartDateBtn"
                            android:layout_width="0dp"
                            android:layout_height="48dp"
                            android:layout_weight="1"
                            android:text="Start Date"/>

                        <Button
                            android:id="@+id/selectEndDateBtn"
                            android:layout_width="0dp"
                            android:layout_height="48dp"
                            android:layout_marginStart="10dp"
                            android:layout_weight="1"
                            android:text="End Date"/>
                    </LinearLayout>

                    <LinearLayout
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:orientation="horizontal"
                        android:layout_marginBottom="16dp">

                        <TextView
                            android:id="@+id/selectedStartDate"
                            android:layout_width="0dp"
                            android:layout_height="wrap_content"
                            android:layout_weight="1"
                            android:text="Tap start date"
                            android:textColor="#E57373"
                            android:textSize="14sp"/>

                        <TextView
                            android:id="@+id/selectedEndDate"
                            android:layout_width="0dp"
                            android:layout_height="wrap_content"
                            android:layout_weight="1"
                            android:text="Tap end date"
                            android:textColor="#E57373"
                            android:textSize="14sp"/>
                    </LinearLayout>

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="Mood"
                        android:textColor="#2D2D2D"
                        android:textStyle="bold"
                        android:textSize="16sp"
                        android:layout_marginBottom="8dp"/>

                    <LinearLayout
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:gravity="center"
                        android:orientation="horizontal"
                        android:layout_marginBottom="16dp">

                        <TextView
                            android:id="@+id/moodHappy"
                            android:layout_width="0dp"
                            android:layout_height="44dp"
                            android:layout_weight="1"
                            android:gravity="center"
                            android:text="Happy"
                            android:textSize="15sp"/>

                        <TextView
                            android:id="@+id/moodNeutral"
                            android:layout_width="0dp"
                            android:layout_height="44dp"
                            android:layout_weight="1"
                            android:gravity="center"
                            android:text="Neutral"
                            android:textSize="15sp"/>

                        <TextView
                            android:id="@+id/moodSad"
                            android:layout_width="0dp"
                            android:layout_height="44dp"
                            android:layout_weight="1"
                            android:gravity="center"
                            android:text="Sad"
                            android:textSize="15sp"/>
                    </LinearLayout>

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="Pain Level"
                        android:textColor="#2D2D2D"
                        android:textStyle="bold"
                        android:textSize="16sp"
                        android:layout_marginBottom="8dp"/>

                    <LinearLayout
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:gravity="center"
                        android:orientation="horizontal"
                        android:layout_marginBottom="16dp">

                        <TextView
                            android:id="@+id/painLow"
                            android:layout_width="0dp"
                            android:layout_height="44dp"
                            android:layout_weight="1"
                            android:gravity="center"
                            android:text="Low"
                            android:textSize="15sp"/>

                        <TextView
                            android:id="@+id/painMedium"
                            android:layout_width="0dp"
                            android:layout_height="44dp"
                            android:layout_weight="1"
                            android:gravity="center"
                            android:text="Medium"
                            android:textSize="15sp"/>

                        <TextView
                            android:id="@+id/painHigh"
                            android:layout_width="0dp"
                            android:layout_height="44dp"
                            android:layout_weight="1"
                            android:gravity="center"
                            android:text="High"
                            android:textSize="15sp"/>
                    </LinearLayout>

                    <EditText
                        android:id="@+id/specialNotesInput"
                        android:layout_width="match_parent"
                        android:layout_height="88dp"
                        android:layout_marginBottom="16dp"
                        android:background="@drawable/input_background"
                        android:gravity="top"
                        android:hint="Special notes"
                        android:inputType="textMultiLine"
                        android:padding="14dp"
                        android:textSize="14sp"/>

                    <Button
                        android:id="@+id/savePeriodBtn"
                        android:layout_width="match_parent"
                        android:layout_height="52dp"
                        android:text="Save Period"/>
                </LinearLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:id="@+id/periodHistoryCard"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="16dp"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp"
                app:cardBackgroundColor="#FFFFFF">

                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:orientation="vertical"
                    android:padding="16dp">

                    <TextView
                        android:id="@+id/nextPeriodText"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:text="Next period reminder: Add history"
                        android:textColor="#8B7BB5"
                        android:textSize="16sp"
                        android:textStyle="bold"
                        android:layout_marginBottom="14dp"/>

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="Period History"
                        android:textColor="#2D2D2D"
                        android:textSize="16sp"
                        android:textStyle="bold"
                        android:layout_marginBottom="8dp"/>

                    <TextView
                        android:id="@+id/historyText"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:text="No previous months saved yet."
                        android:textColor="#555555"
                        android:textSize="14sp"
                        android:lineSpacingExtra="3dp"/>
                </LinearLayout>
            </androidx.cardview.widget.CardView>

            <!-- Legend Card -->
            <androidx.cardview.widget.CardView
                android:id="@+id/calendarLegendCard"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="16dp"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp"
                app:cardBackgroundColor="#FFFFFF">

                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:orientation="vertical"
                    android:padding="16dp">

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="Legend"
                        android:textSize="16sp"
                        android:textStyle="bold"
                        android:textColor="#2D2D2D"
                        android:layout_marginBottom="12dp"/>

                    <!-- Period -->
                    <LinearLayout
                        android:id="@+id/legendPeriodRow"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:orientation="horizontal"
                        android:gravity="center_vertical"
                        android:minHeight="40dp"
                        android:layout_marginBottom="8dp">
                        <View android:layout_width="10dp" android:layout_height="10dp"
                            android:background="@drawable/dot_red" android:layout_marginEnd="10dp"/>
                        <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
                            android:text="Period" android:textSize="14sp" android:textColor="#555555"/>
                    </LinearLayout>

                    <!-- Workout -->
                    <LinearLayout
                        android:id="@+id/legendWorkoutRow"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:orientation="horizontal"
                        android:gravity="center_vertical"
                        android:minHeight="40dp"
                        android:layout_marginBottom="8dp">
                        <View android:layout_width="10dp" android:layout_height="10dp"
                            android:background="@drawable/dot_purple" android:layout_marginEnd="10dp"/>
                        <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
                            android:text="Workout" android:textSize="14sp" android:textColor="#555555"/>
                    </LinearLayout>

                    <!-- Meals -->
                    <LinearLayout
                        android:id="@+id/legendMealRow"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:orientation="horizontal"
                        android:gravity="center_vertical"
                        android:minHeight="40dp"
                        android:layout_marginBottom="8dp">
                        <View android:layout_width="10dp" android:layout_height="10dp"
                            android:background="@drawable/dot_teal" android:layout_marginEnd="10dp"/>
                        <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
                            android:text="Meals Logged" android:textSize="14sp" android:textColor="#555555"/>
                    </LinearLayout>

                </LinearLayout>
            </androidx.cardview.widget.CardView>

            <!-- Clear Monthly Calendar -->
            <androidx.constraintlayout.widget.ConstraintLayout
                android:id="@+id/clearCalendarBtn"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:padding="4dp">

                <TextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="Clear Monthly Calendar"
                    android:textSize="16sp"
                    android:textStyle="bold"
                    android:textColor="#2D2D2D"
                    app:layout_constraintStart_toStartOf="parent"
                    app:layout_constraintTop_toTopOf="parent"
                    app:layout_constraintBottom_toBottomOf="parent"/>

                <ImageView
                    android:layout_width="24dp"
                    android:layout_height="24dp"
                    android:src="@drawable/ic_delete"
                    app:tint="#AAAAAA"
                    app:layout_constraintEnd_toEndOf="parent"
                    app:layout_constraintTop_toTopOf="parent"
                    app:layout_constraintBottom_toBottomOf="parent"/>

            </androidx.constraintlayout.widget.ConstraintLayout>

        </LinearLayout>
    </androidx.core.widget.NestedScrollView>

    <!-- Bottom Navigation -->
    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bottomNav"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom"
        android:background="#FFFFFF"
        app:menu="@menu/bottom_nav_menu"
        app:itemIconTint="@color/bottom_nav_color"
        app:itemTextColor="@color/bottom_nav_color"
        app:labelVisibilityMode="labeled"/>

    <!-- FAB Add Button -->
    <com.google.android.material.floatingactionbutton.FloatingActionButton
        android:id="@+id/fabAdd"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom|center_horizontal"
        android:layout_marginBottom="28dp"
        android:src="@drawable/ic_add_circle"
        app:backgroundTint="#8B7BB5"
        app:tint="#FFFFFF"
        app:elevation="4dp"/>

</androidx.coordinatorlayout.widget.CoordinatorLayout>

```

---

## Period Tracking Hub

**Purpose:** Lets users open previous/current/next month period calendars and the period guide.

**Activity File:** `PeriodTrackingHubActivity.kt`  
**Layout File:** `activity_period_tracking_hub.xml`

### Kotlin Activity Code

```kotlin
package com.example.pcostracker

import android.content.Intent
import android.os.Bundle
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import androidx.cardview.widget.CardView
import com.google.android.material.bottomnavigation.BottomNavigationView

class PeriodTrackingHubActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_period_tracking_hub)

        findViewById<TextView>(R.id.backBtn).setOnClickListener { finish() }
        findViewById<CardView>(R.id.cardPreviousMonth).setOnClickListener { openPeriodMonth(-1) }
        findViewById<CardView>(R.id.cardCurrentMonth).setOnClickListener { openPeriodMonth(0) }
        findViewById<CardView>(R.id.cardNextMonth).setOnClickListener { openPeriodMonth(1) }
        findViewById<CardView>(R.id.cardPeriodGuide).setOnClickListener {
            startActivity(Intent(this, PeriodGuideActivity::class.java))
        }

        setupNavigation()
    }

    private fun openPeriodMonth(monthOffset: Int) {
        startActivity(
            Intent(this, CalendarActivity::class.java)
                .putExtra("periodMode", true)
                .putExtra("monthOffset", monthOffset)
        )
    }

    private fun setupNavigation() {
        val bottomNav = findViewById<BottomNavigationView>(R.id.bottomNav)
        bottomNav.setOnItemSelectedListener { item ->
            when (item.itemId) {
                R.id.nav_home -> { startActivity(Intent(this, MainActivity::class.java)); true }
                R.id.nav_calendar -> { startActivity(Intent(this, CalendarActivity::class.java)); true }
                R.id.nav_add -> { startActivity(Intent(this, AddDataHubActivity::class.java)); true }
                R.id.nav_reports -> { startActivity(Intent(this, ReportHubActivity::class.java)); true }
                R.id.nav_settings -> { startActivity(Intent(this, SettingsActivity::class.java)); true }
                else -> false
            }
        }
    }
}

```

### XML Layout Code

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.coordinatorlayout.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#F0EEF8">

    <androidx.core.widget.NestedScrollView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:paddingBottom="88dp">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="20dp">

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="18dp"
                android:gravity="center_vertical"
                android:orientation="horizontal">

                <TextView
                    android:id="@+id/backBtn"
                    android:layout_width="44dp"
                    android:layout_height="44dp"
                    android:gravity="center"
                    android:text="&lt;"
                    android:textColor="#2D2D2D"
                    android:textSize="24sp" />

                <TextView
                    android:layout_width="0dp"
                    android:layout_height="wrap_content"
                    android:layout_weight="1"
                    android:text="Period Tracking"
                    android:textColor="#2D2D2D"
                    android:textSize="24sp"
                    android:textStyle="bold" />
            </LinearLayout>

            <androidx.cardview.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="150dp"
                android:layout_marginBottom="16dp"
                app:cardCornerRadius="18dp"
                app:cardElevation="0dp">

                <FrameLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent">

                    <ImageView
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:scaleType="centerCrop"
                        android:src="@drawable/period_report_image" />

                    <View
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:background="@drawable/gradient_overlay_dark" />

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_gravity="bottom|start"
                        android:layout_margin="18dp"
                        android:text="Track your cycle with care"
                        android:textColor="#FFFFFF"
                        android:textSize="20sp"
                        android:textStyle="bold" />
                </FrameLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:id="@+id/cardPreviousMonth"
                android:layout_width="match_parent"
                android:layout_height="104dp"
                android:layout_marginBottom="12dp"
                app:cardBackgroundColor="#FFFFFF"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">
                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:gravity="center_vertical"
                    android:orientation="horizontal"
                    android:padding="18dp">
                    <ImageView
                        android:layout_width="46dp"
                        android:layout_height="46dp"
                        android:background="@drawable/icon_bg_purple_light"
                        android:padding="10dp"
                        android:src="@drawable/ic_chevron_left"
                        app:tint="#8B7BB5" />
                    <TextView
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:layout_marginStart="14dp"
                        android:layout_weight="1"
                        android:text="Previous Month"
                        android:textColor="#2D2D2D"
                        android:textSize="18sp"
                        android:textStyle="bold" />
                </LinearLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:id="@+id/cardCurrentMonth"
                android:layout_width="match_parent"
                android:layout_height="104dp"
                android:layout_marginBottom="12dp"
                app:cardBackgroundColor="#FFFFFF"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">
                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:gravity="center_vertical"
                    android:orientation="horizontal"
                    android:padding="18dp">
                    <ImageView
                        android:layout_width="46dp"
                        android:layout_height="46dp"
                        android:background="@drawable/icon_bg_red_light"
                        android:padding="10dp"
                        android:src="@drawable/ic_calendar"
                        app:tint="#E57373" />
                    <TextView
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:layout_marginStart="14dp"
                        android:layout_weight="1"
                        android:text="Current Month"
                        android:textColor="#2D2D2D"
                        android:textSize="18sp"
                        android:textStyle="bold" />
                </LinearLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:id="@+id/cardNextMonth"
                android:layout_width="match_parent"
                android:layout_height="104dp"
                android:layout_marginBottom="12dp"
                app:cardBackgroundColor="#FFFFFF"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">
                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:gravity="center_vertical"
                    android:orientation="horizontal"
                    android:padding="18dp">
                    <ImageView
                        android:layout_width="46dp"
                        android:layout_height="46dp"
                        android:background="@drawable/icon_bg_teal_light"
                        android:padding="10dp"
                        android:src="@drawable/ic_chevron_right"
                        app:tint="#4DB6AC" />
                    <TextView
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:layout_marginStart="14dp"
                        android:layout_weight="1"
                        android:text="Next Month"
                        android:textColor="#2D2D2D"
                        android:textSize="18sp"
                        android:textStyle="bold" />
                </LinearLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:id="@+id/cardPeriodGuide"
                android:layout_width="match_parent"
                android:layout_height="104dp"
                app:cardBackgroundColor="#FFFFFF"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">
                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:gravity="center_vertical"
                    android:orientation="horizontal"
                    android:padding="18dp">
                    <ImageView
                        android:layout_width="46dp"
                        android:layout_height="46dp"
                        android:background="@drawable/icon_bg_red_light"
                        android:padding="10dp"
                        android:src="@drawable/ic_reports"
                        app:tint="#E57373" />
                    <TextView
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:layout_marginStart="14dp"
                        android:layout_weight="1"
                        android:text="Period Guide"
                        android:textColor="#2D2D2D"
                        android:textSize="18sp"
                        android:textStyle="bold" />
                </LinearLayout>
            </androidx.cardview.widget.CardView>
        </LinearLayout>
    </androidx.core.widget.NestedScrollView>

    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bottomNav"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom"
        android:background="#FFFFFF"
        app:itemIconTint="@color/bottom_nav_color"
        app:itemTextColor="@color/bottom_nav_color"
        app:labelVisibilityMode="labeled"
        app:menu="@menu/bottom_nav_menu" />
</androidx.coordinatorlayout.widget.CoordinatorLayout>

```

---

## Period Guide

**Purpose:** Provides period cycle, bleeding, remedies, foods, hygiene, and doctor warning guidance.

**Activity File:** `PeriodGuideActivity.kt`  
**Layout File:** `activity_period_guide.xml`

### Kotlin Activity Code

```kotlin
package com.example.pcostracker

import android.content.Intent
import android.os.Bundle
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import com.google.android.material.bottomnavigation.BottomNavigationView

class PeriodGuideActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_period_guide)

        findViewById<TextView>(R.id.backBtn).setOnClickListener { finish() }
        setupNavigation()
    }

    private fun setupNavigation() {
        val bottomNav = findViewById<BottomNavigationView>(R.id.bottomNav)
        bottomNav.setOnItemSelectedListener { item ->
            when (item.itemId) {
                R.id.nav_home -> { startActivity(Intent(this, MainActivity::class.java)); true }
                R.id.nav_calendar -> { startActivity(Intent(this, CalendarActivity::class.java)); true }
                R.id.nav_add -> { startActivity(Intent(this, AddDataHubActivity::class.java)); true }
                R.id.nav_reports -> { startActivity(Intent(this, ReportHubActivity::class.java)); true }
                R.id.nav_settings -> { startActivity(Intent(this, SettingsActivity::class.java)); true }
                else -> false
            }
        }
    }
}

```

### XML Layout Code

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.coordinatorlayout.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#F0EEF8">

    <androidx.core.widget.NestedScrollView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:clipToPadding="false"
        android:paddingBottom="88dp">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="20dp">

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="18dp"
                android:gravity="center_vertical"
                android:orientation="horizontal">

                <TextView
                    android:id="@+id/backBtn"
                    android:layout_width="44dp"
                    android:layout_height="44dp"
                    android:gravity="center"
                    android:text="&lt;"
                    android:textColor="#2D2D2D"
                    android:textSize="24sp" />

                <TextView
                    android:layout_width="0dp"
                    android:layout_height="wrap_content"
                    android:layout_weight="1"
                    android:text="Period Guide"
                    android:textColor="#2D2D2D"
                    android:textSize="24sp"
                    android:textStyle="bold" />
            </LinearLayout>

            <androidx.cardview.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="150dp"
                android:layout_marginBottom="16dp"
                app:cardCornerRadius="18dp"
                app:cardElevation="0dp">

                <FrameLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent">

                    <ImageView
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:scaleType="centerCrop"
                        android:src="@drawable/period_report_image" />

                    <View
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:background="@drawable/gradient_overlay_dark" />

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_gravity="bottom|start"
                        android:layout_margin="18dp"
                        android:text="Simple period care guide"
                        android:textColor="#FFFFFF"
                        android:textSize="20sp"
                        android:textStyle="bold" />
                </FrameLayout>
            </androidx.cardview.widget.CardView>

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="14dp"
                android:orientation="horizontal">

                <androidx.cardview.widget.CardView
                    android:layout_width="0dp"
                    android:layout_height="132dp"
                    android:layout_marginEnd="8dp"
                    android:layout_weight="1"
                    app:cardBackgroundColor="#FFF3F4"
                    app:cardCornerRadius="16dp"
                    app:cardElevation="0dp">

                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:lineSpacingExtra="5dp"
                        android:padding="14dp"
                        android:text="🗓  Cycle\n21-35 days\n\n🩸  Bleeding\n3-7 days"
                        android:textColor="#444444"
                        android:textSize="14sp" />
                </androidx.cardview.widget.CardView>

                <androidx.cardview.widget.CardView
                    android:layout_width="0dp"
                    android:layout_height="132dp"
                    android:layout_marginStart="8dp"
                    android:layout_weight="1"
                    app:cardBackgroundColor="#FFF9E6"
                    app:cardCornerRadius="16dp"
                    app:cardElevation="0dp">

                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:lineSpacingExtra="5dp"
                        android:padding="14dp"
                        android:text="🙂  Common\nCramps\nBloating\nMood swings\nTiredness"
                        android:textColor="#444444"
                        android:textSize="14sp" />
                </androidx.cardview.widget.CardView>
            </LinearLayout>

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="14dp"
                android:orientation="horizontal">

                <androidx.cardview.widget.CardView
                    android:layout_width="0dp"
                    android:layout_height="210dp"
                    android:layout_marginEnd="8dp"
                    android:layout_weight="1"
                    app:cardBackgroundColor="#F1F7FF"
                    app:cardCornerRadius="16dp"
                    app:cardElevation="0dp">

                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:lineSpacingExtra="5dp"
                        android:padding="14dp"
                        android:text="☕  Remedies\nWarm water bag\nWarm tea/water\nLight exercise\nGood sleep\nDrink water"
                        android:textColor="#444444"
                        android:textSize="14sp" />
                </androidx.cardview.widget.CardView>

                <androidx.cardview.widget.CardView
                    android:layout_width="0dp"
                    android:layout_height="210dp"
                    android:layout_marginStart="8dp"
                    android:layout_weight="1"
                    app:cardBackgroundColor="#F0FBF7"
                    app:cardCornerRadius="16dp"
                    app:cardElevation="0dp">

                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:lineSpacingExtra="5dp"
                        android:padding="14dp"
                        android:text="🍌  Helpful Foods\nBanana\nGinger tea\nDark chocolate\nSpinach\nDates\nEggs"
                        android:textColor="#444444"
                        android:textSize="14sp" />
                </androidx.cardview.widget.CardView>
            </LinearLayout>

            <androidx.cardview.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="14dp"
                app:cardBackgroundColor="#FFF4FA"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">

                <TextView
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:lineSpacingExtra="5dp"
                    android:padding="16dp"
                    android:text="🧼  Hygiene\nChange pads every 4-6 hrs\nWear cotton underwear\nTrack dates with apps like Flo or Clue"
                    android:textColor="#444444"
                    android:textSize="14sp" />
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                app:cardBackgroundColor="#F7F1FF"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">

                <TextView
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:lineSpacingExtra="5dp"
                    android:padding="16dp"
                    android:text="⚕  See Doctor If\nVery heavy bleeding\nSevere pain\nMissed periods often\nBad smell/discharge"
                    android:textColor="#444444"
                    android:textSize="14sp" />
            </androidx.cardview.widget.CardView>
        </LinearLayout>
    </androidx.core.widget.NestedScrollView>

    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bottomNav"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom"
        android:background="#FFFFFF"
        app:itemIconTint="@color/bottom_nav_color"
        app:itemTextColor="@color/bottom_nav_color"
        app:labelVisibilityMode="labeled"
        app:menu="@menu/bottom_nav_menu" />
</androidx.coordinatorlayout.widget.CoordinatorLayout>

```

---

## Reports Hub

**Purpose:** Lets users choose Period, Meal, Workout, or Final Report.

**Activity File:** `ReportHubActivity.kt`  
**Layout File:** `activity_report_hub.xml`

### Kotlin Activity Code

```kotlin
package com.example.pcostracker

import android.content.Intent
import android.os.Bundle
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import androidx.cardview.widget.CardView
import com.google.android.material.bottomnavigation.BottomNavigationView

class ReportHubActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_report_hub)

        findViewById<CardView>(R.id.cardPeriodReport).setOnClickListener {
            startActivity(Intent(this, ReportsActivity::class.java))
        }
        findViewById<CardView>(R.id.cardMealReport).setOnClickListener {
            startActivity(Intent(this, MealReportActivity::class.java))
        }
        findViewById<CardView>(R.id.cardWorkoutReport).setOnClickListener {
            startActivity(Intent(this, WorkoutWellnessReportActivity::class.java))
        }
        findViewById<CardView>(R.id.cardFinalReport).setOnClickListener {
            startActivity(Intent(this, FinalReportActivity::class.java))
        }

        findViewById<TextView>(R.id.backBtn).setOnClickListener { finish() }
        setupNavigation()
    }

    private fun setupNavigation() {
        val bottomNav = findViewById<BottomNavigationView>(R.id.bottomNav)
        bottomNav.selectedItemId = R.id.nav_reports
        bottomNav.setOnItemSelectedListener { item ->
            when (item.itemId) {
                R.id.nav_home -> { startActivity(Intent(this, MainActivity::class.java)); true }
                R.id.nav_calendar -> { startActivity(Intent(this, CalendarActivity::class.java)); true }
                R.id.nav_add -> { startActivity(Intent(this, AddDataHubActivity::class.java)); true }
                R.id.nav_reports -> true
                R.id.nav_settings -> { startActivity(Intent(this, SettingsActivity::class.java)); true }
                else -> false
            }
        }
    }
}

```

### XML Layout Code

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.coordinatorlayout.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#F0EEF8">

    <androidx.core.widget.NestedScrollView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:paddingBottom="80dp">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="20dp">

            <TextView
                android:id="@+id/backBtn"
                android:layout_width="44dp"
                android:layout_height="44dp"
                android:gravity="center"
                android:text="&lt;"
                android:textColor="#2D2D2D"
                android:textSize="24sp"/>

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_marginBottom="6dp"
                android:text="Reports"
                android:textColor="#2D2D2D"
                android:textSize="26sp"
                android:textStyle="bold"/>

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_marginBottom="18dp"
                android:text="Choose the monthly report you want to review"
                android:textColor="#888888"
                android:textSize="14sp"/>

            <androidx.cardview.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="150dp"
                android:layout_marginBottom="16dp"
                app:cardBackgroundColor="#FFFFFF"
                app:cardCornerRadius="18dp"
                app:cardElevation="0dp">

                <FrameLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent">

                    <ImageView
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:padding="18dp"
                        android:scaleType="centerInside"
                        android:src="@drawable/report_checklist_image" />

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_gravity="bottom|start"
                        android:layout_margin="18dp"
                        android:text="Your monthly health reports"
                        android:textColor="#2D2D2D"
                        android:textSize="20sp"
                        android:textStyle="bold" />
                </FrameLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:id="@+id/cardPeriodReport"
                android:layout_width="match_parent"
                android:layout_height="92dp"
                android:layout_marginBottom="12dp"
                app:cardBackgroundColor="#FFFFFF"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">

                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:gravity="center_vertical"
                    android:orientation="vertical"
                    android:paddingStart="18dp"
                    android:paddingEnd="18dp">

                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:text="Period Report"
                        android:textColor="#2D2D2D"
                        android:textSize="18sp"
                        android:textStyle="bold"/>

                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:layout_marginTop="4dp"
                        android:text="Periods, mood, pain level, notes and cycle insight"
                        android:textColor="#888888"
                        android:textSize="13sp"/>
                </LinearLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:id="@+id/cardMealReport"
                android:layout_width="match_parent"
                android:layout_height="92dp"
                android:layout_marginBottom="12dp"
                app:cardBackgroundColor="#FFFFFF"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">

                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:gravity="center_vertical"
                    android:orientation="vertical"
                    android:paddingStart="18dp"
                    android:paddingEnd="18dp">

                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:text="Meal Report"
                        android:textColor="#2D2D2D"
                        android:textSize="18sp"
                        android:textStyle="bold"/>

                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:layout_marginTop="4dp"
                        android:text="Monthly meal tracking"
                        android:textColor="#888888"
                        android:textSize="13sp"/>
                </LinearLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:id="@+id/cardWorkoutReport"
                android:layout_width="match_parent"
                android:layout_height="92dp"
                android:layout_marginBottom="12dp"
                app:cardBackgroundColor="#FFFFFF"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">

                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:gravity="center_vertical"
                    android:orientation="vertical"
                    android:paddingStart="18dp"
                    android:paddingEnd="18dp">

                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:text="Workout Report"
                        android:textColor="#2D2D2D"
                        android:textSize="18sp"
                        android:textStyle="bold"/>

                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:layout_marginTop="4dp"
                        android:text="Meditation, yoga and exercise progress"
                        android:textColor="#888888"
                        android:textSize="13sp"/>
                </LinearLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:id="@+id/cardFinalReport"
                android:layout_width="match_parent"
                android:layout_height="92dp"
                app:cardBackgroundColor="#FFFFFF"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">

                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:gravity="center_vertical"
                    android:orientation="vertical"
                    android:paddingStart="18dp"
                    android:paddingEnd="18dp">

                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:text="Final Report"
                        android:textColor="#2D2D2D"
                        android:textSize="18sp"
                        android:textStyle="bold"/>

                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:layout_marginTop="4dp"
                        android:text="Monthly period, meal and workout updates"
                        android:textColor="#888888"
                        android:textSize="13sp"/>
                </LinearLayout>
            </androidx.cardview.widget.CardView>
        </LinearLayout>
    </androidx.core.widget.NestedScrollView>

    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bottomNav"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom"
        android:background="#FFFFFF"
        app:itemIconTint="@color/bottom_nav_color"
        app:itemTextColor="@color/bottom_nav_color"
        app:labelVisibilityMode="labeled"
        app:menu="@menu/bottom_nav_menu"/>
</androidx.coordinatorlayout.widget.CoordinatorLayout>

```

---

## Period Report

**Purpose:** Shows cycle insight, period history, mood, pain level, notes, and next reminder.

**Activity File:** `ReportsActivity.kt`  
**Layout File:** `activity_reports.xml`

### Kotlin Activity Code

```kotlin
package com.example.pcostracker

import android.content.Intent
import android.graphics.Color
import android.os.Bundle
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import com.google.android.material.bottomnavigation.BottomNavigationView

class ReportsActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_reports)

        bindPeriodReport()
        findViewById<TextView>(R.id.backBtn).setOnClickListener { finish() }

        setupNavigation()
    }

    override fun onResume() {
        super.onResume()
        bindPeriodReport()
    }

    private fun bindPeriodReport() {
        val logs = PeriodStore.getLogs(this)
        val averageCycle = PeriodStore.averageCycleLength(logs)?.toString() ?: "--"
        val latestDuration = logs.firstOrNull()?.durationDays?.toString() ?: "--"
        val nextPeriod = PeriodStore.nextPeriodStart(this)?.let { PeriodStore.formatDisplay(it) } ?: "Add period history"
        val predicted = PeriodStore.predictedNextLog(this)
        val averageCycleValue = PeriodStore.averageCycleLength(logs)
        val isRegular = averageCycleValue != null && averageCycleValue in 21..35
        val statusText = when {
            logs.size < 2 -> "Add another period month to calculate regularity"
            isRegular -> "Your cycle is regular"
            else -> "Your cycle is irregular"
        }

        findViewById<TextView>(R.id.reportCycleLength).text = "$averageCycle days"
        findViewById<TextView>(R.id.reportPeriodDuration).text = "$latestDuration days"
        findViewById<TextView>(R.id.reportNextPeriod).text = predicted?.let {
            "$nextPeriod (${it.durationDays} days expected)"
        } ?: nextPeriod
        findViewById<TextView>(R.id.cycleInsightText).text = PeriodStore.insight(logs)
        findViewById<TextView>(R.id.cycleStatusText).text = statusText
        findViewById<TextView>(R.id.cycleStatusText).setTextColor(
            Color.parseColor(if (isRegular) "#4CAF50" else "#E57373")
        )

        findViewById<TextView>(R.id.periodHistoryReport).text =
            if (logs.isEmpty()) {
                "No period records yet. Tap Period on Home or Calendar to add your start date, end date, mood, pain level, and notes."
            } else {
                logs.joinToString("\n\n") {
                    "${PeriodStore.formatDisplay(it.startDate)} - ${PeriodStore.formatDisplay(it.endDate)}\nDuration: ${it.durationDays} days   Mood: ${it.mood}   Pain: ${it.painLevel}\nNotes: ${it.notes.ifEmpty { "No special notes" }}"
                }
            }
    }

    private fun setupNavigation() {
        val bottomNav = findViewById<BottomNavigationView>(R.id.bottomNav)
        bottomNav.selectedItemId = R.id.nav_reports
        bottomNav.setOnItemSelectedListener { item ->
            when (item.itemId) {
                R.id.nav_home -> { startActivity(Intent(this, MainActivity::class.java)); true }
                R.id.nav_calendar -> { startActivity(Intent(this, CalendarActivity::class.java)); true }
                R.id.nav_add -> { startActivity(Intent(this, AddDataHubActivity::class.java)); true }
                R.id.nav_reports -> { startActivity(Intent(this, ReportHubActivity::class.java)); true }
                R.id.nav_settings -> { startActivity(Intent(this, SettingsActivity::class.java)); true }
                else -> false
            }
        }
    }
}

```

### XML Layout Code

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.coordinatorlayout.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#F0EEF8">

    <androidx.core.widget.NestedScrollView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:clipToPadding="false"
        android:paddingBottom="80dp">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="20dp">

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="8dp"
                android:gravity="center_vertical"
                android:orientation="horizontal">

                <TextView
                    android:id="@+id/backBtn"
                    android:layout_width="44dp"
                    android:layout_height="44dp"
                    android:gravity="center"
                    android:text="&lt;"
                    android:textColor="#2D2D2D"
                    android:textSize="24sp" />

                <TextView
                    android:layout_width="0dp"
                    android:layout_height="wrap_content"
                    android:layout_weight="1"
                    android:text="Period Report"
                    android:textColor="#2D2D2D"
                    android:textSize="24sp"
                    android:textStyle="bold" />
            </LinearLayout>

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_marginBottom="20dp"
                android:text="Previous months, cycle insight, mood, pain level and notes"
                android:textColor="#888888"
                android:textSize="14sp" />

            <androidx.cardview.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="150dp"
                android:layout_marginBottom="16dp"
                app:cardCornerRadius="18dp"
                app:cardElevation="0dp">

                <FrameLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent">

                    <ImageView
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:scaleType="centerCrop"
                        android:src="@drawable/period_report_image" />

                    <View
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:background="@drawable/gradient_overlay_dark" />

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_gravity="bottom|start"
                        android:layout_margin="18dp"
                        android:text="Monthly cycle insight"
                        android:textColor="#FFFFFF"
                        android:textSize="20sp"
                        android:textStyle="bold" />
                </FrameLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="16dp"
                app:cardBackgroundColor="#FFFFFF"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">

                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:orientation="vertical"
                    android:padding="16dp">

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_marginBottom="10dp"
                        android:text="Cycle Insight Report"
                        android:textColor="#2D2D2D"
                        android:textSize="18sp"
                        android:textStyle="bold" />

                    <TextView
                        android:id="@+id/cycleStatusText"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:layout_marginBottom="10dp"
                        android:text="Add another period month to calculate regularity"
                        android:textColor="#4CAF50"
                        android:textSize="14sp"
                        android:textStyle="bold" />

                    <LinearLayout
                        android:layout_width="match_parent"
                        android:layout_height="12dp"
                        android:layout_marginBottom="8dp"
                        android:orientation="horizontal">

                        <View
                            android:layout_width="0dp"
                            android:layout_height="match_parent"
                            android:layout_weight="2"
                            android:background="#4CAF50" />

                        <View
                            android:layout_width="0dp"
                            android:layout_height="match_parent"
                            android:layout_weight="1"
                            android:background="#FFB74D" />

                        <View
                            android:layout_width="0dp"
                            android:layout_height="match_parent"
                            android:layout_weight="2"
                            android:background="#E0E0E0" />
                    </LinearLayout>

                    <LinearLayout
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:layout_marginBottom="16dp"
                        android:orientation="horizontal">

                        <TextView
                            android:layout_width="0dp"
                            android:layout_height="wrap_content"
                            android:layout_weight="1"
                            android:text="Regular\n21-35 days"
                            android:textColor="#555555"
                            android:textSize="12sp" />

                        <TextView
                            android:layout_width="0dp"
                            android:layout_height="wrap_content"
                            android:layout_weight="1"
                            android:gravity="end"
                            android:text="Irregular\n&lt; 21 or &gt; 35 days"
                            android:textColor="#E57373"
                            android:textSize="12sp" />
                    </LinearLayout>

                    <TextView
                        android:id="@+id/cycleInsightText"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:layout_marginBottom="16dp"
                        android:text="Add period history to calculate your cycle insight."
                        android:textColor="#4CAF50"
                        android:textSize="14sp"
                        android:textStyle="bold" />

                    <LinearLayout
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:layout_marginBottom="12dp"
                        android:orientation="horizontal">

                        <LinearLayout
                            android:layout_width="0dp"
                            android:layout_height="wrap_content"
                            android:layout_weight="1"
                            android:orientation="vertical">

                            <TextView
                                android:layout_width="wrap_content"
                                android:layout_height="wrap_content"
                                android:text="Cycle Length"
                                android:textColor="#888888"
                                android:textSize="12sp" />

                            <TextView
                                android:id="@+id/reportCycleLength"
                                android:layout_width="wrap_content"
                                android:layout_height="wrap_content"
                                android:text="-- days"
                                android:textColor="#2D2D2D"
                                android:textSize="16sp"
                                android:textStyle="bold" />
                        </LinearLayout>

                        <LinearLayout
                            android:layout_width="0dp"
                            android:layout_height="wrap_content"
                            android:layout_weight="1"
                            android:orientation="vertical">

                            <TextView
                                android:layout_width="wrap_content"
                                android:layout_height="wrap_content"
                                android:text="Last Duration"
                                android:textColor="#888888"
                                android:textSize="12sp" />

                            <TextView
                                android:id="@+id/reportPeriodDuration"
                                android:layout_width="wrap_content"
                                android:layout_height="wrap_content"
                                android:text="-- days"
                                android:textColor="#E57373"
                                android:textSize="16sp"
                                android:textStyle="bold" />
                        </LinearLayout>
                    </LinearLayout>

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="Next Possible Period"
                        android:textColor="#888888"
                        android:textSize="12sp" />

                    <TextView
                        android:id="@+id/reportNextPeriod"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:layout_marginBottom="16dp"
                        android:text="Add period history"
                        android:textColor="#8B7BB5"
                        android:textSize="16sp"
                        android:textStyle="bold" />

                    <TextView
                        android:id="@+id/periodHistoryReport"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:lineSpacingExtra="4dp"
                        android:text="No period records yet."
                        android:textColor="#555555"
                        android:textSize="14sp" />
                </LinearLayout>
            </androidx.cardview.widget.CardView>
        </LinearLayout>
    </androidx.core.widget.NestedScrollView>

    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bottomNav"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom"
        android:background="#FFFFFF"
        app:itemIconTint="@color/bottom_nav_color"
        app:itemTextColor="@color/bottom_nav_color"
        app:labelVisibilityMode="labeled"
        app:menu="@menu/bottom_nav_menu" />

</androidx.coordinatorlayout.widget.CoordinatorLayout>

```

---

## Meal Report

**Purpose:** Shows monthly meal tracking completion report.

**Activity File:** `MealReportActivity.kt`  
**Layout File:** `activity_meal_report.xml`

### Kotlin Activity Code

```kotlin
package com.example.pcostracker

import android.content.Intent
import android.os.Bundle
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import androidx.recyclerview.widget.LinearLayoutManager
import androidx.recyclerview.widget.RecyclerView
import com.google.android.material.bottomnavigation.BottomNavigationView

class MealReportActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_meal_report)
        bindReport()
        findViewById<TextView>(R.id.backBtn).setOnClickListener { finish() }
        setupNavigation()
    }

    override fun onResume() {
        super.onResume()
        bindReport()
    }

    private fun bindReport() {
        val completed = MealTrackingStore.completedCount(this)
        val percent = (completed * 100) / 90

        findViewById<TextView>(R.id.mealReportGoal).text = "$completed/90 meals"
        findViewById<TextView>(R.id.mealReportPercent).text = "$percent/100"

        val dayLetters = listOf("M", "T", "W", "T", "F", "S", "S")
        val weeks = (0..3).map { week ->
            (0..6).map { day ->
                MonthlyDayStatus(
                    dayLetter = dayLetters[day],
                    weekNumber = week + 1,
                    status = MealTrackingStore.dayStatus(this, week * 7 + day)
                )
            }
        }
        findViewById<RecyclerView>(R.id.mealReportGrid).apply {
            layoutManager = LinearLayoutManager(this@MealReportActivity)
            adapter = MonthlyCalendarAdapter(weeks) { dayIndex ->
                startActivity(
                    Intent(this@MealReportActivity, MealPlanActivity::class.java)
                        .putExtra("mealTab", "daily")
                        .putExtra("dayIndex", dayIndex)
                )
            }
        }
    }

    private fun setupNavigation() {
        val bottomNav = findViewById<BottomNavigationView>(R.id.bottomNav)
        bottomNav.selectedItemId = R.id.nav_reports
        bottomNav.setOnItemSelectedListener { item ->
            when (item.itemId) {
                R.id.nav_home -> { startActivity(Intent(this, MainActivity::class.java)); true }
                R.id.nav_calendar -> { startActivity(Intent(this, CalendarActivity::class.java)); true }
                R.id.nav_add -> { startActivity(Intent(this, AddDataHubActivity::class.java)); true }
                R.id.nav_reports -> { startActivity(Intent(this, ReportHubActivity::class.java)); true }
                R.id.nav_settings -> { startActivity(Intent(this, SettingsActivity::class.java)); true }
                else -> false
            }
        }
    }
}

```

### XML Layout Code

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.coordinatorlayout.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#F0EEF8">

    <androidx.core.widget.NestedScrollView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:clipToPadding="false"
        android:paddingBottom="80dp">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="20dp">

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="6dp"
                android:gravity="center_vertical"
                android:orientation="horizontal">

                <TextView
                    android:id="@+id/backBtn"
                    android:layout_width="44dp"
                    android:layout_height="44dp"
                    android:gravity="center"
                    android:text="&lt;"
                    android:textColor="#2D2D2D"
                    android:textSize="24sp" />

                <TextView
                    android:layout_width="0dp"
                    android:layout_height="wrap_content"
                    android:layout_weight="1"
                    android:text="Meal Report"
                    android:textColor="#2D2D2D"
                    android:textSize="24sp"
                    android:textStyle="bold"/>
            </LinearLayout>

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_marginBottom="16dp"
                android:text="Monthly meal tracking"
                android:textColor="#888888"
                android:textSize="14sp"/>

            <androidx.cardview.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="150dp"
                android:layout_marginBottom="16dp"
                app:cardCornerRadius="18dp"
                app:cardElevation="0dp">

                <FrameLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent">

                    <ImageView
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:scaleType="centerCrop"
                        android:src="@drawable/meal_tracking_image" />

                    <View
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:background="@drawable/gradient_overlay_dark" />

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_gravity="bottom|start"
                        android:layout_margin="18dp"
                        android:text="Monthly meal report"
                        android:textColor="#FFFFFF"
                        android:textSize="20sp"
                        android:textStyle="bold" />
                </FrameLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                app:cardBackgroundColor="#FFFFFF"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">

                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:orientation="vertical"
                    android:padding="16dp">

                    <TextView
                        android:id="@+id/mealReportGoal"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:text="0/90 meals"
                        android:textColor="#009688"
                        android:textSize="22sp"
                        android:textStyle="bold"/>

                    <TextView
                        android:id="@+id/mealReportPercent"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:layout_marginBottom="12dp"
                        android:text="0/100"
                        android:textColor="#2D2D2D"
                        android:textSize="16sp"
                        android:textStyle="bold"/>

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_marginBottom="8dp"
                        android:text="Monthly Meal Tracking Report"
                        android:textColor="#2D2D2D"
                        android:textSize="17sp"
                        android:textStyle="bold"/>

                    <androidx.recyclerview.widget.RecyclerView
                        android:id="@+id/mealReportGrid"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:nestedScrollingEnabled="false"/>
                </LinearLayout>
            </androidx.cardview.widget.CardView>
        </LinearLayout>
    </androidx.core.widget.NestedScrollView>

    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bottomNav"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom"
        android:background="#FFFFFF"
        app:itemIconTint="@color/bottom_nav_color"
        app:itemTextColor="@color/bottom_nav_color"
        app:labelVisibilityMode="labeled"
        app:menu="@menu/bottom_nav_menu"/>
</androidx.coordinatorlayout.widget.CoordinatorLayout>

```

---

## Final Report

**Purpose:** Combines monthly period, meal, and workout report summaries.

**Activity File:** `FinalReportActivity.kt`  
**Layout File:** `activity_final_report.xml`

### Kotlin Activity Code

```kotlin
package com.example.pcostracker

import android.content.Intent
import android.os.Bundle
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import com.google.android.material.bottomnavigation.BottomNavigationView

class FinalReportActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_final_report)
        bindReport()
        findViewById<TextView>(R.id.backBtn).setOnClickListener { finish() }
        setupNavigation()
    }

    override fun onResume() {
        super.onResume()
        bindReport()
    }

    private fun bindReport() {
        val periodLogs = PeriodStore.getLogs(this)
        val latestPeriod = periodLogs.firstOrNull()
        val cycleInsight = PeriodStore.insight(periodLogs)
        val averageCycle = PeriodStore.averageCycleLength(periodLogs)?.toString() ?: "--"

        findViewById<TextView>(R.id.finalPeriodSummary).text =
            if (latestPeriod == null) {
                "No period records yet."
            } else {
                "Latest: ${PeriodStore.formatDisplay(latestPeriod.startDate)} - ${PeriodStore.formatDisplay(latestPeriod.endDate)}\nDuration: ${latestPeriod.durationDays} days\nMood: ${latestPeriod.mood}   Pain: ${latestPeriod.painLevel}\nCycle length: $averageCycle days\n$cycleInsight"
            }

        val completedMeals = MealTrackingStore.completedCount(this)
        val mealPercent = (completedMeals * 100) / 90
        findViewById<TextView>(R.id.finalMealSummary).text =
            "Monthly meals: $completedMeals/90 ($mealPercent/100)"

        val days = WorkoutTrackingStore.daysInCurrentMonth()
        val meditation = WorkoutTrackingStore.monthCount(this, "Meditation")
        val yoga = WorkoutTrackingStore.monthCount(this, "Yoga")
        val exercise = WorkoutTrackingStore.monthCount(this, "Exercises")
        val workoutPercent = WorkoutTrackingStore.totalPercent(this)
        findViewById<TextView>(R.id.finalWorkoutSummary).text =
            "Meditation: $meditation/$days\nYoga: $yoga/$days\nExercise: $exercise/$days\nTotal: $workoutPercent/100"
    }

    private fun setupNavigation() {
        val bottomNav = findViewById<BottomNavigationView>(R.id.bottomNav)
        bottomNav.selectedItemId = R.id.nav_reports
        bottomNav.setOnItemSelectedListener { item ->
            when (item.itemId) {
                R.id.nav_home -> { startActivity(Intent(this, MainActivity::class.java)); true }
                R.id.nav_calendar -> { startActivity(Intent(this, CalendarActivity::class.java)); true }
                R.id.nav_add -> { startActivity(Intent(this, AddDataHubActivity::class.java)); true }
                R.id.nav_reports -> { startActivity(Intent(this, ReportHubActivity::class.java)); true }
                R.id.nav_settings -> { startActivity(Intent(this, SettingsActivity::class.java)); true }
                else -> false
            }
        }
    }
}

```

### XML Layout Code

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.coordinatorlayout.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#F0EEF8">

    <androidx.core.widget.NestedScrollView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:clipToPadding="false"
        android:paddingBottom="80dp">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="20dp">

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="6dp"
                android:gravity="center_vertical"
                android:orientation="horizontal">

                <TextView
                    android:id="@+id/backBtn"
                    android:layout_width="44dp"
                    android:layout_height="44dp"
                    android:gravity="center"
                    android:text="&lt;"
                    android:textColor="#2D2D2D"
                    android:textSize="24sp" />

                <TextView
                    android:layout_width="0dp"
                    android:layout_height="wrap_content"
                    android:layout_weight="1"
                    android:text="Final Report"
                    android:textColor="#2D2D2D"
                    android:textSize="24sp"
                    android:textStyle="bold"/>
            </LinearLayout>

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_marginBottom="16dp"
                android:text="Monthly period, meal and workout updates"
                android:textColor="#888888"
                android:textSize="14sp"/>

            <androidx.cardview.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="150dp"
                android:layout_marginBottom="16dp"
                app:cardBackgroundColor="#FFFFFF"
                app:cardCornerRadius="18dp"
                app:cardElevation="0dp">

                <FrameLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent">

                    <ImageView
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:padding="18dp"
                        android:scaleType="centerInside"
                        android:src="@drawable/report_checklist_image" />

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_gravity="bottom|start"
                        android:layout_margin="18dp"
                        android:text="Monthly health summary"
                        android:textColor="#2D2D2D"
                        android:textSize="20sp"
                        android:textStyle="bold" />
                </FrameLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="14dp"
                app:cardBackgroundColor="#FFFFFF"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">

                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:orientation="vertical"
                    android:padding="16dp">

                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:layout_marginBottom="8dp"
                        android:text="Monthly Period Report"
                        android:textColor="#2D2D2D"
                        android:textSize="18sp"
                        android:textStyle="bold"/>

                    <TextView
                        android:id="@+id/finalPeriodSummary"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:lineSpacingExtra="4dp"
                        android:text="No period records yet."
                        android:textColor="#555555"
                        android:textSize="14sp"/>
                </LinearLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="14dp"
                app:cardBackgroundColor="#FFFFFF"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">

                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:orientation="vertical"
                    android:padding="16dp">

                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:layout_marginBottom="8dp"
                        android:text="Monthly Meal Report"
                        android:textColor="#2D2D2D"
                        android:textSize="18sp"
                        android:textStyle="bold"/>

                    <TextView
                        android:id="@+id/finalMealSummary"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:lineSpacingExtra="4dp"
                        android:text="Monthly meals: 0/90"
                        android:textColor="#555555"
                        android:textSize="14sp"/>
                </LinearLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                app:cardBackgroundColor="#FFFFFF"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">

                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:orientation="vertical"
                    android:padding="16dp">

                    <TextView
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:layout_marginBottom="8dp"
                        android:text="Monthly Workout Report"
                        android:textColor="#2D2D2D"
                        android:textSize="18sp"
                        android:textStyle="bold"/>

                    <TextView
                        android:id="@+id/finalWorkoutSummary"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:lineSpacingExtra="4dp"
                        android:text="Meditation: 0/31"
                        android:textColor="#555555"
                        android:textSize="14sp"/>
                </LinearLayout>
            </androidx.cardview.widget.CardView>
        </LinearLayout>
    </androidx.core.widget.NestedScrollView>

    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bottomNav"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom"
        android:background="#FFFFFF"
        app:itemIconTint="@color/bottom_nav_color"
        app:itemTextColor="@color/bottom_nav_color"
        app:labelVisibilityMode="labeled"
        app:menu="@menu/bottom_nav_menu"/>
</androidx.coordinatorlayout.widget.CoordinatorLayout>

```

---

