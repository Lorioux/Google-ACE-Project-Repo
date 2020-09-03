Google Associate Cloud Engineer Course Work

This project aims at creation cloud service take advantage of the Google Cloud Platform Capabilities. Through this project, I will demonstrate how we start and development and deploy a cloud-based service by:

    (1)	creating a project for a business initiative.
    (2)	defining the roles of all business stakeholders, and authorization.
    (3)	allocating project resources, and 
    (4)	deploying the resources to execute the business service.

In order to reach former project milestones, I ‘will explore multiple GCP capabilities such as: 

    (1)	Cloud IAM – Access Management
    (2)	Virtual Private Cloud (VPC) Networks – Network Management
    (3)	Compute Engine – Virtual Machines (VM) Provisioning
    (4)	Cloud Monitoring – VM Monitoring
    (5)	Cloud Marketplaces – Third-party resources exploration
    (6)	Deployment Manager
    (7)	Cloud SQL and BigQuery – Data Management
    (8)	Cloud Functions – Running Serverless Functions  
    (9)	Google Kubernetes Engine
    (10)	 Google Cloud Boot


1.	Project Access Management with Cloud IAM

  As any business initiative need to be protected for non-authorized stakeholders, the cloud-based project (initiative) handled similarly as in-premise traditional project. Only affected – most directly affected stakeholders - need to have adequate minimal authorization levels to access project information and resources. Herein, we apply the same principle for two project stakeholders. One has full project ownership role, and another has some resources and capabilities writing and viewing (access) roles.

Explore the IAM console




Set up the Service Account User


Add Member in the Service Account


Create a VM with the Service Account User

gcloud beta compute --project=qwiklabs-gcp-02-fe32105891fc instances create demoiam --zone=us-central1-c --machine-type=e2-micro --subnet=default --network-tier=PREMIUM --maintenance-policy=MIGRATE --service-account=read-bucket-objects@qwiklabs-gcp-02-fe32105891fc.iam.gserviceaccount.com --scopes=https://www.googleapis.com/auth/cloud-platform --image=debian-9-stretch-v20200902 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=demoiam --reservation-affinity=any


