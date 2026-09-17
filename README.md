# Cisco 300-435 ENAUTO Exam: Automating and Programming Cisco Enterprise Solutions

[![Cisco Certified](https://img.shields.io/badge/Cisco_Certified-CCNP_Enterprise_|_ENAUTO-049fd9?style=for-the-badge&logo=cisco&logoColor=white)](https://www.cisco.com/)
[![Track](https://img.shields.io/badge/Track-Enterprise_Automation-049fd9?style=for-the-badge&logo=cisco)](https://www.cisco.com/)
[![Level](https://img.shields.io/badge/Level-Professional_Concentration-1BA0D7?style=for-the-badge)](https://www.cisco.com/)
[![Duration](https://img.shields.io/badge/Duration-90_Minutes-orange?style=for-the-badge)](https://www.cisco.com/)
[![Score](https://img.shields.io/badge/Passing_Score-~825%20%2F%201000-blue?style=for-the-badge)](https://www.cisco.com/)
[![Practice Partner](https://img.shields.io/badge/Practice_Partner-CertsClub_(20%25_Off_Code:_club20)-28a745?style=for-the-badge&logo=shield)](https://www.certsclub.com/cisco/)

---

## 1. Exam Overview & Candidate Profile

The **Cisco 300-435 ENAUTO (Automating and Programming Cisco Enterprise Solutions)** exam tests a candidate's implementation of enterprise automated solutions, including network programmability protocols (NETCONF, RESTCONF, YANG), Python automation frameworks, and controller APIs across Cisco Catalyst Center (DNA Center), Cisco SD-WAN (vManage), and Cisco Meraki. Achieving a passing score awards the **Cisco Certified Specialist - Enterprise Automation and Programmability** certification and satisfies concentration requirements for both **CCNP Enterprise** and **Cisco Certified DevNet Professional**.

### Target Candidate Profile & Roles
* **Enterprise Network Automation Engineer**
* **NetDevOps Solutions Developer**
* **Network Software Architect**
* **Senior Infrastructure Automation Specialist**
* **Prerequisites:** Mastery of Python scripting fundamentals, Linux environments, Git version control, and enterprise routing/switching architectures (CCNP ENCOR level).

---

## 2. Key Exam Specifications

| Parameter | Official Specification |
| :--- | :--- |
| **Exam Code** | 300-435 |
| **Exam Name** | Automating and Programming Cisco Enterprise Solutions (ENAUTO) |
| **Associated Credential** | Cisco Certified Specialist - Enterprise Automation / CCNP Enterprise / DevNet Professional |
| **Duration** | 90 Minutes |
| **Passing Score** | ~825 / 1000 (Dynamic scaled scoring) |
| **Question Count** | 55–65 questions |
| **Question Formats** | Multiple Choice (single/multiple select), Drag-and-Drop, Python/YANG/JSON Code Snippets |
| **Delivery Vendor** | Pearson VUE Authorized Test Centers & OnVUE Online Remote Proctored |
| **Practice Test Partner** | **[300-435 Practice Test](https://www.certsclub.com/cisco/)** (Coupon: `club20` for 20% off) |

---

## 3. Skills Measured & Blueprint Domain Weighting

| Domain Code | Domain Title | Exam Weight | Key Technical Objectives Covered |
| :--- | :--- | :---: | :--- |
| **1.0** | **Network Programmability Foundation** | **10%** | Common version control workflows (Git branch, merge, pull request, conflict resolution); CI/CD pipeline automation; data formatting standards (JSON, XML, YAML); secure credential handling via environment variables. |
| **2.0** | **Automate APIs and Protocols** | **30%** | REST APIs (HTTP methods, response headers, status codes); NETCONF (RFC 6241) RPCs (`<get>`, `<get-config>`, `<edit-config>`, `<commit>`); RESTCONF (RFC 8040) operations; YANG 1.1 data modeling constructs; Model-Driven Telemetry (gRPC dial-in vs. dial-out). |
| **3.0** | **Network Device Programmability** | **20%** | Python device automation with `ncclient`, `requests`, and `netmiko`; Cisco pyATS / Genie test and validation framework; guest shell on-box Python scripting; EEM automation scripts interacting with Python. |
| **4.0** | **Cisco DNA Center (Catalyst Center)** | **20%** | Cisco Catalyst Center Intent REST APIs; authentication token retrieval; device discovery and provisioning workflows; task and execution status polling; Webhooks and Event Notifications. |
| **5.0** | **Cisco SD-WAN** | **10%** | Cisco vManage REST APIs; session login and CSRF token (`X-XSRF-TOKEN`) validation; device template attachment; real-time device health and alarms monitoring; policy deployment automation. |
| **6.0** | **Cisco Meraki** | **10%** | Meraki Dashboard REST API v1; API key authentication (`X-Cisco-Meraki-API-Key`); organization, network, and device provisioning; webhook alerts and payload secret validation; pagination and rate limit handling. |

---

## 4. Scenario-Based Technical Practice Questions

### Scenario 1: Python REST API Automation - Cisco DNA Center Async Task Polling
**Topology Background:**  
A developer writes a Python script to provision a new interface configuration across 50 Catalyst switches using the Cisco Catalyst Center (DNA Center) Intent API. The API endpoint `/dna/intent/api/v1/network-device` accepts the request and returns an HTTP status code `202 Accepted` with a response payload containing:
```json
{
  "response": {
    "taskId": "7f098a12-4c22-411e-a967-1049bce34567",
    "url": "/dna/intent/api/v1/task/7f098a12-4c22-411e-a967-1049bce34567"
  },
  "version": "1.0"
}
```
How should the developer's script verify that the provisioning job has completed successfully?

* A. Immediately issue a `POST` request to `/dna/intent/api/v1/task` to force execution.
* B. Query the provided task URL (`/dna/intent/api/v1/task/{taskId}`) periodically using `GET` until the `isError` flag is evaluated and `progress` returns complete.
* C. Assume the configuration succeeded because HTTP 202 guarantees execution completion on target devices.
* D. Send a `DELETE` request to clear the taskId and reload the controller.

**Correct Answer:** **B**

**Detailed Technical Explanation:**  
* In the Cisco Catalyst Center REST API, operations that require device interaction or take non-trivial execution time are handled **asynchronously**.
* The server responds with **HTTP `202 Accepted`**, indicating the request is queued, and provides a unique `taskId` and status URL.
* The client script must poll the task URL (`/dna/intent/api/v1/task/{taskId}`) using **HTTP `GET`** in a loop (with appropriate sleep delays) until `endTime` is populated. The script then checks whether `isError` is false and inspects `progress` or `additionalStatusString` to verify that the device configuration completed successfully.
* Distractor analysis: Option A is incorrect because tasks cannot be forced via POST. Option C is incorrect; HTTP 202 only acknowledges receipt of the request, not successful device deployment. Option D cancels the task.

---

### Scenario 2: Cisco SD-WAN vManage REST API - CSRF Token Handling
**Topology Background:**  
A network engineer uses Python `requests` to create a new device configuration template on Cisco SD-WAN vManage via an HTTP `POST` request to `/dataservice/template/device`. The script successfully authenticates against `/j_security_check` with username and password, but the subsequent `POST` call fails with HTTP status code `403 Forbidden`. What mandatory security header did the engineer omit from the `POST` request?

* A. `Authorization: Bearer <token>`
* B. `X-XSRF-TOKEN: <token>`
* C. `Content-Type: application/xml`
* D. `Accept: text/plain`

**Correct Answer:** **B**

**Detailed Technical Explanation:**  
* Cisco SD-WAN vManage implements Cross-Site Request Forgery (**CSRF**) protection for all mutating HTTP requests (`POST`, `PUT`, `DELETE`).
* Authentication workflow:
  1. Authenticate with credentials via `POST /j_security_check` and store the session cookies (e.g., `JSESSIONID`).
  2. Send a `GET` request to `/dataservice/client/token` to retrieve a temporary cryptographic anti-CSRF token.
  3. Include this token in all subsequent mutating API requests inside the custom HTTP header: **`X-XSRF-TOKEN: <retrieved_token>`**.
* Without this header, vManage rejects mutating requests with **`403 Forbidden`**.
* Distractor analysis: Option A is used for OAuth 2.0 bearer authentication, not vManage session/cookie authentication. Options C and D specify payload MIME types that do not satisfy CSRF validation.

---

### Scenario 3: NETCONF RPC Operations - `ncclient` Subtree Filtering
**Topology Background:**  
An automation script uses the Python `ncclient` library to connect to a Cisco Catalyst 9300 switch over SSH port 830. The developer wants to retrieve only the operational and configuration data of interface `GigabitEthernet1/0/1` rather than the entire switch configuration. Which NETCONF RPC and method call should the developer execute?

* A. `m.copy_config(target='running', source='startup')`
* B. `m.get(filter=('subtree', xml_filter))`
* C. `m.edit_config(target='running', config=xml_filter)`
* D. `m.dispatch('reboot')`

**Correct Answer:** **B**

**Detailed Technical Explanation:**  
* In NETCONF (RFC 6241):
  * **`<get>`:** Retrieves both running configuration data **and** operational state/statistics data.
  * **`<get-config>`:** Retrieves only configuration data from a specified datastore (e.g., `running`, `startup`, or `candidate`).
* In Python's `ncclient.manager`:
  * **`m.get(filter=('subtree', xml_filter))`** sends a `<get>` RPC with an XML subtree filter matching only the target interface (`GigabitEthernet1/0/1`), returning its operational state (IP addresses, packets in/out, CRC errors) and configuration.
* Distractor analysis: Option A overwrites the running configuration. Option C mutates configurations. Option D is an invalid non-standard call.

---

### Scenario 4: RESTCONF Protocol - MIME Types and Data Encoding Headers
**Topology Background:**  
A developer makes an HTTP `PATCH` request to update an interface description on a Cisco IOS XE router using the RESTCONF interface:
`PATCH /restconf/data/ietf-interfaces:interfaces/interface=GigabitEthernet1`
The router returns HTTP status code `415 Unsupported Media Type`. What is the root cause of this error?

* A. The requested interface does not exist in the running configuration.
* B. The client request omitted or incorrectly specified the `Content-Type` header, which must be set to `application/yang-data+json` or `application/yang-data+xml`.
* C. The RESTCONF feature is disabled in Cisco IOS XE.
* D. The user account lacks administrative privilege level 15.

**Correct Answer:** **B**

**Detailed Technical Explanation:**  
* Per RFC 8040 (RESTCONF Protocol), clients communicating with a RESTCONF server must specify standardized MIME media types:
  * **`Content-Type: application/yang-data+json`** (or `application/yang-data+xml`) when sending data payloads in requests.
  * **`Accept: application/yang-data+json`** to specify the desired response format.
* When a client sends a payload with an unrecognized or generic header (e.g., `Content-Type: application/json` instead of `yang-data+json`), the RESTCONF agent rejects the payload with **`415 Unsupported Media Type`**.
* Distractor analysis: Option A results in `404 Not Found`. Option C results in `404 Not Found` or connection refused. Option D results in `401 Unauthorized` or `403 Forbidden`.

---

### Scenario 5: YANG 1.1 Data Modeling - Configuration vs. Operational State
**Topology Background:**  
A network software engineer is building a custom YANG module to model optical transceivers. The model must define two attributes:
1. `admin-state`: The user-configurable administrative state (enabled or disabled).
2. `rx-optical-power`: The real-time, read-only laser power reading measured in dBm by the hardware sensor.
Which YANG statement correctly enforces that `rx-optical-power` cannot be altered by a NETCONF `<edit-config>` request?

* A. `mandatory true;`
* B. `config false;`
* C. `read-only true;`
* D. `status current;`

**Correct Answer:** **B**

**Detailed Technical Explanation:**  
* In the YANG data modeling language (RFC 7950):
  * **`config true` (default):** Indicates that the node represents configuration data that can be created, modified, or deleted via `<edit-config>`.
  * **`config false`:** Explicitly designates the node as **operational data (state data)**. Operational data is read-only; it can be inspected via NETCONF `<get>` or streamed via telemetry, but cannot be modified by user configuration operations.
* Distractor analysis: Option A specifies that the leaf must exist in valid configurations. Option C is invalid syntax in YANG (it is SNMP MIB syntax). Option D indicates whether the definition is current or deprecated.

---

### Scenario 6: pyATS / Genie Framework - Parsing Raw CLI Output to Dictionaries
**Topology Background:**  
A NetDevOps engineer writes a test automation script using the Cisco pyATS and Genie framework to validate interface line protocol states across a campus network before and after a software maintenance window. What Genie method transforms unstructured Cisco IOS `show ip interface brief` CLI string output into a structured Python dictionary?

* A. `device.parse('show ip interface brief')`
* B. `device.execute('show ip interface brief')`
* C. `device.configure('show ip interface brief')`
* D. `device.sendline('show ip interface brief')`

**Correct Answer:** **A**

**Detailed Technical Explanation:**  
* In the **Cisco pyATS / Genie** library:
  * **`device.execute()`** sends a CLI command and returns the raw, unformatted text string.
  * **`device.parse()`** passes the command to Genie's built-in parsers, which tokenize the CLI output and return a clean, structured Python **dictionary** (`dict`). For example:
    ```python
    output = device.parse('show ip interface brief')
    status = output['interface']['GigabitEthernet1/0/1']['status']
    ```
* Distractor analysis: Option B returns raw strings without key-value mapping. Option C enters configuration mode. Option D is a low-level pexpect/unicon method.

---

### Scenario 7: Cisco Meraki Dashboard API - Webhook Security Validation
**Topology Background:**  
An enterprise uses Cisco Meraki Dashboard webhooks to trigger automated Slack channel alerts whenever an access switch port goes down. An attacker attempts to flood the webhook server with spoofed fake port-down alerts. What mechanism does the Meraki Dashboard API provide to verify that an incoming HTTP `POST` webhook notification was legitimately generated by Meraki?

* A. Meraki includes a shared secret string configured in the dashboard inside the `sharedSecret` field of the JSON payload.
* B. Meraki signs each packet using a 4096-bit PGP key attached in the email header.
* C. Meraki requires the webhook receiver to initiate a BGP peering session back to Cisco.
* D. Meraki only transmits webhooks over unencrypted HTTP port 80.

**Correct Answer:** **A**

**Detailed Technical Explanation:**  
* When configuring Webhook alerts in the Cisco Meraki Dashboard:
  1. The administrator defines a **Shared Secret** string on the Meraki Dashboard alert template.
  2. When Meraki triggers an alert, it transmits an HTTP `POST` containing a JSON payload with the key `"sharedSecret": "<configured_secret>"`.
  3. The receiver application verifies that the incoming `sharedSecret` matches the pre-shared key before accepting and processing the event. If the secret does not match, the receiver drops the packet as an unauthenticated forgery.
* Distractor analysis: Options B, C, and D are fictional or insecure mechanisms not used by Meraki.

---

### Scenario 8: Model-Driven Telemetry - Dial-In vs. Dial-Out Architecture
**Topology Background:**  
An architect implements Model-Driven Telemetry (MDT) to stream interface counter statistics from 200 Cisco Catalyst 9500 core switches to an Apache Kafka pipeline. The security team mandates that network edge switches must never listen on open inbound management ports; all network connections must originate from internal switch interfaces outbound toward central telemetry collectors. Which MDT telemetry mode satisfies this requirement?

* A. Dial-In Telemetry over NETCONF
* B. Dial-Out Telemetry over gRPC / TCP
* C. SNMPv2c Trap receiver
* D. Syslog over UDP port 514

**Correct Answer:** **B**

**Detailed Technical Explanation:**  
* Model-Driven Telemetry supports two connection establishment modes:
  * **Dial-In Mode:** The external monitoring collector initiates an inbound session (over gRPC or NETCONF) to the router/switch on port 830 or 57400 and requests a telemetry subscription. This requires opening inbound listening ports on the network devices.
  * **Dial-Out Mode:** The router/switch **actively initiates** an outbound TCP/TLS connection to the central telemetry collector (typically over gRPC on port 57500) and begins pushing data according to its local subscription configuration.
* Because edge switches must not listen on open inbound ports, **Dial-Out Telemetry** satisfies the security constraint.
* Distractor analysis: Option A requires inbound connections from collector to switch. Options C and D are legacy polling/unstructured logging protocols.

---

### Scenario 9: Python `requests` Library - Handling Self-Signed SSL Certificates
**Topology Background:**  
A developer writes a script to automate configurations on a lab Cisco Catalyst Center controller using a self-signed development SSL certificate:
```python
import requests

url = "https://10.10.10.50/dna/system/api/v1/auth/token"
response = requests.post(url, auth=("admin", "Cisco123!"))
```
The script crashes with: `requests.exceptions.SSLError: HTTPSConnectionPool: Max retries exceeded ... certificate verify failed`. How should the developer modify the request call to allow testing against the lab environment without certificate validation errors?

* A. Set `verify=False` within the `requests.post()` parameter list.
* B. Change the URL protocol scheme from `https://` to `ftp://`.
* C. Set `timeout=0` to ignore network handshake checks.
* D. Pass `allow_redirects=True`.

**Correct Answer:** **A**

**Detailed Technical Explanation:**  
* The Python `requests` library verifies SSL/TLS certificates by default using Mozilla's trusted CA bundle (via `certifi`).
* In non-production labs with self-signed certificates, certificate validation fails, throwing an `SSLError`.
* Passing **`verify=False`** (e.g., `requests.post(url, auth=..., verify=False)`) instructs `requests` to ignore TLS certificate chain validation, allowing the HTTPS connection to proceed.
* Distractor analysis: Option B changes the protocol away from HTTP. Option C halts socket timeout handling. Option D controls HTTP 3xx redirect behavior.

---

### Scenario 10: Configuration Management - Ansible Playbook Structure for Cisco IOS
**Topology Background:**  
An engineer writes an Ansible playbook to configure an NTP server across multiple Cisco routers:
```yaml
---
- name: Configure Network Time Protocol
  hosts: routers
  gather_facts: no
  tasks:
    - name: Set Primary NTP Server
      cisco.ios.ios_config:
        lines:
          - ntp server 10.1.1.50
```
What role does `gather_facts: no` perform in this playbook, and why is it recommended for network device automation?

* A. It prevents Ansible from executing standard Linux setup commands (like inspecting `/etc` or Linux system daemons) that fail or slow down execution on network OS appliances.
* B. It disables SSH encryption between the Ansible control node and switches.
* C. It suppresses error reporting during execution.
* D. It commits changes without generating running-config diffs.

**Correct Answer:** **A**

**Detailed Technical Explanation:**  
* By default, Ansible runs the `setup` module on managed nodes to gather Linux system facts (e.g., CPU, RAM, OS distribution, file systems).
* Cisco IOS and network appliances do not run a standard Linux shell; attempting to run `setup` on them either fails or introduces substantial delay.
* Setting **`gather_facts: no`** instructs Ansible to skip standard host fact discovery, significantly speeding up playbook execution. (If network facts are required, modules like `cisco.ios.ios_facts` can be explicitly called).
* Distractor analysis: Option B is false; transport security is managed via `ansible_connection: network_cli`. Option C is false; errors are still caught. Option D is false; diffs are controlled by the `--diff` flag.

---

## 5. Recommended Study Resources & Official Documentation

* [Cisco 300-435 ENAUTO Official Blueprint Topics](https://learningnetwork.cisco.com/s/enauto-exam-topics)
* [Cisco DevNet Learning Labs: Network Programmability & ENAUTO](https://developer.cisco.com/)
* [300-435 Practice Test - CertsClub](https://www.certsclub.com/cisco/) (Use coupon `club20` for 20% off)
* [Cisco Catalyst Center REST API Documentation](https://developer.cisco.com/dna-center/)
* [Cisco SD-WAN vManage REST API Guide](https://developer.cisco.com/sdwan/)
* [RFC 8040: RESTCONF Protocol](https://datatracker.ietf.org/doc/html/rfc8040)
* [RFC 6241: Network Configuration Protocol (NETCONF)](https://datatracker.ietf.org/doc/html/rfc6241)

---

## 6. SEO Keywords & Search Index Topics

```
300-435, 300-435 exam, 300-435 practice test, 300-435 study guide, cisco 300-435,
enauto, cisco enauto, ccnp enterprise automation, devnet professional automation,
certsclub 300-435, dna center intent api taskid polling, vmanage api x-xsrf-token,
ncclient netconf subtree filter, restconf 415 unsupported media type yang-data+json,
yang config false operational state, genie pyats parse show ip interface brief,
meraki webhook sharedsecret validation, model driven telemetry dial-out grpc,
python requests verify false, ansible gather facts no cisco ios
```

---

## 7. Community Discussions & Contributions

* **Script & Playbook Sharing:** Share Postman collections, pyATS test scripts, or Ansible playbooks in [GitHub Discussions](../../discussions).
* **Issue Submissions:** To report errata or suggest new scenario additions, open a ticket in [GitHub Issues](../../issues).
* **Code Contributions:** Submissions containing sample Python automation scripts or CML automated topology labs are welcome via Pull Requests.

---
*Maintained by the Cisco Certified Curriculum Community. Contributions and pull requests are welcomed.*
