# Security & Compliance

Sensitive data needs additional. Concepts covered here are :

- [IAM]()
- [Data security]()
- [Data loss prevention]()
- [Compliance]()

# Identity & access management
Cloud IAM

## Predefined Roles
## Custom Roles
## Using Roles with Service Accounts
## Access Constrol with Policies

# Using IAM with Storage & Processing Services

## Cloud Storage
## Cloud Bigtable
## BigQuery
## Dataflow

# Data Security

## Encryption
## Key Management

# Privacy / Data Loss Prevention

## Detecting Sensitive Data
## Running Data Loss Preventin Jobs
## Inspection Best Practices

# Legal Compliance

## Health Insurance Portability and Accountability Act 
The HIPAA is a federal law in the US that protects individuals’ healthcare infos (enacted in 1996, updated in 2003 & 2005)

1. [The HIPAA Privacy Rule](www.hhs.gov/hipaa/for-professionals/privacy/index.html)
protect patient’s healthcare infos :
    - limits on data that can be shared by healthcare providers, insurers...
    - grants patients the right to review infos in their records & request infos

2. [The HIPAA Security Rule](www.hhs.gov/hipaa/for-professionals/security/index.html) defines standards for protecting records. Organizations that hold electronic healthcare data :
- are responsible for the confidentiality, integrity & availability of healthcare infos
- have to protect against expected threats & prevent unauthorized disclosures.
- in practice: security management, access control practices, incident response procedures, contingency planning & evaluation of security measures

[The Health Information Technology for Economic and Clinical Health (HITECH)](www.hhs.gov/hipaa/for-professionals/special-topics/hitech-act-enforcement-interim-final-rule/index.html) extended the application of HIPAA to business associates of healthcare providers and insurers. They must follow HIPAA regulations as well.

[More infos here](https://cloud.google.com/security/compliance/hipaa/)

## Children’s Online Privacy Protection Act
The [COPPA](www.ftc.gov/tips-advice/business-center/guidance/complying-coppa-frequently-asked-questions) (a U.S. federal law passed in 1998) applies to websites & online services that collect infos about children under the age of 13. Here is a summary of these rules :
- Post clear and comprehensive privacy policies
- Provide direct notice to parents before collecting a child’s personal information
- Give parents a choice about how a child’s data is used
- Give parents access to data collected about a child
- Give parents the opportunity to block collection of a child’s data
- Keep a child’s data only as long as needed to fulfill the purpose for which it was created
- In general, maintain the confidentiality, integrity, and availability of collected data (name, address, online contact infos, telephone number, geolocation & photos)


## Federal Risk and Authorization Management Program
The [FedRAMP](www.fedramp.gov) is a U.S. federal government program that promotes a standard approach to assessment, authorization, & monitoring of cloud resources - to ensure that cloud systems used by governments are adequately secure, reduce duplication of effort & reduce risk management costs.

## General Data Protection Regulation
The [GDPR](https://gdpr-info.eu) is a EU regulation passed in 2016 (enforcement began in 2018). Its purpose is to standardize privacy protections across the EU, grant controls to individuals over their private infos & specify security practices.

- a controller : 
    - person or organization that determines the purpose & means of processing personal data
    - responsible for gaining and managing the consent of individuals whose data is collected
    - direct processors on implementing the wishes of individuals who request access or changes to data
- a processor : 
    - person or organization that processes data on behalf of a controller.
    - responsible for securing data and conducting audits
    - must notify the controller in case of data breach. Controllers in turn must notify the supervising authority & individuals concerned
