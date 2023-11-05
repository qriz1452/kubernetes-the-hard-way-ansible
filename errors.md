## aa-cfn-template :

* yaml not well formed errors in indentation.

* Yaml not well formed :  missing one " '  "   , it should be  like this  ' jsdbjbsd '

* Template contains errors.: Template format error: Invalid outputs property : [SecurityGroups, InstanceId, PrivateIp, PublicIp, AvailabilityZone, SubnetId, PublicDnsName]. :

Before :
```
 BastionEC2InstanceDetails:
    Description: "Comprehensive details of the BastionEC2Instance "
    Value: 
      !Sub |
        {
          "InstanceId": "${InstanceId}",
          "AvailabilityZone": "${AvailabilityZone}",
          "PrivateIp": "${PrivateIp}",
          "PublicIp": "${PublicIp}",
          "PublicDnsName": "${PublicDnsName}",
          "SecurityGroups": "${SecurityGroups}",
          "SubnetId": "${SubnetId}"
        }
    InstanceId: !Ref BastionEC2Instance
    AvailabilityZone: !GetAtt BastionEC2Instance.AvailabilityZone
    PrivateIp: !GetAtt BastionEC2Instance.PrivateIp
    PublicIp: !If [HasPublicIp, !GetAtt BastionEC2Instance.PublicIp, 'N/A']
    PublicDnsName: !GetAtt BastionEC2Instance.PublicDnsName
    SecurityGroups: !Join [", ", !GetAtt BastionEC2Instance.SecurityGroupIds]
    SubnetId: !Ref Subnet1


```

Resolved after :
```
BastionEC2InstanceDetails:
    Description: "Comprehensive details of the BastionEC2Instance"
    Value: 
      !Sub
        - |
          {
            "InstanceId": "${InstanceId}",
            "AvailabilityZone": "${AvailabilityZone}",
            "PrivateIp": "${PrivateIp}",
            "PublicIp": "${PublicIp}",
            "PublicDnsName": "${PublicDnsName}",
            "SecurityGroups": "${SecurityGroups}
            "SubnetId": "${SubnetId}"
          }
        - InstanceId: !Ref BastionEC2Instance
          AvailabilityZone: !GetAtt BastionEC2Instance.AvailabilityZone
          PrivateIp: !GetAtt BastionEC2Instance.PrivateIp
          PublicIp: !If [HasPublicIp, !GetAtt BastionEC2Instance.PublicIp, 'N/A']
          PublicDnsName: !GetAtt BastionEC2Instance.PublicDnsName
          SecurityGroups: !Join [", ", !GetAtt BastionEC2Instance.SecurityGroupIds]
          SubnetId: !Ref PublicSubnet1

```

* ValidationError - Template error: resource BastionEC2Instance does not support attribute type SecurityGroupIds in Fn::GetAtt
Resolved after removing GettAtt SGId as it does not support we need ti use Ref instead of GettAtt

* ValidationError - Unresolved resource dependencies [KubernetesContro2ler1EC2Instance] in the Outputs block of the template  :  that was spelling error
* aws cfn  sorting parameter via abc , but i need as per cfn temlpate : resolved after using metadat and labels , parameter group , parameter labels
* Creating ec2 keypair not supported via cfn -  no solition need to create manually
* Template error: unresolved condition dependency HasPublicIp in Fn::If :  in template we had haspublicip but the condition block was missing
* ValidationError - Template form
at error: Unresolved dependencies [KubernetesWorker3EC2Instance, BastionEC2Instance, KubernetesWorker2EC2Instance, KubernetesClientEC2Instance, KubernetesWorker1EC2Instance, KubernetesController2EC2Instance, KubernetesProxyEC2Instance, AnsibleEC2Instance, KubernetesController1EC2Instance, KubernetesController3EC2Instance]. Cannot reference resources in the Conditions block of the 
template   : Resolved after removing get public ip from outputs  that was error because of condition block , getattr

 * Invalid id: "/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2" (expecting "ami-...") (Service: AmazonEC2; Status Code: 400; Error Code: InvalidAMIID.Malformed; Request ID: 97d30c7a-8728-4c27-b02a-d9174584fad0; Proxy: null) : Incorrect ami was provided . resolved after correcting ami id , we were using ssm parameter later changed to mapping for multiple region with amis
 *   5/11/2023, 1:21:54 pm - Template contains errors.: Template format error: Mappings attribute name 'Ubuntu20-FocalFossa20.04LTS' must contain only alphanumeric characters. : Resolved after updaing name
 * Parameter validation failed: parameter value for parameter name KeyName does not exist. Rollback requested by user. : forgot to select keypair name from parameters aws cfn dashboard
 * The parameter groupName cannot be used with the parameter subnet (Service: AmazonEC2; Status Code: 400; Error Code: InvalidParameterCombination; Request ID: 89641781-9ab9-4c6b-a3a3-7c23b46887e3; Proxy: null) : Updated the sg name to SecurityGroupIds with getattr from ref and subnetId from ref to getattr  ( if ref used we will still get error so use getattr )
 * Security group sg-05edcba630bd675fa and subnet subnet-09ac1a82955833f71 belong to different networks. (Service: AmazonEC2; Status Code: 400; Error Code: InvalidParameter; Request ID: 39117cfe-aa91-4fa4-aa70-74964ec1d188; Proxy: null) : added subnetid with getattr then resolved

 * Encountered unsupported property SecurityGroupsIds  : its Ids  not sIds

 * the whole stack launched  but on ec2 dashboard there is no name of machines  : added tag Name and value
 
