Google Associate Cloud Engineer Course Work

This project aims at creation cloud service take advantage of the Google Cloud Platform Capabilities. Through this project, will demonstrate how we organize, development and deploy a cloud-based service by:

    (1)	Creating a project for a business initiative.
    (2)	Adding the roles, service accounts and associating them to business stakeholders.
    (3)	Allocating project resources, and 
    (4)	Deploying the resources to execute the business service.
    (5)     Monitoring the project resources.

In order to reach former project milestones, I ‘will explore multiple GCP capabilities such as: 

    (1)	Cloud IAM – Cloud Resource Management 
    (2)	Virtual Private Cloud (VPC) Network – Configuring Networks, subnetworks, Firewall and Routes
    (3) Networing Services - Configuring Network Loadbalancers
    (4)	Compute Engine – Cloud Computing with Virtual Machines (VM)
    (5)	Cloud Monitoring – VM Monitoring
    (6)	Cloud Marketplaces – Third-party resources exploration
    (7)	Deployment Manager
    (8)	Cloud SQL and BigQuery – Data Management
    (9)	Cloud Functions – Running Serverless Functions  
    (10)	Google Kubernetes Engine
    (11)    Google Cloud Boot


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
      
      i)  Exploring default network ( deleting and adding firewall rules)
      
      declare -a rules=("${ gcloud compute firewall-rules list --format='table(name)'}")
      for r in $rules[@]; do
          $(gcloud compute firewall-rules $r --quiet)
      done;
      gcloud compute --project=qwiklabs-gcp-03-12714944c726 firewall-rules create NAME --direction=INGRESS --priority=1000 --       network=mynetwork --action=ALLOW --rules=PROTOCOL:PORT,...
      
      
      ii) Creating auto mode networks
      
          gcloud compute networks create mynetwork --project=qwiklabs-gcp-03-12714944c726 --subnet-mode=auto --bgp-routing-mode=regional
          a. Switching to custom mode
           gcloud compute networks update mynetwork --switch-to-custom-subnet-mode --quiet

    gcloud compute firewall-rules create mynetwork-allow-icmp --project=qwiklabs-gcp-03-12714944c726 --network=projects/qwiklabs-gcp-03-12714944c726/global/networks/mynetwork --description=Allows\ ICMP\ connections\ from\ any\ source\ to\ any\ instance\ on\ the\ network. --direction=INGRESS --priority=65534 --source-ranges=0.0.0.0/0 --action=ALLOW --rules=icmp

    gcloud compute firewall-rules create mynetwork-allow-internal --project=qwiklabs-gcp-03-12714944c726 --network=projects/qwiklabs-gcp-03-12714944c726/global/networks/mynetwork --description=Allows\ connections\ from\ any\ source\ in\ the\ network\ IP\ range\ to\ any\ instance\ on\ the\ network\ using\ all\ protocols. --direction=INGRESS --priority=65534 --source-ranges=10.128.0.0/9 --action=ALLOW --rules=all

    gcloud compute firewall-rules create mynetwork-allow-rdp --project=qwiklabs-gcp-03-12714944c726 --network=projects/qwiklabs-gcp-03-12714944c726/global/networks/mynetwork --description=Allows\ RDP\ connections\ from\ any\ source\ to\ any\ instance\ on\ the\ network\ using\ port\ 3389. --direction=INGRESS --priority=65534 --source-ranges=0.0.0.0/0 --action=ALLOW --rules=tcp:3389

    gcloud compute firewall-rules create mynetwork-allow-ssh --project=qwiklabs-gcp-03-12714944c726 --network=projects/qwiklabs-gcp-03-12714944c726/global/networks/mynetwork --description=Allows\ TCP\ connections\ from\ any\ source\ to\ any\ instance\ on\ the\ network\ using\ port\ 22. --direction=INGRESS --priority=65534 --source-ranges=0.0.0.0/0 --action=ALLOW --rules=tcp:22
      
      ii) Creating to custom mode networks
      gcloud compute networks update mynetwork --switch-to-custom-subnet-mode --quiet
      {
  "autoCreateSubnetworks": False,
  "creationTimestamp": "2020-09-04T09:50:53.266-07:00",
  "description": "",
  "id": "915106178311114370",
  "kind": "compute#network",
  "name": "mynetwork",
  "routingConfig": {
    "routingMode": "REGIONAL"
  },
  "selfLink": "projects/qwiklabs-gcp-03-12714944c726/global/networks/mynetwork",
  "subnetworks": [
    "projects/qwiklabs-gcp-03-12714944c726/regions/europe-west1/subnetworks/mynetwork",
    "projects/qwiklabs-gcp-03-12714944c726/regions/us-east1/subnetworks/mynetwork",
    "projects/qwiklabs-gcp-03-12714944c726/regions/asia-southeast2/subnetworks/mynetwork",
    "projects/qwiklabs-gcp-03-12714944c726/regions/us-west4/subnetworks/mynetwork",
    "projects/qwiklabs-gcp-03-12714944c726/regions/us-west3/subnetworks/mynetwork",
    "projects/qwiklabs-gcp-03-12714944c726/regions/europe-north1/subnetworks/mynetwork",
    "projects/qwiklabs-gcp-03-12714944c726/regions/asia-northeast3/subnetworks/mynetwork",
    "projects/qwiklabs-gcp-03-12714944c726/regions/europe-west4/subnetworks/mynetwork",
    "projects/qwiklabs-gcp-03-12714944c726/regions/europe-west2/subnetworks/mynetwork",
    "projects/qwiklabs-gcp-03-12714944c726/regions/northamerica-northeast1/subnetworks/mynetwork",
    "projects/qwiklabs-gcp-03-12714944c726/regions/asia-east1/subnetworks/mynetwork",
    "projects/qwiklabs-gcp-03-12714944c726/regions/us-west2/subnetworks/mynetwork",
    "projects/qwiklabs-gcp-03-12714944c726/regions/europe-west3/subnetworks/mynetwork",
    "projects/qwiklabs-gcp-03-12714944c726/regions/us-west1/subnetworks/mynetwork",
    "projects/qwiklabs-gcp-03-12714944c726/regions/us-central1/subnetworks/mynetwork",
    "projects/qwiklabs-gcp-03-12714944c726/regions/asia-northeast2/subnetworks/mynetwork",
    "projects/qwiklabs-gcp-03-12714944c726/regions/asia-east2/subnetworks/mynetwork",
    "projects/qwiklabs-gcp-03-12714944c726/regions/asia-southeast1/subnetworks/mynetwork",
    "projects/qwiklabs-gcp-03-12714944c726/regions/australia-southeast1/subnetworks/mynetwork",
    "projects/qwiklabs-gcp-03-12714944c726/regions/southamerica-east1/subnetworks/mynetwork",
    "projects/qwiklabs-gcp-03-12714944c726/regions/europe-west6/subnetworks/mynetwork",
    "projects/qwiklabs-gcp-03-12714944c726/regions/us-east4/subnetworks/mynetwork",
    "projects/qwiklabs-gcp-03-12714944c726/regions/asia-northeast1/subnetworks/mynetwork",
    "projects/qwiklabs-gcp-03-12714944c726/regions/asia-south1/subnetworks/mynetwork"
  ]
}

      
      [ { "allowed": [ { "IPProtocol": "icmp" } ], "creationTimestamp": "2020-09-02T05:53:19.975-07:00", "description": "Allow ICMP from anywhere", "direction": "INGRESS", "disabled": false, "id": "6612380008690363216", "kind": "compute#firewall", "logConfig": { "enable": false }, "name": "default-allow-icmp", "network": "https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-03-12714944c726/global/networks/default", "priority": 65534, "selfLink": "https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-03-12714944c726/global/firewalls/default-allow-icmp", "sourceRanges": [ "0.0.0.0/0" ] }, { "allowed": [ { "IPProtocol": "tcp", "ports": [ "0-65535" ] }, { "IPProtocol": "udp", "ports": [ "0-65535" ] }, { "IPProtocol": "icmp" } ], "creationTimestamp": "2020-09-02T05:53:19.932-07:00", "description": "Allow internal traffic on the default network", "direction": "INGRESS", "disabled": false, "id": "7538125836719893328", "kind": "compute#firewall", "logConfig": { "enable": false }, "name": "default-allow-internal", "network": "https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-03-12714944c726/global/networks/default", "priority": 65534, "selfLink": "https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-03-12714944c726/global/firewalls/default-allow-internal", "sourceRanges": [ "10.128.0.0/9" ] }, { "allowed": [ { "IPProtocol": "tcp", "ports": [ "3389" ] } ], "creationTimestamp": "2020-09-02T05:53:19.961-07:00", "description": "Allow RDP from anywhere", "direction": "INGRESS", "disabled": false, "id": "4770309286085667664", "kind": "compute#firewall", "logConfig": { "enable": false }, "name": "default-allow-rdp", "network": "https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-03-12714944c726/global/networks/default", "priority": 65534, "selfLink": "https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-03-12714944c726/global/firewalls/default-allow-rdp", "sourceRanges": [ "0.0.0.0/0" ] }, { "allowed": [ { "IPProtocol": "tcp", "ports": [ "22" ] } ], "creationTimestamp": "2020-09-02T05:53:19.945-07:00", "description": "Allow SSH from anywhere", "direction": "INGRESS", "disabled": false, "id": "6115244746838518608", "kind": "compute#firewall", "logConfig": { "enable": false }, "name": "default-allow-ssh", "network": "https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-03-12714944c726/global/networks/default", "priority": 65534, "selfLink": "https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-03-12714944c726/global/firewalls/default-allow-ssh", "sourceRanges": [ "0.0.0.0/0" ] } ]


3. Cloud Computing Resources (Virtual Machines) with Compute Engine

  Like bare steel server hosted in-premisse, we rely on server running on Virtual Machines. The GCP Compute  Engine resource enable the creation on VM Instances as one would need to deploy web servers, application server, etc.
  
  gcloud beta compute --project=qwiklabs-gcp-03-12714944c726 instances create mynet-eu-vm --zone=europe-west1-c --machine-type=n1-standard-1 --subnet=mynetwork --private-network-ip=10.132.0.2 --network-tier=PREMIUM --maintenance-policy=MIGRATE --service-account=567764395026-compute@developer.gserviceaccount.com scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --image=debian-9-stretch-v20200902 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=mynet-eu-vm --reservation-affinity=any

4. Exploring Cloud Monitoring to Manage Virtual Machines

5. 
  



