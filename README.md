sudo rm -f ./minikube-darwin-arm64
brew install minikube
Mac m3 didnot support other vm only qemu is supported so first delete all the minikube installed packagesbrew uninstall minikube Run the following command to clean up the Homebrew cache:
brew cleanup
sudo rm -f ./minikube-darwin-arm64
sudo rm -rf /etc/minikube rm -rf ~/.minikube
brew install minikube 
minikube start --driver qemu --network socket_vmnet
minikube status
kubectl get nodeskubectl create deployment hello-minikube --image=kicbase/echo-server:1.0
kubectl expose deployment hello-minikube --type=NodePort --port=8080
minikube service hello-minikube --url

Since the only vm supported was docker desktop for that we need to install
home brew
brew install docker docker-compose
install docker desktop
brew install minikube
minikube start --driver=docker --alsologtostderr



kubectl run hello-minikube
kubectl cluster-info
kubectl get nodes

kubectl run nginx --image=nginx
kubectl run podname --image=dockerhubimagename
kubectl describe pod nginx
kubectl get pods -o wide extra information about pod status (Within cluster each pod will have its own ip address)

kubectl run redis --image=redis --dry-run=client -o yaml > redis.yaml
to create a pod using the command
pod-definition.yaml

apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
    - name: nginx
      image: nginx

kubectl create -f redis.yaml
kubectl edit command to edit the pod details

apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend
  labels:
    app: mywebsite
    tier: frontend
spec:
  replicas: 4
  template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
    spec:
      containers:
        - name: nginx
          image: nginx
  selector:
   matchLabels:
    app: myapp
     

kubectl create -f rc-definition.yaml
kubectl get replicationcontroller
kuectl get pods


replica set
kubectl create -f replicaset-defnition.yaml
kubectl get replicaset
kubectl delete replicaset myapp-replicaset
kubectl get pods
kubectl edit replicaset myapp-replicaset

How do you increase or scale the replicas from 3 to 6
kubectl replace -f replicaset-definition.yaml
kubectl scale --replicas=6 -f replicaset-defnintion.yaml
kubectl scale --replicas=6 replicaset myapp-replicaset  (here replicaset is the type and myap-replicaset is the name)
https://kubernetes.io/docs/concepts/
 kubectl scale --replicas=2 replicaset new-replica-set

 kubectl explain replicaset command to show in detail error about replicaset definitionfile

 kubectl get rs (replicasets)
 In order to generate the new replica set we need to delete all the existing pods in old replica set and create them automatically new replica set


kubectl create -f deployment-defnition.yaml
kubectl get deployments
kubectl get replicaset
kubectl get pods

To see all the objects crete use after create deployment

kubectl get all

kubectl get deploy

Deployment Updates and rollouts

When you first create a deployment, it triggers a rollout, a new rollout creates a new deployment
revision.
Let's call it revision one.
In the future when the application is upgraded, meaning when the container version is updated to a
new one, a new rollout is triggered and a new deployment revision is created named Revision two.
This helps us keep track of the changes made to our deployment and enables us to roll back to a previous
version of deployment if necessary.
You can see the status of your rollout by running the command cube control rollout status followed by
the name of the deployment.

kubectl rollout status deployment/my-deployment

To see the revisions and history of rollout run, the cube control rollout history command followed
by the deployment name.
kubectl rollout history deployment/my-deployment

There are two types of deployment strategies.
Say, for example, you have five replicas of your web application instance deployed.
One way to upgrade these to a newer version is to destroy all of these and then create newer versions
of application instances, meaning first destroy the five running instances, and then deploy five new instances of the new application version.
The problem with this, as you can imagine, is that during the period after the older versions are
down and before any newer version is up, the application is down and inaccessible to users.
This strategy is known as the recreate strategy and thankfully this is not the default deployment strategy.

The second strategy is where we do not destroy all of them at once.
Instead, we take down the older version and bring up a newer version one by one.
This way the application never goes down and the upgrade is seamless.
Remember, if you do not specify a strategy while creating the deployment, it will assume it to be
rolling.

In other words, rolling update is the default deployment strategy.

When I say update, it could be different things, such as updating your application version by updating
the version of Docker containers used, updating their labels or updating the number of replicas, etcetera.
Since we already have a deployment definition file, it is easy for us to modify this file once we make
the necessary changes.
We run the Kube control apply command to apply the changes.

kubectl apply -f deployment-definituion.yaml

A new rollout is triggered and a new revision of the deployment is created.
But there is another way to do the same thing.

You could use the Kube control set image command to update the image of your application.
kubectl set image deployment/my-deploymment ngx-container=nginx:1.0.9
But remember, doing it this way will result in the deployment definition file having a different configuration.

to undo a change or rollback to previous revision we use
kubectl rollout undo deployment/my-deployment

records the kuberntes to record the reason/cause for the change
kubectl create -f deployment-definituion.yaml --record
kubectl edit deployment myap-deployment --record

command to change the image verions inside the deploument
kubectl set image deployment myapp-deployment nginx:1:.18 --record


apiVersion: apps/v1
kind: Deployment
metadata:
 name: frontend
 labels: 
  app: myapp
  type: frontend
spec:
 selector:
  matchLabels:
   app: myapp
 template:
  metadata:
   name: frontend-pod
   labels:
     app: myapp
     type: frontend
  spec:
   containers:
    - name: webapp
      image: kodekloud/webapp-color:v2
 strategy:
  type:  Recreate      // or default RollingUpdate


  Upgrade the application by setting the image on the deployment to kodekloud/webapp-color:v3

Do not delete and re-create the deployment. Only set the new image name for the existing deployment.
k set image deploy deploymentname containername=image version and name
k set image deploy frontend webapp=kodekloud/webapp-color:v3
k set image --help

Kubernetes service
----------------

First, if we were to search into the Kubernetes node at one 92.1, 68 .1.2 from the node, we would
be able to access the pod's web page by doing a curl.
Or if the node has a die, we would fire up a browser and see the web page in a browser following the
address.
HTTP 10.2 40 4.0.2.
But this is from inside the Kubernetes node, and that's not what I really want.
I want to be able to access the web server from my own laptop without having to switch into the node
and simply by accessing the IP of the Kubernetes node.
So we need something in the middle to help us map requests to the node from our laptop through the node
to the pod running the web container.
This is where the Kubernetes service comes into play.

The Kubernetes node has an IP address, and that is one 92.1 60 8.1.2.
My laptop is on the same network as well, so it has an IP address.
One 92.1 68 1.10.
The internal pod network is in the range 10.2 40 4.0.0 and the port has an IP 10.2 40 4.0.2.
Clearly I cannot ping or access the port at address 10.2 44 0.2 as it's in a separate network.
So what are the options to see the web page?

The Kubernetes service is an object, just like parts replica set or deployments that we worked with
before.
One of its use cases is to listen to a port on the node and forward requests on that port to a port
on the port running the web application.


NodePort which is used to communicate to outside world by mapping the port on node to port on pod(internal port where webserver runs)


Node port, where the service makes an internal port accessible on a port on the node
apiVersion: apps/v1
targetPort: 80 port on the pod
port: port on the service which is an virtual server which will have its own ip address
nodePort: Port on node which is set to node which ranges from 30000 to 32767

service-definition.yaml
apiVersion: v1
kind: Service
metadata:
 name: myapp-service
spec:
 type: NodePort
 ports:
  - port: 80
    targetPort: 80
    nodePort: 30004
  selector:
   app: myapp

   kubectl create -f service-definition.yaml
   kubectl get svc

   minikube service myapp-service --url to get the application url


Since we are creating a frontend service for enabling external access to users, we will set it to NodePort.?
Service Type NodePort
--------------
deployment-definition.yaml
------------------------
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  labels:
    app: mywebsite
    tier: frontend
spec:
  replicas: 4
  template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
    spec:
      containers:
        - name: nginx
          image: nginx
  selector:
    matchLabels:
      app: myapp


service-definition.yaml
----------------------
apiVersion: v1
kind: Service
metadata:
  name: frontend
  labels:
    app: myapp
spec:
  type: NodePort
  ports:
    - port: 80
      targetPort: 80
  selector:
   app: myapp

Let us now try to create a service-definition.yml file from scratch. This time all in one go.
 You are tasked to create a service to enable the frontend pods to access a backend set of Pods. 

 
   deployment-definition.yaml
   --------------------------
apiVersion: apps/v1
kind: Deployment
metadata:
  name: image-processing-deployment
  labels:
    tier: backend
spec:
  replicas: 4
  template:
    metadata:
      name: image-processing-pod
      labels:
        tier: backend
    spec:
      containers:
        - name: mycustom-image-processing
          image: someorg/mycustom-image-processing
  selector:
    matchLabels:
      tier: backend

--ClusterIp service definition.yml

apiVersion: v1
kind: Service
metadata:
  name: image-processing
  labels:
    app: myapp
spec:
  # type: ClusterIP
  ports:
    - port: 80
      targetPort: 8080
  selector:
    tier: backend


Useful commands
kubectl delete pod postgres-pod redis-pod result-app-pod voting-app-pod worker-app-pod
kubectl get pods
kubectl get services
kubectl get nodes
kubectl scale deployment voting-app-deploy --replicas=3 

kubectl describe pod worker-app-pod 
kubectl describe servie worker-service
minikube service result-service --url