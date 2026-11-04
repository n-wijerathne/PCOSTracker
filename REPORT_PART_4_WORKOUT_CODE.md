# Part 4 - Workout and Wellness Screens

This section contains separate screen documentation and code for the requested report part.

## Workout & Wellness Hub

**Purpose:** Main workout menu for All, Meditation, Yoga, Exercise, and Guide.

**Activity File:** `WorkoutWellnessHubActivity.kt`  
**Layout File:** `activity_workout_wellness_hub.xml`

### Kotlin Activity Code

```kotlin
package com.example.pcostracker

import android.content.Intent
import android.os.Bundle
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import androidx.cardview.widget.CardView
import com.google.android.material.bottomnavigation.BottomNavigationView

class WorkoutWellnessHubActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_workout_wellness_hub)

        findViewById<TextView>(R.id.backBtn).setOnClickListener { finish() }
        findViewById<CardView>(R.id.cardAllWellness).setOnClickListener { openWellness("all") }
        findViewById<CardView>(R.id.cardMeditationWellness).setOnClickListener { openWellness("meditation") }
        findViewById<CardView>(R.id.cardYogaWellness).setOnClickListener { openWellness("yoga") }
        findViewById<CardView>(R.id.cardExerciseWellness).setOnClickListener { openWellness("exercise") }
        findViewById<CardView>(R.id.cardWellnessGuide).setOnClickListener {
            startActivity(Intent(this, WorkoutWellnessGuideActivity::class.java))
        }

        setupNavigation()
    }

    private fun openWellness(tab: String) {
        startActivity(Intent(this, WorkoutWellnessActivity::class.java).putExtra("workoutTab", tab))
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
                    android:text="Workout &amp; Wellness Tracking"
                    android:textColor="#2D2D2D"
                    android:textSize="23sp"
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
                        android:src="@drawable/workout_report_image" />

                    <View
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:background="@drawable/gradient_overlay_dark" />

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_gravity="bottom|start"
                        android:layout_margin="18dp"
                        android:text="Choose your wellness routine"
                        android:textColor="#FFFFFF"
                        android:textSize="20sp"
                        android:textStyle="bold" />
                </FrameLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:id="@+id/cardAllWellness"
                android:layout_width="match_parent"
                android:layout_height="96dp"
                android:layout_marginBottom="12dp"
                app:cardBackgroundColor="#FFFFFF"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">
                <LinearLayout android:layout_width="match_parent" android:layout_height="match_parent"
                    android:gravity="center_vertical" android:orientation="horizontal" android:padding="18dp">
                    <ImageView android:layout_width="46dp" android:layout_height="46dp"
                        android:background="@drawable/icon_bg_purple_light" android:padding="10dp"
                        android:src="@drawable/ic_add_circle" app:tint="#8B7BB5"/>
                    <TextView android:layout_width="0dp" android:layout_height="wrap_content"
                        android:layout_marginStart="14dp" android:layout_weight="1" android:text="All"
                        android:textColor="#2D2D2D" android:textSize="18sp" android:textStyle="bold"/>
                </LinearLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:id="@+id/cardMeditationWellness"
                android:layout_width="match_parent"
                android:layout_height="96dp"
                android:layout_marginBottom="12dp"
                app:cardBackgroundColor="#FFFFFF"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">
                <LinearLayout android:layout_width="match_parent" android:layout_height="match_parent"
                    android:gravity="center_vertical" android:orientation="horizontal" android:padding="18dp">
                    <ImageView android:layout_width="46dp" android:layout_height="46dp"
                        android:background="@drawable/icon_bg_purple_light" android:padding="10dp"
                        android:src="@drawable/ic_heart" app:tint="#8B7BB5"/>
                    <TextView android:layout_width="0dp" android:layout_height="wrap_content"
                        android:layout_marginStart="14dp" android:layout_weight="1" android:text="Meditation"
                        android:textColor="#2D2D2D" android:textSize="18sp" android:textStyle="bold"/>
                </LinearLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:id="@+id/cardYogaWellness"
                android:layout_width="match_parent"
                android:layout_height="96dp"
                android:layout_marginBottom="12dp"
                app:cardBackgroundColor="#FFFFFF"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">
                <LinearLayout android:layout_width="match_parent" android:layout_height="match_parent"
                    android:gravity="center_vertical" android:orientation="horizontal" android:padding="18dp">
                    <ImageView android:layout_width="46dp" android:layout_height="46dp"
                        android:background="@drawable/icon_bg_teal_light" android:padding="10dp"
                        android:src="@drawable/ic_splash_yoga" app:tint="#4DB6AC"/>
                    <TextView android:layout_width="0dp" android:layout_height="wrap_content"
                        android:layout_marginStart="14dp" android:layout_weight="1" android:text="Yoga"
                        android:textColor="#2D2D2D" android:textSize="18sp" android:textStyle="bold"/>
                </LinearLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:id="@+id/cardExerciseWellness"
                android:layout_width="match_parent"
                android:layout_height="96dp"
                android:layout_marginBottom="12dp"
                app:cardBackgroundColor="#FFFFFF"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">
                <LinearLayout android:layout_width="match_parent" android:layout_height="match_parent"
                    android:gravity="center_vertical" android:orientation="horizontal" android:padding="18dp">
                    <ImageView android:layout_width="46dp" android:layout_height="46dp"
                        android:background="@drawable/icon_bg_red_light" android:padding="10dp"
                        android:src="@drawable/ic_reports" app:tint="#E57373"/>
                    <TextView android:layout_width="0dp" android:layout_height="wrap_content"
                        android:layout_marginStart="14dp" android:layout_weight="1" android:text="Exercise"
                        android:textColor="#2D2D2D" android:textSize="18sp" android:textStyle="bold"/>
                </LinearLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:id="@+id/cardWellnessGuide"
                android:layout_width="match_parent"
                android:layout_height="96dp"
                app:cardBackgroundColor="#FFFFFF"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">
                <LinearLayout android:layout_width="match_parent" android:layout_height="match_parent"
                    android:gravity="center_vertical" android:orientation="horizontal" android:padding="18dp">
                    <ImageView android:layout_width="46dp" android:layout_height="46dp"
                        android:background="@drawable/icon_bg_teal_light" android:padding="10dp"
                        android:src="@drawable/ic_calendar" app:tint="#4DB6AC"/>
                    <TextView android:layout_width="0dp" android:layout_height="wrap_content"
                        android:layout_marginStart="14dp" android:layout_weight="1"
                        android:text="Workouts &amp; Wellness Guide"
                        android:textColor="#2D2D2D" android:textSize="18sp" android:textStyle="bold"/>
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

## Workout & Wellness

**Purpose:** Shows selected workout/yoga/meditation video image, completion time, and daily schedule.

**Activity File:** `WorkoutWellnessActivity.kt`  
**Layout File:** `activity_workout_wellness.xml`

### Kotlin Activity Code

```kotlin
package com.example.pcostracker

import android.content.Intent
import android.graphics.Color
import android.os.Bundle
import android.view.View
import android.widget.CheckBox
import android.widget.ImageView
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import androidx.recyclerview.widget.LinearLayoutManager
import androidx.recyclerview.widget.RecyclerView
import com.google.android.material.bottomnavigation.BottomNavigationView
import com.google.android.material.floatingactionbutton.FloatingActionButton

class WorkoutWellnessActivity : AppCompatActivity() {

    private lateinit var scheduleAdapter: ScheduleAdapter
    private var currentTab = "all"

    private val allWorkouts = listOf(
        WorkoutItem(
            id = "2",
            title = "Daily Exercise",
            category = "Exercises",
            startTime = "Daily",
            endTime = "Practice",
            durationMins = 30,
            calories = 180,
            level = "Intermediate",
            description = "A daily PCOS-friendly exercise practice with light cardio and bodyweight strength movements.",
            sequence = listOf(
                "5 min warm-up",
                "3 sets of 15 squats",
                "3 sets of 10 push-ups",
                "3 sets of 30 second planks",
                "5 min cool-down stretch"
            ),
            imageRes = R.drawable.exercise_video_image
        ),
        WorkoutItem(
            id = "3",
            title = "Daily Yoga",
            category = "Yoga",
            startTime = "Daily",
            endTime = "Practice",
            durationMins = 20,
            calories = 80,
            level = "Beginner",
            description = "A gentle daily yoga practice to improve flexibility and support hormonal balance.",
            sequence = listOf(
                "Cat-Cow pose - 10 breaths",
                "Child's pose - 1 minute",
                "Downward-facing dog - 30 seconds",
                "Warrior I - 30 seconds each side",
                "Seated forward fold - 1 minute",
                "Corpse pose - 2 minutes"
            ),
            imageRes = R.drawable.yoga_video_image
        ),
        WorkoutItem(
            id = "1",
            title = "Daily Meditation",
            category = "Meditation",
            startTime = "Daily",
            endTime = "Practice",
            durationMins = 10,
            calories = 30,
            level = "Beginner",
            description = "A daily calming meditation practice to support stress balance and wellness.",
            sequence = listOf(
                "Find a comfortable seated position",
                "Close your eyes and focus on breathing",
                "Inhale for 4 counts, hold for 4, exhale for 6",
                "Body scan from head to toe",
                "Set your intention for the day"
            ),
            imageRes = R.drawable.meditation_video_image
        )
    )

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_workout_wellness)

        setupScheduleList(allWorkouts)
        setupDailySchedule()
        setupTabs()
        setupNavigation()

        findViewById<TextView>(R.id.backBtn).setOnClickListener { finish() }
        findViewById<FloatingActionButton>(R.id.fabAdd).setOnClickListener {
            startActivity(Intent(this, AddDataHubActivity::class.java))
        }
    }

    private fun setupTabs() {
        val tabs = mapOf(
            "all" to findViewById<TextView>(R.id.tabAll),
            "exercise" to findViewById(R.id.tabExercises),
            "yoga" to findViewById(R.id.tabYoga),
            "meditation" to findViewById(R.id.tabMeditation)
        )

        fun selectTab(key: String) {
            currentTab = key
            tabs.forEach { (k, v) ->
                v.setBackgroundResource(if (k == key) R.drawable.tab_selected_purple else 0)
                v.setTextColor(if (k == key) Color.WHITE else Color.parseColor("#888888"))
            }

            val filtered = when (key) {
                "all" -> allWorkouts
                "exercise", "exercises" -> allWorkouts.filter { it.category == "Exercises" }
                "yoga" -> allWorkouts.filter { it.category == "Yoga" }
                "meditation" -> allWorkouts.filter { it.category == "Meditation" }
                else -> allWorkouts
            }
            findViewById<androidx.cardview.widget.CardView>(R.id.dailyScheduleCard).visibility =
                if (key == "all") View.GONE else View.VISIBLE

            scheduleAdapter = ScheduleAdapter(filtered) { workout ->
                val intent = Intent(this, WorkoutDetailActivity::class.java)
                intent.putExtra("workoutId", workout.id)
                startActivity(intent)
            }
            findViewById<RecyclerView>(R.id.scheduleList).adapter = scheduleAdapter

            // Update featured card
            if (filtered.isNotEmpty()) updateFeaturedCard(filtered.first())
        }

        tabs.forEach { (key, view) -> view.setOnClickListener { selectTab(key) } }
        selectTab(intent.getStringExtra("workoutTab") ?: "all")
    }

    private fun setupDailySchedule() {
        findViewById<CheckBox>(R.id.scheduleDone).setOnCheckedChangeListener { _, isChecked ->
            val category = when (currentTab) {
                "meditation" -> "Meditation"
                "yoga" -> "Yoga"
                "exercise", "exercises" -> "Exercises"
                else -> null
            }
            if (category == null) {
                WorkoutTrackingStore.categories.forEach {
                    WorkoutTrackingStore.setDone(this, it, isChecked)
                }
            } else {
                WorkoutTrackingStore.setDone(this, category, isChecked)
            }
        }
    }

    private fun setupScheduleList(workouts: List<WorkoutItem>) {
        scheduleAdapter = ScheduleAdapter(workouts) { workout ->
            val intent = Intent(this, WorkoutDetailActivity::class.java)
            intent.putExtra("workoutId", workout.id)
            startActivity(intent)
        }
        val recyclerView = findViewById<RecyclerView>(R.id.scheduleList)
        recyclerView.layoutManager = LinearLayoutManager(this)
        recyclerView.adapter = scheduleAdapter
    }

    private fun updateFeaturedCard(workout: WorkoutItem) {
        findViewById<ImageView>(R.id.featuredImage).setImageResource(workout.imageRes)
        findViewById<TextView>(R.id.featuredTitle).text = workout.title
        findViewById<TextView>(R.id.featuredDuration).text = "${workout.durationMins} min"
        findViewById<TextView>(R.id.featuredCategory).text = workout.category
        findViewById<TextView>(R.id.scheduleType).text = "Completion Time"
        findViewById<TextView>(R.id.scheduleTime).text = "${workout.durationMins} min"
        findViewById<CheckBox>(R.id.scheduleDone).apply {
            setOnCheckedChangeListener(null)
            isChecked = WorkoutTrackingStore.isDone(this@WorkoutWellnessActivity, workout.category)
            setOnCheckedChangeListener { _, isChecked ->
                WorkoutTrackingStore.setDone(this@WorkoutWellnessActivity, workout.category, isChecked)
            }
        }

        findViewById<TextView>(R.id.featuredPlayBtn).setOnClickListener {
            val intent = Intent(this, WorkoutDetailActivity::class.java)
            intent.putExtra("workoutId", workout.id)
            startActivity(intent)
        }
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
        android:paddingBottom="80dp"
        android:clipToPadding="false">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="20dp">

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:gravity="center_vertical"
                android:orientation="horizontal">

                <TextView
                    android:id="@+id/backBtn"
                    android:layout_width="44dp"
                    android:layout_height="44dp"
                    android:gravity="center"
                    android:text="&lt;"
                    android:textColor="#2D2D2D"
                    android:textSize="24sp"/>

                <TextView
                    android:layout_width="0dp"
                    android:layout_height="wrap_content"
                    android:layout_weight="1"
                    android:text="Workout &amp; Wellness"
                    android:textSize="24sp"
                    android:textStyle="bold"
                    android:textColor="#2D2D2D"/>
            </LinearLayout>

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Stay active and balanced"
                android:textSize="13sp"
                android:textColor="#AAAAAA"
                android:layout_marginBottom="20dp"/>

            <!-- Category Tabs -->
            <androidx.cardview.widget.CardView
                android:id="@+id/categoryTabsCard"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="20dp"
                android:visibility="gone"
                app:cardCornerRadius="24dp"
                app:cardElevation="0dp"
                app:cardBackgroundColor="#FFFFFF">

                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:orientation="horizontal"
                    android:padding="4dp">

                    <TextView
                        android:id="@+id/tabAll"
                        android:layout_width="0dp"
                        android:layout_height="36dp"
                        android:layout_weight="1"
                        android:text="All"
                        android:gravity="center"
                        android:textSize="13sp"
                        android:textColor="#FFFFFF"
                        android:background="@drawable/tab_selected_purple"/>

                    <TextView
                        android:id="@+id/tabExercises"
                        android:layout_width="0dp"
                        android:layout_height="36dp"
                        android:layout_weight="1"
                        android:text="Exercises"
                        android:gravity="center"
                        android:textSize="13sp"
                        android:textColor="#888888"/>

                    <TextView
                        android:id="@+id/tabYoga"
                        android:layout_width="0dp"
                        android:layout_height="36dp"
                        android:layout_weight="1"
                        android:text="Yoga"
                        android:gravity="center"
                        android:textSize="13sp"
                        android:textColor="#888888"/>

                    <TextView
                        android:id="@+id/tabMeditation"
                        android:layout_width="0dp"
                        android:layout_height="36dp"
                        android:layout_weight="1"
                        android:text="Meditation"
                        android:gravity="center"
                        android:textSize="13sp"
                        android:textColor="#888888"/>

                </LinearLayout>
            </androidx.cardview.widget.CardView>

            <!-- Featured / Daily Pick Banner -->
            <androidx.cardview.widget.CardView
                android:id="@+id/featuredCard"
                android:layout_width="match_parent"
                android:layout_height="200dp"
                android:layout_marginBottom="24dp"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">

                <FrameLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent">

                    <ImageView
                        android:id="@+id/featuredImage"
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:scaleType="centerCrop"
                        android:src="@drawable/bg_yoga"/>

                    <!-- Dark overlay -->
                    <View
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:background="@drawable/gradient_overlay"/>

                    <!-- Play button -->
                    <TextView
                        android:id="@+id/featuredPlayBtn"
                        android:layout_width="48dp"
                        android:layout_height="48dp"
                        android:text="▶"
                        android:textSize="18sp"
                        android:textColor="#FFFFFF"
                        android:gravity="center"
                        android:background="@drawable/play_button_bg"
                        android:layout_gravity="center"/>

                    <!-- Info overlay -->
                    <LinearLayout
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:orientation="vertical"
                        android:layout_gravity="bottom"
                        android:padding="16dp">

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="DAILY PICK"
                            android:textSize="10sp"
                            android:textColor="#FFFFFF"
                            android:textStyle="bold"
                            android:background="@drawable/daily_pick_badge"
                            android:paddingStart="8dp"
                            android:paddingEnd="8dp"
                            android:paddingTop="3dp"
                            android:paddingBottom="3dp"
                            android:layout_marginBottom="6dp"/>

                        <TextView
                            android:id="@+id/featuredTitle"
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="Daily Exercise"
                            android:textSize="20sp"
                            android:textStyle="bold"
                            android:textColor="#FFFFFF"/>

                        <LinearLayout
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:orientation="horizontal"
                            android:gravity="center_vertical">

                            <TextView
                                android:layout_width="wrap_content"
                                android:layout_height="wrap_content"
                                android:text="⏱ "
                                android:textSize="11sp"
                                android:textColor="#DDDDDD"/>

                            <TextView
                                android:id="@+id/featuredDuration"
                                android:layout_width="wrap_content"
                                android:layout_height="wrap_content"
                                android:text="20 min"
                                android:textSize="12sp"
                                android:textColor="#DDDDDD"/>

                            <TextView
                                android:layout_width="wrap_content"
                                android:layout_height="wrap_content"
                                android:text="  •  "
                                android:textSize="12sp"
                                android:textColor="#DDDDDD"/>

                            <TextView
                                android:id="@+id/featuredCategory"
                                android:layout_width="wrap_content"
                                android:layout_height="wrap_content"
                                android:text="Yoga"
                                android:textSize="12sp"
                                android:textColor="#DDDDDD"/>

                        </LinearLayout>
                    </LinearLayout>

                </FrameLayout>
            </androidx.cardview.widget.CardView>

            <!-- Daily Schedule Header -->
            <androidx.constraintlayout.widget.ConstraintLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="12dp">

                <TextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="Daily Wellness Schedule"
                    android:textSize="18sp"
                    android:textStyle="bold"
                    android:textColor="#2D2D2D"
                    app:layout_constraintStart_toStartOf="parent"
                    app:layout_constraintTop_toTopOf="parent"/>

                <TextView
                    android:id="@+id/seeAll"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text=""
                    android:visibility="gone"
                    android:textSize="13sp"
                    android:textColor="#8B7BB5"
                    app:layout_constraintEnd_toEndOf="parent"
                    app:layout_constraintTop_toTopOf="parent"/>

            </androidx.constraintlayout.widget.ConstraintLayout>

            <androidx.cardview.widget.CardView
                android:id="@+id/dailyScheduleCard"
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

                    <LinearLayout
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:gravity="center_vertical"
                        android:orientation="horizontal">

                        <LinearLayout
                            android:layout_width="0dp"
                            android:layout_height="wrap_content"
                            android:layout_weight="1"
                            android:orientation="vertical">

                            <TextView
                                android:id="@+id/scheduleType"
                                android:layout_width="match_parent"
                                android:layout_height="wrap_content"
                                android:text="Completion Time"
                                android:textColor="#2D2D2D"
                                android:textSize="16sp"
                                android:textStyle="bold"/>

                            <TextView
                                android:id="@+id/scheduleTime"
                                android:layout_width="match_parent"
                                android:layout_height="wrap_content"
                                android:layout_marginTop="6dp"
                                android:text="30 min"
                                android:textColor="#8B7BB5"
                                android:textSize="24sp"
                                android:textStyle="bold"/>
                        </LinearLayout>

                        <CheckBox
                            android:id="@+id/scheduleDone"
                            android:layout_width="wrap_content"
                            android:layout_height="48dp"
                            android:text="Done"
                            android:textSize="14sp"/>
                    </LinearLayout>
                </LinearLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:id="@+id/weeklyReportCard"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="16dp"
                android:visibility="gone"
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
                        android:text="Weekly Workout &amp; Wellness Report"
                        android:textSize="17sp"
                        android:textStyle="bold"
                        android:textColor="#2D2D2D"
                        android:layout_marginBottom="12dp"/>

                    <LinearLayout
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:orientation="horizontal"
                        android:layout_marginBottom="12dp">

                        <TextView
                            android:id="@+id/meditationWeeklyCount"
                            android:layout_width="0dp"
                            android:layout_height="54dp"
                            android:layout_weight="1"
                            android:gravity="center"
                            android:background="@drawable/icon_bg_purple_light"
                            android:text="0/7"
                            android:textColor="#8B7BB5"
                            android:textStyle="bold"
                            android:textSize="18sp"/>

                        <TextView
                            android:id="@+id/yogaWeeklyCount"
                            android:layout_width="0dp"
                            android:layout_height="54dp"
                            android:layout_marginStart="8dp"
                            android:layout_weight="1"
                            android:gravity="center"
                            android:background="@drawable/icon_bg_teal_light"
                            android:text="0/7"
                            android:textColor="#4DB6AC"
                            android:textStyle="bold"
                            android:textSize="18sp"/>

                        <TextView
                            android:id="@+id/exerciseWeeklyCount"
                            android:layout_width="0dp"
                            android:layout_height="54dp"
                            android:layout_marginStart="8dp"
                            android:layout_weight="1"
                            android:gravity="center"
                            android:background="@drawable/icon_bg_red_light"
                            android:text="0/7"
                            android:textColor="#E57373"
                            android:textStyle="bold"
                            android:textSize="18sp"/>
                    </LinearLayout>

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="Meditation    Yoga    Exercise"
                        android:textColor="#888888"
                        android:textSize="12sp"
                        android:layout_marginBottom="8dp"/>

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="Weekly Percentage"
                        android:textColor="#888888"
                        android:textSize="12sp"/>

                    <TextView
                        android:id="@+id/workoutWeeklyPercent"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="0/100"
                        android:textColor="#2D2D2D"
                        android:textSize="24sp"
                        android:textStyle="bold"/>
                </LinearLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:id="@+id/monthlyReportCard"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="16dp"
                android:visibility="gone"
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
                        android:text="Monthly Workout &amp; Wellness Report"
                        android:textSize="17sp"
                        android:textStyle="bold"
                        android:textColor="#2D2D2D"
                        android:layout_marginBottom="12dp"/>

                    <LinearLayout
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:orientation="horizontal"
                        android:layout_marginBottom="12dp">

                        <LinearLayout
                            android:layout_width="0dp"
                            android:layout_height="74dp"
                            android:layout_weight="1"
                            android:gravity="center"
                            android:orientation="vertical"
                            android:background="@drawable/icon_bg_purple_light">

                            <TextView
                                android:layout_width="wrap_content"
                                android:layout_height="wrap_content"
                                android:text="Meditation"
                                android:textSize="12sp"/>

                            <TextView
                                android:id="@+id/meditationMonthlyCount"
                                android:layout_width="wrap_content"
                                android:layout_height="wrap_content"
                                android:text="0/31"
                                android:textStyle="bold"
                                android:textSize="18sp"
                                android:textColor="#8B7BB5"/>
                        </LinearLayout>

                        <LinearLayout
                            android:layout_width="0dp"
                            android:layout_height="74dp"
                            android:layout_marginStart="8dp"
                            android:layout_weight="1"
                            android:gravity="center"
                            android:orientation="vertical"
                            android:background="@drawable/icon_bg_teal_light">

                            <TextView
                                android:layout_width="wrap_content"
                                android:layout_height="wrap_content"
                                android:text="Yoga"
                                android:textSize="12sp"/>

                            <TextView
                                android:id="@+id/yogaMonthlyCount"
                                android:layout_width="wrap_content"
                                android:layout_height="wrap_content"
                                android:text="0/31"
                                android:textStyle="bold"
                                android:textSize="18sp"
                                android:textColor="#4DB6AC"/>
                        </LinearLayout>

                        <LinearLayout
                            android:layout_width="0dp"
                            android:layout_height="74dp"
                            android:layout_marginStart="8dp"
                            android:layout_weight="1"
                            android:gravity="center"
                            android:orientation="vertical"
                            android:background="@drawable/icon_bg_red_light">

                            <TextView
                                android:layout_width="wrap_content"
                                android:layout_height="wrap_content"
                                android:text="Exercise"
                                android:textSize="12sp"/>

                            <TextView
                                android:id="@+id/exerciseMonthlyCount"
                                android:layout_width="wrap_content"
                                android:layout_height="wrap_content"
                                android:text="0/31"
                                android:textStyle="bold"
                                android:textSize="18sp"
                                android:textColor="#E57373"/>
                        </LinearLayout>
                    </LinearLayout>

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="Total Percentage"
                        android:textColor="#888888"
                        android:textSize="12sp"/>

                    <TextView
                        android:id="@+id/workoutMonthlyPercent"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="0/100"
                        android:textColor="#2D2D2D"
                        android:textSize="24sp"
                        android:textStyle="bold"/>
                </LinearLayout>
            </androidx.cardview.widget.CardView>

            <!-- Schedule List -->
            <androidx.recyclerview.widget.RecyclerView
                android:id="@+id/scheduleList"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:nestedScrollingEnabled="false"/>

        </LinearLayout>
    </androidx.core.widget.NestedScrollView>

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

## Workout Detail

**Purpose:** Shows workout details, image, duration, calories, description, and step sequence.

**Activity File:** `WorkoutDetailActivity.kt`  
**Layout File:** `activity_workout_detail.xml`

### Kotlin Activity Code

```kotlin
package com.example.pcostracker

import android.content.Intent
import android.os.Bundle
import android.widget.Button
import android.widget.ImageView
import android.widget.TextView
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity
import androidx.recyclerview.widget.LinearLayoutManager
import androidx.recyclerview.widget.RecyclerView
import com.google.android.material.bottomnavigation.BottomNavigationView
import com.google.android.material.floatingactionbutton.FloatingActionButton

class WorkoutDetailActivity : AppCompatActivity() {

    private val allWorkouts = listOf(
        WorkoutItem(
            "2",
            "Daily Exercise",
            "Exercises",
            "Daily",
            "Practice",
            30,
            180,
            "Intermediate",
            "A daily PCOS-friendly exercise practice with light cardio and bodyweight strength movements.",
            listOf(
                "5 min warm-up jogging in place",
                "3 sets of 15 squats",
                "3 sets of 10 push-ups",
                "3 sets of 30 second planks",
                "5 min cool-down stretch"
            ),
            R.drawable.exercise_video_image
        ),
        WorkoutItem(
            "3",
            "Daily Yoga",
            "Yoga",
            "Daily",
            "Practice",
            20,
            80,
            "Beginner",
            "A gentle daily yoga practice to improve flexibility and support hormonal balance.",
            listOf(
                "Cat-Cow pose - 10 breaths",
                "Child's pose - 1 minute",
                "Downward-facing dog - 30 seconds",
                "Warrior I - 30 seconds each side",
                "Seated forward fold - 1 minute",
                "Corpse pose - 2 minutes"
            ),
            R.drawable.yoga_video_image
        ),
        WorkoutItem(
            "1",
            "Daily Meditation",
            "Meditation",
            "Daily",
            "Practice",
            10,
            30,
            "Beginner",
            "A daily calming meditation practice to support stress balance and wellness.",
            listOf(
                "Find a comfortable seated position",
                "Close your eyes and focus on breathing",
                "Inhale for 4 counts, hold for 4, exhale for 6",
                "Body scan from head to toe",
                "Set your intention for the day"
            ),
            R.drawable.meditation_video_image
        )
    )

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_workout_detail)

        val workoutId = intent.getStringExtra("workoutId") ?: "2"
        val workout = allWorkouts.find { it.id == workoutId } ?: allWorkouts.first()

        bindWorkout(workout)
        setupNavigation()

        findViewById<TextView>(R.id.backBtn).setOnClickListener { finish() }

        findViewById<Button>(R.id.startWorkoutBtn).setOnClickListener {
            Toast.makeText(this, "Starting ${workout.title}!", Toast.LENGTH_SHORT).show()
        }

        var isFav = false
        val favBtn = findViewById<TextView>(R.id.favouriteBtn)
        favBtn.setOnClickListener {
            isFav = !isFav
            favBtn.text = if (isFav) "♥" else "♡"
            Toast.makeText(
                this,
                if (isFav) "Added to favourites" else "Removed from favourites",
                Toast.LENGTH_SHORT
            ).show()
        }

        findViewById<FloatingActionButton>(R.id.fabAdd).setOnClickListener {
            startActivity(Intent(this, AddDataHubActivity::class.java))
        }
    }

    private fun bindWorkout(workout: WorkoutItem) {
        findViewById<ImageView>(R.id.detailHeroImage).setImageResource(workout.imageRes)
        findViewById<TextView>(R.id.detailCategory).text = workout.category.uppercase()
        findViewById<TextView>(R.id.detailLevel).text = workout.level
        findViewById<TextView>(R.id.detailTitle).text = workout.title
        findViewById<TextView>(R.id.detailDuration).text = "${workout.durationMins} min"
        findViewById<TextView>(R.id.detailCalories).text = "${workout.calories} kcal"
        findViewById<TextView>(R.id.detailDescription).text = workout.description

        val recyclerView = findViewById<RecyclerView>(R.id.sequenceList)
        recyclerView.layoutManager = LinearLayoutManager(this)
        recyclerView.adapter = SequenceAdapter(workout.sequence)
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
        android:paddingBottom="80dp"
        android:clipToPadding="false">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical">

            <!-- Hero Image -->
            <FrameLayout
                android:layout_width="match_parent"
                android:layout_height="260dp">

                <ImageView
                    android:id="@+id/detailHeroImage"
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:scaleType="centerCrop"
                    android:src="@drawable/bg_exercise"/>

                <View
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:background="@drawable/gradient_overlay_dark"/>

                <!-- Back Button -->
                <TextView
                    android:id="@+id/backBtn"
                    android:layout_width="36dp"
                    android:layout_height="36dp"
                    android:text="‹"
                    android:textSize="22sp"
                    android:gravity="center"
                    android:textColor="#FFFFFF"
                    android:background="@drawable/back_btn_dark_bg"
                    android:layout_margin="16dp"/>

                <!-- Info overlaid on image -->
                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:orientation="vertical"
                    android:layout_gravity="bottom"
                    android:padding="20dp">

                    <LinearLayout
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:orientation="horizontal"
                        android:layout_marginBottom="8dp">

                        <TextView
                            android:id="@+id/detailCategory"
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="EXERCISES"
                            android:textSize="11sp"
                            android:textColor="#FFFFFF"
                            android:textStyle="bold"
                            android:background="@drawable/daily_pick_badge"
                            android:paddingStart="10dp"
                            android:paddingEnd="10dp"
                            android:paddingTop="4dp"
                            android:paddingBottom="4dp"
                            android:layout_marginEnd="8dp"/>

                        <TextView
                            android:id="@+id/detailLevel"
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="Intermediate"
                            android:textSize="11sp"
                            android:textColor="#FFFFFF"
                            android:background="@drawable/level_badge_bg"
                            android:paddingStart="10dp"
                            android:paddingEnd="10dp"
                            android:paddingTop="4dp"
                            android:paddingBottom="4dp"/>

                    </LinearLayout>

                    <TextView
                        android:id="@+id/detailTitle"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="Daily Exercise"
                        android:textSize="24sp"
                        android:textStyle="bold"
                        android:textColor="#FFFFFF"
                        android:layout_marginBottom="8dp"/>

                    <LinearLayout
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:orientation="horizontal">

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="⏱ "
                            android:textSize="13sp"
                            android:textColor="#DDDDDD"/>

                        <TextView
                            android:id="@+id/detailDuration"
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="30 min"
                            android:textSize="13sp"
                            android:textColor="#DDDDDD"
                            android:layout_marginEnd="16dp"/>

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="🔥 "
                            android:textSize="13sp"/>

                        <TextView
                            android:id="@+id/detailCalories"
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="180 kcal"
                            android:textSize="13sp"
                            android:textColor="#DDDDDD"/>

                    </LinearLayout>

                </LinearLayout>
            </FrameLayout>

            <!-- Content Card -->
            <androidx.cardview.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginTop="-16dp"
                app:cardCornerRadius="24dp"
                app:cardElevation="0dp"
                app:cardBackgroundColor="#F0EEF8">

                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:orientation="vertical"
                    android:padding="20dp">

                    <!-- Start Workout + Favourite Row -->
                    <LinearLayout
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:orientation="horizontal"
                        android:gravity="center_vertical"
                        android:layout_marginBottom="24dp">

                        <Button
                            android:id="@+id/startWorkoutBtn"
                            android:layout_width="0dp"
                            android:layout_height="52dp"
                            android:layout_weight="1"
                            android:text="▶  Start Workout"
                            android:textColor="#FFFFFF"
                            android:textSize="15sp"
                            android:background="@drawable/button_background"
                            android:layout_marginEnd="12dp"/>

                        <TextView
                            android:id="@+id/favouriteBtn"
                            android:layout_width="48dp"
                            android:layout_height="48dp"
                            android:text="♡"
                            android:textSize="20sp"
                            android:textColor="#8B7BB5"
                            android:gravity="center"
                            android:background="@drawable/favourite_btn_bg"/>

                    </LinearLayout>

                    <!-- Description -->
                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="Description"
                        android:textSize="18sp"
                        android:textStyle="bold"
                        android:textColor="#2D2D2D"
                        android:layout_marginBottom="8dp"/>

                    <TextView
                        android:id="@+id/detailDescription"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:text="A moderate intensity workout description."
                        android:textSize="14sp"
                        android:textColor="#666666"
                        android:lineSpacingExtra="4dp"
                        android:layout_marginBottom="24dp"/>

                    <!-- Sequence -->
                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="Sequence"
                        android:textSize="18sp"
                        android:textStyle="bold"
                        android:textColor="#2D2D2D"
                        android:layout_marginBottom="12dp"/>

                    <androidx.recyclerview.widget.RecyclerView
                        android:id="@+id/sequenceList"
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
        app:menu="@menu/bottom_nav_menu"
        app:itemIconTint="@color/bottom_nav_color"
        app:itemTextColor="@color/bottom_nav_color"
        app:labelVisibilityMode="labeled"/>

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

## Workout Guide

**Purpose:** Provides PCOS-friendly workout and wellness tips.

**Activity File:** `WorkoutWellnessGuideActivity.kt`  
**Layout File:** `activity_workout_wellness_guide.xml`

### Kotlin Activity Code

```kotlin
package com.example.pcostracker

import android.content.Intent
import android.os.Bundle
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import com.google.android.material.bottomnavigation.BottomNavigationView

class WorkoutWellnessGuideActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_workout_wellness_guide)

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
        android:paddingBottom="88dp">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="20dp">

            <LinearLayout android:layout_width="match_parent" android:layout_height="wrap_content"
                android:layout_marginBottom="18dp" android:gravity="center_vertical" android:orientation="horizontal">
                <TextView android:id="@+id/backBtn" android:layout_width="44dp" android:layout_height="44dp"
                    android:gravity="center" android:text="&lt;" android:textColor="#2D2D2D" android:textSize="24sp"/>
                <TextView android:layout_width="0dp" android:layout_height="wrap_content" android:layout_weight="1"
                    android:text="Workouts &amp; Wellness Guide" android:textColor="#2D2D2D"
                    android:textSize="22sp" android:textStyle="bold"/>
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
                        android:src="@drawable/workout_report_image" />

                    <View
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:background="@drawable/gradient_overlay_dark" />

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_gravity="bottom|start"
                        android:layout_margin="18dp"
                        android:text="PCOS-friendly movement"
                        android:textColor="#FFFFFF"
                        android:textSize="20sp"
                        android:textStyle="bold" />
                </FrameLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="14dp"
                app:cardBackgroundColor="#F0FBF7"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">
                <TextView android:layout_width="match_parent" android:layout_height="wrap_content"
                    android:lineSpacingExtra="5dp" android:padding="16dp"
                    android:text="🏃  Best Workouts for PCOS\nWalking - 30 mins daily\nYoga - reduces stress &amp; hormones\nStrength training - helps weight control\nCycling or dancing - improves fitness\nStretching - reduces body stress"
                    android:textColor="#444444" android:textSize="15sp"/>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                app:cardBackgroundColor="#F7F1FF"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">
                <TextView android:layout_width="match_parent" android:layout_height="wrap_content"
                    android:lineSpacingExtra="5dp" android:padding="16dp"
                    android:text="💜  Wellness Tips\nSleep 7-8 hours\nDrink more water\nReduce sugary/junk foods\nEat protein, fruits &amp; vegetables\nManage stress with meditation or breathing exercises"
                    android:textColor="#444444" android:textSize="15sp"/>
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

## Workout Report

**Purpose:** Shows weekly and monthly meditation, yoga, and exercise completion reports.

**Activity File:** `WorkoutWellnessReportActivity.kt`  
**Layout File:** `activity_workout_wellness_report.xml`

### Kotlin Activity Code

```kotlin
package com.example.pcostracker

import android.content.Intent
import android.os.Bundle
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import com.google.android.material.bottomnavigation.BottomNavigationView

class WorkoutWellnessReportActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_workout_wellness_report)

        bindReport()
        findViewById<TextView>(R.id.backBtn).setOnClickListener { finish() }
        setupNavigation()
    }

    override fun onResume() {
        super.onResume()
        bindReport()
    }

    private fun bindReport() {
        val days = WorkoutTrackingStore.daysInCurrentMonth()

        findViewById<TextView>(R.id.meditationWeeklyCount).text =
            "${WorkoutTrackingStore.weekCount(this, "Meditation")}/7"
        findViewById<TextView>(R.id.yogaWeeklyCount).text =
            "${WorkoutTrackingStore.weekCount(this, "Yoga")}/7"
        findViewById<TextView>(R.id.exerciseWeeklyCount).text =
            "${WorkoutTrackingStore.weekCount(this, "Exercises")}/7"
        findViewById<TextView>(R.id.workoutWeeklyPercent).text =
            "${WorkoutTrackingStore.weeklyPercent(this)}/100"

        findViewById<TextView>(R.id.meditationMonthlyCount).text =
            "${WorkoutTrackingStore.monthCount(this, "Meditation")}/$days"
        findViewById<TextView>(R.id.yogaMonthlyCount).text =
            "${WorkoutTrackingStore.monthCount(this, "Yoga")}/$days"
        findViewById<TextView>(R.id.exerciseMonthlyCount).text =
            "${WorkoutTrackingStore.monthCount(this, "Exercises")}/$days"
        findViewById<TextView>(R.id.workoutMonthlyPercent).text =
            "${WorkoutTrackingStore.totalPercent(this)}/100"
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
        android:paddingBottom="80dp">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="20dp">

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="16dp"
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
                    android:text="Workout &amp; Wellness Report"
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
                        android:src="@drawable/workout_report_image" />

                    <View
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:background="@drawable/gradient_overlay_dark" />

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_gravity="bottom|start"
                        android:layout_margin="18dp"
                        android:text="Workout and wellness progress"
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
                        android:layout_marginBottom="12dp"
                        android:text="Weekly Workout &amp; Wellness Report"
                        android:textColor="#2D2D2D"
                        android:textSize="17sp"
                        android:textStyle="bold" />

                    <LinearLayout
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:layout_marginBottom="12dp"
                        android:orientation="horizontal">

                        <TextView
                            android:id="@+id/meditationWeeklyCount"
                            android:layout_width="0dp"
                            android:layout_height="54dp"
                            android:layout_weight="1"
                            android:background="@drawable/icon_bg_purple_light"
                            android:gravity="center"
                            android:text="0/7"
                            android:textColor="#8B7BB5"
                            android:textSize="18sp"
                            android:textStyle="bold" />

                        <TextView
                            android:id="@+id/yogaWeeklyCount"
                            android:layout_width="0dp"
                            android:layout_height="54dp"
                            android:layout_marginStart="8dp"
                            android:layout_weight="1"
                            android:background="@drawable/icon_bg_teal_light"
                            android:gravity="center"
                            android:text="0/7"
                            android:textColor="#4DB6AC"
                            android:textSize="18sp"
                            android:textStyle="bold" />

                        <TextView
                            android:id="@+id/exerciseWeeklyCount"
                            android:layout_width="0dp"
                            android:layout_height="54dp"
                            android:layout_marginStart="8dp"
                            android:layout_weight="1"
                            android:background="@drawable/icon_bg_red_light"
                            android:gravity="center"
                            android:text="0/7"
                            android:textColor="#E57373"
                            android:textSize="18sp"
                            android:textStyle="bold" />
                    </LinearLayout>

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="Meditation    Yoga    Exercise"
                        android:textColor="#888888"
                        android:textSize="12sp" />

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_marginTop="8dp"
                        android:text="Weekly Percentage"
                        android:textColor="#888888"
                        android:textSize="12sp" />

                    <TextView
                        android:id="@+id/workoutWeeklyPercent"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="0/100"
                        android:textColor="#2D2D2D"
                        android:textSize="24sp"
                        android:textStyle="bold" />
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
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_marginBottom="12dp"
                        android:text="Monthly Workout &amp; Wellness Report"
                        android:textColor="#2D2D2D"
                        android:textSize="17sp"
                        android:textStyle="bold" />

                    <LinearLayout
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:layout_marginBottom="12dp"
                        android:orientation="horizontal">

                        <LinearLayout
                            android:layout_width="0dp"
                            android:layout_height="74dp"
                            android:layout_weight="1"
                            android:background="@drawable/icon_bg_purple_light"
                            android:gravity="center"
                            android:orientation="vertical">

                            <TextView
                                android:layout_width="wrap_content"
                                android:layout_height="wrap_content"
                                android:text="Meditation"
                                android:textSize="12sp" />

                            <TextView
                                android:id="@+id/meditationMonthlyCount"
                                android:layout_width="wrap_content"
                                android:layout_height="wrap_content"
                                android:text="0/31"
                                android:textColor="#8B7BB5"
                                android:textSize="18sp"
                                android:textStyle="bold" />
                        </LinearLayout>

                        <LinearLayout
                            android:layout_width="0dp"
                            android:layout_height="74dp"
                            android:layout_marginStart="8dp"
                            android:layout_weight="1"
                            android:background="@drawable/icon_bg_teal_light"
                            android:gravity="center"
                            android:orientation="vertical">

                            <TextView
                                android:layout_width="wrap_content"
                                android:layout_height="wrap_content"
                                android:text="Yoga"
                                android:textSize="12sp" />

                            <TextView
                                android:id="@+id/yogaMonthlyCount"
                                android:layout_width="wrap_content"
                                android:layout_height="wrap_content"
                                android:text="0/31"
                                android:textColor="#4DB6AC"
                                android:textSize="18sp"
                                android:textStyle="bold" />
                        </LinearLayout>

                        <LinearLayout
                            android:layout_width="0dp"
                            android:layout_height="74dp"
                            android:layout_marginStart="8dp"
                            android:layout_weight="1"
                            android:background="@drawable/icon_bg_red_light"
                            android:gravity="center"
                            android:orientation="vertical">

                            <TextView
                                android:layout_width="wrap_content"
                                android:layout_height="wrap_content"
                                android:text="Exercise"
                                android:textSize="12sp" />

                            <TextView
                                android:id="@+id/exerciseMonthlyCount"
                                android:layout_width="wrap_content"
                                android:layout_height="wrap_content"
                                android:text="0/31"
                                android:textColor="#E57373"
                                android:textSize="18sp"
                                android:textStyle="bold" />
                        </LinearLayout>
                    </LinearLayout>

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="Total Percentage"
                        android:textColor="#888888"
                        android:textSize="12sp" />

                    <TextView
                        android:id="@+id/workoutMonthlyPercent"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="0/100"
                        android:textColor="#2D2D2D"
                        android:textSize="24sp"
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

