# Docker Ansible 

Now that we understand how Ansible can work on a single machine, how can we use it to set up an infrastructure, then apply these benchmarks to these new systems? We will be using Docker to create new containers to simulate an infrastructure. Since everyone who will be here will have different types of systems, I will be showing the commands from the perspective of both macOS, and WSL for Windows users.

# WSL Steps
First we are going to install Docker on our system. You can do this from Docker's official [website](https://docker.com)

DOnce we have Docker Desktop installed, we are then going to install wsl through Powershell. Here's how:
 - *wsl --install*
   
![wsla1](https://github.com/user-attachments/assets/dac9a988-ff0a-43a8-b5c8-2a4a13eed9ae)

After installing wsl and create a UNIX user, we need to ensure that Docker is running and is integrated with WSl. On the Docker Desktop application, head **Settings > Resources > WSL Integration** and enable Docker for the distro shown.

![wsla2](https://github.com/user-attachments/assets/f24f712f-2647-432c-93eb-ac8daa4a603e)

Now we can finally verify within our WSL session with the command:
 - *docker --version*

![wsla3](https://github.com/user-attachments/assets/6dbec798-b359-47e3-a36e-b01e5fc587f2)

I experienced some network issues with WSL so here is the troubleshooting I did incase any of your experiences this as well:

I wasn't able to update anything so I tried to ping google.com and it wouldn't resolve. I then pinged 8.8.8.8 and it work so we're dealing with DNS. Let's edit the resolv.conf and wsl.conf file our issues.
 - *sudo nano /etc/resolv.conf*
   - nameserver 8.8.8.8 #Google DNS
   - nameserver 1.1.1.1 #Cloudflare DNS

![wsla4](https://github.com/user-attachments/assets/875c3245-8939-47fc-bfa3-c4487a691d7e)

 - *sudo nano /etc/wsl.conf
   - [network]
   - generateResolvConf = false

![wsla5](https://github.com/user-attachments/assets/21799e32-ecf7-4b69-86b3-1a101545cfe6)

After this you can shutdown out of your instance and start a new shell. Now that our network issues are fixed, we can go about updating our environment to support Ansible.
 - *wsl -d ubuntu*
 - *sudo apt update*
 - *sudo apt install -y software-properties-common*
 - *sudo add-apt-repository --yes --update ppa:ansible/ansible*
 - *sudo apt update*

![wsl6](https://github.com/user-attachments/assets/4ec60969-0163-44a0-b3ca-9b2ef6cc54f4)

Now that our environment is set, we can install Python3-Pip and Ansible. Run the following command to do so:
 - *sudo apt install -y ansible python3-pip*

Now that our install is complete, lets install the Docker SDK for Python. This is needed for the Ansible Docker Module.
 - *sudo apt install -y python3-docker*

The last install left to do is the Ansible collection for Docker Community. Here is the command we need to run to do so:
 - *ansible-galaxy collection install community.docker*

Now that we have everything installed, we are going to create our playbook to set up new Ubuntu containers preconfigured with our security benchmarks.

You can get the yaml file for this lab from my github with the following command:
 - *wget https://raw.githubusercontent.com/STaj-55/Ansible-Workshop/main/docker_wsl.yml*

With the playbook yaml file on our system, we can now use ansible to create our new Docker containers with our benchmarks.
 - *ansible-playbook docker_wsl.yml --ask-become-pass*

![wsl12](https://github.com/user-attachments/assets/acd011d6-d257-4508-97cf-675cbce64c28)
![wsl13](https://github.com/user-attachments/assets/13f32f6d-f60f-484a-8069-9e8ea28fdcd3)

When first running the command you will be prompted to proivde your BECOME pass (this is your normal pass).

![wsla14](https://github.com/user-attachments/assets/15b41e4c-b91c-41cd-adaf-499106528eec)

The file should run successfully and show that each container is now created when you check on Docker Desktop.

Now we are going to verify that our configurations have been applied.

First we'll go through manually to ensure our changes have been made. Here is the command to sign onto one of your containers:
 - *docker exec -it server1 bash*

After getting on the container we'll run the following commands to ensure we receive an output, knowing our configs stayed:
 - *grep '^Protocol 2' /etc/ssh/sshd_config*
 - *grep '^IgnoreRhosts' /etc/ssh/sshd_config*
 - *grep '^PermitEmptyPasssword' /etc/ssh/sshd_config*
 - *grep '^PermitRootLogin' /etc/ssh/sshd_config*
   
![wsla6](https://github.com/user-attachments/assets/759a2eab-fe34-4411-bd61-536e25fd5e43)

 - *ufw status*
   
 ![wsla10](https://github.com/user-attachments/assets/b0b4a9e2-2332-46a8-a664-61d7b7c04ead)

 - *cat /etc/issue*
 - *cat /etc/issue.net*
 - cat /etc/motd
   
![wsla7](https://github.com/user-attachments/assets/e24a106d-05de-4920-820e-97751e50d92b)

 - cat /etc/modprobe.d/CIS.conf
   
![wsl11](https://github.com/user-attachments/assets/72436c8d-fff7-43be-9c41-9d60065e7a15)

 - sysctl net.ipv4.tcp_syncookies
   
 ![wsla8](https://github.com/user-attachments/assets/5ab36386-6d22-4fac-9da2-e49e5de76e77)

 - *ls -l /etc/passwd | grep '^-rw-r--r--'*
 - *ls -l /etc/shadow | grep '^-rw-r-----'*
 - *ls -l /etc/gshadow | grep '^-rw-r-----'*
 - *ls -l /etc/group | grep '^-rw-r--r--'*

![wsla9](https://github.com/user-attachments/assets/860188f1-f47b-456d-a6f0-ddee504d1063)

As you can see by our screenshots, all configurations we have made have been successful. I hope you enjoyed learning about Ansible and it's cool capabilities.

# macOS Steps
First we are going to install Docker on our system. You can do so from Docker's official [website](https://docker.com).

Once we have Docker Desktop installed we are going to verify the installation. On macOS you can do this in the terminal by running the command:
 - *docker --version*

<img width="449" alt="Screenshot 2024-11-17 at 6 58 42 PM" src="https://github.com/user-attachments/assets/6e83cad0-be9d-4b2a-a20c-8a12244f33c2">

After installing Docker, we are going to make sure Python is up to date on our system

**Note: For macOS, I ran into a lot of issues with incompatible versions of Ansible and Python, below are the correct commands versions needed to make this lab work**
 - *brew install pyenv*
 - *pyenv install 3.9.16*
 - *pyenv global 3.9.16*
 - *python --version*

After running these commands trying to install the correct version of python, my version didn't change so here is the troubleshooting i did:
 - *echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc*
 - *echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc*
 - *echo 'eval "$(pyenv init --path)"' >> ~/.zshrc*
 - *source ~/.zshrc*
 - *python --version*

After getting your Python version to 3.9, you can now comfortably install Ansible on your control node. We will be installing a version that is compatible with Docker through github. Here is the command:
 - *git clone https://github.com/ansible/ansible.git*
 - *cd ansible*
 - *git checkout stable-2.15*
 - *pip install -e .*

To ensure it is fully installed we will run the following command:
 - *pip install ansible-core=2.15.0*
 - *ansible --version*

After installing Ansible, we are now going to install the Docker Collection for Ansible. 

Here is the command for macOS:
 - *ansible-galaxy collection install community.docker --force*

Here is the command for the Docker SDK for Python:
 - *pip3 install docker*

Now that we have all the prerequesities, let's start by creating our initial Ansible Playbook. This playbook will be used to set up Docker containers and apply standard configurations.

In this config file, we are going to perform the following:
 - *Create a Docker Network*
 - *Set up Docker Containers*
 - *Standardize Container Setup*
 - *Apply CIS Benchmark Standards*

Now you can do one of the following:
 - *Write the entire file yourself*
 - *Pull it from my Github*

I highly recommended pulling my config file, so you don't have to deal with syntax issues. Here's the command to do so:
 - wget https://raw.githubusercontent.com/STaj-55/Ansible-Workshop/main/docker_lab.yml

To check your conf file to ensure everything is correct, you can use a site like YAMLint to check for syntax errors. Another methods is by adding *-vvv* towards the endc of your ansible-playbook file.

After writting the complete config file, you can then run the comamnd to start Docker and to create all of the containers that are preconfigured

We can do this with the following command:
 - *sudo ansible-playbook docker_lab.yml --ask-become-pass*

<img width="1008" alt="Screenshot 2024-11-17 at 11 33 07 PM" src="https://github.com/user-attachments/assets/80016737-0dca-438f-9c97-677e0de61b8d">

<img width="479" alt="Screenshot 2024-11-17 at 11 33 18 PM" src="https://github.com/user-attachments/assets/cc8e3da1-6818-4c85-95e3-b788d2b54074">

<img width="938" alt="Screenshot 2024-11-17 at 11 33 28 PM" src="https://github.com/user-attachments/assets/ca03ddb0-9c6c-4cce-84d1-689bf1ba76d7">

Now that this has worked successfully, we can see in Docker that the three containers are now live.

<img width="960" alt="Screenshot 2024-11-17 at 11 34 38 PM" src="https://github.com/user-attachments/assets/95512ea2-0f94-4787-915a-0bcbfaad84d7">

All we need to do now is ensure that each container has their security configurations applied. We can run this script in order to check everything in a quick manner.


