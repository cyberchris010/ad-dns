<p align="center">
<img src="https://github.com/user-attachments/assets/090ca439-9a93-458d-9b6f-a7416997ff8c" alt="activedir-logo"/>
</p>


<h1>Understanding DNS with Active Directory</h1>
This tutorial explains how to use Active Directory to gain a deeper understanding of how DNS works.<br />


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
<img width="1200" src="https://github.com/user-attachments/assets/ac3043b3-bf97-44e6-828a-a74f7a8f7cc7" alt="howdnsworksgeneral"/>
</p>
<p>
<br />

  - The first and most common type of DNS record is the **A-record**, or **Address Record**.
  - A-records resolve domain names to IPv4 addresses, which are 32-bit numerical addresses.
  - When a user types in a domain name, like `google.com`, into a web browser, DNS uses an A record to resolve the domain name, like `google.com`, to an IP address.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/9849e3ac-6a12-433a-87eb-70de63446663" alt="howdnsworks-arecords"/>
</p>
<p>
<br />

 - When a client computer searches for a domain name, it performs three sequential checks for the A-record of that domain name on the backend:
   - First, the client checks its own **local DNS cache** for the specific A-record mapping the given domain name to its current IPv4 address.
   - **Local DNS cache** is a temporary storage location on a client computer that keeps records of previously visited domain names (`google.com`) and their corresponding IP addresses.
   - **Local DNS cache** is important because it expedites the process of a client computer accessing websites a user has visited before by allowing the client computer to quickly retrieve a stored A-record instead of performing a complete DNS lookup every time.
   - If a client computer contains no A-record in its **local DNS cache**, it resorts to trying to find a given A-record in its **local Host file**, which is stored in long-term memory on its hard drive. This is much less efficient than retrieving it from its **local DNS cache**.
   - If a client computer can't find the A-record in the **local Host File**, it must perform a DNS lookup with its **DNS server**, which is the least efficient way to acquire an A-record.
<p>
<img width="1628" height="780" alt="howdnsworks-localcache2" src="https://github.com/user-attachments/assets/aab19d5f-2ae7-4318-8188-79f93000c9fd" />
</p>
<p>
<br />
 
  - A **CNAME (Canonical Name) record** is a DNS record that is similar to an A-record.
  - Whereas A-records resolve domain names to IP addresses, **CNAME records** resolve domain names to domain names via aliases.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/fb9518cc-b2c7-4595-8a7d-4241d0c78444" alt="howdnsworks-cnamerecords"/>
</p>
<p>
<br />

 - A hands-on way to gain deeper insight into how DNS works is to deploy two Azure Virtual Machines (a client and an Active Directory domain controller) and observe how A-records, local DNS cache, and CNAME records function between them.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/08e1ae1c-48be-47c6-99ca-e2bb9beeb24e" alt="howdnsworks-arecords-1"/>
</p>
<p>
<br />

<h2>Step 2 - A-Record Exercise 🅰️📀</h2>

- From a **local macOS system**, log in to the **Domain Controller VM** as admin user, `jane_admin`.
  -  Open the **Microsoft App (Remote Desktop)** and double-click the **Domain Controller VM** tile under **Saved PCs**.
 - Log-in to `dc-1` as `jane_admin` by entering `mydomain.com\jane_admin` for **Username** and the corresponding password.
 - Click **Continue**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/6824ae94-dd69-41ac-a58f-b6c764727f0b" alt="rdpasdomadminindc1"/>
</p>
<p>
<br /> 

- Log in to the **Client VM** as the **domain admin user**, `jane_admin`:
   - Open the **Microsoft App (formerly Remote Desktop)** on the **local macOS system**.
   - Double-click the **Client-1 VM** tile, enter `mydomain.com\jane_admin` for **Username**, and the corresponding password for **Password**.
   - Click **Continue**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/70eb54d1-9c57-42a0-a54a-be9ce751504c" alt="rdpasjaneadminclientvm"/>
</p>
<p>
<br />

- On **Client-1**, open **PowerShell** as **administrator** and attempt to `ping mainframe`.
- Observe what happens when it fails: a message is returned that reads, `Ping request could not find host mainframe. Please check the name and try again.`
<img width="1200" src="https://github.com/user-attachments/assets/d4d0070c-c64c-43ed-9f47-c0d465a44e35" alt="client1pingsmainframe1"/>
</p>
<p>
<br />

 - When **Client-1** attempts to interact with a hostname on the network, in this case, when it tries to `ping mainframe`, **Client-1** first checks its local DNS cache for `mainframe's` A-record.
   - This is the fastest method that **Client-1** can access an A-record for the hostname, `mainframe`.
 - When **Client-1** doesn't find an A-record for the `mainframe` hostname, it checks its local **host file**.
   - This is the second fastest method **Client-1** can access an A-record for the hostname, `mainframe`. 
 - When **Client-1** fails to find a `mainframe` record in its **host file**, it then checks its **DNS Server**, which, in this case, is the **dc-1 VM**, the **Domain Controller**.
   - This is the slowest method **Client-1** can access an A-record for hostname, `mainframe`.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/68b7efa9-ab24-4e52-bc9c-1ad681c3003c" alt="howdnsworks-arecords-1"/>
</p>
<p>
<br />

- To simulate **Client-1** checking its local DNS cache for `mainframe's` A-record, open **PowerShell** as **administrator** from **Client-1** and run `ipconfig /displaydns`.
- A long list of DNS records is then printed out, which **Client-1** has in its local DNS cache.
- However, there are no records for hostname, `mainframe`.
<img width="1200" src="https://github.com/user-attachments/assets/3695255b-94a9-4cb7-893c-11ea99b2b744" alt="ipconfigdisplaydns"/>
</p>
<p>
<br />

- When **Client-1** can't find an A-record for hostname, `mainframe`, in its local DNS cache, it checks its **local Host file**.
- To demonstrate **Client-1** checking its host file for hostname, `mainframe`, open **File Explorer** on **Client-1** and navigate to **This PC > Windows (C:) > Windows > System32 > drivers > etc > hosts**.
- Double-click `hosts`, which is **Client-1's** local host file, and open it with **Notepad**.
  - The local host file is where **Client-1** stores specific IP address-to-hostname mappings.
  - In this case, there is no mapping for `mainframe` in **Client-1's** host file.
<img width="1200" src="https://github.com/user-attachments/assets/50d46d80-0e33-4d9f-a0ca-b1e0d973170f" alt="client1localhostfile"/>
</p>
<p>
<br />

- After **Client-1** fails to find a mapping for `mainframe` in its local host file, it checks its **DNS Server** for `mainframe's` A-record.
- To simulate **Client-1** checking with its DNS Server for `mainframe`, open **PowerShell** as **administrator** on **Client-1** and attempt an `nslookup` of `mainframe`: `nslookup mainframe`.
  - It returns the hostname `dc-1.internal.cloudapp.net` and the Private IP address of the **DNS Server** (`10.0.0.4`) that **Client-1** is querying.
  - It also returns a message that `dc-1.internal.cloudapp.net can't find mainframe: Non-existent domain`.
  - The **DNS Server** is responding to **Client-1's** query for `mainframe`, explaining that it shows no records for `mainframe`.
<img width="1200" src="https://github.com/user-attachments/assets/7d19d3b6-d8fd-41aa-b7b1-4ef5f4420c33" alt="nslookupmainframe"/>
</p>
<p>
<br />

- **Client-1** can't find `mainframe` in its local DNS cache, nor in its local host file, nor from its local DNS Server, so a DNS A-record for `mainframe` needs to be created on the domain controller (**DC-1 VM**).
- Then the DNS A-record for `mainframe` on the domain controller needs to be configured to point to the domain controller's **Private IP address**.
  - On the **Domain Controller VM** (dc-1), click the **Start** menu in the bottom left, click **Windows Administrative Tools**, then click **DNS** to open **DNS Manager**.
  - On the left, click `dc-1` to expand it, then navigate to **Forward Lookup Zones > mydomain.com**.
<img width="1200" src="https://github.com/user-attachments/assets/d5ba9a9d-820f-40e8-a77b-5c7f704486b8" alt="dnsmanagerfilepath"/>
</p>
<p>
<br />

 - Right-click anywhere in the blank space on the right and select **New Host (A or AAA)**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/ea431792-bb58-46bc-8d20-b773418cd22d" alt="newhostaoraaa"/>
</p>
<p>
<br />

 - In the **New Host** dialog box, enter `mainframe` in the **Name** field.
 - To find the **Private IP address** of the domain controller, open **PowerShell**, run `ipconfig`, then find the `IPv4 Address`, which is `10.0.0.4`.
 - Enter the **Private IP address** of the domain controller, `10.0.0.4`, in the **IP address** field within the **New Host** menu.
 - Select **Create associated pointer (PTR) record**, then click **Add Host**.
 - When the **PTR record** message displays, click **OK**.
 - Then click **Done** and click **Refresh** at the top of the **DNS Manager** menu.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/5eba2f19-1ccf-45c6-88ca-5ae6820d3fe8" alt="addnewarecordfornewhost"/>
</p>
<p>
<br />

 - Notice the newly created **A record** for `mainframe`, which points to the **Private IP address** of the domain controller (10.0.0.4) in the **DNS Manager**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/7af50bdf-90a3-4ca8-b5fd-ed2c1899f8e6" alt="mainframehostarecordcreated"/>
</p>
<p>
<br />

- Now that the domain controller contains an A-record for `mainframe`, attempt to `ping` "mainframe" again from **Client-1**:
  - Back on **Client-1**, run **PowerShell** as **administrator** and run `ping mainframe`.
  - Notice that it matches `mainframe` to 10.0.0.4 and then a `ping` replies are returned.
<img width="1200" src="https://github.com/user-attachments/assets/0feaa451-ac10-4a8e-80ec-3010078a8d34" alt="client1succeedsinpingingmainframe"/>
</p>
<p>
<br />

 - Once an A-record for `mainframe` is created on the domain controller, when **Client-1** `pings` "mainframe", it goes down its checklist on the backend.
   - After failing to find `mainframe` in its local DNS cache, **Client-1** checks its local host file, which doesn't contain any mappings for `mainframe`.
   - However, when **Client-1** checks its **DNS Server**, the DNS Server on the domain controller finds the A-record that was created for `mainframe`.
   - The DNS server then replies to **Client-1's** `ping` with the IP address of `mainframe`.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/fbc335cf-0f4b-4fd5-8f1c-c0ac91454d9c" alt="howdnsworks-arecords-2"/>
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
<img width="1200" src="https://github.com/user-attachments/assets/c32f42aa-27b5-4b17-997a-cf931cc90c38" alt="changemainframednstogoogle"/>
</p>
<p>
<br />

 - Go back to the **Client-1 VM** and open **PowerShell** as **administrator**.
 - Attempt to **ping** "mainframe" by issuing the following command: `ping mainframe`.
 - Observe how the `ping` replies are returned from `10.0.0.4` immediately after changing the IP address of "mainframe's" DNS A-record to `8.8.8.8`.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/3d0acd85-636e-496a-ba02-e9d2cbae4f27" alt="pingmainframerepliesolddns"/>
</p>
<p>
<br />

 - To observe the current local DNS cache on **Client-1**, execute `ipconfig /displaydns` in **PowerShell** as **administrator**.
 - Scroll down to the `mainframe` entry in the output and observe that the IP address for the (A) Host Record of `mainframe` reads `10.0.0.4` in **Client-1's** local DNS cache.
<img width="1200" src="https://github.com/user-attachments/assets/fa9eaa82-757a-41c6-ac81-f49710a04973" alt="displaydnsolddnscache"/>
</p>
<p>
<br />

 - When **Client-1** attempts to `ping` "mainframe" after the A-record has been changed for "mainframe" on the **Domain Controller DNS server**, **Client-1** first checks its local DNS cache for any A-record entries of "mainframe's" hostname and IP address.
 - In this case, there is an A-record for "mainframe" in **Client-1's** local DNS cache.
 - However, the IP address for "mainframe" in the local DNS cache is a deprecated IP address from before "mainframe's" A-record was changed on the **Domain Controller**.
 - While the **Domain Controller DNS server** has a current A-record entry for "mainframe" with an IP address of `8.8.8.8`, the DNS cache shows an A-record for "mainframe" with an IP address of `10.0.0.4`.
 - There is a mismatch between the DNS IP addresses for "mainframe" on **Client-1** and the **Domain Controller**.
 - As such, when **Client-1** attempts to `ping` "mainframe" after its A-record has been changed on the **Domain Controller**, **Client-1** receives replies with the old IP address for "mainframe".
<img width="1578" height="785" alt="howdnsworks-dnscache1" src="https://github.com/user-attachments/assets/0a47f911-db62-4a44-9845-e18a57a4bede" />
</p>
<p>
<br />

 - To force **Client-1** to acquire the updated A-record for "mainframe, **Client-1's** local DNS cache needs to be flushed.
   - To flush the local DNS cache on **Client-1**, run `ipconfig /flushdns` in **PowerShell** as **administrator**.
   - Notice the output of the command: `Windows IP Configuration Successfully flushed the DNS Resolver Cache.`
<img width="1200" src="https://github.com/user-attachments/assets/f8b19232-d2ad-4503-ac3a-075d36fa33ac" alt="flushclient1dnscache"/>
</p>
<p>
<br />

  - Verify that the local DNS cache has been flushed on **Client-1** by running `ipconfig /displaydns` in **PowerShell** as **administrator**.
   <img width="1200" src="https://github.com/user-attachments/assets/544b2c4f-5de9-448e-b8f9-27118914ebfc" alt="displaydnsafterflush"/>
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
<img width="1200" src="https://github.com/user-attachments/assets/60801e66-d1a6-4105-a954-0994fb3e8cd7" alt="pingmainframe2google"/>
</p>
<p>
<br />

 - Once **Client-1's** local DNS cache is flushed, it is primed to acquire the new A-record for "mainframe" once it attempts to `ping mainframe`.
 - Without any A-records for "mainframe" in its local DNS cache, **Client-1** checks its local Host file, from which it finds no A-records for "mainframe".
 - Next, **Client-1** queries the DNS server on the **Domain Controller** for the IP address that belongs to "mainframe" when it attempts to `ping` "mainframe.
 - On the backend, the DNS server on the **Domain Controller** hands off the current A-record for "mainframe" to **Client-1**, and **Client-1** then stores the A-record in its local DNS cache for future queries of "mainframe".
<p>
<img width="1200" src="https://github.com/user-attachments/assets/5b5d4354-36a5-41cb-9385-53101b7b2b33" alt="howdnsworks-localcache2"/>
</p>
<p>
<br />

<h2>Step 4 - CNAME Record Exercise 📛📀</h2>

 - Back on `dc-1`, the **Domain Controller**, create a CNAME (Canonical Name) record that points the host "search" to [www.google.com](www.google.com).
   - First, click the **Start** menu and open the **DNS Manager** within **Windows Administrative Tools**.
   - In **DNS Manager**, on the left, navigate to **DNS > dc-1 > Forward Lookup Zones** and click the domain on the DNS server, **mydomain.com**.
   - Then, on the right, right-click anywhere in the blank space and select **New Alias (CNAME)**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/e796868f-b367-4e6f-96f1-2a7beb95d7c0" alt="dnsmanagernewaliascname"/>
</p>
<p>
<br />

 - Next, in the **New Resource Record** menu, enter `search` in the **Alias name** field.
 - In the **Fully qualified domain name (FQDN)** field, enter `www.google.com`, which is where `search` is going to be mapped to.
 - Then click **OK**.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/72f7b32f-d479-4009-8910-37ddb29a9843" alt="newresourcerecordaliascname"/>
</p>
<p>
<br />

 - Back on **Client-1**, run **PowerShell** as **administrator** and attempt to `ping` "search" with `ping search`.
 - Observe that the output of the command indicates **Client-1** is `pinging wwww.google.com` at the IP address, `142.250.73.132`. 
<p>
<img width="1200" src="https://github.com/user-attachments/assets/0ade1e1d-7871-49ba-8cf2-2027c25a8305" alt="pingsearch2"/>
</p>
<p>
<br />

 - When `nslookup search` is executed in **PowerShell**, the **Domain Controller** resolves the host, "search", to `www.google.com` at the IP addresses, `2607:f8b0:400a:806::2004` and `142.250.73.100`.
<p>
<img width="1200" src="https://github.com/user-attachments/assets/091189d9-4acc-415d-ba7e-c94d2266bf29" alt="nslookupsearch"/>
</p>
<p>
<br />


