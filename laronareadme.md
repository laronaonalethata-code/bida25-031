# SECTION A: BASIC GRADING SYSTEM

# Ask for the number of students
num_students = int(input("Enter the number of students: "))

total_grade = 0
student_data = []

# Loop through each student
for i in range(num_students):
    print("\nStudent", i + 1)

    # Get student name
    name = input("Enter student's name: ")

    # Get and validate grade
    while True:
        grade = float(input("Enter grade (0 - 100): "))

        if 0 <= grade <= 100:
            break
        else:
            print("Invalid grade. Please enter a grade between 0 and 100.")

    # Add grade to total
    total_grade += grade

    # Store student information
    student_data.append((name, grade))

# Calculate class average
average = total_grade / num_students

# Display results
print("\n========== CLASS RESULTS ==========")
print("Class Total:", total_grade)
print("Class Average:", round(average, 2))

task b
# SECTION B: MULTIPLE SUBJECTS

# Subjects
subjects = ["Math", "English", "Science"]

# Ask for the number of students
num_students = int(input("Enter the number of students: "))

# List to store all students
students = []

# Collect student information
for i in range(num_students):
    print("\nStudent", i + 1)

    name = input("Enter student's name: ")

    grades = []

    # Get grades for each subject
    for subject in subjects:
        while True:
            grade = float(input("Enter " + subject + " grade (0 - 100): "))

            if 0 <= grade <= 100:
                break
            else:
                print("Invalid grade. Please enter a grade between 0 and 100.")

        grades.append(grade)

    # Store student as a tuple
    student = (name, grades)
    students.append(student)


# Calculate and display each student's average
print("\n========== STUDENT RESULTS ==========")

for name, grades in students:
    student_average = sum(grades) / len(grades)

    print(name)
    print("Math:", grades[0])
    print("English:", grades[1])
    print("Science:", grades[2])
    print("Average:", round(student_average, 2))
    print("-------------------------------------")


# Find highest and lowest grades for each subject
print("\n========== SUBJECT RESULTS ==========")

for i in range(len(subjects)):

    subject_grades = []

    # Collect all grades for the current subject
    for name, grades in students:
        subject_grades.append(grades[i])

    highest = max(subject_grades)
    lowest = min(subject_grades)

    print(subjects[i])
    print("Highest Grade:", highest)
    print("Lowest Grade:", lowest)
    print("-------------------------------------")


# Display summary table
print("\n========== SUMMARY TABLE ==========")

print(f"{'Name':<15}{'Math':<10}{'English':<10}{'Science':<10}{'Average':<10}")
print("-" * 55)

for name, grades in students:
    student_average = sum(grades) / len(grades)

    print(
        f"{name:<15}"
        f"{grades[0]:<10}"
        f"{grades[1]:<10}"
        f"{grades[2]:<10}"
        f"{student_average:<10.2f}"
    )

    """Section C: Dictionaries for student management.

Data model:
    students = {
        "Alice": {"Math": [90, 85], "English": [78], "Science": [88, 92]},
        "Bob":   {"Math": [70],     "English": [65, 80], "Science": [75]},
    }

Outer key  = student name
Inner key  = subject name
Inner value = list of grades for that subject
"""

SUBJECTS = ["Math", "English", "Science"]  # at least three; edit to add more


# ---------- input helpers ----------

def read_grade(prompt):
    """Prompt until the user enters a number from 0 to 100 (inclusive)."""
    while True:
        text = input(prompt).strip()
        try:
            grade = float(text)
        except ValueError:
            print("  Invalid input: please enter a number.")
            continue
        if 0 <= grade <= 100:
            return grade
        print("  Invalid grade: it must be between 0 and 100.")


def read_grades_for_subject(name, subject):
    """Read one or more grades for a subject; blank line finishes (min. 1)."""
    grades = [read_grade(f"  {subject} grade for {name} (0-100): ")]
    while True:
        text = input(f"  Another {subject} grade? (enter to finish, or type it): ").strip()
        if text == "":
            return grades
        try:
            value = float(text)
        except ValueError:
            print("  Invalid input: please enter a number.")
            continue
        if 0 <= value <= 100:
            grades.append(value)
        else:
            print("  Invalid grade: it must be between 0 and 100.")


def choose_subject():
    """Let the user pick a subject by name (case-insensitive). None if invalid."""
    print("Subjects: " + ", ".join(SUBJECTS))
    text = input("Subject: ").strip().lower()
    for subject in SUBJECTS:
        if subject.lower() == text:
            return subject
    print("  Unknown subject.")
    return None


# ---------- data helpers ----------

def find_name(students, name):
    """Return the stored key matching name (case-insensitive), else None."""
    for stored in students:
        if stored.lower() == name.strip().lower():
            return stored
    return None


def average(grades):
    return sum(grades) / len(grades) if grades else 0.0


def student_average(record):
    """Average across every grade in every subject."""
    all_grades = [g for grades in record.values() for g in grades]
    return average(all_grades)


# ---------- features ----------

def add_student(students):
    name = input("New student's name: ").strip()
    if not name:
        print("  Name cannot be empty.")
        return
    if find_name(students, name):
        print(f"  {name} already exists. Use 'Update' to change their grades.")
        return
    students[name] = {s: read_grades_for_subject(name, s) for s in SUBJECTS}
    print(f"  Added {name}.")


def update_student(students):
    name = find_name(students, input("Student to update: "))
    if name is None:
        print("  Student not found.")
        return
    subject = choose_subject()
    if subject is None:
        return
    print(f"  Current {subject} grades: {format_grades(students[name][subject])}")
    choice = input("  (a)dd a grade, or (r)eplace all grades? ").strip().lower()
    if choice == "a":
        students[name][subject].append(read_grade("  New grade (0-100): "))
    elif choice == "r":
        students[name][subject] = read_grades_for_subject(name, subject)
    else:
        print("  Cancelled.")
        return
    print(f"  Updated {name}'s {subject} grades.")


def remove_student(students):
    name = find_name(students, input("Student to remove: "))
    if name is None:
        print("  Student not found.")
        return
    del students[name]
    print(f"  Removed {name}.")


def view_subject(students):
    if not students:
        print("  No students yet.")
        return
    subject = choose_subject()
    if subject is None:
        return
    print(f"\nAll {subject} grades")
    print("-" * 40)
    for name, record in students.items():
        print(f"{name:<20} {format_grades(record[subject])}")


def search_student(students):
    name = find_name(students, input("Student to search for: "))
    if name is None:
        print("  Student not found.")
        return
    record = students[name]
    print(f"\n{name}")
    print("-" * 40)
    for subject, grades in record.items():
        print(f"{subject:<10} {format_grades(grades)}   (avg {average(grades):.2f})")
    print(f"Overall average: {student_average(record):.2f}")


def view_all(students):
    if not students:
        print("  No students yet.")
        return
    name_w = max(len("Name"), *(len(n) for n in students))
    header = f"{'Name':<{name_w}} | " + " | ".join(f"{s:>8}" for s in SUBJECTS) + f" | {'Average':>8}"
    print("\n" + "-" * len(header))
    print(header)
    print("-" * len(header))
    for name, record in students.items():
        cells = " | ".join(f"{average(record[s]):>8.2f}" for s in SUBJECTS)
        print(f"{name:<{name_w}} | {cells} | {student_average(record):>8.2f}")
    print("-" * len(header))
    print("(subject columns show each subject's average)")


def format_grades(grades):
    return ", ".join(f"{g:g}" for g in grades) if grades else "-"


# ---------- main menu ----------

MENU = """
=== Grade Manager ===
1. Add a student
2. Update a student's grades
3. Remove a student
4. View all grades for a subject
5. Search for a student
6. View all students
0. Quit
"""

ACTIONS = {
    "1": add_student,
    "2": update_student,
    "3": remove_student,
    "4": view_subject,
    "5": search_student,
    "6": view_all,
}


def main():
    students = {}
    while True:
        print(MENU)
        choice = input("Choose an option: ").strip()
        if choice == "0":
            print("Goodbye!")
            break
        action = ACTIONS.get(choice)
        if action is None:
            print("  Invalid option, try again.")
        else:
            action(students)

"""Section D: Functions, search/update, and error handling.
 
Same data model as Section C:
    students = {"Alice": {"Math": [90, 85], "English": [78], "Science": [88]}}
 
Design:
  * "Core" functions (parse_grade, calculate_average, add_student, ...) do one
    job, take everything they need as parameters, and RAISE ValueError or
    KeyError with an informative message when something is wrong.
  * "ui_" functions handle the keyboard, call the core functions inside
    try/except, and print those messages, so the program never crashes
    on bad input.
"""
 
SUBJECTS = ["Math", "English", "Science"]  # at least three; edit to add more
 
 
# =====================================================================
# Validation and calculation (core, reusable)
# =====================================================================
 
def parse_grade(text):
    """Convert text to a grade in [0, 100]. Raise ValueError if it is invalid."""
    try:
        grade = float(text)
    except (TypeError, ValueError):
        raise ValueError(f"'{text}' is not a number.") from None
    if not 0 <= grade <= 100:  # also rejects 'nan'
        raise ValueError(f"{text} is out of range; grades must be between 0 and 100.")
    return grade
 
 
def calculate_average(grades):
    """Average of a list of grades. Raise ValueError if there are none."""
    if not grades:
        raise ValueError("there are no grades to average.")
    return sum(grades) / len(grades)
 
 
def calculate_student_average(record):
    """Average over every grade in every subject of one student's record."""
    all_grades = [g for grades in record.values() for g in grades]
    return calculate_average(all_grades)
 
 
def find_student(students, name):
    """Return the stored name matching `name` (case-insensitive).
 
    Raise KeyError if there is no such student.
    """
    wanted = name.strip().lower()
    for stored in students:
        if stored.lower() == wanted:
            return stored
    raise KeyError(f"No student named '{name.strip()}' was found.")
 
 
def validate_subject(subject):
    """Return the canonical subject name (case-insensitive) or raise ValueError."""
    for known in SUBJECTS:
        if known.lower() == subject.strip().lower():
            return known
    raise ValueError(f"Unknown subject '{subject.strip()}'. Choose from: {', '.join(SUBJECTS)}.")
 
 
# =====================================================================
# Data operations (core, reusable)
# =====================================================================
 
def check_new_name(students, name):
    """Raise ValueError if `name` is blank or already used."""
    if not name.strip():
        raise ValueError("Student name cannot be empty.")
    try:
        find_student(students, name)
    except KeyError:
        return  # good: the name is free
    raise ValueError(f"'{name.strip()}' already exists. Use update to change their grades.")
 
 
def add_student(students, name, record):
    """Add a student. `record` maps each subject to a non-empty list of grades.
 
    Raise ValueError for a blank/duplicate name, missing subjects, or bad grades.
    """
    check_new_name(students, name)
    name = name.strip()
 
    clean = {}
    for subject in SUBJECTS:
        grades = record.get(subject)
        if not grades:
            raise ValueError(f"Missing grades for {subject}.")
        clean[subject] = [parse_grade(g) for g in grades]
    students[name] = clean
 
 
def update_grades(students, name, subject, grades, replace=False):
    """Add `grades` to a student's subject list, or replace the list.
 
    Raise KeyError (no such student) or ValueError (bad subject/grades).
    """
    stored = find_student(students, name)
    subject = validate_subject(subject)
    if not grades:
        raise ValueError("No grades were provided.")
    clean = [parse_grade(g) for g in grades]
    if replace:
        students[stored][subject] = clean
    else:
        students[stored].setdefault(subject, []).extend(clean)
 
 
def remove_student(students, name):
    """Remove a student by name. Raise KeyError if not found."""
    del students[find_student(students, name)]
 
 
# =====================================================================
# Display
# =====================================================================
 
def format_grades(grades):
    return ", ".join(f"{g:g}" for g in grades) if grades else "no data"
 
 
def display_student(name, record):
    """Print one student's grades per subject and their overall average."""
    print(f"\n{name}")
    print("-" * 40)
    for subject in SUBJECTS:
        grades = record.get(subject)  # .get: a missing subject is not a crash
        if grades:
            print(f"{subject:<10} {format_grades(grades)}   (avg {calculate_average(grades):.2f})")
        else:
            print(f"{subject:<10} no data")
    try:
        print(f"Overall average: {calculate_student_average(record):.2f}")
    except ValueError:
        print("Overall average: n/a (no grades recorded)")
 
 
def display_students_summary(students):
    """Print a table of every student's per-subject averages and overall average."""
    if not students:
        print("  There are no students to show yet.")
        return
 
    def cell(grades):
        try:
            return f"{calculate_average(grades):>8.2f}"
        except ValueError:
            return f"{'n/a':>8}"
 
    name_w = max(len("Name"), *(len(n) for n in students))
    header = f"{'Name':<{name_w}} | " + " | ".join(f"{s:>8}" for s in SUBJECTS) + f" | {'Average':>8}"
    line = "-" * len(header)
    print("\n" + line)
    print(header)
    print(line)
    for name, record in students.items():
        subject_cells = " | ".join(cell(record.get(s, [])) for s in SUBJECTS)
        all_grades = [g for grades in record.values() for g in grades]
        print(f"{name:<{name_w}} | {subject_cells} | {cell(all_grades)}")
    print(line)
    print("(subject columns show each subject's average)")
 
 
def display_subject(students, subject):
    """Print every student's grades for one subject."""
    print(f"\nAll {subject} grades")
    print("-" * 40)
    for name, record in students.items():
        print(f"{name:<20} {format_grades(record.get(subject))}")
 
 
# =====================================================================
# Keyboard input (with try/except retry loops)
# =====================================================================
 
def read_grade(prompt):
    """Prompt until a valid grade is entered; show why each attempt failed."""
    while True:
        try:
            return parse_grade(input(prompt).strip())
        except ValueError as error:
            print(f"  Invalid grade: {error}")
 
 
def read_grades_for_subject(name, subject):
    """Read one or more grades; an empty entry finishes (at least one required)."""
    grades = [read_grade(f"  {subject} grade for {name} (0-100): ")]
    while True:
        text = input(f"  Another {subject} grade? (enter to finish, or type it): ").strip()
        if text == "":
            return grades
        try:
            grades.append(parse_grade(text))
        except ValueError as error:
            print(f"  Invalid grade: {error}")
 
 
def read_subject():
    """Prompt until a known subject is entered; blank cancels (returns None)."""
    print("Subjects: " + ", ".join(SUBJECTS))
    while True:
        text = input("Subject (blank to cancel): ").strip()
        if not text:
            return None
        try:
            return validate_subject(text)
        except ValueError as error:
            print(f"  {error}")
 
 
# =====================================================================
# Menu actions (UI layer: catch errors and report them)
# =====================================================================
 
def ui_add_student(students):
    name = input("New student's name: ").strip()
    try:
        check_new_name(students, name)  # fail fast, before asking for every grade
        record = {s: read_grades_for_subject(name, s) for s in SUBJECTS}
        add_student(students, name, record)
        print(f"  Added {name}.")
    except ValueError as error:
        print(f"  Could not add student: {error}")
 
 
def search_and_update(students):
    """Find a student by name, show them, then optionally update their grades."""
    try:
        name = find_student(students, input("Student to search for: "))
    except KeyError as error:
        print(f"  {error.args[0]}")
        return
 
    display_student(name, students[name])
 
    if input("\nUpdate this student's grades? (y/n): ").strip().lower() != "y":
        return
    subject = read_subject()
    if subject is None:
        print("  Cancelled.")
        return
    mode = input("  (a)dd grades or (r)eplace all grades? ").strip().lower()
    if mode not in ("a", "r"):
        print("  Cancelled: please answer 'a' or 'r'.")
        return
    try:
        if mode == "a":
            grades = [read_grade("  New grade (0-100): ")]
        else:
            grades = read_grades_for_subject(name, subject)
        update_grades(students, name, subject, grades, replace=(mode == "r"))
        print(f"  Updated {name}'s {subject} grades.")
        display_student(name, students[name])
    except (KeyError, ValueError) as error:
        print(f"  Could not update: {error}")
 
 
def ui_remove_student(students):
    try:
        remove_student(students, input("Student to remove: "))
        print("  Student removed.")
    except KeyError as error:
        print(f"  {error.args[0]}")
 
 
def ui_view_subject(students):
    if not students:
        print("  There are no students to show yet.")
        return
    subject = read_subject()
    if subject is not None:
        display_subject(students, subject)
 
 
def ui_summary(students):
    display_students_summary(students)
 
 
# =====================================================================
# Main menu
# =====================================================================
 
MENU = """
=== Grade Manager ===
1. Add a student
2. Search for a student (and update grades)
3. Remove a student
4. View all grades for a subject
5. View students summary
0. Quit
"""
 
ACTIONS = {
    "1": ui_add_student,
    "2": search_and_update,
    "3": ui_remove_student,
    "4": ui_view_subject,
    "5": ui_summary,
}
 
 
def main():
    students = {}
    try:
        while True:
            print(MENU)
            choice = input("Choose an option: ").strip()
            if choice == "0":
                break
            action = ACTIONS.get(choice)
            if action is None:
                print("  Invalid option, please enter a number from the menu.")
            else:
                action(students)
    except (EOFError, KeyboardInterrupt):
        print()  # Ctrl-D / Ctrl-C: leave cleanly
    print("Goodbye!")
 
 
if __name__ == "__main__":
    main()

if __name__ == "__main__":
    main()
print("\nStudent Grades:")
for name, grade in student_data:
    print(name, "-", grade, "| Class Average:", round(average, 2))
