
# SageMaker
- fully managed service to build machine learning models.
- It can do everything at one place 
- It can collect the data=> Build and train models=> Deploy models on sagemaker itself.

### Supervised Algorithms:
- Linear Regressions
- KNN Algorithms

### Unsupervised Algorithms:
- PCA
- K Means
- Anomaly Detection
## Automatic Model Tuning:
- Define the objective of the tuning
- It will automatically choose the hyperparameters ranges, maximum runtimes of a tuning job, stop conditions etc.
## Model Deployment Types:
- One click deployment, automatic scaling given to this service.
- Managed solution-reduce overhead
- Real time deployment: 
    - One prediction at a time
    - 6 mb per data is the maximum you can put.
    - go for this when you need AI Model for Mobile or Web Application and you need the lowest latency.
- Serverless deployment:
    - Saves cost and idle between traffic spikes.
    - More latency due to cold starts
    - Use it when the data can handle cold spikes
    - 6 mb per data is the maximum you can put.
    
![image.png](AWS%20Certified%20AI%20Practitioner%2014e898cf4c9e80b79415dc48856fae87/image%205.png)
    
- Asynchonrous:
    - For large payload sizes upto `1 gb`
    - Long processing times
    - `It is near real time not real time`
    - Both request and response are stored in S3
    - Processing time is maximum one hour.
    
- Batch Processing
    - Predictions for entire data sets⇒ Multiple predictions can be given as the entire data set is given as an input
    - Processing time is maximum one hour.
    - `Latency is higher as the entire dataset is given.`
    - Concurrent processing of data⇒ BEST CHOICE.
    - 100MB per batch(mini batch)→ Per invocation so if the data is of 1gb we can divide it into batches/chunks of 100mb each directly more than 100mb is not possible without chunking it.
    
![image.png](AWS%20Certified%20AI%20Practitioner%2014e898cf4c9e80b79415dc48856fae87/image%206.png)

## Sage Maker Studio

- Team Collaboration
- Automated Workflows
- Deploy ML models everything at one place.
- UI to use all the features at one place is called SageMaker Studio.
![alt text](image.png)
### Sage-maker Data Wrangler:

- Prepare the tabular and image data for machine learning.
- Transformation or data preparation service.
- Data preparation, transformations on the data, feature engineering everything is done here.
- `SQL SUPPORTED`
- `Data Quality Feature Supported`
    ![alt text](image-1.png)


    ![image.png](AWS%20Certified%20AI%20Practitioner%2014e898cf4c9e80b79415dc48856fae87/image%2013.png)
    ![alt text](image-2.png)
    ![alt text](image-3.png)
    ![alt text](image-4.png)
### What are ML Features?

- Features are the inputs to ML Models used during training of those models and used for inference.
![alt text](image-5.png)
## Sage-Maker Feature Store

- Ingest features from a variety of sources
- Ability to define the data transformation and logic in the feature directly using feature store
- can publish directly from sage maker data wrangler to the feature store also.(image and tabular data features of data wrangler)

### Sage Maker Clarify:

- Evaluate Foundational Models: does comparison of models
- gives some tasks to these models and then clarify evaluates the models which one is better and effective.
- Evaluate human factor such as friendliness or humour
part of sage maker studio
- We can use our **own team to evaluate these models⇒ Involves humans also in clarify**
![alt text](image-6.png)

#### **Model Explainability in Clarify:**

- Set of tools that explain how these model works internally to the user.
- Understand the prediction logic of these models and explain the same to the user in easy language.
- Helps in increasing the trust and understanding of the model with the client as well as the user.
- Can detect human biases in our own datasets and models
- Specify the input features and biases in our datasets which will be automatically detected
- It is not focused on manually labeling data like Ground Truth but can provide reports and visualizations that can be reviewed by humans (e.g., fairness reports or feature importance).
- Use Case: Clarify is used to monitor and evaluate the fairness of machine learning models and to ensure that they are not unfairly biased based on sensitive attributes like gender, age, or race.
- For example, after training a model, you could use Clarify to check if there are any biases in predictions. If you find that the model is making biased decisions based on gender or race, you can investigate those patterns with human feedback or make adjustments to your dataset.

![image.png](AWS%20Certified%20AI%20Practitioner%2014e898cf4c9e80b79415dc48856fae87/image%2014.png)



### Sage Maker Ground Truth:
- Ground Truth is a data labeling service that uses human annotators to help label datasets for training machine learning models.
- Human Feedback: Ground Truth allows you to create labeling jobs that can be reviewed and annotated by human workers (often through Amazon Mechanical Turk, or private labeling teams).
- Use Case: You typically use Ground Truth when you need a high-quality labeled dataset for supervised learning. This is important when the model’s training data needs to be manually curated, such as identifying objects in images, transcribing audio, or categorizing text.
- Reinforced learning where human feedback is included in the `reward` function.
- Labelling Data In Your Inputs as per human perecptive: `Use Sagemaker ground truth plus`
- Clarify also gives feedback but that is an AI Service giving the feedback when you want human feedback to be included in the model go for SageMaker Ground Truth.

![image.png](AWS%20Certified%20AI%20Practitioner%2014e898cf4c9e80b79415dc48856fae87/image%2015.png)



## SageMaker Governance

### Sagemaker Model Cards:
- This is used for creating `ML Documentation`.
- They serve as a standardized format for capturing key details about a model's development, its performance, its intended use, and any potential limitations. 
- The goal is to enhance transparency, accountability, and trust in the model by providing clear documentation that can be shared with stakeholders.

### Sagemaker Model Dashboard:
- Repo for all models and their insights+information
- Can be access from the sagemaker console
- help you find models which violate thresholds you have set for data quality, inference etc.
![alt text](image-7.png)
### Sagemaker Model Monitor
- monitors the quality of deployed models, can be scheduled or always. 
- We get alerts if they violate the quality standards
- Kind of monitoring tool for already deployed models.
![alt text](image-8.png)
### Sagemaker Model Registry: 
- Central repo to track manage and version machine learning models like git. 
- We can have different version for models, Manage Model Approval status of a model to take the approvals before being deployed.

### Sagemaker Pipelines: 
- Automate the process of building, deploying and training a machine learning model. eg: mlops
- This is a CI-CD Pipeline for sagemaker.

#### Supported Steps:
- processing-feature enggineering
- Training
- Tuning for hyperparameter Training
- AutoML-Auto training
- Model: Create or register a model
- `Quality Check`
- `Clarify Check: Perform drifts check against baselines.`

![image.png](AWS%20Certified%20AI%20Practitioner%2014e898cf4c9e80b79415dc48856fae87/image%2016.png)

### Sage-Maker Role Manager:

- Define roles within sagemaker like IAM.
- Example: Data Engineer, Data Scientist etc

### Sage-Maker Jumpstart:
- pre trained foundation models or computer vision models directly on the sagemaker similar to bedrock
- Collection is larger compare to bedrock it has huggingface,databricks, meta, stability AI etc.
- Full control on the deployment of the model.

![image.png](AWS%20Certified%20AI%20Practitioner%2014e898cf4c9e80b79415dc48856fae87/image%2017.png)

### Sagemaker Canvas: 
- Build ML models using a visual interface.
- `no coding required for this.`
- powered by sagemaker autopilot. 
- part of sagemaker studio and does something calls `AUTO-MLOPS`
![alt text](image-11.png)

### ML-FLOW
- MLFLOW: Open source tool which helps ML teams manage the entire machine learning lifecycle.
- Used to manage the entire Machine Learning Model lifecycle from code till deployment
![alt text](image-10.png)
### ML Flow Tracking servers:
- Used to track runs and experiments

![image.png](AWS%20Certified%20AI%20Practitioner%2014e898cf4c9e80b79415dc48856fae87/image%2018.png)

## Summary
![image.png](AWS%20Certified%20AI%20Practitioner%2014e898cf4c9e80b79415dc48856fae87/image%2019.png)