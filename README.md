# Operationalizing Machine Learning in Azure ML
## Overview 
This project is part of the Machine Learning Engineer with Microsoft Azure Udacity Nanodegree. The project involves creation of a cloud-based AutoML model, deployment into an endpoint, consumption of the endpoint for prediction and deploying a pipeline. 

## Architectural Diagram
![diagram](images/Architecture_diag.png)

## Key Steps
The project involves the following key steps:

1. **Authentication** : Installing the Azure Machine Learning Extension, creating a Service Principal and associating it with the workspace. 
2. **Automated ML Experiment** : Uploading the dataset, initialising a compute cluster and running the AutoML experiment to find the best classification model. 
3. **Deploy the best model** : Deploying the best classification model into an endpoint. 
4. **Enable logging** : Enabling logging and application insights to monitor the endpoint. 
5. **Swagger Documentation** : Using swagger documentation with the swagger.json data from the endpoint to browse the endpoint requests and responses.
6. **Consume model endpoints** : Interacting with the model endpoints using test JSON payloads. 
7. **Create and publish a pipeline** : Automation of the workflow and publication of the pipeline with the endpoint. 

## 1. Authentication
The provided Udacity workspace was pre-configured, so this step was skipped.

## 2. Automated ML Experiment
### Registered Datasets
First, the [Bank Marketing](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) dataset was uploaded to the workspace.
![Datasets](images/07_Datasets.PNG)

### Setting up and Running the AutoML Experiment
A **Standard_DS12_v2** cluster was created with **minimum number of nodes** set to 1, **Concurrency** set to 5 and **Exit criterion** changed to 1 hour. 
The AutoML  was set to a Classification type with target column set as **'y'**.
![CompletedExperiments](images/02_CompletedExperimentRun.PNG)

### Exploring the models
After the AutoML run had completed, the list of models under the **Models** tab was listed by descending AUC weighted value.
![ModelList](images/03_ModelsList.PNG)

The best model can be accessed by clicking on it to view a summary of the model.
![Best Model](images/04_BestModel.PNG)

The best model details can be accessed through the details tab for the model.
![Model Detials](images/05_BestModelDetails.PNG)

The best model metrics can also be accessed through the metrics tab.
![Model Metrics](images/06_BestModelMetrics.PNG)

## 3. Deploy the best model
The best model can then be deployed into an endpoint using the *Deploy* option. The Model was deployed as a web service, selecting *Azure Container Instance* and enabling authentication. Wait until the endpoint has reached a *Healthy* status.

Accessing the model through the Experiments section will show if the model has been deployed into an endpoint. 
![Model Deployment](images/08_ModelDeployment.PNG)

In the Endpoints section, the list will show the deployed endpoint for the model. 
![Endpoint List](images/09_EndpointList.PNG)

## 4. Enable logging

Enabling application insights is useful to monitor the endpoint activity. First the config.json file can be downloaded for the workspace by selecting the workspace ID from the top right in Azure ML studio. The config.json file needs to be placed in the same directory as the logs.py, and then ensure the endpoint status is healthy before running the logs.py script from the terminal:
`python logs.py`

Once the logs.py script has run, the output will look like this:
![EnableInsights](images/10_EnableInsights.PNG)

Then check the endpoint again, once the status is back to healthy the Application Insights Enabled option will switch to True, with a link to the insights. 
![EndpointInsights](images/11_Endpoint_InsightsEnables.PNG)

## 5. Swagger Documentation
To consume the best AutoML model using Swagger, download the **swagger.json** file from the endpoint. Save this into the **swagger** directory with **serve.py**. Then run the **swagger.sh** and **serve.py** files to explore the documentation for the endpoint.

Within this project, going to `http://localhost:9000` will open the Swagger UI. Within the UI, enter the URL that is serving the **swagger.json** file and click **Explore**, in this project the URL is `http://localhost:8000/swagger.json` which is being served by the **serve.py** script. Then the following views will appear showing the available requests and responses in the REST API. 

The following image shows the name of the deployed endpoint and the get and post requests available.
![swagger.sh](images/12_SwaggerUI.PNG)

The image here shows soem details of the request and reponse for the post request, including required format of the JSON payload. 
![swagger_responses.sh](images/13_SwaggerResponses.PNG)

## 6. Consume model endpoints

To interact with the model using the endpoint, the **scoring_uri** and the **key** must be updated in the **endpoint.py** script prior to running the script in the terminal. The **endpoint.py** script has 2 example datasets, one that should result in a yes and the other in a no. The results from running the script are shown below. 

![Consume the endpoint](images/14_EndpointResponses.PNG)

## 7. Create and publish a pipeline

The Jupyter Notebook named **aml-pipelines-with-automated-machine-learning-step** was used to create a **Pipeline** The notebook aids in creating a pipeline for running the AutoML experiment and then deploying the best model into a pipeline endpoint and consuming the endpoint with a pipeline run.

After running through the notebook, there is a **Run Details** widget that shows the progress of the running pipeline, a screenshot of this after a completed run is show below:
![RunDetails](images/15_RunDetailsNotebook.PNG)

In the **Pipelines** section of ML Studio, the list of pipeline runs is also available to view, as shown below. 
![PipelineRuns](images/16_PipelineRuns.PNG)

There is also a **Pipeline Endpoints** tab which shows a list of all the endpoints, as shown below. 
![Pipeline Endpoint](images/17_PipelineEndpoints.PNG)

By clicking on a live pipeline endpoint it is also possible to view the published endpoint details as shown here, which includes if the endpoint is active and a link to the REST endpoint. 
![Published Endpoint](images/18_PublishedPipeline.PNG)

The pipeline runs shown in the following image show a numbero of runs which are REST api requests served to the pipeline through the notebook
![Pipeline EndpoingRuns](images/21_PipelineEndpointRuns.PNG)

Finally, it is possible to drill down into each run to view the status and other details of each request performed against the endpoint. An example of one is shown here:
![Pipeline Run_Overview](images/19_PipelineRunOverview.PNG)

## Screen Recording

- Link to screen recording **submission** - https://www.youtube.com/watch?v=S9j_hDimD_0

## Suggested Improvements
The dataset is highly imbalanced, so addressing the imbalanced classes should yield an improvement over the existing model because with around 90% of the of the records belonging to one class, even a poor model could classify all records as belonging to the same class and achieve a very high accuracy in class prediction. 
