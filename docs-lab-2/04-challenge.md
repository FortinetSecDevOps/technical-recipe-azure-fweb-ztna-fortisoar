# Tasks - FortiSOAR & FortiClient EMS

## Task 1 - Quarantine Client

As a first Task in this lab, you will develop your own Playbook to quarantine Client on FortiClient EMS. Based on the Informaition you have learned in the Prvious Chapter, the Playbook has to accomplish the following tasks:

- The Information of the Client need to be extracted based on a Security Event / Alert retrived from FortiAnalyzer/FortiWeb

  - To trigger an Attack alert, make sure that a Security profile is configured within the FortiWeb Policy, in addition, feel free to use the following string and just append it to the URL to trigger an Attack Alert

  ```url
  ?q=%27%20or%20data%20@>%20%27{"a":"b"}%27%20--
  ```

- Retrieve more details based on the Alert Information about the Client

- Provide Approval Wizard before Quarantine Client with the following Information

  - FortiClient EMS ID
  - Client IP
  - Reason to Quarantine
  - OS Version
  - FortiClient Version

- Quarantine Client on FortiClient EMS

- Don't forget to think about the case, that the Approval get's rejected.

### Hint

In case you will use the `Get all Endpoints` function of the FortiClient EMS Connector, the following Values are required:

```json
# Verifiction:
{
  "saml_id": "",
  "ldap_ids": []
}

# Filter
<Specify you filter here>
```

## Task 2 - Unquarantine Client

As the last Task, you have developed a Playbook which allows to quarantine a Client via FortiClient EMS. As you may notice, you have lost remote access to the Client. Your next Task is to develop a Second Playbook which will unquarantine the previous Client based in the same Input Values like in Task 1.

For this, the Playbook has to accomplish the following tasks:

- Retrieve more details based on the Alert Information about the Client

- Provide Approval Wizard before Quarantine Client with the following Information

  - FortiClient EMS ID
  - Client IP
  - Reason Client got Quarantined
  - OS Version
  - FortiClient Version

- Unquarantine Client on FortiClient EMS

- Don't forget to think about the case, that the Approval get's rejected.

# Solutions

In this Section you will find a detailed solution of the previos tasks. In addition, there are example Playbooks attached which you can import as a solution.

## Challenge 1 Solution

### Example Playbook Overview

<img src="./assets/image-20230711133047657.png" alt="image-20230711133047657" style="100%;" />

- [Download Example Playbook](./assets/Solution-1-Playbook-(2023711135).json)

### Step Details

1. Stepname: `Start`

This is the Intitial Step. It is configured to start `Manual` and the Scope is defined to be only relevant for `Alerts`

<img src="./assets/image-20230711154414015.png" alt="image-20230711154414015" style="zoom: 67%;" />

2. Stepname: `extract alert source data`

FortiSOAR attaches by default the complete source data to every alert. This includes also non-visible information. As this Playbook extracts the Client IP address and for a better readablity, the source data will be extracted into it's own variable.

<img src="./assets/image-20230711154713835.png" alt="image-20230711154713835" style="zoom: 67%;" />

3. Stepname: `extract client details`

Based on the source data of the alert, the client ip address and the alert subject would be relevant for further use. These get stored within their own variables.

<img src="./assets/image-20230711154824343.png" alt="image-20230711154824343" style="zoom: 67%;" />

4. Stepname: `get all endpoints filtered`

This step will use the FortiClient EMS Connector to retrieve all endpoints but with a filter based on the client ip address based on the information of the alert.

<img src="./assets/image-20230711155330577.png" alt="image-20230711155330577" style="zoom:67%;" />

<img src="./assets/image-20230711155401188.png" alt="image-20230711155401188" style="zoom:67%;" />

5. Stepname: `approve quarantine`

Before quarantine the client, a approval step based on the `Manual Input` Step is defined. This Step will output various details as requested by the Task and proceeds depending on the Button which is selected by the User.

<img src="./assets/image-20230711155636573.png" alt="image-20230711155636573" style="zoom:67%;" />

<img src="./assets/image-20230711155707879.png" alt="image-20230711155707879" style="zoom:67%;" />

<img src="./assets/image-20230711155727082.png" alt="image-20230711155727082" style="zoom:67%;" />

<img src="./assets/image-20230711155739757.png" alt="image-20230711155739757" style="zoom:67%;" />

6. Stepname: `quarantine client`

This step leverages the FortiClient EMS connector to quarantine the Client based on the client id which can be extracted of the result of step `get all endpoints filtered`

<img src="./assets/image-20230711160123665.png" alt="image-20230711160123665" style="zoom:67%;" />



6. Stepname: `cancel execution`

This step is just an empty step which does nothing but ends the execution of the Playbook.

<img src="./assets/image-20230711160033685.png" alt="image-20230711160033685" style="zoom:67%;" />



## Challenge 2 Solution

### Example Playbook Overview

![image-20230711135059383](./assets/image-20230711135059383.png)

- [Download Example Playbook](./assets/Solution-2-Playbook-(2023711135).json)

### Step Details

1. Stepname: `Start`

This is the Intitial Step. It is configured to start `Manual` and the Scope is defined to be only relevant for `Alerts`

<img src="./assets/image-20230711154414015.png" alt="image-20230711154414015" style="zoom: 67%;" />

2. Stepname: `extract alert source data`

FortiSOAR attaches by default the complete source data to every alert. This includes also non-visible information. As this Playbook extracts the Client IP address and for a better readablity, the source data will be extracted into it's own variable.

<img src="./assets/image-20230711154713835.png" alt="image-20230711154713835" style="zoom: 67%;" />

3. Stepname: `extract client details`

Based on the source data of the alert, the client ip address and the alert subject would be relevant for further use. These get stored within their own variables.

<img src="./assets/image-20230711154824343.png" alt="image-20230711154824343" style="zoom: 67%;" />

4. Stepname: `get all endpoints filtered`

This step will use the FortiClient EMS Connector to retrieve all endpoints but with a filter based on the client ip address based on the information of the alert.

<img src="./assets/image-20230711155330577.png" alt="image-20230711155330577" style="zoom:67%;" />

<img src="./assets/image-20230711155401188.png" alt="image-20230711155401188" style="zoom:67%;" />

5. Stepname: `approve quarantine`

Before quarantine the client, a approval step based on the `Manual Input` Step is defined. This Step will output various details as requested by the Task and proceeds depending on the Button which is selected by the User.

<img src="./assets/image-20230711155636573.png" alt="image-20230711155636573" style="zoom:67%;" />

<img src="./assets/image-20230711155707879.png" alt="image-20230711155707879" style="zoom:67%;" />

<img src="./assets/image-20230711155727082.png" alt="image-20230711155727082" style="zoom:67%;" />

<img src="./assets/image-20230711155739757.png" alt="image-20230711155739757" style="zoom:67%;" />

6. Stepname: `unquarantine client`

This step leverages the FortiClient EMS connector to unquarantine the Client based on the client id which can be extracted of the result of step `get all endpoints filtered`

<img src="./assets/image-20230711160506558.png" alt="image-20230711160506558" style="zoom:67%;" />



6. Stepname: `cancel execution`

This step is just an empty step which does nothing but ends the execution of the Playbook.

<img src="./assets/image-20230711160033685.png" alt="image-20230711160033685" style="zoom:67%;" />
