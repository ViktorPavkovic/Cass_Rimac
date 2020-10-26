 # Cass_Rimac
 Hello and welcome to my project.
 
 The initial idea was to start from single EC2 machine (ansible) and create everything else. Since up until now I've worked with playbooks mostly, I started writing everything in one and see how far that can get me. At some point during this I've moved ansible within the cass-vpc and used it as bastion.
 
 ##### The initial plan:
 - Create VPC (w/out IG) and set up VPC peering with ansible
 - Configure subnet and security group
 - Launch EC2 instances from pre-setup AMI with varying environments
 - Update host file with new node(s)
 - Update Cassandra configuration and restart service
 
 Since Cassandra can't have public access, I've decided to configure AMI with Cassandra, Python and Java installed.
 Everything except Cassandra configuration was tested and worked from single playbook. At this point I decided to split it into tasks in single role. Sadly, I've ran out of time and this remains incomplete and untested.

&nbsp;
 ***
&nbsp;

 #### Prerequisites
 Requires Ansible (written in 2.9) with amazon.aws collection.
 
 #### Network topology
 
    VPC: 10.10.0.0/16
	    Subnet 1 (cassandra):   10.10.0.0/24
		Node1	10.10.0.x
	    	Node2	10.10.0.x
	    	...
	    Subnet 2 (ansible):     10.10.1.0/24
	    	Ansible	10.10.1.x

&nbsp;
 ***
&nbsp;

 #### Using the code
 
 The entire thing still needs main playbook, but it's written in mind that you'll be calling it with
 `ansible-playbook cassCluster.yml --extra-vars "ec2prod_count=x ec2dev_count=y" --vault-password-file /path/to/file`
 
 &nbsp;
 
 Thank you for reading! :)
