<h1>Kubernetes Cluster Installation and Configuration</h1>


<h2>Description</h2>
This project demonstrates how to set up a multi-node Kubernetes cluster from scratch using kubeadm. 
It covers control plane initialization, worker node joining, pod networking setup with Flannel, 
etcd backup, and restore operations.
<br />


<h2>Tools and Technologies</h2>

<ul>
    <li>Ubuntu 24.04 LTS</li>
    <li>Kubernetes (kubeadm, kubelet, kubectl)</li>
    <li>Containerd as container runtime</li>
    <li>Flannel for pod networking</li>
    <li>etcdctl for etcd backup and restore</li>
    <li>EC2 Instances (AWS)</li>
</ul>

<h2>Project Walk-through</h2>

<p align="center">
Install common dependencies, container runtime (containerd), and Kubernetes tools (kubeadm, kubelet, kubectl) on all nodes <br />
<img src="https://i.postimg.cc/VL4KBwq6/1.jpg"/>
<br />
<br />
Initialize the Kubernetes control plane node using kubeadm and Install Flannel CNI <br/>
<img src="https://i.postimg.cc/vH8zmKJd/2.jpg" />
<br />
<br />
Join worker nodes to the cluster using the kubeadm join command  <br/>
<img src="https://i.postimg.cc/ZRZNMBZh/3.jpg"/>
<br />
<br />
Deploy a test application & Perform etcd backup <br/> 
<img src="https://i.postimg.cc/sxbDbqSZ/4.jpg" />
<br />
<br />
Simulate data loss and restore cluster from etcd backup <br/> 
<img src="https://i.postimg.cc/JzZGCrp7/5.jpg" />
<br />
<br />

</p>

