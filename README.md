# Project set up
* create an inventory file (e.g. hosts or hosts.yaml) that holds the remote hosts that ansible will handle.
* Example entry is
```yaml
webserver: # <-- group
  hosts: # <-- List of hosts in group
    gcloud_host: # <-- host number 1 in group
      ansible_host: 35.189.109.16
      ansible_port: 22
      ansible_ssh_user: rg
    app01:  # <-- host number 2 in group
      ansible_host: app01
    app02:  # <-- host number 3 in group
      ansible_host: app02
  vars:  # <-- common variables in this group
    ansible_python_interpreter: /usr/bin/python3
```
* to test if all hosts are accesible, run
```bash
ansible -m ping all
```
* to test if a group of hosts are accesible, run
```bash
ansible -m ping all <group-name>
```

# Run development environment with Vagrant
* run testing environment
```bash
vagrant plugin install vagrant-hostmanager
cd vagrant
vagrunt up
vagrant ssh-config >> ~/.ssh/config
```
* run a playbook
```bash
ansible-playbook -l database playbooks/database.yml
```
Links:
* [Vagrant Quick start](https://learn.hashicorp.com/collections/vagrant/getting-started)

## Vault
* create a file that holds the **secret**
```bash
touch playbooks/vars/api_key.yml
```
* encrypt the file
```bash
ansible-vault encrypt playbooks/vars/api_key.yml
```
* run task that needs this file
```bash
ansible-playbook playbooks/use-api-key.yaml --ask-vault-pass
```
and you will be asked to provide the password
* edit the encrypoted file with
```bash
ansible-vault edit playbooks/vars/api_key.ym
```
* use stored password to decrypt
create a file that holds the password with 600 permissions
```bash
vim ~/.ansible/vault_pass.txt
chmod 600 ~/.ansible/vault_pass.txt
```
```bash
ansible-playbook playbooks/use-api-key.yaml --vault-password-file  ~/.ansible/vault_pass.txt
```
Links:
* [Encrypting content with Ansible Vault](https://docs.ansible.com/ansible/latest/user_guide/vault.html)
## Ignore host_key_checking

add this line to ``ansible.cfg`` in [defaults] section
```ini
host_key_checking = false
```
Links:
* [Managing host key checking](https://docs.ansible.com/ansible/latest/user_guide/connection_details.html)


## Get host basic info
```bash
ansible-playbook -l <hostname> playbooks/hostvars_and_facts.yml
```
## Create self-signed certificates
```bash
cd files/certs
openssl req -x509 -newkey rsa:4096 -keyout server.key -out server.crt -days 365 --nodes -subj '/C=GR/O=myorganization/OU=it/CN=myorg.com'
```
notice that crt and key files are added to .gitignore


* postgres
install postgresql role
```bash
ansible-galaxy install geerlingguy.postgresql
```
## Docker
```bash
ansible-galaxy install geerlingguy.docker
ansible-galaxy install geerlingguy.pip

```
## Jenkins
```bash
ansible-galaxy install geerlingguy.jenkins
ansible-galaxy install geerlingguy.java

```

## Deploy your project without docker & kubernates
```bash
ansible-galaxy install geerlingguy.postgresql
ansible-galaxy install jebovic.mailhog
```

## Open ports on your vm
```bash
8000, 9000, 5000, 8025
```

## Urls for access project's componets
```bash
http://your_vm_ip:8000/    #adminSystem
http://your_vm_ip:9000/    #userSystem
http://your_vm_ip:8025/    #mailhog)
```

## Deploy your project with docker
```bash
ansible-playbook playbooks/docker-install.yml
ansible-playbook playbooks/django-app-docker.yml
ansible-playbook playbooks/django2-app-docker.yml
```

## Open ports on your vm
```bash
8000, 9000, 80, 8025, 100, 5432
```

## Urls for access project's componets
```bash
http://your_vm_ip/    #adminSystem
http://your_vm_ip:100/    #userSystem
http://your_vm_ip:125/    #mailhog)
```

## Deploy your project with kubernates
```bash
ansible-playbook playbooks/django-install-microk8s.yml
ansible-playbook playbooks/django2-install-microk8s.yml
```

##Install kubernates on your vm
sudo snap install microk8s --classic
sudo ufw allow in on eth0 && sudo ufw allow out on eth0
sudo ufw default allow routed

##Enable kubernates
microk8s enable dns ingress storage
microk8s enable dashboard

##Access with sudo
sudo usermod -a -G microk8s $USER
sudo chown -f -R $USER ~/.kube
su - $USER

## Open ports on your vm
```bash
8000, 9000, 80, 8025, 100, 16443, 30191
```

## Urls for access project's componets
```bash
django-21918.cloudns.ph #adminSystem
django2-system.ddns.net #userSystem
your_vm_ip:30191 #mailhog
```

