<div align="center">

# PA4 Submission: TaskFlow Pipeline

<img alt="GitHub only" src="https://img.shields.io/badge/Submit-GitHub%20URL%20Only-10b981?style=for-the-badge">
<img alt="Total points" src="https://img.shields.io/badge/Total-100%20points-7c3aed?style=for-the-badge">

</div>

<div style="background:#f5f3ff;color:#111827;border-left:6px solid #6330bc;padding:14px 18px;border-radius:10px;margin:18px 0;">
Copy this file to <code style="color:#111827;background:#ddd6fe;padding:2px 4px;border-radius:4px;">SUBMISSION.md</code>. Put every screenshot in <code style="color:#111827;background:#ddd6fe;padding:2px 4px;border-radius:4px;">docs/</code>, embed it under the correct task, and write a short description below each image explaining what it proves. The grader should not need any file outside this repository.
</div>

## Student Information

| Field | Value |
|---|---|
| Name | Sana Humayun |
| Roll Number | 26100273 |
| GitHub Repository URL | https://github.com/sanahumayun/CS487-PA4 |
| Resource Group | `rg-sp26-26100273` |
| Assigned Region | `uaenorth` but it had 0 quota when i started so i've used `ukwest` |

## Evidence Rules

- Use relative image paths, for example: `![AKS nodes](docs/aks-nodes.png)`.
- Every image must have a 1-3 sentence description below it.
- Azure Portal screenshots must show the resource name and enough page context to identify the service.
- CLI screenshots must show the command and output.
- Mask secrets such as function keys, ACR passwords, and storage connection strings.


## Task 1: App Service Web App (15 points)

### Evidence 1.1: Forked Repository

![Forked Repository](docs\Task1\Screenshot 2026-04-29 194050.png)

Description: This is my working fork of the CS487-PA4 starter repository under my GitHub account. It contains all four starter folders: webapp, function-app, validate-api, and report-job, along with the README and docs directory.

### Evidence 1.2: App Service Overview

![App Service Overview](docs\Task1\Screenshot 2026-04-29 194118.png)

Description: The Web App `pa4-26100273` is deployed in resource group `rg-sp26-26100273`, UK West region, running on Linux with Node 20 LTS runtime. Status shows Running and the public URL is `pa4-26100273.azurewebsites.net`. Note: the web app was recreated as `pa4-26100273-web` after a plan conflict during Function App deployment; both share the `pa4-26100273` App Service Plan (B1).

### Evidence 1.3: Deployment Center / GitHub Actions

![Deployment Center](docs\Task1\Screenshot 2026-04-29 194840.png)

Description: The Deployment Center shows the Web App is connected to the `main` branch of the forked GitHub repository `sanahumayun/CS487-PA4` using GitHub Actions as the build provider. Every push to main triggers an automatic redeployment.

### Evidence 1.4: Live Web UI

![Live Web UI](docs\Task1\Screenshot 2026-04-29 204653.png)

Description: The TaskFlow order form loads successfully over HTTPS from the App Service URL. The form shows fields for Order ID, SKU, and Quantity, confirming the Node.js frontend is being served correctly by the App Service.

### Evidence 1.5: Application Settings

![Application Settings](docs\Task1\Screenshot 2026-04-29 204741.png)

Description: The Web App environment variables show `FUNCTION_START_URL` and `FUNCTION_STATUS_URL` configured, pointing to the Durable Function HTTP starter endpoint. These are populated in Task 7 after the Function App is deployed.

---

## Task 2: Azure Container Registry (15 points)

### Evidence 2.1: ACR Overview

![ACR Overview](docs\Task2\Screenshot 2026-05-02 125435.png)

Description: The Azure Container Registry `pa426100273` is deployed in resource group `rg-sp26-26100273`, UK West region, with Basic SKU and Provisioning state Succeeded. The login server is `pa426100273.azurecr.io`.

### Evidence 2.2: Docker Builds

![Docker Builds](docs\Task2\Screenshot 2026-05-02 130547.png)(docs\Task2\Screenshot 2026-05-02 130716.png)(docs\Task2\Screenshot 2026-05-02 131636.png)

Description: Successful local Docker builds for all three images: `validate-api:v1` built from `./validate-api`, `report-job:v1` built from `./report-job`, and `func-app:v1` built from `./function-app`. All builds completed with platform `linux/amd64`.

### Evidence 2.3: ACR Repositories

![ACR Repositories](docs\Task2\Screenshot 2026-05-02 133338.png)

Description: The ACR repository list confirms all three images were pushed successfully: `function-app`, `report-job`, and `validate-api`, all tagged `v1` and available at `pa426100273.azurecr.io`.

---

## Task 3: Durable Function Implementation (12 points)

### Evidence 3.1: Completed Function Code

[function_app.py](function-app/function_app.py)

Description: The orchestrator chains two activities: `validate_activity` posts the order to the AKS validator and returns a valid/invalid result. If valid, `report_activity` uses the Azure SDK to create an ACI running the report-job image, polls until completion, deletes the ACI, and returns the blob URL. If invalid, the orchestrator returns a rejected status immediately without creating an ACI.

### Evidence 3.2: Local Function Handler Listing

![func start](docs\Task3\Screenshot 2026-05-02 140105.png)

Description: Running `func start` locally shows all four Durable Function handlers registered: `http_starter` (HTTP POST trigger), `my_orchestrator` (orchestrationTrigger), `report_activity` (activityTrigger), and `validate_activity` (activityTrigger). This confirms the Durable Functions runtime discovered all handlers correctly.

---

## Task 4: Function App Container Deployment (8 points)

### Evidence 4.1: Function App Container Configuration

![Function App Container](docs\Task4\Screenshot 2026-05-05 110614.png)

Description: The Function App `pa4-26100273` Deployment Center shows it is configured to pull from Azure Container Registry `pa426100273`, image `func-app`, tag `v1`. Authentication uses Admin Credentials.

### Evidence 4.2: Functions List

![Functions List](docs\Task4\Screenshot 2026-05-05 111234.png)

Description: The Functions tab in the Portal shows all four functions registered and enabled: `http_starter` (HTTP), `my_orchestrator` (Orchestration), `report_activity` (Activity), and `validate_activity` (Activity). This confirms the container image deployed successfully and the Durable Functions runtime loaded all handlers.

### Evidence 4.3: Orchestration Smoke Test

![Error](docs\Task4\image.png)

Description: The smoke test curl output and Failed status screenshots could not be captured at this stage due to `AuthorizationFailure` on `AzureWebJobsStorage`. The subscription's security policy disables key-based authentication on storage accounts. The managed identity `mi-pa4-26100273` (client ID: `e837bb03-1c0f-4125-a644-80771e9d7b90`) requires `Storage Blob Data Contributor`, `Storage Queue Data Contributor`, and `Storage Table Data Contributor` roles on the storage account to be granted by the instructor. All four functions are correctly implemented and visible in the Portal as shown in Evidence 4.2.



---

## Task 5: AKS Validator (15 points)

### Evidence 5.1: AKS Cluster

![AKS Cluster](docs\Task5\image.png)

Description: The AKS cluster `pa4-26100273` is deployed in resource group `rg-sp26-26100273`, UK West region, with 1 node of size `Standard_B2s`. The cluster is in Running state with Kubernetes version 1.34.4.

### Evidence 5.2: Kubernetes Nodes and Pods

![Kubectl Nodes and Pods](docs\Task5\Screenshot 2026-05-05 111822.png)(docs\Task5\Screenshot 2026-05-05 112013.png)

Description: `kubectl get nodes` shows one node `aks-nodepool1-16433285-vmss000001` in Ready status. `kubectl get pods` shows the `validate-deployment` pod in Running state with 1/1 containers ready and 0 restarts.

### Evidence 5.3: Kubernetes Service

![Kubectl Service](docs\Task5\Screenshot 2026-05-05 112043.png)

Description: `kubectl get service validate-service` shows the LoadBalancer service with cluster IP `10.0.149.170` and external IP `20.254.208.93` on port `8080:31549/TCP`. This public IP is the stable endpoint used by the Durable Function to call the validator.

### Evidence 5.4: Validator API Tests

![Validator API Tests](docs\Task5\Screenshot 2026-05-05 112424.png)

Description: Three tests confirm the validator works correctly. `GET /health` returns `{"status":"ok"}`. A valid order with `qty=2` returns `{"valid":true,"reason":"ok","order_id":"O-1001"}`. An invalid order with `qty=999` returns `{"valid":false,"reason":"quantity exceeds limit","order_id":"O-1002"}`, demonstrating the qty > 100 rejection rule.

### Evidence 5.5: Function App `VALIDATE_URL`

![VALIDATE_URL Setting](docs\Task5\Screenshot 2026-05-05 112555.png)

Description: The Function App environment variables show `VALIDATE_URL` set to `http://20.254.208.93:8080/validate`. The `validate_activity` function reads this variable at runtime to POST order payloads to the AKS validator.

### Evidence 5.6: AKS Idle Behavior

Description: The AKS `Standard_B2s` node continues running and billing even when no orders are being processed. Unlike ACI which only exists during a job run, the AKS node pool has no scale-to-zero on a standard cluster — the VM stays alive to ensure the validator responds immediately without cold-start delay when the next order arrives.

---

## Task 6: ACI Report Job (15 points)

### Evidence 6.1: Blob Container

![Reports Blob Container](docs\Task6\Screenshot 2026-05-05 113208.png)

Description: The `reports` blob container was created in storage account `rgsp26261002738a9a` in resource group `rg-sp26-26100273`. This is where the report-job container writes generated PDF files after each successful order.

### Evidence 6.2: Manual ACI Run

![ACI Run](docs\Task6\Screenshot 2026-05-05 115216.png)

Description: The `ci-report-test` container instance was created manually with the `report-job:v1` image, managed identity `mi-pa4-26100273` attached, restart policy Never, and the required environment variables. The container started and ran but could not upload to blob storage due to the managed identity lacking `Storage Blob Data Contributor` access on the storage account — a subscription-level permission issue requiring instructor intervention.

### Evidence 6.3: ACI Logs

![ACI Logs](docs/task6-aci-logs.png)

Description: The container logs show the report-job successfully parsed the order JSON and generated the PDF locally. The failure occurred at the blob upload step with `AuthorizationFailure`, confirming the PDF generation logic works correctly and only the storage write permission is missing.

### Evidence 6.4: Generated PDF

![PDF Generation](docs\Task6\docs\Task6\image.png)

Description: The PDF could not be confirmed in blob storage because the managed identity `mi-pa4-26100273` was not pre-granted `Storage Blob Data Contributor` access to `rgsp26261002738a9a`. The report-job code generates the PDF successfully as shown in the logs, but the upload step fails at authorization. This is a subscription-level restriction outside student control.

### Evidence 6.5: Function App Managed Identity

![Managed Identity](docs\Task6\Screenshot 2026-05-05 115837.png)

Description: The Function App `pa4-26100273` Identity blade shows the user-assigned managed identity `mi-pa4-26100273` attached under the User assigned tab. This identity is used by `report_activity` via `DefaultAzureCredential` to authenticate to Azure when creating ACI instances programmatically.

### Evidence 6.6: Report App Settings

![Report App Settings](docs\Task6\Screenshot 2026-05-05 115655.png)

Description: The Function App environment variables show all required settings: `REPORT_IMAGE` (the ACR image URI for the report job), `ACR_SERVER`, `ACR_USERNAME`, `ACR_PASSWORD` (masked), `STORAGE_ACCOUNT_URL` (the blob storage endpoint), `REPORT_RG`, `REPORT_LOCATION`, `SUBSCRIPTION_ID`, and `AZURE_CLIENT_ID`. These are read by `report_activity` at runtime to create and configure the ACI.

---

## Task 7: End-to-End Pipeline (15 points)

Description: The end-to-end pipeline test could not be completed due to the `AuthorizationFailure` on `AzureWebJobsStorage` preventing the Function App orchestrator from running. All individual components are correctly deployed and verified: the AKS validator responds correctly to both valid and invalid orders, the ACI report-job generates PDFs correctly, and all Function App settings are configured. The pipeline will work end-to-end once the managed identity is granted the required storage roles.

---

## Task 8: Write-up and Architecture Diagram (5 points)

### Evidence 8.1: Architecture Diagram

![Architecture Diagram](docs\architecture_diagram.png)

Description: The diagram shows all Azure resources and their connections: GitHub CI/CD to App Service, Web App to Function App (start + status polling), Function App to AKS validator (HTTP validate call), Function App to ACI (SDK-based creation, ephemeral per run), ACI to Blob Storage (PDF write), ACR providing images to Function App, AKS, and ACI, and the managed identity relationship between the Function App and the resource group.

### Question 8.2: Service Selection

**App Service** was chosen to host the TaskFlow web frontend because it is a fully managed PaaS platform optimized for long-running, stateful web applications. It supports CI/CD directly from GitHub via GitHub Actions, meaning every push to the main branch automatically redeploys the frontend without manual intervention. Unlike containers or serverless compute, App Service maintains a persistent process which suits a web UI that must always be available to accept user requests.

**Azure Durable Functions** was chosen to coordinate the multi-step pipeline because it provides stateful orchestration out of the box. Each activity checkpoint is persisted to storage, meaning if the runtime crashes mid-pipeline it replays from the last checkpoint rather than restarting from scratch. Durable Functions also handles the async gap between calling the AKS validator and waiting for the ACI report job to finish without holding a thread open for the entire duration.

**Azure Kubernetes Service (AKS)** was chosen for the validator microservice because the validator is a long-lived HTTP service that must respond to every order submission. AKS provides a stable LoadBalancer endpoint, declarative deployment manifests, and automatic pod restarts if the container crashes. It represents the industry standard for enterprise microservice orchestration and gives full control over the container lifecycle.

**Azure Container Instances (ACI)** was chosen for the report generator because it is a short-lived batch job that starts, generates a PDF, uploads it to blob storage, and exits. ACI bills only for the seconds the container is alive, making it far more cost-efficient than keeping an always-on Kubernetes pod idle between orders. Running an idle report container 99% of the time on AKS would waste significant compute budget.

### Question 8.3: ACI vs AKS

**AKS idle behavior:** The AKS node pool continues running and billing even when no orders arrive. The validator pod stays alive on the `Standard_B2s` node consuming CPU and memory regardless of traffic. There is no scale-to-zero on a standard AKS cluster. This is appropriate because the validator must respond immediately without cold-start delay.

**ACI idle behavior:** ACI has no idle state in this architecture. The report job ACI does not exist between orders — it is created on demand by `report_activity`, runs for approximately 20-30 seconds, and is deleted programmatically. There is no persistent ACI resource sitting idle between runs.

**Spam scenario:** If a malicious user submitted 1000 orders in a minute, AKS would incur minimal additional cost since the existing validator pod handles all requests on the same node. ACI would be the most expensive service — `report_activity` would attempt to create 1000 separate container instances, each billing for compute time, which would exhaust the subscription's ACI quota and generate significant unexpected charges.

**Why AKS and ACI cannot be swapped:** The validator needs a persistent stable endpoint that is always ready — ACI has no persistent endpoint and would require creating a new container for each validation request, adding unacceptable latency. Conversely, running the report generator on AKS would waste compute keeping an idle pod running between orders when ACI's per-second billing is far more appropriate for a batch job.

### Question 8.4: Durable Functions vs Plain HTTP

Implementing the validate → report flow as two plain HTTP functions calling each other would introduce two concrete problems. First, **state persistence**: if the runtime restarts after validation succeeds but before the report is generated, a plain HTTP function has no way to know validation already passed. It would either re-validate or lose the result entirely. Durable Functions checkpoints the validation result to storage before calling `report_activity`, so a replay skips validation and proceeds directly to report generation. Second, **timeout and reliability**: the report activity takes up to 60 seconds to create an ACI, wait for completion, and clean up. A plain HTTP function calling another HTTP function would hold an open connection for the entire duration, which is fragile under network instability and wastes resources. Durable Functions handles this asynchronously with built-in retry logic and no open connection required.

### Question 8.5: Cost Review

The AKS `Standard_B2s` node is the single most expensive resource. A `Standard_B2s` VM in UK West costs approximately $0.042/hour, totaling roughly $1.00/day and $7/week. The Function App and Web App share a single B1 App Service Plan at approximately $0.018/hour ($3/week). ACR Basic tier costs approximately $0.167/day. ACI costs are negligible at roughly $0.0000015 per vCPU-second for the few test runs performed. The AKS node dominates cost because it runs continuously as a VM regardless of whether any orders are being processed.

### Question 8.6: Challenges Faced

**Challenge 1: Azure subscription security policy blocking storage authentication.**
The instructor's subscription enforces a policy that disables key-based authentication on storage accounts. This caused `AuthorizationFailure` errors on `AzureWebJobsStorage`, preventing the Function App from starting. Multiple approaches were attempted: connection strings (blocked by policy), managed identity with the auto-generated storage account `rgsp26261002738a9a` (identity not pre-granted access), and switching account names. The issue was identified as the managed identity `mi-pa4-26100273` lacking `Storage Blob Data Contributor`, `Storage Queue Data Contributor`, and `Storage Table Data Contributor` roles on the storage account. This required TA intervention to resolve.

**Challenge 2: PowerShell CLI compatibility issues.**
The Azure CLI on Windows PowerShell exhibited several unexpected behaviors. The pipe character `|` in `--linux-fx-version DOCKER|image:tag` was intercepted by PowerShell as a pipeline operator even inside quoted strings and variables, requiring workarounds via `cmd /c` and string concatenation. Additionally, deprecated CLI flags were removed in newer CLI versions requiring discovery of replacements. JSON strings passed as environment variables to `az container create` had their double quotes stripped by PowerShell causing `JSONDecodeError` in the report job container, which was resolved by using a JSON parameter file instead.