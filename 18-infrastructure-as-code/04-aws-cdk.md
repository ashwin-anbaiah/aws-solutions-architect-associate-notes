# AWS Cloud Development Kit (CDK)

## What is AWS CDK?

**AWS CDK** (Cloud Development Kit) is an **open-source framework** that lets you define cloud infrastructure using familiar **programming languages** — instead of writing raw JSON/YAML CloudFormation templates.

Under the hood, CDK **synthesizes** your code into a CloudFormation template, then deploys it.

```
Your Code (Python/TypeScript/Java/etc.)
        ↓  cdk synth
CloudFormation Template (JSON/YAML)
        ↓  cdk deploy
AWS Cloud Resources
```

## Supported Languages

- TypeScript (most popular)
- Python
- Java
- C# (.NET)
- Go

## CDK vs CloudFormation

| Feature | CloudFormation | AWS CDK |
|---|---|---|
| Language | JSON / YAML only | TypeScript, Python, Java, C#, Go |
| Abstraction level | Low (raw resources) | High (constructs / patterns) |
| Reuse | Nested stacks, copy-paste | Classes, modules, libraries |
| IDE support | Limited | Full (autocomplete, linting, type-checking) |
| Logic (loops, conditions) | Limited intrinsic functions | Full programming language constructs |
| Output | Template is the artifact | Synthesizes to CloudFormation |

## CDK Core Concepts

### Constructs

The **basic building block** of CDK. A construct represents one or more AWS resources.

Three levels:
| Level | Name | Description |
|---|---|---|
| **L1** | Cfn Resources | Direct 1:1 mapping to a CloudFormation resource (e.g., `CfnBucket`) |
| **L2** | AWS Constructs | Higher-level with sensible defaults and helper methods (e.g., `Bucket`) |
| **L3** | Patterns | Complete architectures (e.g., `ApplicationLoadBalancedFargateService`) |

### Stacks

- A **CDK Stack** maps directly to a **CloudFormation Stack**
- One CDK app can contain multiple stacks
- Each stack is independently deployable

### Apps

- A **CDK App** is the root of the CDK construct tree
- It contains one or more stacks
- `app.synth()` produces the CloudFormation templates

## CDK Workflow

```bash
npm install -g aws-cdk       # Install CDK CLI
cdk init app --language python   # Initialize a new CDK project
pip install aws-cdk.aws-ec2      # Install service libraries

# Development loop:
cdk synth          # Synthesize CloudFormation template (preview)
cdk diff           # Show what will change vs deployed stack
cdk deploy         # Deploy to AWS
cdk destroy        # Tear down the stack
```

## CDK Code Example (Python)

```python
from aws_cdk import aws_ec2 as ec2, core

class EC2InstanceStack(core.Stack):
    def __init__(self, scope: core.Construct, id: str, **kwargs):
        super().__init__(scope, id, **kwargs)

        vpc = ec2.Vpc.from_lookup(self, "VPC", is_default=True)

        sg = ec2.SecurityGroup(self, "InstanceSG", vpc=vpc,
                               description="Allow SSH", allow_all_outbound=True)
        sg.add_ingress_rule(ec2.Peer.any_ipv4(), ec2.Port.tcp(22))

        instance = ec2.Instance(self, "EC2Instance",
            instance_type=ec2.InstanceType("t2.micro"),
            machine_image=ec2.MachineImage.latest_amazon_linux(),
            vpc=vpc,
            security_group=sg)

        core.CfnOutput(self, "InstanceId", value=instance.instance_id)

app = core.App()
EC2InstanceStack(app, "EC2InstanceStack")
app.synth()
```

## Key Points / Exam Tips

- CDK is **not a replacement** for CloudFormation — it synthesizes *to* CloudFormation
- CDK uses familiar **programming languages**, enabling loops, conditions, functions, and OOP
- **L1 constructs** = raw CloudFormation (most verbose); **L3 constructs** = full patterns (least verbose)
- `cdk synth` generates the CloudFormation template; `cdk deploy` deploys it
- CDK is open-source — the construct library is published on npm, PyPI, Maven, etc.
- CDK apps can deploy to multiple accounts and regions using **environments**

## Trigger Words

| Keyword | Think |
|---|---|
| "Define infrastructure using Python/TypeScript" | AWS CDK |
| "Synthesize CloudFormation template from code" | CDK (`cdk synth`) |
| "High-level cloud patterns with sensible defaults" | CDK L2/L3 constructs |
| "Open-source IaC framework" | AWS CDK |
| "IaC with full programming language support" | AWS CDK |
