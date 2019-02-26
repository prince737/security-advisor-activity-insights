# Intro
You can use the following steps to install an agent that will run on one of your Kubernetes clusters to collect audit flow logs from your IBM Cloud Account. These audit flow logs are stored in your Cloud Object Storage (COS) instance. Then, you can enable Security Advisor Activity Insights to analyze your activity flow logs to detect and alert you to suspicious activity.

# Prerequisites
- For Windows 10, Before starting with steps mentioned above, activate WSL (Windows Subsystem for Linux) and install [Ubuntu shell](https://win10faq.com/install-run-ubuntu-bash-windows-10/)
- yq CLI
  - For MacOS, Windows 10: Install [yq CLI](http://mikefarah.github.io/yq/)
  - For CentOS, Red Hat and Ubuntu : Install yq CLI version 1.15 by running the folowing commands:      
  `wget https://github.com/mikefarah/yq/releases/download/1.15.0/yq_linux_amd64`       
  `mv yq_linux_amd64 yq`     
  `chmod +x yq`     
  `mv yq /usr/local/bin/`       
  `yq -V`       
- cURL binary
  - For CentOS and Red Hat, update cURL binary using `yum update -y nss curl libcurl`
- Install [Kubernetes CLI (kubectl)](https://kubernetes.io/docs/tasks/tools/install-kubectl/) v1.10.11 or higher
- Install [Kubernetes Helm (package manager)](https://docs.helm.sh/using_helm/#from-script) v2.9.0 or higher
- Please verify whether Helm TLS is enabled before proceeding with installation. It is recommended to [enable TLS](https://github.com/helm/helm/blob/master/docs/tiller_ssl.md) in your Helm Tiller.                        
  **Note**:            
    - If you are using workstation to handle installation of analytics components in multiple clusters and Helm TLS is enabled, be sure that the TLS configuration in your workstation is current and corresponds to the cluster where you are planing to install these components.

# Steps to run
1. Download the tar file.
2. Unzip the file by running `tar -xvf security-advisor-activity-insights.tar` in your terminal.
3. Change into the file directory. `cd security-advisor-activity-insights`
4. Run the install command. 
    ```
    ./activity-insight-install.sh <cos_region> <cos_api_key> <at_region> <account_api_key> <account_spaces>
    ```
     - The `<cos_region>` value is the region in which your COS is deployed. Options include `us-south` or `eu-gb`.
     - The <cos_api_key> value is the [api key](https://cloud.ibm.com/docs/services/cloud-object-storage/iam?topic=cloud-object-storage-service-credentials#service-credentials) that you created to access your COS instance and bucket. The key should have the Writer IAM service role.
     - The <at_region> value is either us-south or eu-gb - the region of the [Activity Tracker](https://cloud.ibm.com/docs/services/cloud-activity-tracker?topic=cloud-activity-tracker-getting-started-with-cla#getting-started-with-cla) instance
     - The <account_api_key> region is the platform [api key](https://cloud.ibm.com/docs/iam?topic=iam-manapikey#ibm-cloud-api-keys) of your IBM Cloud account
     - <account_spaces> comma seperate list of [IBM account space GUID](https://cloud.ibm.com/docs/cli/reference/ibmcloud?topic=cloud-cli-ibmcloud_commands_account#ibmcloud_account_space)
5. Verify the installation :
     - `helm ls | grep activity-insights` should return a helm release named `activity-insights` in DEPLOYED state, use `--tls` flag if helm is TLS enabled.
     - `kubectl get pods -n security-advisor-insights | grep activity-insights` should return one pod related to `activity-insights` in the state "RUNNING".
     **Note**: If you create your COS instance and bucket outside of the Security Advisor UI, be sure to use the following naming convention for the bucket: `sa.<account_id>.telemetric.<cos_region>`. Also be sure to set up service-to-service [authorization](https://cloud.ibm.com/docs/iam?topic=iam-serviceauth#serviceauth) in IBM Cloud IAM to give Security Advisor permission to read data from your COS instance. Set the `source` service to Security Advisor and the `target` service to your  Cloud Object Storage instance with a `Reader` IAM role.   
6. Take the rule packages and upload to your cos bucket. A default set of rule packages can be found [here](https://github.ibm.com/security-services/security-advisor-project-management/wiki/Doc-page-for-Activity-Insights).

# Deleting the setup
1. `helm del --purge activity-insights` , use `--tls` flag if helm is TLS enabled.
2. `kubectl delete ns security-advisor-insights`

# Troubleshooting
1. If you get an error similar to `Error: incompatible versions client and server`, run `helm init --upgrade`
2. If you get an error similar to `namespaces security-advisor-insights is forbidden: User system:serviceaccount:kube-system:default cannot get resource namespaces in API group in thenamespace security-advisor-insights`, fix the [helm setup](https://cloud.ibm.com/docs/containers?topic=containers-integrations#helm), as the kube-system default service account does not have the cluster-admin access in your cluster.
