UCI MACHINE LEARNING HACKATHON - GROUP JAAEHAX (Clinical Dataset)  
Jatin Momaya (jmomaya@uci.edu)  
Arvind Kumar (arvindsk@uci.edu)  
Aditya Chowdhury (chowda1@uci.edu)  
Eric Chou (eschou1@uci.edu)  

QUESTION: Is there a correlation between age and likelihood of death?  
HYPOTHESIS: As age increases, likelihood of death also increases.  
OUR APPROACH: We used SQL queries from Microsoft SQL Server Management Studio to get data. 
We first calculated the age of each patient grouped by individual patient IDs.
To calculate the likelihood of death, we decided to set up a 'death score' based on the data we had.

We began our data collection by filtering in only the patient IDs that were inpatient visits. 
To do this we used the dbo.visit_occurrence database and specifying the visit_concept_id to '9201' (Inpatient Visits). 
We initially chose to limit our data to 10,000 patients but later found 2,500 allowed a less clustered end result.
 
We decided to calculate our own "death score" in attempts to predict how likely a person is to die depending on various factors.
We decided on 4 difference factors and gave them each a different weight so that the maximum total death score would be 1,000.  

Our first factor was the total number of conditions reported for each patientID and its death score maximum value was 250.  
Our reasoning behind this factor was that: the more conditions you are diagnosed with, the more serious your case is. 
We used dbo.condition_occurrence to document the total number of conditions reported per patient. 
To assign a number between 1-250 (the score) we sorted the data by the total number of conditions reported in ascending order. 
Since we had a total of 2,500 patients, we divided the data into 250 parts to find 250 even categories of 10 patientIDs. 
We assigned the number 1 to the first 10 patientIDs, 2 to the second 10 patient IDs, and so on until we reached 250 for the last 10 patientIDs. 
We ran into some errors due to our dataset containing NaN values so we cleaned the dataset by filling the NaN values by the mean number of conditions.

Our second factor was the total length of hospitalization reported for each patientID and its death score maximum value was 250.
Our reasoning behind this factor was that: the more days you spend in the hospital, the more serious your case is. 
We used dbo.cvisit_occurrence to calculate the total number of days each patientID spent in the hospital.
To assign a number between 1-250 (the score) we sorted the data by the total length of hospitalization in ascending order.
Since we had a total of 2,500 patients, we divided the data into 250 parts to find 250 even categories of 10 patientIDs.
We assigned the number 1 to the first 10 patientIDs, 2 to the second 10 patient IDs, and so on until we reached 250 for the last 10 patientIDs.
We ran into some errors due to our dataset containing NaN values so we cleaned the dataset by filling the NaN values by the mean length of hospitalization (in days).

Our third factor and most important factor was if the patient was dead or alive.
Since being dead or alive is a binary condition, we gave dead patients the full score of 350 and alive patients a score of 0.    
Our reasoning behind this factor was that: if the patient was actually dead or alive seemed to be the most important part of our 'death score' since it was the only way we knew for sure if the patient's conditions led to death so we assigned it a higher weight. 
Using dbo.death, we assigned patiets with a death_date to a 'death score' of 350 and those without to a 'death score' of 0.

Our fourth and final factor was the total number of drugs prescribed for each patientID and its death score maximum value was 150.
Our reasoning behind this factor was that: Due to the multiple drugs being used, and lack of clinical knowledge, we weighted drugs prescribed as lower than the other parts of the death score. 
We weren't sure whether the more drugs prescribed correlated to a greater severity of condition or a great recovery of the condition.
However, we did ensure that the drugs we counted were only prescribed by the doctor, the hospital, or the pharmacy and excluded self-reported drug use by patients.
We used dbo.drug_exposure to count the total number of drugs associated with each patient ID and used drug_type_concept_id to filter the type of drugs we counted in our total.
To assign a number between 1-150 (the score) we sorted the data by the total number of drugs prescribed in ascending order. 
Since we had a total of 2,500 patients, we divided the data into 150 parts to find 150 even categories of 17 patientIDs.
We assigned the number 1 to the first 17 patientIDs, 2 to the second 17 patient IDs, and so on until we reached 150 for the last 17 patientIDs. 
We ran into some errors due to our dataset containing NaN values so we cleaned the dataset by filling the NaN values by the mean number of drugs prescribed.

We then proceeded by adding all the individual parts of the death score to get a total death score value and added it to the Age dataframe we had created earlier.

We decided to use the k-means clustering algorithm to analyze the clinical data and to find any sort of patterns in our data. 
We fit the k-means algorithm to our final dataframe and used the elbow method to find the ideal number of clusters needed for the K-clusters. 
We found the optimal number of clusters to be 4.

We then used the kmeans.fit_predict command to predict which cluster each dataset belongs to and plotted the data on a scatter plot with the clusters in different colors and a centroid.
Our graph 'Clusters of UCI Patients' has an x-axis of the Age(in years) and a y-axis of the Death Score(1 - 1000).

CONCLUSION: We found our hypothesis to be invalid. 
Age and death did not seem to have any sort of correlation however we did find some patterns as shown in our scatter plot.
Between ages 0 and 20 there were a lesser amount of patients toward the higher death score side of the graph leading us to believe that, the age group between 0 and 20 allows the highest rate of recoverability from a condition.
Also, this may be due to a lesser amount of data on patients between 0 and 20. There was an aggregation of data points towards the age 0 that we can attribute to early childhood deaths maybe cause by childbirth issues.
There seems to be a greater disparity in death score values of over 600 showing that there were less dead patients than alive patients in our dataset.
We can name our clusters: cyan = least likely to die, red = less likely to die, purple = more likely to die, and blue = most likely to die. 

In further studies, we can use a different machine learning algorithm due to the large size of our data and lack of distinct clusters.
Also, age is not necessarily the best input to measure death against since a person of any age can die at any time.
It would more useful to test something like hospital costs with our death score to see if patients who ended up spending more money for their treatments had a lower death score.
Unfortuantely, we were not able to use the dbo.cost SQL database due to protected health information reasons.










