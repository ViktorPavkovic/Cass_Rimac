 # Cass_Rimac
 Hello and welcome to my project. It’s purpose is automated creation and scaling of Cassandra cluster with Ansible.

 The initial idea was to start from single EC2 machine (ansible) and create everything else. Since up until now I've worked with playbooks mostly, I started writing everything in one and see how far that can get me. At some point during this I've moved ansible within the cass-vpc and used it as bastion.
 
 #### The initial plan:

 When starting the project, I had the following steps in plan:
 - Create VPC (w/out IG) and set up VPC peering with ansible
 - Configure subnet and security group
 - Launch EC2 instances from pre-setup AMI with varying environments
 - Update host file with new node(s)
 - Update Cassandra configuration and restart service
 - Deal with credentials securely
 
 Since Cassandra can't have public access, I've decided to configure AMI with Cassandra, Python and Java installed.
 Everything except Cassandra configuration was tested and worked from single playbook. At this point I decided to split it into tasks in single role. Sadly, I've ran out of time and this remains incomplete and untested.

 ***
 &nbsp;

 #### Prerequisites and using the code
 
 - Requires Ansible (written in 2.9) with amazon.aws collection.
 - It's **strongly** recommended to encrypt ./cassCluster/vars/secret.yml after writing your keys.
 - Only supported region is *eu-west-1* as I've published Cassandra AMI (ami-0eaa60c13746ed0d1) there.
 - Network is hardcoded into tasks (./cassCluster/tasks/getVPC.yml) and should be edited if 10.10.1.0/16 range isn't available.
 
 The entire thing still needs main playbook, but it's written in mind that you'll be calling it with

 `ansible-playbook cassandraCluster.yml --extra-vars "ec2prod_count=x ec2dev_count=y" --vault-password-file /path/to/file`

 #### Network topology
 
    VPC: 10.10.0.0/16
	    Subnet 1 (cassandra):   10.10.0.0/24
            Node1	10.10.0.x
            Node2	10.10.0.x
	    	...
	    Subnet 2 (management):     10.10.1.0/24
	    	Ansible	10.10.1.x

 ***
 &nbsp;

 Thank you for your time! :)
