# Deploying HA Kubernetes Cluster with Ansible

1. In this project I use vagrant to provisin a local dev environment to deploy k8s HA cluster.

2. HAProxy and keepalived are used for loadbalancing of cluster.

3. Below you can perform steps for ansible:


installing Python3.8.12 (optional)

{
    sudo yum -y groupinstall "Development Tools"
    sudo yum -y install openssl-devel bzip2-devel libffi-devel xz-devel wget
    cd /usr/src/ 
    sudo wget https://www.python.org/ftp/python/3.8.12/Python-3.8.12.tgz
    sudo tar -xvzf Python-3.8.12.tgz
    cd Python-3.8.12/
    ./configure --enable-optimizations
    sudo make altinstall
    python3.8 --version
}

create venv for projct

{
    cd
    python3.8 -m venv project
    source project/bin/activate
    pip install --upgrade pip
    pip install ansible
}

#ansible config file

ansible-config init --disabled > ansible.cfg

vi ansible.cfg
inventory=/home/vagrant/ansible/inventory/hosts
remote_user = vagrant
host_key_checking = False
become=False
become_method=sudo
become_user=root
become_ask_pass=False


{
    export ANSIBLE_CONFIG=/home/vagrant/ansible/ansible.cfg
    echo "export ANSIBLE_CONFIG=/home/vagrant/ansible/ansible.cfg" >> ~/.profile
    ansible --version
}

#inventory:
[all_k8s]
k8s-master01
k8s-master02
k8s-master03
k8s-worker01
lb01
lb02

[masters]
k8s-master01
k8s-master02
k8s-master03

[workers]
k8s-worker01

[lb]
lb01
lb02

[ha_leader]
k8s-master01

[ha_member]
k8s-master02
k8s-master03

[ansible]
ansible1


#test
ansible  all_k8s -m ping

4. running below ansible playbooks one by one:

ansible-playbook 00_hostfile.yml
ansible-playbook 01_haproxy_keepalived.yml
ansible-playbook 02_k8s_preparation.yml
ansible-playbook 03_k8s_ha_leader.yml
ansible-playbook 04_k8s_calico.yml
ansible-playbook 05_k8s_ha_member.yml
ansible-playbook 06_k8s_worker.yml

