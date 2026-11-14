## Wazuh agent installation
1. In the Wazuh dashboard, **Agents > Deploy new agent**: Linux, DEB amd64, server address `10.0.2.3`, agent name `victim`.

![Deploy agent form](agentform.png)

2. Ran the generated command on `victim` over SSH, then started the service.

![Agent install output](agentinstall.png)
![Agent service running](recordd.png)

## Result
`victim` appears as **Active** in the dashboard and in `agent_control -l`.

![Agent active in the dashboard](agentactive.png)
![agent_control list](agentcontrollist.png)
