# OCP+ACM meets Ansible Automation Platform

Here we go to see the way to talk from an OpenShift cluster with an Ansible Automation Platform Controller (aka Ansible Tower)

# Prerequisites
- Red Hat OpenShift Container Platform 4.5 or later
- You must have Ansible Tower version 3.7.3 or a later version installed. It is best practice to install the latest supported version of Ansible Tower. See Red Hat Ansible Tower documentation for more details.
- Install the Ansible Automation Platform Resource Operator on to your hub cluster to connect Ansible jobs to the governance framework. For best results when using the AnsibleJob to launch Ansible Tower jobs, the Ansible Tower job template should be idempotent when it is run. If you do not have Ansible Automation Platform Resource Operator, you can find it from the Red Hat OpenShift Container Platform OperatorHub page.

# Ansible Tower

Only for demo purposes we are going to use the AAP Course, available at the [Red Hat University](https://start.learning.redhat.com/totara/3747
)

Step by step, [here.](AnsibleTowerInstance.md)

# Integrate the OCP Cluster with Ansible Tower

Time to deploy the Ansible Automation Platform Resource Operator, cluster scoped

- Namespace where deploy the Operator
````bash
---
apiVersion: v1
kind: Namespace
metadata:
  name: ansible-automation-platform
  labels:
    operators.coreos.com/ansible-automation-platform-operator.ansible-automation-platfor: ''
````
- Subscription to the Operator Channel
````bash
---
apiVersion: operators.coreos.com/v1alpha1
kind: Subscription
metadata:
  labels:
    operators.coreos.com/ansible-automation-platform-operator.ansible-automation-platfor: ''
  name: ansible-automation-platform-operator
  namespace: ansible-automation-platform
spec:
  channel: stable-2.1-cluster-scoped
  installPlanApproval: Manual
  name: ansible-automation-platform-operator
  source: redhat-operators
  sourceNamespace: openshift-marketplace
  startingCSV: aap-operator.v2.1.1-0.1645503271
````
- Operator Group
````bash
---
apiVersion: operators.coreos.com/v1
kind: OperatorGroup
metadata:
  annotations:
    olm.providedAPIs: >-
      AnsibleJob.v1alpha1.tower.ansible.com,AutomationController.v1beta1.automationcontroller.ansible.com,AutomationControllerBackup.v1beta1.automationcontroller.ansible.com,AutomationControllerRestore.v1beta1.automationcontroller.ansible.com,AutomationHub.v1beta1.automationhub.ansible.com,AutomationHubBackup.v1beta1.automationhub.ansible.com,AutomationHubRestore.v1beta1.automationhub.ansible.com,JobTemplate.v1alpha1.tower.ansible.com
  name: ansible-automation-platform
  namespace: ansible-automation-platform
spec: {}
````
- Obtain and approve the Install Plan
````bash
oc patch installplan "$(oc get installplan -n ansible-automation-platform -o 'jsonpath={..metadata.name}')" \
    -n ansible-automation-platform \
    --type merge \
    --patch '{"spec":{"approved":true}}'
````

# Default integration

All the integration needs use an OpenShift secret, this secret contains the Ansible Tower API url and the token to be used.

This secret is needed in all namespaces that going to be execute a remote Ansible Job Template.

# Simple AnsibleJob

````bash
---
apiVersion: tower.ansible.com/v1alpha1
kind: AnsibleJob
metadata:
  generateName: ansible-notification-
  namespace: individual-ansible-job
spec:
  tower_auth_secret: tower
  job_template_name: Remote Notificator
  extra_vars:
    triggered_by: Aura
    notification: email
    to: danifernandezs@redhat.com
````

# Governace policy automation

````bash

````


# References

- https://access.redhat.com/documentation/en-us/red_hat_advanced_cluster_management_for_kubernetes/2.4/html/governance/governance#configuring-governance-ansible
- https://start.learning.redhat.com/totara/3747

## License

This work is under [Creative Commons Attribution-ShareAlike 4.0 International License](http://creativecommons.org/licenses/by-sa/4.0/).

Please read the [LICENSE](LICENSE) file for more details.
