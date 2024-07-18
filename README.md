# k8's cluster setup in on-premise network 

## Create VM instances

Access your server and create an VM instance with 4 VM instances with 2 VCPU, 1 core and 8 GB RAM along with 40 GB storage.

**OR**

IN GCP create 4 VM instances with 2 VCPU, 1 core and 8 GB RAM along with 40 GB storage.


The VM also should be Ubuntu 22.04 

Here we will be having one node as master node and other 3 nodes as worker nodes.

## Firewall Rule

If you are using any cloud provider go to their Firewall Rule and create a firewall to allow IPIP protocol also. 

Example for GCP firewall rule setup:

Name: k8s-calico

Network: Select default.

Priority: Set the priority for this rule. Lower numbers indicate higher priority. (Defaults to 1000 if not specified.)

Direction of traffic: Choose Ingress to allow incoming traffic.

Action on match: Choose Allow.

Targets: Choose All instances in the network 

Source filter: Choose IP ranges.

Source IP ranges: Enter 10.240.0.0/24 or 0.0.0.0/0

Protocols and ports: Select Specified protocols and ports and enter tcp, udp, icmp, ipip.(Small)

## All the Nodes

Setup the hosts for kubernetes configuration.

To know about the hostname enter the command. 

```bash
hostname -f
```

Enter the following details in the /etc/hosts file

```bash
sudo nano /etc/hosts

Internal IP     hostname of master
Internal IP     hostname of worker1
Internal IP     hostname of worker2
Internal IP     hostname of worker3
```

## Master Node


 **Now copy the masterNode.sh script file to the master node**

 Give permission to execute the script file:

 ```bash
 sudo chmod +x masterNode.sh
 ```

## Worker Nodes

  **Now copy the workerNode.sh script file to the worker nodes(1,2,3)**

  Give permission to execute the script file:

 ```bash
 sudo chmod +x workerNode.sh
 ```

## Master Node

If you have missed the join command with the master node execute this command to generate it again.

 ```bash
kubeadm token create --print-join-command
 ```

Copy this command

## Worker Node

Enter the copied command along with **sudo**.

## Master Node

Now when you enter the command **kubectl get nodes**

You will see the status not ready for all the nodes to make it ready install network policy agent **Calico**.

Either copy the calico.yaml file available and paste it here and execute this command.

```bash
kubectl apply -f calico.yaml
 ```

**OR**

Use the following commands to setup calico: If you want to change the pod CIDR from 192.168.0.0/16 change it in the file and then deploy. THis is with the default configuration

```bash
curl https://raw.githubusercontent.com/projectcalico/calico/v3.28.0/manifests/calico.yaml -O

kubectl apply -f calico.yaml
 ```

Now after all the pods are up check the nodes using this command:

```bash
kubectl get nodes
 ```
