#### 1. Create Project Directory

```
mkdir quiz-4
cd quiz-4
```

#### 2. Create Inventory and Define Managed Hosts
Buat file inventory di dalam direktori quiz-4 dengan isi sebagai berikut:

```
[managed]
pod-username-managed1
pod-username-managed2
```

#### 3. Create Roles Directory
```
mkdir -p roles/quiz-roles/tasks
mkdir -p roles/quiz-roles/handlers
mkdir -p roles/quiz-roles/files/html-quiz
mkdir -p roles/quiz-roles/templates
```

#### 4. Edit tasks/main.yml
Buat file roles/quiz-roles/tasks/main.yml dengan isi sebagai berikut:

```
---
- name: Install apache2 package
  apt:
    name: apache2
    state: present

- name: Ensure apache2 service is started and enabled
  service:
    name: apache2
    state: started
    enabled: yes

- name: Ensure quiz-roles.conf.j2 is installed
  template:
    src: quiz-roles.conf.j2
    dest: /etc/apache2/sites-available/quiz-roles.conf
  notify: restart apache2

- name: Enable webserver configuration
  command: a2ensite quiz-roles.conf

- name: Disable default webserver configuration
  command: a2dissite 000-default.conf
  notify:
  - restart apache2

- name: Copy HTML files
  copy:
    src: html-quiz/
    dest: /var/www/quiz-roles/{{ ansible_hostname }}
```

#### 5. Edit handlers/main.yml
Buat file roles/quiz-roles/handlers/main.yml dengan isi sebagai berikut:

```
---
- name: restart apache2
  service:
    name: apache2
    state: restarted
```

#### 6. Create HTML File
Buat file roles/quiz-roles/files/html-quiz/index.html dengan isi sebagai berikut:

```
adinusa lab quiz roles - username
```

#### 7. Create Webserver Configuration Template
Buat file roles/quiz-roles/templates/quiz-roles.conf.j2 dengan isi sebagai berikut:

```
<VirtualHost *:80>
    ServerName quiz-roles.username-adinusa
    DocumentRoot /var/www/quiz-roles/{{ ansible_hostname }}

    <Directory /var/www/quiz-roles/{{ ansible_hostname }}>
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

#### 8. Write Playbook
Buat file quiz-4_roles.yml dengan isi sebagai berikut:

```
---
- hosts: managed
  roles:
    - quiz-roles
```

#### 9. Run the Playbook
```
ansible-playbook -i inventory quiz-4_roles.yml
```

