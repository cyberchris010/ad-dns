<p align="center">
<img src="https://github.com/user-attachments/assets/83387a1b-35c6-4c88-be82-8443f8fc1aa0" alt="activedir-logo"/>
</p>


<h1>Understanding DNS with Active Directory</h1>
This tutorial explains how to use Active Directory to gain deeper insight into how DNS works.<br />


<h2>Environments and Technologies Used 🖥️</h2>

- Microsoft Azure Compute
- Microsoft Azure Resource Groups
- Microsoft Azure Virtual Machines
- Windows App for Mac (previously Microsoft Remote Desktop)
- Active Directory Domain Services
- Active Directory Organizational Units (OUs)
- Active Directory Group Policies
- Active Directory Security Groups
- DNS (Domain Name System)
- DNS A-Records
- Local DNS Cache
- DNS CNAME Records

<h2>Operating Systems Used 💿</h2>

- macOS Sequoia (Local)
- Windows Server 2022
- Windows 10 (21H2)

<h2>List of Prerequisites ✅</h2>

- Microsoft Account
- Microsoft Azure Subscription and Credits
- Active Directory Infrastructure in Azure
- Deployment of Active Directory Domain and Organizational Units in Azure VM
- Deployment of Active Directory Client Joined to the Domain in Azure VM
- Creation of Multiple Employee User Accounts in Active Directory Domain

<h2>High-Level Deployment and Configuration Steps 🪜</h2>

- [**Step 1 - Review DNS (Domain Name System) Key Concepts 🔑💡**](https://github.com/cyberchris010/ad-dns/blob/main/README.md#step-1---review-dns-domain-name-system-key-concepts-)
- [**Step 2 - A-Record Exercise 🅰️📀**](https://github.com/cyberchris010/ad-dns/blob/main/README.md#step-2---a-record-exercise-%F0%9F%85%B0%EF%B8%8F)
- [**Step 3 - Local DNS Cache Exercise 🖥️🗃️**](https://github.com/cyberchris010/ad-dns/blob/main/README.md#step-3---local-dns-cache-exercise-%EF%B8%8F%EF%B8%8F)
- [**Step 4 - CNAME Record Exercise 📛📀**](https://github.com/cyberchris010/ad-dns/blob/main/README.md#step-4---cname-record-exercise-)

<h2>Step 1 - Review DNS (Domain Name System) Key Concepts 🔑💡</h2>

 - **DNS** stands for **Domain Name System**.
 - The primary function of **DNS** is to resolve/translate **human-readable domain names** into **machine-readable IP addresses**, which enables users to access websites and their resources.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/d0b57cbd-9631-4ffe-9c38-812747fa5d15" alt="howdnsworksgeneral"/>
</p>
<p>
<br />

  - The first and most common type of DNS record is the **A-record**, or **Address Record**.
  - A-records resolve domain names to IPv4 addresses, which are 32 bit numerical addresses.
  - When a user types in a domain name, like `google.com`, into a web browser, DNS uses an A record to resolve the domain name, like `google.com`, to an IP address.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/d9b56fd1-3f96-4373-8d8c-934ef9103847" alt="howdnsworks-arecords"/>
</p>
<p>
<br />

 - When a client computer searches for a domain name it performs performs three sequential checks for the A-record of that domain name on the backend:
   - First the client checks its own **local DNS cache** for the specific A-record mapping the given domain name to its current IPv4 address.
   - **Local DNS cache** is a temporary storage location on a client computer that keeps records of previously-visited domain names (`google.com`) and their corresponding IP addresses.
   - **Local DNS cache** is important because it expedites the process of a client computer accessing websites a user has visited before by allowing the client computer quickly retrieve a stored A-record instead of performing a complete DNS lookup every time.
   - If a client computer contains no A-record in its **local DNS cache**, it resorts to trying to find a given A-record in its **local Host file** which is stored on in long-term memory on its hard drive. This is much less efficient than finding it in its **local DNS cache**.
   - If it can't find the A-record in the **local Host File**, the client computer must perform a DNS lookup with its **DNS server**, which is the least efficient way to acquire an A-record.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/0696b159-f35d-4aad-ad4e-8ca65b25366c" alt="howdnsworks-localcache2"/>
</p>
<p>
<br />

  - A **CNAME (Canonical Name) record** is a DNS record that is similar to an A-record.
  - Whereas A-records resolve domain names to IP addresses, **CNAME records** resolve domain names to domain names via aliases.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/a20cde38-0a25-4b11-9b4d-59c13425a24b" alt="howdnsworks-cnamerecords"/>
</p>
<p>
<br />

 - A hands-on way to gain deeper insight into how DNS works is to deploy two Azure Virtual Machines (a client and an Active Directory domain controller) and observe how A-records, local DNS cache, and CNAME records function between them.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/3cc4362b-02ac-41ea-b83b-31e7ddcffbdf" alt="howdnsworks-arecords-1"/>
</p>
<p>
<br />

<h2>Step 2 - A-Record Exercise 🅰️📀</h2>

- From a **local macOS system**, log-in to the **Domain Controller VM** as admin user, `jane_admin`.
  -  Open the **Microsoft App (Remote Desktop)** and double-click the **Domain Controller VM** tile under **Saved PCs**.
 - Log-in to `dc-1` as `jane_admin` by entering `mydomain.com\jane_admin` for **Username** and the corresponding password.
 - Click **Continue**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/760f45fe-54f4-4b24-a91a-a62237a1ed13" alt="rdpasdomadminindc1"/>
</p>
<p>
<br /> 

- Log-in to the **Client VM** as the **domain admin user**, `jane_admin`:
   - Open the **Microsoft App (formerly Remote Desktop)** on the **local macOS system**.
   - Double-click the **Client-1 VM** tile, enter `mydomain.com\jane_admin` for **Username**, and the corresponding password for **Password**.
   - Click **Continue**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/01abeb40-3304-4c73-8e01-eb00dad6eb7f" alt="rdpasjaneadminclientvm"/>
</p>
<p>
<br />

- On **Client-1**, open **PowerShell** as **administrator** and attempt to `ping` **"mainframe"**.
- Observe what happens when it fails: a message is returned that reads, `Ping request could not find host mainframe. Please check the name and try again.`
<img width="1200" src="https://github.com/user-attachments/assets/d4522435-ed33-4d37-a85b-c4458330f6ef" alt="client1pingsmainframe1"/>
</p>
<p>
<br />

 - When **Client-1** attempts interact with a hostname of the network, in this case when it attempts to `ping mainframe`, **Client-1** first checks its local DNS cache for `mainframe's` A-record.
   - This is the fastest method **Client-1** can access an A-record for hostname, `mainframe`.
 - When **Client-1** doesn't find an A-record for the `mainframe` hostname, it checks its local **host file**.
   - This is the second fastest method **Client-1** can access an A-record for the hostname, `mainframe`. 
 - When **Client-1** fails to find a `mainframe` record in its **host file**, it then checks its **DNS Server**, which, in this case, is the **dc-1 VM**, the **Domain Controller**.
   - This is the slowest method **Client-1** can access an A-record for hostname, `mainframe`.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/bf2893d3-d28d-422b-9a1d-fbc40e68fb35" alt="howdnsworks-arecords-1"/>
</p>
<p>
<br />

- To simulate **Client-1** first checking its local DNS cache for `mainframe's` A-record, open **PowerShell** as **administrator** from **Client-1** and run `ipconfig /displaydns`.
- A long list of DNS records is then printed out, which **Client-1** has in its local DNS cache.
- However, there are no records for hostname, `mainframe`.
<img width="1200" src="https://github.com/user-attachments/assets/6abbf800-993e-4381-9a92-197d22320a1a" alt="ipconfigdisplaydns"/>
</p>
<p>
<br />

- When **Client-1** can't find an A-record for hostname, `mainframe`, in its local DNS cache, it checks its **local Host file**.
- To demonstrate **Client-1** checking its host file for hostname, `mainframe`, open **File Explorer** on **Client-1** and navigate to **This PC > Windows (C:) > Windows > System32 > drivers > etc > hosts**.
- Double-click `hosts`, which is **Client-1's** local host file, and open it with **Notepad**.
  - The local host file is where **Client-1** stores specific IP address-to-hostname mappings.
  - In this case, there is not a mapping for `mainframe` in **Client-1's** host file.
<img width="1200" src="https://github.com/user-attachments/assets/137c4293-8561-4558-9acf-2f09c07206ae" alt="client1localhostfile"/>
</p>
<p>
<br />

- After **Client-1** fails to find a mapping for `mainframe` in its local host file, it checks its **DNS Server** for `mainframe's` A-record.
- To simulate **Client-1** checking with its DNS Server for `mainframe`, open **PowerShell** as **administrator** on **Client-1** and attempt an `nslookup` of `mainframe`: `nslookup mainframe`.
  - It returns the hostname `dc-1.internal.cloudapp.net` and Private IP address `10.0.0.4` of the **DNS Server** that **Client-1** is querying.
  - It also returns a message that `dc-1.internal.cloudapp.net can't find mainframe: Non-existent domain`.
  - The **DNS Server** is responding to **Client-1's** query for `mainframe`, explaining that it shows no records for `mainframe`.
<img width="1200" src="https://github.com/user-attachments/assets/1284c1d0-c738-4564-84f4-1ab72587e6a6" alt="nslookupmainframe"/>
</p>
<p>
<br />

- Because **Client-1** can't find `mainframe` in its local DNS cache, nor in its local host file, nor in its local DNS Server, a DNS A-record for `mainframe` needs to be created on the domain controller, on the **DC-1 VM**.
- Then the DNS A-record for `mainframe` on the domain controller needs to be configured to point to the domain controller's **Private IP address**.
  - On the **Domain Controller VM** (dc-1), click the **Start** menu in the bottom left, click **Windows Administrative Tools**, then click **DNS** to open **DNS Manager**.
  - On the left, click `dc-1` to expand it, then navigate to **Forward Lookup Zones > mydomain.com**.
<img width="1200" src="https://github.com/user-attachments/assets/fe6fd228-36a1-4938-9dc6-b5d0f68c77b6" alt="dnsmanagerfilepath"/>
</p>
<p>
<br />

 - Right-click anywhere in the blank space on the right and select **New Host (A or AAA)**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/43c4d02e-bd59-4b8b-a374-7ca237843697" alt="newhostaoraaa"/>
</p>
<p>
<br />

 - In the **New Host** dialog box, enter `mainframe` in the **Name** field.
 - To find the **Private IP address** of the domain controller, open **PowerShell**, run `ipconfig`, then find the `IPv4 Address`, which is `10.0.0.4`.
 - Enter the **Private IP address** of the domain controller, `10.0.0.4`, in the **IP address** field within the **New Host** menu.
 - Select **Create associated pointer (PTR) record** then click **Add Host**.
 - When the **PTR record** message displays click **OK**.
 - Then click **Done** and click **Refresh** at the top of the **DNS Manager** menu.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/ebd4bff5-9fe3-4fe9-abab-cf197bfec0d0" alt="addnewarecordfornewhost"/>
</p>
<p>
<br />

 - Notice the newly created **A record** for `mainframe`, which points to the **Private IP address** of the domain controller (10.0.0.4) in the **DNS Manager**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/3099e78d-1ab4-4cf6-975e-e8c5e4364a19" alt="mainframehostarecordcreated"/>
</p>
<p>
<br />

- Now that the domain controller contains an A-record for `mainframe`, attempt to `ping` "mainframe" again from **Client-1**:
  - Back on **Client-1** run **PowerShell** as **administrator** and run `ping mainframe`.
  - Notice that it matches `mainframe` to 10.0.0.4 and then a `ping` replies are returned.
<img width="1200" src="https://github.com/user-attachments/assets/fbc6fbb4-a80d-4fe7-9d81-ad15c1dd453a" alt="client1succeedsinpingingmainframe"/>
</p>
<p>
<br />

 - Once an A-record for `mainframe` is created on the domain controller, when **Client-1** `pings` "mainframe", it goes down its checklist on the backend.
   - After failing to find `mainframe` in its local DNS cache, **Client-1** checks its local host file, which doesn't contain any mappings for `mainframe`.
   - However, when **Client-1** checks its **DNS Server**, the DNS Server on the domain controller finds the A-record that was created for `mainframe`.
   - The DNS server then replies to **Client-1's** `ping` with the IP address of `mainframe`.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/851e27b5-3570-4534-8608-22e7f057bac0" alt="howdnsworks-arecords-2"/>
</p>
<p>
<br />

<h2>Step 3 - Local DNS Cache Exercise 🖥️🗃️</h2>

 - Back on the **Domain Controller VM**, open **DNS Manager** from **Windows Administrative Tools** in the **Start** menu.
 - In **DNS Manager**, on the left, navigate to **dc-1 > Forward Lookup Zones > mydomain.com** and select **mydomain.com**.
 - Double-click `mainframe` to change its A-record.
 - In the **mainframe Properties** menu, on the **Host (A)** tab, change **IP address** to the public DNS server for `www.google.com`, `8.8.8.8`.
 - Then click **Apply**, **OK**, and **OK**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/9bfa94e9-a770-4311-b29a-cee3a69278a2" alt="changemainframednstogoogle"/>
</p>
<p>
<br />

 - Go back to the **Client-1 VM** and open **PowerShell** as **administrator**.
 - Attempt to **ping** "mainframe" by issuing the following command: `ping mainframe`.
 - Observe how the `ping` replies are returned from `10.0.0.4` immediately after changing the IP address of "mainframe's" DNS A-record to `8.8.8.8`.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/94c78708-1f05-4604-ae91-01b7c75d1f09" alt="pingmainframerepliesolddns"/>
</p>
<p>
<br />

 - To observe the current local DNS cache on **Client-1**, execute `ipconfig /displaydns` in **PowerShell** as **administrator**.
 - Scroll down to the `mainframe` entry in the output and observe that the IP address for the (A) Host Record of `mainframe` reads `10.0.0.4` in **Client-1's** local DNS cache.
<img width="1200" src="https://github.com/user-attachments/assets/889ddaff-3202-4b01-828d-933943d751b0" alt="displaydnsolddnscache"/>
</p>
<p>
<br />

 - When **Client-1** attempts to `ping` "mainframe" after the A-record has been changed for "mainframe" on the **Domain Controller DNS server**, **Client-1** first checks its local DNS cache for any A-record entries of "mainframe's" hostname and IP address.
 - In this case, there is an A-record for "mainframe" in **Client-1's** local DNS cache.
 - However, the IP address for "mainframe" in the local DNS cache is a deprecated IP address from before "mainframe's" A-record was changed on the **Domain Controller**.
 - While the **Domain Controller DNS server** has a current A-record entry for "mainframe" with an IP address of `8.8.8.8`, the DNS cache shows an A-record for "mainframe" with an IP address of `10.0.0.4`.
 - There is a mismatch between the DNS IP addresses on **Client-1's** and the **Domain Controller's** for "mainframe".
 - As such, when **Client-1** attempts to `ping` "mainframe" after its A-record has been changed on the **Domain Controller**, **Client-1** receives replies with the old IP address for "mainframe".
<img width="1200" src="https://github.com/user-attachments/assets/bbb090b8-7b4c-484a-8a81-49bf4075c71d" alt="howdnsworks-dnscache1"/>
</p>
<p>
<br />

 - To force **Client-1** to acquire the updated A-record for "mainframe, **Client-1's** local DNS cache needs to be flushed.
   - To flush the local DNS cache on **Client-1**, run `ipconfig /flushdns` in **PowerShell** as **administrator**.
   - Notice the output of the command: `Windows IP Configuration Successfully flushed the DNS Resolver Cache.`
<img width="1200" src="https://github.com/user-attachments/assets/23bb01f4-81e8-428d-b44e-df64c72d3420" alt="flushclient1dnscache"/>
</p>
<p>
<br />

  - Verify that the local DNS cache has in fact been flushed on **Client-1** by running `ipconfig /displaydns` in **PowerShell** as **administrator**.
   <img width="1200" src="https://github.com/user-attachments/assets/26eff423-9a45-4692-92e2-3e7e20e69add" alt="displaydnsafterflush"/>
</p>
<p>
<br />

Notice how there is no longer an entry for "mainframe" in the local DNS cache:
 ```
    tsfe.trafficshaping.dsp.mp.microsoft.com
    ----------------------------------------
    Record Name . . . . . : tsfe.trafficshaping.dsp.mp.microsoft.com
    Record Type . . . . . : 5
    Time To Live  . . . . : 210
    Data Length . . . . . : 8
    Section . . . . . . . : Answer
    CNAME Record  . . . . : tsfe.trafficshaping.dsp.mp.microsoft.com.delivery.microsoft.com


    Record Name . . . . . : tsfe.trafficshaping.dsp.mp.microsoft.com.delivery.microsoft.com
    Record Type . . . . . : 5
    Time To Live  . . . . : 210
    Data Length . . . . . : 8
    Section . . . . . . . : Answer
    CNAME Record  . . . . : ts-frontend.trafficmanager.net


    Record Name . . . . . : ts-frontend.trafficmanager.net
    Record Type . . . . . : 1
    Time To Live  . . . . : 210
    Data Length . . . . . : 4
    Section . . . . . . . : Answer
    A (Host) Record . . . : 52.183.205.142


    v10.events.data.microsoft.com
    ----------------------------------------
    Record Name . . . . . : v10.events.data.microsoft.com
    Record Type . . . . . : 5
    Time To Live  . . . . : 2
    Data Length . . . . . : 8
    Section . . . . . . . : Answer
    CNAME Record  . . . . : win-global-asimov-leafs-events-data.trafficmanager.net


    Record Name . . . . . : win-global-asimov-leafs-events-data.trafficmanager.net
    Record Type . . . . . : 5
    Time To Live  . . . . : 2
    Data Length . . . . . : 8
    Section . . . . . . . : Answer
    CNAME Record  . . . . : onedscolprdeus18.eastus.cloudapp.azure.com


    Record Name . . . . . : onedscolprdeus18.eastus.cloudapp.azure.com
    Record Type . . . . . : 1
    Time To Live  . . . . : 2
    Data Length . . . . . : 4
    Section . . . . . . . : Answer
    A (Host) Record . . . : 20.42.73.30


    login.microsoftonline.com
    ----------------------------------------
    Record Name . . . . . : login.microsoftonline.com
    Record Type . . . . . : 5
    Time To Live  . . . . : 199
    Data Length . . . . . : 8
    Section . . . . . . . : Answer
    CNAME Record  . . . . : login.mso.msidentity.com


    Record Name . . . . . : login.mso.msidentity.com
    Record Type . . . . . : 5
    Time To Live  . . . . : 199
    Data Length . . . . . : 8
    Section . . . . . . . : Answer
    CNAME Record  . . . . : ak.privatelink.msidentity.com


    Record Name . . . . . : ak.privatelink.msidentity.com
    Record Type . . . . . : 5
    Time To Live  . . . . : 199
    Data Length . . . . . : 8
    Section . . . . . . . : Answer
    CNAME Record  . . . . : www.tm.a.prd.aadg.trafficmanager.net


    Record Name . . . . . : www.tm.a.prd.aadg.trafficmanager.net
    Record Type . . . . . : 1
    Time To Live  . . . . : 199
    Data Length . . . . . : 4
    Section . . . . . . . : Answer
    A (Host) Record . . . : 20.190.151.67


    Record Name . . . . . : www.tm.a.prd.aadg.trafficmanager.net
    Record Type . . . . . : 1
    Time To Live  . . . . : 199
    Data Length . . . . . : 4
    Section . . . . . . . : Answer
    A (Host) Record . . . : 20.190.151.132


    Record Name . . . . . : www.tm.a.prd.aadg.trafficmanager.net
    Record Type . . . . . : 1
    Time To Live  . . . . : 199
    Data Length . . . . . : 4
    Section . . . . . . . : Answer
    A (Host) Record . . . : 20.190.151.131


    Record Name . . . . . : www.tm.a.prd.aadg.trafficmanager.net
    Record Type . . . . . : 1
    Time To Live  . . . . : 199
    Data Length . . . . . : 4
    Section . . . . . . . : Answer
    A (Host) Record . . . : 20.190.151.133


    Record Name . . . . . : www.tm.a.prd.aadg.trafficmanager.net
    Record Type . . . . . : 1
    Time To Live  . . . . : 199
    Data Length . . . . . : 4
    Section . . . . . . . : Answer
    A (Host) Record . . . : 20.190.151.69


    Record Name . . . . . : www.tm.a.prd.aadg.trafficmanager.net
    Record Type . . . . . : 1
    Time To Live  . . . . : 199
    Data Length . . . . . : 4
    Section . . . . . . . : Answer
    A (Host) Record . . . : 20.190.151.6


    Record Name . . . . . : www.tm.a.prd.aadg.trafficmanager.net
    Record Type . . . . . : 1
    Time To Live  . . . . : 199
    Data Length . . . . . : 4
    Section . . . . . . . : Answer
    A (Host) Record . . . : 20.190.151.8


    Record Name . . . . . : www.tm.a.prd.aadg.trafficmanager.net
    Record Type . . . . . : 1
    Time To Live  . . . . : 199
    Data Length . . . . . : 4
    Section . . . . . . . : Answer
    A (Host) Record . . . : 20.190.151.134


    geo.prod.do.dsp.mp.microsoft.com
    ----------------------------------------
    Record Name . . . . . : geo.prod.do.dsp.mp.microsoft.com
    Record Type . . . . . : 5
    Time To Live  . . . . : 146
    Data Length . . . . . : 8
    Section . . . . . . . : Answer
    CNAME Record  . . . . : geo.prod.do.dsp.trafficmanager.net


    Record Name . . . . . : geo.prod.do.dsp.trafficmanager.net
    Record Type . . . . . : 5
    Time To Live  . . . . : 146
    Data Length . . . . . : 8
    Section . . . . . . . : Answer
    CNAME Record  . . . . : array520.prod.do.dsp.mp.microsoft.com


    Record Name . . . . . : array520.prod.do.dsp.mp.microsoft.com
    Record Type . . . . . : 1
    Time To Live  . . . . : 146
    Data Length . . . . . : 4
    Section . . . . . . . : Answer
    A (Host) Record . . . : 52.177.176.186


    _ldap._tcp.default-first-site-name._sites.dc._msdcs.mydomain.com
    ----------------------------------------
    Record Name . . . . . : _ldap._tcp.Default-First-Site-Name._sites.dc._msdcs.mydomain.com
    Record Type . . . . . : 33
    Time To Live  . . . . : 268
    Data Length . . . . . : 16
    Section . . . . . . . : Answer
    SRV Record  . . . . . : dc-1.mydomain.com
                            0
                            100
                            389


    Record Name . . . . . : dc-1.mydomain.com
    Record Type . . . . . : 1
    Time To Live  . . . . : 268
    Data Length . . . . . : 4
    Section . . . . . . . : Additional
    A (Host) Record . . . : 10.0.0.4


    1a.tlu.dl.delivery.mp.microsoft.com
    ----------------------------------------
    Record Name . . . . . : 1A.tlu.dl.delivery.mp.microsoft.com
    Record Type . . . . . : 5
    Time To Live  . . . . : 16
    Data Length . . . . . : 8
    Section . . . . . . . : Answer
    CNAME Record  . . . . : 1d.tlu.dl.delivery.mp.microsoft.com


    Record Name . . . . . : 1d.tlu.dl.delivery.mp.microsoft.com
    Record Type . . . . . : 5
    Time To Live  . . . . : 16
    Data Length . . . . . : 8
    Section . . . . . . . : Answer
    CNAME Record  . . . . : 1d.tlu.dl.delivery.mp.microsoft.com.delivery.microsoft.com


    Record Name . . . . . : 1d.tlu.dl.delivery.mp.microsoft.com.delivery.microsoft.com
    Record Type . . . . . : 5
    Time To Live  . . . . : 16
    Data Length . . . . . : 8
    Section . . . . . . . : Answer
    CNAME Record  . . . . : dcat-b-tlu-net.trafficmanager.net


    Record Name . . . . . : dcat-b-tlu-net.trafficmanager.net
    Record Type . . . . . : 5
    Time To Live  . . . . : 16
    Data Length . . . . . : 8
    Section . . . . . . . : Answer
    CNAME Record  . . . . : bg.microsoft.map.fastly.net


    Record Name . . . . . : bg.microsoft.map.fastly.net
    Record Type . . . . . : 1
    Time To Live  . . . . : 16
    Data Length . . . . . : 4
    Section . . . . . . . : Answer
    A (Host) Record . . . : 151.101.22.172


    dc-1.mydomain.com
    ----------------------------------------
    Record Name . . . . . : dc-1.mydomain.com
    Record Type . . . . . : 1
    Time To Live  . . . . : 3268
    Data Length . . . . . : 4
    Section . . . . . . . : Answer
    A (Host) Record . . . : 10.0.0.4


    fe3cr.delivery.mp.microsoft.com
    ----------------------------------------
    Record Name . . . . . : fe3cr.delivery.mp.microsoft.com
    Record Type . . . . . : 5
    Time To Live  . . . . : 143
    Data Length . . . . . : 8
    Section . . . . . . . : Answer
    CNAME Record  . . . . : fe3.delivery.mp.microsoft.com


    Record Name . . . . . : fe3.delivery.mp.microsoft.com
    Record Type . . . . . : 5
    Time To Live  . . . . : 143
    Data Length . . . . . : 8
    Section . . . . . . . : Answer
    CNAME Record  . . . . : glb.cws.prod.dcat.dsp.trafficmanager.net


    Record Name . . . . . : glb.cws.prod.dcat.dsp.trafficmanager.net
    Record Type . . . . . : 1
    Time To Live  . . . . : 143
    Data Length . . . . . : 4
    Section . . . . . . . : Answer
    A (Host) Record . . . : 20.3.187.198


    kv501.prod.do.dsp.mp.microsoft.com
    ----------------------------------------
    Record Name . . . . . : kv501.prod.do.dsp.mp.microsoft.com
    Record Type . . . . . : 5
    Time To Live  . . . . : 7
    Data Length . . . . . : 8
    Section . . . . . . . : Answer
    CNAME Record  . . . . : kv501.prod.do.dsp.mp.microsoft.com.edgekey.net


    Record Name . . . . . : kv501.prod.do.dsp.mp.microsoft.com.edgekey.net
    Record Type . . . . . : 5
    Time To Live  . . . . : 7
    Data Length . . . . . : 8
    Section . . . . . . . : Answer
    CNAME Record  . . . . : e10370.d.akamaiedge.net


    Record Name . . . . . : e10370.d.akamaiedge.net
    Record Type . . . . . : 1
    Time To Live  . . . . : 7
    Data Length . . . . . : 4
    Section . . . . . . . : Answer
    A (Host) Record . . . : 23.192.210.150
   ````

 - Next, attempt to `ping` "mainframe" from **Client-1** again.
 - Notice that the address of the new A-record in the replies is now `8.8.8.8`, the public DNS server for [www.google.com](www.google.com).
<img width="1200" src="https://github.com/user-attachments/assets/af3bb026-7b8a-4a68-a583-81bc6f3d2170" alt="pingmainframe2google"/>
</p>
<p>
<br />

 - Once **Client-1's** local DNS cache is flushed, it is primed to acquire the new A-record for "mainframe" once it attempts to `ping mainframe`.
 - Without any A-records for "mainframe" in its local DNS cache, **Client-1** checks its local Host file, from which it finds no A-records for "mainframe".
 - Next, **Client-1** queries the DNS server on the **Domain Controller** for the IP address that belongs to "mainframe" when it attempts to `ping` "mainframe.
 - On the backend, the DNS server on the **Domain Controller** hands-off the current A-record for "mainframe" to **Client-1**, and **Client-1** then stores the A-record in its local DNS cache for future queries of "mainframe".
<p>
<img width="1200" src="https://github.com/user-attachments/assets/42ed73cd-4e29-416c-ad31-2190597bb628" alt="howdnsworks-localcache2"/>
</p>
<p>
<br />

<h2>Step 4 - CNAME Record Exercise 📛📀</h2>

 - Back on `dc-1`,the **Domain Controller**, create a CNAME (Canonical Name) record that points the host "search" to [www.google.com](www.google.com).
   - First, click the **Start** menu and open the **DNS Manager** within **Windows Administrative Tools**.
   - In **DNS Manager**, on the left, navigate to **DNS > dc-1 > Forward Lookup Zones** and click the domain on the DNS server, **mydomain.com**.
   - Then, on the right, right-click anywhere in the blank space and select **New Alias (CNAME)**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/9d6366a2-a7c2-4a4e-8203-126d76bab0c4" alt="dnsmanagernewaliascname"/>
</p>
<p>
<br />

 - Next, in the **New Resource Record** menu, enter `search` in the **Alias name** field.
 - In the **Fully qualified domain name (FQDN)** field, enter `www.google.com`, which is where `search` is going to be mapped to.
 - Then click **OK**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/290786bf-6781-4418-8281-8860f7618f4b" alt="newresourcerecordaliascname"/>
</p>
<p>
<br />

 - Back on **Client-1**, run **PowerShell** as **administrator** and attempt to `ping` "search" with `ping search`.
 - Observe that the output of the command indicates **Client-1** is `pinging wwww.google.com` at the IP address, `142.250.73.132`. 
<p>
<img width="1200" src="https://github.com/user-attachments/assets/4fcc5e5c-7556-42dd-92ba-3e97da6ab98f" alt="pingsearch2"/>
</p>
<p>
<br />

 - When `nslookup search` is executed in **PowerShell**, the **Domain Controller** resolves host, "search", to `www.google.com` at the IP addresses, `2607:f8b0:400a:806::2004` and `142.250.73.100`.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/f53464c1-c874-4ecc-a0c8-66e2286d9b31" alt="nslookupsearch"/>
</p>
<p>
<br />


