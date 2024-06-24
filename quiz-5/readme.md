#### 1. Create a Directory: 
To create a directory named “quiz-5” in your working directory
````
mkdir quiz-5
````

#### 2. Create ansible.cfg and Inventory:
````
#ansible.cfg
[defaults]
inventory = ./inventory
````

````
#inventory
[managed]
pod-abdul7-managed1
pod-abdul7-managed2
````

#### 3.Create Encrypted File: 
Create a file named quiz-Secret.yml
````
username: abdul7
passwd: adinusa2023
````

Encrypt the file using Ansible Vault:
````
ansible-vault encrypt quiz-Secret.yml
````

#### 4. Create Playbook: Create a playbook named quiz-5_secrets.yml that uses the variables from quiz-Secret.yml.
````
#quiz-Secret.yml
- name: create user accounts for all servers
  hosts: managed
  become: true

  vars_files:
    - quiz-Secret.yml

  tasks:
    - name: create user form quiz-Secret.yml
      user:
        name: "{{username}}"
        password: "{{passwd | password_hash('sha512')}}"
````


#### 5. Create Password File: Create a plain text file named quiz-pass
````
echo 'adinusa' > quiz-pass
````

Change the permissions of the file:
````
chmod 600 quiz-pass
````

#### 6. check syntax with ask vault pass
````
ansible-playbook --syntax-check --ask-vault-pass quiz-5_secrets.yml
````

#### 7. Run playbook with use password from file quiz-pass
````
ansible-playbook  --vault-password-file=quiz-pass quiz-5_secrets.yml
````


