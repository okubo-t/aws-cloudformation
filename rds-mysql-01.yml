AWSTemplateFormatVersion: "2010-09-09"
Description:
  RDS for  MySQL Create

Metadata:
  "AWS::CloudFormation::Interface":
    ParameterGroups:
      - Label:
          default: "Project Name Prefix"
        Parameters:
          - PJPrefix
      - Label:
          default: "RDS Configuration"
        Parameters:
          - DBInstanceName
          - MySQLMajorVersion
          - MySQLMinorVersion
          - DBInstanceClass
          - DBInstanceStorageSize
          - DBInstanceStorageType
          - DBName
          - DBMasterUserName
          - DBPassword
          - MultiAZ
    ParameterLabels:
      DBInstanceName:
        default: "DBInstanceName"
      MySQLMajorVersion:
        default: "MySQLMajorVersion"
      MySQLMinorVersion:
        default: "MySQLMinorVersion"
      DBInstanceClass:
        default: "DBInstanceClass"
      DBInstanceStorageSize:
        default: "DBInstanceStorageSize"
      DBInstanceStorageType:
        default: "DBInstanceStorageType"
      DBName:
        default: "DBName"
      DBMasterUserName:
        default: "DBUserName"
      DBPassword:
        default: "DBPassword"
      MultiAZ:
        default: "MultiAZ"

# ------------------------------------------------------------#
# Input Parameters
# ------------------------------------------------------------# 
Parameters:
  PJPrefix:
    Type: String

  DBInstanceName:
    Type: String
    Default: "rds"
  MySQLMajorVersion:
    Type: String
    Default: "5.7"
    AllowedValues: [ "5.5", "5.6", "5.7" ]
  MySQLMinorVersion:
    Type: String
    Default: "22"
  DBInstanceClass:
    Type: String
    Default: "db.m4.large" 
  DBInstanceStorageSize:
    Type: String
    Default: "30"
  DBInstanceStorageType:
    Type: String
    Default: "gp2"
  DBName:
    Type: String
    Default: "db"
  DBMasterUserName:
    Type: String
    Default: "dbuser"
    NoEcho: true
    MinLength: 1
    MaxLength: 16
    AllowedPattern: "[a-zA-Z][a-zA-Z0-9]*"
    ConstraintDescription: "must begin with a letter and contain only alphanumeric characters."
  DBPassword: 
    Default: "dbpassword"
    NoEcho: true
    Type: String
    MinLength: 8
    MaxLength: 41
    AllowedPattern: "[a-zA-Z0-9]*"
    ConstraintDescription: "must contain only alphanumeric characters."
  MultiAZ: 
    Default: "false"
    Type: String
    AllowedValues: [ "true", "false" ]

Resources: 
# ------------------------------------------------------------#
#  DBInstance MySQL
# ------------------------------------------------------------#
  DBInstance: 
    Type: "AWS::RDS::DBInstance"
    Properties: 
      DBInstanceIdentifier: !Sub "${PJPrefix}-${DBInstanceName}"
      Engine: MySQL
      EngineVersion: !Sub "${MySQLMajorVersion}.${MySQLMinorVersion}"
      DBInstanceClass: !Ref DBInstanceClass
      AllocatedStorage: !Ref DBInstanceStorageSize
      StorageType: !Ref DBInstanceStorageType
      DBName: !Ref DBName
      MasterUsername: !Ref DBMasterUserName
      MasterUserPassword: !Ref DBPassword
      DBSubnetGroupName: !Ref DBSubnetGroup
      PubliclyAccessible: false
      MultiAZ: !Ref MultiAZ
      PreferredBackupWindow: "18:00-18:30"
      PreferredMaintenanceWindow: "sat:19:00-sat:19:30"
      AutoMinorVersionUpgrade: false
      DBParameterGroupName: !Ref DBParameterGroup  
      VPCSecurityGroups:
        - !Ref RDSSecurityGroup
      CopyTagsToSnapshot: true
      BackupRetentionPeriod: 7
      Tags: 
        - Key: "Name"
          Value: !Ref DBInstanceName
    DeletionPolicy: "Delete"

# ------------------------------------------------------------#
#  DBParameterGroup
# ------------------------------------------------------------#
  DBParameterGroup:
    Type: "AWS::RDS::DBParameterGroup"
    Properties:
      Family: !Sub "MySQL${MySQLMajorVersion}"
      Description: !Sub "${PJPrefix}-${DBInstanceName}-parm"

# ------------------------------------------------------------#
#  SecurityGroup for RDS (MySQL)
# ------------------------------------------------------------#
  RDSSecurityGroup:
    Type: "AWS::EC2::SecurityGroup"
    Properties:
      VpcId: { "Fn::ImportValue": !Sub "${PJPrefix}-vpc" }
      GroupName: !Sub "${PJPrefix}-${DBInstanceName}-sg"
      GroupDescription: "-"
      Tags:
        - Key: "Name"
          Value: !Sub "${PJPrefix}-${DBInstanceName}-sg"
# Rule
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 3306
          ToPort: 3306
          CidrIp: { "Fn::ImportValue": !Sub "${PJPrefix}-vpc-cidr" }

# ------------------------------------------------------------#
#  DBSubnetGroup
# ------------------------------------------------------------#
  DBSubnetGroup: 
    Type: "AWS::RDS::DBSubnetGroup"
    Properties: 
      DBSubnetGroupName: !Sub "${PJPrefix}-${DBInstanceName}-subnet"
      DBSubnetGroupDescription: "-"
      SubnetIds: 
        - { "Fn::ImportValue": !Sub "${PJPrefix}-private-subnet-a" }
        - { "Fn::ImportValue": !Sub "${PJPrefix}-private-subnet-c" }

# ------------------------------------------------------------#
# Output Parameters
# ------------------------------------------------------------#                
Outputs:
#DBInstance
  DBInstanceID:
    Value: !Ref DBInstance
    Export:
      Name: !Sub "${PJPrefix}-${DBInstanceName}-id"

  DBInstanceEndpoint:
    Value: !GetAtt DBInstance.Endpoint.Address
    Export:
      Name: !Sub "${PJPrefix}-${DBInstanceName}-endpoint"

  DBName:
    Value: !Ref DBName
    Export:
      Name: !Sub "${PJPrefix}-${DBInstanceName}-dbname"
