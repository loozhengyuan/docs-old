# Setup Windows Admin Center on Hyper-V Server 2019

## Prepare Hyper-V Server 2019

### Step 1: Enable Remote Management

To enable remote management in Hyper-V Server:

1. Log into Hyper-V Server.
2. At the **Server Configuration** \(SCONFIG\) tool, type **4** to configure remote management.
3. Type **1** to enable Remote Management.
4. Type **4** to return to the main menu.

### Step 2: Enable File Server Role \(Optional\)



### Step 3: Enable Hyper-V Module for Powershell \(Optional\)

## Deploy Windows Admin Center

```text
msiexec /i <PATH TO INSTALLATION FILE>.msi /qn /L*v log.txt SME_PORT=<PORT> SSL_CERTIFICATE_OPTION=generate
```



## Login to WAC Web UI

Navigate to `<ip or fqdn of server>:port` and enter your credentials.



