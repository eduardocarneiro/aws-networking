# Setup AWS Site-to-Site VPN Virtual Private Gateway

This is a step-by-step used to deploy a AWS Site-to-Site IPSec VPN using Virtual Private Gateway and Strongswan
 from On-premise side.

---

## 📑 Table of Contents

<details open>
<summary><b>Click to expand/collapse sections</b></summary>

<ul>
    <li>
        <a href="step-1"><b>01. Overview of On-Premise environment</b></a>
        <ul>
            <li><a href="#step-1a">On-premise Diagram</a></li>
            <li><a href="#step-1b">Setup Global GW router</a></li>
            <li><a href="#step-1c">Setup Local GW router</a></li>
        </ul>
        <a href="step-2"><b>02. Create a VPN Setup from AWS side</b></a>
        <ul>
            <li><a href="#step-2a">Create VPN Customer Gateway</a></li>
            <li><a href="#step-2b">Create VPN Virtual Private Gateway</a></li>
            <li><a href="#step-2c">Create VPN Connection</a></li>
            <li><a href="#step-2d">Download your VPN configuration</a></li>
            <li><a href="#step-2e">Update "Route table" with your On-Premise network</a></li>
        </ul>
        <a href="step-3"><b>03. Create a VPN setup from On-premise side</b></a>
        <ul>
            <li><a href="#step-3a">Install your Linux (AlmaLinux 9.6)</a></li>
            <li><a href="#step-3b">Install Strongswan (LibreSwan) [1]</a></li>
            <li><a href="#step-3c">Enable "IP Forward"</a></li>
            <li><a href="#step-3d">Create "/etc/ipsec.conf" file</a></li>
            <li><a href="#step-3e">Create "/etc/ipsec.secrets" file/a></li>
            <li><a href="#step-3f">Restart IPsec service</a></li>
            <li><a href="#step-3g">Create HealthCheck script "/etc/ipsec.d/aws-updown.sh"</a></li>
            <li><a href="#step-3h">Restart IPsec service</a></li>
            <li><a href="#step-3i">Check the IPsec VPN services</a></li>
        </ul>
       <a href="step-4"><b>04. Create a VPN setup from On-premise side</b></a>
        <ul>
            <li><a href="#step-4a">Install your Linux (AlmaLinux 9.6)</a></li>
            <li><a href="#step-4b">Install Strongswan (LibreSwan) [1]</a></li>
            <li><a href="#step-4c">Enable "IP Forward"</a></li>
            <ul>
                <li><a href="#step-4c">Enable "IP Forward"</a></li>
                <li><a href="#step-4c">Enable "IP Forward"</a></li>
            </ul>
</ul>
</details>