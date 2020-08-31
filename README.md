# Network Inventory Report with Ansible

## Purpose

Creates a compliance report for network devices . In this example if a device's version, NTP, AAA or Syslog doesn't match what is configured in the variables then it will be highlighted in the report. This role is configured to only send reports to Confluence and Mattermost at this time.

## Notes

1. This playbook assumes that the devices are in a group respective to their OS (ASAs are in group asa, Aristas are in group eos, etc).
2. In the group_vars folder is {os}.yml and within that is the desired_version variable.
3. The server, Confluence and Mattermost variables are found in defaults/main.yml
4. If there is not a task for your OS you can create one, just make sure you follow the structure of the other devices to ensure a uniform variable set.
5. If you are setting the tokens as variables, (recommended) then make sure to remove **confluence_token** and **mattermost_token** from defaults/main.yml
6. Confluence is assumed to be an Atlassian hosted solution, you may have to modify the URI call if its an on-premise device.

## The playbook

```
---
- name: Build Network Compliance Report
  hosts: all
  gather_facts: False

  roles:
    - network_compliance_report
```

## Structure

The playbook has 2 templates for Confluence and Mattermost depending if you pass in an entire inventory or if you do a limit.

- **all**: Will loop through all groups in the inventory except for All and Ungrouped. Make sure each device has its OS task otherwise it will error out
- **limit**: Will loop through only the groups in the ansible_limit specified at runtime

## Required Variables

There are currently 2 ways to create a report, Mattermost and Confluence. Below are the required variables for each which can be found in "Defaults"

I strongly recommend vaulting the API tokens! In Ansible Tower use a [Custom Credential Type](https://www.ansible.com/blog/ansible-tower-feature-spotlight-custom-credentials) Please see step 5 in notes and make sure you remove these if you set them as vars.

### OS Versions

These should be placed in vars/main.yml

Specifies the desired version for the devices to run. Ansible will compare this version to the current version to determine if it's compliant.

Examples:

- **asa_version**: "9.8(4)26"
- **eos_version**: "4.24.1.1F"
- **f5_version**: "15.1.0.2"
- **ios_version**: "16.12.03"
- **panos_version**: "9.0.0"

### Server Vars

These should be placed in vars/main.yml

- **aaa_servers**: List of AAA servers to check against
- **snmp_servers**: List of SNMP servers to check against
- **ntp_servers**: List of NTP servers to check against
- **syslog_servers**: List of Syslog servers to check against
- **snmp_servers**: List of SNMP servers to check against
- **dns_servers**: List of DNS servers to check against

### Mattermost

These should be placed in defaults/main.yml

- **enable_mattermost**: (yes/no) Includes the Mattermost tasks
- **mattermost_url**: the url for the mattermost instance
- **mattermost_token**: the api token for the mattermost instance
- **mattermost_channel**: the channel to send the message to

### Confluence

These should be placed in defaults/main.yml

- **enable_confluence**: (yes/no) Includes the Confluence tasks
- **confluence_url**: Base URL for confluence (myconfluence.atlassian.com)
- **confluence_user**: Username for Confluence
- **confluence_token**: API Token for Confluence
- **confluence_title**: Title of the page to create
- **confluence_space**: This is the space that the page will be created (E.G. Network, Dev, etc)

## Example Output

Devices not matching the variables for each check will exhibit an ! if the version is off or X if the servers are missing.

### Confluence Output

![Example Confluence Report](https://i.imgur.com/kR7m6E8.png)

### Mattermost Output

![Example Mattermost Report](https://i.imgur.com/JkPm4I4.png)

---

![Red Hat Ansible Automation][6]

Red Hat® Ansible® Automation consists of three products:

- [Red Hat® Ansible® Tower][7]: Built for operationalizing and scaling
  automation, managing complex deployments and speeding up productivity. Extend
  the power of Ansible Tower with Workflows and Surveys to streamline jobs and
  simple tools to share solutions with your team.

- [Red Hat® Ansible® Engine][8]: a fully supported product built on the
  foundational capabilities of the Ansible project. Also provides support for
  select modules including Infoblox.

- [Red Hat® Ansible® Network Automation][9]: provides support for select
  networking modules from Arista (EOS), Cisco (IOS, IOS XR, NX-OS), Juniper
  (JunOS), Open vSwitch, and VyOS. Includes Ansible Tower, Ansible Engine, and
  curated content specifically for network use cases.

[1]: http://docs.ansible.com/ansible/latest/nxos_facts_module.html
[2]: http://docs.ansible.com/ansible/latest/list_of_network_modules.html
[3]: images/htmlreport.png
[4]: http://docs.ansible.com/ansible/latest/ios_facts_module.html
[5]: http://docs.ansible.com/ansible/latest/template_module.html
[6]: images/rh-ansible-automation.png
[7]: https://www.ansible.com/tower
[8]: https://www.ansible.com/ansible-engine
[9]: https://www.ansible.com/networking
