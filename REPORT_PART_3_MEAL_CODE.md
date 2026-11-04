# Part 3 - Meal Tracking Screens

This section contains separate screen documentation and code for the requested report part.

## Meal Tracking Hub

**Purpose:** Main meal menu for Daily, Weekly, Monthly, Meal Gallery, and Food Guide.

**Activity File:** `MealTrackingHubActivity.kt`  
**Layout File:** `activity_meal_tracking_hub.xml`

### Kotlin Activity Code

```kotlin
package com.example.pcostracker

import android.content.Intent
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import androidx.cardview.widget.CardView
import com.google.android.material.bottomnavigation.BottomNavigationView

class MealTrackingHubActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_meal_tracking_hub)

        findViewById<android.widget.TextView>(R.id.backBtn).setOnClickListener { finish() }
        findViewById<CardView>(R.id.cardDailyMealTracking).setOnClickListener {
            startActivity(Intent(this, MealPlanActivity::class.java).putExtra("mealTab", "daily"))
        }
        findViewById<CardView>(R.id.cardWeeklyMealTracking).setOnClickListener {
            startActivity(Intent(this, MealPlanActivity::class.java).putExtra("mealTab", "monthly"))
        }
        findViewById<CardView>(R.id.cardMonthlyMealTracking).setOnClickListener {
            startActivity(Intent(this, MealPlanActivity::class.java).putExtra("mealTab", "monthly"))
        }
        findViewById<CardView>(R.id.cardMealGallery).setOnClickListener {
            startActivity(Intent(this, MealGalleryActivity::class.java))
        }
        findViewById<CardView>(R.id.cardFoodGuide).setOnClickListener {
            startActivity(Intent(this, FoodGuideActivity::class.java))
        }

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
                    android:text="Meal Tracking"
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
                        android:text="Plan and track your meals"
                        android:textColor="#FFFFFF"
                        android:textSize="20sp"
                        android:textStyle="bold" />
                </FrameLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:id="@+id/cardDailyMealTracking"
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
                        android:src="@drawable/ic_add_circle"
                        app:tint="#4DB6AC" />

                    <TextView
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:layout_marginStart="14dp"
                        android:layout_weight="1"
                        android:text="Daily"
                        android:textColor="#2D2D2D"
                        android:textSize="18sp"
                        android:textStyle="bold" />
                </LinearLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:id="@+id/cardWeeklyMealTracking"
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
                        android:src="@drawable/ic_calendar"
                        app:tint="#8B7BB5" />

                    <TextView
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:layout_marginStart="14dp"
                        android:layout_weight="1"
                        android:text="Weekly"
                        android:textColor="#2D2D2D"
                        android:textSize="18sp"
                        android:textStyle="bold" />
                </LinearLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:id="@+id/cardMonthlyMealTracking"
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
                        android:src="@drawable/ic_reports"
                        app:tint="#E57373" />

                    <TextView
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:layout_marginStart="14dp"
                        android:layout_weight="1"
                        android:text="Monthly"
                        android:textColor="#2D2D2D"
                        android:textSize="18sp"
                        android:textStyle="bold" />
                </LinearLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:id="@+id/cardMealGallery"
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
                        android:src="@drawable/ic_reports"
                        app:tint="#4DB6AC" />

                    <TextView
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:layout_marginStart="14dp"
                        android:layout_weight="1"
                        android:text="Meal Gallery"
                        android:textColor="#2D2D2D"
                        android:textSize="18sp"
                        android:textStyle="bold" />
                </LinearLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:id="@+id/cardFoodGuide"
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
                        android:background="@drawable/icon_bg_teal_light"
                        android:padding="6dp"
                        android:src="@drawable/ic_splash_meal" />

                    <TextView
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:layout_marginStart="14dp"
                        android:layout_weight="1"
                        android:text="Food Guide"
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

## Daily / Monthly Meal Tracking

**Purpose:** Tracks daily meals and weekly/monthly meal completion.

**Activity File:** `MealPlanActivity.kt`  
**Layout File:** `activity_meal_plan.xml`

### Kotlin Activity Code

```kotlin
package com.example.pcostracker

import android.app.DatePickerDialog
import android.content.Intent
import android.graphics.Color
import android.os.Bundle
import android.view.View
import android.widget.Button
import android.widget.EditText
import android.widget.ImageView
import android.widget.LinearLayout
import android.widget.TextView
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity
import androidx.cardview.widget.CardView
import androidx.recyclerview.widget.LinearLayoutManager
import androidx.recyclerview.widget.RecyclerView
import com.google.android.material.bottomnavigation.BottomNavigationView
import com.google.android.material.floatingactionbutton.FloatingActionButton
import java.text.SimpleDateFormat
import java.util.Calendar
import java.util.Locale

class MealPlanActivity : AppCompatActivity() {

    private lateinit var mealAdapter: MealPlanAdapter
    private val meals = mutableListOf<MealItem>()
    private var currentDayIndex = 0
    private var currentWeekIndex = 0
    private val dateFormat = SimpleDateFormat("yyyy-MM-dd", Locale.US)

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_meal_plan)

        setupTabs()
        setupInputs()
        setupDayNavigator()
        setupWeekNavigator()
        setupMealsList()
        setupNavigation()
        currentDayIndex = intent.getIntExtra("dayIndex", 0).coerceIn(0, 27)
        selectTab(intent.getStringExtra("mealTab") ?: "daily")

        findViewById<TextView>(R.id.backBtn).setOnClickListener { finish() }
        findViewById<FloatingActionButton>(R.id.fabAdd).setOnClickListener {
            selectTab("daily")
        }
    }

    override fun onResume() {
        super.onResume()
        refreshAll()
    }

    private fun setupTabs() {
        findViewById<TextView>(R.id.tabDaily).setOnClickListener { selectTab("daily") }
        findViewById<TextView>(R.id.tabMonthly).setOnClickListener { selectTab("monthly") }
        findViewById<ImageView>(R.id.clearMealHistoryBtn).setOnClickListener {
            MealTrackingStore.clearHistory(this)
            refreshAll()
            Toast.makeText(this, "Meal tracking history cleared", Toast.LENGTH_SHORT).show()
        }
    }

    private fun selectTab(selected: String) {
        val tabs = mapOf(
            "daily" to findViewById<TextView>(R.id.tabDaily),
            "weekly" to findViewById(R.id.tabWeekly),
            "monthly" to findViewById(R.id.tabMonthly)
        )
        tabs.forEach { (key, tab) ->
            tab.setBackgroundResource(if (key == selected) R.drawable.tab_selected_bg else 0)
            tab.setTextColor(Color.parseColor(if (key == selected) "#FFFFFF" else "#888888"))
        }

        findViewById<LinearLayout>(R.id.dailyContainer).visibility = if (selected == "daily") View.VISIBLE else View.GONE
        findViewById<LinearLayout>(R.id.weeklyContainer).visibility = View.GONE
        findViewById<CardView>(R.id.monthlyContainer).visibility = if (selected == "monthly") View.VISIBLE else View.GONE
        refreshAll()
    }

    private fun setupInputs() {
        findViewById<EditText>(R.id.startWeightInput).setText(MealTrackingStore.startWeight(this))
        findViewById<EditText>(R.id.endWeightInput).setText(MealTrackingStore.endWeight(this))
        findViewById<TextView>(R.id.startDateText).text = MealTrackingStore.startDate(this)

        findViewById<TextView>(R.id.startDateText).setOnClickListener { showStartDatePicker() }
        findViewById<Button>(R.id.saveMealTrackingBtn).setOnClickListener {
            MealTrackingStore.saveWeights(
                this,
                findViewById<EditText>(R.id.startWeightInput).text.toString().trim(),
                findViewById<EditText>(R.id.endWeightInput).text.toString().trim()
            )
            refreshAll()
        }
    }

    private fun showStartDatePicker() {
        val cal = Calendar.getInstance().apply {
            time = dateFormat.parse(MealTrackingStore.startDate(this@MealPlanActivity))!!
        }
        DatePickerDialog(this, { _, year, month, day ->
            val selected = Calendar.getInstance().apply {
                set(Calendar.YEAR, year)
                set(Calendar.MONTH, month)
                set(Calendar.DAY_OF_MONTH, day)
            }
            val date = dateFormat.format(selected.time)
            MealTrackingStore.setStartDate(this, date)
            findViewById<TextView>(R.id.startDateText).text = date
            refreshMonthly()
        }, cal.get(Calendar.YEAR), cal.get(Calendar.MONTH), cal.get(Calendar.DAY_OF_MONTH)).show()
    }

    private fun setupDayNavigator() {
        findViewById<TextView>(R.id.prevDay).setOnClickListener {
            if (currentDayIndex > 0) {
                currentDayIndex--
                refreshDaily()
            }
        }

        findViewById<TextView>(R.id.nextDay).setOnClickListener {
            if (currentDayIndex < 27) {
                currentDayIndex++
                refreshDaily()
            }
        }
    }

    private fun setupWeekNavigator() {
        findViewById<TextView>(R.id.prevWeek).setOnClickListener {
            if (currentWeekIndex > 0) {
                currentWeekIndex--
                refreshWeekly()
            }
        }
        findViewById<TextView>(R.id.nextWeek).setOnClickListener {
            if (currentWeekIndex < 3) {
                currentWeekIndex++
                refreshWeekly()
            }
        }
    }

    private fun setupMealsList() {
        mealAdapter = MealPlanAdapter(meals) { meal ->
            updateMealStatusBadge()
        }
        findViewById<RecyclerView>(R.id.mealsList).apply {
            layoutManager = LinearLayoutManager(this@MealPlanActivity)
            adapter = mealAdapter
        }

        findViewById<Button>(R.id.saveDailyMealsBtn).setOnClickListener {
            meals.forEach { meal ->
                MealTrackingStore.setCompleted(this, meal.id, meal.isCompleted)
            }
            refreshProgress()
            refreshMonthly()
            updateMealStatusBadge()
            Toast.makeText(this, "Meal changes saved", Toast.LENGTH_SHORT).show()
        }
    }

    private fun refreshAll() {
        refreshDaily()
        refreshWeekly()
        refreshMonthly()
        refreshProgress()
    }

    private fun refreshDaily() {
        val dayInWeek = currentDayIndex % 7
        val week = (currentDayIndex / 7) + 1
        findViewById<TextView>(R.id.currentDayName).text = MealTrackingStore.dayNames[dayInWeek]
        findViewById<TextView>(R.id.currentWeekLabel).text = "Week $week"

        meals.clear()
        meals.addAll(MealTrackingStore.mealsFor(currentDayIndex).map {
            it.copy(isCompleted = MealTrackingStore.isCompleted(this, it.id))
        })
        mealAdapter.notifyDataSetChanged()
        updateMealStatusBadge()
    }

    private fun refreshWeekly() {
        findViewById<TextView>(R.id.currentWeekName).text = "Week ${currentWeekIndex + 1}"
        findViewById<TextView>(R.id.currentWeekPhase).text = "Suggested PCOS-friendly plan"
        val text = MealTrackingStore.weekPlan(currentWeekIndex).mapIndexed { dayIndex, dayMeals ->
            val dayName = MealTrackingStore.dayNames[dayIndex]
            val mealsText = dayMeals.joinToString("\n") { "${it.type}: ${it.description}" }
            "$dayName\n$mealsText"
        }.joinToString("\n\n")
        findViewById<TextView>(R.id.weeklyScheduleText).text = text
    }

    private fun refreshMonthly() {
        val allDays = listOf("M", "T", "W", "T", "F", "S", "S")
        val weeks = (0..3).map { week ->
            (0..6).map { day ->
                MonthlyDayStatus(
                    dayLetter = allDays[day],
                    weekNumber = week + 1,
                    status = MealTrackingStore.dayStatus(this, week * 7 + day)
                )
            }
        }
        findViewById<RecyclerView>(R.id.monthlyCalendarGrid).apply {
            layoutManager = LinearLayoutManager(this@MealPlanActivity)
            adapter = MonthlyCalendarAdapter(weeks) { dayIndex ->
                currentDayIndex = dayIndex
                selectTab("daily")
            }
        }
    }

    private fun refreshProgress() {
        val completed = MealTrackingStore.completedCount(this)
        val percent = (completed * 100) / 90
        findViewById<TextView>(R.id.completedMeals).text = completed.toString()
        findViewById<TextView>(R.id.progressText).text = "$percent%"
        findViewById<com.google.android.material.progressindicator.CircularProgressIndicator>(R.id.weeklyProgress).progress = percent
    }

    private fun updateMealStatusBadge() {
        val statusBadge = findViewById<TextView>(R.id.mealStatusBadge)
        val completed = meals.count { it.isCompleted }
        statusBadge.visibility = View.VISIBLE
        when (completed) {
            3 -> {
                statusBadge.text = "DONE"
                statusBadge.setTextColor(Color.parseColor("#4DB6AC"))
            }
            0 -> {
                statusBadge.text = "NOT LOGGED"
                statusBadge.setTextColor(Color.parseColor("#888888"))
            }
            else -> {
                statusBadge.text = "PARTIAL"
                statusBadge.setTextColor(Color.parseColor("#FFB74D"))
            }
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
                android:gravity="center_vertical"
                android:orientation="horizontal"
                android:layout_marginBottom="16dp">

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
                    android:text="Meal Tracking"
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
                        android:text="Daily meal tracking"
                        android:textColor="#FFFFFF"
                        android:textSize="20sp"
                        android:textStyle="bold" />
                </FrameLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:id="@+id/mealGoalCard"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="14dp"
                android:visibility="gone"
                app:cardBackgroundColor="#E0F5F1"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">

                <androidx.constraintlayout.widget.ConstraintLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:padding="16dp">

                    <LinearLayout
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:orientation="vertical"
                        app:layout_constraintBottom_toBottomOf="parent"
                        app:layout_constraintEnd_toStartOf="@+id/progressCircle"
                        app:layout_constraintStart_toStartOf="parent"
                        app:layout_constraintTop_toTopOf="parent">

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:letterSpacing="0.1"
                            android:text="4 WEEK GOAL"
                            android:textColor="#009688"
                            android:textSize="10sp"
                            android:textStyle="bold" />

                        <LinearLayout
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:gravity="center_vertical"
                            android:orientation="horizontal">

                            <TextView
                                android:id="@+id/completedMeals"
                                android:layout_width="wrap_content"
                                android:layout_height="wrap_content"
                                android:text="0"
                                android:textColor="#00695C"
                                android:textSize="28sp"
                                android:textStyle="bold" />

                            <TextView
                                android:layout_width="wrap_content"
                                android:layout_height="wrap_content"
                                android:layout_marginStart="4dp"
                                android:text="/90 Meals"
                                android:textColor="#00695C"
                                android:textSize="14sp" />
                        </LinearLayout>
                    </LinearLayout>

                    <FrameLayout
                        android:id="@+id/progressCircle"
                        android:layout_width="64dp"
                        android:layout_height="64dp"
                        app:layout_constraintBottom_toBottomOf="parent"
                        app:layout_constraintEnd_toEndOf="parent"
                        app:layout_constraintTop_toTopOf="parent">

                        <com.google.android.material.progressindicator.CircularProgressIndicator
                            android:id="@+id/weeklyProgress"
                            android:layout_width="64dp"
                            android:layout_height="64dp"
                            android:layout_gravity="center"
                            android:progress="0"
                            app:indicatorColor="#4DB6AC"
                            app:indicatorSize="64dp"
                            app:trackColor="#FFFFFF"
                            app:trackThickness="5dp" />

                        <TextView
                            android:id="@+id/progressText"
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:layout_gravity="center"
                            android:text="0%"
                            android:textColor="#009688"
                            android:textSize="12sp"
                            android:textStyle="bold" />
                    </FrameLayout>
                </androidx.constraintlayout.widget.ConstraintLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:id="@+id/mealWeightCard"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="14dp"
                android:visibility="gone"
                app:cardBackgroundColor="#FFFFFF"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">

                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:orientation="vertical"
                    android:padding="14dp">

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="Tracking Start"
                        android:textColor="#2D2D2D"
                        android:textSize="16sp"
                        android:textStyle="bold" />

                    <TextView
                        android:id="@+id/startDateText"
                        android:layout_width="match_parent"
                        android:layout_height="44dp"
                        android:gravity="center_vertical"
                        android:textColor="#009688"
                        android:textSize="14sp" />

                    <LinearLayout
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:orientation="horizontal">

                        <EditText
                            android:id="@+id/startWeightInput"
                            android:layout_width="0dp"
                            android:layout_height="48dp"
                            android:layout_weight="1"
                            android:background="@drawable/input_background"
                            android:hint="Start kg"
                            android:inputType="numberDecimal"
                            android:paddingStart="12dp"
                            android:paddingEnd="12dp"
                            android:textSize="14sp" />

                        <EditText
                            android:id="@+id/endWeightInput"
                            android:layout_width="0dp"
                            android:layout_height="48dp"
                            android:layout_marginStart="10dp"
                            android:layout_weight="1"
                            android:background="@drawable/input_background"
                            android:hint="End kg"
                            android:inputType="numberDecimal"
                            android:paddingStart="12dp"
                            android:paddingEnd="12dp"
                            android:textSize="14sp" />
                    </LinearLayout>

                    <Button
                        android:id="@+id/saveMealTrackingBtn"
                        android:layout_width="match_parent"
                        android:layout_height="48dp"
                        android:layout_marginTop="10dp"
                        android:text="Save Tracking" />
                </LinearLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="16dp"
                app:cardBackgroundColor="#FFFFFF"
                app:cardCornerRadius="24dp"
                app:cardElevation="0dp">

                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:orientation="horizontal"
                    android:padding="4dp">

                    <TextView
                        android:id="@+id/tabDaily"
                        android:layout_width="0dp"
                        android:layout_height="36dp"
                        android:layout_weight="1"
                        android:background="@drawable/tab_selected_bg"
                        android:gravity="center"
                        android:text="Daily"
                        android:textColor="#FFFFFF"
                        android:textSize="14sp" />

                    <TextView
                        android:id="@+id/tabWeekly"
                        android:layout_width="0dp"
                        android:layout_height="36dp"
                        android:layout_weight="1"
                        android:gravity="center"
                        android:text="Weekly"
                        android:textColor="#888888"
                        android:textSize="14sp"
                        android:visibility="gone" />

                    <TextView
                        android:id="@+id/tabMonthly"
                        android:layout_width="0dp"
                        android:layout_height="36dp"
                        android:layout_weight="1"
                        android:gravity="center"
                        android:text="Weekly &amp; Monthly"
                        android:textColor="#888888"
                        android:textSize="14sp" />
                </LinearLayout>
            </androidx.cardview.widget.CardView>

            <LinearLayout
                android:id="@+id/dailyContainer"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:orientation="vertical">

                <androidx.cardview.widget.CardView
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:layout_marginBottom="16dp"
                    app:cardBackgroundColor="#FFFFFF"
                    app:cardCornerRadius="16dp"
                    app:cardElevation="0dp">

                    <androidx.constraintlayout.widget.ConstraintLayout
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:padding="12dp">

                        <TextView
                            android:id="@+id/prevDay"
                            android:layout_width="44dp"
                            android:layout_height="44dp"
                            android:gravity="center"
                            android:text="‹"
                            android:textColor="#888888"
                            android:textSize="24sp"
                            app:layout_constraintBottom_toBottomOf="parent"
                            app:layout_constraintStart_toStartOf="parent"
                            app:layout_constraintTop_toTopOf="parent" />

                        <LinearLayout
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:gravity="center"
                            android:orientation="vertical"
                            app:layout_constraintBottom_toBottomOf="parent"
                            app:layout_constraintEnd_toEndOf="parent"
                            app:layout_constraintStart_toStartOf="parent"
                            app:layout_constraintTop_toTopOf="parent">

                            <TextView
                                android:id="@+id/currentDayName"
                                android:layout_width="wrap_content"
                                android:layout_height="wrap_content"
                                android:text="Monday"
                                android:textColor="#2D2D2D"
                                android:textSize="18sp"
                                android:textStyle="bold" />

                            <TextView
                                android:id="@+id/currentWeekLabel"
                                android:layout_width="wrap_content"
                                android:layout_height="wrap_content"
                                android:text="Week 1"
                                android:textColor="#AAAAAA"
                                android:textSize="12sp" />
                        </LinearLayout>

                        <TextView
                            android:id="@+id/nextDay"
                            android:layout_width="44dp"
                            android:layout_height="44dp"
                            android:gravity="center"
                            android:text="›"
                            android:textColor="#888888"
                            android:textSize="24sp"
                            app:layout_constraintBottom_toBottomOf="parent"
                            app:layout_constraintEnd_toEndOf="parent"
                            app:layout_constraintTop_toTopOf="parent" />
                    </androidx.constraintlayout.widget.ConstraintLayout>
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
                            android:text="Daily Meal Tracking"
                            android:textColor="#2D2D2D"
                            android:textSize="18sp"
                            android:textStyle="bold" />

                        <TextView
                            android:id="@+id/mealStatusBadge"
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:layout_marginBottom="8dp"
                            android:text="NOT LOGGED"
                            android:textSize="11sp"
                            android:textStyle="bold" />

                        <androidx.recyclerview.widget.RecyclerView
                            android:id="@+id/mealsList"
                            android:layout_width="match_parent"
                            android:layout_height="wrap_content"
                            android:nestedScrollingEnabled="false" />

                        <Button
                            android:id="@+id/saveDailyMealsBtn"
                            android:layout_width="match_parent"
                            android:layout_height="48dp"
                            android:layout_marginTop="14dp"
                            android:text="Save Changes" />
                    </LinearLayout>
                </androidx.cardview.widget.CardView>
            </LinearLayout>

            <LinearLayout
                android:id="@+id/weeklyContainer"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:orientation="vertical"
                android:visibility="gone">

                <androidx.cardview.widget.CardView
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:layout_marginBottom="16dp"
                    app:cardBackgroundColor="#FFFFFF"
                    app:cardCornerRadius="16dp"
                    app:cardElevation="0dp">

                    <androidx.constraintlayout.widget.ConstraintLayout
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:padding="12dp">

                        <TextView
                            android:id="@+id/prevWeek"
                            android:layout_width="44dp"
                            android:layout_height="44dp"
                            android:gravity="center"
                            android:text="‹"
                            android:textSize="24sp"
                            app:layout_constraintBottom_toBottomOf="parent"
                            app:layout_constraintStart_toStartOf="parent"
                            app:layout_constraintTop_toTopOf="parent" />

                        <LinearLayout
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:gravity="center"
                            android:orientation="vertical"
                            app:layout_constraintBottom_toBottomOf="parent"
                            app:layout_constraintEnd_toEndOf="parent"
                            app:layout_constraintStart_toStartOf="parent"
                            app:layout_constraintTop_toTopOf="parent">

                            <TextView
                                android:id="@+id/currentWeekName"
                                android:layout_width="wrap_content"
                                android:layout_height="wrap_content"
                                android:text="Week 1"
                                android:textColor="#2D2D2D"
                                android:textSize="18sp"
                                android:textStyle="bold" />

                            <TextView
                                android:id="@+id/currentWeekPhase"
                                android:layout_width="wrap_content"
                                android:layout_height="wrap_content"
                                android:text="Suggested PCOS-friendly plan"
                                android:textColor="#AAAAAA"
                                android:textSize="12sp" />
                        </LinearLayout>

                        <TextView
                            android:id="@+id/nextWeek"
                            android:layout_width="44dp"
                            android:layout_height="44dp"
                            android:gravity="center"
                            android:text="›"
                            android:textSize="24sp"
                            app:layout_constraintBottom_toBottomOf="parent"
                            app:layout_constraintEnd_toEndOf="parent"
                            app:layout_constraintTop_toTopOf="parent" />
                    </androidx.constraintlayout.widget.ConstraintLayout>
                </androidx.cardview.widget.CardView>

                <androidx.cardview.widget.CardView
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    app:cardBackgroundColor="#FFFFFF"
                    app:cardCornerRadius="16dp"
                    app:cardElevation="0dp">

                    <TextView
                        android:id="@+id/weeklyScheduleText"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:lineSpacingExtra="4dp"
                        android:padding="16dp"
                        android:textColor="#444444"
                        android:textSize="13sp" />
                </androidx.cardview.widget.CardView>
            </LinearLayout>

            <androidx.cardview.widget.CardView
                android:id="@+id/monthlyContainer"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:visibility="gone"
                app:cardBackgroundColor="#FFFFFF"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">

                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:orientation="vertical"
                    android:padding="16dp">

                    <androidx.constraintlayout.widget.ConstraintLayout
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:layout_marginBottom="12dp">

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="Weekly &amp; Monthly Meal Tracking"
                            android:textColor="#2D2D2D"
                            android:textSize="16sp"
                            android:textStyle="bold"
                            app:layout_constraintBottom_toBottomOf="parent"
                            app:layout_constraintEnd_toEndOf="parent"
                            app:layout_constraintStart_toStartOf="parent"
                            app:layout_constraintTop_toTopOf="parent" />

                        <ImageView
                            android:id="@+id/clearMealHistoryBtn"
                            android:layout_width="28dp"
                            android:layout_height="28dp"
                            android:padding="3dp"
                            android:src="@drawable/ic_delete"
                            app:tint="#888888"
                            app:layout_constraintBottom_toBottomOf="parent"
                            app:layout_constraintEnd_toEndOf="parent"
                            app:layout_constraintTop_toTopOf="parent" />
                    </androidx.constraintlayout.widget.ConstraintLayout>

                    <androidx.recyclerview.widget.RecyclerView
                        android:id="@+id/monthlyCalendarGrid"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:nestedScrollingEnabled="false" />
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

    <com.google.android.material.floatingactionbutton.FloatingActionButton
        android:id="@+id/fabAdd"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom|center_horizontal"
        android:layout_marginBottom="28dp"
        android:src="@drawable/ic_add_circle"
        app:backgroundTint="#009688"
        app:elevation="4dp"
        app:tint="#FFFFFF" />

</androidx.coordinatorlayout.widget.CoordinatorLayout>

```

---

## Meal Gallery

**Purpose:** Shows weekly PCOS-friendly breakfast, lunch, and dinner suggestions.

**Activity File:** `MealGalleryActivity.kt`  
**Layout File:** `activity_meal_gallery.xml`

### Kotlin Activity Code

```kotlin
package com.example.pcostracker

import android.content.Intent
import android.os.Bundle
import android.view.Gravity
import android.widget.LinearLayout
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import androidx.cardview.widget.CardView
import com.google.android.material.bottomnavigation.BottomNavigationView

class MealGalleryActivity : AppCompatActivity() {

    private var currentWeekIndex = 0

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_meal_gallery)

        findViewById<TextView>(R.id.backBtn).setOnClickListener { finish() }
        findViewById<TextView>(R.id.prevWeek).setOnClickListener {
            if (currentWeekIndex > 0) {
                currentWeekIndex--
                bindWeek()
            }
        }
        findViewById<TextView>(R.id.nextWeek).setOnClickListener {
            if (currentWeekIndex < 3) {
                currentWeekIndex++
                bindWeek()
            }
        }

        bindWeek()
        setupNavigation()
    }

    private fun bindWeek() {
        findViewById<TextView>(R.id.currentWeekName).text = "Week ${currentWeekIndex + 1}"
        val container = findViewById<LinearLayout>(R.id.weeklyMealsContainer)
        container.removeAllViews()

        MealTrackingStore.weekPlan(currentWeekIndex).forEachIndexed { dayIndex, meals ->
            val card = CardView(this).apply {
                radius = 16f
                cardElevation = 0f
                setCardBackgroundColor(android.graphics.Color.WHITE)
                useCompatPadding = true
            }
            val cardParams = LinearLayout.LayoutParams(
                LinearLayout.LayoutParams.MATCH_PARENT,
                LinearLayout.LayoutParams.WRAP_CONTENT
            )
            cardParams.setMargins(0, 0, 0, 12)
            card.layoutParams = cardParams

            val dayLayout = LinearLayout(this).apply {
                orientation = LinearLayout.VERTICAL
                setPadding(16.dp(), 14.dp(), 16.dp(), 14.dp())
            }
            dayLayout.addView(TextView(this).apply {
                text = MealTrackingStore.dayNames[dayIndex]
                setTextColor(android.graphics.Color.parseColor("#2D2D2D"))
                textSize = 16f
                setTypeface(typeface, android.graphics.Typeface.BOLD)
            })

            meals.forEach { meal ->
                dayLayout.addView(mealRow(meal))
            }

            card.addView(dayLayout)
            container.addView(card)
        }
    }

    private fun mealRow(meal: MealItem): LinearLayout {
        val iconColor = when (meal.type) {
            "Breakfast" -> "#4DB6AC"
            "Lunch" -> "#8B7BB5"
            else -> "#E57373"
        }
        val row = LinearLayout(this).apply {
            orientation = LinearLayout.HORIZONTAL
            gravity = Gravity.CENTER_VERTICAL
            setPadding(0, 10.dp(), 0, 0)
        }
        row.addView(TextView(this).apply {
            text = meal.type.take(1)
            gravity = Gravity.CENTER
            textSize = 11f
            setTextColor(android.graphics.Color.parseColor(iconColor))
            setTypeface(typeface, android.graphics.Typeface.BOLD)
            setBackgroundResource(R.drawable.meal_letter_bg)
            layoutParams = LinearLayout.LayoutParams(28.dp(), 28.dp())
        })
        row.addView(TextView(this).apply {
            text = "${meal.type}: ${meal.description}"
            setTextColor(android.graphics.Color.parseColor("#444444"))
            textSize = 13f
            val params = LinearLayout.LayoutParams(0, LinearLayout.LayoutParams.WRAP_CONTENT, 1f)
            params.setMargins(10.dp(), 0, 0, 0)
            layoutParams = params
        })
        return row
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

    private fun Int.dp(): Int = (this * resources.displayMetrics.density).toInt()
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
                android:gravity="center_vertical"
                android:orientation="horizontal"
                android:layout_marginBottom="16dp">

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
                    android:text="Meal Gallery"
                    android:textColor="#2D2D2D"
                    android:textSize="24sp"
                    android:textStyle="bold" />
            </LinearLayout>

            <androidx.cardview.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="16dp"
                app:cardBackgroundColor="#FFFFFF"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">

                <androidx.constraintlayout.widget.ConstraintLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:padding="12dp">

                    <TextView
                        android:id="@+id/prevWeek"
                        android:layout_width="44dp"
                        android:layout_height="44dp"
                        android:gravity="center"
                        android:text="&lt;"
                        android:textColor="#888888"
                        android:textSize="20sp"
                        app:layout_constraintBottom_toBottomOf="parent"
                        app:layout_constraintStart_toStartOf="parent"
                        app:layout_constraintTop_toTopOf="parent" />

                    <TextView
                        android:id="@+id/currentWeekName"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="Week 1"
                        android:textColor="#2D2D2D"
                        android:textSize="18sp"
                        android:textStyle="bold"
                        app:layout_constraintBottom_toBottomOf="parent"
                        app:layout_constraintEnd_toEndOf="parent"
                        app:layout_constraintStart_toStartOf="parent"
                        app:layout_constraintTop_toTopOf="parent" />

                    <TextView
                        android:id="@+id/nextWeek"
                        android:layout_width="44dp"
                        android:layout_height="44dp"
                        android:gravity="center"
                        android:text="&gt;"
                        android:textColor="#888888"
                        android:textSize="20sp"
                        app:layout_constraintBottom_toBottomOf="parent"
                        app:layout_constraintEnd_toEndOf="parent"
                        app:layout_constraintTop_toTopOf="parent" />
                </androidx.constraintlayout.widget.ConstraintLayout>
            </androidx.cardview.widget.CardView>

            <LinearLayout
                android:id="@+id/weeklyMealsContainer"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:orientation="vertical" />
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

## Food Guide

**Purpose:** Shows healthy food choices and foods to avoid with icons.

**Activity File:** `FoodGuideActivity.kt`  
**Layout File:** `activity_food_guide.xml`

### Kotlin Activity Code

```kotlin
package com.example.pcostracker

import android.content.Intent
import android.os.Bundle
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import com.google.android.material.bottomnavigation.BottomNavigationView

class FoodGuideActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_food_guide)

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
                    android:text="Food Guide"
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
                        android:text="Healthy PCOS food choices"
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

                <TextView
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:lineSpacingExtra="6dp"
                    android:padding="16dp"
                    android:text="🥗  Healthy Food Choices\n\n🍎  Eat more fruits &amp; vegetables\n🥚  Choose high-protein foods\n🌾  Eat whole grains &amp; fiber foods\n🥜  Include nuts &amp; seeds\n💧  Drink plenty of water"
                    android:textColor="#444444"
                    android:textSize="15sp" />
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                app:cardBackgroundColor="#FFF3F4"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">

                <TextView
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:lineSpacingExtra="6dp"
                    android:padding="16dp"
                    android:text="🚫  Avoid\n\n🍬  Sugary foods &amp; drinks\n🍟  Fast food\n🛢  Oily/processed foods\n☕  Too much caffeine"
                    android:textColor="#444444"
                    android:textSize="15sp" />
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

