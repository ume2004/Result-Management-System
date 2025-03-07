#RESULT MANAGEMENT SYSTEM
from faker import Faker
import pandas as pd

fake = Faker()
students = []

for i in range(10000):
    students.append({
        "Student_ID": i + 1,
        "Name": fake.name(),
        "Age": fake.random_int(min=18, max=25),
        "Department": fake.random_element(["CSE", "ECE", "IT", "ME", "EEE"])
    })

df_students = pd.DataFrame(students)
df_students.to_csv("students.csv", index=False)

import random

subjects = ["Electronics", "Programming", "Database", "Data Science", "Mathematics", "DSA"]

marks = []

for i in range(10000):
    student_marks = {
        "Student_ID": i + 1,
        "Electronics": random.randint(30, 100),
        "Programming": random.randint(30, 100),
        "Database": random.randint(30, 100),
        "Data Science": random.randint(30, 100),
        "Mathematics": random.randint(30, 100),
        "DSA": random.randint(30, 100),
    }
    marks.append(student_marks)

df_marks = pd.DataFrame(marks)
df_marks.to_csv("marks.csv", index=False)

from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("ResultManagement").getOrCreate()

# Load data
students_df = spark.read.csv("students.csv", header=True, inferSchema=True)
marks_df = spark.read.csv("marks.csv", header=True, inferSchema=True)

# Compute statistics
marks_df.createOrReplaceTempView("marks")
avg_marks = spark.sql("SELECT AVG(Electronics) AS Avg_Electronics, AVG(Programming) AS Avg_Programming, "
                      "AVG(Database) AS Avg_Database, AVG(Data Science) AS Avg_DataScience, " # Use backticks to enclose column name with space
                      "AVG(Mathematics) AS Avg_Mathematics, AVG(DSA) AS Avg_DSA FROM marks")

avg_marks.show()

ROMAN KHAN, [3/1/2025 1:58 PM]
df_marks["Total"] = df_marks.iloc[:, 1:].sum(axis=1)
df_marks["Percentage"] = df_marks["Total"] / 6
df_marks["Result"] = df_marks["Percentage"].apply(lambda x: "Pass" if x >= 40 else "Fail")

# Get insights
print(df_marks.describe())  # Basic statistics
print(df_marks[df_marks["Result"] == "Fail"].count())  # No. of failing students
print(df_marks.nlargest(50, "Total"))  # Top 50 students

import matplotlib.pyplot as plt

plt.figure(figsize=(10,5))
df_marks[["Electronics", "Programming", "Database", "Data Science", "Mathematics", "DSA"]].mean().plot(kind='bar', color='skyblue')
plt.title("Average Marks per Subject")
plt.xlabel("Subjects")
plt.ylabel("Average Marks")
plt.show()
