# **Study Group Matcher**

A robust **C++17 application** designed to generate **high-cohesion study groups** from multiple data sources, with customizable grouping logic and transparent cohesion scoring. Ideal for educators, administrators, or students seeking balanced, collaborative teams based on shared attributes.

---

## **Detailed App Functionality**

### **1. Multi-Source Data Integration**

The app supports **three distinct data inputs**:

#### **• Simulated Data**

Generates **2–500 statistically realistic student profiles** with weighted distributions:

* Engineering Focus: e.g., 25% computer systems, 20% cybersecurity
* Study Time: 45% afternoon, 35% evening
* Region: 30% US Northeast, 25% US West
* Includes **correlated traits** (e.g., cybersecurity students often speak Russian/Mandarin)

#### **• CSV File Import**

* Loads from local/network CSV files
* Predefined schema
* Supports:

  * `ANY` wildcards
  * `-` delimited multi-value fields (e.g., `Coding-Hiking`, `English-Mandarin`)

#### **• Public JSON URL Fetch**

* Parses JSON arrays from HTTP/HTTPS endpoints
* SSL verification
* Redirect-following
* 15-second timeout

---

### **2. Customizable Grouping Logic**

| **Grouping Mode**     | **Description**                               | **Scoring Weighting**                                                       |
| --------------------- | --------------------------------------------- | --------------------------------------------------------------------------- |
| **Default (Optimal)** | Prioritizes collaboration-critical attributes | Language **40%** → Focus **30%** → Study Time **20%** → Course Load **10%** |
| **Custom**            | User selects 1–2 attributes to prioritize     | Single attribute = **100%**                                                 |

---

### **3. Supported Attributes**

| **Attribute**     | **Type**    | **Importance** | **Description**                  |
| ----------------- | ----------- | -------------- | -------------------------------- |
| Engineering Focus | Enum        | High           | Academic specialization          |
| Study Time        | Enum        | High           | Preferred study window           |
| Language          | Multi-value | High           | Spoken languages                 |
| Course Load       | Integer     | Low+           | Number of courses (0–6) or `ANY` |
| Region            | Enum        | Low            | Geographic region                |
| Hobby             | Multi-value | Low            | Hobbies/interests                |
| OS                | Enum        | None           | Primary operating system         |

---

### **4. Cohesion Scoring & Transparency**

Each group receives a **0–100 cohesion score**, calculated from the **average pairwise similarity** of its members. Higher scores indicate more aligned groups.

---

### **5. Export & Visualization**

| **Output Type** | **Description**                           | **Use Case**        |
| --------------- | ----------------------------------------- | ------------------- |
| Human-Readable  | Shows groups, attributes, cohesion scores | Quick review        |
| CSV Export      | Saves groups to CSV                       | Sharing, LMS import |

---

### **6. Input Validation & Error Handling**

* Validates:

  * Non-empty IDs
  * GraduationYear 1900–2100
  * CourseLoad 0–6 or `ANY`
* Descriptive errors for:

  * Malformed JSON
  * Invalid CSV
  * HTTP/SSL/network failures

---

## **Key Design Decisions**

| **Design Decision**         | **Implementation**                   | **Benefit**          |
| --------------------------- | ------------------------------------ | -------------------- |
| Polymorphic Data Readers    | Abstract `PersonReader` + subclasses | Easy extension       |
| Separation of Concerns      | 3-layer architecture                 | Maintainability      |
| C++17 Compliance            | `std::optional`, structured bindings | Safety + performance |
| Greedy Clustering Algorithm | Seed-based sorting + top-N           | Fast (`O(n² log n)`) |
| Reusable Helpers            | Static validator utilities           | Consistency          |

---

# **Step-by-Step Setup & Run Instructions**

## **Prerequisites**

| **Component**    | **Requirements**        |
| ---------------- | ----------------------- |
| Operating System | macOS 12+               |
| C++ Compiler     | Clang or GCC 9+         |
| Build System     | CMake 3.10+             |
| Dependencies     | `curl`, `nlohmann-json` |
| IDE (Optional)   | CLion, Xcode, VS Code   |

---

## **Step 1: Install Dependencies**

```bash
xcode-select --install
brew install curl
brew install nlohmann-json
```

## **Step 2: Clone the Repository**

```bash
git clone https://github.com/your-username/study-group-matcher.git
cd study-group-matcher
```

## **Step 3: Build the Application**

### **Option A: Using CLion (Recommended)**

1. Open CLion → "Open" → select project folder
2. CLion detects `CMakeLists.txt`
3. Choose Debug or Release
4. Build (hammer icon or `Cmd+B`)

### **Option B: Using Command Line**

```bash
mkdir cmake-build-debug
cd cmake-build-debug
cmake ..
make -j 8
```

---

## **Step 4: Run the Application**

### **Option A: Using CLion**

Select run target → Run (`Cmd+R`)

### **Option B: Using Command Line**

```bash
cd cmake-build-debug
./study-group-matcher
```

---

## **Step 5: Test the Application**

| **Test Case**  | **Steps**                                           | **Expected Outcome**    |
| -------------- | --------------------------------------------------- | ----------------------- |
| Simulated Data | Select Option 1 → Enter 6 students → Group size = 3 | Auto-generated profiles |
| CSV Import     | Create `students.csv` → Option 2                    | Successfully loaded     |
| JSON URL       | Option 3 → Use provided URL → Group size = 2        | JSON imported           |

---

# **Data Format Schemas**

## **CSV Format (Required Columns)**

Use `ANY` for wildcards, `-` for multi-values.

```
ID,GraduationYear,Region,PrimaryOS,EngineeringFocus,StudyTime,CourseLoad,FavoriteColors,Hobbies,Languages
student_001,2025,us-west,MacOS,computer_systems,Afternoon,4,Blue-Green,Coding-Hiking,English-Spanish
student_002,ANY,uk-ireland,Windows,neural_engineering,Evening,3,Yellow-Purple,Reading-Music,English-Mandarin
student_003,2026,china,Linux,cybersecurity,Morning,ANY,Red-Gray,Gaming-Sports,English-Russian
```

---

## **JSON Format (Public URL)**

```json
[
  {
    "ID": "student_001",
    "GraduationYear": "2025",
    "Region": "us-west",
    "PrimaryOS": "MacOS",
    "EngineeringFocus": "computer_systems",
    "StudyTime": "Afternoon",
    "CourseLoad": "4",
    "FavoriteColors": "Blue-Green",
    "Hobbies": "Coding-Hiking",
    "Languages": "English-Spanish"
  },
  {
    "ID": "student_002",
    "GraduationYear": "ANY",
    "Region": "uk-ireland",
    "PrimaryOS": "Windows",
    "EngineeringFocus": "neural_engineering",
    "StudyTime": "Evening",
    "CourseLoad": "3",
    "FavoriteColors": "Yellow-Purple",
    "Hobbies": "Reading-Music",
    "Languages": "English-Mandarin"
  }
]
```

---

# **Troubleshooting**

| **Error Message**         | **Cause**                   | **Solution**                   |
| ------------------------- | --------------------------- | ------------------------------ |
| `json.hpp file not found` | Missing `nlohmann-json`     | `brew reinstall nlohmann-json` |
| `curl library not found`  | curl not detected           | `brew reinstall curl`          |
| `Protected member access` | `validatePerson` not public | Move to `public` section       |
| `HTTP request failed`     | Invalid/private URL         | Use test URL                   |
| `JSON parse error`        | Malformed JSON              | Validate with JSONLint         |

---

# **For Intel Mac Users**

Set:

```cmake
set(HOMEBREW_PREFIX "/usr/local")
```

---

# **Usage Walkthrough**

```text
=== Data Source ===
1. Simulated Data
2. CSV File
3. Public JSON URL
Your choice (1/2/3): 1
```

Example:

```text
Number of simulated students (2-500): 6
Group size (2-3): 3
Your choice (1/2): 1

🔄 Generating groups...

📚 Group 1: computer_systems | Afternoon | Size: 3 | Cohesion: 86/100
```

### **CSV Export**

```text
Save groups to CSV? (1=Yes/0=No): 1
Enter output path: ./my_study_groups.csv
✅ Groups saved!
```

---

# **Contributing**

1. Fork repo
2. `git checkout -b feature/new-data-source`
3. Implement changes
4. Add tests
5. Open PR with description

---

