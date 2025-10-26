# azure-two-tier-vnet-architecture
This project focuses on security and seamless communication between multiple virtual networks. Two virtual networks (VNets) were deployed to enable secure and efficient connectivity.  To ensure seamless communication, VNet peering was implemented, allowing traffic to flow securely between the networks without the need for additional gateways.For enhanced security granularity, Network Security Groups (NSGs) were configured with both inbound allow and deny rules. These rules define and control the type of traffic permitted between the VNets, ensuring a robust and well-segmented network environment.To enforce security and traffic control, Network Security Groups (NSGs) were applied to subnets and network interfaces. The NSGs include granular inbound and outbound rules to allow or deny specific traffic flows based on source/destination IPs, ports, and protocols.

**This setup ensures**:

- End-to-end secure communication between VNets

- Controlled access policies through layered NSG configurations

- Optimized network performance using private peering routes


# Implementation
**Step 1 — Create the Resource Group**
- Sign in to Azure Portal
- Search for Resource groups → Click Create.
- Choose a Subscription and Region (northeurope).
- RG-UDR.
- Review + Create → Create

**Step 2 — Create the First Virtual Network (vnet-sql)**

- Go to Virtual Networks → Create.

- Select your resource group.

- vnet-sql.

- Define the Address space (e.g., 10.0.0.0/16).

- Add a subnet:

- Name: subnet-sql

- Subnet range: 10.0.0.0/24

- Review and create the network.

**Step 3 — Create the second Virtual Network (vnet-web)**

- Go to Virtual Networks → Create.

- Select your resource group.

- vnet-web.

- Define the Address space (e.g., 10.1.0.0/16).

- Add a subnet:

- Name: subnet-web

- Subnet range: 10.1.0.0/24

- Review and create the network.

**Step 4 — Configure VNet Peering**

- Open vnet-sql in the portal.

- Under Settings, select Peerings → Add.

- Name the peering .

- “Remote virtual network” dropdown, select vnet-web.

- Enable:

- Allow virtual network access 

- Allow forwarded traffic 

- Click Add.

- Repeat the same process from vnet-web to vnet-sql to complete the bidirectional connection.

**Step 5 — Create and Configure Network Security Groups (NSGs)**

-  Create.

- Assign the NSG to your resource group.

- nsg-sql.

- After creation, open the NSG and go to Inbound security rules.

- Add rules:

- Allow ms sql (Port 1433) from web IP.
-  RDP (Port 3389) from all sources.
-  Deny all

**Step 6 — Create and Configure Network Security Groups (NSGs)**

-  Create.

- Assign the NSG to your resource group.

- nsg-web.

- After creation, open the NSG and go to Inbound security rules.

- Add rules:
- RDP (Port 3389) from all sources.
- HTTP (port 80)

  **Step 7 — Deploy Two Virtual Machines (for testing)**

- Create.

- Place one VM in vnet-web (subnet-web) and another in vnet-sql (subnet-sql).

- Use small size (B1s) to minimize cost.

**Step 8 — Test Connectivity (SQL Server Communication)**

- Connect to the Web Server VM

- Use RDP (for Windows) to access the Web Server VM hosted in vnet-web.

- Launch SQL Server Management Studio (SSMS) on the Web Server VM.

- Connect to the SQL Server VM

- In SSMS, enter the private IP address of the SQL Server VM (located in vnet-sql) as the Server name.

- Use SQL Authentication.

- Click Connect.

- Validate the Connection

# Troubleshooting

**Issue: SQL Authentication Connection Failed in SSMS**

While testing connectivity from the Web Server VM (vnet-web) to the SQL Server VM (vnet-sql) using SQL Server Management Studio (SSMS), the connection attempt with SQL Authentication failed.

**Root Cause**

The SQL Server instance was configured with Windows Authentication mode only (default setting).
This configuration prevents login attempts using SQL Authentication credentials (username and password) from remote clients.

**Resolution Steps**

- Access the SQL Server VM locally

- Log in to the SQL Server VM via RDP.

- Open SSMS and connect using Windows Authentication with localhost as the server name.

- Enable SQL Server and Windows Authentication Mode

- In SSMS, right-click the server name → Select Properties → Go to Security.

- Under Server Authentication, select SQL Server and Windows Authentication mode.

- Click OK, then restart the SQL Server service to apply the changes.

- Update SQL Login Credentials

- execute a script to alter credentials:

ALTER LOGIN [YourUserName] WITH PASSWORD = 'password!';


**Verify NSG Rules**

- Ensure port 1433 is open in:

- The inbound NSG rule of the SQL Server(vnet-sql).

- Confirm that outbound traffic from the Web Server VM (vnet-web) is not blocked.

**Test Connection Again**

- From the Web Server VM, open SSMS.

- Use the SQL Server VM’s private IP address as the Server name, and the newly created SQL login credentials.

_ Successful connection using SQL Authentication.

# Design Architecture 

