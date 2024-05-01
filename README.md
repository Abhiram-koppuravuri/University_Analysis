# University_Analysis
##Overview
Used kaggle dataset of a fictional university (https://www.kaggle.com/datasets/ananta/student-performance-dataset) and made power bi charts based on it.
It has department wise analysis, semester wise analysis, student wise analysis with slicers.


##Description of Charts in each page
1. Department wise analysis
This page has the following,
Two slicers according to department and year for the charts,

a) Department choice applied and Got
  This Donut chart shows count and percentage of people who got or not got their preferred department.
  I have created a calcualted column based on below DAX code for the chart

  Choice = IF([Department_Choices] = [Department_Admission], "Yes", "No")


b) Count of students by department name
  A pie chart to visualize number of students in departments interactable via slicers.


c) Count of students by Date of Admission and Department name
  Stacked bar chart based on Count of students by Date of Admission and Department name interactable via slicers.
  

d) Average marks by department by semester name and department name
  Line chart showing average marks of departments over semesters , average marks by department is calculated by measure 

DAX code:
Average Marks by Department = 
CALCULATE(
   AVERAGE(student_performance_Data[Marks]),
   ALLEXCEPT(Department_Information, Department_Information[Department_Name]) 
)

 Is interactable with slicers of department and year.


 2. Semester focussed Insights
 Have 2 slicers, semester name and department. It has following charts

a) Overall percentage by student 
Stacked column chart based on students of particular semester and department selected on slicer.
Overall percentage is calculated by,

DAX code: 
Overall Percentage = (Student_Performance_Data_Wide_Version[Paper 1]+Student_Performance_Data_Wide_Version[Paper 2]+Student_Performance_Data_Wide_Version[Paper 3]+Student_Performance_Data_Wide_Version[Paper 4]+Student_Performance_Data_Wide_Version[Paper 5]+Student_Performance_Data_Wide_Version[Paper 6]+Student_Performance_Data_Wide_Version[Paper 7])/7


b) Grade and count of grades
 Table which shows Grades A to F with thier count. 

 Just direct marks are given for exam papers in the data set. Hence I developed a certain format to grade students with the below dax code.

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


c) Count of Students
Card which shows total students of department based on selection on slicer.


d) Q and A widget 
 Added so user can ask required analysis from the data.



3. Student level insights
It has 2 slicers,department name and student id (this slicer changes according to department chosen)

a) Overall percentage by semester name
Line chart that shows the student level performance over semesters based on above slicers.



##Limitations in the dataset
1. There is no professor id in the dataset, the lack of a direct way to link specific professors with their students' performance records limits some analyses. This makes it harder to definitively explore professor-level impact and could require us to primarily focus on department-level trends.
"Department Choices" vs. Enrollment 
Hence including 'Professor ID' directly within course-related tables or performance records would significantly expand the types of analyses focused on individual professor impact.
   

2. No grades assigned in dataset for exam papers, this is bad as grades are dynamic and depend on many factors like difficulty of paper, student performance respect to other students etc. Hence I made a grade system to deal this problem as stated above.


3. The disconnect between a student's initial "department choices" and actual "department admission" could complicate analysis. Ideally, it would be better to have a clear way to track why a student interested in Department A ended up enrolled in Department B. Hence capturing additional data points about why students are placed in specific departments (e.g., capacity limitations, academic criteria, etc.) would allow to better analyze the differences between preferences and actual enrollment.
