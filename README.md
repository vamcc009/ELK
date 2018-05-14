# ELK
  These ansible playbooks can be used to automate the set up ELK server as well as it's clients.
  The following playbooks will create an ELK set up with Elasticsearch, Logstash, Kibana and Nginx.
  ```
  server 1 :- Elasticsearch
  server 2 :- Logstash
  server 3: Kibana and Nginx
  ```
  This'll also install java8.
  you need to pass the respective values in the vars file and inventory file.
## ELK server set up

### STEP 1

 Create three ec2 Instances and place their Private IP's in the "inventory.yml" file.
 ```
 [elasticsearch]
 <server1 IP>

 [logstash]
 <server2 IP>

 [kibana]
 <server3 IP>
 ```

### STEP 2

  Now, place the elasticsearch,logstash and kibana IP's in the respective sections in the "vars.yml" file. also choose the username and password for Nginx and pass it in the same file. This username and password will be required while accessing the kibana dashboard.
  ```
  elasticsearch_ip: <server1 IP>
  logstash_private_ip: <server2 IP>
  log_location: syslog
  kibana_private_ip: <server3 IP>
  port: 80
  kibanauser: <username>
  kibanapassword: <password>

  In this set up we're going monitor syslogs. if you want to monitor any other logs then pass that value to "log_location" variable.
  ```

### STEP 3

  Now run run the following ansible command to complete the set up of ELK stack.
  ```
  ansible-playbook -i inventory.yml ELK.yml
  ```
  This will create an ELK stack.

### STEP 4  

  A sample kibana dashboard been configured during the set up. to load the same, login to the kibana server and execute the following commands.
  ```
  ssh ec2-user@<kibana Ip(server 3)>
  cd ~/beats-dashboards-1.1.0
  sh load.sh
  ```
  You can use this sample dashboard or else you can configure your own kibana dashboard as per the need.

### STEP 5
 Now, grab the public IP of the kibana and paste it in the browser. It'll prompt for the username and password. Enter the values which you've defined in the "vars.yml" file in STEP 2.
 You now have an option to choose an Index pattern. select [filebeat] option and set it as default Index by clicking the STAR.

 This completes the ELK set up.

## ELK Clients set up

  Inorder for you to access the logs of other hosts via kibana dashboard you need to configure filebeats in those hosts.

### STEP 1
  Grab the private IP of the host you want to access the logs, and place it in "inventory.yml" file under the client section.
  ```
  [client]
  <client_server_IP>
  ```

### STEP 2
  Inorder for the clients to forward their logs to kibana, they need to communicate with the logstash server of ELK stack.
  So grab the content of "/etc/pki/tls/certs/logstash-forwarder.crt" form the Logstash server(server 2) of ELK stack and place it in "logstash-forwarder.crt" file in the files folder under the client role.

### STEP 3
  Now run the following ansible command to complete the set up of ELK client.
  ```
  ansible-playbook -i inventory.yml elk-client.yml
  ```

  This will configure the client machine to forward it's syslogs to ELK stack and you can access them via Kibana Dashboard.
