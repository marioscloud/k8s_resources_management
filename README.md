Kubernetes Resource Management: Configuring CPU and Memory Limits
An essential part of cloud-native engineering is ensuring applications are "good citizens" within a cluster. This project demonstrates how to configure Resource Requests in Kubernetes to prevent application resource exhaustion and ensure cluster stability.


🚀 Overview
In a multi-tenant Kubernetes or OpenShift environment, an application without resource constraints can consume all available compute power, leading to "noisy neighbor" issues or node failure. This project walks through:

-Defining CPU and Memory requests.

-Debugging FailedScheduling events due to insufficient resources.

-Optimizing resource footprints to comply with cluster capacity.


🛠 Tech Stack
-Container Orchestration: Kubernetes / Red Hat OpenShift

-Tools: kubectl, YAML, Linux CLI

-Image Registry: Quay.io


📋 Key Learning Outcomes
-Infrastructure as Code: Generated and modified Deployment manifests.

-Resource Scheduling: Understood how the Kubernetes Scheduler uses "Requests" to place pods on nodes.

-Troubleshooting: Diagnosed Pending pods using field selectors and event logs.

-Metric Units: Utilized millicores (m) for granular CPU allocation.


📖 Execution Steps
*1. Initial Deployment with High Demands*
Initially, a deployment was created requesting 8 CPUs.

kubectl create deployment hello-limit \
--image quay.io/redhattraining/hello-world-nginx:v1.0 \
--dry-run=client -o yaml > hello-limit.yaml

*Resource Block Configuration:*
YAML
resources:
  requests:
    cpu: "8"
    memory: 20Mi
    
*2. Identifying Scheduling Failures*
Because the request exceeded the physical capacity of the cluster nodes, the Pod remained in a Pending state.

Diagnostic Command:

kubectl get events --field-selector type=Warning
Result: Warning FailedScheduling ... 0/3 nodes are available: Insufficient cpu.

*3. Resource Optimization & Redeployment*
The CPU request was optimized to 1.2 CPUs (1200m) to fit within the cluster’s existing resource quotas.

Updated Configuration:
YAML
resources:
  requests:
    cpu: "1000m"
    memory: 20Mi
    
*4. Verification*
After applying the optimized manifest, the scheduler successfully allocated the Pod to a node.

Bash
kubectl get pods
Result: hello-limit-7c7998ff6b-ctsjp  1/1  Running  0  6s

*🧹 Cleanup*
To maintain cluster hygiene, all resources were removed after verification:

Bash
kubectl delete -f hello-limit.yaml
rm hello-limit.yaml

🧠 Reflection
This exercise simulated a real-world scenario where developers must balance application performance with cluster limitations. By adjusting CPU requests from 8 to 1200m, I demonstrated an understanding of the Kubernetes scheduling algorithm and the importance of right-sizing workloads for cost and stability.
