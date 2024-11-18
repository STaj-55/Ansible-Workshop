# Docker Ansible 

Now that we understand how Ansible can work on a single machine, how can we use it to set up an infrastructure, then apply these benchmarks to these new systems? We will be using Docker to create new containers to simulate an infrastructure. Since everyone who will be here will have different types of systems, I will be showing the commands from the perspective of both macOS, and WSL for Windows users.

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


