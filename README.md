<h1>Enterprise Windows Server 2022 Active Directory Home Lab</h1>

<p>
  A comprehensive, hands-on systems administration and IT support lab simulating an enterprise identity management environment. This project demonstrates deploying Windows Server 2022 inside Oracle VM VirtualBox, configuring virtualization tools and host-guest shared storage, renaming and promoting the server to a dedicated Domain Controller (<code>NY-DC-01</code>) hosting the <code>kevtech.com</code> forest root domain, administering user accounts and security policies via Active Directory Users and Computers (ADUC), and auditing domain health using command-line tools.
</p>

<hr />

<h2>Table of Contents</h2>
<ul>
  <li><a href="#project-architecture">Project Architecture &amp; Topology</a></li>
  <li><a href="#tech-stack">Tech Stack &amp; Prerequisites</a></li>
  <li><a href="#phase-1">Phase 1: Virtualization &amp; Server 2022 Base Provisioning</a></li>
  <li><a href="#phase-2">Phase 2: Guest Additions &amp; Shared Folder Setup</a></li>
  <li><a href="#phase-3">Phase 3: Server Renaming &amp; Domain Controller Promotion</a></li>
  <li><a href="#phase-4">Phase 4: Identity &amp; Access Management (ADUC Operations)</a></li>
  <li><a href="#phase-5">Phase 5: Command-Line Auditing &amp; System Verification</a></li>
  <li><a href="#commands-reference">Key Commands Reference</a></li>
  <li><a href="#future-expansions">Future Lab Expansions</a></li>
</ul>

<hr />

<h2 id="project-architecture">Project Architecture &amp; Topology</h2>

<pre>
[ Physical Host Machine ]
           │
           │  (VirtualBox Host-to-Guest Shared Folder: "helpdesklab")
           ▼
[ Virtual Machine: Windows Server 2022 Standard (Desktop Experience) ]
           │
           ├── Hostname: NY-DC-01 (New York Domain Controller 01)
           ├── Roles: Active Directory Domain Services (AD DS), DNS
           └── Active Directory Forest Root: kevtech.com
                 │
                 ├── Users Container / Organization Units
                 │     ├── User: Mark (Provisioned Employee Account)
                 │     │     ├── Street / Country: United States
                 │     │     ├── Logon Hours: Restricted / Custom Window
                 │     │     └── Account Controls: Password Reset, Unlock, Enable/Disable
                 │     └── User: Kevin (Secondary Administrator / Lab Account)
                 │
                 └── Command-Line Verification &amp; Health Auditing
                       ├── whoami (Context: KEVTECH\Administrator)
                       ├── net user Mark /domain (Group memberships, expiry, logon checks)
                       └── systeminfo (Domain membership, OS version, boot times)
</pre>

<hr />

<h2 id="tech-stack">Tech Stack &amp; Prerequisites</h2>

<ul>
  <li><strong>Hypervisor:</strong> Oracle VM VirtualBox (v7.1.4+)</li>
  <li><strong>Server Operating System:</strong> Windows Server 2022 Standard Evaluation (<em>Desktop Experience</em> selected for GUI management consoles)</li>
  <li><strong>Client Operating System (Planned for domain join):</strong> Windows 11 Enterprise / Pro ISO (Media Creation Tool)</li>
  <li><strong>Directory Services:</strong> Active Directory Domain Services (AD DS), DNS</li>
  <li><strong>Administrative Tools:</strong> Active Directory Users and Computers (ADUC), PowerShell 5.1 / 7, Windows Command Prompt (<code>cmd</code>)</li>
  <li><strong>Hardware Sizing Assigned:</strong>
    <ul>
      <li><strong>vCPUs:</strong> 2 Processors</li>
      <li><strong>Memory:</strong> 8 GB RAM (8192 MB)</li>
      <li><strong>Storage:</strong> Dynamic VDI virtual hard disk</li>
      <li><strong>Display Scale Factor:</strong> 125% virtual screen scaling for management console visibility</li>
    </ul>
  </li>
</ul>

<hr />

<h2 id="phase-1">Phase 1: Virtualization &amp; Server 2022 Base Provisioning</h2>

<ol>
  <li>
    <p><strong>Virtual Machine Creation:</strong></p>
    <p>Configured a new VM in VirtualBox labeled <code>Server 2022</code>. Allocated <strong>8 GB RAM</strong> and <strong>2 vCPUs</strong>. Attached the downloaded Windows Server 2022 Evaluation 64-bit ISO to the virtual optical drive.</p>
  </li>
  <li>
    <p><strong>Operating System Installation:</strong></p>
    <p>Booted the installer and explicitly selected <strong>Windows Server 2022 Standard Evaluation (Desktop Experience)</strong>. Selecting Desktop Experience is mandatory for this lab to enable Server Manager, Active Directory administrative snap-ins, and local GUI consoles rather than headless Server Core.</p>
    <p>Accepted standard custom disk installation and completed base setup.</p>
  </li>
  <li>
    <p><strong>Initial Operating System Configuration:</strong></p>
    <p>Set a strong local Administrator password and updated the system time zone to the organizational standard (<strong>Eastern Time (US &amp; Canada)</strong>).</p>
  </li>
</ol>

<hr />

<h2 id="phase-2">Phase 2: Guest Additions &amp; Shared Folder Setup</h2>

<p>
  To facilitate frictionless host-to-guest file exchange for packages, deployment scripts, and media assets:
</p>

<ol>
  <li>
    <p>Mounted VirtualBox Guest Additions via <strong>Devices &gt; Insert Guest Additions CD image</strong>.</p>
  </li>
  <li>
    <p>Ran the installation executable directly from the virtual optical drive and completed the required system reboot.</p>
  </li>
  <li>
    <p>Configured an auto-mounting shared folder:</p>
    <ul>
      <li><strong>Folder Name:</strong> <code>helpdesklab</code></li>
      <li><strong>Configuration:</strong> Auto-mount: <em>Enabled</em>, Make Permanent: <em>Enabled</em>.</li>
    </ul>
  </li>
  <li>
    <p>Verified synchronization by copying an asset (<code>Kevtech logo.jpg</code>) from the host into the shared folder and accessing it within the guest VM under <code>C:\Users\Kevin\helpdesklab</code>.</p>
  </li>
</ol>

<hr />

<h2 id="phase-3">Phase 3: Server Renaming &amp; Domain Controller Promotion</h2>

<ol>
  <li>
    <p><strong>Standardized Host Naming:</strong></p>
    <p>Replaced the default randomized NetBIOS name (e.g., <code>WIN-0907BLUE</code>) with an enterprise geographical schema:</p>
    <pre>NY-DC-01</pre>
    <p><em>(Signifying New York Domain Controller 01)</em>. Rebooted the machine to apply computer name changes across the Windows registry.</p>
  </li>
  <li>
    <p><strong>Active Directory Domain Services (AD DS) Role Installation:</strong></p>
    <p>Opened <strong>Server Manager &gt; Manage &gt; Add Roles and Features</strong>. Selected <em>Role-based or feature-based installation</em>, checked <strong>Active Directory Domain Services</strong>, accepted all required dependencies and sub-features, and completed installation.</p>
  </li>
  <li>
    <p><strong>Promoting to Domain Controller:</strong></p>
    <p>Triggered the post-deployment configuration banner: <em>Promote this server to a domain controller</em>.</p>
    <ul>
      <li>Deployment Operation: <strong>Add a new forest</strong>.</li>
      <li>Root domain name: <code>kevtech.com</code>.</li>
      <li>Configured Directory Services Restore Mode (DSRM) recovery password.</li>
      <li>Delegated local DNS server and Global Catalog placement.</li>
      <li>Exported and executed the underlying PowerShell promotion command set as Administrator for rapid automated provisioning.</li>
      <li>The server performed an automated restart to apply directory schema and initialize Active Directory partitions.</li>
    </ul>
  </li>
</ol>

<hr />

<h2 id="phase-4">Phase 4: Identity &amp; Access Management (ADUC Operations)</h2>

<p>
  Opened <strong>Active Directory Users and Computers (<code>dsa.msc</code>)</strong> to practice routine Tier-1 and Tier-2 administrative tasks:
</p>

<ol>
  <li>
    <p><strong>User Account Creation:</strong></p>
    <p>Inside the <code>kevtech.com\Users</code> container, provisioned two user identities:</p>
    <ul>
      <li><strong>User 1:</strong> <code>Mark</code> (Logon name: <code>mark@kevtech.com</code>)</li>
      <li><strong>User 2:</strong> <code>Kevin</code> (Logon name: <code>kevin@kevtech.com</code>)</li>
    </ul>
    <p>Assigned baseline passwords and configured first-time logon policies.</p>
  </li>
  <li>
    <p><strong>Granular User Attributes &amp; Metadata:</strong></p>
    <p>Populated enterprise profile fields for <code>Mark</code>: Address, Office, Country/Region set to <code>United States</code>, telephone number, department, job title, and managerial reporting chain.</p>
  </li>
  <li>
    <p><strong>Security Controls &amp; Account Lifecycle Management:</strong></p>
    <ul>
      <li><strong>Logon Hours Restriction:</strong> Configured the logon hours matrix under account properties to restrict logon privileges (e.g., granting Monday through Friday business hours while denying authentication during off-hours and weekends).</li>
      <li><strong>Disabling / Enabling Accounts:</strong> Tested account deactivation via right-click <em>Disable Account</em> (verifying the down-arrow badge on the object icon) and restored access using <em>Enable Account</em>.</li>
      <li><strong>Account Unlock:</strong> Evaluated the <em>Unlock account</em> checkbox workflow under the Account tab to clear simulated account lockout thresholds.</li>
      <li><strong>Password Reset:</strong> Executed manual administrative password resets.</li>
    </ul>
  </li>
</ol>

<hr />

<h2 id="phase-5">Phase 5: Command-Line Auditing &amp; System Verification</h2>

<p>Executed native Windows Command Prompt utilities to validate authentication state, group policies, and domain configuration.</p>

<h3>1. Current Security Context</h3>
<pre><code>whoami</code></pre>
<p>
  <strong>Output:</strong> <code>KEVTECH\administrator</code><br />
  Confirms the active console session is running under domain administrative authority rather than a local workstation account.
</p>

<h3>2. Active Directory User Query</h3>
<pre><code>net user Mark /domain</code></pre>
<p>
  <strong>Audited Information:</strong>
</p>
<ul>
  <li>Account active status (<code>Yes</code>)</li>
  <li>Password last set, password expiration dates, and password changeable window</li>
  <li>Logon hours authorization limits</li>
  <li>Assigned global and local security groups (e.g., <code>*Domain Users</code>)</li>
</ul>

<h3>3. Domain Controller &amp; OS Metadata Audit</h3>
<pre><code>systeminfo</code></pre>
<p>
  <strong>Key Parameters Verified:</strong>
</p>
<ul>
  <li><strong>Host Name:</strong> <code>NY-DC-01</code></li>
  <li><strong>OS Name:</strong> <code>Microsoft Windows Server 2022 Standard Evaluation</code></li>
  <li><strong>Domain:</strong> <code>kevtech.com</code></li>
  <li><strong>Logon Server:</strong> <code>\\NY-DC-01</code></li>
  <li><strong>Total Physical Memory:</strong> <code>8,192 MB</code></li>
</ul>

<h3>4. Administrative Shutdown &amp; Restart Commands</h3>
<p>Practiced CLI maintenance syntax:</p>
<ul>
  <li><code>shutdown /i</code> — Launches the Remote Shutdown Graphical User Interface dialog.</li>
  <li><code>shutdown /s /t 0</code> — Initiates an immediate full system shutdown.</li>
  <li><code>shutdown /r /t 0</code> — Executes an immediate clean system restart.</li>
  <li><code>shutdown /g</code> — Restarts the server and automatically restarts registered applications.</li>
  <li><code>shutdown /?</code> — Displays full switch documentation.</li>
</ul>

<hr />

<h2 id="commands-reference">Key Commands Reference</h2>

<table>
  <thead>
    <tr>
      <th>Command</th>
      <th>Environment</th>
      <th>Function &amp; Objective</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>whoami</code></td>
      <td>Command Prompt / PowerShell</td>
      <td>Identifies current domain authority and active username context</td>
    </tr>
    <tr>
      <td><code>net user &lt;username&gt; /domain</code></td>
      <td>Command Prompt / PowerShell</td>
      <td>Queries the Active Directory database for user account properties, security groups, and expiration times</td>
    </tr>
    <tr>
      <td><code>systeminfo</code></td>
      <td>Command Prompt</td>
      <td>Outputs comprehensive system configuration, network parameters, OS build, and domain registration status</td>
    </tr>
    <tr>
      <td><code>dsa.msc</code></td>
      <td>Run Dialog / CLI</td>
      <td>Directly launches the Active Directory Users and Computers MMC console</td>
    </tr>
    <tr>
      <td><code>shutdown /r /t 0</code></td>
      <td>Command Prompt</td>
      <td>Executes an immediate clean reboot of the domain controller</td>
    </tr>
    <tr>
      <td><code>shutdown /i</code></td>
      <td>Command Prompt</td>
      <td>Opens the graphical remote shutdown tool</td>
    </tr>
  </tbody>
</table>

<hr />

<h2 id="future-expansions">Future Lab Expansions</h2>

<ul>
  <li><strong>Windows 11 Domain Join:</strong> Deploy a Windows 11 Enterprise client VM, align DNS settings with <code>NY-DC-01</code>, and join the client to the <code>kevtech.com</code> domain.</li>
  <li><strong>Group Policy Management (GPO):</strong> Configure and link Group Policy Objects for password complexity, mapped drives, and workstation lockouts.</li>
  <li><strong>Modern Endpoint Management (Microsoft Intune &amp; Entra ID):</strong> Implement hybrid cloud identity syncing and device management capabilities.</li>
  <li><strong>Help Desk Ticketing &amp; Patch Automation:</strong> Integrate an IT service management (ITSM) ticketing tool and automated software deployment workflows.</li>
</ul>
