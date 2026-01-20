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

---

<h2 id="step-2">02. Create a VPN Setup from AWS side</h2>
<h3 id="step-2a">a. Create VPN Customer Gateway</h3>

    		AWS console
			→ search "vpc"
				→ click "customer gateway" on VPC session
					click "new customer gateway"
						Name tag: "your customer gateway name"
						IP address: "your on-premise public IP"
						click "create custome gateway"

<h3 id="step-2b">b. Create VPN Virtual Private Gateway</h3>

    		AWS console
			→ search "vpc"
				→ click "virtual private gateway" on VPC session
					Name tag: "your virtual privat gateway name"
					click "create virtual private gateway"
				→ select "your virtual private gateway" 
				→ click "actions"
					→ click "Attach to VPC"
						choose "your VPN"
							→ click "Attach to VPC"

<h3 id="step-2c">c. Create VPN Connection</h3>

    		AWS console
			→ search "vpc"
				→ click on "Site-to-Site VPN connections" on VPC session
					→ click "Create VPN connection"
						Name tag: "add your vpn connection name"
						Target gateway type: select "Virtual private gateway"
						Virtual private gateway: select "your virtual private gatway"
						Customer gateway: select "Existing"
						Customer gateway ID: select "your customer gateway"
						Routing options: select "static"
						Static IP prefixes: "add the static on-primese network - sample: 192.168.0.0/24" (add all you need)
						Pre-shared key storage: select "Standard"
						Click "Create VPN connection"

<h3 id="step-2d">d. Download your VPN configuration</h3>

    		aws console
			→ search "vpc"
					→ select "your vpn connection"
						→ click "in your VPN ID"
							→ click "Download configuration"
								Vendor: "Strongswan"
								Platform: "Ubuntu 16.04"
								Software: "Strongswan 5.5.1+"
								IKE version: "ikev2"
								→ click "Download"

<h3 id="step-2e">e. Update "Route table" with your On-Premise network</h3>
