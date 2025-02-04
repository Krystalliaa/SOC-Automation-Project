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

<h2>Setting up TheHive on a DigitalOcean Droplet</h2>

<p>Following the setup of the Wazuh Manager, we will now create a separate Droplet to host TheHive, our case management platform. We will use a similar procedure as with the Wazuh Droplet and configure the firewall to allow access.</p>


<h3>Steps to Create TheHive Droplet</h3>

<ol>
    <li><strong>Access the Control Panel:</strong> Log in to the DigitalOcean control panel.</li>
    <li><strong>Create a New Droplet:</strong> Click the "Create" button in the top right corner and select "Droplets".</li>
    <li><strong>Choose a Region:</strong> Select the same region you used for the Wazuh Droplet for better performance and lower latency.</li>
    <li><strong>Select an Operating System:</strong> Choose "Ubuntu" as the operating system.</li>
    <li><strong>Select a Plan:</strong> Choose the "Basic" plan.</li>
    <li><strong>Select CPU Options:</strong> Choose "Premium Intel".</li>
    <li><strong>Select Droplet Size (Memory):</strong> Select a Droplet size with at least 8 GB of RAM.</li>
    <li><strong>Choose an Authentication Method:</strong> Choose the same authentication method (Password or SSH Key) you used for the Wazuh Droplet to maintain consistency. If using a password, ensure you create a strong password and save it securely.</li>
    <li><strong>Choose a Hostname:</strong> Give your Droplet a descriptive hostname, such as "TheHive".</li>
    <li><strong>Create the Droplet:</strong> Click the "Create Droplet" button.</li>
</ol>

<h3>Adding TheHive Droplet to the Firewall</h3>

<p>Now, we will add the newly created TheHive Droplet to the existing firewall to allow access:</p>

<ol>
    <li><strong>Access Firewalls:</strong> In the left menu, select "Networking" and then "Firewalls".</li>
    <li><strong>Select Your Firewall:</strong> Click on the name of the firewall you created for the Wazuh Droplet (e.g., "Wazuh Firewall").</li>
    <li><strong>Add TheHive Droplet:</strong>
        <ol type="a">
            <li>In the firewall configuration page, locate the "Droplets" section.</li>
            <li>Click "Add Droplets".</li>
            <li>Start typing the hostname of your TheHive Droplet ("TheHive") and select it from the suggestions.</li>
            <li>Click "Add droplet".</li>
        </ol>
    </li>
</ol>

<p>By adding TheHive to the existing firewall, we ensure that the same security rules apply to both the Wazuh and TheHive Droplets, simplifying management and maintaining a consistent security posture.</p>

<h2>Installing TheHive on Your Droplet</h2>

<p>This section details the installation of TheHive 5 and its necessary dependencies (Java, Cassandra, and Elasticsearch) on your DigitalOcean Droplet after you have connected via SSH.</p>

<h3>1. Updating System Packages</h3>

<p>Before installing any new software, it's crucial to update the system's package lists and upgrade existing packages to their latest versions. You can run the following command:</p>

<pre><code>apt-get update && apt-get upgrade -y</code></pre>

<p><strong>Explanation:</strong></p>
<p><code>apt-get update</code>: Retrieves the latest package information from the configured repositories.</p>
<p><code>apt-get upgrade -y</code>: Upgrades all upgradable packages on the system. The `-y` flag automatically answers "yes" to any prompts, automating the process.</p>
<p><strong>Note:</strong> The <code>&&</code> allows you to run multiple commands sequentially. The second command will only execute if the first command is successful.</p>

<h3>2. Installing Dependencies</h3>

<p>TheHive relies on several dependencies. Install them using the following command:</p>

<pre><code>apt install wget gnupg apt-transport-https git ca-certificates ca-certificates-java curl software-properties-common python3-pip lsb-release</code></pre>

<h3>3. Installing Java</h3>

<p>TheHive requires Java. We will install Amazon Corretto 11. You can run the following commands one by one, or you can copy and paste the entire block to execute them sequentially:</p>

<pre><code># Import the Amazon Corretto Java 11 signing key
wget -qO- https://apt.corretto.aws/corretto.key | sudo gpg --dearmor -o /usr/share/keyrings/corretto.gpg

# Add the Amazon Corretto Java 11 repository
echo "deb [signed-by=/usr/share/keyrings/corretto.gpg] https://apt.corretto.aws stable main" | sudo tee -a /etc/apt/sources.list.d/corretto.sources.list

# Update package lists
sudo apt update

# Install Java
sudo apt install java-common java-11-amazon-corretto-jdk

# Set JAVA_HOME environment variable
echo 'JAVA_HOME="/usr/lib/jvm/java-11-amazon-corretto"' | sudo tee -a /etc/environment

# Export the JAVA_HOME environment variable (optional but recommended)
export JAVA_HOME="/usr/lib/jvm/java-11-amazon-corretto"
</code></pre>

<p><strong>Explanation:</strong> These commands add the Amazon Corretto repository, install the JDK, and set the <code>JAVA_HOME</code> environment variable.</p>
<p><strong>Note:</strong> You can run all these commands at once by copying and pasting the entire block into your terminal.</p>

<h3>4. Installing Cassandra</h3>

<p>Cassandra is used as the database for TheHive. Install it using these commands. You can copy and paste the entire block to run them sequentially:</p>

<pre><code># Import the Apache Cassandra signing key
wget -qO- https://downloads.apache.org/cassandra/KEYS | sudo gpg --dearmor -o /usr/share/keyrings/cassandra-archive.gpg

# Add the Apache Cassandra repository
echo "deb [signed-by=/usr/share/keyrings/cassandra-archive.gpg] https://debian.cassandra.apache.org 40x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list

# Update package lists
sudo apt update

# Install Cassandra
sudo apt install cassandra
</code></pre>

<p><strong>Explanation:</strong> These commands add the Cassandra repository and install the Cassandra database.</p>
<p><strong>Note:</strong> You can run all these commands at once by copying and pasting the entire block into your terminal.</p>

<h3>5. Installing Elasticsearch</h3>

<p>Elasticsearch is used for indexing and searching data within TheHive. Install it with the following commands. You can copy and paste the entire block to run them sequentially:</p>

<pre><code># Import the ElasticSearch signing key
wget -qO- https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg

# Install apt-transport-https (if not already installed)
sudo apt-get install apt-transport-https

# Add the ElasticSearch repository
echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-7.x.list

# Update package lists
sudo apt update

# Install Elasticsearch
sudo apt install elasticsearch

# **Optional Elasticsearch Configuration**

# Create a jvm.options file (optional) and add the following lines to configure memory allocation:

# -Dlog4j2.formatMsgNoLookups=true
# -Xms2g
# -Xmx2g

# (Replace the memory values with your desired allocation)
</code></pre>

<p><strong>Explanation:</strong> These commands add the Elastic repository, install Elasticsearch, and provide optional settings for the JVM.</p>
<p><strong>Note:</strong> You can run all these commands at once by copying and pasting the entire block into your terminal.</p>

<h3>6. Installing TheHive</h3>

<p>Finally, install TheHive itself. You can copy and paste the entire block to run them sequentially:</p>

<pre><code># Import the Strangebee signing key
wget -O- https://archives.strangebee.com/keys/strangebee.gpg | sudo gpg --dearmor -o /usr/share/keyrings/strangebee-archive-keyring.gpg

# Add the Strangebee repository
echo 'deb [signed-by=/usr/share/keyrings/strangebee-archive-keyring.gpg] https://deb.strangebee.com thehive-5.2 main' | sudo tee -a /etc/apt/sources.list.d/strangebee.list

# Update package lists
sudo apt-get update

# Install TheHive
sudo apt-get install -y thehive
</code></pre>

<p><strong>Explanation:</strong> These commands add the Strangebee repository and install TheHive.</p>
<p><strong>Note:</strong> You can run all these commands at once by copying and pasting the entire block into your terminal.</p>


<h2>Configuring TheHive: Cassandra and Elasticsearch</h2>

<p>This section details the configuration of Cassandra and Elasticsearch, the database and search engine used by TheHive, on your DigitalOcean Droplet.</p>

<h3>1. Configuring Cassandra</h3>

<p>Cassandra serves as the database for TheHive. We need to modify its configuration file to ensure it listens on the correct IP address.</p>

<ol>
    <li><strong>Edit the Cassandra Configuration File:</strong> Open the Cassandra configuration file using `nano`:
        <pre><code>nano /etc/cassandra/cassandra.yaml</code></pre>
    </li>
    <li><strong>Modify Listen Addresses:</strong>
        <ol type="a">
            <li>Press <code>Ctrl + W</code> to open the search function in `nano`.</li>
            <li>Search for <code>listen_address</code> and replace the existing value with the public IP address of your TheHive Droplet.</li>
            <li>Repeat the search and replace for <code>rpc_address</code>, also using your TheHive Droplet's public IP.</li>
        </ol>
    </li>
    <li><strong>Update Seed Nodes:</strong>
        <ol type="a">
            <li>Search for <code>seeds</code>.</li>
            <li>Replace the existing seed addresses with your TheHive Droplet's public IP address followed by <code>:7000</code>.</li>
            <li><strong>Important:</strong> Ensure you leave the <code>:7000</code> port specification unchanged.</li>
        </ol>
    </li>
    <li><strong>Save and Exit `nano`:</strong>
        <ol type="a">
            <li>Press <code>Ctrl + X</code> to exit.</li>
            <li>Type <code>y</code> and press <code>Enter</code> to confirm saving the changes.</li>
        </ol>
    </li>
    <li><strong>Stop and Restart Cassandra:</strong>
        <ol type="a">
            <li>Stop the Cassandra service:
                <pre><code>systemctl stop cassandra.service</code></pre>
            </li>
            <li><strong>Important:</strong> Because we installed TheHive using their package, we need to remove old data files:
                <pre><code>rm -rf /var/lib/cassandra/*</code></pre>
            </li>
            <li>Start the Cassandra service again:
                <pre><code>systemctl start cassandra.service</code></pre>
            </li>
        </ol>
    </li>
        <li><strong>Verify Cassandra Status:</strong>
        <ol type="a">
            <li>Check the Cassandra service status:
                <pre><code>systemctl status cassandra.service</code></pre>
            </li>
            <li>Exit the status viewer by pressing <code>q</code>.</li>
        </ol>
    </li>
</ol>

<h3>2. Configuring Elasticsearch</h3>

<p>Elasticsearch is used for indexing and searching data within TheHive. We need to configure it to work correctly with our setup.</p>

<ol>
    <li><strong>Edit the Elasticsearch Configuration File:</strong> Open the Elasticsearch configuration file with `nano`:
    <pre><code>nano /etc/elasticsearch/elasticsearch.yml</code></pre>
    </li>
    <li><strong>Set Cluster Name:</strong>
        <ol type="a">
            <li>Uncomment the <code>cluster.name</code> line (remove the <code>#</code>).</li>
            <li>Change its value to <code>"thehive"</code>.</li>
        </ol>
    </li>
    <li><strong>Define Node Name:</strong>
        <ol type="a">
            <li>Uncomment the <code>node.name</code> line.</li>
            <li>Leave its value as <code>node-1</code> (assuming this is your only Elasticsearch node).</li>
        </ol>
    </li>
    <li><strong>Configure Network Settings:</strong>
        <ol type="a">
            <li>Uncomment the <code>network.host</code> line.</li>
            <li>Change its value to the public IP address of your TheHive Droplet.</li>
            <li>Uncomment the <code>http.port</code> line and leave it set to <code>9200</code>.</li>
        </ol>
    </li>
    <li><strong>Set Cluster Discovery:</strong>
        <ol type="a">
            <li>Uncomment the <code>cluster.initial_master_nodes</code> line.</li>
            <li>Remove <code>node-2</code> from within the brackets (if present, as we only have one node).</li>
        </ol>
    </li>
    <li><strong>Save and Exit `nano`:</strong>
        <ol type="a">
            <li>Press <code>Ctrl + X</code> to exit.</li>
            <li>Type <code>y</code> and press <code>Enter</code> to confirm saving the changes.</li>
        </ol>
    </li>
    <li><strong>Start and Enable Elasticsearch:</strong>
        <ol type="a">
            <li>Start the Elasticsearch service:
                <pre><code>systemctl start elasticsearch.service</code></pre>
            </li>
            <li>Enable the Elasticsearch service to start on boot:
                <pre><code>systemctl enable elasticsearch.service</code></pre>
            </li>
        </ol>
    </li>
        <li><strong>Verify Elasticsearch Status:</strong>
        <ol type="a">
            <li>Check the Elasticsearch service status:
                <pre><code>systemctl status elasticsearch.service</code></pre>
            </li>
            <li>Exit the status viewer by pressing <code>q</code>.</li>
        </ol>
    </li>
</ol>



<h2>Configuring TheHive</h2>

<p>This section guides you through configuring TheHive, ensuring it uses the correct IP address and is accessible from your browser.</p>

<h3>1. Granting Ownership to TheHive User</h3>

<p>Before modifying TheHive's configuration file, ensure the <code>thehive</code> user and group have ownership of the TheHive directory.</p>

<ol>
    <li><strong>Verify Current Permissions:</strong>
        <p>Use <code>ls -la /opt/thp</code> to check the access permissions for the <code>/opt/thp</code> directory (TheHive directory). The output <img src="./images/permissions.png" alt="Permissions"> should show <code>root</code> as the owner. We need to change this.</p>
    </li>
    <li><strong>Change Ownership:</strong>
        <p>Run the following command to grant ownership of <code>/opt/thp</code> and its contents to the <code>thehive</code> user and group:</p>
        <pre><code>chown -R thehive:thehive /opt/thp</code></pre>
        <p>The <code>-R</code> flag ensures ownership changes are applied recursively to all files and subdirectories within <code>/opt/thp</code>.</p>
    </li>
    <li><strong>Verify Ownership Change:</strong>
        <p>Run <code>ls -la /opt/thp</code> again. The output should now show <code>thehive</code> as the owner.</p>
    </li>
</ol>

<h3>2. Modifying TheHive Configuration File</h3>

<p>TheHive's main configuration is stored in <code>application.conf</code>. We need to adjust some settings to match our setup.</p>

<ol>
    <li><strong>Edit the Configuration File:</strong>
        <p>Open the TheHive configuration file with <code>nano</code>:</p>
        <pre><code>nano /etc/thehive/application.conf</code></pre>
    </li>
    <li><strong>Update Hostnames:</strong>
        <ol type="a">
            <li>Scroll down and locate settings for <code>storage.hostname</code> and <code>index.search.hostname</code>.</li>
            <li>Change these values to your TheHive Droplet's public IP address.</li>
        </ol>
    </li>
    <li><strong>Update Base URL:</strong>
        <ol type="a">
            <li>Scroll down and locate <code>application.baseUrl</code>.</li>
            <li>Delete the existing IP address and insert the public IP of your TheHive Droplet, keeping the port <code>http://&lt;your_public_ip&gt;:9000</code> intact.</li>
        </ol>
    </li>
    <li><strong>Save and Exit `nano`:</strong>
        <ol type="a">
            <li>Press <code>Ctrl + X</code> to exit.</li>
            <li>Type <code>y</code> and press <code>Enter</code> to confirm saving the changes.</li>
        </ol>
    </li>
</ol>

<h3>3. Starting and Enabling TheHive Service</h3>

<p>Now that the configuration is updated, we can start TheHive service.</p>

<ol>
    <li><strong>Start TheHive:</strong>
        <p>Use <code>systemctl start thehive</code> to start the TheHive service.</p>
        <pre><code>systemctl start thehive</code></pre>
    </li>
    <li><strong>Enable TheHive Service:</strong>
        <p>Use <code>systemctl enable thehive</code> to ensure TheHive starts automatically on system boot.</p>
        <pre><code>systemctl enable thehive</code></pre>
    </li>
    <li><strong>Verify Service Status:</strong>
        <p>Check the TheHive service status with <code>systemctl status thehive</code>.</p>
        <pre><code>systemctl status thehive</code></pre>
    </li>
</ol>

<h3>4. Accessing TheHive</h3>

<p>You should now be able to access TheHive by opening your web browser and navigating to your TheHive Droplet's public IP address with port 9000 (e.g., <code>http://&lt;your_public_ip&gt;:9000</code>).</p>

<p><strong>Login:</strong> Use the following credentials to log in:</p>
<ul>
    <li><strong>Username:</strong> <code>admin@thehive.local</code></li>
    <li><strong>Password:</strong> <code>secret</code></li>
</ul>

<p><strong>Remember</strong> to replace <code>&lt;your_public_ip&gt;</code> with the actual public IP address of your TheHive Droplet.</p>



<h2>Configuring Wazuh and Installing the Wazuh Agent</h2>

<p>This section details accessing the Wazuh dashboard, retrieving the admin credentials (if needed), and installing the Wazuh agent on the Windows 10 client.</p>

<h3>1. Accessing the Wazuh Dashboard</h3>

<p>Open a web browser on your Windows 10 virtual machine and navigate to the public IP address of your Wazuh Droplet:</p>

<pre><code>http://&lt;your_wazuh_public_ip&gt;</code></pre>

<p>You will be prompted to log in.</p>

<h3>2. Retrieving Wazuh Credentials (If Needed)</h3>

<p>If you did not save the Wazuh admin password during the installation process, you can retrieve it by connecting to your Wazuh Droplet via SSH and doing the following:</p>

<ol>
    <li>List the files in the current directory:
    <pre><code>ls</code></pre>
        <p>This will show you if the <code>wazuh-install-files.tar</code> is present.</p>
    </li>
    <li>Extract the files from the tar archive:
        <pre><code>tar -xvf wazuh-install-files.tar</code></pre>
    </li>
    <li>Navigate into the extracted directory:
        <pre><code>cd wazuh-install-files/</code></pre>
    </li>
    <li>List the files in the directory:
        <pre><code>ls</code></pre>
    </li>
    <li>Display the contents of the password file:
        <pre><code>cat wazuh-passwords.txt</code></pre>
        <p>This file contains the Wazuh admin password and the Wazuh API user credentials. Copy and save these credentials in a safe place.</p>
    </li>
</ol>

<h3>3. Installing the Wazuh Agent on Windows</h3>

<p>Once you've accessed the Wazuh dashboard, you can install the agent on your Windows 10 client.</p>

<ol>
    <li><strong>Navigate to "Add Agent":</strong> In the Wazuh dashboard, click on the "Add agent" button (the location may vary slightly depending on the Wazuh version).</li>
    <li><strong>Select Agent Type:</strong> Choose "Windows" as the agent type.</li>
    <li><strong>Configure Agent Settings:</strong>
        <ol type="a">
            <li>For the "Server address", enter the public IP address of your Wazuh Droplet.</li>
            <li>You can assign a custom name to the agent (optional).</li>
            <li>Leave the "Select one or more existing groups" as default.</li>
        </ol>
    </li>
    <li><strong>Copy the Installation Command:</strong> The Wazuh dashboard will generate a command for installing the agent. Copy this command.</li>
    <li><strong>Open an Elevated PowerShell Window:</strong> On your Windows 10 VM, open PowerShell as an administrator (right-click PowerShell and select "Run as administrator").</li>
    <li><strong>Paste and Execute the Command:</strong> Paste the copied command into the PowerShell window and press Enter to execute it.</li>
    <li><strong>Start the Wazuh Service:</strong> After the installation completes, start the Wazuh service:
        <pre><code>net start Wazuhsvc</code></pre>
    </li>
    <li><strong>Verify Agent Connection:</strong> Go back to the Wazuh dashboard and wait a minute. The newly installed agent should appear as "Active".</li>
</ol>


<h2>Configuring Wazuh to Ingest Sysmon Logs on Windows 10</h2>

<p>This section details how to configure Wazuh on your Windows 10 client to ingest logs from Sysmon, enabling you to monitor for suspicious activities like mimikatz usage.</p>

<h3>1. Backing Up the Configuration File</h3>

<p>Before making any changes, it's crucial to back up the original Wazuh agent configuration file.</p>

<ol>
    <li><strong>Locate the Configuration File:</strong>
        <p>Navigate to the Wazuh agent configuration directory:</p>
        <pre><code>C:\Program Files (x86)\ossec-agent</code></pre>
        <p>Locate the <code>ossec.conf</code> file.</p>
    </li>
    <li><strong>Create a Backup:</strong>
        <p>Right-click the <code>ossec.conf</code> file and select "Copy."</p>
        <p>Paste the copied file into the same directory and rename it to <code>ossec-backup.conf</code>.</p>
    </li>
</ol>

<h3>2. Opening the Configuration File (as Administrator)</h3>

<p>Open the <code>ossec.conf</code> file with administrative privileges.</p>

<ol>
    <li><strong>Open with Notepad (Admin):</strong>
        <p>Right-click the <code>ossec.conf</code> file and select "Open with > Notepad."</p>
        <p>If prompted for administrator privileges, click "Yes."</p>
    </li>
</ol>

<h3>3. Understanding Log Analysis Configuration</h3>

<p>The "Log Analysis" section in the <code>ossec.conf</code> file defines which logs the Wazuh agent monitors.</p>

<h3>4. Adding Sysmon Log Ingestion</h3>

<p>We will now add a new <code>&lt;localfile&gt;</code> block to ingest Sysmon logs.</p>

<ol>
    <li><strong>Copy an Existing &lt;localfile&gt; Block:</strong>
        <p>Locate an existing <code>&lt;localfile&gt;</code> block within the "Log Analysis" section and copy the entire block.
        <img src="./images/log-analysis.png" alt="Log Analysis">
        </p>
    </li>
    <li><strong>Paste the Block:</strong>
        <p>Paste the copied block below the original.</p>
    </li>
    <li><strong>Configure Sysmon Log Source:</strong>
        <ol type="a">
            <li><strong>Change the &lt;location&gt; Tag:</strong>
                <p>Change the value within the <code>&lt;location&gt;</code> tags to the Sysmon channel name.</p>
            </li>
            <li><strong>Obtain the Sysmon Channel Name:</strong>
                <ol type="i">
                    <li>Open the Event Viewer.</li>
                    <li>Expand "Applications and Services Logs" -> "Microsoft-Windows" -> "Sysmon."</li>
                    <li>Right-click "Operational" and select "Properties."</li>
                    <img src="./images/sysmon-full-name.png" alt="Copy the value under "Channel Name">
                    <li>Copy the value displayed under "Channel Name" (e.g., "Microsoft-Windows-Sysmon/Operational").</li>
                </ol>
            </li>
            <li><strong>Paste the Channel Name:</strong>
                <p>Paste the copied channel name between the <code>&lt;location&gt;</code> tags in your <code>ossec.conf</code> file.</p>
            </li>
        </ol>
    </li>
    <li><strong>Disabling Unnecessary Log Sources (Optional):</strong>
        <p>To prevent Wazuh from forwarding other logs (e.g., application, security, system), you can comment out the corresponding <code>&lt;localfile&gt;</code> blocks by adding <code>#</code> at the beginning of each line within the block.</p>
    </li>
</ol>

<h3>5. Saving the Configuration File</h3>

<ol>
    <li><strong>Save the File:</strong>
        <p>Try saving the <code>ossec.conf</code> file in Notepad.</p>
        <p>If you encounter permission issues:</p>
        <ol type="a">
            <li>Minimize Notepad.</li>
            <li>Right-click the <code>ossec.conf</code> file again.</li>
            <li>Select "Open with > Notepad (Admin)" to ensure you have administrator privileges.</li>
            <li>Repeat the configuration changes and save the file.</li>
        </ol>
    </li>
</ol>

<h3>6. Restarting the Wazuh Agent Service</h3>

<ol>
    <li><strong>Restart the Service:</strong>
        <p>Open the Services window (search for "Services").</p>
        <p>Locate the "Wazuh Agent" service.</p>
        <p>Right-click on the service and select "Restart."</p>
    </li>
</ol>

<h3>7. Verifying Sysmon Log Ingestion</h3>

<ol>
    <li><strong>Check the Wazuh Dashboard:</strong>
        <p>Go to the Wazuh dashboard and navigate to the "Events" section.</p>
        <p>Ensure you're viewing the correct index (e.g., "wazuh-alerts*").</p>
        <p>Search for "Sysmon" in the search bar. It might take some time for Sysmon events to appear.</p>
    </li>
</ol>

<h3>8. Downloading Mimikatz (For Testing Purposes Only)</h3>

<p><strong>Disclaimer:</strong> Downloading and running mimikatz on a production system is highly discouraged. This step is for demonstration purposes only to test your Sysmon configuration.</p>

<ol>
    <li><strong>Disable Windows Defender or Exclude Downloads Folder:</strong>
        <p>Temporarily disable Windows Defender or create an exclusion for your downloads folder.</p>
    </li>
</ol>

<h2>Downloading and Running Mimikatz, Configuring Wazuh to Detect It</h2>

<p>This section explains how to download and run mimikatz for testing purposes and configure Wazuh to detect its execution.</p>

<h3>1. Downloading and Extracting Mimikatz</h3>

<ol>
    <li><strong>Search for Mimikatz:</strong>
        <p>Search online for "mimikatz gentilkiwi."</p>
    </li>
    <li><strong>Find Releases:</strong>
        <p>On the appropriate website (e.g., GitHub), locate the "Releases" section.</p>
    </li>
    <li><strong>Download Mimikatz:</strong>
        <p>Download the "mimikatz_trunk" zip file for version 2.2.0 20220919.</p>
    </li>
    <li><strong>Extract the Files:</strong>
        <p>Extract the downloaded zip file to your Downloads folder (which should already be excluded from Windows Defender scans).</p>
    </li>
</ol>

<h3>2. Running Mimikatz</h3>

<ol>
    <li><strong>Open PowerShell as Administrator:</strong>
        <p>Open PowerShell with administrative privileges.</p>
    </li>
    <li><strong>Navigate to the Mimikatz Directory:</strong>
        <p>Use the <code>cd</code> command to navigate to the x64 directory within the extracted mimikatz folder:</p>
        <pre><code>cd C:\Users\Win-10-Client\Downloads\mimikatz_trunk\x64</code></pre>
    </li>
    <li><strong>Execute Mimikatz:</strong>
        <p>Run the mimikatz executable:</p>
        <pre><code>.\mimikatz.exe</code></pre>
    </li>
</ol>

<h3>3. Checking for Events in the Wazuh Dashboard</h3>

<p>After running mimikatz, check the Wazuh dashboard for any triggered events.</p>

<ol>
    <li><strong>Search for Mimikatz Events:</strong>
        <p>In the Wazuh dashboard, search for "mimikatz."</p>
        <p>If no events are present, it might be because no default Wazuh rules trigger on the specific Sysmon events generated by mimikatz. The next steps will address this.</p>
    </li>
</ol>

<h3>4. Configuring Wazuh to Log All Events (for Testing)</h3>

<p>To ensure all events are logged (for testing and rule development), you can modify the Wazuh manager's configuration.</p>

<ol>
    <li><strong>Connect to the Wazuh Manager via SSH:</strong>
        <p>Connect to your Wazuh server via SSH as root.</p>
    </li>
    <li><strong>Backup the Configuration File:</strong>
        <p>Create a backup of the <code>ossec.conf</code> file:</p>
        <pre><code>cp /var/ossec/etc/ossec.conf ~/ossec-backup.conf</code></pre>
    </li>
    <li><strong>Edit the Configuration File:</strong>
        <p>Open the <code>ossec.conf</code> file with <code>nano</code>:</p>
        <pre><code>nano /var/ossec/etc/ossec.conf</code></pre>
    </li>
    <li><strong>Enable Log All:</strong>
        <p>Change the <code>&lt;logall&gt;</code> and <code>&lt;logall_json&gt;</code> settings to <code>yes</code>:</p>
        <img src="./images/nanoossec.png" alt="Nano Ossec Config">
        <pre><code>&lt;logall&gt;yes&lt;/logall&gt;
&lt;logall_json&gt;yes&lt;/logall_json&gt;
</code></pre>
    </li>
    <li><strong>Save and Exit `nano`:</strong>
        <p>Save and exit the file (<code>Ctrl + X</code>, <code>y</code>, <code>Enter</code>).</p>
    </li>
    <li><strong>Restart the Wazuh Manager:</strong>
        <p>Restart the Wazuh manager service:</p>
        <pre><code>systemctl restart wazuh-manager.service</code></pre>
    </li>
</ol>

<h3>5. Configuring Filebeat to Ingest Archives</h3>

<p>To ingest the archived logs, you need to configure Filebeat.</p>

<ol>
    <li><strong>Edit the Filebeat Configuration File:</strong>
        <p>Open the Filebeat configuration file:</p>
        <pre><code>nano /etc/filebeat/filebeat.yml</code></pre>
    </li>
    <li><strong>Enable Archives:</strong>
        <p>Change the <code>archives.enabled</code> setting to <code>true</code>.</p>
    </li>
    <li><strong>Save and Exit `nano`:</strong>
        <p>Save and exit the file.</p>
    </li>
    <li><strong>Restart Filebeat:</strong>
        <p>Restart the Filebeat service:</p>
        <pre><code>systemctl restart filebeat</code></pre>
    </li>
</ol>

<h3>6. Creating an Index Pattern for Archives</h3>

<p>Create an index pattern in Kibana to search the archived logs.</p>

<ol>
    <li><strong>Navigate to Index Patterns:</strong>
        <p>In the Wazuh dashboard, go to "Stack Management" -> "Index Patterns."</p>
        <img src="./images/index-patterns.png" alt="Index Patterns">
    </li>
    <li><strong>Create Index Pattern:</strong>
        <p>Click "Create index pattern."</p>
    </li>
    <li><strong>Name the Index Pattern:</strong>
        <p>Name the index pattern <code>wazuh-archives-*</code>.</p>
    </li>
    <li><strong>Configure Time Field:</strong>
        <p>Select <code>timestamp</code> as the time field and create the index pattern.</p>
    </li>
</ol>

<h3>7. Creating a Custom Wazuh Rule to Detect Mimikatz</h3>

<p>Now, we will create a custom Wazuh rule to specifically detect mimikatz execution.</p>

<ol>
    <li><strong>Navigate to Rules Management:</strong>
        <p>In the Wazuh dashboard, go to "Management" -> "Rules."</p>
        <img src="./images/rules.png" alt="Rules">
    </li>
    <li><strong>Manage Rule Files:</strong>
        <p>Click "Manage rule files."</p>
    </li>
    <li><strong>Edit local_rules.xml:</strong>
        <p>Click the pencil icon to edit the <code>local_rules.xml</code> file.</p>
    </li>
    <li><strong>Copy and Modify a Sysmon Rule:</strong>
        <p>Copy an existing Sysmon rule (e.g., one related to Event ID 1) as a template. <img src="./images/copy-rule.png" alt="Copy Rule"></p>
        <p>Paste the copied rule into <code>local_rules.xml</code>, below the existing rule, and adjust the indentation.</p>
    </li>
    <li><strong>Modify the Rule Details:</strong>
        <p>Make the following changes to the copied rule:</p>
        <ul>
            <li>Change the <code>rule id</code> to <code>100002</code> (or another unique ID).</li>
            <li>Set the <code>level</code> to <code>15</code>.</li>
            <li>Modify the <code>&lt;field name="win.eventdata.originalFileName" type="pcre2"&gt;</code> to detect mimikatz.exe:
                <pre><code>&lt;field name="win.eventdata.originalFileName" type="pcre2"&gt;(?i)mimikatz\.exe&lt;/field&gt;</code></pre>
            </li>
            <li>Remove the <code>&lt;options&gt;no_full_log&lt;/options&gt;</code> tag.</li>
            <li>Change the <code>&lt;description&gt;</code> to <code>Mimikatz Usage Detected&lt;/description&gt;</code>.</li>
            <li>Set the MITRE ATT&CK <code>&lt;id&gt;</code> to <code>T1003</code> (Credential Dumping).</li>
        </ul>
    </li>
    <li><strong>Save the Rule:</strong>
        <p>Save the <code>local_rules.xml</code> file. This will automatically restart the Wazuh manager.</p>
    </li>
</ol>

<h3>8. Triggering and Verifying the Custom Rule</h3>

<ol>
    <li><strong>Run Mimikatz Again:</strong>
        <p>If you didn't see any events, exit the mimikatz prompt in PowerShell and run <code>.\mimikatz.exe</code> again.
            <img src="./images/mimikatz_exit.png" alt="Mimikatz Exit"></p>
    </li>
    <li><strong>Check Wazuh Security Events:</strong>
        <p>Go back to the Wazuh dashboard and navigate to "Security Events." Refresh the page. You should now see the mimikatz detection event.
            <img src="./images/Mimikatz_detected.png" alt="Mimikatz Detected"></p>
    </li>
</ol>



<h2>Integrating Wazuh with Shuffle.io for Alert Automation</h2>

<p>This guide details how to integrate Wazuh with Shuffle.io to automate workflows triggered by Wazuh security alerts.</p>

<h3>1. Create a Shuffle.io Workflow</h3>

<ol>
    <li><strong>Sign Up for Shuffle.io:</strong>
        <p>Create an account at https://shuffler.io/.</p>
    </li>
    <li><strong>Create a Workflow:</strong>
        <p>Go to "Workflows" -> "Create Workflow."</p>
        <p>Name your workflow "SOC Automation Project."</p>
        <p>Select any two use cases (it doesn't matter for this example).</p>
        <p>Click "Create."</p>
    </li>
    <li><strong>Add Webhook Trigger (Shuffle.png):</strong>
        <p>From the left menu, select the "Webhook Trigger" and drag it next to the "Change Me" icon.</p>
        <p>Rename the webhook to "Wazuh-agent."</p>
        <p>Copy the Webhook URI displayed (Webhook-URI.png).</p>
    </li>
    <li><strong>Add Repeat Back Action:</strong>
        <p>Click the "Change Me" icon next to "Find Actions." </p>
        <p>Select "Repeat back to me."</p>
        <p>In the "Call" field, click the "+" sign and select "Execution Argument."</p>
        <p>Save the action.</p>
</ol>

<h3>2. Configure Wazuh Manager for Shuffle Integration</h3>

<p>Connect to your Wazuh manager via SSH and configure the integration using the `ossec.conf` file.</p>

<ol>
    <li><strong>Edit `ossec.conf` (nano):</strong>
        <p>Type `nano /var/ossec/etc/ossec.conf` to open the configuration file.</p>
    </li>
    <li><strong>Add Integration Block:</strong>
        <p>Scroll to the end of the file, locate the closing `</global>` tag, and add the following integration block, replacing `#THE_URL_WE_COPIED_BEFORE#` with the copied Webhook URI:</p>
    <pre><code>
<integration>
  <name>shuffle</name>
  <hook_url>#THE_URL_WE_COPIED_BEFORE#</hook_url>
  <rule_id>100002</rule_id>
  <alert_format>json</alert_format>
</integration>
</code></pre>
        <p>Ensure proper indentation to match existing blocks.</p>
    </li>
    <li><strong>Save and Exit `nano`:</strong>
        <p>Save the changes (Ctrl + X, Y, Enter).</p>
    </li>
    <li><strong>Restart Wazuh Manager:</strong>
        <p>Restart the Wazuh manager service:</p>
        <pre><code>systemctl restart wazuh-manager.service</code></pre>
    </li>
    <li><strong>Verify Service Status:</strong>
        <p>Check the service status:</p>
        <pre><code>systemctl status wazuh-manager.service</code></pre>
    </li>
</ol>

<h3>3. Triggering the Workflow (Mimikatz Test)</h3>

<ol>
    <li><strong>Run Mimikatz on the Client:</strong>
        <p>On your Windows 10 client, open PowerShell and exit any running mimikatz instance with `exit`.</p>
        <p>Run mimikatz again with the command `.\mimikatz.exe`.</p>
    </li>
    <li><strong>Start Shuffle Workflow (Shuffle-start.png):</strong>
        <p>In your Shuffle.io instance, click "Start" on the created workflow.</p>
    </li>
    <li><strong>Verify Results (results.png):</strong>
        <p>Click the "Person" button at the bottom. If successful, you should see results from the mimikatz execution.</p>
        <p>You can expand the results for more details.</p>
    </li>
</ol>

<p>This demonstrates how to integrate Wazuh with Shuffle.io to automate workflows triggered by specific Wazuh rules (e.g., rule ID 100002 in this example).</p>


<h3>4. Advanced Automation and Integration</h3>

<p>Our Workflow for today:</p>
<ol>
    <li>Mimikatz Alert sent to Shuffle</li>
    <li>Shuffle Receives Mimikatz alerts and extracts SHA256 Hash from the file</li>
    <li>Check the reputation score with VirusTotal</li>
    <li>Send details to thehive to create alerts</li>
    <li>Send email to soc analyst to begin investigation.</li>
</ol>

<p>When we examine the returned hash values, we notice they are appended with their hash type. For example, <img src="./images/hash-1.png" alt="Hash Example"> shows a value appended with <code>SHA1=</code> followed by the actual hash. To automate further processing, we need to extract only the hash value itself.</p>

<p>If we don't extract the hash, the entire string (including <code>SHA1=</code>) would be sent to services like VirusTotal, which is not the desired behavior. We only want to send the raw hash value to VirusTotal for analysis.</p>

<p>Let's proceed with the extraction. Close the execution argument and click the "Change Me" icon. In the "Find actions" field, type <code>regex</code> and select <code>regex capture group</code>. For the input data, click the <code>+</code> button, then select "Execution argument" -> "hashes," which will populate the field like this: <img src="./images/input-data.png" alt="Input Data">.</p>

<p>If you're unfamiliar with writing regular expressions (regex), you can copy the hash value and use an AI solution like ChatGPT to generate the regex code. An example prompt might look like this: <img src="./images/chat-gpt.png" alt="ChatGPT Regex Request">. Then, paste the generated regex code into the regex section in Shuffle, as shown here: <img src="./images/Shuffler.png" alt="Shuffle Regex Configuration">.</p>

<p>Save the workflow. Click the "Person" icon, then click the refresh button or "rerun workflow" next to "details." Expanding the results will show that the SHA256 hash has been correctly parsed. Now we can automate sending this information to VirusTotal for scoring.</p>

<p>Rename the "Change Me" icon to <code>sha256_Regex</code>. Next, we'll use VirusTotal's API to automate hash checks and retrieve results. To use their API, you'll need a VirusTotal account. Sign up on their website, and once you have an account, copy your API key and return to Shuffle.</p>

<p>In Shuffle, click "Apps," search for <code>VirusTotal</code>, and press Enter. Drag and drop the VirusTotal app into your workflow. Rename the action to <code>VirusTotal</code>. In the "Find actions," we don't need to check an IP address; we want to check a hash. Click the dropdown menu; if you only see one option, wait a few minutes and refresh the page. Select <code>Get a hash report</code>. You can either paste your API key directly into the <code>Apikey</code> field or click the orange <code>+ Authenticate VirusTotal v3</code>, paste your API key, and click "Submit."</p>

<p>In the "id" field, delete the existing content, click the <code>+</code> button, hover over "regex," select "group," and click. Save the workflow again. Click the "Person" icon, select the middle workflow, and rerun it. Let's examine the VirusTotal output. Expanding the results, we see a status of <code>200</code>. Expanding further (<code>0: -> body -> data -> attributes -> last_analysis_stats</code>), we see <code>malicious 64</code>, indicating that 64 scanners detected the file as malicious <img src="./images/malicious.png" alt="Malicious VirusTotal Result">.</p>

<p>To recap: We configured our SOAR platform to receive Wazuh alerts, used regex to extract the SHA256 hash, and used VirusTotal to check its reputation. Next, we'll send the details to thehive to create an alert for case management. In the application tab (bottom left), search for <code>thehive</code>, click it, and drag and drop it into your workflow. Access thehive at <code>http://*thehiveip*:9000</code> with the credentials <code>admin@thehive.local</code> and password <code>secret</code>.</p>

<h2>Creating a New Organization and Users in thehive</h2>

<p>This guide walks you through creating a new organization and users in thehive:</p>

<h3>Creating a New Organization</h3>

<ol>
    <li>Click the <strong>plus button</strong> in the top corner.</li>
    <li>Enter your desired organization name (e.g., "Krysta") in the <strong>Name</strong> field.</li>
    <li>Add a description (e.g., "SOC Automation Project") in the <strong>Description</strong> field.</li>
    <li>Click <strong>Confirm</strong> to create the organization.</li>
</ol>

<h3>Adding Users</h3>

<ol>
    <li>Click the new organization name to access it.</li>
    <li>Click the <strong>plus button</strong> <img src="./images/new-users.png" alt="New Users Button"> to add users.</li>
    <li><strong>For a Regular User (Krysta):</strong>
        <ul>
            <li>Leave <strong>Type</strong> as <code>Normal</code>.</li>
            <li>Enter the user's login credentials:
                <ul>
                    <li><strong>Login:</strong> <code>krysta@test.com</code></li>
                    <li><strong>Name:</strong> <code>Krysta</code></li>
                    <li><strong>Profile:</strong> <code>Analyst</code></li>
                </ul>
            </li>
            <li>Click <strong>Confirm</strong> to create the user.</li>
        </ul>
    </li>
    <li><strong>For a Service User (SOAR):</strong>
        <ul>
            <li>Select <code>Service</code> for the <strong>Type</strong>.</li>
            <li>Enter the service account details:
                <ul>
                    <li><strong>Login:</strong> <code>shuffle@test.com</code></li>
                    <li><strong>Name:</strong> <code>SOAR</code></li>
                    <li><strong>Profile:</strong> <code>Analyst</code> (Ideally a custom profile with least privilege)</li>
                </ul>
            </li>
            <li>Click <strong>Confirm</strong> to create the user.</li>
        </ul>
    </li>
</ol>

<h3>Setting Up User Access</h3>

<ol>
    <li><strong>For Krysta:</strong>
        <ul>
            <li>Hover over Krysta's username and select <strong>Preview</strong>.</li>
            <li>Scroll down and click <strong>Set a New Password</strong>.</li>
            <li>Create a password and click <strong>Confirm</strong>.</li>
            <li>Click outside the window.</li>
        </ul>
    </li>
    <li><strong>For SOAR User (API Key):</strong>
        <ul>
            <li>Select preview for the SOAR user.</li>
            <li>Click <strong>Create API key</strong>.</li>
            <li>Copy the generated API key and store it securely.</li>
        </ul>
    </li>
</ol>

<h3>Logging In and Configuring thehive Integration in Shuffle</h3>

<ol>
    <li>Log out of the admin account.</li>
    <li>Log in with Krysta's credentials (<code>krysta@test.com</code> and password).</li>
    <li><strong>Authenticate thehive in Shuffle:</strong>
        <ul>
            <li>Click the <strong>plus button</strong> beside <code>Authenticate</code>.</li>
            <li>Paste your thehive API key into the designated field.</li>
            <li>Enter thehive's public IP address and port number in the URL section.</li>
            <li>Click <strong>Submit</strong>.</li>
        </ul>
    </li>
    <li><strong>Connect thehive to Your Workflow:</strong>
        <ul>
            <li>In the workflow editor, click on the Virustotal icon.</li>
            <li>Drag the blue dot on the Virustotal icon and connect it to the thehive icon.</li>
        </ul>
    </li>
    <li><strong>Configure thehive Alert Creation:</strong>
         <li><strong>Configure thehive Alert Creation:</strong>
        <ul>
            <li>Click on the <strong>date section</strong> (if available). If not, proceed to step 6.</li>
            <li>Click the <strong>plus button</strong> and select <code>Execution argument -> utc time</code>.</li>
            <li>Configure the remaining fields for thehive alert creation according to the provided details in the image <img src="./images/date-description.png"> <img src="flag-pap.png"> </li>
            <li>You can use the provided JSON code in the advanced tab (body section) if you don't have the individual fields (<img src="./images/sourceref.png"> </li>
        </ul>
    </li>
    
 <p>If you don't have the individual date section and other fields, you can use the provided JSON code in the advanced tab (body section):</p>
<pre><code>{
  description": "Mimikatz detected on Computer: $exec.text.win.system.computer from User: $exec.text.win.eventdata.user",

  "externallink": "${externallink}",

  "flag": false,

  "pap": 2,

  "time": "$exec.text.win.eventdata.utcTime",

  "severity": "2",

  "source": "Wazuh",

  "sourceRef": "Rule: 100002",

  "status": "New",

  "summary": "Mimikatz detected on Computer: $exec.text.win.system.computer, ProcessID: $exec.text.win.system.processID and CommandLine: $exec.text.win.eventdata.commandLine",

  "tags": [

    "T1003"

  ],

  "title": "$exec.title",

  "tlp": 2,

  "type": "Internal"

} </pre></code>



<li><strong>Save the Workflow.</strong></li>

<h3>Modifying the Cloud Firewall (Temporary for Testing)</h3>

<p>Before running the workflow, we need to temporarily modify the cloud firewall to allow inbound traffic on port <code>9000</code>, where our thehive instance resides. This rule will be removed after testing.</p>

<ol>
    <li>Navigate to your cloud provider's firewall settings (e.g., DigitalOcean):
        <ul>
            <li>In DigitalOcean, go to <code>Networking -> Firewalls</code>.</li>
            <li>Select the relevant firewall.</li>
        </ul>
    </li>
    <li>Create a new firewall rule:
        <ul>
            <li>Set the rule type to <code>Custom</code>.</li>
            <li>Specify port <code>9000</code>.</li>
            <li>Configure allowed IP addresses:
                <ul>
                    <li>Remove all IPv6 addresses.</li>
                    <li>Keep all IPv4 addresses (allowing access from any source).</li>
                </ul>
            </li>
        </ul>
    </li>
    <li>Save the firewall rule. This will temporarily allow any source to access your machine on port <code>9000</code>.</li>
</ol>


<h3>Verifying the Integration</h3>

<p>Returning to Shuffle, we can now rerun the workflow. Click the "Person" icon and then "Rerun Workflow."</p>

<p>Scrolling down in the workflow execution details, we should see confirmation that thehive integration was successful. Let's verify this in our thehive instance.</p>

<p>Access your thehive instance. You should see that an alert has been automatically created. Refreshing the thehive instance will display the new alert. Opening the alert provides more details about the event <img src="./images/success.png" alt="Successful thehive Alert Creation">.</p>

<p>Adding more information to the "Summary" field in the workflow configuration will provide even more detailed context within thehive alert, further streamlining investigations.</p>


<h3>Sending an Email Notification</h3>

<p>The next step is to send an email notification to our analyst using Shuffle containing relevant information. To achieve this:</p>

<ol>
    <li>Click on "Apps" in the bottom left corner.</li>
    <li>Drag and drop the "Email" application into the workflow.</li>
    <li>Connect the VirusTotal action to the Email action.</li>
    <li>Configure the email settings:
        <ul>
            <li>Enter the recipient's email address (any valid email address can be used).</li>
            <li>Set the subject to <code>"Mimikatz detected!"</code>.</li>
            <li>Configure the email body:
                <ul>
                    <li><code>"Time: "</code> Execution argument -> <code>utcTime</code></li>
                    <li><code>"Title:"</code> Execution argument -> <code>title</code></li>
                    <li><code>"Host:"</code> Execution argument -> <code>computer</code></li>
                </ul>
            </li>
        </ul>
    </li>
</ol>

<p>Save the workflow and rerun it.</p>

<p>Success! The email notification should be received, completing the home lab setup.</p>


<h2>Lab Details</h2>

<p>This lab focused on building a basic Security Operations Center (SOC) environment using several key tools:</p>

<ul>
    <li><strong>Sysmon:</strong> Deployed Sysmon on a Windows endpoint to capture detailed system activity, providing enhanced visibility into endpoint events.</li>
    <li><strong>Wazuh:</strong> Configured Wazuh as a Security Information and Event Management (SIEM) system to collect, analyze, and correlate security events from the Sysmon agent. This included configuring Wazuh to ingest Sysmon logs and creating a custom rule to detect mimikatz activity.</li>
    <li><strong>TheHive:</strong> Set up TheHive as a case management platform to track and manage security incidents generated by Wazuh alerts.</li>
    <li><strong>Mimikatz (Testing):</strong> Used mimikatz in a controlled environment to generate test events and validate the effectiveness of the custom Wazuh rule.</li>
</ul>

<p>Key skills and concepts covered in this lab include:</p>

<ul>
    <li>Endpoint monitoring with Sysmon.</li>
    <li>Log collection and analysis with Wazuh.</li>
    <li>Rule creation and customization in Wazuh.</li>
    <li>Incident management with TheHive.</li>
    <li>Understanding of common attack techniques (e.g., credential dumping with mimikatz).</li>
    <li>Integration of different security tools to create a more comprehensive security posture.</li>
</ul>

<p>By completing this lab, I gained practical experience in setting up and configuring a basic SOC environment, improving my understanding of endpoint security, log analysis, and incident response workflows.</p>
