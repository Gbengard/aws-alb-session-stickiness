# ALB Session Stickiness Demo

This project provides a demonstration of ALB session stickiness using an Application Load Balancer (ALB) managed session cookie called `AWSALB`. The demo consists of several key steps:

## Key Steps:

1. Provision the environment using the 1-click deployment provided below.
2. Verify the functionality of all EC2 instances.
3. Access the load balancer using the `DNS Name` and verify that connections are evenly distributed across targets in the ALB Target group.
4. Enable session stickiness.
5. Verify that your session is locked to one specific instance.
6. Explore the session cookie (`AWSALB`).
7. Shutdown the instance you are locked to and confirm that your session moves to another instance.
8. Disable stickiness and confirm that your connections are distributed evenly again.
9. Perform cleanup to remove the resources.

## Demo Steps:

### Step 1A: Login to an AWS Account

Login to your AWS account and select the `N. Virginia // us-east-1 region`.

### Step 1B: Apply CloudFormation (CFN) Stack

Applying the CloudFormation template will create the necessary resources for the demo, including the DEMO VPC, 3 Public Subnets, an Auto Scaling Group (ASG), and Launch Template (LT). The ALB created by the stack will not have session stickiness enabled.

1. Click [here](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/quickcreate?templateURL=https://techidence.s3.amazonaws.com/aws_projects/alb_stickiness/ALBStickiness.yaml&stackName=ALB) to access the 1-click deployment.
2. Check the box for `capabilities: [AWS::IAM::Role]`.
3. Click `Create Stack`.
4. Wait for the stack to reach the `CREATE_COMPLETE` state before proceeding.

### Step 1C: Verify the Webserver is Running on Each Instance

1. Go to the EC2 Console by clicking [here](https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Home:).
2. Click `Instances`.
3. Select each instance one by one.
4. Copy the `Public DNS (IPv4)` of each instance and open it in a new tab.
5. Repeat the above steps for all 6 running instances.
   - Each instance should display a webpage with a random colored background, an instance ID, and a random cat picture.

### Step 1D: Access the Load Balancer Using the `DNS Name`

1. Go to the EC2 Console by clicking [here](https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#LoadBalancers:).
2. Select the `ALB-ALB-RANDOM` load balancer.
3. On the `Details` tab, copy the `DNS name` and open it in a new tab.
4. The website should load one of the 6 instances you previously opened manually.
5. Refresh the page multiple times to see it cycling between the different pages and cat images.
   - Initially, it may only cycle between 3 instances, but eventually, it should move between all 6.
   - Note: Session stickiness is not enabled, so sessions are being distributed across all targets in the ALB target group.

### Step 1E: Enable Session Stickiness

1. Go to the EC2 Console by clicking [here](https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#TargetGroups:).
2. Click the `ALB-ALBTG-RANDOM` load balancer target group.
3. Ensure the `Group Details` tab is selected.
4. Scroll down to `Attributes` and click `Edit`.
5. Check the `Stickiness` box, set the `Duration` to `1 minute`, and click `Save changes`.
   - Session stickiness is now enabled.

### Step 1F: Verify that Your Session is Locked to One Instance and Explore the Session Cookie

1. Go back to the tab with the load balancer webpage.
2. Refresh the page a few times.
3. After a few refreshes, your session will be locked to a specific backend instance.
4. If you are using Firefox, you can click `Tools` -> `Web Developer` -> `Storage Inspector` to see the `AWSALB` cookie, which is responsible for locking your session to this backend instance.
   - You will remain locked to this instance until either the cookie expires or the instance fails its health check.

### Step 1G: Shutdown the Instance and Confirm You Are Locked to Another Instance

1. Note down the instance ID of the locked instance displayed above the cat gif on the page.
2. Go back to the EC2 Console by clicking [here](https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Instances:sort=instanceState).
3. Locate the instance with the noted instance ID and select it.
4. Right-click `Instance State` and then click `Stop`. Confirm the action by clicking `Yes, Stop`.
5. Return to the load balancer tab.
6. You might see a `504 Gateway TimeOut` Error, but keep clicking refresh.
7. After a while, you should move to a new instance.
8. Click refresh again and repeat a few more times.
9. Go back to the EC2 Console.
10. Locate the stopped instance.
11. Right-click `Instance State` and then click `Start`. Confirm the action by clicking `Yes, Start`.
12. Return to the load balancer tab.
13. Keep refreshing.
14. You are still locked to the new instance and will not return to the old one.

### Step 1H: Disable Stickiness and Confirm Sessions Are Distributed Again

1. Go to the EC2 Console by clicking [here](https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#TargetGroups:).
2. Click the `ALB-ALBTG-RANDOM` load balancer target group.
3. Ensure the `Group Details` tab is selected.
4. Scroll down to `Attributes` and click `Edit`.
5. Uncheck the `Stickiness` box and click `Save changes`.
6. Go back to the load balancer tab.
7. Keep refreshing.
8. You should now start moving freely between all instances.

### Step 1I: Delete the CloudFormation Template and Cleanup

1. Go back to the CloudFormation Console by clicking [here](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks?filteringText=&filteringStatus=active&viewNested=true&hideStacks=false).
2. Check the box next to the `ALB` stack.
3. Click `Delete` and then click `Delete stack`.

Congratulations! You have completed this demo lesson.
