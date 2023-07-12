# Introduction

In this lab, we will begin by providing a brief introduction to FortiSOAR. FortiSOAR is a comprehensive Security Orchestration, Automation, and Response (SOAR) platform. It offers a centralized and integrated solution for managing security incidents, automating response actions, and streamlining security operations.

After familiarizing ourselves with FortiSOAR, we will proceed with a series of initial steps to get acquainted with the platform's interface and functionalities. This will help you gain a better understanding of how FortiSOAR operates and how it can be utilized in real-world scenarios.

Once we have covered the foundational aspects of FortiSOAR, we will move on to the core focus of this lab: developing your own playbook to respond to an incident reported by FortiClient EMS.

By combining the knowledge and skills acquired from previous labs, specifically the Zero Trust Network Access (ZTNA) lab with FortiWeb, you will create a playbook tailored to react efficiently to the incident. This will involve integrating the insights gained from the ZTNA lab, where you explored the secure access capabilities of FortiWeb.

During the playbook development process, you will leverage FortiSOAR's capabilities to automate response actions based on predefined conditions and triggers. These actions may include gathering additional information, analyzing the incident's severity, identifying affected endpoints, and implementing appropriate remediation measures.

By the end of this lab, you will have hands-on experience in utilizing FortiSOAR's features to create a customized playbook that combines the knowledge obtained from previous labs, particularly the ZTNA lab with FortiWeb. This exercise will help you understand the practical application of FortiSOAR in incident response and further enhance your proficiency in leveraging security automation tools.

# Overview

This lab is built as an extension of the previous Lab (ZTNA with FortiWeb). Additionally, FortiAnalyzer & FortiSOAR will be introduced as tools to solve the upcoming task.

There is no need to apply any additional Setup steps as the infrastructure already got deployed at the beginning of the previous Lab.

As similar Scenario is show in the following video by the Product Manager of FortiSOAR. In this Video, Malicious traffic got detected from a specific Workstation. With the help of FortiSOAR and the ML Engine, the SOC analyst get provided with various quick actions and additional information which helps to Quarentine the Client and mitigate the issue within seconds.

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/etWghPmIuCg?controls=0" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## Lab Architecture Diagram

This Diagram provides a high-level overview of the deployed Systems and the corresponding internal IP addresses.

FortiSOAR is deployed as a Management Extension on FortiAnalyzer VM.

   ![Lab Diagram](./assets/cselab.png)

# Prepare FortiAnalyzer and FortiWeb for the Lab

This Chapter includes necessary steps to setup Log shipping and Event Monitoring with FortiAnalyzer of FortiWeb. Please make sure you have completed this section before moving on!

## FortiWeb Preparations

1. Login to FortiWeb with the give credentials

![image-20230709100007610](./assets/image-20230709100007610.png)

2. On the left sided menu, goto `Log&Report` - `Log Policy` - `FortiAnalyzer Policy`

![image-20230709100136898](./assets/image-20230709100136898.png)

3. Select `Create New` at the top left to open the Configuration Wizard

![image-20230709100218505](./assets/image-20230709100218505.png)

4. Provide a meaningful name for the Policy and the click `OK` to save

![image-20230709100329362](./assets/image-20230709100329362.png)

5. After the Policy has been saved, click on `Create New` to a add a new entry to the policy
6. Enter the IP Address of Fortianalyzer into the corresponding field, then click on `OK` to add the entry

![image-20230709100501970](./assets/image-20230709100501970.png)

7. Check that the new entry was added successfully, click `OK` again to make sure that everything is saved.

![image-20230709100708834](./assets/image-20230709100708834.png)

8. On the left sided menu, goto `Log&Report` - `Log Config` - `Global Log Settings`

![image-20230709100817629](./assets/image-20230709100817629.png)

9. Enable FortiAnalyzer and select the previous configured FortiAnalyzer Policy

![image-20230709100858606](./assets/image-20230709100858606.png)

10. Click on `Apply` at the bottom of the Page to save the configuration.

11. To enable the global logging, open the built-in CLI by clicking on the `>_` Symbol the to top right

![image-20230709101052663](./assets/image-20230709101052663.png)

12. Execute the following commands

```shell
config log traffic-log
set status enable
end
```

13. Logging of FortiWeb to FortiAnalyzer is now enabled. Please proceed with the configuration of FortiAnalyzer

## FortiAnalyzer Preparations

1. Login to FortiAnalyzer with the given Credentials

![image-20230709101603138](./assets/image-20230709101603138.png)

2. Goto `Device Manager` and click on `Add Device` to add FortiWeb

![Screenshot 2023-07-09 at 10.17.32](./assets/Screenshot 2023-07-09 at 10.17.32.png)

3. Provide the follwoing Information, then click on `Next` to proceed with the configuration.

   - Name: `FortiWeb`
   - Serial Number: <Serial Number of FortiWeb> (This can be found at the Dashboard of FortiWeb)

   <img src="./assets/Screenshot 2023-07-09 at 10.19.36.png" alt="Screenshot 2023-07-09 at 10.19.36" style="zoom: 50%;" />

   ![image-20230709102101586](./assets/image-20230709102101586.png)

   4. Wait until the Device got added successfully. Then click on `Finish` to close the wizard.

   ![image-20230709102205239](./assets/image-20230709102205239.png)

   5. To finalize the FortiWeb configuration, select the entry from the Device table and click on `Edit`

   ![image-20230709102329079](./assets/image-20230709102329079.png)

   6. Update `Admin User` and `Password` with the given credentials, then click on `OK` to save.

   ![image-20230709102616262](./assets/image-20230709102616262.png)

   

   

   7. To be able to feed Security Events within FortiSOAR, Events need to get generated within the Event Monitor. For this to work, a so called Handler needs to be in Place. The Handler for FortiWeb is disabled by default and needs to be enabled. For this, goto `Incidents & Events` - `Handlers`

   ![image-20230709102903218](./assets/image-20230709102903218.png)

   8. Select the `Basic Handlers` Tab, then use the Search field at the top right to search for `FWB`

   ![image-20230709103021477](./assets/image-20230709103021477.png)

   9. Right click on the search result, click on `Enable` to activate the handler.

   ![image-20230709103116772](./assets/image-20230709103116772.png) 

   10. Check that the Status changes from `disabled` to `enabled` (green checkmark)

   ![image-20230709103200793](./assets/image-20230709103200793.png)#

   11. As soon as FortiWeb detects an attack, a new Event entry will get added. See the following Example:

   ![image-20230709103500888](./assets/image-20230709103500888.png)

   Please make sure, that a `Web Protection Profile` is used within the configured FortiWeb Policy. The default policies provided by FortiWeb are more than enough with regards to this lab.

   ![image-20230709103720408](./assets/image-20230709103720408.png)

12. Congratulations, you are done with the preparations. Please continue to the next Section of the Lab.
