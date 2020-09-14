# Google Associate Cloud Engineer Course Work




#### This project aims at creation cloud service take advantage of the Google Cloud Platform Capabilities. Through this project, will demonstrate how we organize, development and deploy a cloud-based service by:
<section>
   <ol>
        <li> Creating a project for a business initiative. </li>
        <li> Adding the roles, service accounts and associating them to business stakeholders.</li>
        <li> Allocating project resources, and </li>
        <li> Deploying the resources to execute the business service.</li>
        <li> Monitoring the project resources.</li>
    </ol>
</section>

#### In order to reach former project milestones, I explore multiple GCP capabilities such as: 

    
   <ol>
        <li> Cloud IAM – Cloud Resource Management </li>
        <li> Virtual Private Cloud (VPC) Network – Configuring Networks, subnetworks, Firewall and Routes </li>
        <li> Compute Engine – Cloud Computing with Virtual Machines (VM) </li>
        <li> Networing Services and Network Security - Configuring Network Loadbalancers and Security </li>
        <li> Google Kubernetes Engine </li>
        <li> Cloud BigQuery, Cloud SQL, Cloud Dataproc, Machine Learning – Data Management, Implementing Machine Learning in GCP and Data Analytics </li>
        <li> Cloud Functions – Running Serverless Functions  </li>
        <li> Cloud Monitoring – Cloud Deployment Manager and StackDriver </li>
    </ol>

### 1.	Project Access Management with Cloud IAM

  As any business initiative need to be protected for non-authorized stakeholders, the cloud-based project (initiative)is handled similarly. Only affected – most directly affected stakeholders - need to have adequate minimal authorization level to access project information and resources. Herein, we apply the same principle for this demo project by:
  
   #### i)    Setting up stakeholder(users) roles
              
   #### ii)   Setting up the Service Accounts
   
   #### iii)  Binding Members to the Service Accounts and Roles
   
        gcloud iam service-accounts create read-bucket-objects 
        
   #### iv)   Creating a VM with the Service Account User

        gcloud beta compute --project=qwiklabs-gcp-01-586709e6acad instances create demoiam --zone=us-central1-c --machine-type=f1-micro --subnet=default --network-tier=PREMIUM --maintenance-policy=MIGRATE --service-account=read-bucket-objects@qwiklabs-gcp-01-586709e6acad.iam.gserviceaccount.com --scopes=https://www.googleapis.com/auth/cloud-platform --image=debian-9-stretch-v20200902 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=demoiam --reservation-affinity=any

   
### 2. Network Management with VPC Networks

  To ensure communication and accessibility of business service to in- and out-side world, a virtual computer network (VPC) is required. Google Cloud Virtual Private Cloud (VPC) provides networking functionality to Compute Engine virtual machine (VM) instances, Kubernetes Engine containers, and the App Engine flexible environment. In other words, without a VPC network, you cannot create VM instances, containers, or App Engine applications. A VPC network is a global resource that consists of a list of regional virtual subnetworks (subnets) in data centers, all connected by a global wide area network (WAN). VPC networks are logically isolated from each other in Google Cloud. We aim at exploring defaut, auto and customizable networking capabilities by:
     
   #### i)  Exploring default network ( deleting and adding firewall rules)
       
          ## create an array of firewall rules name 
          declare -a rules=("$( gcloud compute firewall-rules list --format='table(name)')");
        
          ## delete a firewall rules
          for r in $rules; do 
              gcloud compute firewall-rules delete $r --quiet;
          done;
          index=0;
          for n in $nets; do  
              if [ "$index" -eq "0" ]; then    
                 $index = $index + 1; 
              else         
                 echo $n; 
              fi; 
          done
          gcloud compute --project=qwiklabs-gcp-03-12714944c726 firewall-rules create NAME --direction=INGRESS --priority=1000 --network=mynetwork --action=ALLOW --rules=PROTOCOL:PORT,...
    
          gcloud compute --project=qwiklabs-gcp-03-12714944c726 firewall-rules create managementnet-allow-icmp-ssh-rdp --direction=INGRESS --priority=1000 --network=managementnet --action=ALLOW --rules=tcp:22,tcp:3389,icmp --source-ranges=0.0.0.0/0
      
  
      
      
   ####  ii) Creating auto mode networks
      
          gcloud compute networks create mynetwork --project=qwiklabs-gcp-03-12714944c726 --subnet-mode=auto --bgp-routing-mode=regional
          a. Switching to custom mode
                
                gcloud compute networks update mynetwork --switch-to-custom-subnet-mode --quiet
   
                gcloud compute firewall-rules create mynetwork-allow-icmp --project=qwiklabs-gcp-03-12714944c726 --network=projects/qwiklabs-gcp-03-12714944c726/global/networks/mynetwork --description=Allows\ ICMP\ connections\ from\ any\ source\ to\ any\ instance\ on\ the\ network. --direction=INGRESS --priority=65534 --source-ranges=0.0.0.0/0 --action=ALLOW --rules=icmp

                gcloud compute firewall-rules create mynetwork-allow-internal --project=qwiklabs-gcp-03-12714944c726 --network=projects/qwiklabs-gcp-03-12714944c726/global/networks/mynetwork --description=Allows\ connections\ from\ any\ source\ in\ the\ network\ IP\ range\ to\ any\ instance\ on\ the\ network\ using\ all\ protocols. --direction=INGRESS --priority=65534 --source-ranges=10.128.0.0/9 --action=ALLOW --rules=all

                gcloud compute firewall-rules create mynetwork-allow-rdp --project=qwiklabs-gcp-03-12714944c726 --network=projects/qwiklabs-gcp-03-12714944c726/global/networks/mynetwork --description=Allows\ RDP\ connections\ from\ any\ source\ to\ any\ instance\ on\ the\ network\ using\ port\ 3389. --direction=INGRESS --priority=65534 --source-ranges=0.0.0.0/0 --action=ALLOW --rules=tcp:3389

                gcloud compute firewall-rules create mynetwork-allow-ssh --project=qwiklabs-gcp-03-12714944c726 --network=projects/qwiklabs-gcp-03-12714944c726/global/networks/mynetwork --description=Allows\ TCP\ connections\ from\ any\ source\ to\ any\ instance\ on\ the\ network\ using\ port\ 22. --direction=INGRESS --priority=65534 --source-ranges=0.0.0.0/0 --action=ALLOW --rules=tcp:22
  
      
   #####  iii) Creating custom mode networks
            gcloud compute networks create managementnet --project=qwiklabs-gcp-03-12714944c726 --subnet-mode=custom --bgp-routing-mode=regional

            gcloud compute networks subnets create managementsubnet-us --project=qwiklabs-gcp-03-12714944c726 --range=10.130.0.0/20 --network=managementnet --region=us-central1
            
            a. Create custom private network and subnetwork (in us and europe)
            gcloud compute networks create privatenet --subnet-mode=custom
            gcloud compute networks subnets create privatesubnet-us --network=privatenet --region=us-central1 --range=172.16.0.0/24
            gcloud compute networks subnets create privatesubnet-eu --network=privatenet --region=europe-west1 --range=172.20.0.0/20
            
### 3. Networking Services - Configuring Network Loadbalancers

3.1 Configure a health-check firewall
      
      gcloud compute firewall-rules create fw-allow-health-checks --network=default --target-tags=allow-health-checks --source-ranges="130.211.0.0/22,35.191.0.0/16" --rules="tcp:80" --direction="IN" --action=ALLOW

3.2 Create the Cloud Router instance

      gcloud compute routers create nat-router-us-central1 --network=default --region=us-central1

      gcloud compute routers nats create nat-config --region=us-central1 --router=nat-router-us-central1 --auto-allocate-nat-external-ips --nat-primary-subnet-ip-ranges
      
3.3 Binding the network configuration to a VM

      gcloud beta compute --project=qwiklabs-gcp-47fa3e3ff8a1797a instances create webservers --zone=us-central1-a --machine-type=f1-micro --subnet=default --no-address --maintenance-policy=MIGRATE --service-account=374712980416-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --tags=allow-health-checks --image=debian-9-stretch-v20200902 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=webservers --reservation-affinity=any

      ## Customize the VM
      cloud compute ssh webservers --zone=us-central1-a --dry-run && cloud compute ssh webservers --zone=us-central1-a &&
      
      ## To install Apache2, run the following commands:
      sudo apt-get update && sudo apt-get install -y apache2 && 
      
      ## To start the Apache server, run the following command:
      sudo service apache2 start
      
      ## Create custom Image
      gcloud compute images create mywebserver --project=qwiklabs-gcp-47fa3e3ff8a1797a --source-disk=webservers --source-disk-zone=us-central1-a --storage-location=us
      
      ##
3.4 Configuring an HTTP Load Balancer with Cloud Armor

i) Create the health check rule
      
      ## create firewall rule
      gcloud compute --project=qwiklabs-gcp-04-3008c93f8470 firewall-rules create fw-allow-health-checks --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:80 --source-ranges=130.211.0.0/22,35.191.0.0/16 --target-tags=allow-health-checks
      
ii) Create the Cloud Router instance
   
      ## create cloud routers for us-central1 and europe-west1 region
      gcloud compute routers create nat-router-us-central1 --region=us-central1 --network default
      gcloud compute routers create nat-router-europe-west1 --region=europe-west1 --network default
      
      ## create the nats for us-central1 and europe-west1 region
      gcloud compute routers nats create nat-usa  --router nat-router-us-central1 --auto-allocate-nat-external-ips --nat-primary-subnet-ip-ranges --region=us-central1  --quiet &
      gcloud compute routers nats create nat-europe --router nat-router-europe-west1  --auto-allocate-nat-external-ips --nat-primary-subnet-ip-ranges --region=europe-west1 --quiet
      
iii) Configure an instance template and create instance groups

      ## Configure the instance template
      gcloud compute instance-templates create us-central1-template --metadata="startup-script-url='gs://cloud-training/gcpnet/httplb/startup.sh'" --network=default --region=us-central1  --subnet default --no-address --tags=allow-health-checks
      
      gcloud compute instance-templates create europe-west1-template --metadata="startup-script-url='gs://cloud-training/gcpnet/httplb/startup.sh'" --network=default --region=europe-west1 --subnet default --no-address --tags=allow-health-checks
      
      ## use the instance templeate for instance gropus
      gcloud compute instance-groups managed create us-central1-mig --size 1 --template us-central1-template --instance-redistribution-type PROACTIVE --region us-central1 --zones=us-central1-b,us-central1-c,us-central1-f
      
      gcloud compute instance-groups managed create europe-west1-mig  --size 1 --template europe-west1-template --instance-redistribution-type PROACTIVE --region europe-west1 --zones=europe-west1-b,europe-west1-c,europe-west1-d

      ## Set additional configuration like autoscaling, minim and maximum no. of instances, cool-down-period
      gcloud compute instance-groups managed set-autoscaling us-central1-mig --cool-down-period 45 --min-num-replicas 1 --max-num-replicas 5 --scale-based-on-cpu --target-cpu-utilization 0.8 --region us-central1
      
      gcloud compute instance-groups managed set-autoscaling europe-west1-mig --cool-down-period 45 --min-num-replicas 1 --max-num-replicas 5 --scale-based-on-cpu --target-cpu-utilization 0.8 --region europe-west1
      
      ## Now that everything is set and instances are running, we can verify the backends
      ## Connect to  vm instance in europe zone
      declare -a name_zone=("$(gcloud compute instances list --filter="zone ~ europe*" --format="table(name, zone)")
       
      ## Create the security policy
      gcloud compute --project=qwiklabs-gcp-04-3008c93f8470 security-policies create deny-siege

      gcloud compute --project=qwiklabs-gcp-04-3008c93f8470 security-policies rules create 1000 --action=deny\(403\) --security-policy=deny-siege --src-ip-ranges=35.223.220.220

      gcloud compute --project=qwiklabs-gcp-04-3008c93f8470 security-policies rules create 2147483647 --action=allow --security-policy=deny-siege --description="Default rule, higher priority overrides it" --src-ip-ranges=\*

      gcloud compute --project=qwiklabs-gcp-04-3008c93f8470 backend-services update http-backend --security-policy=deny-siege
      
      
     

### 4. Cloud Computing Provisioning (Virtual Machines) with Compute Engine

  Like bare steel server hosted in-premisse, we rely on server running on Virtual Machines. The GCP Compute  Engine resource enable the creation on VM Instances as one would need to deploy web servers, application server, etc.
  
        gcloud beta compute --project=qwiklabs-gcp-03-12714944c726 instances create mynet-eu-vm --zone=europe-west1-c --machine-type=n1-standard-1 --subnet=mynetwork --private-network-ip=10.132.0.2 --network-tier=PREMIUM --maintenance-policy=MIGRATE --service-account=567764395026-compute@developer.gserviceaccount.com scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --image=debian-9-stretch-v20200902 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=mynet-eu-vm --reservation-affinity=any

        gcloud beta compute --project=qwiklabs-gcp-03-12714944c726 instances create managementnet-us-vm --zone=us-central1-c --machine-type=f1-micro --subnet=managementsubnet-us --network-tier=PREMIUM --maintenance-policy=MIGRATE --service-account=567764395026-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --image=debian-9-stretch-v20200902 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=managementnet-us-vm --reservation-affinity=any

        gcloud compute instances create privatenet-us-vm --zone=us-central1-c --machine-type=f1-micro --subnet=privatesubnet-us


4.1 Create a Windows virtual machine

      gcloud beta compute --project=qwiklabs-gcp-04-4dcede347b44 instances create instance-2 --zone=europe-west2-a --machine-type=n1-standard-1 --subnet=default --network-tier=PREMIUM --maintenance-policy=MIGRATE --service-account=549175800403-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --tags=http-server,https-server --image=windows-server-2016-dc-core-v20200813 --image-project=windows-cloud --boot-disk-size=100GB --boot-disk-type=pd-ssd --boot-disk-device-name=instance-2 --no-shielded-secure-boot --shielded-vtpm --shielded-integrity-monitoring --reservation-affinity=any

      gcloud compute --project=qwiklabs-gcp-04-4dcede347b44 firewall-rules create default-allow-http --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:80 --source-ranges=0.0.0.0/0 --target-tags=http-server

      gcloud compute --project=qwiklabs-gcp-04-4dcede347b44 firewall-rules create default-allow-https --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:443 --source-ranges=0.0.0.0/0 --target-tags=https-server
      
4.2 Create a custom virtual machine

      gcloud beta compute --project=qwiklabs-gcp-04-4dcede347b44 instances create instance-3 --zone=us-west1-b --machine-type=custom-6-32768 --subnet=default --network-tier=PREMIUM --maintenance-policy=MIGRATE --service-account=549175800403-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --image=debian-9-stretch-v20200902 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=instance-3 --reservation-affinity=any

4.3 Working with Virtual Machines
   i) Create VM with Storage Read/Write
   
        gcloud beta compute --project=qwiklabs-gcp-00-54bac0684c8d instances create mc-server --zone=us-central1-a --machine-type=e2-medium --subnet=default --address=34.72.175.28 --network-tier=PREMIUM --maintenance-policy=MIGRATE --service-account=514643164481-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/trace.append,https://www.googleapis.com/auth/devstorage.read_write --tags=minecraft-server --image=debian-9-stretch-v20200902 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=mc-server --create-disk=mode=rw,size=50,type=projects/qwiklabs-gcp-00-54bac0684c8d/zones/us-central1-a/diskTypes/pd-ssd,name=minecraft-disk,device-name=minecraft-disk --reservation-affinity=any
   
   ii) Prepare the data disk
      
      ## Connect to server via SSH and proceed with:
      gcloud compute ssh mc-server --zone=us-central1-a --quiet
      
      ## To create a directory that serves as the mount point for the data disk, run the following command:
      sudo mkdir -p /home/minecraft &&
      
      ## To format the disk, run the following command:
      sudo mkfs.ext4 -F -E lazy_itable_init=0, lazy_journal_init=0,discard dev/disk/by-id/google-minecraft-disk      && 
      
      ## To mount the disk, run the following command:
      sudo mount -o discard,defaults /dev/disk/by-id/google-minecraft-disk /home/minecraft &&
      
      
   iii) Install and run the Application
   
       ## Install the Java Runtime Environment (JRE) and the Minecraft server
       sudo apt-get update &&
       sudo apt-get install -y default-jre-headless &&
       
       ##To navigate to the directory where the persistent disk is mounted, run the following command:
       cd /home/minecraft && 
       
       ##  Install wget, run the following command:
       sudo apt-get install wget -y && 
       ## Download the current Minecraft server JAR file (1.11.2 JAR), run the following command:
       sudo wget https://launcher.mojang.com/v1/objects/d0d0fe2b1dc6ab4c65554cb734270872b72dadd6/server.jar
       &&
       
       ## To initialize the Minecraft server, run the following command:
       sudo java -Xmx1024M -Xms1024M -jar server.jar nogui &&
       
       ## Open and edit the the EULA, run the following command:
       sudo nano eula.txt 
       ## Change the last line of the file from eula=false to eula=true
       
       
   iv) Allow client traffic with Firewall rule
   
       gcloud compute --project=qwiklabs-gcp-00-54bac0684c8d firewall-rules create minecraft-rule --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:25565 --source-ranges=0.0.0.0/0 --target-tags=minecraft-server
   
   v) Schedule regular backups
   
      ## Create a Cloud Storage bucket
      gcloud compute ssh mc-server --zone=us-central1-a --quiet
      
      ## Create a globally unique bucket name, and store it in the environment variable
      export BUCKET_NAME=mcs-storage
      
      ##  create the bucket using the gsutil tool, part of the Cloud SDK, run the following command:
      gsutil mb gs://$BUCKET_NAME-minecraft-backup
      
      ## Create a backup script file
      cd /home/minecraft
      
      ##backup.sh
         #!/bin/bash
         screen -r mcs -X stuff '/save-all\n/save-off\n'
         /usr/bin/gsutil cp -R ${BASH_SOURCE%/*}/world gs://${BUCKET_NAME}-minecraft-backup/$(date "+%Y%m%d-%H%M%S")-world
         screen -r mcs -X stuff '/save-on\n'
      
      ## set as executable
      sudo chmod 755 /home/minecraft/backup.sh
      
      ## Execute 
      . /home/minecraft/backup.sh
      
      ## Open the cron table for editing:
      sudo crontab -e
      
      ## Select the file editor tool
      ## Add this command as last line, to ensure the cron tab execute a backup in every 4 hours
      0 */4 * * * /home/minecraft/backup.sh
      
   vi) Automating server maintenance
      
      ## Instead of following the manual process to mount the persistent disk and launch the server application in a screen, use metadata scripts to create a startup script and a shutdown script to do this for you.
      gcloud compute instances add-metadata mc-server --metadata="startup-script-url='https://storage.googleapis.com/cloud-training/archinfra/mcserver/startup.sh'; shutdown-script-url='https://storage.googleapis.com/cloud-training/archinfra/mcserver/shutdown.sh'"
      
      
4.4 Configure an instance template and create instance groups

   i) Configure the instance template
   
      gcloud beta compute --project=qwiklabs-gcp-47fa3e3ff8a1797a instance-templates create mywebserver-template --machine-type=f1-micro --subnet=projects/qwiklabs-gcp-47fa3e3ff8a1797a/regions/us-central1/subnetworks/default --no-address --maintenance-policy=MIGRATE --service-account=374712980416-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --region=us-central1 --tags=allow-health-checks --image=mywebserver --image-project=qwiklabs-gcp-47fa3e3ff8a1797a --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=mywebserver-template --reservation-affinity=any
      
   ii) Create the managed instance groups in us and europe from template, and set the Load balancer and healthchecks.
   
      gcloud compute --project "qwiklabs-gcp-47fa3e3ff8a1797a" health-checks create tcp "http-health-check" --timeout "1" --check-interval "60" --unhealthy-threshold "3" --healthy-threshold "2" --port "80"

      gcloud beta compute --project=qwiklabs-gcp-47fa3e3ff8a1797a instance-groups managed create us-central1-mig --base-instance-name=us-central1-mig --template=mywebserver-template --size=1 --zones=us-central1-b,us-central1-c,us-central1-f --instance-redistribution-type=PROACTIVE --health-check=http-health-check --initial-delay=300

      gcloud beta compute --project "qwiklabs-gcp-47fa3e3ff8a1797a" instance-groups managed set-autoscaling "us-central1-mig" --region "us-central1" --cool-down-period "60" --max-num-replicas "2" --min-num-replicas "1" --target-load-balancing-utilization "0.8" --mode "on"

  

### 5. Google Kubernetes Engine
5.1 Create a Namespace for Kubernetes
     
 i) create a production namespace
     export zone=us-central1-a
     export cluster=std-cluster-1
     
     ## create the cloud cluster
     gcloud container clusters create $cluster --num-nodes=3 --enable-ip-alias --zone=$zone 
     
     ## configure access to clusters for kebectl tool.
     gcloud container clusters get-credentials $my_cluster --zone $my_zone
     
 ii) Create a Resource in a Namespace 
 
      ## create a configuration file for kubernetes pods named: /my-pod.yaml
      apiVersion: v1
      kind: Pod
      metadata:
        name: nginx
        labels:
          name: nginx
      spec:
        containers:
        - name: nginx
          image: nginx
          ports:
          - containerPort: 80
          
      ## create a resource within the namespace
      kubectl apply -f ./my-pod.yaml --namespace=production
     
 iii) Configure Roles to Kubernetes access control
 
      ## create a role configuration template named: /pod-reader-role.yaml within the namespace
      kind: Role
      apiVersion: rbac.authorization.k8s.io/v1
      metadata:
        namespace: production
        name: pod-reader
      rules:
      - apiGroups: [""]
        resources: ["pods"]
        verbs: ["create", "get", "list", "watch"]
        
      ## create a custom Role based on the users email
      kubectl create clusterrolebinding cluster-admin-binding --clusterrole cluster-admin --user [USERNAME_1_EMAIL]
      
      ## execute the following command to create the role:
      kubectl apply -f pod-reader-role.yaml
      
   iV) Create a RoleBinding to bind to a user
      
         ## create a rolebinding configuration template named: username2-editor-binding.yaml 
         kind: RoleBinding
         apiVersion: rbac.authorization.k8s.io/v1
         metadata:
           name: username2-editor
           namespace: production
         subjects:
         - kind: User
           name: [USERNAME_2_EMAIL]
           apiGroup: rbac.authorization.k8s.io
         roleRef:
           kind: Role
           name: pod-reader
           apiGroup: rbac.authorization.k8s.io
           
       ## set the user for this role
       export USER2=[USERNAME_2_EMAIL]
       
       ## replace the value [..] in role configuration file with the actual user email, using command <sed>
       sed -i "s/\[USERNAME_2_EMAIL\]/${USER2}/" username2-editor-binding.yaml
       
       ## apply role 
       kubectl apply -f username2-editor-binding.yaml
       
 5.2 Create and Use Pod Security Policies
 
 i) Create a Pod Security Policy
      
      ##  create a Pod Security Policy using the restricted-psp.yaml
      apiVersion: policy/v1beta1
      kind: PodSecurityPolicy
      metadata:
        name: restricted-psp
      spec:
        privileged: false  # Don't allow privileged pods!
        seLinux:
          rule: RunAsAny
        supplementalGroups:
          rule: RunAsAny
        runAsUser:
          rule: MustRunAsNonRoot
        fsGroup:
          rule: RunAsAny
        volumes:
        - '*'
        
     ## create the Pod Security Policy by executing the following command: 
     kubectl apply -f restricted-psp.yaml
     
  ii) Create a ClusterRole to a Pod Security Policy
      
      ## creates a ClusterRole that includes the resource from <psp-cluster-role.yaml> based on the policy created above
      kind: ClusterRole
      apiVersion: rbac.authorization.k8s.io/v1
      metadata:
        name: restricted-pods-role
      rules:
      - apiGroups:
        - extensions
        resources:
        - podsecuritypolicies
        resourceNames:
        - restricted-psp
        verbs:
        - use
   
      ## create the role 
      kubectl create clusterrolebinding cluster-admin-binding --clusterrole cluster-admin --user [USERNAME_1_EMAIL]
      
      ## create the ClusterRole with access to the security policy, execute the following command:
      kubectl apply -f psp-cluster-role.yaml
      
  iii) Create a ClusterRoleBinding for the Pod Security Policy
  
      ## the file psp-cluster-role-binding.yaml binds the <restricted-pods-role> to the <system:serviceaccounts> group in the default Namespace.
      apiVersion: rbac.authorization.k8s.io/v1
      kind: RoleBinding
      metadata:
        name: restricted-pod-rolebinding
        namespace: default
      roleRef:
        apiGroup: rbac.authorization.k8s.io
        kind: ClusterRole
        name: restricted-pods-role
      subjects:
      # Example: All service accounts in default namespace
      - apiGroup: rbac.authorization.k8s.io
        kind: Group
        name: system:serviceaccounts
        
      ##  bind the restricted-pods-role ClusterRole to the serviceaccounts group in the default namespace, execute the following command:
     kubectl apply -f psp-cluster-role-binding.yaml
     
iv) Activate Security Policy

      ## execute the following command to enable the PodSecurityPolicy controller:
      gcloud beta container clusters update $my_cluster --zone $my_zone --enable-pod-security-policy
      
      ## The PodSecurityPolicy controller, can be disabled by running this command:
         # gcloud beta container clusters update [CLUSTER_NAME] --no-enable-pod-security-policy
                  
 v) Testing the Pod Security Policy
      
      ## create a sample Pod manifest called privileged-pod.yaml
      kind: Pod
      apiVersion: v1
      metadata:
        name: privileged-pod
      spec:
        containers:
          - name: privileged-pod
            image: nginx
            securityContext:
              privileged: true
              
     ## To attempt to run the privileged Pod, execute the following command:
     kubectl apply -f privileged-pod.yaml
     
### 6. Cloud BigQuery and Machine Learning – Data Management and Data Analytics

6.1) Load data from Cloud Storage into BigQuery

      ## start bigquery shell
      export my_project_id="$(gcloud config get-value project)"
      bq --project_id=$my_project_id --location=US shell &
      
      ## create dataset located in US using <mk> command
      mk -d --description "This is my log data set." logdata
      mk -d --description "This is my baby names data set." babynames
      #mk --dataset_id=logdata --data_location=US
      
      ## create table <accesslog> into the dataset with schema definition
      mk --table --expiration=3600  $my_project_id:logdata.accesslog
      mk --table --expiration=3600  $my_project_id:babynames.names_2014
      
      ## load data from into table from Google Cloud Storage &&
      load --autodetect --source_format=CSV logdata.accesslog gs://cloud-training/gcpfci/access_log.csv
      bq --project_id=$idp --location=US load --autodetect --format=csv babynames.names_2014 ./yob2014.csv
      
      ## exit bigquery commandline
      
 6.2 Recommending Products Using Cloud SQL and Spark
      
 i) Create a Cloud SQL instance
    
      gcloud sql instances create rentals --database-version=MYSQL_5_7 --region us-central1
 
 ii) Create tables
      
      ## connect to server
      gcloud sql connect rentals --user=root --quiet
      
      ## create the tables 
      
      ## Create this database
      CREATE DATABASE IF NOT EXISTS recommendation_spark;

      USE recommendation_spark;

      DROP TABLE IF EXISTS Recommendation;
      DROP TABLE IF EXISTS Rating;
      DROP TABLE IF EXISTS Accommodation;

      CREATE TABLE IF NOT EXISTS Accommodation
      (
        id varchar(255),
        title varchar(255),
        location varchar(255),
        price int,
        rooms int,
        rating float,
        type varchar(255),
        PRIMARY KEY (ID)
      );

      CREATE TABLE  IF NOT EXISTS Rating
      (
        userId varchar(255),
        accoId varchar(255),
        rating int,
        PRIMARY KEY(accoId, userId),
        FOREIGN KEY (accoId)
          REFERENCES Accommodation(id)
      );

      CREATE TABLE  IF NOT EXISTS Recommendation
      (
        userId varchar(255),
        accoId varchar(255),
        prediction float,
        PRIMARY KEY(userId, accoId),
        FOREIGN KEY (accoId)
          REFERENCES Accommodation(id)
      );
 
 iii) Load data from Cloud Storage into Cloud SQL tables
 
      ## Create storage backet and copy data into backet
      gsutil mb gs://$DEVSHELL_PROJECT_ID
      
      gsutil cp gs://cloud-training/bdml/v2.0/data/accommodation.csv gs://$DEVSHELL_PROJECT_ID
      gsutil cp gs://cloud-training/bdml/v2.0/data/rating.csv gs://$DEVSHELL_PROJECT_ID
      
      ## Load data into tables from storage backets
      gcloud sql import csv rents gs://$DEVSHELL_PROJECT_ID/accommodation.csv  --database=recommendation_spark --table=Accommodation
      gcloud sql import csv rentsx gs://$DEVSHELL_PROJECT_ID/reting.csv  --database=recommendation_spark --table=Rating
 
 i)   Launch Dataproc
 
      ## gcloud dataproc clusters create rentals --region us-central1 --subnet default --zone us-central1-a --master-machine-type n1-standard-4 --master-boot-disk-size 500 --num-workers 2 --worker-machine-type n1-standard-4 --worker-boot-disk-size 500 --image-version 1.3-debian10 --project qwiklabs-gcp-04-527c9e0f6c10
      
      ## Create a file dataproc_launcher.sh and copy this code:
      echo "Authorizing Cloud Dataproc to connect with Cloud SQL"
      CLUSTER=rentals
      CLOUDSQL=rentals
      ZONE=us-central1-a
      NWORKERS=2

      machines="$CLUSTER-m"
      for w in `seq 0 $(($NWORKERS - 1))`; do
         machines="$machines $CLUSTER-w-$w"
      done

      echo "Machines to authorize: $machines in $ZONE ... finding their IP addresses"
      ips=""
      for machine in $machines; do
          IP_ADDRESS=$(gcloud compute instances describe $machine --zone=$ZONE --format='value(networkInterfaces.accessConfigs[].natIP)' | sed "s/\['//g" | sed "s/'\]//g" )/32
          echo "IP address of $machine is $IP_ADDRESS"
          if [ -z  $ips ]; then
             ips=$IP_ADDRESS
          else
             ips="$ips,$IP_ADDRESS"
          fi
      done

      echo "Authorizing [$ips] to access cloudsql=$CLOUDSQL"
      gcloud sql instances patch $CLOUDSQL --authorized-networks $ips --quiet
 
 v) Run the ML model
 
      # Download and edit this file, changing the IP, Password to SQL instance IP and password
      gsutil cp gs://cloud-training/bdml/v2.0/model/train_and_apply.py train_and_apply.py
      # cloudshell edit train_and_apply.py
      
      # copy into storage
      gsutil cp train_and_apply.py gs://$DEVSHELL_PROJECT_ID
      
      # Create a Job to run our Model, with REST EQUIVALENT TO:
      POST /v1/projects/[project_id]/regions/us-central1/jobs:submit/
      {
        "projectId": "qwiklabs-gcp-04-527c9e0f6c10",
        "job": {
          "placement": {
            "clusterName": "rentals"
          },
          "reference": {
            "jobId": "job-b093a52f"
          },
          "pysparkJob": {
            "mainPythonFileUri": "gs://qwiklabs-gcp-04-527c9e0f6c10/train_and_apply.py"
          }
        }
      }


### 7. Cloud Functions and Cloud Pub/Sub – Running Serverless Functions (example)

7.1 Clone source code in Cloud Shell

      ## clone the repository for the class, enter the following command:
      git clone https://github.com/GoogleCloudPlatform/training-data-analyst
      
      ## Create a soft link as a shortcut to your working directory:
      ln -s ~/training-data-analyst/courses/developingapps/v1.2/nodejs/cloudfunctions ~/cloudfunctions
      
7.2 Configure and run the case study application

      ##  change the working directory, enter the following command:
      cd ~/cloudfunctions/start
      
      configure the Quiz application, enter the following command:
      . prepare_environment.sh
      
      ## run the web application, enter the following command:
      npm start
      
 7.3 Working with Cloud Functions
 
      ## 
      
### 8. Exploring Cloud Monitoring to Manage Resources

8.1 Create a Cloud Monitoring workspace

   i) Create a Deployment Manager deployment
            
      ## set an environm variable for zone
      export MY_ZONE=us-central1-a
      
      ## Create Deployment Manager template < mydeploy.yaml>:
      ## gsutil cp gs://cloud-training/gcpfcoreinfra/mydeploy.yaml mydeploy.yaml
      resources:
      - name: my-vm
        type: compute.v1.instance
        properties:
          zone: us-central1-a
          machineType: zones/us-central1-a/machineTypes/n1-standard-1
          metadata:
            items:
            - key: startup-script
              value: "apt-get update"
          disks:
          - deviceName: boot
            type: PERSISTENT
            boot: true
            autoDelete: true
            initializeParams:
              sourceImage: https://www.googleapis.com/compute/v1/projects/debian-cloud/global/images/debian-9-stretch-v20180806
          networkInterfaces:
          - network: https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-00-4aa3603ca888/global/networks/default
            accessConfigs:
            - name: External NAT
              type: ONE_TO_ONE_NAT
      
      ## use the sed command to replace the PROJECT_ID placeholder string with your Google Cloud Platform project ID using this command
      sed -i -e "s/PROJECT_ID/$DEVSHELL_PROJECT_ID/" mydeploy.yaml
      
      ## use the sed command to replace the ZONE placeholder string with your Google Cloud Platform zone using this command:
      sed -i -e "s/ZONE/$MY_ZONE/" mydeploy.yaml
   
      ## Build a deployment from the template:
      gcloud deployment-manager deployments create my-first-depl --config mydeploy.yaml
      
   ii) Update a Deployment Manager deployment
   
      ##  Launch the nano text editor to edit the mydeploy.yaml file
      nano mydeploy.yaml
      
      ## sets the value of the startup script, value: "apt-get ... "
      value: "apt-get update; apt-get install nginx-light -y"
      
      ## Enter this command to cause Deployment Manager to update your deployment to install the new startup script:
      gcloud deployment-manager deployments update my-first-depl --config mydeploy.yaml
      
 8.2  View the Load on a VM using Cloud Monitoring
 
 i) Create a Monitoring workspace
 
    ## Connect to VM with SSh 
    gcloud compute ssh my-vm --zone us-central1-a --quiet &
    
    ## execute this command to create a CPU load: This Linux pipeline forces the CPU to work on compressing a continuous stream of random data.
    dd if=/dev/urandom | gzip -9 >> /dev/null &
    
    
    ## install Agents libraries
    curl -sSO https://dl.google.com/cloudagents/add-monitoring-agent-repo.sh
    sudo bash add-monitoring-agent-repo.sh &
    sudo apt-get update &
    
    ## Install Agent
    sudo apt-cache madison stackdriver-agent -y &
    
    ## or
    sudo apt-get install -y 'stackdriver-agent=6.*'
    
    ## Start Agent Service
    sudo service stackdriver-agent start &&
    
    ## In the Metric pane of Metrics Explorer, select the resource type GCE VM instance and the metric CPU usage.
    
 
