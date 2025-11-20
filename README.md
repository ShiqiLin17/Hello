Study Group Matcher

A robust C++17 application designed to generate high-cohesion study groups from multiple data sources, with customizable grouping logic and actionable cohesion scoring. Ideal for educators, academic administrators, or students seeking to form balanced, collaborative teams based on shared attributes.
📋 Detailed App Functionality

Study Group Matcher streamlines the process of creating study groups by leveraging data-driven similarity scoring and flexible data integration. Below is a breakdown of core features:

1. Multi-Source Data Integration

The app supports three distinct data inputs to accommodate different use cases:

• Simulated Data: Generates statistically realistic student profiles (2–500 students) with weighted distributions for attributes like engineering focus (e.g., 25% computer systems, 20% cybersecurity), study time (45% afternoon, 35% evening), and region (30% US Northeast, 25% US West). Includes correlations (e.g., cybersecurity students often speak Russian/Mandarin).

• CSV File Import: Loads student data from local/network CSV files with a predefined schema (supports ANY wildcards for flexible matching and - delimited multi-value fields like hobbies/languages).

• Public JSON URL Fetch: Parses student data from public HTTP/HTTPS JSON endpoints (e.g., GitHub Gists, APIs) with automatic SSL verification, redirect following, and 15-second timeout for reliability.

2. Customizable Grouping Logic

• Default Mode (Optimal for Study Groups): Prioritizes attributes critical for collaboration with weighted scoring:

◦ Language (40%): Shared communication language.

◦ Engineering Focus (30%): Aligned academic/professional interests.

◦ Study Time (20%): Overlapping availability.

◦ Course Load (10%): Similar workload (±1 course).

• Custom Mode: Let users select 1–2 attributes to prioritize (e.g., region + hobby, course load + study time) with 50/50 weighting for dual attributes.

• Edge Case Handling: Automatically merges small datasets (fewer students than group size) into a single group to avoid non-viable teams.

3. Cohesion Scoring & Transparency

Each group receives a 0–100 cohesion score calculated as the average similarity between all member pairs. Higher scores indicate more aligned members, helping users quickly identify the most collaborative groups.

4. Export & Visualization

• Human-Readable Output: Prints groups with member details (ID, focus, study time, languages, course load) and cohesion scores.

• CSV Export: Saves groups to a CSV file for sharing, analysis, or integration with other tools (e.g., spreadsheets, LMS platforms).

5. Input Validation & Error Handling

• Validates student data (e.g., non-empty IDs, graduation years 1900–2100, course load 0–6 or ANY).

• Provides descriptive errors for invalid inputs (e.g., malformed JSON, unreadable CSV files, HTTP request failures).
🔧 Key Design Decisions

The app’s architecture is built around modularity, scalability, and maintainability, with key design choices including:

1. Polymorphic Data Readers

• Abstract base class PersonReader defines a common read() interface, with subclasses CSVReader and SimulatedDataReader implementing source-specific logic. This design allows easy extension to new data sources (e.g., Excel, SQL) without modifying core grouping logic.

2. Separation of Concerns

• Data Layer: Handles input parsing (CSV/JSON) and validation (via PersonReader and PersonEnums).

• Business Logic Layer: Implements grouping/clustering (via StudyGroupMatcher) independent of data sources.

• Presentation Layer: Manages user interaction and output (via main.cpp), keeping core logic decoupled from UI.

3. C++17 Standard Compliance

• Uses modern C++17 features (e.g., std::optional, std::unordered_set, structured bindings) for type safety, performance, and readability. Avoids C++20+ features (e.g., std::unordered_set::contains()) to ensure broad compatibility.

4. Greedy Clustering Algorithm

• Employs a greedy approach to group formation:

1. Select a random "seed" student from the remaining pool.

2. Sort remaining students by similarity to the seed.

3. Add the top N-1 students to form a group.

4. Repeat until all students are grouped.

• This balances performance (O(n² log n) time complexity) and cohesion, making it suitable for datasets up to 500 students.

5. Reusable Helper Methods

• Static helpers (e.g., validatePerson, parseDelimitedSet, formatSentinelInt) centralize common logic, reducing code duplication and improving maintainability.
🚀 Instructions to Run the Application

Prerequisites
Component Requirements 
Operating System macOS 12+ (Apple Silicon or Intel) 
C++ Compiler Clang (Xcode Command Line Tools) or GCC 9+ 
Build System CMake 3.10+ 
Dependencies curl (HTTP requests), nlohmann-json (JSON parsing) – installed via Homebrew 
IDE (Optional) CLion (recommended), Xcode, or VS Code 

Step 1: Install Dependencies

Use Homebrew to install required libraries:
# Install Xcode Command Line Tools (if not already installed)
xcode-select --install

# Install curl (for HTTP requests)
brew install curl

# Install nlohmann-json (for JSON parsing)
brew install nlohmann-json
Step 2: Clone the Repository
git clone https://github.com/your-username/study-group-matcher.git
cd study-group-matcher
Step 3: Build the Application

Option A: Using CLion (Recommended)

1. Open CLion and select "Open" → Navigate to the cloned repository folder.

2. CLion will auto-detect the CMakeLists.txt file. Click "Load CMake Project".

3. Ensure the build configuration is set to Debug or Release (default: Debug).

4. Click the "Build" icon (hammer) or press Cmd+B (macOS) / Ctrl+B (Windows/Linux).

Option B: Using Command Line
# Create a build directory
mkdir cmake-build-debug
cd cmake-build-debug

# Configure CMake
cmake ..

# Build the project (use -j to speed up with multiple cores)
make -j 8
Step 4: Run the Application

Option A: Using CLion

1. Select study-group-matcher as the run target (top-right dropdown).

2. Click the "Run" icon (play) or press Cmd+R (macOS) / Ctrl+R (Windows/Linux).

3. Follow the command-line prompts (see Usage Walkthrough).

Option B: Using Command Line
# Navigate to the build directory (if not already there)
cd cmake-build-debug

# Run the executable
./study-group-matcher
Step 5: Test the Application

Validate core functionality with these test cases:

1. Simulated Data Test:

◦ Select Option 1 → Enter 6 students → Group size 3 → Default grouping.

◦ Verify 2 groups are generated with cohesion scores (typically 50–90).

2. CSV Import Test:

◦ Create a students.csv file with the sample CSV format.

◦ Select Option 2 → Enter the file path (e.g., ./students.csv).

◦ Verify the app loads the correct number of students and generates groups.

3. JSON URL Test:

◦ Select Option 3 → Enter the public test URL:
https://gist.githubusercontent.com/anonymous/8a9f3f60e79078c4189d66219815898f/raw/students_test.json

◦ Verify the app fetches 5 students and generates valid groups.
📊 Data Format Schemas

CSV Format (Required Columns)

Save as students.csv (header row optional; use ANY for wildcards, - for multi-values):
ID,GraduationYear,Region,PrimaryOS,EngineeringFocus,StudyTime,CourseLoad,FavoriteColors,Hobbies,Languages
student_001,2025,us-west,MacOS,computer_systems,Afternoon,4,Blue-Green,Coding-Hiking,English-Spanish
student_002,ANY,uk-ireland,Windows,neural_engineering,Evening,3,Yellow-Purple,Reading-Music,English-Mandarin
student_003,2026,china,Linux,cybersecurity,Morning,ANY,Red-Gray,Gaming-Sports,English-Russian
JSON Format (Public URL)

Must be a JSON array of student objects (matches CSV schema):
[
  {"ID":"student_001","GraduationYear":"2025","Region":"us-west","PrimaryOS":"MacOS","EngineeringFocus":"computer_systems","StudyTime":"Afternoon","CourseLoad":"4","FavoriteColors":"Blue-Green","Hobbies":"Coding-Hiking","Languages":"English-Spanish"},
  {"ID":"student_002","GraduationYear":"ANY","Region":"uk-ireland","PrimaryOS":"Windows","EngineeringFocus":"neural_engineering","StudyTime":"Evening","CourseLoad":"3","FavoriteColors":"Yellow-Purple","Hobbies":"Reading-Music","Languages":"English-Mandarin"}
]
🛠️ Troubleshooting

Common Issues & Fixes
Error Message Root Cause Solution 
"json.hpp file not found" nlohmann-json not installed or CMake can’t find it. Reinstall via brew reinstall nlohmann-json; verify HOMEBREW_PREFIX in CMakeLists.txt. 
"curl library not found" curl not installed or CMake can’t find it. Reinstall via brew reinstall curl; ensure link_directories points to /opt/homebrew/lib (Apple Silicon) or /usr/local/lib (Intel). 
"Protected member access" validatePerson method in PersonReader.h is not public. Ensure validatePerson is declared in the public section of PersonReader. 
"HTTP request failed" Invalid URL, private endpoint, or network issue. Use a public JSON URL (e.g., the test URL provided); check internet connectivity. 
"JSON parse error" Malformed JSON (e.g., missing commas, invalid array structure). Validate JSON with JSONLint; ensure the top-level is an array ([]). 

For Intel Mac Users

Update HOMEBREW_PREFIX in CMakeLists.txt from /opt/homebrew to /usr/local (Homebrew’s default path for Intel):
# Replace this line:
set(HOMEBREW_PREFIX "/opt/homebrew")

# With this:
set(HOMEBREW_PREFIX "/usr/local")
📌 Usage Walkthrough

1. Launch the App: Run the executable and see the welcome screen.

2. Select Data Source:
=== Data Source ===
1. Simulated Data (statistically interesting students)
2. CSV File (local/network path)
3. Public JSON URL (e.g., GitHub Gist, API endpoint)
Your choice (1/2/3): 1
3. Configure Simulated Data:
Number of simulated students (2-500): 6
✅ Generated 6 simulated students!
4. Set Group Size:
Group size (2-3): 3
5. Choose Grouping Logic:
=== Grouping Configuration ===
1. Default (Best for study groups: Language + Focus + Study Time)
2. Custom Attributes
Your choice (1/2): 1
6. View Groups:
🔄 Generating groups...

==================================== Study Groups ====================================
📊 Cohesion Score (0-100): Higher = more similar (shared language/focus/time)

📚 Group 1: computer_systems | Afternoon | Size: 3 | Cohesion: 86/100
-----------------------------------------------------------------------------------
  • ID: student_001 | Focus: computer_systems | Time: Afternoon | Langs: English-Spanish | Courses: 4
  • ID: student_003 | Focus: computer_systems | Time: Afternoon | Langs: English-French | Courses: 3
  • ID: student_005 | Focus: computer_systems | Time: Evening | Langs: English | Courses: 5

📚 Group 2: neural_engineering | Evening | Size: 3 | Cohesion: 72/100
-----------------------------------------------------------------------------------
  • ID: student_002 | Focus: neural_engineering | Time: Evening | Langs: English-Mandarin | Courses: 3
  • ID: student_004 | Focus: neural_engineering | Time: Evening | Langs: English-Hindi | Courses: 4
  • ID: student_006 | Focus: cybersecurity | Time: Morning | Langs: English-Russian | Courses: ANY
7. Export to CSV (Optional):
Save groups to CSV? (1=Yes/0=No): 1
Enter output path (e.g., ./study_groups.csv): ./my_study_groups.csv
✅ Groups saved to: ./my_study_groups.csv
🤝 Contributing

Contributions are welcome! To extend the app:

1. Fork the repository.

2. Create a feature branch (git checkout -b feature/new-data-source).

3. Implement changes (follow existing code style and modular design).

4. Add test cases for new functionality.

5. Submit a pull request with a detailed description of changes.
📄 License

This project is licensed under the MIT License. See the LICENSE file for details.
