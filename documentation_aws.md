# AWS Documentation

Amazon Web Services is an online cloud-computing supplier. We have an account that is hooked up to the lab's card, so the billing is very easy and simple. You can find specifications on the computer specs/pricing at [amazon pricing](https://aws.amazon.com/ec2/pricing/)

## Online Access

Goto [amazon](https://aws.amazon.com/). Hit the Login/Create an AWS account to login, the credentials are those that I send out to the team.

From here you can do many things, you guys will probably be principally wanting to just start/stop computers. To do this, go to EC2 - either on the front page, or Services -> EC2. 
On the `EC2 Dashboard`, there will be many links. Here are the key ones:

- `Running Instances` - these are your computers. This links to the AWS Instance page. 
- `Volumes` - these are your volumes/disk spaces for said computers
- `Key Pairs` - these are your PEM files that allow access to said computers. Mostly you can ignore these.

If you ever want to check your billing just click 'Max Tegmark' in the top right and select 'Billing and Cost Management'.

## Starting machines

We have two principle computers that are used for almost everything:

- `ClusterSorter` - This is a computing heavy unit preloaded with Klusta and Mathematica. 
- `firstAWS`,`secondAWS`,`thirdAWS`,`fifthAWS` - These are GPU units preloaded and configured with torch and char, so they are ready to pump out the neural nets. Don't ask why I skipped `fourthAWS`.

From the AWS Instance page, you start these by simply right clicking on the computer and then selected `Instance State` and then `Start`. It will take a minute or so to spool up, and then you can ssh into it with the associated Key. For all of these, the TeamTegmark.pem (in the Cortex Dropbox folder) is the default pem key. You will need to set this up on your computer, for more details see the [Connecting to Machines](## Connecting to machines) below.

The clustersorter is the only computer with a static IP (aka the Public DNS does not change), so to connect to the other instances, you must copy the `Public DNS` from the Description box at the bottom of the AWS console page. It should look something like `ec2-52-91-129-247.compute-1.amazonaws.com`. You will need this to connect to the machine. 

Once the Status Checks stops saying `Initializing`, the computer is up and running and ready to use!

## Starting new machines

From the AWS Instance page, click on the big blue `Launch Instance` button. The AMI is the operating system. 

### AMI Instructions

The AMI that we use for torch-rnn is `ami-c79b7eac`. You can find more details about the setup of these machines at [this github](https://github.com/brotchie/torch-ubuntu-gpu-ec2-install). 

The AMI that we use for cluster sorting is `ami-8992109e`. Though you can check this/see a list at [the anaconda docs](https://docs.continuum.io/anaconda/amazon-aws). 

The AMI that most closely emulates tor is the 'Ubuntu Server', `ami-2d39803a`. You can always just boot this up and hand install all the packages. My recommendation if you want to ever do something new is to go and see if there is an associated AMI with it pre-configured. This simplifies life **significantly**. 

### Instance Type

This is selecting the hardware of the computer. It is pretty self-explanatory. The important links are to [specifications](https://aws.amazon.com/ec2/instance-types/) and [pricing](https://aws.amazon.com/ec2/pricing/). For general context, the cluster sorter is a compute optimized c4.4xlarge, the torch guys are g2.2x larges (these are actually better than the g2.8x).

### Configure Instance

Make sure the shutdown behaviour is on `Stop` not `Terminate`.

### Add Storage

Add how ever many gigs of hard disk you need. Keep in mind you can move these between instances, but it is a little challenging.

### Tag Instance

Skip it

### Security Group

Make sure to create a new one for each machine, or else the ssh'ing will be significantly more complicated

## Connecting to machines

Following [this](http://nerderati.com/2011/03/17/simplify-your-life-with-an-ssh-config-file/) tutorial, you should set up your config file. For an amazon ec2 instace the settings will be

- Host:  ***insert your choice of host name***
- HostName:  This should be the public DNS of the amazon server (eg ec2-107-23-179-223.compute-1.amazonaws.com)
- User:  ubuntu (or ec2-user, this is the username of the computer, it depends on the OS)
- IdentityFile:  ~/.ssh/TeamTegmark.pem (make sure to copy the Team Tegmark pem file into this folder from the dropbox, and also chmod 400 it).

After this you can simply type `ssh (hostname)`

## Stopping Machines

There are two easy ways to do this

### Option 1: SSH
While sshed into the machine, type 
```bash
sudo poweroff
```
Note that sometimes this bugs and does not completely kill the instance, so it may be a good idea to log in and check

### Option 2: Online
Go online to the console, go to the running instances and simply right click a computer, hit `Instance State`, and then `Stop`. **Do not hit the terminate button.** I have configured all of our computers to not terminate until we tell them to, but it is worth noting that if you start another computer this is **not** the default behaviour. 









