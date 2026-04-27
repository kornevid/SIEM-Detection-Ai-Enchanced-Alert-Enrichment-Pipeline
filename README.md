# Ai-Enchanced SIEM Detection & Alert Enrichment Pipeline

## Objective

The objective of this project was to design and implement a detection and alert enrichment pipeline in a controlled lab environment.

The lab simulates real-world SOC workflows by:

- Collecting logs from a Windows endpoint using Splunk Universal Forwarder
- Ingesting and analyzing telemetry in Splunk SIEM
- Detecting suspicious activity such as failed login attempts (EventCode 4625)
- Triggering alerts and forwarding them via webhook to n8n
- Enriching alerts using AI (ChatGPT) and threat intelligence (AbuseIPDB)
- Delivering structured, actionable alerts to Slack

This project demonstrates how detection, automation, and enrichment can be combined into a modern SOC pipeline.

### Skills Learned

- Implementation and configuration of Splunk SIEM for log ingestion and detection
- Creation of detection rules for Windows security events (e.g., failed logins)
- Understanding of endpoint telemetry and log sources
- Building automated workflows using n8n (webhooks, data processing, integrations)
- Integration of AI for alert analysis and summarization
- Use of threat intelligence (AbuseIPDB) for IOC enrichment
- Experience in designing alert pipelines (Detection → Enrichment → Notification)


### Tools Used

1.**SIEM & Log Management**
- **Splunk Siem** - log ingestion,search and detection
- **Splunk Universal Forwarder** - log collection from Windows Endpoint
- **Windows Event Logs** - authentication andsystem activity monitoring

2.**Automation & Integration**
- **n8n** - workflow automation and alert orchistration
- **Webhooks** - real-time alert forwarding from Splunk

3.**AI & Enrichment**
- **ChatGPT API** - alert analysis,summarization and formating
- **AbuseIPDB** - IP reputation and threat intelligence enrichment

4.**Alerting**
- **Slack** - delivery of structured alerts to SOC channel

5.**Infrastructure**
- **VirtualBox** - Virtual Lab Environment
- **Ubuntu Server** - hosting Splunk and n8n
- **Windows 10** - log source endpoint

6.**Network Configuration**
- **Splunk** - 192.168.0.136
- **n8n** - 192.168.0.67
- **Windows 10** - 192.168.0.241


## Steps
1. **Environment Setup**

- I used VirtualBox as my virtual lab environment.I created several virtual machines - windows 10,a splunk server and an n8n server.

2. **Initial Setup and Server Access**

- First of all on the Ubuntu-based Splunk and n8n servers, I installed the OpenSSH Server package and performed a system update and upgrade.
I then verified that the SSH service was running using the "sudo systemctl status ssh" command, which showed that it was active and listening on port 22.
When I attempted to connect from my host machine, the connection initially failed because the network settings for the virtual machines had not been configured.
To resolve this, I configured the network in VirtualBox to use a bridged adapter, allowing each virtual machine to obtain its own IP address.After that the SSH connection to the Splunk and n8n servers was successful.
<img width="1074" height="617" alt="Screenshot 2026-04-23 124558" src="https://github.com/user-attachments/assets/4169917e-077d-4e0d-9873-74a1c4679b91" /> .
  
3. **Splunk Installation**

- After connecting to the Splunk server via SSH, I downloaded the Splunk Enterprise package.
I then switched to the Splunk user using the command sudo -u splunk bash and started the installation process. During the setup, I configured the administrator credentials for managing Splunk.

<img width="1186" height="398" alt="Screenshot 2026-04-23 130457" src="https://github.com/user-attachments/assets/e8828efc-f48e-4e91-af28-1c1112a27aaf" /> . <img width="1105" height="620" alt="Screenshot 2026-04-23 131007" src="https://github.com/user-attachments/assets/e46997d5-f2ac-4275-af1f-caa8a6cf1520" /> . 


- After returning to my main user account, I enabled Splunk to start automatically at boot for the Splunk user using the command sudo ./splunk enable boot-start -user splunk.

<img width="630" height="22" alt="Screenshot 2026-04-23 142851" src="https://github.com/user-attachments/assets/8e7eed7a-7fc3-4cc6-9307-ef2c642c2454" /> . 

- I then attempted to access the Splunk server at 192.168.0.136 from my host machine, and the connection was successful.
<img width="1867" height="551" alt="Screenshot 2026-04-23 143534" src="https://github.com/user-attachments/assets/b26a220d-0138-493f-81a9-c66699efd93b" /> . 

4. **Splunk Configuration** 

- I configured Splunk to listen on port 9997, which is the default port for receiving forwarded data.
 
<img width="1870" height="348" alt="Screenshot 2026-04-23 143952" src="https://github.com/user-attachments/assets/58d97590-d36c-4fca-a8e5-6002fe2a67df" /> . 

- I then created a new index named project_aut and installed the Windows Event Log add-on.

<img width="618" height="332" alt="Screenshot 2026-04-23 144337" src="https://github.com/user-attachments/assets/3c631291-a9e4-4c5c-846e-fba860b72e30" /> . 

5. **Windows Log Forwarding** 

- Next, I installed the Splunk Universal Forwarder on the Windows machine to enable data collection and forwarding to the Splunk server.
<img width="1000" height="836" alt="Screenshot 2026-04-23 151342" src="https://github.com/user-attachments/assets/76ad45a4-c318-4e3b-a542-06f88bd69232" /> . <img width="490" height="385" alt="Screenshot 2026-04-23 151653" src="https://github.com/user-attachments/assets/ea59c0e5-b605-477d-a6a7-43bf04362088" /> . <img width="1014" height="849" alt="Screenshot 2026-04-23 153639" src="https://github.com/user-attachments/assets/1653f2e4-4dac-45c3-8225-9d36d289b850" /> .

- I downloaded an inputs.conf configuration file and added it to the Program Files\SplunkUniversalForwarder\etc\system\local directory to configure data collection and forwarding to the Splunk server.
<img width="706" height="838" alt="Screenshot 2026-04-27 195845" src="https://github.com/user-attachments/assets/cafa19d0-9440-4299-be3a-bf45d58f6496" /> .

- I verified the functionality of the Splunk deployment by establishing an RDP connection from the host machine to the Windows machine.
<img width="1769" height="967" alt="Screenshot 2026-04-23 172028" src="https://github.com/user-attachments/assets/8b39a28b-3bc1-41f9-a71e-381e6bfb901c" /> .

6. **n8n Deployment**
- I deployed n8n on an Ubuntu server using Docker. After installing and configuring Docker, I created a docker-compose.yaml file, where I defined the necessary service configuration:
<img width="1050" height="321" alt="Screenshot 2026-04-23 175240" src="https://github.com/user-attachments/assets/785a3439-ce18-4fe0-9434-f59dc89629ba" />

- Afterward, I adjusted the file permissions to enable external access to the n8n server. I then launched the service using the command sudo docker-compose up -d.

<img width="1063" height="576" alt="Screenshot 2026-04-23 175622" src="https://github.com/user-attachments/assets/245a1738-5303-4dc5-80ba-fe10df54005c" /> .

7. **Alert Configuration**

- To trigger alerts in Splunk, I intentionally initiated an RDP connection from the host machine to the virtual machine using invalid credentials.
<img width="450" height="463" alt="Screenshot 2026-04-27 202133" src="https://github.com/user-attachments/assets/3f1a29ac-61c1-4533-82c1-24d303f86b82" /> .

- By applying filters in Splunk, I detected a failed login attempt (EventCode=4625) and configured it as an alert.
- <img width="1868" height="585" alt="Screenshot 2026-04-23 185039" src="https://github.com/user-attachments/assets/9a5dcc47-302e-4846-83b5-e8104a7ffa0a" /> . <img width="1868" height="451" alt="Screenshot 2026-04-23 185527" src="https://github.com/user-attachments/assets/2d2d2ded-c0d8-45e0-af6a-b009da9f12f9" /> .

8. **n8n Webhook Integration**
- After that, I configured a webhook in n8n to receive alerts from Splunk and automatically trigger a workflow.
In n8n, I created a Webhook node configured to accept HTTP POST requests and generated a unique test URL endpoint. This endpoint was then used in Splunk as a trigger action for alerts.
On the Splunk side, while configuring the alert, I added a webhook action and specified the n8n webhook URL. The alert was set to trigger when the number of results exceeded zero, ensuring that any detected failed login attempt (EventCode=4625) would send a request to n8n.
Once the alert was triggered, Splunk sent a JSON payload containing event details (such as timestamp, username, source IP, and event count) to the n8n webhook.
In n8n, I verified that the webhook successfully received the data by using the “Listen for test event” option, confirming that the integration was functioning correctly and that the workflow could be executed based on incoming Splunk alerts.
<img width="1811" height="900" alt="Screenshot 2026-04-23 185940" src="https://github.com/user-attachments/assets/c49e3042-9b85-452a-987e-21c1c3506280" /> . <img width="787" height="826" alt="Screenshot 2026-04-23 190008" src="https://github.com/user-attachments/assets/6fb8e5df-1cf7-4a6c-8acf-3ac91e2a7b86" /> . <img width="1122" height="531" alt="Screenshot 2026-04-23 190311" src="https://github.com/user-attachments/assets/ed9e856d-3629-4bee-b916-c74442369b34" /> .

9. **AI Processing** 
- Next, I integrated ChatGPT into the workflow to automate the analysis of security events.
I created a project on the OpenAI platform and generated an API key, which was then added to n8n as a credential.
After that, I included a “Message a Model” node in the workflow and configured it to use the GPT-4.1 model.
Within this node, I defined a prompt that instructs the model to act as a Tier 1 SOC analyst and analyze incoming security events received from Splunk via the webhook. The prompt dynamically incorporates event data, such as the alert name ({{ $json.body.search_name }}), allowing the model to process real-time security information.
This setup enables automated interpretation of alerts, providing contextual analysis of potential security incidents as part of the workflow.
<img width="1409" height="897" alt="Screenshot 2026-04-27 204113" src="https://github.com/user-attachments/assets/93c2350d-a3cd-46c9-bc6b-fa23834f0943" /> .

- I then linked the Webhook node to the “Message a Model” (ChatGPT) node in n8n, allowing incoming Splunk alerts to be automatically processed and analyzed by the model as part of the workflow.
<img width="854" height="483" alt="Screenshot 2026-04-26 152112" src="https://github.com/user-attachments/assets/fae7eb8e-1c83-4778-9b48-764950451951" /> . 

10. **Slack Integration**

- Next, I integrated Slack to enable automated alert notifications.
I configured a Slack bot and assigned the necessary permissions to allow it to send messages to a specific channel.
In n8n, I added a Slack node and configured it to send messages to my designated channel (#alerts).
<img width="1396" height="776" alt="Screenshot 2026-04-26 154634" src="https://github.com/user-attachments/assets/a3b2771e-cd70-4e59-85c3-4c2f921e4e6b" /> . <img width="1421" height="747" alt="Screenshot 2026-04-26 154744" src="https://github.com/user-attachments/assets/4dbe1406-6475-4b82-b5eb-3c280e595df1" /> .

- Initially, I encountered a not_in_channel error, which indicated that the bot had not been added to the channel. After inviting the bot to the channel, the issue was resolved.
I then sent test messages to verify the integration and ensure that notifications were being delivered correctly.
<img width="1029" height="508" alt="Screenshot 2026-04-26 155015" src="https://github.com/user-attachments/assets/4398e690-ba61-431e-8b86-85b9b1c622ff" /> .

- After completing the configuration of all automation components, I executed a test workflow. The workflow successfully processed the alert, analyzed it using the model, and sent the output to Slack, confirming that the entire pipeline was functioning as expected.
<img width="1056" height="712" alt="Screenshot 2026-04-26 171251" src="https://github.com/user-attachments/assets/a4b63310-da22-4e3c-bc86-6d00ae741063" /> . 


11. **Threat Intelligence Enrichment**

- To enhance the workflow with threat intelligence capabilities, I integrated the AbuseIPDB API as a tool within the ChatGPT (“Message a Model”) node.
I configured an HTTP Request node in n8n to query the AbuseIPDB API (/api/v2/check) and pass the source IP address dynamically from the Splunk alert data.
<img width="1390" height="887" alt="Screenshot 2026-04-26 172818" src="https://github.com/user-attachments/assets/3ef27c3e-4ad0-4dc0-9582-bce6f3cdae29" /> .

- Within the prompt, I explicitly instructed the model to use the AbuseIPDB tool when analyzing IP addresses. For testing purposes, I used a known malicious IP address (45[.]131[.]195[.]72) and included it in the prompt as the source IP.

<img width="1233" height="202" alt="Screenshot 2026-04-26 171650" src="https://github.com/user-attachments/assets/6d7251cd-bb16-4921-a877-459dc9e259e6" /> . <img width="1255" height="838" alt="Screenshot 2026-04-26 171709" src="https://github.com/user-attachments/assets/c63c9a13-2e09-48cd-aeeb-f4f00467b52d" /> . <img width="458" height="498" alt="Screenshot 2026-04-26 171746" src="https://github.com/user-attachments/assets/bbf7686d-3717-4063-9a23-d7167ac81d0f" /> . 

- When the workflow is executed, the model automatically calls the AbuseIPDB API to enrich the alert with threat intelligence data, such as abuse confidence score, number of reports, ISP, and usage type.

The model then generates a structured analysis, including:

  - a summary of the detected attack (e.g., brute-force attempt),
  - threat intelligence enrichment based on AbuseIPDB data,
  - severity assessment,and recommended response actions.

<img width="1390" height="887" alt="Screenshot 2026-04-26 172818" src="https://github.com/user-attachments/assets/1e9123df-6ee5-49b9-8750-5a7d226d14ee" /> . <img width="910" height="432" alt="Screenshot 2026-04-26 172945" src="https://github.com/user-attachments/assets/6f1d8b73-82e1-433a-8ef8-3fef16ebde41" /> . 

12. **FInal Results**
- Finally, the enriched and analyzed result is sent to Slack, providing a clear and actionable security alert for further investigation.

This demonstrates a fully automated SOC workflow that combines detection (Splunk), orchestration (n8n), AI-based analysis (ChatGPT), and threat intelligence enrichment (AbuseIPDB).
<img width="1049" height="455" alt="Screenshot 2026-04-26 173537" src="https://github.com/user-attachments/assets/8513a53a-4324-45af-a41e-b1ea4641a943" />

13.**Conclusion**
- This project demonstrates the implementation of a detection and alert enrichment pipeline in a controlled lab environment, simulating key components of a modern SOC workflow.

- Through this lab, I was able to combine log collection, detection engineering, automation, and threat intelligence into a unified process. The integration of AI allowed for improved alert analysis and more structured, actionable outputs.

- While the current implementation focuses on detection and enrichment, it also highlights the foundation for further development into a full incident response and SOAR solution by adding automated remediation actions.

- Overall, this project strengthened my understanding of how security events are generated, detected, enriched, and communicated within a SOC environment.


















