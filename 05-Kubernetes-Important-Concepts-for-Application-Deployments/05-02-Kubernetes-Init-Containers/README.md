# Kubernetes - Init Containers

## Step-01: Introduction
- Init Containers run **before** App containers
- Init containers can contain **utilities or setup scripts** not present in an app image.
- We can have and run **multiple Init Containers** before App Container. 
- Init containers are exactly like regular containers, **except:**
  - Init containers always **run to completion.**
  - Each init container must **complete successfully** before the next one starts.
- If a Pod's init container fails, Kubernetes repeatedly restarts the Pod until the init container succeeds.
- However, if the Pod has a `restartPolicy of Never`, Kubernetes does not restart the Pod.


## Step-02: Implement Init Containers
- Update `initContainers` section under Pod Template Spec which is `spec.template.spec` in a Deployment
```yml
  template:
    metadata:
      labels:
        app: usermgmt-restapp
    spec:
      initContainers:
        - name: init-db
          image: busybox:1.31
          command: ['sh', '-c', 'echo -e "Checking for the availability of MySQL Server deployment"; while ! nc -z mysql 3306; do sleep 1; printf "-"; done; echo -e "  >> MySQL DB Server has started";']
```
- Command Breakdown
The command is a shell script that does the following:
Echo Initial Message:
bash
echo -e "Checking for the availability of MySQL Server deployment"
This prints a message indicating that the check is starting.
While Loop:
bash
while ! nc -z mysql 3306; do
  sleep 1
  printf "-"
done
nc -z mysql 3306: Uses the nc (netcat) command to check if port 3306 (MySQL's default port) is open on the host named "mysql".
If the connection fails, it sleeps for 1 second and prints a "-".
This loop continues until a successful connection is made.
Final Echo:
bash
echo -e "  >> MySQL DB Server has started"
Once the loop exits (meaning a successful connection was made), it prints a message indicating that MySQL is ready.
Workflow
When the pod starts, Kubernetes runs this init container before the main application container.
The init container repeatedly attempts to connect to the MySQL server.
It will keep trying until it successfully connects, ensuring the database is ready.
Once the connection is successful, the init container completes, allowing the main application container to start.
This approach ensures that the main application doesn't start until its database dependency is fully operational, preventing potential errors due to an unavailable database.

## Step-03: Create & Test
```
# Create All Objects
kubectl apply -f kube-manifests/

# List Pods
kubectl get pods

# Watch List Pods screen
kubectl get pods -w

# Describe Pod & Discuss about init container
kubectl describe pod <usermgmt-microservice-xxxxxx>

# Access Application Health Status Page
http://<WorkerNode-Public-IP>:31231/usermgmt/health-status
```

## Step-04: Clean-Up
- Delete all k8s objects created as part of this section
```
# Delete All
kubectl delete -f kube-manifests/

# List Pods
kubectl get pods

# Verify sc, pvc, pv
kubectl get sc,pvc,pv
```

## References:
- https://kubernetes.io/docs/concepts/workloads/pods/init-containers/
