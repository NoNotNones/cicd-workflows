---
title: "Setting up your environment"
weight: 100
params:
  bookCollapseSection: true
---

## **Sample Toplology**

                    Kubernetes Learning Journey

          ┌─────────────────────────────────────┐
          │          Podman Desktop             │
          │      (Container Runtime)            │
          └─────────────────────────────────────┘
                           │
                           ▼
          ┌─────────────────────────────────────┐
          │              kind                   │
          │     (Local Kubernetes Cluster)      │
          └─────────────────────────────────────┘
                           │
                           ▼
          ┌─────────────────────────────────────┐
          │             kubectl                 │
          │      (Manage Kubernetes)            │
          └─────────────────────────────────────┘
                           │
                           ▼
          ┌─────────────────────────────────────┐
          │              Helm                   │
          │     (Package Kubernetes Apps)       │
          └─────────────────────────────────────┘
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
      AWS CLI + EKSCTL              Azure CLI
             │                           │
             ▼                           ▼
            EKS                         AKS

## Preparing Windows machines
- Create a folder, run **Windows Powershell**: 
```
New-Item -ItemType Directory $env:USERPROFILE\Apps 
```
- Add to Windows PATH: 
```
setx /m PATH "$env:PATH;$env:USERPROFILE\Apps"
```
{{% details title="Info" open=false %}}
  > SUCCESS: Specified value was saved.
{{% /details %}}

### Install **Windows Subsystem for Linux (WSL)**

  - ``` Enable-WindowsOptionalFeature `
-Online `
-FeatureName VirtualMachinePlatform `
-All `
-NoRestart ```
  - ``` wsl --install ```
  - ``` Restart-Computer -Force```
  
{{< image src="./w1.jpg" >}}

### Installing **Podman** Desktop on Windows
- Download: https://podman-desktop.io/
- Install > Windows Linux Subsystem ( WSLv2) > Create a Podman machine ( Let's default setup)
  - Enable Kubernetes setup & Create Podman Machine

{{< image src="./p1.jpg" >}}

- Checking Podman CLI > PS: `podman`
- Run container: `podman run --rm hello-world`
- Create Docker Alias: `Set-Alias -Name Docker -Value Podman`
  - Testing: `docker run --rm hello-world`

{{% columns %}}
- {{< image src="./p2.jpg" >}}
- {{< image src="./p3.jpg" >}}
{{% /columns %}}

### Installing **kind**
-  The tool that we'll use to create local Kubernetes clusters.
-  Setup: https://kind.sigs.k8s.io/docs/user/quick-start/
   -  On Windows in PowerShell: 
    > curl.exe -Lo kind-windows-amd64.exe https://kind.sigs.k8s.io/dl/v0.32.0/kind-windows-amd64
    > Move-Item .\kind-windows-amd64.exe c:\some-dir-in-your-PATH\kind.exe
   - Add to Windows PATH: `C:\Apps`
     {{% details title="Settings > " open=false %}}
     > Windows > Environment Variables > User variable > Path > Edit
     > New: `C:\Apps`
     {{% /details %}}
   - Checking: `kind --version` > *kind version 0.32.0*


### Installing **Helm**
- Source : https://github.com/helm/helm/releases
  - Windows PS: `systeminfo | findstr /C:"System Type"` 
    > System Type: x64-based PC → AMD64/x86-64
- Install: **helm-v3.21.2-windows-amd64**

{{% columns %}}
- {{< image src="./s1.jpg" >}}
- {{< image src="./s2.jpg" >}}
{{% /columns %}}

### Installing **Azure CLI**
- Source: https://learn.microsoft.com/vi-vn/cli/azure/install-azure-cli-windows?view=azure-cli-latest&pivots=winget
  - Windows PS: 
    > -  `winget install --exact --id Microsoft.AzureCLI`
    > -  `az --version`

### Configuring **Azure access** interactively
{{% details title="Logging In to The Azure CLI" open=false %}}
  - There are two ways of logging in to Azure
    - **Interactive login** is the easies way to log in but is not suitable for automation.
    - With a **service principal** are great for Azure CLI login automation!
{{% /details %}}

- Login Azure (PS):  `az login`
- Authenticate: `az login --use-device-code`
- Select **Subscription** and tenant: `1`
- Run: `az group list`

### Configuring Azure access with a **service principal**

- Subscription ID check: `az account show` > `id`
- Create Service Principal:

  ```
    az ad sp create-for-rbac \
    --role Owner \
    --scopes /subscriptions/<subscription-id>
  ```
  > - "appID":
  > - "displayName":
  > - "password":
  > - "tenant":

- Login: 
  ```
  az login \
  --service-principal \
  --username 11111111-1111-1111-1111-111111111111 \
  --password xxxxxxxxxxxxxxxx \
  --tenant 22222222-2222-2222-2222-222222222222
  ```

- Result checking: 
  ```
    "user": 
      "name": "11111111-1111-1111-1111-111111111111",
      "type": "servicePrincipal"
  ```