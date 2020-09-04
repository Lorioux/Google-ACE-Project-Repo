Google Associate Cloud Engineer Course Work

This project aims at creation cloud service take advantage of the Google Cloud Platform Capabilities. Through this project, will demonstrate how we organize, development and deploy a cloud-based service by:

    (1)	Creating a project for a business initiative.
    (2)	Adding the roles, service accounts and associating them to business stakeholders.
    (3)	Allocating project resources, and 
    (4)	Deploying the resources to execute the business service.
    (5)     Monitoring the project resources.

In order to reach former project milestones, I ‘will explore multiple GCP capabilities such as: 

    (1)	Cloud IAM – Cloud Resource Management 
    (2)	Virtual Private Cloud (VPC) Networking – Configuring Networks, subnetworks, Firewall and LoadBalancers
    (3)	Compute Engine – Cloud Computing with Virtual Machines (VM)
    (4)	Cloud Monitoring – VM Monitoring
    (5)	Cloud Marketplaces – Third-party resources exploration
    (6)	Deployment Manager
    (7)	Cloud SQL and BigQuery – Data Management
    (8)	Cloud Functions – Running Serverless Functions  
    (9)	Google Kubernetes Engine
    (10)    Google Cloud Boot


1.	Project Access Management with Cloud IAM

  As any business initiative need to be protected for non-authorized stakeholders, the cloud-based project (initiative)is handled similarly. Only affected – most directly affected stakeholders - need to have adequate minimal authorization level to access project information and resources. Herein, we apply the same principle for this demo project by:
  
    i)    Setting up stakeholder(users) roles
    ii)   Setting up the Service Accounts
    iii)  Binding Members to the Service Accounts and Roles
    
        
        <code>
            gcloud iam service-accounts create read-bucket-objects 
        </code>
        
        Create a VM with the Service Account User

        gcloud beta compute --project=qwiklabs-gcp-01-586709e6acad instances create demoiam --zone=us-central1-c --machine-type=f1-micro --subnet=default --network-tier=PREMIUM --maintenance-policy=MIGRATE --service-account=read-bucket-objects@qwiklabs-gcp-01-586709e6acad.iam.gserviceaccount.com --scopes=https://www.googleapis.com/auth/cloud-platform --image=debian-9-stretch-v20200902 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=demoiam --reservation-affinity=any


2. Network Management with VPC Networks

  To ensure communication and accessibility of business service to in- and out-side world, a virtual computer network (VPC) is required. Google Cloud Virtual Private Cloud (VPC) provides networking functionality to Compute Engine virtual machine (VM) instances, Kubernetes Engine containers, and the App Engine flexible environment. In other words, without a VPC network, you cannot create VM instances, containers, or App Engine applications. A VPC network is a global resource that consists of a list of regional virtual subnetworks (subnets) in data centers, all connected by a global wide area network (WAN). VPC networks are logically isolated from each other in Google Cloud. We aim at exploring defaut, auto and customizable networking capabilities by:
      
      i) Creating auto mode networks
      ii) Creating custom mode networks

3. Cloud Computing Resources (Virtual Machines) with Compute Engine

  Like bare steel server hosted in-premisse, we rely on server running on Virtual Machines. The GCP Compute  Engine resource enable the creation on VM Instances as one would need to deploy web servers, application server, etc.    

4. Exploring Cloud Monitoring to Manage Virtual Machines

5. 
  



