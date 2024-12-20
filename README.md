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
