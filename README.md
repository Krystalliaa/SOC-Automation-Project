<h1>Setting up a SOC Automation Home Lab</h1>

<p>This document outlines the setup of a home lab focused on Security Operations Center (SOC) automation. This lab aims to provide practical experience with tools and techniques used in modern SOC environments, particularly focusing on automation and orchestration. This documentation is intended for junior cybersecurity professionals looking to enhance their skills in this area.</p>

<h2>Lab Components</h2>

<p>This initial phase of the lab involves setting up a Windows 10 virtual machine, which will serve as a representative endpoint within our simulated network.</p>

<ul>
    <li><strong>Windows 10 Pro VM:</strong> This virtual machine will simulate a typical user workstation and be used for testing automation scripts and tools. We will be installing the Professional edition of Windows 10 for its added features relevant to domain joining and management.</li>
</ul>

<h2>What I Did (Step-by-Step)</h2>

<h3>1. Creating the Windows 10 Pro Virtual Machine</h3>

<p>The first step was to create the virtual environment using Oracle VirtualBox. I downloaded and installed Oracle VirtualBox from the official website (<a href="https://www.virtualbox.org/">https://www.virtualbox.org/</a>) and obtained a Windows 10 Pro ISO file.</p>

<p>Within VirtualBox, I created a new virtual machine for Windows 10 Pro, following these steps:</p>

<ol>
    <li><strong>Open VirtualBox and Click "New":</strong> I launched the VirtualBox application and clicked the "New" button in the toolbar to begin creating a new virtual machine.</li>
    <li><strong>Name and Operating System Selection:</strong> In the "Create Virtual Machine" wizard, I provided a descriptive name for the VM (e.g., "Win10-Automation"). I then selected the operating system type as "Microsoft Windows" and the version as "Windows 10 (64-bit)".</li>
    <li><strong>Memory Size (RAM):</strong> I allocated an appropriate amount of RAM to the VM. 4GB (4096 MB) is usually sufficient for Windows 10, but you can adjust this based on your host system's resources.</li>
    <li><strong>Hard Disk:</strong> I selected "Create a virtual hard disk now" and clicked "Create". In the "Hard disk file type" window, I chose "VDI (VirtualBox Disk Image)". In the "Storage on physical hard disk" window, I selected "Dynamically allocated". This allows the virtual hard disk to grow as needed, saving space on the host system. I then allocated a reasonable size for the virtual hard disk (e.g., 50GB).</li>
    <li><strong>Virtual Machine Settings (Optional but Recommended):</strong> After creating the VM, I accessed its settings to make some optional but recommended adjustments:
        <ul>
            <li><strong>System -> Processor:</strong> If your host system has multiple cores, you can allocate more than one core to the VM for better performance.</li>
            <li><strong>Display -> Video Memory:</strong> Increasing the video memory can improve graphics performance within the VM.</li>
            <li><strong>Storage -> Controller: IDE:</strong> I clicked on the empty disc icon and selected "Choose a disk file..." to select the Windows 10 Pro ISO file I had downloaded. This makes the ISO available to the VM as a bootable installation medium.</li>
            <li><strong>Network:</strong> I will configure the network settings later, after the OS installation is complete.</li>
        </ul>
    </li>
    <li><strong>Start the Virtual Machine:</strong> After configuring the settings, I selected the newly created VM and clicked "Start". This launched the VM and began the Windows 10 Pro installation process.</li>
</ol>

<p>The Windows 10 Pro installation process then proceeded as usual. I followed the on-screen prompts, selected my preferred settings, and created a local user account with a strong password.</p>


<h2>Installing Sysmon on the Windows 10 Client</h2>

<p>Sysmon is a powerful tool that enhances endpoint visibility by capturing detailed system activity. We will install Sysmon on our Windows 10 client machine (e.g., "Win10-Automation") to generate security events that will be collected by Wazuh.</p>

<h3>Prerequisites</h3>

<ul>
    <li>Windows 10 client machine with administrative privileges.</li>
    <li>Downloaded Sysmon archive (e.g., `sysmon.zip`) from the official Microsoft Sysinternals website (<a href="https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon">https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon</a>).</li>
    <li>Downloaded Sysmon configuration file (e.g., `sysmonconfig.xml`) from a trusted source like the Olaf Hartong Sysmon configuration project on GitHub (<a href="https://github.com/olafhartong/sysmon-modular">https://github.com/olafhartong/sysmon-modular</a>). <strong>Note:</strong> Exercise caution when using third-party configurations. Ensure you understand the contents of the configuration file before deploying it in your environment.</li>
</ul>

<h3>Steps</h3>

<ol>
    <li><strong>Extract the Sysmon Archive:</strong> Right-click the downloaded `sysmon.zip` file and select "Extract All...". Choose a convenient location for the extracted files (e.g., C:\Sysmon).</li>
    <li><strong>Obtain and Review the Sysmon Configuration:</strong>
        <ul>
            <li>Navigate to the Sysmon configuration file download page (e.g., GitHub repository linked above).</li>
            <li>Carefully review the configuration file (e.g., `sysmonconfig.xml`) to understand the enabled monitoring options. This file defines what system activities Sysmon will capture.</li>
            <li><strong>Optional:</strong> You can customize the configuration file to tailor Sysmon's behavior to your specific needs. This might involve enabling or disabling certain monitoring options.</li>
        </ul>
    </li>
    <li><strong>Copy the Configuration File:</strong> Copy the Sysmon configuration file (e.g., `sysmonconfig.xml`) to the extracted Sysmon folder (e.g., C:\Sysmon).</li>
    <li><strong>Open an Elevated PowerShell Window:</strong> Right-click the Windows Start menu button, select "Windows PowerShell (Admin)", or search for "PowerShell" and right-click "Run as administrator".</li>
    <li><strong>Navigate to the Sysmon Directory:</strong> Use the `cd` command to change the directory to the folder containing the extracted Sysmon files. For example, if you extracted the files to `C:\Sysmon`, run:

  <pre><code>cd C:\Sysmon</code></pre>
   </li>
    <li><strong>Install Sysmon with Configuration:</strong> Run the following command to install Sysmon as a Windows service and apply the configuration file:
    <pre><code>.\sysmon64.exe -i sysmonconfig.xml</code></pre>
        <p>Replace <code>sysmonconfig.xml</code> with the actual filename of your configuration file if it differs.</p>
        <p>This command accepts the End-User License Agreement (EULA) for Sysmon.</p>
        <p>**Note:** If you are using a 32-bit system, replace `sysmon64.exe` with `sysmon.exe` in the command.</p>
    </li>
    <li><strong>Verify Installation (Optional):</strong> To confirm successful installation, you can:
        <ul>
            <li>Open the Windows Services window (search for "Services").</li>
            <li>Locate the service named "Sysmon64".</li>
            <li>Right-click the service and select "Properties". Verify that the service is running and the startup type is set to "Automatic".</li>
        </ul>
    </li>
</ol>

<h3>Additional Notes</h3>

<p>Sysmon logs events to the Windows Event Viewer under "Applications and Services Logs/Microsoft/Windows/Sysmon/Operational". You can use Event Viewer to inspect these logs and monitor system activity.</p>
<p>Consider scheduling regular reviews of the Sysmon configuration to ensure it aligns with your security requirements.</p>

<h2>Setting up the Wazuh Manager on a DigitalOcean Droplet (Free Trial)</h2>

<p>For this lab, I'm utilizing the DigitalOcean free trial to host the Wazuh Manager. While local VMs are a cost-effective long-term solution, using a cloud environment for initial exploration can be helpful. This section details the steps to create a Droplet (virtual server) on DigitalOcean.</p>

<h3>Prerequisites</h3>

<ul>
    <li>A DigitalOcean account (you will need to provide payment information for the free trial).</li>
</ul>

<h3>Steps</h3>

<ol>
    <li><strong>Sign Up for a DigitalOcean Account:</strong> Navigate to the DigitalOcean website and sign up for a new account using one of the available methods.</li>
    <li><strong>Provide Payment Information:</strong> After signing up, you will be redirected to a page prompting you to enter your credit card or other payment details. This is required even for the free trial.</li>
    <li><strong>Access the Control Panel:</strong> Once your account is set up, click "Explore our control panel" (or a similar button/link) usually located in the top left or center of the page.</li>
    <li><strong>Create a New Droplet:</strong> In the DigitalOcean control panel, click the "Create" button in the top right corner and select "Droplets".</li>
    <li><strong>Choose a Region:</strong> Select the region that is geographically closest to you. This can improve network latency.</li>
    <li><strong>Select an Operating System:</strong> Choose "Ubuntu" as the operating system for your Droplet.</li>
    <li><strong>Select a Plan:</strong> Choose the "Basic" plan.</li>
    <li><strong>Select CPU Options:</strong> Choose "Premium Intel" for better performance.</li>
    <li><strong>Select Droplet Size (Memory):</strong> Select a Droplet size with at least 8 GB of RAM. Wazuh can be resource-intensive, and 8GB is a good starting point for a lab environment.</li>
    <li><strong>Choose an Authentication Method:</strong> You have two options for connecting to your Droplet:
        <ul>
            <li><strong>SSH Key Pair:</strong> This is the more secure method. If you are familiar with SSH keys, it is highly recommended you use this option.</li>
            <li><strong>Password:</strong> For simplicity in this lab setup, I chose to connect with a password. If you choose this option:
                <ul>
                    <li>Create a strong, unique password.</li>
                    <li>**Crucially:** Store this password in a secure location (e.g., a password manager). Losing this password will make it very difficult to access your Droplet.</li>
                </ul>
            </li>
        </ul>
    </li>
    <li><strong>Choose a Hostname:</strong> Give your Droplet a descriptive hostname. I used "Wazuh" in this case.</li>
    <li><strong>Create the Droplet:</strong> Click the "Create Droplet" button to initiate the Droplet creation process. This may take a few minutes.</li>
</ol>

<p>Once the Droplet is created, DigitalOcean will display its IP address. You will need this IP address to connect to your Wazuh Manager.</p>


<h2>Configuring the DigitalOcean Firewall</h2>

<p>To secure the Wazuh Manager Droplet and allow only necessary traffic, I configured a firewall in the DigitalOcean control panel. This ensures that only authorized connections from my public IP address can reach the Droplet.</p>

<h3>Steps</h3>

<ol>
    <li><strong>Access the Firewall Section:</strong> From the left-hand menu in the DigitalOcean control panel, I selected "Manage" -> "Networking" -> "Firewalls".</li>
    <li><strong>Create a New Firewall:</strong> Clicked the "Create Firewall" button.</li>
    <li><strong>Name the Firewall:</strong> Provided a descriptive name for the firewall (e.g., "Wazuh Firewall").</li>
    <li><strong>Configure Inbound Rules (TCP):</strong> Configured the inbound rules for TCP traffic:
        <ol type="a">
            <li>Changed the first "Type" option to "All TCP".</li>
            <li>In the "Sources" section, deleted any existing entries.</li>
            <li>Added my public IP address as a source. To find my public IP, I used a service like <a href="https://www.whatismyipaddress.com/">whatismyipaddress.com</a>.</li>
        </ol>
    </li>
    <li><strong>Configure Inbound Rules (UDP):</strong> Repeated the same process for UDP traffic:
        <ol type="a" start="1">
            <li>Changed the "Type" option to "All UDP".</li>
            <li>In the "Sources" section, deleted any existing entries.</li>
            <li>Added my public IP address as a source.</li>
        </ol>
    </li>
    <li><strong>Create the Firewall:</strong> Once both TCP and UDP rules were configured with my public IP as the source, I scrolled to the bottom of the page and clicked "Create Firewall".</li>
    <li><strong>Apply the Firewall to the Droplet:</strong> To associate the newly created firewall with the Wazuh Droplet:
        <ol type="a">
            <li>From the left-hand menu, selected "Droplets".</li>
            <li>Selected the "Wazuh" Droplet.</li>
            <li>Selected the "Networking" tab.</li>
            <li>Scrolled down to the "Firewalls" section and clicked "Edit".</li>
            <li>Selected the newly created firewall (e.g., "Wazuh Firewall").</li>
            <li>In the "Droplets" section of the Firewall configuration, clicked "Add Droplets".</li>
            <li>Started typing the name of the Droplet ("Wazuh") and selected it from the suggestions.</li>
            <li>Clicked "Add droplet".</li>
        </ol>
    </li>
</ol>

<p>By configuring the firewall in this way, I ensured that only traffic originating from my specified public IP address can reach the Wazuh Droplet on all TCP and UDP ports. This significantly enhances the security of the server.</p>



<h2>Connecting to and Installing Wazuh on the Droplet</h2>

<p>Now that the Droplet is set up and the firewall is configured, we can connect to it and install the Wazuh server software.</p>

<h3>Connecting to the Droplet</h3>

<p>**Method 1: Using the Droplet Console (Recommended)**</p>
<ol>
    <li><strong>Access the Droplet:</strong> In the DigitalOcean control panel, navigate to "Droplets" and select the "Wazuh" Droplet.</li>
    <li><strong>Launch the Console:</strong> Click on the "Access" tab and then "Launch Droplet Console". This will open a terminal window within your browser where you can interact with the Droplet directly.</li>
</ol>

<p>**Method 2: Using an SSH Client (Alternative)**</p>
<ol>
    <li><strong>Download an SSH Client:</strong> If the Droplet Console doesn't work, download an SSH client like PuTTY for your local machine.</li>
    <li><strong>Connect with SSH:</strong> Use the provided IP address (from DigitalOcean) and port 22 (default SSH port) in your SSH client to connect to the Droplet. You will be prompted for the password you created during the Droplet setup.</li>
</ol>

<h3>Updating the System</h3>

<p>Once connected to the Droplet, either through the console or SSH, run the following command to update the system's package lists and install any available updates:</p>

<pre><code>apt-get update && apt-get upgrade -y</code></pre>
<p><strong>Explanation:</strong></p>
<p><code>apt-get update</code>: Updates the list of available packages from the repositories.</p>
<p><code>apt-get upgrade -y</code>: Upgrades all installed packages to the latest versions. The `-y` flag automatically accepts any prompts during the upgrade process.</p>

<p><strong>Important:</strong> When prompted to confirm which services to restart after the update, you can simply press Enter to accept the defaults.</p>

<h3>Installing Wazuh</h3>

<p>After the system is updated, run the following command to download and execute the Wazuh installation script:</p>

<pre><code>curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh && sudo bash ./wazuh-install.sh -a</code></pre>
<p><strong>Explanation:</strong></p>
<p><code>curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh</code>: Downloads the Wazuh installation script from the official repository. The `-s` flag silences output during the download.</p>
<p><code>sudo bash ./wazuh-install.sh -a</code>: Executes the downloaded script with root privileges (`sudo`) and the `-a` flag which selects the "all" installation option (including the Wazuh manager, worker, and API).</p>

<p><strong>Crucially:</strong> During the installation, the script will **generate** a password for the Wazuh admin user. **Make absolutely sure to copy and save this generated password in a secure location (e.g., a password manager). You will need this password to access the Wazuh web interface.**</p>

<h3>Accessing the Wazuh Web Interface</h3>

<p>Once the installation completes, you can access the Wazuh web interface in a web browser. Open a new browser window and navigate to the following URL, replacing <code>&lt;your_public_ip_address&gt;</code> with the actual public IP address of your Wazuh Droplet:</p>

<pre><code>https://&lt;your_public_ip_address&gt;</code></pre>

<p><strong>Login:</strong> Use the username "wazuh_admin" and the **generated** password (that you saved during the installation) to log in to the Wazuh web interface.</p>

<p>This will take you to the Wazuh dashboard where you can begin managing your security information and event management (SIEM) system.</p>


