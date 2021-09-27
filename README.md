# scania-trucks


Problem Statement-

    Defect detection in Air Pressure system (APS) of a truck.


Data Description-

    The dataset consists of data collected from heavy Scania trucks in everyday usage. The system in focus is the Air Pressure system (APS) which generates pressurised
    air that are utilized in various functions in a truck,such as braking and gear changes. The datasets' positive class consists of component failures
    for a specific component of the APS system. The negative class consists of trucks with failures for components not related to the APS. The data consists of a subset of all available data, selected by experts.
    The attribute names of the data have been anonymized for proprietary reasons. 

    Apart from training files, we also require a "schema" file from the client, which contains all the relevant information about the training files such as:
    Name of the files, Length of Date value in FileName, Length of Time value in FileName, Number of Columns, Name of the Columns, and their datatype.

      
      
 
Data Validation -
   
   In this step, we perform different sets of validation on the given set of training files.  
   
     1. Name Validation- We validate the name of the files based on the given name in the schema file. We have created a regex pattern as per the name given in the schema file to use for validation. After validating the pattern in the name, we check for the length of date in the file name as well as the length of time in the file name. If all the values are as per requirement, we move such files to "Good_Data_Folder" else we move such files to "Bad_Data_Folder."

     2. Number of Columns - We validate the number of columns present in the files, and if it doesn't match with the value given in the schema file, then the file is moved to "Bad_Data_Folder."


     3.	Name of Columns - The name of the columns is validated and should be the same as given in the schema file. If not, then the file is moved to "Bad_Data_Folder".

     4. The datatype of columns - The datatype of columns is given in the schema file. This is validated when we insert the files into Database. If the datatype is wrong, then the file is moved to "Bad_Data_Folder".


     5. Null values in columns - If any of the columns in a file have all the values as NULL or missing, we discard such a file and move it to "Bad_Data_Folder".

Data Insertion in Database-

      1) Database Creation and connection - Create a database with the given name passed. If the database is already created, open the connection to the database. 
   
      2) Table creation in the database - Table with name - "Good_Data", is created in the database for inserting the files in the "Good_Data_Folder" based on given column names and datatype in the schema file. If the table is already present, then the new table is not created and new files are inserted in the already present table as we want training to be done on new as well as old training files.   
   
      3) Insertion of files in the table - All the files in the "Good_Data_Folder" are inserted in the above-created table. If any file has invalid data type in any of the columns, the file is not loaded in the table and is moved to "Bad_Data_Folder".


Model Training -

    1) Data Export from Db - The data in a stored database is exported as a CSV file to be used for model training.
    
    2) Data Preprocessing   
      a) Drop columns not useful for training the model. Such columns were selected while doing the EDA.
      b) Replace the invalid values with numpy “nan” so we can use imputer on such values.
      d) Check for null values in the columns. If present, impute the null values 
      e) Scale the training and test data separately 
      
    3) Clustering - KMeans algorithm is used to create clusters in the preprocessed data. The optimum number of clusters is selected by plotting the elbow plot, and for the dynamic selection of the number of clusters, we are using "KneeLocator" function. The idea behind clustering is to implement different algorithms
       To train data in different clusters. The Kmeans model is trained over preprocessed data and the model is saved for further use in prediction.
       
    4) Model Selection -We are using two algorithms, "SVM" and "KNN". The data is trained on both the algorithms with the best parameters derived from GridSearch. We calculate the AUC scores for both models and select the model with the best score.  
    
Prediction Data Description- 

      Client will send the data in multiple set of files in batches at a given location. Data will contain predictors in 170 columns.
      Apart from prediction files, we also require a "schema" file from client which contains all the relevant information about the training files such as:
      Name of the files, Length of Date value in FileName, Length of Time value in FileName, Number of Columns, Name of the Columns and their datatype.

 Data Validation-
      
      In this step, we perform different sets of validation on the given set of training files.  
      
       1) Name Validation- We validate the name of the files on the basis of given Name in the schema file. We have created a regex pattern as per the name given in schema file, to use for validation. After validating the pattern in the name, we check for length of date in the file name as well as length of time in the file name. If all the values are as per requirement, we move such files to "Good_Data_Folder" else we move such files to "Bad_Data_Folder". 
       
       2) Number of Columns - We validate the number of columns present in the files, if it doesn't match with the value given in the schema file then the file is moved to "Bad_Data_Folder". 
       
       3) Name of Columns - The name of the columns is validated and should be same as given in the schema file. If not, then the file is moved to "Bad_Data_Folder". 
       
       4) Datatype of columns - The datatype of columns is given in the schema file. This is validated when we insert the files into Database. If dataype is wrong then the file is moved to "Bad_Data_Folder". 
       
       5) Null values in columns - If any of the columns in a file has all the values as NULL or missing, we discard such file and move it to "Bad_Data_Folder".  


Data Insertion in Database -

       1) Database Creation and connection - Create database with the given name passed. If the database is already created, open the connection to the database. 
       
       2) Table creation in the database - Table with name - "Good_Data", is created in the database for inserting the files in the "Good_Data_Folder" on the basis of given column names and datatype in the schema file. If table is already present then new table is not created, and new files are inserted the already present table as we want training to be done on new as well old training files.  
       
       3) Insertion of files in the table - All the files in the "Good_Data_Folder" are inserted in the above-created table. If any file has invalid data type in any of the columns, the file is not loaded in the table and is moved to "Bad_Data_Folder".

Prediction -

     1) Data Export from Db - The data in the stored database is exported as a CSV file to be used for prediction.
     
     2) Data Preprocessing   
        a) Drop columns not useful for training the model. Such columns were selected while doing the EDA.
        b) Replace the invalid values with numpy “nan” so we can use imputer on such values.
        c) Check for null values in the columns. If present, impute the null values.
        d) Scale the training data.
        
     3) Clustering - KMeans model created during training is loaded, and clusters for the preprocessed prediction data is predicted.
     
     4) Prediction - Based on the cluster number, the respective model is loaded and is used to predict the data for that cluster.
     
     5) Once the prediction is made for all the clusters, the predictions along with the original names before label encoder are saved in a CSV file at a given location and the location is returned to the client.
 





