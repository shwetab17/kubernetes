# kubernetes setup on Ubuntu 18.04 LTS using kubeadm

Platform: ubuntu 18.04 (bionic lts)

Prerequisites:
 2 vCPUs
 2 GB mem
 swap disaled

install docker
    
    apt install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
    add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    apt update
    apt install -y docker-ce=5:19.03.10~3-0~ubuntu-bionic containerd.io


Kubernetes Setup
Add Apt repository

    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
    echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" > /etc/apt/sources.list.d/kubernetes.list

Install Kubernetes components
       
     apt update && apt install -y kubeadm=1.19.0-00 kubelet=1.19.0-00 kubectl=1.19.0-00

On kmaster:

    Initialize Kubernetes Cluster
        kubeadm init --apiserver-advertise-address=192.168.1.122 --pod-network-cidr=192.168.0.0/16  --ignore-preflight-errors=all
    
   Deploy Calico network
                  
         kubectl --kubeconfig=/etc/kubernetes/admin.conf create -f https://docs.projectcalico.org/v3.14/manifests/calico.yaml
   Cluster join command
            
            kubeadm token create --print-join-command
        o/p: kubeadm join 192.168.1.122:6443 --token xbtbwr.bzgxqx0ysuacxzti     --discovery-token-ca-cert-hash sha256:53c7233e3b709ccaa4f41e5cacedee0227ff2e5a467066dc8d04dd5349621437

   To be able to run kubectl commands as non-root user
   If you want to be able to run kubectl commands as non-root user, then as a non-root user perform these

        mkdir -p $HOME/.kube
        sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
        sudo chown $(id -u):$(id -g) $HOME/.kube/config


On Kworkers:

   Join the cluster
    
    kubeadm join 192.168.1.122:6443 --token xbtbwr.bzgxqx0ysuacxzti     --discovery-token-ca-cert-hash sha256:53c7233e3b709ccaa4f41e5cacedee0227ff2e5a467066dc8d04dd5349621437


Verifying the cluster
    Get Nodes status
      
      kubectl get nodes
   Get component status
   
    kubectl get cs
