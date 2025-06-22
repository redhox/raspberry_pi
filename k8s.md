
## install du config panel pour pi <a href="https://amrith.me/posts/tech/k8s/install-k8s-raspberrypi/">source</a>
### raspberry pi cgroup config:
pour les pi le fichier de config boot n'est pas <code>/boot/cmdline.txt</code> mais <code>/boot/firmware/cmdline.txt</code>
les ellement du fichier boot sont sur une seul ligne 
```bash
sudo nano /boot/firmware/cmdline.txt
```

```bash
cgroup_enable=cpuset cgroup_enable=memory
```
puis reboot 
### Network config
nommé la machine 
```bash
sudo hostnamectl set-hostname k8s-contol-panel
```
ip de la machine et nom de l'hote de la machine
```bash
echo "192.168.1.25 k8s-control-panel" | sudo tee -a /etc/hosts
```
```bash
sudo nano /etc/network/interfaces.d/eth0
```
```bash
auto eth0
iface eth0 inet static
address 192.168.1.88
netmask 255.255.255.0
gateway 192.168.1.1
dns-nameservers 1.1.1.1 1.0.0.1
```
### desactivation du swap
pour la session
```bash
swapoff -a
```
indefiniment 
```bash
sudo dphys-swapfile swapoff
sudo systemctl disable dphys-swapfile
sudo rm -f /var/swap
```
### kuber network
```bash
cat <<EOF | tee /etc/modules-load.d/containerd.conf
overlay
br_netfilter
EOF
modprobe overlay
modprobe br_netfilter
cat <<EOF | tee /etc/sysctl.d/99-kubernetes-k8s.conf
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF
sysctl --system
```
Install Containerd
```bash
apt update
apt install -y containerd
```
```bash
containerd config default > /etc/containerd/config.toml
```
ligne 114 changer ``` [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options] ``` "SystemdCgroup" en ```true```
```bash
nano -l /etc/containerd/config.toml
```
```bash
systemctl enable containerd
systemctl restart containerd
```
### instalation de kuber 
ajout des repertoires
```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gpg
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.31/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

```
installation des paquets
```bash
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
```
bloquer les mis a jour de kuber
```bash
sudo apt-mark hold kubelet kubeadm kubectl
```
### initialisation
```bash
sudo kubeadm init --control-plane-endpoint=$HOSTNAME
```  
pour les droit utilisateur d'utilisation <code>kubectl</code>
```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
``` 
pour l'utilisation as root
```
>export KUBECONFIG=/etc/kubernetes/admin.conf
```
le control panel devrais etre fonctionelle 
```bash
kubectl get nodes
```
ajout du modul <code>calico</code> pour le control network
```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.29.0/manifests/calico.yaml

```
verifier l'install de calico (source crash si le network n'est pas corectement configuré)
```bash
kubectl get pods -n kube-system
```
créé un tokent d'invitation de nodes (duré 24h)
```bash
sudo kubeadm token create --print-join-command
```
créé un tokent d'invitation de control panel (duré 24h)
```bash
kubeadm token create --print-join-command --ttl
```
## delet config
```bash
sudo kubeadm reset
sudo apt-get purge kubeadm kubectl kubelet kubernetes-cni kube*
sudo apt-get autoremove
```
```bash
sudo rm -rf ~/.kube
sudo rm -rf /etc/kubernetes
sudo rm -rf /etc/cni/net.d
sudo rm -rf /var/lib/kubelet
sudo rm -rf /var/lib/dockershim # Si vous utilisiez Docker comme runtime
sudo rm -rf /opt/cni
sudo rm -rf /var/lib/etcd
```
```bash
sudo iptables -F && sudo iptables -X
sudo iptables -t nat -F && sudo iptables -t nat -X
sudo iptables -t raw -F && sudo iptables -t raw -X
sudo iptables -t mangle -F && sudo iptables -t mangle -X
```
```bash
sudo systemctl restart docker
```
```bash
sudo ip link set cni0 down
sudo ifconfig cni0 down
sudo brctl delbr cni0 
```
```bash
sudo rm -rf /etc/cni/net.d/*
```
```bash
sudo systemctl daemon-reload
```

## create nodes
supression du swap
```bash 
sudo dphys-swapfile swapoff
sudo systemctl disable dphys-swapfile
sudo rm -f /var/swap
``` 
créé un nom pour les worker
```bash 
sudo hostnamectl set-hostname k8s-worker1
``` 
ajout de l'ip du control panel pour la connection  

```bash
echo "192.168.1.25 k8s-control-panel" | sudo tee -a /etc/hosts
```
installation de containerd
```bash 
apt update
apt install -y containerd
containerd config default > /etc/containerd/config.toml
systemctl enable containerd
systemctl restart containerd
```
ajout des repo kuber
```bash 
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gpg
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.31/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

```
installation de kuber
```bash 
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```
supression de la config d'une node
```bash  
sudo kubeadm reset
sudo rm -rf /etc/kubernetes/pki/ca.crt
sudo rm -rf /etc/kubernetes/kubelet.conf
```
# installation de helm:
```bash  

curl https://baltocdn.com/helm/signing.asc | sudo gpg --dearmor -o /usr/share/keyrings/helm.gpg
echo "deb [signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable.list
sudo apt update
sudo apt install helm -y
```
installation de ngnix
```bash  
helm upgrade --install ingress-nginx ingress-nginx \
  --repo https://kubernetes.github.io/ingress-nginx \
  --namespace ingress-nginx --create-namespace \
  --set controller.image.repository=registry.k8s.io/ingress-nginx/controller \
  --set controller.image.tag="v1.12.3" \
  --set controller.image.arch="arm64" \
  --set controller.admissionWebhooks.enabled=false
```
