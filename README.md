## Ansible playbook for Cassandra cluster management

### About this project
Hello and welcome to my project. It’s purpose is automated creation and scaling of Cassandra cluster with Ansible.

### Before we start
It is strongly recommended to encrypt `files/secret.yml` with ansible-vault as it contains sensitive data.

Cassandra should not be directly accessed through internet so the playbook does not assign it public IP. For configuration Ansible needs access to instance, so run this playbook on machine with access to desired subnet (such as another EC2 instance with Ansible installed).

By default, playbook creates security group that allows access from anywhere. This is not recommended and should be replaced with your desired IPs in `tasks/manageEC2.yml`.

Playbook creates EC2 key pair and stores it to S3 bucket (bucket name is in key_bucket variable). Either create the bucket or skip the step and provide your key by altering variable key_name.

Outside of builtin Ansible collection, playbook requires community.aws and amazon.aws to also be installed. Run `ansible-galaxy collection install community.aws` and `ansible-galaxy collection install amazon.aws` to set it up.

### How to use
Clone the repository locally, fill and encrypt `files/secret.yml` than run `ansible-playbook main.yml --ask-vault-pass`.

Credentials for Cassandra are stored locally to cass.info, please remember to save them somewhere safe.

### What it does
By default, playbook does following setps:

* Create new key pair Cassandra-Cluster, store it locally (~/.ssh) and in S3 bucket. If key already exists, download it from S3 bucket.

* Create SG-Cassandra security group with default Cassandra ports and SSH accessible from anywhere

* Launch 1x t3.small Amazon Linux 2 EC2 instance

* Install Cassandra 3.11.10 and configure it (copy configuration from cassandra.j2 and enable service)

* When creating a new cluster `files/change_pass.j2` is run which changes credentials for default database user. These credentials are stored locally in cass.info. You can also use this file to run some cqlsh commands (creating keyspace, tables, other users and such). 

### Connecting to database
When instance is up you can connect to it using SSH (.pem key is stored in your ~/.ssh, user is ec2-user) and run `cqlsh -u cassandra -p [password] [private ip]`

Password and IP are in cass.info file.

### Changing number of nodes in cluster
To increase number of nodes simply increase the `ec2_count` variable in main.yml.

Decreasing the number of nodes does not remove them from Cassandra ring and aditional manual steps are required. Either decomission or assassinate nodes using nodetool before or after number of instances has been lowered. When reducing the number of instances Ansible sorts them alphabetically by Instance ID (i-xxx) and deletes the first ones.