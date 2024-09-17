
# University Analysis Dashboard

This project utilizes a Kaggle dataset featuring a fictional university (https://www.kaggle.com/datasets/ananta/student-performance-dataset) to generate insightful Power BI visualizations. It includes department-wise analysis, semester-wise breakdowns, and student-level insights with interactive slicers.

# Data model
![Screenshot 2024-09-17 223717](https://github.com/user-attachments/assets/88fe59c1-b31d-40aa-a9a8-3d5a3a30731a)



## Description of Charts in each page


## 1. Department wise analysis
This page has the following,
Two slicers according to department and year for the charts,


![Screenshot 2024-09-17 224023](https://github.com/user-attachments/assets/312d43f7-0d9c-4362-b1bc-3749feb439d7)



## a) Student Count by Department:
A pie chart visualizing the distribution of students across departments, interactable via slicers.
## b) Student Count by Date of Admission and Department:
A stacked bar chart showcasing student counts by date of admission and department, interactable via slicers.
## c) Average Marks by Department and Semester:
A line chart displaying the average marks of departments across semesters. The average marks by department are calculated using the following measure:


Average Marks by Department = 
CALCULATE(
    AVERAGE(student_performance_Data[Marks]),
    ALLEXCEPT(Department_Information, Department_Information[Department_Name])
)


This chart is interactable with slicers for department and year.


## 2. Semester-focused Insights
 Have 2 slicers, semester name and department.

 ![Screenshot 2024-05-02 190720](https://github.com/Abhiram-koppuravuri/University_Analysis/assets/121746477/638d07fe-f979-47a6-b9c6-09c5e08a9767)
 

## a) Overall Percentage by Student:
A stacked column chart representing the overall percentage of students in a specific semester and department, calculated by the following DAX code:

Overall Percentage = (Student_Performance_Data_Wide_Version[Paper 1] + Student_Performance_Data_Wide_Version[Paper 2] + Student_Performance_Data_Wide_Version[Paper 3] + Student_Performance_Data_Wide_Version[Paper 4] + Student_Performance_Data_Wide_Version[Paper 5] + Student_Performance_Data_Wide_Version[Paper 6] + Student_Performance_Data_Wide_Version[Paper 7]) / 7

## b) Grade Distribution:
A table displaying grades A to F along with their respective counts. Grades are assigned based on a certain format derived from the provided DAX code.

Grade = 
VAR CurrentRank = 
    RANKX(
        FILTER(
            ALL('Student_Performance_Data_Wide_Version'),
            'Student_Performance_Data_Wide_Version'[Semster_Name] = EARLIER('Student_Performance_Data_Wide_Version'[Semster_Name]) &&
            'Student_Performance_Data_Wide_Version'[Dept] = EARLIER('Student_Performance_Data_Wide_Version'[Dept])
        ),
        [Overall Percentage],
        ,
        DESC
    )
VAR TotalStudents = 
    CALCULATE(
        COUNTROWS('Student_Performance_Data_Wide_Version'),
        FILTER(
            ALL('Student_Performance_Data_Wide_Version'),
            'Student_Performance_Data_Wide_Version'[Semster_Name] = EARLIER('Student_Performance_Data_Wide_Version'[Semster_Name]) &&
            'Student_Performance_Data_Wide_Version'[Dept] = EARLIER('Student_Performance_Data_Wide_Version'[Dept])
        )
    )
RETURN
    SWITCH(
        TRUE(),
        CurrentRank <= TotalStudents * 0.08, "A",
        CurrentRank <= TotalStudents * 0.23, "B",
        CurrentRank <= TotalStudents * 0.53, "C",
        CurrentRank <= TotalStudents * 0.78, "D", 
        CurrentRank <= TotalStudents * 0.93, "E", 
        "F" 
    ) 

The code calculates a student's rank within their department for a specific semester by comparing their 'Overall Percentage' to others.
It then assigns a letter grade (A, B, C, D, E, or F)  based on the student's calculated rank relative to the total number of students in their department and semester.
## c) Student Count:
A card showcasing the total number of students in a department based on slicer selection.
## d) Q&A Widget:
An interactive widget allowing users to ask for specific analyses from the data.
## 3. Student-level Insights
It has 2 slicers,department name and student id (this slicer changes according to department chosen)

![Screenshot 2024-05-02 190733](https://github.com/Abhiram-koppuravuri/University_Analysis/assets/121746477/2030082a-8884-4e04-94cc-33462db4dce0)


## a) Overall Percentage by Semester:
Line chart that shows the student level performance over semesters based on above slicers.
## Limitations in the dataset
## 1. Absence of Professor ID:
The dataset lacks a direct link between professors and student performance records, hindering analyses at the individual professor level. Integrating 'Professor ID' within course-related tables or performance records would enhance the scope of analyses focused on professor impact.
## 2. Lack of Assigned Grades:
Exam papers in the dataset do not have assigned grades, this is bad as grades are dynamic and depend on many factors like difficulty of paper, student performance respect to other students etc. Hence I made a grade system to deal this problem as stated above.
## 3. Disconnection between Department Choices and Admission:
The disparity between a student's initial department choices and actual admission could complicate analysis. Capturing additional data points about why students are placed in specific departments would enable better analysis of preferences versus enrollment outcomes.
