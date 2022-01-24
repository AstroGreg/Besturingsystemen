# LABO 4: Ansible

[uitleg](https://git.uclllabs.be/bs2/labos/Ansible)

## Installatie ansible

```bash
ssh bs2
sudo apt install ansible sshpass -y
```

## Verkenning van de labo-omgeving

```bash
git clone https://github.com/Tiebevn/BS2_Labo4.git
cd BS2_Labo4/
./cleanup.sh 
./setup.sh 
cat hosts
ansible all -m ping
```

## Inventory management

```bash
nano hosts
	all:
		children:
			webservers:
				hosts:
					172.16.23.10:
					172.16.23.11:
			databases:
				hosts:
					172.16.23.12:
					172.16.23.13:
		hosts:
			172.16.23.14:
			172.16.23.15:
		vars:
			ansible_connection: ssh
			ansible_user: root
			ansible_pass: kroepoek
```

```bash
ansible databases -m ping
```

## Ons eerste playbook

```yaml
nano my_first_playbook.yml
   - hosts: webservers
     tasks:
      - name: Create a file
        command: touch /root/hello.txt
      
      - name: Show the directory content
        command: ls /root
```

```bash
ansible-playbook my_first_playbook.yml
```

```yaml
nano second_playbook.yml
   - hosts: webservers
     tasks:
      - name: Create a file
        file: /root/mysecondfile.txt
        state: touch
```

```bash
ansible-playbook second_playbook.yml
```

inloggen met ssh:

```bash
ssh root@172.16.23.10
```

```yaml
nano third_playbook.yml
   - hosts: webservers
     tasks:
       - name: Install Nginx
         apt: name=nginx state=latest

       - name: start nginx
         service:
           name: nginx
           state: started

       - name: copy custom index-files
         copy:
           src: ./index.html
           dest: /var/www/html/index.html
```

```bash
ansible-playbook third_playbook.yml
```

```yaml
nano mysql_playbook.yml
   - hosts: webservers
     tasks:
      - name: Install MySQL
        apt: name=mysql-server state=latest

      - name: start mysql
        service:
          name: mysql
          state: started
```

```bash
ansible-playbook mysql_playbook.yml
```

## Playbooks: the advanced stuff

### Variabelen

```yaml
nano var_playbook.yml
   - hosts: webservers
 
     vars:
      - usergroup: www-data
      - users:
         - admin
         - user1
         - user2

  tasks:
    - name: Create user
      user:
        name: "{{ item }}"
        state: present
        groups: "{{ usergroup }}"
      loop: "{{users}}"
```

```bash
ansible-playbook var_playbook.yml
```

### Oefening 1

```yaml
nano oef1_playbook.yml
   - hosts: webservers
 
     vars:
      - files:
         - passwords.txt
         - schattigekatjes.jpg
         - cosci_prices.xlsx
 
     tasks:
     - name: Create file
       file:
         path: ./{{ item }}
         state: touch
         mode: 660
         owner: root
       loop: "{{files}}"
```

```bash
ansible-playbook oef1_playbook.yml
```

### Oefening 2

```yaml
nano oef2_playbook.yml
   - hosts: webservers

     tasks:
      - name: Count files in backups
        shell:
          cmd: ls | wc -l
          chdir: /root
        register: number_files

      - name: delete if more than 5
        shell:
          cmd: ls -t | xargs rm
          chdir: /root 
        when: number_files.stdout > 5 == true
```

```bash
ansible-playbook oef2_playbook.yml
```
