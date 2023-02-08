content-origin:: external
content-status:: wip
content-authors:: [[Elton Stoneman]]
lang:: en
type:: content

- Read it online
	- [O'Reilly Book](https://www.oreilly.com/library/view/learn-kubernetes-in/9781617297984/)
- Topics
	- [[Kubernetes]]
	- [[DevOps]]
	- [[CI/CD]]
- Links
	- [Source code](https://github.com/sixeyed/kiamol)
- Summary
	- --- Week 1: Fast Track to Kubernetes
	- ((63e114c1-5ad8-40f9-ac68-65064c3c6161))
	- ((63e114c1-827d-4ac4-a13b-8cc15702a7c4))
	- ((63e114c1-0cda-4c3f-b89a-b1daaf411368))
	- Chapter 4 - Configuring applications with ConfigMaps and Secrets
	- Chapter 5 - Storing data with Volumes, Mounts, and Claims
	- Chapter 6 - Scaling applications across multiple pods with controllers
	- --- Week 2: Kubernetes in the real world
	- Chapter 7 - Extending applications with multi-container Pods
	- Chapter 8 - Running data-heavy apps with StatefulSets and Jobs
	- Chapter 9 - Managing app releases with Rollouts and Rollbacks
	- Chapter 10 - Packaging and managing apps with Helm
	- Chapter 11 - App development-developer workflows and [[CI/CD]]
	- --- Week 3: Preparing for production
	- Chapter 12 - Empowering self-healing apps
	- Chapter 13 - Centralizing logs with [[Fluentd]] and [[Elasticsearch]]
	- Chapter 14 - Monitoring applications and Kubernetes with [[Prometheus]]
	- Chapter 15 - Managing incoming traffic with [[Ingress]]
	- Chapter 16 - Securing applications with Policies, Contexts, and Admission control
	- --- Week 4. Pure and Applied Kubernetes
	- Chapter 17 - Securing resources with [[Role-Based Access Control (RBAC)]]
	- Chapter 18 - Deploying Kubernetes: Multinode and Multiarchitecture Clusters
	- Chapter 19 - Controlling workload placement and automatic scaling
	- Chapter 20 - Extending Kubernetes with custom resources and operators
	- Chapter 21 - Running serverless functions in Kubernetes
	- Chapter 22 - Never the end
	- Appendix A - Packaging applications from source code into docker images
	- Appendix B - Adding observability with containerized monitoring
	- Appendix C - Application configuration management in containers
	- Appendix D - Writing and managing application logs with docker
- Chapter 1 - Before you begin
  id:: 63e114c1-5ad8-40f9-ac68-65064c3c6161
	- Installing Kubernetes
	- Verifying Kubernetes by getting the nodes
- Chapter 2 - Running containers in Kubernetes with Pods and Deployments
  id:: 63e114c1-827d-4ac4-a13b-8cc15702a7c4
	- Code snippets
		- ```bash
		  # run a Pod with a single container; the restart flag tells Kubernetes
		  # to create just the Pod and no other resources:
		  kubectl run hello-kiamol --image=kiamol/ch02-hello-kiamol --restart=Never
		   
		  # wait for the Pod to be ready:
		  kubectl wait --for=condition=Ready pod hello-kiamol
		   
		  # list all the Pods in the cluster:
		  kubectl get pods
		   
		  # show detailed information about the Pod:
		  kubectl describe pod hello-kiamol
		  
		  # ----
		  
		  # get the basic information about the Pod:
		  kubectl get pod hello-kiamol
		   
		  # specify custom columns in the output, selecting network details:
		  kubectl get pod hello-kiamol --output custom-columns=NAME:metadata.name,NODE_IP:status.hostIP,POD_IP:status.podIP 
		   
		  # specify a JSONPath query in the output,
		  # selecting the ID of the first container in the Pod:
		  kubectl get pod hello-kiamol -o jsonpath='{.status.containerStatuses[0].containerID}'
		  
		  # ---
		  
		  # find the Pod’s container:
		  docker container ls -q --filter label=io.kubernetes.container.name=hello-kiamol
		   
		  # now delete that container:
		  docker container rm -f $(docker container ls -q --filter label=io.kubernetes.container.name=hello-kiamol)
		   
		  # check the Pod status:
		  kubectl get pod hello-kiamol
		   
		  # and find the container again:
		  docker container ls -q --filter label=io.kubernetes.container.name=hello-kiamol
		  
		  # ---
		  
		  # listen on port 8080 on your machine and send traffic
		  # to the Pod on port 80:
		  kubectl port-forward pod/hello-kiamol 8080:80
		   
		  # now browse to http://localhost:8080
		   
		  # when you’re done press ctrl-c to end the port forward
		  
		  # ---
		  
		  # create a Deployment called "hello-kiamol-2", running the same web app:
		  kubectl create deployment hello-kiamol-2 --image=kiamol/ch02-hello-kiamol
		   
		  # list all the Pods:
		  kubectl get pods
		  
		  # ---
		  
		  # print the labels that the Deployment adds to the Pod:
		  kubectl get deploy hello-kiamol-2 -o jsonpath='{.spec.template.metadata.labels}'
		   
		  # list the Pods that have that matching label:
		  kubectl get pods -l app=hello-kiamol-2
		  
		  # list all Pods with a label called "app," showing the Pod name and
		  # labels:
		  kubectl get pods -l app -o custom-columns=NAME:metadata.name,LABELS:metadata.labels
		   
		  # update the "app" label for the the unmanaged Pod:
		  kubectl label pods -l app=hello-kiamol-x --overwrite app=hello-kiamol-2
		   
		  # fetch the Pods again:
		  kubectl get pods -l app -o custom-columns=NAME:metadata.name,LABELS:metadata.labels
		  
		  # ---
		  
		  # switch from the root of the kiamol repository to the chapter 2 folder:
		  cd ch02
		  
		  # deploy the application from the manifest file:
		  kubectl apply -f pod.yaml
		   
		  # list running Pods:
		  kubectl get pods
		  
		  # ---
		  
		  # run the app using the Deployment manifest:
		  kubectl apply -f deployment.yaml
		   
		  # find Pods managed by the new Deployment:
		  kubectl get pods -l app=hello-kiamol-4
		  
		  # ---
		  
		  # check the internal IP address of the first Pod we ran: 
		  kubectl get pod hello-kiamol -o custom-columns=NAME:metadata.name,POD_IP:status.podIP
		   
		  # run an interactive shell command in the Pod:
		  kubectl exec -it hello-kiamol -- sh
		   
		  # inside the Pod, check the IP address:
		  hostname -i
		   
		  # and test the web app:
		  wget -O - http://localhost | head -n 4
		   
		  # leave the shell:
		  exit
		  
		  # ---
		  
		  # print the latest container logs from Kubernetes:
		  kubectl logs --tail=2 hello-kiamol
		   
		  # and compare the actual container logs--if you’re using Docker:
		  docker container logs --tail=2 $(docker container ls -q --filter label=io.kubernetes.container.name=hello-kiamol)
		  
		  # ---
		  
		  # make a call to the web app inside the container for the 
		  # Pod we created from the Deployment YAML file: 
		  kubectl exec deploy/hello-kiamol-4 -- sh -c 'wget -O - http://localhost > /dev/null'
		   
		  # and check that Pod’s logs:
		  kubectl logs --tail=1 -l app=hello-kiamol-4
		  
		  # ---
		  
		  # create the local directory:
		  mkdir -p /tmp/kiamol/ch02
		   
		  # copy the web page from the Pod:
		  kubectl cp hello-kiamol:/usr/share/nginx/html/index.html /tmp/kiamol/ch02/index.html
		   
		  # check the local file contents:
		  cat /tmp/kiamol/ch02/index.html
		  
		  # ---
		  
		  # list all running Pods:
		  kubectl get pods
		   
		  # delete all Pods:
		  kubectl delete pods --all
		   
		  # check again:
		  kubectl get pods
		  
		  # ---
		  
		  # view Deployments:
		  kubectl get deploy
		   
		  # delete all Deployments:
		  kubectl delete deploy --all
		   
		  # view Pods:
		  kubectl get pods
		   
		  # check all resources:
		  kubectl get all
		  ```
- Chapter 3 - Connecting Pods Over The Network with Services
  id:: 63e114c1-0cda-4c3f-b89a-b1daaf411368
	- Code Snippets
		- ```bash
		  # start up your lab environment--run Docker Desktop if it's not running--
		  # and switch to this chapter’s directory in your copy of the source code:
		  cd ch03
		   
		  # create two Deployments, which each run one Pod:
		  kubectl apply -f sleep/sleep1.yaml -f sleep/sleep2.yaml
		   
		  # wait for the Pod to be ready:
		  kubectl wait --for=condition=Ready pod -l app=sleep-2
		   
		  # check the IP address of the second Pod:
		  kubectl get pod -l app=sleep-2 --output jsonpath='{.items[0].status.podIP}'
		   
		  # use that address to ping the second Pod from the first:
		  kubectl exec deploy/sleep-1 -- ping -c 2 $(kubectl get pod -l app=sleep-2 --output jsonpath='{.items[0].status.podIP}')
		  
		  # ---
		  
		  # check the current Pod’s IP address:
		  kubectl get pod -l app=sleep-2 --output jsonpath='{.items[0].status.podIP}'
		   
		  # delete the Pod so the Deployment replaces it:
		  kubectl delete pods -l app=sleep-2
		   
		  # check the IP address of the replacement Pod:
		  kubectl get pod -l app=sleep-2 --output jsonpath='{.items[0].status.podIP}'
		  
		  # ---
		  
		  # deploy the Service defined in listing 3.1:
		  kubectl apply -f sleep/sleep2-service.yaml
		   
		  # show the basic details of the Service:
		  kubectl get svc sleep-2
		   
		  # run a ping command to check connectivity--this will fail:
		  kubectl exec deploy/sleep-1 -- ping -c 1 sleep-2
		  
		  # ---
		  
		  # run the website and API as separate Deployments: 
		  kubectl apply -f numbers/api.yaml -f numbers/web.yaml
		   
		  # wait for the Pod to be ready:
		  kubectl wait --for=condition=Ready pod -l app=numbers-web
		   
		  # forward a port to the web app:
		  kubectl port-forward deploy/numbers-web 8080:80
		   
		  # browse to the site at http://localhost:8080 and click the Go button
		  # --you'll see an error message
		   
		  # exit the port forward:
		  ctrl-c
		  
		  # ---
		  
		  # deploy the Service from listing 3.2:
		  kubectl apply -f numbers/api-service.yaml
		   
		  # check the Service details:
		  kubectl get svc numbers-api
		  
		  # forward a port to the web app:
		  kubectl port-forward deploy/numbers-web 8080:80
		   
		  # browse to the site at http://localhost:8080 and click the Go button
		   
		  # exit the port forward:
		  ctrl-c
		  
		  # ---
		  
		  # deploy the LoadBalancer Service for the website--if your firewall checks 
		  # that you want to allow traffic, then it is OK to say yes:
		  kubectl apply -f numbers/web-service.yaml
		   
		  # check the details of the Service:
		  kubectl get svc numbers-web
		   
		  # use formatting to get the app URL from the EXTERNAL-IP field:
		  kubectl get svc numbers-web -o jsonpath='http://{.status.loadBalancer.ingress[0].*}:8080'
		  
		  # ---
		  
		  # delete the current API Service:
		  kubectl delete svc numbers-api
		   
		  # deploy a new ExternalName Service:
		  kubectl apply -f numbers-services/api-service-externalName.yaml
		   
		  # check the Service configuration:
		  kubectl get svc numbers-api
		   
		  # refresh the website in your browser and test with the Go button
		  
		  # run the DNS lookup tool to resolve the Service name:
		  kubectl exec deploy/sleep-1 -- sh -c 'nslookup numbers-api | tail -n 5'
		  
		  # ---
		  
		  # remove the existing Service:
		  kubectl delete svc numbers-api
		   
		  # deploy the headless Service:
		  kubectl apply -f numbers-services/api-service-headless.yaml
		   
		  # check the Service:
		  kubectl get svc numbers-api
		   
		  # check the endpoint: 
		  kubectl get endpoints numbers-api
		   
		  # verify the DNS lookup:
		  kubectl exec deploy/sleep-1 -- sh -c 'nslookup numbers-api | grep "^[^*]"'
		   
		  # browse to the app--it will fail when you try to get a number
		  
		  # ---
		  
		  # show the endpoints for the sleep-2 Service:
		  kubectl get endpoints sleep-2
		   
		  # delete the Pod:
		  kubectl delete pods -l app=sleep-2
		   
		  # check the endpoint is updated with the IP of the replacement Pod:
		  kubectl get endpoints sleep-2
		   
		  # delete the whole Deployment:
		  kubectl delete deploy sleep-2
		   
		  # check the endpoint still exists, with no IP addresses:
		  kubectl get endpoints sleep-2
		  
		  # ---
		  
		  # check the Services in the default namespace:
		  kubectl get svc --namespace default
		  
		  # check Services in the system namespace:
		  kubectl get svc -n kube-system
		  
		  # try a DNS lookup to a fully qualified Service name:
		  kubectl exec deploy/sleep-1 -- sh -c 'nslookup numbers-api.default.svc.cluster.local | grep "^[^*]"'
		  
		  # and for a Service in the system namespace:
		  kubectl exec deploy/sleep-1 -- sh -c 'nslookup kube-dns.kube-system.svc.cluster.local | grep "^[^*]"'
		  
		  # ---
		  
		  # delete Deployments:
		  kubectl delete deploy --all 
		   
		  # and Services:
		  kubectl delete svc --all 
		   
		  # check what’s running:
		  kubectl get all
		  ```
	-
- Chapter 4 - Configuring applications with ConfigMaps and Secrets
- Chapter 5 - Storing data with Volumes, Mounts, and Claims
- Chapter 6 - Scaling applications across multiple pods with controllers
- Chapter 7 - Extending applications with multi-container Pods
- Chapter 8 - Running data-heavy apps with StatefulSets and Jobs
- Chapter 9 - Managing app releases with Rollouts and Rollbacks
- Chapter 10 - Packaging and managing apps with Helm
- Chapter 11 - App development-developer workflows and [[CI/CD]]
- Chapter 12 - Empowering self-healing apps
- Chapter 13 - Centralizing logs with [[Fluentd]] and [[Elasticsearch]]
- Chapter 14 - Monitoring applications and Kubernetes with [[Prometheus]]
- Chapter 15 - Managing incoming traffic with [[Ingress]]
- Chapter 16 - Securing applications with Policies, Contexts, and Admission control
- Chapter 17 - Securing resources with [[Role-Based Access Control (RBAC)]]
- Chapter 18 - Deploying Kubernetes: Multinode and Multiarchitecture Clusters
- Chapter 19 - Controlling workload placement and automatic scaling
- Chapter 20 - Extending Kubernetes with custom resources and operators
- Chapter 21 - Running serverless functions in Kubernetes
- Chapter 22 - Never the end
- Appendix A - Packaging applications from source code into docker images
- Appendix B - Adding observability with containerized monitoring
- Appendix C - Application configuration management in containers
- Appendix D - Writing and managing application logs with docker