# Overview
Domains provide a classification layer that describes the type of security control involved during the execution of a scenario in OpenAEV. They help users understand which defensive capability is being evaluated and allow scenarios and payloads to be interpreted with clearer operational intent.

## Domains

A Domain represents the security control category targeted by a payload or injector execution. This classification helps to:

- Clarify the objective and security angle of each execution.
- Improve visibility when reviewing results based on security control categories.
- Align test coverage with the organization's security capabilities (endpoint, network, cloud, email, etc.).

Domains are currently predefined by the platform and cannot be created or modified by users. They are assigned automatically based on the Payload (and in certain cases the Injector Contract). User-defined management of Domains may be introduced in future releases.

## Available Domains

The following Domains are currently supported in OpenAEV:

| Domain                 | Description                                                                                                        |
|------------------------|--------------------------------------------------------------------------------------------------------------------|
| **ENDPOINT**           | Evaluates workstation and server controls such as EDR, antivirus, local monitoring, and response.                  |
| **DATA EXFILTRATION**  | Assesses the ability to detect or block attempts to exfiltrate data.                                               |
| **URL FILTERING**      | Validates mechanisms controlling web access and URL categorization or filtering.                                   |
| **TABLE-TOP**          | Represents process-oriented or organizational exercises involving manual decision-making or coordinated response.  |
| **CLOUD**              | Evaluates cloud-native security controls including IAM, configuration posture, and cloud logging visibility.       |
| **NETWORK**            | Targets network security capabilities such as segmentation, IDS/IPS, firewalls, and traffic inspection.            |
| **EMAIL INFILTRATION** | Tests security controls protecting email flows, including phishing detection and malicious attachment filtering.   |
| **WEB APP**            | Focuses on the security of web applications and related controls such as vulnerability detection and WAF behavior. |

## How Domains Are Applied

### Payloads define the Domain
Domains are primarily defined at the Payload level. Each payload declares one or more Domains that describe the security control category involved in its execution. This ensures consistent and predictable classification across the platform.

### Injector Contracts
In some cases, an Injector Contract may also carry a Domain.  
However, when an injector uses a Payload, the Payload’s Domain always takes precedence. This ensures that the Domain reflects the actual technical behavior of the executed action.

## Usage

Domains allow users to:

- Filter and interpret scenario results based on the type of security control tested.
- Design scenario flows that focus on specific areas of the security stack.
- Generate reports organized by security capability (endpoint, network, cloud, etc.).

## Domains defined by Injectors and Collectors
Some Injectors and Collectors can define and **manage their own Domains**.

In these cases:

- The Injectors/Collectors declares the Domain associated with the Injector Contracts/Payloads it produces.
- The Domain attached by the Injector or Collector is then applied to the corresponding Injector Contracts/Payload executions.
- This mechanism allows certain integrations to carry a Domain that is closest to their technical and operational behavior, without requiring manual configuration on the platform.

## Automatic weekly updates of Domains
Injectors and Collectors are **updated on a weekly basis**. 

During these updates:

- The list of Domains associated with their Payloads is synchronized with the platform.
- If a Domain that should be present (according to the latest Injector/Collector definition) has been removed or modified via the platform, it will be re-added or reset to its expected value during the next update.
- Domains that have been added manually in addition to those defined by the Injector or Collector are preserved. Only missing Domains are added back; no extra, user-added Domains are deleted.