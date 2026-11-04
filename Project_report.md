# BCA Data Science Final Year Project
# Project By: Kalpana
# College: Ngf College of Engineering and Technology
# Session: 2024-26
# Topic: DBMS Keys Implementation with Data Analysis

import sqlite3
import pandas as pd

print("Project Started...")

# Step 1: Database Connection
conn = sqlite3.connect('college.db')
cursor = conn.cursor()
cursor.execute("PRAGMA foreign_keys = ON")

# Step 2: Create Tables with All Keys

# Table 1: Primary Key + Unique Key
cursor.execute("DROP TABLE IF EXISTS students")
cursor.execute('''
CREATE TABLE students (
    roll_no INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    email TEXT UNIQUE,
    branch TEXT
)
''')

# Table 2: Foreign Key + Composite Key Concept
cursor.execute("DROP TABLE IF EXISTS marks")
cursor.execute('''
CREATE TABLE marks (
    mark_id INTEGER PRIMARY KEY,
    roll_no INTEGER,
    subject TEXT,
    marks INTEGER,
    FOREIGN KEY (roll_no) REFERENCES students(roll_no)
)
''')

print("1. Tables Created with Primary, Unique and Foreign Keys")

# Step 3: Insert Data (Exception Handling)
try:
    students_data = [
        (101, 'Student1', 's1@college.com', 'DS'),
        (102, 'Student2', 's2@college.com', 'DS'),
        (103, 'Student3', 's3@college.com', 'DS')
    ]
    cursor.executemany("INSERT INTO students VALUES (?, ?, ?, ?)", students_data)

    marks_data = [
        (1, 101, 'Python', 85),
        (2, 101, 'SQL', 90),
        (3, 102, 'Python', 78),
        (4, 103, 'SQL', 92)
    ]
    cursor.executemany("INSERT INTO marks VALUES (?, ?, ?, ?)", marks_data)
    conn.commit()
    print("2. Sample Data Inserted Successfully")

except sqlite3.IntegrityError as e:
    print("Key Violation Error:", e)

# Step 4: Data Science Part - Analysis with Pandas
query = '''
SELECT s.roll_no, s.name, m.subject, m.marks 
FROM students s 
JOIN marks m ON s.roll_no = m.roll_no
'''

df = pd.read_sql_query(query, conn)

print("\n3. Data Analysis Report:")
print(df)

print("\n4. Average Marks (Subject-wise):")
print(df.groupby('subject')['marks'].mean())

print("\n5. Topper Student:")
print(df.loc[df['marks'].idxmax()])

conn.close()
print("\nProject Completed Successfully - college.db created")
