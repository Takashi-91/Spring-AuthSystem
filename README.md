// Project: APDS7311 ICE Task - User Registration API (Kotlin version)
// Framework: Spring Boot
// Dependencies: Spring Web, Spring Security

// User.kt
package com.example.registration.model

data class User(
    val id: Long,
    val email: String,
    val password: String
)

// EmailValidator.kt
package com.example.registration.util

object EmailValidator {
    private val EMAIL_REGEX = "^[A-Za-z0-9+_.-]+@[A-Za-z0-9.-]+$".toRegex()

    fun isValidEmail(email: String): Boolean {
        return EMAIL_REGEX.matches(email)
    }
}

// PasswordValidator.kt
package com.example.registration.util

object PasswordValidator {
    fun isValidPassword(password: String): Boolean {
        return password.length >= 8 &&
               password.any { it.isUpperCase() } &&
               password.any { it.isDigit() }
    }
}

// UserController.kt
package com.example.registration.controller

import com.example.registration.model.User
import com.example.registration.util.EmailValidator
import com.example.registration.util.PasswordValidator
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder
import org.springframework.web.bind.annotation.*

@RestController
class UserController {

    private val users = mutableListOf<User>()
    private val passwordEncoder = BCryptPasswordEncoder()
    private var userId = 1L

    @PostMapping("/register")
    fun register(@RequestBody request: Map<String, String>): Map<String, String> {
        val email = request["email"] ?: return mapOf("message" to "Email is required.")
        val password = request["password"] ?: return mapOf("message" to "Password is required.")

        if (!EmailValidator.isValidEmail(email)) {
            return mapOf("message" to "Invalid email format.")
        }
        if (!PasswordValidator.isValidPassword(password)) {
            return mapOf("message" to "Password must be at least 8 characters, include 1 number and 1 uppercase letter.")
        }

        val hashedPassword = passwordEncoder.encode(password)
        val newUser = User(userId++, email, hashedPassword)
        users.add(newUser)

        return mapOf("message" to "Registration successful.")
    }
}

// RegistrationApplication.kt
package com.example.registration

import org.springframework.boot.autoconfigure.SpringBootApplication
import org.springframework.boot.runApplication

@SpringBootApplication
class RegistrationApplication

fun main(args: Array<String>) {
    runApplication<RegistrationApplication>(*args)
}

// application.properties (empty for this task)
# src/main/resources/application.properties

// build.gradle.kts (Kotlin DSL) - Add these dependencies
/*
dependencies {
    implementation("org.springframework.boot:spring-boot-starter-web")
    implementation("org.springframework.boot:spring-boot-starter-security")
}
*/
