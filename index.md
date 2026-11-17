1. Introduction (Building Better Visibility: How Additional Telemetry Improved My Wazuh SIEM Deployment)
Fill this part of your blog out in the 1. Introduction note.

One of the most important lessons I've learned while studying cybersecurity is that successful threat detection depends on visibility. Security analysts can only investigate what they can observe, which makes log collection and telemetry management foundational components of any Security Information and Event Management (SIEM) deployment.

For my Sprint 11 project, I built upon an existing Wazuh environment by implementing a modification that expanded endpoint monitoring capabilities on the AD01 Windows Server. The environment already collected Sysmon and Windows Event Logs, but I wanted to determine whether Microsoft Defender Operational logs could provide additional context during security investigations.

To validate the value of this new telemetry source, I conducted three MITRE ATT&CK-aligned experiments involving PowerShell downloads, archive creation, and scheduled task creation. These exercises helped me understand how Sysmon, Windows Defender, and Wazuh work together to improve detection visibility while also highlighting areas where logging gaps may still exist.

By the end of this project, I discovered that layered telemetry provides significantly more investigative value than relying on a single log source alone.

🌿 EXAMPLE 🌿: Alex the nurse (and true crime fan) enters infosec!

2. Setup (Modifying the Wazuh Deployment)
The primary objective of this project was to enhance endpoint visibility by ingesting Windows Defender Operational logs into Wazuh from the AD01 Windows Server.

The baseline environment already collected:

Windows Event Logs
Sysmon telemetry
Standard Wazuh monitoring data
However, Microsoft Defender generates additional security-relevant events that can provide behavioral insights into potentially malicious activity. My goal was to determine whether these events would complement the existing telemetry already being collected.

Replicable Procedure
To implement this modification, I performed the following steps:

Logged into the AD01 Windows Server.
Opened the Wazuh agent configuration file.
Added monitoring for the Windows Defender Operational Event Channel using the following configuration:
XML

Microsoft-Windows-Windows Defender/Operational

<log_format>eventchannel</log_format>

Saved the configuration file.
Restarted the Wazuh agent service.
Generated test activity on the endpoint.
Verified that Windows Defender Operational events appeared within the Wazuh dashboard.
Confirmed successful event parsing and ingestion.
Mistake Made During Setup
One mistake I made during implementation was expecting Defender events to immediately appear after modifying the configuration file. Initially, no events appeared in the dashboard, which led me to believe the configuration was incorrect.

After reviewing the configuration and restarting the Wazuh agent again, I realized the issue was related to validation timing rather than the XML syntax itself. This reinforced the importance of methodically confirming configuration changes before assuming a deployment failure.

Use the Chosen Changelogs note to keep track of the most valuable changelogs associated with this part of the process.

Tip

You must also describe at least one (1) mistake you’ve made in the process, along with how you identified that there was a problem and then worked out a correct solution. (Review your documented mistakes to choose the most interesting one(s) to showcase in your story.)

🌿 A very succinct example (shortened to illustrate general flow) 🌿

3. Experiment time!
You must describe at least three (3) experiments you ran after standing up the deployment to see what kinds of events appear in the logs.

This is the most fun part of the blog post!
These are the experiments described in Experiment 1 - PowerShell Download Activity, Experiment 2 - Archive Creation Activity, Experiment 3 -Scheduled Task Creation.
Use the Chosen Changelogs note to keep track of the most valuable changelogs associated with this part of the process.

Tip

You must also describe at least one (1) mistake you’ve made in the process, along with how you identified that there was a problem and then worked out a correct solution. (Review your documented mistakes to choose the most interesting one(s) to showcase in your story.)

Experiment #1- Observe PowerShell Download Activity (MITRE ATT&CK T1105 - Ingress Tool Transfer)
After configuring the Defender log collection, I will execute a PowerShell download command from AD01 and observe what telemetry is generated across Sysmon, Windows Defender, and Wazuh.

Example activity:

Invoke-WebRequest -Uri https://example.com/file.txt -OutFile file.txt

The purpose of this experiment is to determine whether Defender generates security-relevant alerts for PowerShell-based download behavior and compare those findings against the Sysmon process creation events already being collected.

I expect to observe:

PowerShell process creation events
Network connection activity
Defender behavioral monitoring events
Wazuh correlation opportunities
### References to use while conducting
- MITRE ATT&CK T1105  
  
- Atomic Red Team  
  
- Microsoft Defender Documentation  
  
- Sysmon Event ID Documentation
🔬 Experiment #2 - Observe Archive Creation Activity (MITRE ATT&CK T1560.001 - Archive Collected Data)
For the second experiment, I will simulate a common attacker staging technique by creating a compressed archive using PowerShell's built-in compression functionality.

Example activity:

PowerShell

Compress-Archive -Path C:\Users\Public\Documents -DestinationPath C:\Temp\data.zip

This experiment interests me because data staging often occurs before exfiltration. I want to determine whether Sysmon, Defender, and Wazuh generate meaningful telemetry that would help an analyst identify suspicious archive creation activity.

I will investigate:

Process creation events
File creation activity
Defender detections or alerts
Wazuh rule matches
### References to use while implementing
- MITRE ATT&CK T1560.001  
  
- Atomic Red Team  
  
- Wazuh Rules Documentation  
  
- Sysmon Event Reference
🔬 Experiment #3 - Observe Scheduled Task Creation (MITRE ATT&CK T1053.005 - Scheduled Task/Job)
For the third experiment, I will create a scheduled task on AD01 to simulate an attacker attempting to establish persistence.

Example activity:

BAT

schtasks /create /tn "LabTask" /tr "notepad.exe" /sc daily /st 12:00

Persistence mechanisms are particularly interesting because they are commonly used by real-world threat actors. I want to determine which telemetry sources generate the most useful evidence for detecting scheduled task creation.

I will compare:

Sysmon process creation logs
Task Scheduler operational logs
Windows Defender telemetry
Wazuh alerts and event correlation
This experiment will also help identify logging gaps and determine whether additional audit settings should be enabled.

### References to use while implementing	
- MITRE ATT&CK T1053.005  
  
- Atomic Red Team  
  
- Microsoft Task Scheduler Logging Documentation  
  
- Wazuh Windows Monitoring Guide	**.]`
4. Conclusion
The conclusion to your blog post should include:

A brief overview of the findings from each of your experiments.
A warning about how to avoid making the same mistakes you made in Setup and Experiment time.
This project successfully achieved the objective outlined in my Sprint 11 Project Plan Proposal: expanding visibility within a Wazuh deployment through the addition of Windows Defender Operational event logging.

Across all three experiments, I observed that Defender telemetry complemented existing Sysmon and Windows logging sources. Rather than replacing existing visibility, the new telemetry enriched investigations by providing additional behavioral context.

The experiments also highlighted several important lessons:

PowerShell download activity generates useful artifacts across multiple logging sources.
Archive creation can become a meaningful indicator when correlated with other behavior.
Scheduled task creation produces numerous artifacts that can assist with persistence detection.
Log correlation is significantly more valuable than relying on a single telemetry source.
I sure learned my lesson — it's worth double-checking that your configuration file saved correctly before you spend hours beating your head against the wall wondering why the agent isn't working properly!

Advice for Avoiding My Mistakes
If you plan to conduct a similar project:

Validate Wazuh agent configuration carefully before troubleshooting.
Restart services after making configuration changes.
Do not assume a missing alert means logging failed.
Focus on telemetry collection and correlation, not just detections.
Document every configuration change throughout the project.
These practices will save time and improve the quality of your investigation results.

5. Final thoughts
5.1 The coolest thing you learned
Tell us about the coolest thing you learned during this project.

The coolest thing I learned was how different telemetry sources complement one another. Before this project, I viewed Sysmon, Windows Defender, and Wazuh as separate tools. After conducting these experiments, I realized they create a much stronger detection capability when working together.

5.2 One piece of advice
At least one (1) piece of advice you’d give to anybody embarking on the same sort of project: what do you wish you’d known before starting your own SIEM deployment journey? Document it here.

Don't become overly focused on alerts. Learn how telemetry is generated, where it originates, and how multiple log sources can be correlated. Understanding the data is often more valuable than simply receiving an alert notification.

5.3 Your favorite resource
Highlight at least one (1) great external resource you discovered while undertaking this journey. Explain why you found it interesting, how you discovered it (A tutor? A link from another resource? A Twitter thread?), and how you used it to help you understand or accomplish something. (Use the References note to keep track of useful resources.)

My favorite resource was the Wazuh documentation. The documentation provided clear guidance for configuring event collection, understanding agent behavior, and validating log ingestion during the project.

Resource: Wazuh Documentation - Windows Event Channel Monitoring

5.4 Thank somebody!
Explicitly thank at least two (2) external security researchers whose work helped you in some way during this process. You could even have a paragraph showing gratitude to each of the researchers whose work you used in some way during your project. But at the very least, at minimum, recognize by name at least two (2). (Use the Gratitudes note to keep track of opportunities to thank others as you think of them.)

Gratitude is the gift that keeps on giving

Acknowledging who helped you is a standard way of showing respect in the cybersecurity community — and it’s a great way to make new friends. Sometimes you’ll even find a new mentor by tipping your hat to the researchers whose work helped you! The more sincere and thoughtful your note of gratitude, the more likely you’ll make new friends in the industry through your post.

I would like to thank:

The TripleTen Cybersecurity Program instructors and reviewers for providing guidance and helping me develop practical SIEM experience.
The Wazuh and open-source security communities for maintaining documentation and educational resources that supported this project.
6. Annotated resources
You must include an annotated list of references you found useful while completing this project.

You should review your References summary to see a full list of all the resources you've added to this vault.

Pick and choose the top 5–25 entries listed here.
The more references you include, the better. (Your blog post will be more likely to appear in other people's search results if you include lots of links to reputable cyber security-related content in your post.)
Tip

Think of the audience for these resources as fellow students learning the same thing as you — you’re not justifying your choices to a teacher; you’re trying to light the way for anybody else trying to retrace your footsteps.

1. Wazuh Documentation – Windows Event Channel Monitoring
Author: Wazuh
Affiliation: Wazuh Inc.
Date Published: Continuously Updated
Description: Provided instructions for monitoring Windows event channels and configuring Windows log collection within Wazuh.
2. Wazuh Decoder Documentation
Author: Wazuh
Affiliation: Wazuh Inc.
Date Published: Continuously Updated
Description: Helped explain how Wazuh interprets and processes Windows event data after ingestion.
3. Microsoft Defender Operational Event Log Documentation
Author: Microsoft
Affiliation: Microsoft Corporation
Date Published: Continuously Updated
Description: Provided information about Defender-generated telemetry and operational event logging behavior.
4. MITRE ATT&CK Framework
Author: MITRE Corporation
Affiliation: MITRE
Date Published: Continuously Updated
Description: Used to map each experiment to real-world adversary techniques including T1105, T1560.001, and T1053.005.
5. Atomic Red Team
Author: Red Canary
Affiliation: Red Canary
Date Published: Continuously Updated
Description: Provided adversary emulation concepts and testing methodologies that informed the experiment design.
6. Sysmon Event Reference
Author: Microsoft
Affiliation: Microsoft Corporation
Date Published: Continuously Updated
Description: Used to understand process creation, file operation, and other endpoint telemetry generated during testing.
7. Microsoft Task Scheduler Logging Documentation
Author: Microsoft
Affiliation: Microsoft Corporation
Date Published: Continuously Updated
Description: Helped identify relevant scheduled task artifacts and logging locations during persistence testing.
