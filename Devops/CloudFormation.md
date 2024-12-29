# CloudFormation

- Written in Yaml or JSON.
- Free, pay for the underlying resources
- CloudFormation stacks are isolated from each other
- Deleting a stack deletes every single resource or artifact created by that stack.
- Automatic generation of diagrams as per your code.
- Separate stacks for each application or for network, app stacks etc.
```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: CloudFormation Template to Create an EC2 Instance with a Security Group
Resources:
  IngressSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: "Allow SSH and HTTP access"
      SecurityGroupIngress:
        - IpProtocol: "tcp"
          FromPort: "22"
          ToPort: "22"
          CidrIp: "0.0.0.0/0"  
        - IpProtocol: "tcp"
          FromPort: "80"
          ToPort: "80"
          CidrIp: "0.0.0.0/0" 
      SecurityGroupEgress:
        - IpProtocol: "-1"
          CidrIp: "0.0.0.0/0"  

  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t2.medium
      ImageId: ami-0c55b159cbfafe1f0  
      SecurityGroups:
        - Ref: IngressSecurityGroup
      KeyName: my-key-pair  
      Tags:
        - Key: Name
          Value: MyEC2Instance

Outputs:
  EC2InstancePublicIP:
    Description: "Public IP of the created EC2 instance"
    Value: !GetAtt MyEC2Instance.PublicIp
  EC2InstanceId:
    Description: "Instance ID of the created EC2 instance"
    Value: !Ref MyEC2Instance
  EC2AmiID: 
    Description: "AMI ID Used for the instance"
    Value: !GetAtt MyEC2Instance.AMIID

```
## Templates

- **Templates are uploaded and referenced from S3**. To update a template, we cannot edit the previous version. 
- We need re-upload a new version of the template and CloudFormation will find out what needs to be modified to reach the desired state.
- Direct editing of the older template is not allowed.
- Deploying Templates
    - Manual - editing templates in the **CloudFormation Designer (GUI)**, this is done using the aws console that is why it is manual in nature.
    - Automated - editing templates in a YAML file and using CLI to deploy the templates

## Stacks

- A stack is a collection of AWS resources that you can manage as a single unit
- All the resources in a stack are defined by the stack's CloudFormation template
- Stack options:
    - Tags: key/value pairs attached to the stack.
    - Permissions: IAM service role that can be assumed by CloudFormation
    - Stack failure options:
        - Specifies what to do if something fails while the stack is provisioned
        - Options:
            - Roll back all stack resources
            - Preserve successfully provisioned resources
    - Stack policy: `defines the resources that we want to protect from unintentional updates during a stack update`
    - Rollback configuration: we can monitor the stack while it is being created/updated and we can roll it back in case a threshold is breached (example if any alarm goes to ALARM state)
    - Notification options: we can specify an SNS topic where notifications should go
    - Stack creation options: following options are included for stack creation, but aren't available as part of stack updates:
        - Timeout: Specifies the amount of time, in minutes, that CloudFormation should allot before timing out stack creation operations

## Template Parameters and Pseudo Parameters

- Template parameters allow input via the console, CLI or API when the stack is created or updated
- Parameters are defined within the resources and they can be referenced from within the logical resources
- Parameters can have `default values`, allowed values, min/max length, allowed patterns, no echo (useful for passwords, the value is not displayed when typed) and types
- Pseudo Parameters: 
    - AWS makes available parameters which can be referenced by the CF template
    - Example:
        - `AWS::Region`
        - `AWS::StackId`
        - `AWS::StackName`
        - `AWS::AccountId`
    - Pseudo parameters `are parameters which can not be populated by us`, they are populated by AWS and provided for us to reference them
- Parameters provide portability for the template
- Best practice:
    - Minimize number of parameters and provide defaults where applicable
    - Use pseudo parameters where possible

## Intrinsic Functions

- Examples of functions:
    - `Ref` and `Fn::GetAtt`: reference a value from one logical resource
    - `Fn::Join` and `Fn::Split`: join/split strings to create new ones
    - `Fn::GetAZs` and `Fn::Select`: get availability zones in a regions and select one
    - Conditions: `Fn::IF`, `And`, `Equals`, `Not`, `Or`
    - `Fn::Base64` and `Fn::Sub`: encode strings to base64, substitute replacement on variables in the text
    - `Fn:Cidr`: build CIDR blocks
- `Fn::GetAZs` 

## Mappings

In AWS CloudFormation, Mappings are used to create a set of key-value pairs that can be used to specify conditional values, such as choosing the appropriate AMI based on the region or environment. 
- uses !FindInMap Intrinsic function
```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: CloudFormation Template with Mappings to Select AMI ID Based on Region

Mappings:
  RegionToAMI:
    us-east-1:
      AMI: ami-0c55b159cbfafe1f0 
    us-west-2:
      AMI: ami-0d5d9d301c853a04a 
    eu-west-1:
      AMI: ami-0fc61db8544f41e8c 

Resources:
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t2.micro  
      ImageId: !FindInMap
        - RegionToAMI
        - !Ref "AWS::Region" 
        - AMI
      SecurityGroups:
        - Ref: MySecurityGroup
      KeyName: my-key-pair  
      AvailabilityZone: !Select [ 0, !GetAZs '' ] 
      Tags:
        - Key: Name
          Value: MyEC2Instance

  MySecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: "Allow SSH and HTTP access"
      SecurityGroupIngress:
        - IpProtocol: "tcp"
          FromPort: "22"
          ToPort: "22"
          CidrIp: "0.0.0.0/0"  
        - IpProtocol: "tcp"
          FromPort: "80"
          ToPort: "80"
          CidrIp: "0.0.0.0/0"  # Allow HTTP access from anywhere
      SecurityGroupEgress:
        - IpProtocol: "-1"
          CidrIp: "0.0.0.0/0"  # Allow all outbound traffic

Outputs:
  EC2InstancePublicIP:
    Description: "Public IP of the created EC2 instance"
    Value: !GetAtt MyEC2Instance.PublicIp
  EC2InstanceId:
    Description: "Instance ID of the created EC2 instance"
    Value: !Ref MyEC2Instance

```

## Outputs

- The `Outputs` section in a template is optional
- We can declare values in this section which will be visible as output in the CLI/Console
- Output will be accessible from a parent stack when using nesting
- Outputs can be exported allowing cross-stack references
- Example:
    ```
    Outputs:
        WordPressUrl:
            Description: 'description text'
            Value: !Join['', 'https://', !GetAtt Instance.DNSName]
    ```

## Conditions

- Allows stack to react to certain conditions and change infrastructure based on those
- They declared in an optional section named `Conditions`
- Conditions use other intrinsic functions: `AND`, `EQUALS`, `IF`, `NOT`, `OR`
- Conditions can be nested
```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: CloudFormation Template using Mappings and Conditionals for EC2 Instance creation

Parameters: #Similiar to variables of terraform
  Environment:
    Type: String
    Description: Environment for the EC2 instance (e.g., dev, prod)
    Default: dev
    AllowedValues:
      - dev
      - prod
    ConstraintDescription: Must be either 'dev' or 'prod'

Mappings:
  InstanceTypeMapping:
    dev:
      InstanceType: t2.micro  
    prod:
      InstanceType: m5.large  

Conditions:
  IsDev: !Equals [ !Ref Environment, dev ]  
  IsProd: !Equals [ !Ref Environment, prod ]  

Resources:
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: 
        Fn::If:
          - IsDev
          - !FindInMap [InstanceTypeMapping, dev, InstanceType]  
          - Fn::If:
              - IsProd
              - !FindInMap [InstanceTypeMapping, prod, InstanceType]  
              - !Ref "AWS::NoValue"  
      ImageId: ami-0c55b159cbfafe1f0  
      SecurityGroups:
        - Ref: MySecurityGroup
      KeyName: my-key-pair  # Replace with your key pair name
      AvailabilityZone: !Select [ 0, !GetAZs '' ]  # Default to first AZ in the region
      Tags:
        - Key: Name
          Value: !Sub "${Environment}-EC2Instance"


  MySecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: "Allow SSH and HTTP access"
      SecurityGroupIngress:
        - IpProtocol: "tcp"
          FromPort: "22"
          ToPort: "22"
          CidrIp: "0.0.0.0/0"  
        - IpProtocol: "tcp"
          FromPort: "80"
          ToPort: "80"
          CidrIp: "0.0.0.0/0"  
      SecurityGroupEgress:
        - IpProtocol: "-1"
          CidrIp: "0.0.0.0/0" 

Outputs:
  EC2InstancePublicIP:
    Description: "Public IP of the created EC2 instance"
    Value: !GetAtt MyEC2Instance.PublicIp
  EC2InstanceId:
    Description: "Instance ID of the created EC2 instance"
    Value: !Ref MyEC2Instance

```

## DependsOn

- Allows us to establish dependencies between resources
- CFN tried to be efficient by creating/updating/deleting resources in parallel
- Also, it tries to determine a dependency order (example: VPC => SUBNET => EC2) by using references or functions
- Dependency can be defined using the `DependsOn` property specify the resource on which we depend on
- `DependsOn` can accept a single resource or a list of resources

## Creation Policies, Wait Conditions and cfn-signal

- AWS CloudFormation signals are used to indicate the status of a resource during the creation or update process of a stack. They are often used with AWS::CloudFormation::WaitCondition and AWS::CloudFormation::WaitConditionHandle resources to delay the stack's completion until a specific signal is received, allowing you to coordinate the setup of resources like EC2 instances, applications, or external services.

### What are CloudFormation Signals?
In CloudFormation, you can use signals to indicate whether a resource has successfully completed some action (e.g., an EC2 instance is ready, an application has been installed, etc.). This helps in cases where CloudFormation has to wait for a certain condition before moving forward, ensuring that resources are properly set up before continuing.

#### There are two main components involved:

- WaitCondition: This resource waits for signals before CloudFormation can complete the stack creation or update.
- WaitConditionHandle: This is a special resource that is used to send the signal (either success or failure) to CloudFormation.
Typical Use Case:
A common scenario is launching an EC2 instance, configuring it (for example, installing an application), and only marking the stack as "completed" when the EC2 instance is fully configured. If the instance fails to configure correctly, you might want to fail the stack creation or update.

Key Resources:
AWS::CloudFormation::WaitCondition: Waits for a signal to proceed.
AWS::CloudFormation::WaitConditionHandle: Sends a signal to the wait condition.
- A `WaitCondition` relies on a `WaitHandle`, which is another logical resource. Its job is to generate a presigned url which can be used to send signals to `WaitCondition`
- With `WaitHandle` we can pass back data to the template. This data can be retrieved using the `!GetAtt WaitCondition.Data` function
```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Example of using CloudFormation Signals with WaitCondition

Resources:
  # WaitConditionHandle that EC2 will signal when it's ready
  MyWaitConditionHandle:
    Type: AWS::CloudFormation::WaitConditionHandle

  # EC2 Instance
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t2.micro
      ImageId: ami-0c55b159cbfafe1f0  # Example AMI (replace with actual)
      SecurityGroups:
        - Ref: MySecurityGroup
      KeyName: my-key-pair
      UserData:
        Fn::Base64: |
          #!/bin/bash
          # Example UserData to install a simple web server
          sudo yum update -y
          sudo yum install -y httpd
          sudo service httpd start
          echo "Hello World" > /var/www/html/index.html
          # Send signal to CloudFormation when done
          curl --data "Success" https://%MyWaitConditionHandle%/signal

  # Security Group for EC2 instance
  MySecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: "Allow HTTP and SSH access"
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: "22"
          ToPort: "22"
          CidrIp: "0.0.0.0/0"
        - IpProtocol: tcp
          FromPort: "80"
          ToPort: "80"
          CidrIp: "0.0.0.0/0"

  # WaitCondition that waits for the signal from the EC2 instance
  MyWaitCondition:
    Type: AWS::CloudFormation::WaitCondition
    Properties:
      Handle: Ref: MyWaitConditionHandle
      Timeout: '300'  # Wait for up to 5 minutes
      Count: '1'  # Wait for one successful signal

Outputs:
  EC2InstancePublicIP:
    Description: "Public IP of the EC2 instance"
    Value: !GetAtt MyEC2Instance.PublicIp

```
## Nested Stacks

- Most simple projects will generally utilize a CFN stack
- Stacks can have limits:
    - Resource limit: 500 resources per stack
    - We can't easily reuse resources, example reference a VPC
- There are 2 ways to architect multi-stack projects:
    - Nested Stacks
    - Cross-Stack References
- Nested Stacks:
    - Root Stack: the stack which is created first, created manually or using some automation
    - A Parent Stack is the parent of any stack which it immediately creates
    - A root stack can create nested stacks having several parent stacks
    - A root stack can have parameters and outputs (just like a normals stack)
- A stack can have another CFN stack as a resource using `AWS::CloudFormation::Stack` type which needs an url to the template
- We can provide input values to the nested stacks. We need to supply values to any parameters from a nested stack if the parameter does not have a default value defined
- Any outputs of a nested stack are returned to the root stack which can be referenced as `NESTEDStack.Outputs.XXX`
- Benefits of a nested stack is to reuse the same template, not the actual stack created
- We should use nested stacks when we want to link the life cycles of different stacks

## Cross-Stack References

- CFN stacks are designed to be isolated and self-contained
- With nested stacks we can reuse code only, with cross-stack references we can reference resources created by other stacks
- Outputs are normally not visible from other stacks, exception being nested stacks which case the parent stack can reference the nested stack output
- Outputs of a template can be exported making them visible from other stacks
- Exports must have unique names in the region
- To use the exported resources we can use `Fn::ImportValue` intrinsic function
- Cross-region or cross-account is not supported for cross-stack references

## StackSets

- Allows to create/update/delete infrastructure across many regions or many accounts
- StackSets are containers in an admin account (account where the StackSet is applied, it does not have to be any special account)
- StackSets contain stack instances (containers for individual stacks) which reference stacks
- Stack instances and stacks are created in target accounts
- Each stack created by a StackSet is a stack created in one region in one account
- Security: we can use self-managed roles or service-managed roles (everything handled by the product). CFN will assume a role to interact with the target accounts
- Terminology:
    - Concurrent Accounts: a value specifying how in how many accounts can we deploy at the same time
    - Failure Tolerance: amount of individual deployments which can fail before declaring the StackSet itself as failed
    - Retain Stacks: remove stack instances from a StackSet but retain the infrastructure
- StackSet use cases:
    - Crate AWS Config Rules
    - Create IAM Roles for cross-account access

## DeletionPolicy

- If we delete a logical resource from a template, by default the physical resource will be deleted by CFN
- With certain type of resources this can cause data loss
- With deletion policy, we can define on each resource to **Delete** (default), **Retain** or **Snapshot** (if supported)
- Supported resources for snapshot are: EBS volumes, ElastiCache, Neptune, RDS, Redshift
- With snapshot before the physical resource is deleted, a snapshot is taken
- Deletion policies only apply to delete operation, NOT replace operation!

## Stack Roles

- By default CFN uses the permissions of the identity who initiates the stack creation
- CFN stack roles is feature where CFN can assume a role to gain permissions to create resources from a stack without the need for the initiator to have the necessary permissions
- The identity creating the stack does not need resource permissions, only `PassRole`

## `AWS::CloudFormation::Init` and `cfn-init`

- CloudFormationInit is a simple configuration management system
- Configuration directive are stored in the template
- `AWS::CloudFormation::Init` is part of EC2 instance logical resource. With this we can specify configurations which will be applied to the created EC2 instance
- User Data is procedural (HOW should things to be done) / `cfn-init` is a desired state (WHAT we want to occur)
- `cfn-init` can be cross-platform and idempotent
- Accessing the CFN init data is done with the `cfn-init` helper script which should be installed on the instance

## `cfn-hup`

- `cfn-init` is a helper tool running once as part of bootstrapping (user data)
- If the `AWS::CloudFormation::Init` is updated, `cfn-init` is not rerun
- `cfn-hup` is a helper tool which can be installed on EC2 instances
- It will detect changes in the resources metadata
- When change is detected, it can run configurable actions. It might rerun `cfn-init` if necessary

## Change Sets

- Change Sets let us preview the changes that will happen after we update a stack
- We can have multiple change sets and preview each of them
- We can chose which change set we want to apply by executing it

## Custom Resources

- CloudFormation doesn't support everything in AWS
- Custom Resources let CFN integrate with anything it does not yet support or wont support at all
- With Custom Resources we can extend CFN to do things which it does not natively support (example: fet configuration from a third party)
- Architecture of custom resources:
    - CFN sends data to an endpoint defined in the custom resource
    - This endpoint might be a Lambda function or an SNS topic
    - When a custom resources is created/update/deleted, CFN sends events to this endpoint containing the operation and any additional property information
    - The compute (Lambda function) can respond to this custom data, letting it know of the success/failure of its execution