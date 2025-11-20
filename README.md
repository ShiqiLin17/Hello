327 Final Project README (draft) 
README for ed_solovey_1_fan_club

Overview

This project implements a three-stage workflow for loading, transforming, and grouping person-related data (targeted at the "ed_solovey_1_fan_club" context). It supports multiple data input methods, processes structured Person data, and uses attribute-based clustering to generate customizable group results. The system uses CMake for builds, CLion for development, Qt for the GUI, and GitHub Actions for automated testing.

Tools & Development Environment

The project relies on these core tools:

• GitHub: Version control and automated testing (via the Run Tests pipeline)

• CLion: C/C++ IDE (integrates with CMake/Qt/GTest)

• Qt: GUI framework (handles input selection, parameter configuration, and result export)

• CMake: Build system (version ≥3.10, manages dependencies and targets)

• Google Test (GTest): Testing framework for validating Person data logic and grouping

System Workflow

The system follows three sequential modules:

1. Data Loading: Imports JSON/CSV data or generates random Person entries (via Qt GUI)

2. Data Transformation: Aggregates data into a Vector of People, adjusts entries, and persists to CSV

3. Grouping: Converts Person attributes to numerical vectors and runs clustering (configurable via Qt)

Person Data Attributes (Full Definition)

This table lists the structured fields of the Person object (aligned with the project's data schema):
| Field              | Type       | Description & Examples                                                                 | Special Notes                  |
| :----------------- | :--------- | :------------------------------------------------------------------------------------- | :----------------------------- |
| `id`               | `string`   | Unique identifier (e.g., `"ed"`, `"U123"`, `"123"`, `"person#1"`)                      | —                              |
| `gradYear`         | `int`      | Graduation year (e.g., `2029`, `2026`, `2001`)                                         | Supports `ANY` wildcard        |
| `region`           | `enum`     | Geographic region (e.g., `us-northeast`, discrete selectable values)                    | Primary **grouping** field     |
| `primaryOS`        | `enum`     | Operating system (e.g., `MacOS`, `Windows`, `Other`)                                   | —                              |
| `engFocus`         | `enum`     | Engineering focus (e.g., specific tracks, `Other`)                                      | Primary **grouping** field     |
| `studyTime`        | `enum`     | Study time window (e.g., `Morning`, `Night`, `Afternoon`)                               | Discrete value                 |
| `enrolledCourses`  | `int`      | Number of enrolled courses (range `0–6`)                                                | Supports `ANY` wildcard        |
| `favColors`        | `string`   | Favorite color(s) (e.g., `"blue-silver"`)                                               | Supports `ANY` wildcard        |
| `hobbies`          | `string`   | Interest tags (e.g., `"movies-music"`)                                                 | Supports `ANY` wildcard        |
| `languages`        | `string`   | Spoken languages (e.g., `"english-spanish"`)                                           | Supports `ANY` wildcard        |

CMake Configuration (CMakeLists.txt)

Core Settings

• Project name: ed_solovey_1_fan_club

• Executable name: ed_solovey_1_fan_club

• C++ standard: C++17

• Minimum CMake version: 3.10

Build Targets

• Main executable: ed_solovey_1_fan_club (compiled from src/ source files + main.cpp)

• Test executable: run_tests (compiled from src/ + tests/ files + test_main.cpp, linked to GTest::GTest/GTest::Main/pthread)

Automated Testing (GitHub Actions)

• Trigger: Repository dispatch event with type run-tests

• Test Environment: ubuntu-latest (timeout: 10 minutes)

• Test Logic: Validates Person attribute parsing, data transformation, and grouping algorithm behavior

• Logs: Compilation output → build/build_log.txt; test output → build/test_output.txt

Usage Steps

1. Repository Setup
Clone the project from GitHub, then open the root directory in CLion (CMake configuration is auto-detected).

2. Build the Application
Select the ed_solovey_1_fan_club target in CLion, then run Build (Ctrl+F9).

3. Launch the GUI
Run the ed_solovey_1_fan_club executable (Shift+F10) to open the Qt interface:

◦ Load data via JSON/CSV file or random generation

◦ Adjust the Person dataset (add/remove entries)

◦ Configure grouping parameters (e.g., use region + engFocus as grouping keys)

◦ Export final group results to a file

4. Run Tests

◦ Local: Select the run_tests target in CLion and run (Shift+F10)

◦ Automated: Trigger the Run Tests pipeline via GitHub repository dispatch (type: run-tests)


CVS sample for testing (tests all attributes) 
id,gradYear,region,primaryOS,engFocus,studyTime,enrolledCourses,favColors,hobbies,languages
ed,2026,us-northeast,MacOS,software,Evening,4,blue-silver,movies-music,english
U123,2025,us-southeast,Windows,hardware,Morning,3,red-black,sports-hiking,english-spanish
123,2024,us-west,Other,other,Night,5,green-gold,reading-gaming,french
person#1,ANY,us-midwest,MacOS,software,Afternoon,ANY,purple-white,music,spanish
