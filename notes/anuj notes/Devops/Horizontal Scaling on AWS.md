- **Buzzwords:**
	- Images (AMI): Snapshots of machines from which we can create more machines.
	- **Load Balancer:** An entrypoint that users send requests to that forwards it to one of many machines (or more specifically, to a target group).
	- **Target Groups:** A group of EC2 instances that a load balancer can send requests to
	- **Launch Template:** A template that can be used to start new machines
---
- **Steps to make your server horizontally scalable:**
1. Create an EC2 instance (Install everything required and clone codebase)
2. Create an AMI (image) using your instance
3. Create security group for your ASG
4. Create a launch template
	1. Add initialising script in user data, e.g.
	```bash
	#!/bin/bash 
	export PATH=$PATH:/home/ubuntu/.nvm/versions/node/v22.0.0/bin/
	# you can also pull repo code for latest code to before starting the server
	npm install -g pm2
	cd /home/ubuntu/week-22
	pm2 start index.js
	```
5. Create an ASG (generally a single availability zone will be okay because ASGs try to balance instances in each zone and hence if multiple are chosen, your instances might randomly shut down and start)
6. Load Balancer: Add an HTTPS listener for your domain and request a certificate from ACM
7. Target Group: Attach the target group to ASG
---
**For autoscaling:**
- You can create a `dynamic autoscaling policy`
	![[Dynamic Scaling Policy.png]]
- You can set Min & Max servers on the ASG
---
**For Cleanup:**
Delete all the following things, one by one:
- ASG
- Target Group
- Load Balancer
- Launch Template
- Image
- Instance that the image was created from