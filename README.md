# Intro
You can use the following steps to install an agent that will run on one of your Kubernetes clusters to collect audit flow logs from your IBM Cloud Account. These audit flow logs are stored in your Cloud Object Storage (COS) instance. Then, you can enable Security Advisor Activity Insights to analyze your activity flow logs to detect and alert you to suspicious activity.

# Versions 
- `v2.0` : Follow the steps to [install](https://github.com/ibm-cloud-security/security-advisor-activity-insights/tree/master/v2.0#prerequisites) `v2.0`. This is supported on clusters with version "">=1.10.3" and <= v1.15.10". It is supported only on Helm v2
- `v3.0` : New customer are advised to install this version. It supports Kuberenetes version ">=v1.16.7" with Helm v3. Helm v2 is not supported. Installation steps can be found [here](https://github.com/ibm-cloud-security/security-advisor-activity-insights/tree/master/v3.0#prerequisites)
