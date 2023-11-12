# Technical Test SmartM2M
Dear SmartM2M Team, thanks for your opportunity and give me time to finish the Ansible & Kubernetes Challenge Deployment. In this page, i will to describe how this case can build and work appropriate with the requirement in guideline. Lest begind


## Table of Content
- [Ansible](#ansible)
- [Kubernetes](#kubernetes)


## Ansible
### Case Study
Create an inventory file in Ansible, group your inventory to follow these criteria:
node1 should be the member of "loadbalancer" group.
node2 and node3 should be the member of "backend" group.

After creating the inventory, create a playbook to install nginx on all of the servers.
Then, create another playbook to change the default nginx index page with a html file contains "served from: <hostname>". perform this task on the backend group expected result: when we hit http://node2, we should get "served from: node2"

Lastly, create playbook to setup nginx in the loadbalancer group as follows:
- Make it load balance between nodes in the backend group
- Use "least connection" load balancing method

### Solution
First, for inventory iam create using *.ini extention and private key methode so as we did'n need add hosts password for execute the program. All of the Ansible file can you find [(here)](/ansible/)

1. Install Nginx, for run this program you can execute that command.
    ```
    ansible-playbook -i inventory.ini nginx-install.yml
    ```

2. Change default nginx page for backend.
    ```
    ansible-playbook -i inventory.ini nginx-page.yml
    ```

3. Setup nginx load balancer.
    ```
    ansible-playbook -i inventory.ini nginx-page.yml
    ```

### Result
I'am trying to access public address of node1 (loadbalancer) with some incognito browser and loadbalancer can direct to node2 and node3 (backend) webserver.

<p align="center">
<img width="400" src="result/ansible/lb1.png" alt="Load Balancer"><img width="418" src="result/ansible/lb2.png" alt="Load Balancer">
</p>

## Kubernetes
### Case Study
Create a deployment as follows:
- Name: nginx-app
- Using container nginx with version 1.11.10-alpine
- The deployment should contain 3 replicas

Next, deploy the application with new version 1.11.13-alpine, by performing a rolling update.

Finally, rollback that update to the previous version 1.11.10-alpine.

Set a node named k8s-node-1 as unavailable and reschedule all the pods running on it.

Create a Persistent Volume with name app-data, of capacity 2Gi and access mode ReadWriteMany. The type of volume is hostPath and its location is /srv/app-data.
Create a Persistent Volume Claim that requests the Persistent Volume you had created above. The claim should request 2Gi. Ensure that the Persistent Volume Claim has the same storageClassName as the Persistent Volume you had previously created.

You have been asked to set up a Kubernetes cluster, one master and one worker node.
You have done the initialization of the master, what is the next steps to make the worker node join the cluster?

### Solution

1. Make the worker node join the cluster, first insert this command on master node
    ```
    sudo kubeadm token create --print-join-command
    ```

    The result show command with credential for worker node, copy this and run in worker node. After that, check the node is join or not in master note.

    <p align="center">
    <img width="500" src="result/kubernetes/3. get-node.png" alt="Load Balancer">
    <p>
    
2. Create Deployment nginx-app, for run this program you can execute that command.
    ```
    kubectl apply -f nginx-install.yml
    ```

    After that, for make sure the apps its already running, iam using this command with result.

    <p align="center">
    <img width="500" src="result/kubernetes/4. get-pods.png" alt="Load Balancer">
    <p>

3. Deploy the application with new version 1.11.13-alpine, for this case iam user the same yml like nginx-install but with change the image version.
    ```
    kubectl apply -f nginx-upgrade.yml --record
    ```
    Alternatively you didn't create new or edit yml, you need only using 1 command like this.
    ```
    kubectl set image deployment/nginx-app nginx=nginx:1.11.13-alpine --record
    ```
    for see the the history of rolling using this command.
    ```
    kubectl rollout history deployment nginx-app
    ```
    <p align="center">
    <img width="500" src="result/kubernetes/5. history-rollout.png" alt="Load Balancer">
    <p>

    Or for verify the version is change, iam can use discribe command
    ```
    kubectl describe deployment/nginx-app
    ```

    <p align="center">
    <img width="500" src="result/kubernetes/6. descib-pods.png" alt="Load Balancer">
    <p>

4. Rollback that update to the previous version 1.11.10-alpine.
    ```
    kubectl rollout undo deployment nginx-app
    ```

    <p align="center">
    <img width="500" src="result/kubernetes/7. undo-rollout.png" alt="Load Balancer">
    <p>

5. Set a node named k8s-node-1 as unavailable and reschedule all the pods running on it.
    ```
    kubectl drain k8s-node-1 --ignore-daemonsets
    ```

    <p align="center">
    <img width="500" src="result/kubernetes/8. set-unavailable.png" alt="Load Balancer">
    <p>

6. Create a Persistent Volume with name app-data.
    ```
    kubectl apply -f pvolume.yml
    ```

    <p align="center">
    <img width="500" src="result/kubernetes/9. peersistance-volume.png" alt="Load Balancer">
    <p>    

7. Create a Persistent Volume Claim that requests the Persistent Volume you had created above.
    ```
    kubectl apply -f pvolumeclaim.yml
    ```

    <p align="center">
    <img width="500" src="result/kubernetes/10. volume-claim.png" alt="Load Balancer">
    <p>
