# VPN Port Connectivity Troubleshooting: Claro Network

This guide provides a systematic approach to testing if **IPSEC** ports is successfully traversing your internet service provider. 

In my case I am based in Brazil and I use 'Claro' as service provider. Here we have many others, you can use this example to check your case.

---

## 📑 Index

<details open>
<summary><b>Test VPN port in your network</b></summary>
<ol>
    <li><a href="#step-1">Study case</a></li>
    <li><a href="#step-2">Create an EC2 Instance in your default AWS VPC</a></li>
    <li><a href="#step-3">Open the IPsec port in your router</a></li>
    <ul>
      <li><a href="#step-4">Open the port 500 and forward it to VPN Server</a></li>
    </ul>
    <li><a href="#step-5">Setup UDP Listener on VPN Server</a></li>
    <ul>
      <li><a href="#step-6">Create an UDP Python service</a></li>
      <li><a href="#step-7">Run your service</a></li>
    </ul>
    <li><a href="#step-8">Test the Connection</a></li>
    <li><a href="#step-9">Result on VPN Server</a></li>
</ol>
</details>


---

<h2 id="step-1">1. Study case</h2>

The first step is access your router and check if in your WAN port you have a public IP.

    if yes: 
        continue reading this 
    else
        unfortunately, you need a public IP to do that.

From AWS documentation, what we need to do is something like the picture below to sucssefully has a IPsec VPN site-to-site, from your AWS account to your environment using StrongSwan from your side.

AWS Diagram:
<img src="./aws-vpn-diagram.png" alt="AWS VPN Diagram">

My LAB:
<img src="./my-lab-diagram.png" alt="My VMware LAB Diagram">


--- 
<h2 id="step-2">2. Create an EC2 Instance in your default AWS VPC</h2>

Deploy a small EC2 instance (e.g., t2.micro) within your **Default VPC**. This instance will act as the external "Internet" client to probe your home/office network.

<ul>
    <li><b>Security Group:</b> Ensure Outbound rules allow UDP traffic.</li>
    <li><b>Public IP:</b> Note the Elastic IP or Public IPv4 of this instance.</li>
</ul>

<img src="./aws-ec2.png" alt="Print of an EC2 image instance on AWS Cloud">

---

<h2 id="step-3">3. Open the IPsec port in your router</h2>
<h3 id="step-4">Open the port 500 and forward it to VPN Server</h3>
Access your service provider router (my case at `192.168.0.1`) and navigate to the **Port Forwarding / Virtual Server** section.

<table>
  <tr>
    <th>Setting</th>
    <th>Value</th>
  </tr>
  <tr>
    <td><b>External Port</b></td>
    <td>500</td>
  </tr>
  <tr>
    <td><b>Internal Port</b></td>
    <td>500</td>
  </tr>
  <tr>
    <td><b>Protocol</b></td>
    <td>UDP</td>
  </tr>
  <tr>
    <td><b>Target IP</b></td>
    <td>192.168.0.14 (Your VPN Server)</td>
  </tr>
</table>

My router TP-Link DECO example:
<table>
  <tr>
    <th>01</th>
    <th>02</th>
    <th>03</th>
    <th>04</th>
    <th>05</th>
    <th>06</th>  
  </tr>
  <tr>
    <td><img src="./router-screen1.png" alt="Screen 1" style="width: 100%; border-radius: 5px; display: block;"></td>
    <td><img src="./router-screen2.png" alt="Screen 2" style="width: 100%; border-radius: 5px; display: block;"></td>
    <td><img src="./router-screen3.png" alt="Screen 2" style="width: 100%; border-radius: 5px; display: block;"></td>
    <td><img src="./router-screen4.png" alt="Screen 2" style="width: 100%; border-radius: 5px; display: block;"></td>
    <td><img src="./router-screen5.png" alt="Screen 2" style="width: 100%; border-radius: 5px; display: block;"></td>
    <td><img src="./router-screen6.png" alt="Screen 2" style="width: 100%; border-radius: 5px; display: block;"></td>
  </tr>
</table>

---

<h2 id="step-5">5. Setup UDP Listener on VPN Server</h2>

On the final destination server, we will run a simple Python script to listen for incoming packets to verify the path is open.

<h3 id="step-6">Create the UDP Python Service</h3>

Create a file named `vpn_test.py`:

<pre style="background-color: #2d2d2d; color: #ccc; border-radius: 5px; padding: 15px;">
<code style="color: #66d9ef;">import</code> socket

UDP_IP = <span style="color: #e6db74;">"0.0.0.0"</span>
UDP_PORT = <span style="color: #ae81ff;">500</span>

sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
sock.bind((UDP_IP, UDP_PORT))

<span style="color: #66d9ef;">print</span>(<span style="color: #e6db74;">f"Listening for VPN traffic on port {UDP_PORT}..."</span>)

<span style="color: #66d9ef;">while True</span>:
    data, addr = sock.recvfrom(<span style="color: #ae81ff;">1024</span>)
    <span style="color: #66d9ef;">print</span>(<span style="color: #e6db74;">f"Received message: {data} from {addr}"</span>)
</pre>

<h3 id="step-7">Run the Service</h3> 

    [root@vpn-a ~]# sudo python3 vpn_test.py

<h2 id="step-8">6. Test the Connection</h2>

Log in to your AWS EC2 instance and send a test packet toward your Claro Router's Public IP.
     
    ubuntu@ip-172-x-x-x:~$ echo "test aws" | nc -uv public-ip 500


Success Criteria: If the configuration is correct, you will see the message "Received message: b'VPN_TEST_PACKET'" appear in your VPN server's Python console.

<h2 id="step-9">6. Result on VPN Server</h2>

	  [root@vpn-a ~]# python service.py 
	  UDP server listening on 0.0.0.0:500
	  Received message: X from ('192.168.0.14', 45987)
	  Received message: X from ('192.168.0.14', 45987)
	  Received message: X from ('192.168.0.14', 45987)
	  Received message: X from ('192.168.0.14', 45987)
	  Received message: X from ('192.168.0.14', 45987)
	  Received message: test aws
	   from ('192.168.0.14', 46127)
