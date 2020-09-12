# LAB: Google Cloud Fundamentals: Getting Started with Compute Engine

## Objectives:

In this lab, you will learn how to perform the following tasks:

    - Create a Compute Engine virtual machine using the Google Cloud Platform (GCP) Console.
    - Create a Compute Engine virtual machine using the gcloud command-line interface.
    - Connect between the two instances.

## Steps:

1. Create a Compute Engine virtual machine using the Google Cloud Platform (GCP) Console.

    - Create a my-vm-1 instance.
        ```
            gcloud compute instances create "my-vm-1" --machine-type "n1-standard-1" --image-project "debian-cloud" --image "debian-9-stretch-v20190213" --subnet "default" --tags "http-server" --zone "us-central1-a" 
        ```

    - Create the firewall rule to allow http traffic of all instances with target tag http-server.
        ```
    
            gcloud compute firewall-rules create allow-http --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:80 --source-ranges=0.0.0.0/0 --target-tags=http-server
        
        ```

2. Create a Compute Engine virtual machine using the gcloud command-line interface.


    - Create a my-vm-2 instance.

        ```
            gcloud compute instances create "my-vm-2" --machine-type "n1-standard-1" --image-project "debian-cloud" --image "debian-9-stretch-v20190213" --subnet "default" --zone "us-central1-b"

        ```

3. Connect between the two instances.

    1. Use the ping command to confirm that my-vm-2 can reach my-vm-1 over the network:
       
        - Connect to my-vm-2
            ```

                gcloud compute ssh my-vm-2
                
            ```
        
        - Ping my-vm-1 from my-vm-2. Confirm my-vm-2 can reach my-vm-1
            ```

                ping -c 4 my-vm-1

            ```

        - Inside my-vm-2 ssh to my-vm-1 and install nginx-light

            ```
                ssh my-vm-1

                sudo apt-get install nginx-light -y

            ```

        - Still on the my-vm-1, use the nano text editor to add a custom message to the home page of the web server:

            ```

                sudo nano /var/www/html/index.nginx-debian.html
            
            ```

        - Move the cursor to the line just below the h1 header. Add text like this, and replace YOUR_NAME with your name

            ```
                Hi from YOUR_NAME
            ```

        - Press Ctrl+O and then press Enter to save your edited file, and then press Ctrl+X to exit the nano text editor.

        - Confirm that the web server in my-vm-1 is serving your new page. 
            ```
                curl http://localhost/
            
            ```
                
                - Result:

                    Response will be the HTML source of the web server's home page, including your line of custom text.

        - Exit my-vm-1 to my-vm-2

            ```
                exit

            ```

        - Confirm that my-vm-2 can reach the web server on my-vm-1.

            ```

                curl http://my-vm-1/

            ```

                - Result:

                    Response will be the HTML source of the web server's home page, including your line of custom text.

# LAB: GCP Fundamentals: Getting Started with Kubernetes Engine

# Objectives:

In this lab, you learn how to perform the following tasks:

    - Provision a Kubernetes cluster using Kubernetes Engine.

    - Deploy and manage Docker containers using kubectl.

# Steps:

1. Confirm that needed APIs are enabled. 


    - On the GCP console note the GCP project name.

    - In the GCP Console, on the Navigation menu, click APIs & Services.

    - Scroll down in the list of enabled APIs, and confirm that both of these APIs are enabled:

            - Kubernetes Engine API

            - Container Registry API

    - If either API is missing, click Enable APIs and Services at the top. Search for the above APIs by name and enable each for your current project.

2. Start a Kubernetes Engine cluster

    - In GCP console, on the top right toolbar, click the Open Cloud Shell button.

    - Click continue

    - On the opened Cloud Shell prompt, place the zone that Qwiklabs assigned you to into an environment variable called MY_ZONE. Replace the us-central1-a with the zone given.

        ```
            export MY_ZONE=us-central1-a
        ```

    - Start a Kubernetes cluster managed by Kubernetes Engine. Name the cluster webfrontend and configure it to run 2 nodes:

        gcloud container clusters create webfrontend --zone $MY_ZONE --num-nodes 2

    - After the cluster is created, check your installed version of Kubernetes using the kubectl version command.

        ```
            kubectl version

        ```
    
        N/B The gcloud container clusters create command automatically authenticated kubectl for you.

    - View your running nodes in the GCP Console. On the Navigation menu, click Compute Engine > VM Instances

3. Run and deploy a container

    - From your Cloud Shell prompt, launch a single instance of the nginx container

        ```
            kubectl create deploy nginx --image=nginx:1.17.10
        ```
    
    - View the pod running the nginx container:

        ```

            kubectl get pods
        ```
    
    - Expose the nginx container to the Internet:

        ```
            kubectl expose deployment nginx --port 80 --type LoadBalancer
        ```

    - View the new service:

        ```
            kubectl get services
        ```

        N/B It may take a few seconds before the External-IP field is populated for your service.

    - Open a new web browser tab and paste your cluster's external IP address into the address bar. Nginx default home page will be displayed on the browser.

    - Scale up the number of pods running on your service:

        ```
             kubectl scale deployment nginx --replicas 3
        ```

    - Confirm that Kubernetes has updated the number of pods:

        ```
             kubectl get pods
        ```

    - Confirm that your external IP address has not changed:

        ```
            kubectl get services
        ```

