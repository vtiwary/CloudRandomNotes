If we dont specify the Resource name, while creating the resource, CFT will create with random name which will include stack name, logical Id and some random stuff


CFT update types
1. Update with no interruption : add instance profile to EC2
2. Update with some interruption : change isntance types
3. Replacement: create new instance, point the reference to the new one, delete old instance : change the region

While creating the resources we have tell Cloudfomraton to pass to a different role that way that IAM roles creates it if it has the permssion (otherwise if we dont specify it will try to create using our ID)

While create the CFT we can get an estimate of the cost of the infra it will create in the review section before we click on it to create the infra


Parameters:
helps to reuse the template and use values not known aheaed of time
parameters are types, tring, Number, List ,list of numbers ,SSM parameters, description, min-max, allowedValues etc,patter (regex)
Refer parameters with Fn::Ref or !Ref (!Ref IN YAML) .. Ref can't be used in AWSTEMPALTEVERSON, DESCRIPTION, TRANSFORM AND MAPPING

Cloudfomratio : Parameter: NoEcho: True means provided entry will be secret and wont be displayed
NoEcho
Whether to mask the parameter value to prevent it from being displayed in the console, command line tools, or API. If you set the NoEcho attribute to true, CloudFormation returns the parameter value masked as asterisks (*****) for any calls that describe the stack or stack events, except for information stored in the locations specified below.

**** read following***
https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html

If parameter is like
  DbSubnetIpBlocks:
    Description: "Comma-delimited list of three CIDR blocks"
    Type: CommaDelimitedList
    Default: "10.0.48.0/24, 10.0.112.0/24, 10.0.176.0/24"
	
We can select CidrBlock: !Select [0, !Ref DbSubnetIpBlocks]

In SSM, there is a pubic accessibleparameter which returns the latest AMI ID for AMAZON Linux 2, can be used in CFT to fetch the latest value



When fetching something from Public SSM store

Parameters:
  InstanceType:
    Description: WebServer EC2 Instance Type
    Type: AWS::SSM::Parameter::Value<String>
    Default: /dev/ec2/instanceType

  ImageId: 
    Type: AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>
    Default: /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2

Resources:
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: !Ref InstanceType
      ImageId: !Ref ImageId

if we change the /dev/ec2/instanceType to say t2.small and use update stack with the same stack without uploading a new one, since the new one is a differnt, it will change the underlying version