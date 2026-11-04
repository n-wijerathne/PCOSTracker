# Activity Code Appendix

This appendix contains the source code for every Activity in the PCOS Tracker app.

## AddDataHubActivity.kt

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

## AddMealActivity.kt

```kotlin
package com.example.pcostracker

import android.os.Bundle
import android.widget.*
import androidx.appcompat.app.AppCompatActivity
import com.google.android.material.chip.ChipGroup
import com.google.firebase.auth.FirebaseAuth
import com.google.firebase.firestore.FirebaseFirestore

class AddMealActivity : AppCompatActivity() {

    private val db = FirebaseFirestore.getInstance()
    private val auth = FirebaseAuth.getInstance()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_add_meal)

        val preselectedDay = intent.getStringExtra("selectedDay") ?: "Monday"
        val dayNames = listOf("Monday","Tuesday","Wednesday","Thursday","Friday","Saturday","Sunday")

        // Setup day spinner
        val spinner = findViewById<Spinner>(R.id.daySpinner)
        val adapter = ArrayAdapter(this, android.R.layout.simple_spinner_item, dayNames)
        adapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item)
        spinner.adapter = adapter
        spinner.setSelection(dayNames.indexOf(preselectedDay))

        findViewById<TextView>(R.id.backBtn).setOnClickListener { finish() }

        findViewById<Button>(R.id.saveMealBtn).setOnClickListener {
            saveMeal(spinner, dayNames)
        }
    }

    private fun saveMeal(spinner: Spinner, dayNames: List<String>) {
        val userId = auth.currentUser?.uid ?: return

        val chipGroup = findViewById<ChipGroup>(R.id.mealTypeGroup)
        val selectedChipId = chipGroup.checkedChipId
        if (selectedChipId == -1) {
            Toast.makeText(this, "Please select a meal type", Toast.LENGTH_SHORT).show()
            return
        }

        val mealType = findViewById<com.google.android.material.chip.Chip>(selectedChipId).text.toString()
        val description = findViewById<EditText>(R.id.mealDescription).text.toString().trim()
        val selectedDay = spinner.selectedItem.toString()

        if (description.isEmpty()) {
            Toast.makeText(this, "Please enter meal description", Toast.LENGTH_SHORT).show()
            return
        }

        val meal = hashMapOf(
            "type" to mealType,
            "description" to description,
            "day" to selectedDay,
            "completed" to false
        )

        db.collection("users").document(userId)
            .collection("mealPlans")
            .add(meal)
            .addOnSuccessListener {
                Toast.makeText(this, "Meal added!", Toast.LENGTH_SHORT).show()
                finish()
            }
            .addOnFailureListener {
                Toast.makeText(this, "Error: ${it.message}", Toast.LENGTH_LONG).show()
            }
    }
}
```

## CalendarActivity.kt

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

## DailyCheckInActivity.kt

```kotlin
package com.example.pcostracker

import android.content.Intent
import android.os.Bundle
import android.view.View
import android.widget.Button
import android.widget.TextView
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity
import com.google.android.material.bottomnavigation.BottomNavigationView
import com.google.android.material.chip.ChipGroup
import com.google.firebase.auth.FirebaseAuth
import com.google.firebase.firestore.FirebaseFirestore
import java.text.SimpleDateFormat
import java.util.*

class DailyCheckInActivity : AppCompatActivity() {

    private val db = FirebaseFirestore.getInstance()
    private val auth = FirebaseAuth.getInstance()
    private var selectedPainLevel = "Low"
    private var selectedCycleType = "Normal"

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_daily_check_in)

        setupMealRows()
        setupPainSelector()
        setupSaveButton()
        setupNavigation()

        findViewById<TextView>(R.id.backBtn).setOnClickListener { finish() }
    }

    private fun setupMealRows() {
        // Set meal labels
        findViewById<TextView>(R.id.rowBreakfast.let { R.id.mealName }).apply { }

        listOf(
            Pair(R.id.rowBreakfast, "Breakfast"),
            Pair(R.id.rowLunch, "Lunch"),
            Pair(R.id.rowDinner, "Dinner"),
            Pair(R.id.rowSnacks, "Snacks")
        ).forEach { (rowId, label) ->
            val row = findViewById<View>(rowId)
            row.findViewById<TextView>(R.id.mealName).text = label
            row.findViewById<TextView>(R.id.mealAddBtn).setOnClickListener {
                Toast.makeText(this, "Add $label", Toast.LENGTH_SHORT).show()
            }
        }
    }

    private fun setupPainSelector() {
        val emojiLow = findViewById<TextView>(R.id.emojiLow)
        val emojiMedium = findViewById<TextView>(R.id.emojiMedium)
        val emojiHigh = findViewById<TextView>(R.id.emojiHigh)
        val painLow = findViewById<TextView>(R.id.painLow)
        val painMedium = findViewById<TextView>(R.id.painMedium)
        val painHigh = findViewById<TextView>(R.id.painHigh)

        fun selectPain(level: String) {
            selectedPainLevel = level
            emojiLow.setBackgroundResource(if (level == "Low") R.drawable.emoji_selected_bg else 0)
            emojiMedium.setBackgroundResource(if (level == "Medium") R.drawable.emoji_selected_bg else 0)
            emojiHigh.setBackgroundResource(if (level == "High") R.drawable.emoji_selected_bg else 0)
            painLow.setTextColor(getColor(if (level == "Low") android.R.color.holo_green_dark else R.color.grey_text))
            painMedium.setTextColor(getColor(if (level == "Medium") android.R.color.holo_orange_dark else R.color.grey_text))
            painHigh.setTextColor(getColor(if (level == "High") android.R.color.holo_red_dark else R.color.grey_text))
        }

        emojiLow.setOnClickListener { selectPain("Low") }
        emojiMedium.setOnClickListener { selectPain("Medium") }
        emojiHigh.setOnClickListener { selectPain("High") }

        // Cycle insight
        val cycleNormal = findViewById<TextView>(R.id.cycleNormal)
        val cyclePcos = findViewById<TextView>(R.id.cyclePcos)

        cycleNormal.setOnClickListener {
            selectedCycleType = "Normal"
            cycleNormal.setTextColor(getColor(android.R.color.holo_green_dark))
            cyclePcos.setTextColor(getColor(R.color.grey_text))
        }
        cyclePcos.setOnClickListener {
            selectedCycleType = "PCOS"
            cyclePcos.setTextColor(getColor(android.R.color.holo_red_dark))
            cycleNormal.setTextColor(getColor(R.color.grey_text))
        }
    }

    private fun setupSaveButton() {
        findViewById<Button>(R.id.saveCheckInBtn).setOnClickListener {
            saveToFirestore()
        }
    }

    private fun saveToFirestore() {
        val userId = auth.currentUser?.uid ?: return
        val today = SimpleDateFormat("yyyy-MM-dd", Locale.getDefault()).format(Date())

        val chipGroup = findViewById<ChipGroup>(R.id.workoutChipGroup)
        val selectedWorkouts = mutableListOf<String>()
        for (i in 0 until chipGroup.childCount) {
            val chip = chipGroup.getChildAt(i) as? com.google.android.material.chip.Chip
            if (chip?.isChecked == true) selectedWorkouts.add(chip.text.toString())
        }

        val checkInData = hashMapOf(
            "date" to today,
            "painLevel" to selectedPainLevel,
            "cycleType" to selectedCycleType,
            "workouts" to selectedWorkouts,
            "timestamp" to com.google.firebase.Timestamp.now()
        )

        db.collection("users").document(userId)
            .collection("checkIns").document(today)
            .set(checkInData)
            .addOnSuccessListener {
                Toast.makeText(this, "Check-in saved!", Toast.LENGTH_SHORT).show()
                finish()
            }
            .addOnFailureListener {
                Toast.makeText(this, "Error: ${it.message}", Toast.LENGTH_LONG).show()
            }
    }

    private fun setupNavigation() {
        val bottomNav = findViewById<BottomNavigationView>(R.id.bottomNav)
        bottomNav.setOnItemSelectedListener { item ->
            when (item.itemId) {
                R.id.nav_home -> { startActivity(Intent(this, MainActivity::class.java)); true }
                R.id.nav_calendar -> { startActivity(Intent(this, CalendarActivity::class.java)); true }
                R.id.nav_add -> true
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

## FinalReportActivity.kt

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

## FoodGuideActivity.kt

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

## LoginActivity.kt

```kotlin
package com.example.pcostracker

import android.content.Intent
import android.os.Bundle
import android.view.View
import android.widget.Button
import android.widget.EditText
import android.widget.TextView
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity
import com.google.firebase.auth.FirebaseAuth

class LoginActivity : AppCompatActivity() {

    private lateinit var auth: FirebaseAuth
    private lateinit var emailInput: EditText
    private lateinit var passwordInput: EditText
    private lateinit var loginButton: Button
    private lateinit var signUpText: TextView
    private lateinit var forgotPassword: TextView

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_login)

        auth = FirebaseAuth.getInstance()

        emailInput = findViewById(R.id.emailInput)
        passwordInput = findViewById(R.id.passwordInput)
        loginButton = findViewById(R.id.loginButton)
        signUpText = findViewById(R.id.signUpText)
        forgotPassword = findViewById(R.id.forgotPassword)

        // Login button
        loginButton.setOnClickListener {
            val email = emailInput.text.toString().trim()
            val password = passwordInput.text.toString().trim()

            if (validateInputs(email, password)) {
                loginUser(email, password)
            }
        }

        // Sign Up navigation
        signUpText.setOnClickListener {
            startActivity(Intent(this, SignUpActivity::class.java))
        }

        // Forgot Password
        forgotPassword.setOnClickListener {
            val email = emailInput.text.toString().trim()
            if (email.isEmpty()) {
                Toast.makeText(this, "Enter your email first", Toast.LENGTH_SHORT).show()
            } else {
                sendPasswordReset(email)
            }
        }
    }

    private fun validateInputs(email: String, password: String): Boolean {
        if (email.isEmpty()) {
            emailInput.error = "Email is required"
            return false
        }
        if (!android.util.Patterns.EMAIL_ADDRESS.matcher(email).matches()) {
            emailInput.error = "Enter a valid email"
            return false
        }
        if (password.isEmpty()) {
            passwordInput.error = "Password is required"
            return false
        }
        if (password.length < 6) {
            passwordInput.error = "Password must be at least 6 characters"
            return false
        }
        return true
    }

    private fun loginUser(email: String, password: String) {
        loginButton.isEnabled = false
        loginButton.text = "Logging in..."

        auth.signInWithEmailAndPassword(email, password)
            .addOnCompleteListener(this) { task ->
                loginButton.isEnabled = true
                loginButton.text = "Login"

                if (task.isSuccessful) {
                    Toast.makeText(this, "Login successful!", Toast.LENGTH_SHORT).show()
                    startActivity(Intent(this, MainActivity::class.java))
                    finish()
                } else {
                    Toast.makeText(
                        this,
                        "Login failed: ${task.exception?.message}",
                        Toast.LENGTH_LONG
                    ).show()
                }
            }
    }

    private fun sendPasswordReset(email: String) {
        auth.sendPasswordResetEmail(email)
            .addOnCompleteListener { task ->
                if (task.isSuccessful) {
                    Toast.makeText(this, "Reset email sent to $email", Toast.LENGTH_LONG).show()
                } else {
                    Toast.makeText(this, "Error: ${task.exception?.message}", Toast.LENGTH_LONG).show()
                }
            }
    }
}
```

## MainActivity.kt

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

## MealGalleryActivity.kt

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

## MealPlanActivity.kt

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

## MealReportActivity.kt

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

## MealTrackingHubActivity.kt

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

## PeriodGuideActivity.kt

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

## PeriodTrackingHubActivity.kt

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

## PersonalInfoActivity.kt

```kotlin
package com.example.pcostracker


import android.app.DatePickerDialog
import android.content.Intent
import android.net.Uri
import android.os.Bundle
import android.view.View
import android.widget.Button
import android.widget.CheckBox
import android.widget.LinearLayout
import android.widget.TextView
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity
import androidx.activity.result.contract.ActivityResultContracts
import androidx.constraintlayout.widget.ConstraintLayout
import com.google.android.material.bottomnavigation.BottomNavigationView
import com.google.android.material.floatingactionbutton.FloatingActionButton
import com.google.firebase.auth.FirebaseAuth
import com.google.firebase.firestore.FirebaseFirestore
import android.widget.EditText
import de.hdodenhof.circleimageview.CircleImageView
import java.util.*

class PersonalInfoActivity : AppCompatActivity() {

    private val auth = FirebaseAuth.getInstance()
    private val db = FirebaseFirestore.getInstance()
    private var isGoalDropdownOpen = false
    private var selectedImageUri: Uri? = null

    private val imagePicker = registerForActivityResult(ActivityResultContracts.OpenDocument()) { uri ->
        uri ?: return@registerForActivityResult
        selectedImageUri = uri
        try {
            contentResolver.takePersistableUriPermission(uri, Intent.FLAG_GRANT_READ_URI_PERMISSION)
        } catch (_: SecurityException) {
        }
        findViewById<CircleImageView>(R.id.profileImageEdit).setImageURI(uri)
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_personal_info)

        loadUserData()
        setupDatePicker()
        setupGoalDropdown()
        setupSaveButton()
        setupNavigation()

        findViewById<TextView>(R.id.backBtn).setOnClickListener { finish() }

        val openImagePicker = View.OnClickListener {
            imagePicker.launch(arrayOf("image/*"))
        }
        findViewById<TextView>(R.id.editImageBtn).setOnClickListener(openImagePicker)
        findViewById<CircleImageView>(R.id.profileImageEdit).setOnClickListener(openImagePicker)

        findViewById<FloatingActionButton>(R.id.fabAdd).setOnClickListener {
            startActivity(Intent(this, AddDataHubActivity::class.java))
        }
    }

    private fun loadUserData() {
        val savedImageUri = ProfileStore.imageUri(this)
        if (savedImageUri.isNotEmpty()) {
            selectedImageUri = Uri.parse(savedImageUri)
            findViewById<CircleImageView>(R.id.profileImageEdit).setImageURI(selectedImageUri)
        }

        findViewById<EditText>(R.id.fullNameInput).setText(ProfileStore.name(this))
        findViewById<EditText>(R.id.emailInput).setText(ProfileStore.email(this))
        findViewById<TextView>(R.id.dobText).text = ProfileStore.dob(this)

        val user = auth.currentUser ?: return
        if (ProfileStore.email(this).isEmpty()) {
            findViewById<EditText>(R.id.emailInput).setText(user.email)
        }

        db.collection("users").document(user.uid).get()
            .addOnSuccessListener { doc ->
                val firestoreName = doc.getString("fullName").orEmpty()
                val firestoreDob = doc.getString("dob").orEmpty()
                val firestoreImage = doc.getString("profileImageUri").orEmpty()

                if (firestoreName.isNotEmpty()) {
                    findViewById<EditText>(R.id.fullNameInput).setText(firestoreName)
                }
                if (firestoreDob.isNotEmpty()) {
                    findViewById<TextView>(R.id.dobText).text = firestoreDob
                }
                if (firestoreImage.isNotEmpty() && savedImageUri.isEmpty()) {
                    selectedImageUri = Uri.parse(firestoreImage)
                    findViewById<CircleImageView>(R.id.profileImageEdit).setImageURI(selectedImageUri)
                }

                val goals = doc.get("goals") as? List<*> ?: emptyList<String>()
                if (goals.contains("Track Cycle"))
                    findViewById<CheckBox>(R.id.goalTrackCycle).isChecked = true
                if (goals.contains("Manage Symptoms"))
                    findViewById<CheckBox>(R.id.goalManageSymptoms).isChecked = true
                if (goals.contains("Improve Nutrition"))
                    findViewById<CheckBox>(R.id.goalImproveNutrition).isChecked = true
                if (goals.contains("Stay Active"))
                    findViewById<CheckBox>(R.id.goalStayActive).isChecked = true

                updateGoalText()
            }
    }

    private fun setupDatePicker() {
        val dobText = findViewById<TextView>(R.id.dobText)
        val calendarIcon = findViewById<TextView>(R.id.calendarIcon)

        val showDatePicker = {
            val cal = Calendar.getInstance()
            DatePickerDialog(this, { _, year, month, day ->
                dobText.text = "%02d.%02d.%d".format(day, month + 1, year)
            }, cal.get(Calendar.YEAR) - 25, cal.get(Calendar.MONTH),
                cal.get(Calendar.DAY_OF_MONTH)).show()
        }

        dobText.setOnClickListener { showDatePicker() }
        calendarIcon.setOnClickListener { showDatePicker() }
    }

    private fun setupGoalDropdown() {
        val goalDropdown = findViewById<ConstraintLayout>(R.id.goalDropdown)
        val goalOptions = findViewById<LinearLayout>(R.id.goalOptionsContainer)

        goalDropdown.setOnClickListener {
            isGoalDropdownOpen = !isGoalDropdownOpen
            goalOptions.visibility = if (isGoalDropdownOpen) View.VISIBLE else View.GONE
        }

        listOf(R.id.goalTrackCycle, R.id.goalManageSymptoms,
            R.id.goalImproveNutrition, R.id.goalStayActive).forEach { id ->
            findViewById<CheckBox>(id).setOnCheckedChangeListener { _, _ -> updateGoalText() }
        }
    }

    private fun updateGoalText() {
        val count = listOf(R.id.goalTrackCycle, R.id.goalManageSymptoms,
            R.id.goalImproveNutrition, R.id.goalStayActive)
            .count { findViewById<CheckBox>(it).isChecked }

        findViewById<TextView>(R.id.goalText).text =
            if (count == 0) "Select goals" else "$count Selected"
    }

    private fun setupSaveButton() {
        findViewById<Button>(R.id.saveChangesBtn).setOnClickListener {
            val name = findViewById<EditText>(R.id.fullNameInput).text.toString().trim()
            val email = findViewById<EditText>(R.id.emailInput).text.toString().trim()
            val dob = findViewById<TextView>(R.id.dobText).text.toString()

            if (name.isEmpty()) {
                Toast.makeText(this, "Please enter your name", Toast.LENGTH_SHORT).show()
                return@setOnClickListener
            }

            val selectedGoals = mutableListOf<String>()
            if (findViewById<CheckBox>(R.id.goalTrackCycle).isChecked)
                selectedGoals.add("Track Cycle")
            if (findViewById<CheckBox>(R.id.goalManageSymptoms).isChecked)
                selectedGoals.add("Manage Symptoms")
            if (findViewById<CheckBox>(R.id.goalImproveNutrition).isChecked)
                selectedGoals.add("Improve Nutrition")
            if (findViewById<CheckBox>(R.id.goalStayActive).isChecked)
                selectedGoals.add("Stay Active")

            val userData = hashMapOf(
                "fullName" to name,
                "email" to email,
                "dob" to dob,
                "goals" to selectedGoals,
                "profileImageUri" to selectedImageUri?.toString().orEmpty()
            )

            ProfileStore.save(this, name, email, dob, selectedImageUri?.toString())

            val userId = auth.currentUser?.uid
            if (userId == null) {
                Toast.makeText(this, "Profile saved!", Toast.LENGTH_SHORT).show()
                finish()
                return@setOnClickListener
            }

            db.collection("users").document(userId)
                .set(userData, com.google.firebase.firestore.SetOptions.merge())
                .addOnSuccessListener {
                    Toast.makeText(this, "Profile saved!", Toast.LENGTH_SHORT).show()
                    finish()
                }
                .addOnFailureListener {
                    Toast.makeText(this, "Error: ${it.message}", Toast.LENGTH_LONG).show()
                }
        }
    }

    private fun setupNavigation() {
        val bottomNav = findViewById<BottomNavigationView>(R.id.bottomNav)
        bottomNav.selectedItemId = R.id.nav_settings
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

## PrivacyPolicyActivity.kt

```kotlin
package com.example.pcostracker

import android.content.Intent
import android.os.Bundle
import android.view.View
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import com.google.android.material.bottomnavigation.BottomNavigationView

class PrivacyPolicyActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_privacy_policy)

        findViewById<TextView>(R.id.backBtn).setOnClickListener { finish() }
        setupPolicyRows()
        setupNavigation()
    }

    private fun setupPolicyRows() {
        bindPolicyRow(
            R.id.privacyCollect,
            "i",
            "Collect only necessary information",
            "We collect only the user and health information needed for PCOS tracking."
        )
        bindPolicyRow(
            R.id.privacyPersonalized,
            "*",
            "Personalized tracking",
            "Your data helps provide personalized PCOS tracking and recommendations."
        )
        bindPolicyRow(
            R.id.privacySecure,
            "#",
            "Secure and protected",
            "We keep user information secure and protected inside the app."
        )
        bindPolicyRow(
            R.id.privacyConsent,
            "!",
            "Consent first",
            "We do not share personal data without user consent."
        )
        bindPolicyRow(
            R.id.privacyControl,
            "<",
            "User control",
            "Users can update or delete their data when requested."
        )
    }

    private fun bindPolicyRow(rowId: Int, icon: String, title: String, body: String) {
        val row = findViewById<View>(rowId)
        row.findViewById<TextView>(R.id.privacyIcon).text = icon
        row.findViewById<TextView>(R.id.privacyTitle).text = title
        row.findViewById<TextView>(R.id.privacyBody).text = body
    }

    private fun setupNavigation() {
        val bottomNav = findViewById<BottomNavigationView>(R.id.bottomNav)
        bottomNav.selectedItemId = R.id.nav_settings
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

## ReportHubActivity.kt

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

## ReportsActivity.kt

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

## settingsActivity.kt

```kotlin
package com.example.pcostracker

import android.content.Intent
import android.net.Uri
import android.os.Bundle
import android.widget.TextView
import android.widget.Toast
import android.widget.LinearLayout
import androidx.appcompat.app.AlertDialog
import androidx.appcompat.app.AppCompatActivity
import androidx.appcompat.widget.SwitchCompat
import com.google.android.material.bottomnavigation.BottomNavigationView
import com.google.android.material.floatingactionbutton.FloatingActionButton
import com.google.firebase.auth.FirebaseAuth
import com.google.firebase.firestore.FirebaseFirestore

class SettingsActivity : AppCompatActivity() {

    private val auth = FirebaseAuth.getInstance()
    private val db = FirebaseFirestore.getInstance()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_settings)

        loadUserProfile()
        setupToggles()
        setupClickListeners()
        setupNavigation()

        findViewById<TextView>(R.id.backBtn).setOnClickListener { finish() }
    }

    private fun loadUserProfile() {
        val savedName = ProfileStore.name(this)
        val savedEmail = ProfileStore.email(this)
        val savedImageUri = ProfileStore.imageUri(this)
        if (savedName.isNotEmpty()) {
            findViewById<TextView>(R.id.settingsUserName).text = savedName
        }
        if (savedEmail.isNotEmpty()) {
            findViewById<TextView>(R.id.settingsUserEmail).text = savedEmail
        }
        if (savedImageUri.isNotEmpty()) {
            findViewById<de.hdodenhof.circleimageview.CircleImageView>(R.id.settingsProfileImage)
                .setImageURI(Uri.parse(savedImageUri))
        }

        val user = auth.currentUser ?: return
        if (savedEmail.isEmpty()) {
            findViewById<TextView>(R.id.settingsUserEmail).text = user.email
        }

        db.collection("users").document(user.uid).get()
            .addOnSuccessListener { doc ->
                val name = doc.getString("fullName") ?: user.displayName ?: "User"
                findViewById<TextView>(R.id.settingsUserName).text = name
            }
    }

    private fun setupToggles() {
        val notifToggle = findViewById<SwitchCompat>(R.id.notifToggle)
        val darkModeToggle = findViewById<SwitchCompat>(R.id.darkModeToggle)

        notifToggle.setOnCheckedChangeListener { _, isChecked ->
            Toast.makeText(this,
                if (isChecked) "Notifications enabled" else "Notifications disabled",
                Toast.LENGTH_SHORT).show()
        }

        darkModeToggle.setOnCheckedChangeListener { _, isChecked ->
            Toast.makeText(this,
                if (isChecked) "Dark mode coming soon!" else "Light mode",
                Toast.LENGTH_SHORT).show()
        }
    }

    private fun setupClickListeners() {
        // Edit Profile
        findViewById<TextView>(R.id.editProfileBtn).setOnClickListener {
            startActivity(Intent(this, PersonalInfoActivity::class.java))
        }
        findViewById<de.hdodenhof.circleimageview.CircleImageView>(R.id.settingsProfileImage).setOnClickListener {
            startActivity(Intent(this, PersonalInfoActivity::class.java))
        }
        findViewById<TextView>(R.id.settingsUserName).setOnClickListener {
            startActivity(Intent(this, PersonalInfoActivity::class.java))
        }
        findViewById<LinearLayout>(R.id.profileSection).setOnClickListener {
            startActivity(Intent(this, PersonalInfoActivity::class.java))
        }

        // Data Backup
        findViewById<androidx.cardview.widget.CardView>(R.id.cardDataBackup).setOnClickListener {
            Toast.makeText(this, "Backup started...", Toast.LENGTH_SHORT).show()
        }

        // Privacy Policy
        findViewById<androidx.cardview.widget.CardView>(R.id.cardPrivacyPolicy).setOnClickListener {
            startActivity(Intent(this, PrivacyPolicyActivity::class.java))
        }

        // Delete History
        findViewById<androidx.cardview.widget.CardView>(R.id.cardDeleteHistory).setOnClickListener {
            AlertDialog.Builder(this)
                .setTitle("Delete History")
                .setMessage("Are you sure you want to delete all history records? This cannot be undone.")
                .setPositiveButton("Delete") { _, _ ->
                    deleteHistory()
                }
                .setNegativeButton("Cancel", null)
                .show()
        }

        // Log Out
        findViewById<androidx.cardview.widget.CardView>(R.id.cardLogOut).setOnClickListener {
            AlertDialog.Builder(this)
                .setTitle("Log Out")
                .setMessage("Are you sure you want to log out?")
                .setPositiveButton("Log Out") { _, _ ->
                    auth.signOut()
                    val intent = Intent(this, LoginActivity::class.java)
                    intent.flags = Intent.FLAG_ACTIVITY_NEW_TASK or Intent.FLAG_ACTIVITY_CLEAR_TASK
                    startActivity(intent)
                }
                .setNegativeButton("Cancel", null)
                .show()
        }

        // FAB
        findViewById<FloatingActionButton>(R.id.fabAdd).setOnClickListener {
            startActivity(Intent(this, AddDataHubActivity::class.java))
        }
    }

    private fun deleteHistory() {
        val userId = auth.currentUser?.uid ?: return
        db.collection("users").document(userId).collection("checkIns")
            .get()
            .addOnSuccessListener { docs ->
                val batch = db.batch()
                docs.forEach { batch.delete(it.reference) }
                batch.commit().addOnSuccessListener {
                    Toast.makeText(this, "History deleted", Toast.LENGTH_SHORT).show()
                }
            }
    }

    private fun setupNavigation() {
        val bottomNav = findViewById<BottomNavigationView>(R.id.bottomNav)
        bottomNav.selectedItemId = R.id.nav_settings
        bottomNav.setOnItemSelectedListener { item ->
            when (item.itemId) {
                R.id.nav_home -> { startActivity(Intent(this, MainActivity::class.java)); true }
                R.id.nav_calendar -> { startActivity(Intent(this, CalendarActivity::class.java)); true }
                R.id.nav_add -> { startActivity(Intent(this, AddDataHubActivity::class.java)); true }
                R.id.nav_reports -> { startActivity(Intent(this, ReportHubActivity::class.java)); true }
                R.id.nav_settings -> true
                else -> false
            }
        }
    }
}

```

## SignUpActivity.kt

```kotlin
package com.example.pcostracker

import android.content.Intent
import android.os.Bundle
import android.view.View
import android.widget.Button
import android.widget.EditText
import android.widget.TextView
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity
import com.google.firebase.auth.FirebaseAuth

class SignUpActivity : AppCompatActivity() {

    private lateinit var auth: FirebaseAuth
    private lateinit var emailInput: EditText
    private lateinit var passwordInput: EditText
    private lateinit var signUpButton: Button
    private lateinit var loginText: TextView
    private lateinit var forgotPassword: TextView

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_login)

        auth = FirebaseAuth.getInstance()

        emailInput = findViewById(R.id.emailInput)
        passwordInput = findViewById(R.id.passwordInput)
        signUpButton = findViewById(R.id.loginButton)
        loginText = findViewById(R.id.signUpText)
        forgotPassword = findViewById(R.id.forgotPassword)

        findViewById<TextView>(R.id.welcomeTitle).text = "Create Account"
        findViewById<TextView>(R.id.welcomeSubtitle).text = "Start tracking your health today"
        signUpButton.text = "Sign Up"
        loginText.text = "Login"
        forgotPassword.visibility = View.GONE

        signUpButton.setOnClickListener {
            val email = emailInput.text.toString().trim()
            val password = passwordInput.text.toString().trim()

            if (validateInputs(email, password)) {
                createAccount(email, password)
            }
        }

        loginText.setOnClickListener {
            startActivity(Intent(this, LoginActivity::class.java))
            finish()
        }
    }

    private fun validateInputs(email: String, password: String): Boolean {
        if (email.isEmpty()) {
            emailInput.error = "Email is required"
            return false
        }
        if (!android.util.Patterns.EMAIL_ADDRESS.matcher(email).matches()) {
            emailInput.error = "Enter a valid email"
            return false
        }
        if (password.isEmpty()) {
            passwordInput.error = "Password is required"
            return false
        }
        if (password.length < 6) {
            passwordInput.error = "Password must be at least 6 characters"
            return false
        }
        return true
    }

    private fun createAccount(email: String, password: String) {
        signUpButton.isEnabled = false
        signUpButton.text = "Creating..."

        auth.createUserWithEmailAndPassword(email, password)
            .addOnCompleteListener(this) { task ->
                signUpButton.isEnabled = true
                signUpButton.text = "Sign Up"

                if (task.isSuccessful) {
                    Toast.makeText(this, "Account created!", Toast.LENGTH_SHORT).show()
                    startActivity(Intent(this, MainActivity::class.java))
                    finish()
                } else {
                    Toast.makeText(
                        this,
                        "Sign up failed: ${task.exception?.message}",
                        Toast.LENGTH_LONG
                    ).show()
                }
            }
    }
}

```

## SplashActivity.kt

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

## TrackerHubActivity.kt

```kotlin
package com.example.pcostracker

import android.content.Intent
import android.os.Bundle
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import androidx.cardview.widget.CardView
import com.google.android.material.bottomnavigation.BottomNavigationView

class TrackerHubActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_tracker_hub)

        findViewById<CardView>(R.id.cardPeriodHub).setOnClickListener {
            startActivity(Intent(this, PeriodTrackingHubActivity::class.java))
        }
        findViewById<CardView>(R.id.cardMealHub).setOnClickListener {
            startActivity(Intent(this, MealTrackingHubActivity::class.java))
        }
        findViewById<CardView>(R.id.cardWorkoutHub).setOnClickListener {
            startActivity(Intent(this, WorkoutWellnessHubActivity::class.java))
        }
        findViewById<CardView>(R.id.cardFinalReportHub).setOnClickListener {
            startActivity(Intent(this, FinalReportActivity::class.java))
        }

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

## WorkoutDetailActivity.kt

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

## WorkoutWellnessActivity.kt

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

## WorkoutWellnessGuideActivity.kt

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

## WorkoutWellnessHubActivity.kt

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

## WorkoutWellnessReportActivity.kt

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

