CC Practical Steps
Practical 1 - /**
* A simple example showing how to create a datacenter with one host and run
one
* cloudlet on it.
*/
public static void main(String[] args) {

		Log.printLine("Starting CloudSimExample1...");

		try {
			// First step: Initialize the CloudSim package.
			int num_user = 1; 
			Calendar calendar = Calendar.getInstance();
			boolean trace_flag = false; 

			CloudSim.init(num_user, calendar, trace_flag);

			// Second step: Create Datacenters
			
			Datacenter datacenter0 = createDatacenter("Datacenter_0");

			// Third step: Create Broker
			DatacenterBroker broker = createBroker();
			int brokerId = broker.getId();

			// Fourth step: Create one virtual machine
			vmlist = new ArrayList<Vm>();

			// VM description
			int vmid = 0;
			int mips = 1000;
			long size = 10000; // image size (MB)
			int ram = 512; // vm memory (MB)
			long bw = 1000;
			int pesNumber = 1; // number of cpus
			String vmm = "Xen"; // VMM name

			// create VM
			Vm vm = new Vm(vmid, brokerId, mips, pesNumber, ram, bw, size, vmm, new CloudletSchedulerTimeShared());
			vmlist.add(vm);
			broker.submitVmList(vmlist);

			// Fifth step: Create one Cloudlet
			cloudletList = new ArrayList<Cloudlet>();

			// Cloudlet properties
			int id = 0;
			long length = 400000;
			long fileSize = 300;
			long outputSize = 300;
			UtilizationModel utilizationModel = new UtilizationModelFull();

			Cloudlet cloudlet = new Cloudlet(id, length, pesNumber, fileSize, outputSize, utilizationModel, utilizationModel, utilizationModel);
			cloudlet.setUserId(brokerId);
			cloudlet.setVmId(vmid);

			cloudletList.add(cloudlet);

			broker.submitCloudletList(cloudletList);

			// Sixth step: Starts the simulation
			CloudSim.startSimulation();
			CloudSim.stopSimulation();

			//Final step: Print results when simulation is over
			List<Cloudlet> newList = broker.getCloudletReceivedList();
			printCloudletList(newList);

			Log.printLine("CloudSimExample1 finished!");
		} catch (Exception e) {
			e.printStackTrace();
			Log.printLine("Unwanted errors happen");
		}
	}

-------------------------------------------------------------------------------------------------------------

Practical 2 - An example showing how to create scalable simulation
CloudSimExample6
public static void main(String[] args) {
		Log.printLine("Starting CloudSimExample6...");

		try {
			// First step: Initialize the CloudSim package. 
			int num_user = 1;   // number of grid users
			Calendar calendar = Calendar.getInstance();
			boolean trace_flag = false; 

			CloudSim.init(num_user, calendar, trace_flag);

			// Second step: Create Datacenters
			@SuppressWarnings("unused")
			Datacenter datacenter0 = createDatacenter("Datacenter_0");
			@SuppressWarnings("unused")
			Datacenter datacenter1 = createDatacenter("Datacenter_1");

			//Third step: Create Broker
			DatacenterBroker broker = createBroker();
			int brokerId = broker.getId();

			//Fourth step: Create VMs and Cloudlets and send them to broker
			vmlist = createVM(brokerId,20); //creating 20 vms
			cloudletList = createCloudlet(brokerId,40); // creating 40 cloudlets

			broker.submitVmList(vmlist);
			broker.submitCloudletList(cloudletList);

			// Fifth step: Starts the simulation
			CloudSim.startSimulation();

			// Final step: Print results when simulation is over
			List<Cloudlet> newList = broker.getCloudletReceivedList();

			CloudSim.stopSimulation();
			printCloudletList(newList);
			Log.printLine("CloudSimExample6 finished!");
		}
		catch (Exception e)
		{
			e.printStackTrace();
			Log.printLine("The simulation has been terminated due to an unexpected error");
		}
	}


-------------------------------------------------------------------------------------------------------------


Practical 3 - NetworkExample2
/**
* A simple example showing how to create
* two datacenters with one host and a
* network topology each and run two cloudlets
* on them.
*/
public static void main(String[] args) {

		Log.printLine("Starting NetworkExample2...");

		try {
			// First step: Initialize the CloudSim package. 
			int num_user = 1;   // number of cloud users
			Calendar calendar = Calendar.getInstance();
			boolean trace_flag = false; 

			CloudSim.init(num_user, calendar, trace_flag);

			// Second step: Create Datacenters
			//Datacenters are the resource providers in CloudSim.
			Datacenter datacenter0 = createDatacenter("Datacenter_0");
			Datacenter datacenter1 = createDatacenter("Datacenter_1");

			//Third step: Create Broker
			DatacenterBroker broker = createBroker();
			int brokerId = broker.getId();

			//Fourth step: Create one virtual machine
			vmlist = new ArrayList<Vm>();

			//VM description
			int vmid = 0;
			int mips = 250;
			long size = 10000; //image size (MB)
			int ram = 512; //vm memory (MB)
			long bw = 1000;
			int pesNumber = 1; //number of cpus
			String vmm = "Xen"; //VMM name

			//create two VMs
			Vm vm1 = new Vm(vmid, brokerId, mips, pesNumber, ram, bw, size, vmm, new CloudletSchedulerTimeShared());
			vmid++;
			Vm vm2 = new Vm(vmid, brokerId, mips, pesNumber, ram, bw, size, vmm, new CloudletSchedulerTimeShared());

			//add the VMs to the vmList
			vmlist.add(vm1);
			vmlist.add(vm2);

			//submit vm list to the broker
			broker.submitVmList(vmlist);


			//Fifth step: Create two Cloudlets
			cloudletList = new ArrayList<Cloudlet>();

			//Cloudlet properties
			int id = 0;
			long length = 40000;
			long fileSize = 300;
			long outputSize = 300;
			UtilizationModel utilizationModel = new UtilizationModelFull();

			Cloudlet cloudlet1 = new Cloudlet(id, length, pesNumber, fileSize, outputSize, utilizationModel, utilizationModel, utilizationModel);
			cloudlet1.setUserId(brokerId);

			id++;
			Cloudlet cloudlet2 = new Cloudlet(id, length, pesNumber, fileSize, outputSize, utilizationModel, utilizationModel, utilizationModel);
			cloudlet2.setUserId(brokerId);

			//add the cloudlets to the list
			cloudletList.add(cloudlet1);
			cloudletList.add(cloudlet2);

			broker.submitCloudletList(cloudletList);
			broker.bindCloudletToVm(cloudlet1.getCloudletId(),vm1.getId());
			broker.bindCloudletToVm(cloudlet2.getCloudletId(),vm2.getId());


			//Sixth step: configure network
			//load the network topology file
			NetworkTopology.buildNetworkTopology("topology.brite");

			//Datacenter0 will correspond to BRITE node 0
			int briteNode=0;
			NetworkTopology.mapNode(datacenter0.getId(),briteNode);

			//Datacenter1 will correspond to BRITE node 2
			briteNode=2;
			NetworkTopology.mapNode(datacenter1.getId(),briteNode);

			//Broker will correspond to BRITE node 3
			briteNode=3;
			NetworkTopology.mapNode(broker.getId(),briteNode);


			// Sixth step: Starts the simulation
			CloudSim.startSimulation();


			// Final step: Print results when simulation is over
			List<Cloudlet> newList = broker.getCloudletReceivedList();

			CloudSim.stopSimulation();

			printCloudletList(newList);

			Log.printLine("NetworkExample2 finished!");
		}
		catch (Exception e) {
			e.printStackTrace();
			Log.printLine("The simulation has been terminated due to an unexpected error");
		}
	}


-------------------------------------------------------------------------------------------------------------



Practical 4 - NetworkExample3
 * A simple example showing how to create
 * a datacenter with one host and a network
 * topology and and run one cloudlet on it.
 * Here, instead of using a BRIE file describing
 * the links, links are inserted in the code.
 */

public static void main(String[] args) {

		Log.printLine("Starting NetworkExample3...");

		try {
			// First step: Initialize the CloudSim package.
			int num_user = 2;   // number of cloud users
			Calendar calendar = Calendar.getInstance();
			boolean trace_flag = false;  // mean trace events

			// Initialize the CloudSim library
			CloudSim.init(num_user, calendar, trace_flag);

			// Second step: Create Datacenters
			Datacenter datacenter0 = createDatacenter("Datacenter_0");
			Datacenter datacenter1 = createDatacenter("Datacenter_1");

			//Third step: Create Brokers
			DatacenterBroker broker1 = createBroker(1);
			int brokerId1 = broker1.getId();

			DatacenterBroker broker2 = createBroker(2);
			int brokerId2 = broker2.getId();

			//Fourth step: Create one virtual machine for each broker/user
			vmlist1 = new ArrayList<Vm>();
			vmlist2 = new ArrayList<Vm>();

			//VM description
			int vmid = 0;
			long size = 10000; //image size (MB)
			int mips = 250;
			int ram = 512; //vm memory (MB)
			long bw = 1000;
			int pesNumber = 1; //number of cpus
			String vmm = "Xen"; //VMM name

			//create two VMs: the first one belongs to user1
			Vm vm1 = new Vm(vmid, brokerId1, mips, pesNumber, ram, bw, size, vmm, new CloudletSchedulerTimeShared());

			//the second VM: this one belongs to user2
			Vm vm2 = new Vm(vmid, brokerId2, mips, pesNumber, ram, bw, size, vmm, new CloudletSchedulerTimeShared());

			//add the VMs to the vmlists
			vmlist1.add(vm1);
			vmlist2.add(vm2);

			//submit vm list to the broker
			broker1.submitVmList(vmlist1);
			broker2.submitVmList(vmlist2);

			//Fifth step: Create two Cloudlets
			cloudletList1 = new ArrayList<Cloudlet>();
			cloudletList2 = new ArrayList<Cloudlet>();

			//Cloudlet properties
			int id = 0;
			long length = 40000;
			long fileSize = 300;
			long outputSize = 300;
			UtilizationModel utilizationModel = new UtilizationModelFull();

			Cloudlet cloudlet1 = new Cloudlet(id, length, pesNumber, fileSize, outputSize, utilizationModel, utilizationModel, utilizationModel);
			cloudlet1.setUserId(brokerId1);

			Cloudlet cloudlet2 = new Cloudlet(id, length, pesNumber, fileSize, outputSize, utilizationModel, utilizationModel, utilizationModel);
			cloudlet2.setUserId(brokerId2);
			cloudletList1.add(cloudlet1);
			cloudletList2.add(cloudlet2);

			//submit cloudlet list to the brokers
			broker1.submitCloudletList(cloudletList1);
			broker2.submitCloudletList(cloudletList2);


			//Sixth step: configure network
			//load the network topology file
			NetworkTopology.buildNetworkTopology("topology.brite");

			//Datacenter0 will correspond to BRITE node 0
			int briteNode=0;
			NetworkTopology.mapNode(datacenter0.getId(),briteNode);

			//Datacenter1 will correspond to BRITE node 2
			briteNode=2;
			NetworkTopology.mapNode(datacenter1.getId(),briteNode);

			//Broker1 will correspond to BRITE node 3
			briteNode=3;
			NetworkTopology.mapNode(broker1.getId(),briteNode);

			//Broker2 will correspond to BRITE node 4
			briteNode=4;
			NetworkTopology.mapNode(broker2.getId(),briteNode);

			// Sixth step: Starts the simulation
			CloudSim.startSimulation();

			// Final step: Print results when simulation is over
			List<Cloudlet> newList1 = broker1.getCloudletReceivedList();
			List<Cloudlet> newList2 = broker2.getCloudletReceivedList();

			CloudSim.stopSimulation();

			Log.print("=============> User "+brokerId1+"    ");
			printCloudletList(newList1);

			Log.print("=============> User "+brokerId2+"    ");
			printCloudletList(newList2);

			Log.printLine("NetworkExample3 finished!");
		}
		catch (Exception e) {
			e.printStackTrace();
			Log.printLine("The simulation has been terminated due to an unexpected error");
		}
	}



-------------------------------------------------------------------------------------------------------------



Practical 5 - AWS Operations
EC2 Creation
Go to you AWS Console and search for EC2
Select a region
Click on Instances (Running)
Click on “Launch Instance”
Give a name to your EC2 instance
Now we need to select an Amazon Machine Image (AMI). Search for Ubuntu
Select Ubuntu Server 22.04 LTS (HVM), SSD Volume Type
Select the default instance type: t2.micro that provides 1 Virtual CPU and 1 GB Memory
Create a SSH key pair so that only the individual that has this key can access the instance from his end. Download and store this key in a safe location as we will use it to connect to our EC2 instance later.
Choose the default network settings. Allow SSH traffic from 0.0.0.0/0 (Anywhere) means that we can access this instance from anywhere. But in production we should configure this to the IP of that machine which will be authorized to access that instance.
Then configure your storage, this will be like your C Drive.
Finally, click on Launch Instance.





EC2 Client Connection
Go to the instance we created.
Click on Connect.
Go to SSH Client and copy the command
ssh -i "ammar.pem" ubuntu@ec2-13-201-78-164.ap-south-1.compute.amazonaws.com
Go to the location where this pem key is installed and open that location in CMD, paste this command over there 
Let’s then run some commands and verify the configurations we selected.
Check Platform Information
cat /etc/os-release
Check Memory
free -m
lscpu
Check Storage
df -h
Check IP Address
ip a


S3 Bucket Creation
Go to your AWS console and search for S3.
Click on Create Bucket.
Give a name to your bucket.
Choose US East Virginia as the AWS region.
Choose all the default settings and click create bucket.
Our bucket is created.
S3 Bucket Static Website Hosting
Create a simple index.html file
Go to the bucket that you created
Click on upload and upload this index HTML file over there
Now click on the uploaded html file and you’ll be able to see an associated Object URL where this file is hosted.
If you click on it, you’ll be redirected to that URL but the HTML file won’t be displayed.
It’s happening because of the security implemented by AWS. Here is what we need to do to fix it
Go to the properties tab of the bucket, enable static website hosting and in the index document text field enter ‘index.html’
Go to permissions tab, over there uncheck ‘Block Public Access’
In permissions tab, select ‘Object Ownership’ and enable ACLs (Access Control Lists)
Go inside the bucket, select all files (in our case we only have one index.html file) and from the actions dropdown select ‘Make public using ACL’
Now the website will be accessible



-------------------------------------------------------------------------------------------------------------



Practical 7 - To perform various GIT operations on Local and Remote Repository
Creating user and editing
git config --global user.name “Ammar”
git config --global user.email “engineer.ammar18724@gmail.com”
git config --global --edit

Enter a directory and and make a new folder using ‘mkdir newFolder’
git init (To initialise empty git repository)

Create a new file in the directory using the GUI and add it to git
git add Sum.java

Commit the file to git 
git commit -m “Commit Message”

Check status and log
git status
git log

If you modify a file, add it to git and commit it again.

If you add/modify multiple files and want to add them together
git add .

If you want to switch to a previous commit, get the commit ID using git log and then 
git checkout commitID

Master commit
git checkout master

Create new branches 
git branch nameOfBranch
git checkout -b nameOfBranch (Creates and switches simultaneously)

To switch to that branch.
git checkout new_branch 

List branches
git branch

To merge files from one branch to another
git merge nameOfBranch

Create a new gitignore file and add a file you don’t want to be seen to it
touch .gitignore
git status
Add the name of the file you don’t want to be seen to the .gitignore file
git status

Create a GitHub account and upload the files on it
git remote add origin https://github.com/ammare03/nameOfRepository
git branch -M master
git push -u origin master
Check your github account and your files should be visible on it

If you wish to put a different branch
git checkout dev
git push -u origin dev

To clone the repository to a local directory
git clone pathOfRepository

Create a new branch add a new file to it and push this branch to GitHub
git checkout -b anything
git add Sum.java
git commit -m “New Commit”
git push -u origin anything

Now we will Pull Request for a specific branch
Click on Compare & Pull Request on the GitHub website
Click on Create Pull Request
Move to the main page and click on pull request
Click on closed to see the pull request
Merge the pull request and then delete the branch.
Practical 8 - Configuring and building a job in Jenkins.
Go to the browser and go to localhost:8080 to login to Jenkins.
Enter the new project’s name in the Enter an Item Name field and select the Freestyle project type. Click OK to continue.
Under the General Tab, add a project description in the Description field.
Add a build step
Scroll down to the Build section
Open the Add Build step drop-down menu and select Execute Windows batch command
Enter the command you want to execute in the Command field. For this tutorial, we are using a simple set of commands that display the current version of Java and Jenkins working directory:
java –version
dir
Click the Save button to save the changes to the project.
Click on the Build Now link on the left hand side of the new project page
Click on the link to the latest project build in the Build History section.
Click on the Console Output link on the left hand side of the screen to display the output for the commands you entered
The console output indicates that Jenkins is successfully executing the commands, displaying the current version of Java and Jenkins working directory


-------------------------------------------------------------------------------------------------------------


Practical 9 - To create a CI/CD Pipeline in Jenkins
Go to your Jenkins Portal
Click on ‘New Item’ on the left side of the screen
Name the Pipeline
Select the Job type and click OK. A configuration related to the pipeline opens on the screen. Set the Definition as ‘Pipeline Script’ and scroll down and in the dialog box of ‘Script’ select ‘GitHub + Maven’
The next step is to integrate the Jenkins file into the Version Control System. To do that you must:
Select ‘Pipeline Script for SCM’
Then in the SCM dialog box, select Git.
Under ScriptPath write ‘Jenkinsfile’
Add the Git repository URL. You can add the credentials if any. After this save the configuration.
A page now appears on the screen that gives you various options like ‘Build Now’, ‘Pipeline’, ‘Configure’ etc. Click on the ‘Build Now’ option.



-------------------------------------------------------------------------------------------------------------



Practical 11 - To understand Jenkins Master-Slave Architecture and scale your standalone implementation by implementing slave nodes.
Click on Manage Jenkins on the Dashboard
Go to Security
Change the Agents -> TCP port for inbound agents to Random
Go back to Manage Jenkins and under System Configuration click on Nodes
There is a master node by default
 Click on New Node
Assign a name to the node and select the Permanent Agent option
Give a meaningful description and set the number of executors to 5
Assign any local directory that you want to run the workspace in as the Remote Root Directory
Assign a meaningful label
Configure the launch method as shown

Click on Save
Open a new CMD in the working directory that we created and run the following commands
curl.exe -sO http://localhost:8080/jnlpJars/agent.jar
java -jar agent.jar -jnlpUrl http://localhost:8080/computer/slaveNode1/jenkins-agent.jnlp -secret 8ac91952c614b477dfdc95aee7469bc02ba22276deebfe09a2c03fd939f6d758 -workDir "C:\Users\user42\Documents\21BIT016\CC\jenkinsWorkspace"
You should see INFO: Connected in the CMD.

Running a Job using the Slave Node
Go to Dashboard and click on New Item
Enter a name and select the freestyle project option
When configuring the job, under General -> Check the Restrict where this project can be run option and write the name of the slave node we created
Write any public GitHub URL
Save the Job
Click on Build Now on the left and check the Console Output



