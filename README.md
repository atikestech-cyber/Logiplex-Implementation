# LogiPlex Implementation

A reference implementation and documentation for using the **LogiPlex Connector with SailPoint IdentityIQ** to logically separate application-specific accounts and entitlements from a shared OpenLDAP or Active Directory source.

## Overview

The LogiPlex Connector provides a logical separation layer between a single directory source and multiple business applications in SailPoint IdentityIQ.

It allows multiple applications to use the same physical directory while presenting their accounts and entitlements as separate logical applications within IdentityIQ.

## Problem

When multiple applications use a shared directory source, all application groups may be aggregated into a single IdentityIQ application.

This can make it difficult to:

* Identify application-specific entitlements
* Manage application ownership
* Control access requests
* Define certification scope
* Review application-specific access

## Solution

LogiPlex separates the directory objects into logical applications based on configured directory structures such as Organizational Units (OUs).

```text
                 OpenLDAP / Active Directory
                           |
                           v
                    Source Connector
                           |
                           v
                    LogiPlex Connector
                           |
                           v
                       Split Rule
                           |
             +-------------+-------------+
             |             |             |
             v             v             v
        Application A  Application B  Application C
```

The physical directory remains unchanged while IdentityIQ receives logically separated application views.

## Key Components

| Component          | Purpose                                       |
| ------------------ | --------------------------------------------- |
| Source Connector   | Connects IdentityIQ to the physical directory |
| LogiPlex Connector | Provides logical application separation       |
| Custom Object      | Stores application-to-OU mappings             |
| Split Rule         | Determines the target logical application     |
| Provisioning Rule  | Sends logical requests to the physical source |

## Aggregation

During aggregation:

1. Accounts and groups are retrieved from the directory.
2. The LogiPlex split logic evaluates the objects.
3. Group DN/OU information is used to determine the target application.
4. Matching accounts and groups are logically separated.
5. Each application receives its relevant entitlements.

```text
Directory
    |
    v
Aggregation
    |
    v
Split Logic
    |
    v
DN / OU Evaluation
    |
    +----------+----------+
    |          |          |
    v          v          v
   HR       Finance     Sales
   App        App        App
```

## Provisioning

Provisioning requests made through a logical application are translated back to the underlying physical directory.

```text
Access Request
      |
      v
Logical Application
      |
      v
Provisioning Logic
      |
      v
Source Connector
      |
      v
OpenLDAP / Active Directory
```

## Application Mapping

The implementation uses a Custom Object to map logical applications to directory OUs.

Example:

```text
HR Application       = ou=hr,ou=groups,dc=example,dc=com
Finance Application  = ou=finance,ou=groups,dc=example,dc=com
Sales Application    = ou=sales,ou=groups,dc=example,dc=com
```

The split logic evaluates the incoming group DN against these mappings and determines the appropriate logical application.

## Split Logic

### Account Processing

For account objects, the split logic:

* Reads the account's groups
* Determines the application associated with each group
* Groups entitlements by application
* Creates logical account representations
* Retains only the relevant groups for each application

### Group Processing

For group objects, the split logic:

* Reads the group DN
* Determines the target application
* Creates the corresponding logical group representation

### Default Behavior

Objects that cannot be associated with a configured application remain associated with the original source application.

## Benefits

* Logical separation of application entitlements
* Single physical directory source
* Improved entitlement visibility
* Application-specific access requests
* Cleaner certification scope
* Simplified application ownership
* Centralized directory management

## Repository Contents

```text
Logiplex-Implementation/
│
├── README.md
│
├── LogiPlex Connector Documentation
│
└── Connector Package
```

The repository includes the implementation documentation and connector package required to understand the LogiPlex implementation.

## Documentation

Detailed implementation documentation covers:

* Architecture
* Connector configuration
* Custom Object configuration
* Split logic
* Aggregation behavior
* Provisioning behavior
* Validation
* Troubleshooting

## Security Notice

Do not commit sensitive information such as:

* Passwords
* API keys
* Access tokens
* LDAP credentials
* Production URLs
* Private certificates
* Environment-specific secrets

Use placeholders for environment-specific configuration.

## Disclaimer

This repository is provided for implementation reference and demonstration purposes. Review and adapt the configuration to your IdentityIQ environment before using it in production.
