# Network Inventory Report with Ansible

## Purpose

Creates a network inventory report for device versions. If a device version doesn't match what is configured in the "desired_version" variable then it will be highlighted in the report. This will send a report to Mattermost but can be modified to use Slack or other chat applications or documentation tools like Confluence.

## The playbook

Only two modules are needed for this example. The [{os}\_facts module][1] and
the [mattermost module][5]

```
---
- name: Build Arista inventory report
  hosts: eos
  connection: network_cli
  gather_facts:  False

  roles:
    - network_compliance_report
```

## Required Variables

There are 2 ways to create a report, Mattermost and Confluence. Below are the required variables for each.

**Mattermost**

- _mattermost_url_ = the url for the mattermost instance
- _mattermost_token_ = the api token for the mattermost instance
- _mattermost_channel_ = the channel to send the message to

**Confluence**

- **confluence_url**= URL for Confluence
- **confluence_user**= Username for Confluence
- **confluence_token**= API Token for Confluence
- **confluence_title**= Title of the page to create
- **confluence_json_query**= This is the JSON query to parse the Confluence payload for relevant data needed
- **confluence_space**= This is the space that the page will be created (E.G. Network, Dev, etc)

## Example Output

Devices not matching the desired version will have a warning sign next to the version.

![Example Arista Report](https://i.imgur.com/hQoAALw.png)

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
