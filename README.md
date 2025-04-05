# K8s-Cluster-failed
K8s Cluster failed
This guide outlines the steps to identify and resolve issues when an Azure Kubernetes Service (AKS) cluster enters a Failed provisioning or operational state.

🔎 1. Verify Cluster Status
Start by checking the current status of the AKS cluster using the Azure CLI:

bash
Copy
Edit
az aks show --resource-group <RESOURCE_GROUP> --name <CLUSTER_NAME> --output table
Pay attention to the provisioningState field.

Expected: Succeeded

If Failed, look for status or statusMessage fields to gather details about the failure.

📋 2. Review Azure Activity Logs
Check Azure activity logs to understand what might have caused the failure:

Navigate to Azure Portal > Monitor > Activity Log.

Apply filters for the relevant resource group.

Look for events marked as Error or Failed.

This helps identify provisioning issues such as quota limitations, denied access, or policy conflicts.

🧱 3. Inspect Node Pools and Virtual Machine Scale Sets (VMSS)
Problems with underlying VMSS or node pools may lead to overall cluster failures.

To check node pools:

bash
Copy
Edit
az aks nodepool list --resource-group <RESOURCE_GROUP> --cluster-name <CLUSTER_NAME> --output table
To inspect VMSS instance statuses (if using VMSS-backed node pools):

bash
Copy
Edit
az vmss list-instances --resource-group <RESOURCE_GROUP> --name <VMSS_NAME> --output table
Look for instances in a failed or unavailable state.

🚧 4. Common Root Causes to Consider
Evaluate the following common issues that can lead to cluster failure:

VNet/Subnet exhaustion – No available IPs for new nodes.

Regional resource quotas exceeded – Check CPU, storage, or NIC limits.

Identity/permissions issues – Problems with the service principal or managed identity.

Version compatibility – Node pool images or OS versions may be outdated or mismatched.

🔄 5. Attempt Cluster Recovery Actions
Depending on the failure scenario, you may try the following recovery options:

Restart the Cluster
bash
Copy
Edit
az aks start --resource-group <RESOURCE_GROUP> --name <CLUSTER_NAME>
Upgrade or Refresh Node Pools
bash
Copy
Edit
az aks nodepool upgrade \
  --resource-group <RESOURCE_GROUP> \
  --cluster-name <CLUSTER_NAME> \
  --name <NODEPOOL_NAME> \
  --kubernetes-version <VERSION> \
  --mode Upgrade
Note: Use --no-wait for asynchronous upgrade if needed.

🩺 6. Run Diagnostic Commands
Use the built-in diagnostic tools for AKS to validate configuration and health:

bash
Copy
Edit
az aks check-acr --resource-group <RESOURCE_GROUP> --name <CLUSTER_NAME> --acr <ACR_NAME>
bash
Copy
Edit
az aks diagnose --resource-group <RESOURCE_GROUP> --name <CLUSTER_NAME>
These can help uncover misconfigurations or integration issues with container registries, networking, etc.

🆘 7. Contact Microsoft Support (If Needed)
If the cluster cannot be recovered using the steps above, especially in production environments, create a high-severity support request:

bash
Copy
Edit
az support ticket create --problem-class-id 100029 --severity "Critical"
✅ Best Practices
Ensure subnet sizing accounts for scaling needs.

Regularly monitor Azure resource usage and limits.

Maintain identity (SP/MID) lifecycle awareness.

Keep AKS versions and node images up-to-date.
