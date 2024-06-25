#### 1. Create directory quiz-6
````
mkdir quiz-6
````

#### 2. Create directory group_vars on quiz-6 file
````
cd quiz-6
mkdir group_vars
````

#### 3. In the group_vars folder, create 2 new files with the requirement
a. Create the first file named managed1-host-<username>.yml. This file needs to contain a list of user ops from user ops1 until ops50 (ops[1-50]) and list of user dev from user dev1 until dev50 (dev[1-50]). Name the variable for list of user ops with 'list_user_ops_managed1' and for user dev with 'list_user_dev_managed1'.
b. Create the second file named managed2-host-<username>.yml. This file needs to contain a list of user ops from user ops51 until ops100 (ops[51-100]) and list of user dev from user dev51 until dev100 (dev[51-100]). Name the variable for list of user ops with 'list_user_ops_managed2' and for user dev with 'list_user_dev_managed2'.
````
vi managed1-host-abdul7.yml
list_user_ops_managed1:
ops{{ item }}
#set item with ops1 until ops50


list_user_dev_managed1:
ops{{ item }}
#set item with dev1 until dev50

vi managed2-host-abdul7.yml
list_user_ops_managed2:
ops{{ item }}
#set item with ops51 until ops100


list_user_dev_managed2:
ops{{ item }}
#set item with dev51 until dev100

````

#### 4. In the quiz-6 folder, create inventory file. Value of the section in this file needs to be same with the file name in the group_vars directory
````
vim inventory

[managed1-host-abdul7]
pod-abdul7-managed1

[managed2-host-abdul7]
pod-abdul7-managed2
````

#### 5. In the quiz-6 folder, create a file named secret.yml and store the variable pass with adinusa88 as the value. 

````
echo pass:adinusa88 > secret.yml
````

#### 6. The file needs to be encrypted by using ansible-vault, and use belajaransible as the encryption password. Store the encryption password in the vault-pass file and change the permission file with 600
````
ansible-vault encrypt secret.yml

chmod 600 secret.yml
````

#### 7. In the quiz-6 folder, create playbook with below tasks:
a. Create user with username dev[1-50] dan ops[1-50] in pod-username-managed1
b. Create user with username dev[51-100] dan ops[51-100] in pod-username-managed2
c. Each user has password "adinusa88"
d. Name the file of playbook with quiz-6_loop.yml

````
vim quiz-6-loop.yml

---
- name: Create users on managed hosts
  hosts: all
  become: yes
  vars_files:
    - secret.yml

  tasks:
    - name: Create ops users on managed1
      user:
        name: "{{ item }}"
        password: "{{ pass | password_hash('sha512') }}"
      loop: "{{ list_user_ops_managed1 }}"
      when: inventory_hostname == 'pod-abdul7-managed1'

    - name: Create dev users on managed1
      user:
        name: "{{ item }}"
        password: "{{ pass | password_hash('sha512') }}"
      loop: "{{ list_user_dev_managed1 }}"
      when: inventory_hostname == 'pod-abdul7-managed1'


    - name: Create ops users on managed2
      user:
        name: "{{ item }}"
        password: "{{ pass | password_hash('sha512') }}"
      loop: "{{ list_user_ops_managed2 }}"
      when: inventory_hostname == 'pod-abdul7-managed2'

    - name: Create dev users on managed2
      user:
        name: "{{ item }}"
        password: "{{ pass | password_hash('sha512') }}"
      loop: "{{ list_user_dev_managed2 }}"
      when: inventory_hostname == 'pod-abdul7-managed2'
````
