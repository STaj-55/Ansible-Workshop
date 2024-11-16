# Ansible-Workshop

***Today we will be learning how Ansible works and how we can use it in Cybersecurity!***

For our implementation of Ansible in Cyber, we will be using Ansible to apply baseline security settings aligned with CIS Benchmarks. Center for Internet Security developed the best practices for improving cybersecurity, ensuring that their benchmarks can help protect systems against common thretas and ensure compliance with industry standards.

To start, we first need to obtain an ISO of **Ubuntu 24.04 LTS**. Once we have that, we can create a VM for workshop.

***Note: If you are using your own personal ubuntu VM, I would recommend taking a snapshot of your VM incase this lab messes with any of your present configurations***

After completing the Ubuntu installation process, we can finally start our lab.

First we are going to ensure our environment is up to date with the command: 
 - *sudo apt update*

Now we are going to install ssh through the following commands:
 - *sudo apt install openssh-server*
 - *sudo systemctl start ssh*
 - *sudo systemctl enable ssh*

Once that is done, we can now install **Ansible** using the command: 
 - *sudo apt install ansible*

After the installation finishes, we verify the installation is complete by checking the version of Ansible that is installed. We can do this with: 
 - *ansible --version* 

![ansible1](https://github.com/user-attachments/assets/2f4d5cac-e9e4-4d48-aaea-ad257581eef7)

Now that Ansible is installed, we are going to create an **Inventory File**. First we are going to create a directory to store our playbooks in. I created a hidden directory for just my playbooks as shown in the screenshot.

![ansible2](https://github.com/user-attachments/assets/a682fec5-4858-4970-bf0b-899cdf6b24eb)

Once here, we will create the inventory file with the command: 
 - *nano inventory.ini*

![ansible3](https://github.com/user-attachments/assets/31acef0e-3df0-4af0-9aae-8b7159e52d2d)

This will allow us to define our managed nodes, in our situation localhost, so Ansible knows where to apply the configurations.

Now in the same directory we are going to create our CIS Benchmark playbook. You can either write down the CIS Benchmarks with the text editor of your choice, or you can pull the yaml file I created on my github. The choice is yours...

Here a command to run to install the playbook file to your system:
 - *wget https://raw.githubusercontent.com/STaj-55/Ansible-Workshop/main/CIS_Benchmarks.yml*

Here is the explanation of each of our security configruations and why:
 - **Disabled Unused Filesystems**: Disabling unused filesystems reduces the system's attack surface by removing unnecessary components.
 - **SSH Protocol Set to 2**: Enforcing SSH protocol version 2 enhances security by using a more robust, security and updated protocol.
 - **Disabled PermitEmptyPasswords**: Disallowing empty passwords prevents unauthorized access through accoutns without passwords.
 - **Enabled IgnoreRhosts**: Ignoring .rhosts files prevent insecure trust relatioships that could lead to unauthorized access.
 - **UFW Firewall Enabled**: Enabling the firewall controls network access, allowing only approved connections to the server.
 - **Password Policy Configuration**: Enforcinggg strong passwords and limiting retries reduces the risk of brute-force attacks.
 - **MOTD and Login Banners**: Displaying a security warning deters unauthorized access and establishes legal notice of monitoring.
 - **TCP SYN Cookies Enabled**: Enabling SYN cookies help protect the server form SYN flood attacks, a common DoS method.
 - **File Permissions for Sensitive Files**: Setting secure permissions on key system files restricts unauthorized access to critical data.

After creating or importing the yaml playbook, we can now run throug the following command: 
 - *ansible-playbook -i inventory.ini CIS_Benchmarks.yml --ask-become-pass*

for the become pass provide your account pass

![ansible4](https://github.com/user-attachments/assets/941e64ac-a850-462a-969c-a6c295667d3e)
![ansible5](https://github.com/user-attachments/assets/29596b99-3c1e-44c5-a4d5-7b8c1741fa91)

After completing the command, you should see 28 changed (mine took several tries and some troubleshooting)

Now let's verify that each of the changes we made through Ansible, have been applied. First let's verify disabled filesystems. We can do this with the command:
 - *cat /etc/modprobe.d/CIS.conf*
   
![ansible6](https://github.com/user-attachments/assets/42c3556d-f2ca-4ecd-8f58-b90810040f7f)

If done successfully, your output should look like the screenshot above ^

Next is verification of SSH Configs. We are going to run a few grep commands:
 - *grep '^Protocol' /etc/ssh/sshd_config*
 - *grep '^PermitEmptyPasswords' /etc/ssh/sshd_config*
 - *grep '^IgnoreRhosts' /etc/ssh/sshd_config*

![ansible7](https://github.com/user-attachments/assets/30b43d8f-f19f-4964-8a72-2a44167098f8)

If done successfully, your output should look like the screenshot above ^

After verifying SSH, we are going to verify our firewall. Here is the command to check:
 - *sudo ufw status*

![ansible8](https://github.com/user-attachments/assets/529bfa4d-9705-4558-9c81-028a89bf3946)

Your firewall output should say active, showing the configurations were applied.

Now let's verify password policy configurations, here are the grep commands to do so:
 - *grep '^minlen' /etc/security/pwquality.conf*
 - *grep '^retry' /etc/security/pwquality.conf*

![ansible9](https://github.com/user-attachments/assets/0b40f262-9096-4c5e-bb87-6bd7925b10a8)

Our output should resemble the values we assinged within our playbook.

Let's ensure the Message of the Day has been applied. We can do so with the cat command:
 - *cat /etc/motd*
 - *cat /etc/issue*
 - *cat /etc/issue.net*

![ansible10](https://github.com/user-attachments/assets/26c0032f-dc2d-4e0f-8f6d-694ef92a0b79)

With our security message appearing with each cat command, we have successfully set up our MOTD.

Let's check our network setting for TCP SYN Cookies. The command to do so is:
 - *sysctl net.ipv4.tcp_syncookies*

![ansible11](https://github.com/user-attachments/assets/b3c79196-764c-4364-8eef-e72d35d79b82)

With our output saying 1, we have succesfully assinged our tcp syn cookies correctly.

Finally we are going to check our file permissions that we changed in our playbook. Here's how to use the ls command:
 - *ls -l /etc/passwd*
 - *ls -l /etc/shadow*
 - *ls -l /etc/gshadow*
 - *ls -l /etc/group*

![Ansible12](https://github.com/user-attachments/assets/037bab73-a2d7-4d12-950e-7bb9c0f08c49)

Congratulations! You have successfully completed every step to our Ansible Workshop! I hope you had fun learning about Ansible and its awesome powers of automation. If you have any questions, feel free to let me know.

