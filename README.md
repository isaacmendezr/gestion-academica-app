
# Academic Management System - Android App

<em>Native Android application for managing university academic operations with offline-first architecture and role-based access control.</em>

---

## Table of Contents

- [Academic Management System - Android App](#academic-management-system---android-app)
  - [Table of Contents](#table-of-contents)
  - [Overview](#overview)
  - [Architecture](#architecture)
  - [Features](#features)
  - [Technology Stack](#technology-stack)
  - [User Roles \& Permissions](#user-roles--permissions)
  - [Data Models](#data-models)
  - [Project Structure](#project-structure)
  - [Ecosystem](#ecosystem)
  - [Getting Started](#getting-started)
    - [Prerequisites](#prerequisites)
    - [Installation](#installation)
    - [Configuration](#configuration)
    - [Usage](#usage)
  - [Development Best Practices](#development-best-practices)
  - [Contributing](#contributing)
  - [License](#license)
  - [Contact](#contact)

---

## Overview

**Academic Management System - Android App** is a native Android application built with Kotlin that provides comprehensive academic administration capabilities for universities. The system features offline-first architecture with Room Database, enabling full functionality without internet connectivity, and seamlessly synchronizes with a REST backend when available.

**Key Capabilities:**
- Complete CRUD operations for students, professors, courses, careers, and academic cycles
- Academic offering management with course-group-professor assignments
- Student enrollment system with course validation
- Grade assignment interface for professors
- Academic transcript viewing with detailed course history
- User management with role-based access control (Administrator, Registrar, Professor, Student)
- Offline-first operation with local/remote mode switching

This mobile application communicates with:
- **Backend API**: [SistemaAcademico-Backend](https://github.com/isaacmendezr/SistemaAcademico-Backend) - Spring Boot REST API with Oracle Database integration

---

## Architecture

**Framework:** Android (Kotlin)  
**Architecture Pattern:** MVVM (Model-View-ViewModel)  
**Dependency Injection:** Hilt (Dagger)  
**Navigation:** Jetpack Navigation Component (Single Activity)  
**Local Database:** Room (SQLite)  
**Network Layer:** Retrofit + OkHttp  
**State Management:** Kotlin StateFlow + Coroutines  
**UI Pattern:** ViewBinding + Material Design 3

**Architectural Layers:**
```
UI Layer (Fragments/Activities)
    ↓
ViewModel (Business Logic + State)
    ↓
Repository (Data Abstraction)
    ↓
Data Sources (Remote API + Local Room DB)
```

**Repository Pattern Implementation:**
- Each repository has three implementations:
  - `Interface`: Defines contracts
  - `Remote`: REST API calls via Retrofit
  - `Local`: Room Database operations
  - `Impl`: Selects Remote or Local based on ConfigManager mode

---

## Features

| Category | Description |
| :-------- | :----------- |
| 👥 **Student Management** | Complete CRUD operations, search by ID/name/career, academic history navigation |
| 👨‍🏫 **Professor Management** | Full professor administration with course assignment tracking |
| 📚 **Course Management** | Course catalog with credits, hours, and curriculum planning |
| 🎓 **Career Management** | Academic programs with course-per-cycle associations |
| 📅 **Academic Cycle Management** | Semester/quarter management with activation controls |
| 🗓️ **Academic Offering** | Program course groups by cycle, assign professors and schedules |
| 📝 **Enrollment System** | Student enrollment with duplicate validation and group switching |
| 📊 **Grade Assignment** | Professor interface for grade entry (0-100 scale) |
| 📜 **Academic Transcript** | Complete student history with courses, grades, and professors |
| 👤 **User Management** | Role-based user administration (Admin, Registrar, Professor, Student) |
| 🔐 **Authentication** | Secure login with role validation and session management |
| 📴 **Offline-First** | Full functionality with Room Database, configurable sync mode |
| 🔍 **Search & Filter** | Real-time search across all entity lists |
| ✅ **Validation System** | Entity-specific validators with detailed error messages |
| 🎨 **Material Design UI** | Consistent design with swipe actions and responsive layouts |

---

## Technology Stack

**Core:**
- **Kotlin** - Primary language
- **Gradle** - Build system with Kotlin DSL

**Architecture Components:**
- **Jetpack Navigation** - Single Activity navigation with Safe Args
- **Hilt** - Dependency injection
- **Lifecycle** - ViewModel, LiveData, StateFlow
- **ViewBinding** - Type-safe view access

**Data Layer:**
- **Retrofit** - REST API client
- **OkHttp** - HTTP client with logging interceptor
- **Gson** - JSON serialization
- **Room** - SQLite ORM with KSP code generation

**UI Components:**
- **Material Design** - UI components
- **RecyclerView** - List rendering with DiffUtil
- **ConstraintLayout** - Responsive layouts

**Utilities:**
- **ThreeTen BP** - Date/time handling
- **Kotlin Coroutines** - Asynchronous operations
- **KSP** - Annotation processing

**Configuration:**
- **Compile SDK:** 35
- **Min SDK:** 23 (Android 6.0)
- **Target SDK:** 35
- **Java Version:** 11
- **Base URL:** `http://10.0.2.2:8080/api/` (Android Emulator)

---

## User Roles & Permissions

| Role | Access Level | Capabilities |
|------|--------------|--------------|
| 🔧 **Administrator** | Full access (except grade assignment) | Manage courses, careers, professors, students, cycles, academic offerings, users, view transcripts |
| 📋 **Registrar** | Enrollment only | Enroll students, manage enrollments, change groups |
| 👨‍🏫 **Professor** | Grade assignment only | View assigned groups, enter/edit grades |
| 🎓 **Student** | Read-only (own data) | View personal academic transcript |

**Access Control:**
- Enforced at navigation level (RolePermissions.kt)
- Menu items hidden based on role
- Automatic redirection for unauthorized access attempts
- Session validation on app start

---

## Data Models

**Core Entities (Room + API):**

| Entity | Key Fields | Description |
|--------|-----------|-------------|
| **Student** | idAlumno, cedula, nombre, email, fechaNacimiento, pkCarrera | Student records with career assignment |
| **Professor** | idProfesor, cedula, nombre, email | Faculty information |
| **Course** | idCurso, codigo, nombre, creditos, horasSemanales | Course catalog |
| **Career** | idCarrera, codigo, nombre, titulo | Academic programs |
| **Cycle** | idCiclo, anio, numero, fechaInicio, fechaFin, estado | Academic periods |
| **CareerCourse** | idCarreraCurso, pkCarrera, pkCurso, pkCiclo | Course-career-cycle relationships |
| **Group** | idGrupo, idCarreraCurso, numeroGrupo, horario, idProfesor | Course sections |
| **Enrollment** | idMatricula, pkAlumno, pkGrupo, nota | Student enrollments with grades |
| **User** | idUsuario, cedula, clave, tipo | Authentication records |

**DTOs (Data Transfer Objects):**
- `CursoDto` - Course with extended information
- `GrupoDto` - Group with professor details
- `GrupoProfesorDto` - Professor's groups in active cycle
- `MatriculaAlumnoDto` - Enrollment with full academic context

---

## Project Structure

```sh
GestionAcademicaApp/
├── app/
│   ├── build.gradle.kts                    # App-level build configuration
│   ├── src/
│   │   ├── main/
│   │   │   ├── AndroidManifest.xml
│   │   │   ├── java/com/example/gestionacademicaapp/
│   │   │   │   ├── GestionAcademicaApp.kt  # Application class (@HiltAndroidApp)
│   │   │   │   ├── data/                    # Data Layer
│   │   │   │   │   ├── api/
│   │   │   │   │   │   ├── ApiService.kt   # Retrofit interface (310 endpoints)
│   │   │   │   │   │   └── model/
│   │   │   │   │   │       ├── dto/        # Data Transfer Objects
│   │   │   │   │   │       │   ├── CursoDto.kt
│   │   │   │   │   │       │   ├── GrupoDto.kt
│   │   │   │   │   │       │   ├── GrupoProfesorDto.kt
│   │   │   │   │   │       │   └── MatriculaAlumnoDto.kt
│   │   │   │   │   │       └── [9 Entity models]  # Room entities
│   │   │   │   │   ├── dao/                # Room DAOs (9 interfaces)
│   │   │   │   │   ├── repository/         # Repository implementations
│   │   │   │   │   │   └── [9 Repositories] # Remote, Local, Impl per entity
│   │   │   │   │   └── AppDatabase.kt      # Room database configuration
│   │   │   │   ├── di/
│   │   │   │   │   └── AppModule.kt        # Hilt dependency injection
│   │   │   │   ├── ui/                      # Presentation Layer
│   │   │   │   │   ├── MainActivity.kt     # Single Activity host
│   │   │   │   │   ├── alumnos/            # Student management (Fragment, ViewModel, Adapter)
│   │   │   │   │   ├── carreras/           # Career management + course association
│   │   │   │   │   ├── ciclos/             # Academic cycle management
│   │   │   │   │   ├── cursos/             # Course catalog
│   │   │   │   │   ├── historial_academico/ # Academic transcripts
│   │   │   │   │   ├── inicio/             # Dashboard/home
│   │   │   │   │   ├── login/              # Authentication
│   │   │   │   │   ├── matricula/          # Enrollment system (3 fragments)
│   │   │   │   │   ├── notas/              # Grade assignment
│   │   │   │   │   ├── oferta_academica/   # Academic offering (2 fragments)
│   │   │   │   │   ├── perfil/             # User profile
│   │   │   │   │   ├── profesores/         # Professor management
│   │   │   │   │   ├── usuarios/           # User management
│   │   │   │   │   └── common/
│   │   │   │   │       ├── DialogFormularioFragment.kt # Generic form dialog
│   │   │   │   │       ├── CampoFormulario.kt  # Form field definitions
│   │   │   │   │       ├── adapter/
│   │   │   │   │       │   └── BaseAdapter.kt  # RecyclerView base
│   │   │   │   │       ├── state/
│   │   │   │   │       │   └── UiState.kt      # Loading/Success/Error states
│   │   │   │   │       └── validators/         # 8 entity validators
│   │   │   │   └── utils/
│   │   │   │       ├── ConfigManager.kt    # Local/Remote mode toggle
│   │   │   │       ├── Constants.kt        # Global constants
│   │   │   │       ├── ErrorHandler.kt     # Error mapping
│   │   │   │       ├── Extensions.kt       # Kotlin extensions
│   │   │   │       ├── Notificador.kt      # Snackbar utility
│   │   │   │       ├── ResourceProvider.kt # String resources for ViewModels
│   │   │   │       ├── RolePermissions.kt  # RBAC configuration
│   │   │   │       ├── SearchViewUtils.kt  # Search standardization
│   │   │   │       └── SessionManager.kt   # User session management
│   │   │   └── res/
│   │   │       ├── drawable/               # 25+ vector drawables
│   │   │       ├── layout/                 # 35+ XML layouts
│   │   │       ├── menu/                   # Navigation menus
│   │   │       ├── navigation/
│   │   │       │   └── mobile_navigation.xml # Nav graph (232 lines)
│   │   │       ├── values/
│   │   │       │   ├── strings.xml         # 256+ string resources
│   │   │       │   ├── colors.xml
│   │   │       │   └── themes.xml
│   │   │       └── xml/
│   │   │           └── network_security_config.xml
│   │   └── test/                           # Unit tests
│   └── proguard-rules.pro
├── gradle/
│   ├── libs.versions.toml                  # Version catalog
│   └── wrapper/
├── build.gradle.kts                        # Root build configuration
├── settings.gradle.kts
└── README.md
```

---

## Ecosystem

The **Academic Management System - Android App** is part of an integrated application ecosystem:

| Component | Description | Repository |
|------------|-------------|-------------|
| 📱 **Android App** | Native mobile client with offline-first architecture | *This repository* |
| 💻 **Backend API** | Spring Boot REST API with Oracle DB and stored procedures | [SistemaAcademico-Backend](https://github.com/isaacmendezr/SistemaAcademico-Backend) |

**Integration Points:**
- REST API communication via Retrofit
- Shared data models and validation rules
- Offline synchronization capability
- JWT/Session-based authentication (planned)

---

## Getting Started

### Prerequisites

- **Android Studio** Ladybug | 2024.2.1 or later
- **JDK** 11 or later
- **Android SDK** 35 (compileSdk)
- **Gradle** 8.10.1+
- **Backend API** - [SistemaAcademico-Backend](https://github.com/isaacmendezr/SistemaAcademico-Backend) running on `localhost:8080`

### Installation

1. Clone the repository:
   ```sh
   git clone https://github.com/isaacmendezr/gestion-academica-app.git
   cd gestion-academica-app
   ```

2. Open the project in Android Studio:
   ```sh
   # From Android Studio: File > Open > Select project directory
   ```

3. Sync Gradle dependencies:
   ```sh
   # Android Studio will automatically prompt to sync
   # Or manually: File > Sync Project with Gradle Files
   ```

4. Build the project:
   ```sh
   ./gradlew build
   ```

### Configuration

1. **Backend URL Configuration:**
   
   Edit `app/src/main/java/com/example/gestionacademicaapp/utils/Constants.kt`:
   ```kotlin
   const val BASE_URL = "http://10.0.2.2:8080/api/"  // For Android Emulator
   // Or use your actual backend URL:
   // const val BASE_URL = "http://your-backend-url:8080/api/"
   ```

2. **Network Security (For API development):**
   
   The app includes `network_security_config.xml` to allow cleartext traffic for local development.

3. **Database Pre-population:**
   
   The Room database is pre-populated with test data on first run:
   - 8 test users (1 admin, 1 registrar, 3 professors, 3 students)
   - 3 careers, 6 courses, 2 cycles
   - 6 groups, 8 enrollments

   **Test Credentials:**
   ```
   Admin:       Cedula: 500500500, Password: admin123
   Registrar:   Cedula: 600600600, Password: matri123
   Professor:   Cedula: 100100100, Password: prof123
   Student:     Cedula: 111111111, Password: alumno1
   ```

### Usage

1. **Run on Emulator:**
   ```sh
   # In Android Studio: Run > Run 'app'
   # Or via command line:
   ./gradlew installDebug
   ```

2. **Run on Physical Device:**
   - Enable USB Debugging on your Android device
   - Connect device via USB
   - Select device in Android Studio and run

3. **Switch between Local/Remote modes:**
   - On login screen, use the toggle switch
   - **Local Mode**: Uses Room Database (offline)
   - **Remote Mode**: Connects to backend API

4. **Initial Login:**
   ```
   Username: 500500500 (Admin)
   Password: admin123
   ```

5. **Test the API connection:**
   - Ensure backend is running on `http://localhost:8080`
   - Switch to Remote mode
   - Login with test credentials
   - Verify data synchronization

---

## Development Best Practices

**Code Quality:**
- ✅ MVVM architecture with clear separation of concerns
- ✅ Repository pattern for data abstraction
- ✅ Dependency injection with Hilt
- ✅ Kotlin Coroutines for asynchronous operations
- ✅ Sealed classes for UI state management
- ✅ Extension functions for code reusability

**UI/UX:**
- ✅ Material Design 3 components
- ✅ Consistent color scheme and typography
- ✅ Responsive layouts with ConstraintLayout
- ✅ RecyclerView with DiffUtil for performance
- ✅ Swipe gestures for edit/delete actions
- ✅ Real-time search with debouncing
- ✅ Loading states and error handling

**Data Management:**
- ✅ Offline-first with Room Database
- ✅ Configurable sync strategy
- ✅ Entity validators for data integrity
- ✅ Generic form dialog for CRUD operations
- ✅ Pre-populated test data

**Security:**
- ✅ Role-based access control (RBAC)
- ✅ Session management with SharedPreferences
- ✅ Input validation on all forms
- ✅ Network security configuration

---

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## License

This project is part of an academic system developed for educational purposes.

---

## Contact

**Developer:** Isaac Méndez  
**Repository:** [gestion-academica-app](https://github.com/isaacmendezr/gestion-academica-app)  
**Backend Repository:** [SistemaAcademico-Backend](https://github.com/isaacmendezr/SistemaAcademico-Backend)

---

**Note:** This Android application requires the backend API to be running for full functionality in Remote mode. Local mode provides complete offline operation with pre-populated test data.
