# Installation of kubeproxy in Google Cloud Platform
* Run below command to create the google cloud instances
gcloud compute instances create ansible master-kubernetes node1 --image=centos-7
* Generate key-pair to ssh to all three instances.
run ssh-keygen and upload public key to gcp at project level
* ssh to ansible node and install required packages:
* Generate key-pair named ansible_keys for anisible to connect to other vms. Upload it's public key to GCP at project level(can be added to aster-kubernetes node1)
* Also create ~/.ssh/config file in ansible instance
```
  $ cat .ssh/config
    Host node1 master-kubernetes
        IdentityFile ~/.ssh/ansible_keys
```

*	Install pip:
```
  $curl -LO https://bootstrap.pypa.io/get-pip.py
  $sudo python get-pip.py
  $ pip -V
  pip 18.1 from /usr/lib/python2.7/site-packages/pip (python 2.7)
```

* Install Ansible
```
  $ sudo pip install ansible
  $ ansible --version
    ansible 2.7.5
    config file = None
    configured module search path = [u'/home/mukes/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
    ansible python module location = /usr/lib/python2.7/site-packages/ansible
    executable location = /usr/bin/ansible
    python version = 2.7.5 (default, Oct 30 2018, 23:45:53) [GCC 4.8.5 20150623 (Red Hat 4.8.5-36)]
```

* Install netaddr
  $ sudo pip install netaddr
  
* Download kubeproxy package from github(https://github.com/kubernetes-incubator/kubespray/tags)
  Latest version: https://github.com/kubernetes-sigs/kubespray/archive/v2.8.1.tar.gz</br>
  Run command:
```
   $curl -LO https://github.com/kubernetes-incubator/kubespray/archive/v2.8.1.tar.gz
   $tar -xvf v2.8.1.tar.gz
   $cd kubespray-2.8.1/
```
  There is sample folder under inventory and copy and rename it to inventory/mycluster
        ``` $cp -rfp inventory/sample inventory/mycluster```

* Edit hosts.ini file and add below content
    
    ```
    [all]
    master-kubernetes ansible_ssh_host=master-kubernetes ansible_user=your_username
    node1 ansible_ssh_host=node1 ansible_user=your_username
    [kube-master]
    # node1
    # node2
    master-kubernetes
    [etcd]
    master-kubernetes
    [kube-node]
    node1
    [k8s-cluster:children]
    kube-master
    kube-node
   ```
* Run below command to test if connection is working
```
    $ ansible -i inventory/mycluster/hosts.ini -m ping all
    node1 | SUCCESS => {
        "changed": false,
        "ping": "pong"
    }
    master-kubernetes | SUCCESS => {
        "changed": false,
        "ping": "pong"
```
    
* Also run below command to check if you can escalate the privilege
    $ ansible -b -i inventory/mycluster/hosts.ini -m ping all

* Finally run command to create the cluster:
     ```$ ansible-playbook -b -i inventory/mycluster/hosts.ini cluster.yml```

* After successful installation, login to master node and run basic commands e.g.
```
      # /usr/local/bin/kubectl get nodes
      NAME                STATUS   ROLES    AGE   VERSION
      master-kubernetes   Ready    master   60m   v1.12.3
      node1               Ready    node     59m   v1.12.3
   ```
* kubernetes will be run as root user so commands won't work. Run below command to solve the issue:
```
    mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

* /usr/local/bin is not added in root path, so it can be added in .bashrc of root:</br>
    export PATH=/usr/local/bin:$PATH
* For removing the installation, run reset playbook from ansible instance</br>
``` $ansible-playbook -b -i  inventory/mycluster/hosts.ini reset.yml ```

  

 
