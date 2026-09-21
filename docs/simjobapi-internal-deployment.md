# SimJobApi Internal Deployment Guide

## Purpose
Document the internal deployment flow for enabling SimJobApi for a customer tenant, using CFL as the example.

## Example environment
- Customer: CFL
- Target environment: Internal
- Example SimJobApi port: `8085`

## Deployment steps
1. Check which ports are already used by the SimJobApi containers on Internal.
   - Pick a port that is not in use.
   - In most cases this is the next available increment.
   - Example: if the previous tenant uses `8084`, use `8085`.

2. Add `SimJobApi_Port` in Delivery Manager for the customer.

3. Push the Delivery Manager settings to Octopus.

4. Deploy SIF to Internal.
   - Make sure **Use SimJobApi** is checked before starting the deployment.
   - Confirm the tenant is set correctly for the deployment.

![Octopus deployment with Use SimJobApi enabled](https://github.com/user-attachments/assets/e9751bb1-5066-4419-8cbf-af0c511522f8)

5. After the deployment succeeds, verify the container port is exposed on `vDockerDevApp03`.
   - Connect to `vDockerDevApp03`.
   - Run `docker ps`.
   - Confirm the selected port is mapped to the SimJobApi container.

![docker ps showing the exposed SimJobApi port](https://github.com/user-attachments/assets/1308626d-ac45-4b41-9530-f30945c816f8)

6. Add an IIS entry to route incoming traffic to the correct container port.
   - RDP into `vDockerDevApp03`.
   - Open `C:\scripts`.
   - Run `.\launch-IIS.ps1`.
   - In IIS, go to **VDOCKERDEVAPP03 > Sites > Default Web Site > URL Rewrite**.
   - Review an existing customer rule and replicate the same pattern for the current site.
   - Update the rule so it points to the selected SimJobApi port.

![IIS URL Rewrite example for mapping to the container port](https://github.com/user-attachments/assets/5c1139d5-692e-414a-826c-544869ab0876)

## Validation checklist
- [ ] Selected port is not already used by another SimJobApi container.
- [ ] `SimJobApi_Port` is added in Delivery Manager.
- [ ] Delivery Manager settings are pushed to Octopus.
- [ ] SIF Internal deployment completed successfully.
- [ ] **Use SimJobApi** was enabled during deployment.
- [ ] `docker ps` shows the expected port mapping on `vDockerDevApp03`.
- [ ] IIS URL Rewrite points to the correct container port.
