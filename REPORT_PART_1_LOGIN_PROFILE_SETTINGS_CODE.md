# Part 1 - Login, Profile, Privacy and Settings Screens

This section contains separate screen documentation and code for the requested report part.

## Login Screen

**Purpose:** Allows registered users to enter email and password and sign in to the app.

**Activity File:** `LoginActivity.kt`  
**Layout File:** `activity_login.xml`

### Kotlin Activity Code

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

### XML Layout Code

```xml
<?xml version="1.0" encoding="utf-8"?>
<ScrollView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#F0EEF8"
    android:fillViewport="true">

    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="24dp">

        <!-- Heart Icon Box -->
        <FrameLayout
            android:id="@+id/iconContainer"
            android:layout_width="60dp"
            android:layout_height="60dp"
            android:background="@drawable/rounded_icon_bg"
            android:layout_marginTop="80dp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent">

            <ImageView
                android:layout_width="32dp"
                android:layout_height="32dp"
                android:layout_gravity="center"
                android:src="@drawable/ic_heart"
                app:tint="#8B7BB5"/>
        </FrameLayout>

        <TextView
            android:id="@+id/welcomeTitle"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Welcome Back"
            android:textSize="26sp"
            android:textStyle="bold"
            android:textColor="#2D2D2D"
            android:layout_marginTop="16dp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@+id/iconContainer"/>

        <TextView
            android:id="@+id/welcomeSubtitle"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Track your health with peace of mind"
            android:textSize="14sp"
            android:textColor="#888888"
            android:layout_marginTop="8dp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@+id/welcomeTitle"/>

        <!-- Email Field -->
        <TextView
            android:id="@+id/emailLabel"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Email"
            android:textSize="14sp"
            android:textColor="#2D2D2D"
            android:layout_marginTop="40dp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@+id/welcomeSubtitle"/>

        <EditText
            android:id="@+id/emailInput"
            android:layout_width="match_parent"
            android:layout_height="56dp"
            android:hint="hello@example.com"
            android:inputType="textEmailAddress"
            android:background="@drawable/input_background"
            android:paddingStart="16dp"
            android:paddingEnd="16dp"
            android:textSize="14sp"
            android:layout_marginTop="8dp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@+id/emailLabel"/>

        <!-- Password Field -->
        <TextView
            android:id="@+id/passwordLabel"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Password"
            android:textSize="14sp"
            android:textColor="#2D2D2D"
            android:layout_marginTop="16dp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@+id/emailInput"/>

        <EditText
            android:id="@+id/passwordInput"
            android:layout_width="match_parent"
            android:layout_height="56dp"
            android:hint="••••••••"
            android:inputType="textPassword"
            android:background="@drawable/input_background"
            android:paddingStart="16dp"
            android:paddingEnd="16dp"
            android:textSize="14sp"
            android:layout_marginTop="8dp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@+id/passwordLabel"/>

        <!-- Forgot Password -->
        <TextView
            android:id="@+id/forgotPassword"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Forgot Password?"
            android:textColor="#8B7BB5"
            android:textSize="13sp"
            android:layout_marginTop="8dp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintTop_toBottomOf="@+id/passwordInput"/>

        <!-- Login Button -->
        <Button
            android:id="@+id/loginButton"
            android:layout_width="match_parent"
            android:layout_height="56dp"
            android:text="Login"
            android:textColor="#FFFFFF"
            android:textSize="16sp"
            android:background="@drawable/button_background"
            android:layout_marginTop="24dp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@+id/forgotPassword"/>

        <!-- Sign Up -->
        <LinearLayout
            android:id="@+id/signUpContainer"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:orientation="horizontal"
            android:layout_marginTop="24dp"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@+id/loginButton">

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Don't have an account? "
                android:textColor="#888888"
                android:textSize="14sp"/>

            <TextView
                android:id="@+id/signUpText"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Sign Up"
                android:textColor="#8B7BB5"
                android:textStyle="bold"
                android:textSize="14sp"/>
        </LinearLayout>

    </androidx.constraintlayout.widget.ConstraintLayout>
</ScrollView>
```

---

## Sign Up Screen

**Purpose:** Allows a new user to create an account and access the app.

**Activity File:** `SignUpActivity.kt`  
**Layout File:** `activity_login.xml`

### Kotlin Activity Code

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

### XML Layout Code

```xml
<?xml version="1.0" encoding="utf-8"?>
<ScrollView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#F0EEF8"
    android:fillViewport="true">

    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="24dp">

        <!-- Heart Icon Box -->
        <FrameLayout
            android:id="@+id/iconContainer"
            android:layout_width="60dp"
            android:layout_height="60dp"
            android:background="@drawable/rounded_icon_bg"
            android:layout_marginTop="80dp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent">

            <ImageView
                android:layout_width="32dp"
                android:layout_height="32dp"
                android:layout_gravity="center"
                android:src="@drawable/ic_heart"
                app:tint="#8B7BB5"/>
        </FrameLayout>

        <TextView
            android:id="@+id/welcomeTitle"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Welcome Back"
            android:textSize="26sp"
            android:textStyle="bold"
            android:textColor="#2D2D2D"
            android:layout_marginTop="16dp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@+id/iconContainer"/>

        <TextView
            android:id="@+id/welcomeSubtitle"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Track your health with peace of mind"
            android:textSize="14sp"
            android:textColor="#888888"
            android:layout_marginTop="8dp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@+id/welcomeTitle"/>

        <!-- Email Field -->
        <TextView
            android:id="@+id/emailLabel"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Email"
            android:textSize="14sp"
            android:textColor="#2D2D2D"
            android:layout_marginTop="40dp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@+id/welcomeSubtitle"/>

        <EditText
            android:id="@+id/emailInput"
            android:layout_width="match_parent"
            android:layout_height="56dp"
            android:hint="hello@example.com"
            android:inputType="textEmailAddress"
            android:background="@drawable/input_background"
            android:paddingStart="16dp"
            android:paddingEnd="16dp"
            android:textSize="14sp"
            android:layout_marginTop="8dp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@+id/emailLabel"/>

        <!-- Password Field -->
        <TextView
            android:id="@+id/passwordLabel"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Password"
            android:textSize="14sp"
            android:textColor="#2D2D2D"
            android:layout_marginTop="16dp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@+id/emailInput"/>

        <EditText
            android:id="@+id/passwordInput"
            android:layout_width="match_parent"
            android:layout_height="56dp"
            android:hint="••••••••"
            android:inputType="textPassword"
            android:background="@drawable/input_background"
            android:paddingStart="16dp"
            android:paddingEnd="16dp"
            android:textSize="14sp"
            android:layout_marginTop="8dp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@+id/passwordLabel"/>

        <!-- Forgot Password -->
        <TextView
            android:id="@+id/forgotPassword"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Forgot Password?"
            android:textColor="#8B7BB5"
            android:textSize="13sp"
            android:layout_marginTop="8dp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintTop_toBottomOf="@+id/passwordInput"/>

        <!-- Login Button -->
        <Button
            android:id="@+id/loginButton"
            android:layout_width="match_parent"
            android:layout_height="56dp"
            android:text="Login"
            android:textColor="#FFFFFF"
            android:textSize="16sp"
            android:background="@drawable/button_background"
            android:layout_marginTop="24dp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@+id/forgotPassword"/>

        <!-- Sign Up -->
        <LinearLayout
            android:id="@+id/signUpContainer"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:orientation="horizontal"
            android:layout_marginTop="24dp"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@+id/loginButton">

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Don't have an account? "
                android:textColor="#888888"
                android:textSize="14sp"/>

            <TextView
                android:id="@+id/signUpText"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Sign Up"
                android:textColor="#8B7BB5"
                android:textStyle="bold"
                android:textSize="14sp"/>
        </LinearLayout>

    </androidx.constraintlayout.widget.ConstraintLayout>
</ScrollView>
```

---

## Personal Information

**Purpose:** Allows users to edit full name, email, DOB, goals, and upload profile image.

**Activity File:** `PersonalInfoActivity.kt`  
**Layout File:** `activity_personal_info.xml`

### Kotlin Activity Code

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

            <!-- Header with back button -->
            <androidx.constraintlayout.widget.ConstraintLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="24dp">

                <TextView
                    android:id="@+id/backBtn"
                    android:layout_width="36dp"
                    android:layout_height="36dp"
                    android:text="‹"
                    android:textSize="22sp"
                    android:gravity="center"
                    android:textColor="#2D2D2D"
                    android:background="@drawable/nav_button_bg"
                    app:layout_constraintStart_toStartOf="parent"
                    app:layout_constraintTop_toTopOf="parent"
                    app:layout_constraintBottom_toBottomOf="parent"/>

                <TextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="Personal Information"
                    android:textSize="18sp"
                    android:textStyle="bold"
                    android:textColor="#2D2D2D"
                    app:layout_constraintStart_toStartOf="parent"
                    app:layout_constraintEnd_toEndOf="parent"
                    app:layout_constraintTop_toTopOf="parent"
                    app:layout_constraintBottom_toBottomOf="parent"/>

            </androidx.constraintlayout.widget.ConstraintLayout>

            <!-- Profile Image with Edit -->
            <FrameLayout
                android:layout_width="90dp"
                android:layout_height="90dp"
                android:layout_gravity="center_horizontal"
                android:layout_marginBottom="28dp">

                <de.hdodenhof.circleimageview.CircleImageView
                    android:id="@+id/profileImageEdit"
                    android:layout_width="80dp"
                    android:layout_height="80dp"
                    android:src="@drawable/ic_profile_placeholder"
                    app:civ_border_width="2dp"
                    app:civ_border_color="#8B7BB5"/>

                <TextView
                    android:id="@+id/editImageBtn"
                    android:layout_width="26dp"
                    android:layout_height="26dp"
                    android:text="✏️"
                    android:textSize="12sp"
                    android:gravity="center"
                    android:background="@drawable/edit_image_btn_bg"
                    android:layout_gravity="bottom|end"/>

            </FrameLayout>

            <!-- Full Name -->
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Full Name"
                android:textSize="13sp"
                android:textColor="#555555"
                android:layout_marginBottom="6dp"
                android:layout_marginStart="4dp"/>

            <EditText
                android:id="@+id/fullNameInput"
                android:layout_width="match_parent"
                android:layout_height="56dp"
                android:hint="Enter your name"
                android:inputType="textPersonName"
                android:background="@drawable/input_background"
                android:paddingStart="16dp"
                android:paddingEnd="16dp"
                android:textSize="15sp"
                android:textColor="#2D2D2D"
                android:layout_marginBottom="16dp"/>

            <!-- Email -->
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Email Address"
                android:textSize="13sp"
                android:textColor="#555555"
                android:layout_marginBottom="6dp"
                android:layout_marginStart="4dp"/>

            <EditText
                android:id="@+id/emailInput"
                android:layout_width="match_parent"
                android:layout_height="56dp"
                android:hint="Enter your email"
                android:inputType="textEmailAddress"
                android:background="@drawable/input_background"
                android:paddingStart="16dp"
                android:paddingEnd="16dp"
                android:textSize="15sp"
                android:textColor="#2D2D2D"
                android:layout_marginBottom="16dp"/>

            <!-- Date of Birth -->
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Date of Birth"
                android:textSize="13sp"
                android:textColor="#555555"
                android:layout_marginBottom="6dp"
                android:layout_marginStart="4dp"/>

            <androidx.constraintlayout.widget.ConstraintLayout
                android:layout_width="match_parent"
                android:layout_height="56dp"
                android:background="@drawable/input_background"
                android:layout_marginBottom="16dp">

                <TextView
                    android:id="@+id/dobText"
                    android:layout_width="0dp"
                    android:layout_height="wrap_content"
                    android:hint="DD.MM.YYYY"
                    android:textSize="15sp"
                    android:textColor="#2D2D2D"
                    android:paddingStart="16dp"
                    app:layout_constraintStart_toStartOf="parent"
                    app:layout_constraintTop_toTopOf="parent"
                    app:layout_constraintBottom_toBottomOf="parent"
                    app:layout_constraintEnd_toStartOf="@+id/calendarIcon"/>

                <TextView
                    android:id="@+id/calendarIcon"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="📅"
                    android:textSize="18sp"
                    android:paddingEnd="16dp"
                    app:layout_constraintEnd_toEndOf="parent"
                    app:layout_constraintTop_toTopOf="parent"
                    app:layout_constraintBottom_toBottomOf="parent"/>

            </androidx.constraintlayout.widget.ConstraintLayout>

            <!-- My Goal -->
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="My Goal"
                android:textSize="13sp"
                android:textColor="#555555"
                android:layout_marginBottom="6dp"
                android:layout_marginStart="4dp"/>

            <androidx.constraintlayout.widget.ConstraintLayout
                android:id="@+id/goalDropdown"
                android:layout_width="match_parent"
                android:layout_height="56dp"
                android:background="@drawable/input_background"
                android:layout_marginBottom="32dp">

                <TextView
                    android:id="@+id/goalText"
                    android:layout_width="0dp"
                    android:layout_height="wrap_content"
                    android:text="2 Selected"
                    android:textSize="15sp"
                    android:textColor="#2D2D2D"
                    android:paddingStart="16dp"
                    app:layout_constraintStart_toStartOf="parent"
                    app:layout_constraintTop_toTopOf="parent"
                    app:layout_constraintBottom_toBottomOf="parent"
                    app:layout_constraintEnd_toStartOf="@+id/dropdownIcon"/>

                <TextView
                    android:id="@+id/dropdownIcon"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="⌄"
                    android:textSize="20sp"
                    android:textColor="#8B7BB5"
                    android:paddingEnd="16dp"
                    app:layout_constraintEnd_toEndOf="parent"
                    app:layout_constraintTop_toTopOf="parent"
                    app:layout_constraintBottom_toBottomOf="parent"/>

            </androidx.constraintlayout.widget.ConstraintLayout>

            <!-- Goal Options (hidden by default) -->
            <LinearLayout
                android:id="@+id/goalOptionsContainer"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:orientation="vertical"
                android:visibility="gone"
                android:background="@drawable/input_background"
                android:padding="12dp"
                android:layout_marginTop="-24dp"
                android:layout_marginBottom="24dp">

                <CheckBox android:id="@+id/goalTrackCycle"
                    android:layout_width="wrap_content" android:layout_height="wrap_content"
                    android:text="Track Cycle" android:textColor="#2D2D2D"
                    android:buttonTint="#8B7BB5"/>
                <CheckBox android:id="@+id/goalManageSymptoms"
                    android:layout_width="wrap_content" android:layout_height="wrap_content"
                    android:text="Manage Symptoms" android:textColor="#2D2D2D"
                    android:buttonTint="#8B7BB5"/>
                <CheckBox android:id="@+id/goalImproveNutrition"
                    android:layout_width="wrap_content" android:layout_height="wrap_content"
                    android:text="Improve Nutrition" android:textColor="#2D2D2D"
                    android:buttonTint="#8B7BB5"/>
                <CheckBox android:id="@+id/goalStayActive"
                    android:layout_width="wrap_content" android:layout_height="wrap_content"
                    android:text="Stay Active" android:textColor="#2D2D2D"
                    android:buttonTint="#8B7BB5"/>
            </LinearLayout>

            <!-- Save Button -->
            <Button
                android:id="@+id/saveChangesBtn"
                android:layout_width="match_parent"
                android:layout_height="56dp"
                android:text="Save Changes"
                android:textColor="#FFFFFF"
                android:textSize="16sp"
                android:background="@drawable/button_background"/>

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

## Privacy Policy

**Purpose:** Shows how the app collects, protects, and uses personal/health information.

**Activity File:** `PrivacyPolicyActivity.kt`  
**Layout File:** `activity_privacy_policy.xml`

### Kotlin Activity Code

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
        android:paddingBottom="86dp">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="20dp">

            <androidx.constraintlayout.widget.ConstraintLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="18dp">

                <TextView
                    android:id="@+id/backBtn"
                    android:layout_width="44dp"
                    android:layout_height="44dp"
                    android:gravity="center"
                    android:text="&lt;"
                    android:textColor="#2D2D2D"
                    android:textSize="24sp"
                    app:layout_constraintBottom_toBottomOf="parent"
                    app:layout_constraintStart_toStartOf="parent"
                    app:layout_constraintTop_toTopOf="parent"/>

                <TextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="Privacy Policy"
                    android:textColor="#2D2D2D"
                    android:textSize="24sp"
                    android:textStyle="bold"
                    app:layout_constraintBottom_toBottomOf="parent"
                    app:layout_constraintStart_toEndOf="@id/backBtn"
                    app:layout_constraintTop_toTopOf="parent"/>
            </androidx.constraintlayout.widget.ConstraintLayout>

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_marginBottom="18dp"
                android:text="How PCOS Tracker protects your information"
                android:textColor="#888888"
                android:textSize="13sp"/>

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
                        android:padding="10dp"
                        android:scaleType="centerInside"
                        android:src="@drawable/privacy_policy_image" />

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_gravity="bottom|start"
                        android:layout_margin="18dp"
                        android:text="Your data stays protected"
                        android:textColor="#2D2D2D"
                        android:textSize="20sp"
                        android:textStyle="bold" />
                </FrameLayout>
            </androidx.cardview.widget.CardView>

            <androidx.cardview.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="12dp"
                app:cardBackgroundColor="#FFFFFF"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp">

                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:orientation="vertical"
                    android:padding="18dp">

                    <include layout="@layout/item_privacy_policy_row"
                        android:id="@+id/privacyCollect"/>
                    <include layout="@layout/item_privacy_policy_row"
                        android:id="@+id/privacyPersonalized"/>
                    <include layout="@layout/item_privacy_policy_row"
                        android:id="@+id/privacySecure"/>
                    <include layout="@layout/item_privacy_policy_row"
                        android:id="@+id/privacyConsent"/>
                    <include layout="@layout/item_privacy_policy_row"
                        android:id="@+id/privacyControl"/>
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

## Settings

**Purpose:** Allows access to profile, notification preferences, privacy policy, data backup, delete history, and logout.

**Activity File:** `settingsActivity.kt`  
**Layout File:** `activity_settings.xml`

### Kotlin Activity Code

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
                android:layout_marginBottom="24dp">

                <TextView
                    android:id="@+id/backBtn"
                    android:layout_width="44dp"
                    android:layout_height="44dp"
                    android:gravity="center"
                    android:text="&lt;"
                    android:textColor="#2D2D2D"
                    android:textSize="24sp"
                    app:layout_constraintBottom_toBottomOf="parent"
                    app:layout_constraintStart_toStartOf="parent"
                    app:layout_constraintTop_toTopOf="parent"/>

                <TextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="Settings"
                    android:textSize="24sp"
                    android:textStyle="bold"
                    android:textColor="#2D2D2D"
                    app:layout_constraintBottom_toBottomOf="parent"
                    app:layout_constraintStart_toEndOf="@id/backBtn"
                    app:layout_constraintTop_toTopOf="parent"/>

            </androidx.constraintlayout.widget.ConstraintLayout>

            <!-- Profile Section -->
            <LinearLayout
                android:id="@+id/profileSection"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:orientation="vertical"
                android:gravity="center"
                android:layout_marginBottom="28dp">

                <de.hdodenhof.circleimageview.CircleImageView
                    android:id="@+id/settingsProfileImage"
                    android:layout_width="80dp"
                    android:layout_height="80dp"
                    android:src="@drawable/ic_profile_placeholder"
                    app:civ_border_width="2dp"
                    app:civ_border_color="#8B7BB5"
                    android:layout_marginBottom="12dp"/>

                <TextView
                    android:id="@+id/settingsUserName"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="Sarah Johnson"
                    android:textSize="18sp"
                    android:textStyle="bold"
                    android:textColor="#2D2D2D"/>

                <TextView
                    android:id="@+id/settingsUserEmail"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="sarah.j@example.com"
                    android:textSize="13sp"
                    android:textColor="#888888"
                    android:layout_marginBottom="12dp"/>

                <TextView
                    android:id="@+id/editProfileBtn"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="Edit Profile"
                    android:textSize="13sp"
                    android:textColor="#8B7BB5"
                    android:background="@drawable/outline_button_bg"
                    android:paddingStart="20dp"
                    android:paddingEnd="20dp"
                    android:paddingTop="6dp"
                    android:paddingBottom="6dp"/>

            </LinearLayout>

            <!-- PREFERENCES Label -->
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="PREFERENCES"
                android:textSize="11sp"
                android:textColor="#AAAAAA"
                android:letterSpacing="0.1"
                android:layout_marginBottom="10dp"/>

            <!-- Notifications -->
            <androidx.cardview.widget.CardView
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
                        android:id="@+id/notifIcon"
                        android:layout_width="36dp"
                        android:layout_height="36dp"
                        android:text="🔔"
                        android:textSize="18sp"
                        android:gravity="center"
                        android:background="@drawable/icon_bg_purple_light"
                        app:layout_constraintStart_toStartOf="parent"
                        app:layout_constraintTop_toTopOf="parent"
                        app:layout_constraintBottom_toBottomOf="parent"/>

                    <TextView
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:text="Notifications"
                        android:textSize="15sp"
                        android:textColor="#2D2D2D"
                        android:layout_marginStart="12dp"
                        app:layout_constraintStart_toEndOf="@id/notifIcon"
                        app:layout_constraintTop_toTopOf="parent"
                        app:layout_constraintBottom_toBottomOf="parent"
                        app:layout_constraintEnd_toStartOf="@+id/notifToggle"/>

                    <androidx.appcompat.widget.SwitchCompat
                        android:id="@+id/notifToggle"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:checked="true"
                        app:thumbTint="#FFFFFF"
                        app:trackTint="@color/switch_track_color"
                        app:layout_constraintEnd_toEndOf="parent"
                        app:layout_constraintTop_toTopOf="parent"
                        app:layout_constraintBottom_toBottomOf="parent"/>

                </androidx.constraintlayout.widget.ConstraintLayout>
            </androidx.cardview.widget.CardView>

            <!-- Dark Mode -->
            <androidx.cardview.widget.CardView
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
                        android:id="@+id/darkModeIcon"
                        android:layout_width="36dp"
                        android:layout_height="36dp"
                        android:text="🌙"
                        android:textSize="18sp"
                        android:gravity="center"
                        android:background="@drawable/icon_bg_purple_light"
                        app:layout_constraintStart_toStartOf="parent"
                        app:layout_constraintTop_toTopOf="parent"
                        app:layout_constraintBottom_toBottomOf="parent"/>

                    <TextView
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:text="Dark Mode"
                        android:textSize="15sp"
                        android:textColor="#2D2D2D"
                        android:layout_marginStart="12dp"
                        app:layout_constraintStart_toEndOf="@id/darkModeIcon"
                        app:layout_constraintTop_toTopOf="parent"
                        app:layout_constraintBottom_toBottomOf="parent"
                        app:layout_constraintEnd_toStartOf="@+id/darkModeToggle"/>

                    <androidx.appcompat.widget.SwitchCompat
                        android:id="@+id/darkModeToggle"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:checked="false"
                        app:thumbTint="#FFFFFF"
                        app:trackTint="@color/switch_track_color"
                        app:layout_constraintEnd_toEndOf="parent"
                        app:layout_constraintTop_toTopOf="parent"
                        app:layout_constraintBottom_toBottomOf="parent"/>

                </androidx.constraintlayout.widget.ConstraintLayout>
            </androidx.cardview.widget.CardView>

            <!-- DATA & PRIVACY Label -->
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="DATA &amp; PRIVACY"
                android:textSize="11sp"
                android:textColor="#AAAAAA"
                android:letterSpacing="0.1"
                android:layout_marginBottom="10dp"/>

            <!-- Data Backup -->
            <androidx.cardview.widget.CardView
                android:id="@+id/cardDataBackup"
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
                        android:id="@+id/backupIcon"
                        android:layout_width="36dp"
                        android:layout_height="36dp"
                        android:text="💾"
                        android:textSize="18sp"
                        android:gravity="center"
                        android:background="@drawable/icon_bg_purple_light"
                        app:layout_constraintStart_toStartOf="parent"
                        app:layout_constraintTop_toTopOf="parent"
                        app:layout_constraintBottom_toBottomOf="parent"/>

                    <TextView
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:text="Data Backup"
                        android:textSize="15sp"
                        android:textColor="#2D2D2D"
                        android:layout_marginStart="12dp"
                        app:layout_constraintStart_toEndOf="@id/backupIcon"
                        app:layout_constraintTop_toTopOf="parent"
                        app:layout_constraintBottom_toBottomOf="parent"
                        app:layout_constraintEnd_toStartOf="@+id/arrowBackup"/>

                    <TextView
                        android:id="@+id/arrowBackup"
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

            <!-- Privacy Policy -->
            <androidx.cardview.widget.CardView
                android:id="@+id/cardPrivacyPolicy"
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
                        android:id="@+id/privacyIcon"
                        android:layout_width="36dp"
                        android:layout_height="36dp"
                        android:text="🔒"
                        android:textSize="18sp"
                        android:gravity="center"
                        android:background="@drawable/icon_bg_purple_light"
                        app:layout_constraintStart_toStartOf="parent"
                        app:layout_constraintTop_toTopOf="parent"
                        app:layout_constraintBottom_toBottomOf="parent"/>

                    <TextView
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:text="Privacy Policy"
                        android:textSize="15sp"
                        android:textColor="#2D2D2D"
                        android:layout_marginStart="12dp"
                        app:layout_constraintStart_toEndOf="@id/privacyIcon"
                        app:layout_constraintTop_toTopOf="parent"
                        app:layout_constraintBottom_toBottomOf="parent"
                        app:layout_constraintEnd_toStartOf="@+id/arrowPrivacy"/>

                    <TextView
                        android:id="@+id/arrowPrivacy"
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

            <!-- Delete History -->
            <androidx.cardview.widget.CardView
                android:id="@+id/cardDeleteHistory"
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
                        android:id="@+id/deleteIcon"
                        android:layout_width="36dp"
                        android:layout_height="36dp"
                        android:text="🗑️"
                        android:textSize="18sp"
                        android:gravity="center"
                        android:background="@drawable/icon_bg_red_light"
                        app:layout_constraintStart_toStartOf="parent"
                        app:layout_constraintTop_toTopOf="parent"
                        app:layout_constraintBottom_toBottomOf="parent"/>

                    <TextView
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:text="Delete History Records"
                        android:textSize="15sp"
                        android:textColor="#2D2D2D"
                        android:layout_marginStart="12dp"
                        app:layout_constraintStart_toEndOf="@id/deleteIcon"
                        app:layout_constraintTop_toTopOf="parent"
                        app:layout_constraintBottom_toBottomOf="parent"
                        app:layout_constraintEnd_toStartOf="@+id/arrowDelete"/>

                    <TextView
                        android:id="@+id/arrowDelete"
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

            <!-- Log Out -->
            <androidx.cardview.widget.CardView
                android:id="@+id/cardLogOut"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                app:cardCornerRadius="16dp"
                app:cardElevation="0dp"
                app:cardBackgroundColor="#FFF0F0">

                <androidx.constraintlayout.widget.ConstraintLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:padding="16dp">

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:text="⬅️  Log Out"
                        android:textSize="15sp"
                        android:textColor="#E57373"
                        android:textStyle="bold"
                        app:layout_constraintStart_toStartOf="parent"
                        app:layout_constraintEnd_toEndOf="parent"
                        app:layout_constraintTop_toTopOf="parent"
                        app:layout_constraintBottom_toBottomOf="parent"/>

                </androidx.constraintlayout.widget.ConstraintLayout>
            </androidx.cardview.widget.CardView>

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

