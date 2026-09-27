# CloudFormation Template Structure

## Template Sections

| Section | Required? | Purpose |
|---|---|---|
| `AWSTemplateFormatVersion` | No | Template version — always `"2010-09-09"` |
| `Description` | No | Free-text description of the template |
| `Metadata` | No | Extra info for UI (e.g., parameter grouping) |
| **`Resources`** | **YES** | Declares all AWS resources — only mandatory section |
| `Parameters` | No | Input values passed at stack creation/update |
| `Mappings` | No | Fixed key-value lookup tables |
| `Conditions` | No | Logical conditions controlling resource creation |
| `Outputs` | No | Values returned after creation; can be exported for cross-stack use |

## Parameters

Accept user input at deploy time (environment name, instance type, etc.).

```yaml
Parameters:
  Env:
    Type: String
    Default: dev
    AllowedValues: [dev, prod]
    Description: Deployment environment
```

- Referenced using `!Ref ParameterName`
- Support `Default`, `AllowedValues`, `AllowedPattern`, `MinLength`, `MaxLength`

## Mappings

Static lookup tables — great for AMI IDs per region or instance sizes per env.

```yaml
Mappings:
  InstanceConfig:
    dev:  { InstanceType: t2.micro }
    prod: { InstanceType: t3.small }
```

- Accessed with `!FindInMap [MapName, TopLevelKey, SecondLevelKey]`

## Conditions

Boolean expressions evaluated at deploy time to control whether resources/properties are created.

```yaml
Conditions:
  IsProd: !Equals [ !Ref Env, prod ]
```

- Condition intrinsic functions: `Fn::And`, `Fn::Or`, `Fn::Not`, `Fn::If`, `Fn::Equals`

## Resources (Mandatory)

```yaml
Resources:
  EC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: !FindInMap [InstanceConfig, !Ref Env, InstanceType]
      ImageId: ami-0abcdef1234567890
      Tags:
        - Key: Name
          Value: !Sub "${Env}-ec2"
```

## Outputs

```yaml
Outputs:
  ElasticIP:
    Description: Elastic IP address
    Value: !Ref ElasticIP
    Export:
      Name: MyStack-ElasticIP
```

- Use `!ImportValue MyStack-ElasticIP` in another stack to consume the export

## Common Intrinsic Functions

| Function | Purpose |
|---|---|
| `!Ref` | Resource ID or parameter value |
| `!GetAtt Resource.Attr` | Attribute of a resource (e.g., `PublicIp`, `Arn`) |
| `!FindInMap [Map, K1, K2]` | Look up a Mappings value |
| `!Sub "string ${Var}"` | String substitution |
| `!If [Cond, IfTrue, IfFalse]` | Conditional value |
| `!Join [delim, [list]]` | Join a list into a string |
| `!Select [index, list]` | Pick one item from a list |
| `!ImportValue ExportName` | Import exported output from another stack |

## Key Points / Exam Tips

- `Resources` is the **only mandatory** template section
- `!Ref` on a **parameter** returns its value; `!Ref` on a **resource** returns the resource's logical ID or physical ID
- `!GetAtt` is needed for attributes like `PublicIp` — `!Ref` alone won't give you these
- `Mappings` are **static** (defined in template); `Parameters` are **dynamic** (supplied at runtime)
- Outputs with `Export` enable **cross-stack references** — useful for sharing VPC IDs, security group IDs, etc.
- `Conditions` cannot be nested directly — use intrinsic functions like `Fn::And`

## Trigger Words

| Keyword | Think |
|---|---|
| "AMI ID per region" | Mappings + `!FindInMap` |
| "Conditionally create resource in prod only" | Conditions + `Fn::If` |
| "Share VPC ID between stacks" | Outputs + Export + `!ImportValue` |
| "User supplies instance type at deploy time" | Parameters |
| "Only mandatory section" | Resources |
