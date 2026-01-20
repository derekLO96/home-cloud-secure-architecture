## Deployment

### Why This Document Exists

This document describes the deployment process for this project. It focuses on the documentation of what I went through and the thought process on which this project came to life.

Here I will walk you, the reader, step by step on how I achieved my goals of creating a secure photo and video server at home. This document has several parts to it and it follows the exact steps I took to get to a functional system.

### Deployment Model

Before I begin it is important to understand what the model is for this project so the vision is more clear on how this project was achieved.

This project follows a hub-and-spoke model, this means that there is a central system, the hub, that connects other systems together, the spokes:

**1.** Oracle bastion is configured and provisioned first (hub)

**2.** Home server is configured and deployed second (spoke)

**3.** Client devices are then configured and added last (spoke)

This order is **crucial** and it ensures that secure connectivity is established **before** any application services become accessible.

## Prerequisites

Lets make sure that we have everything in place before configuration can begin.

### Infrastructure

- One cloud VM with a public IP
- One private ARM-based server
- One or more client devices

### Software

- Linux (Ubuntu for bastion and Raspberry pi OS 64-bit Lite for home server)
- WireGuard
- Docker & Docker Compose
- iptables / ufw
- Fail2Ban

## Deployment Phase

### Phase 1: Oracle Bastion Configuration

The concept of a "cloud bastion" is explained on the lessons-learn.md file and is recommended to go see first before continuing reading this document.

**1.** Create an oracle cloud account. This website explains exactly how to do just that:

```
https://profile.oracle.com/myprofile/account/create-account.jspx
```

**2.** Now create a compute instance:

While logged into your oracle cloud account and on the main menu, go to the navigation menu -> compute -> instance

![Oracle instance menu](Images/oracle-instance-meny.png "walk-through step 2")

**3.** Select the "Create Instance Button"

**4.** Lets configure this page so there are no confusions

- Choose a name for you cloud relay
- Under "Placement" leave the default option "AD1" during the time of this writing
- Under the Advanced option you can leave the default option once again "On-demand capacity"
- Leave cluster placement group off
- Under "Image" select "Change Image," Select the Ubuntu image, scroll down and choose "Canonical Ubuntu {Latest Version}," then press the "Select Image" button.
- Under "Shape" select the "Change Shape" button, choose "Virtual Machine," then under choose "Specialty and previous generation," scroll down and under "Image" choose the option that says "Always Free-eligible."
- Ignore the advance tab for now
- Click the "Next" button
- Under "Security" choose the "Shielded Instance" option. Since not all free options will allow us to have the "confidential computing" option, there might be an error message informing us of this. For this project this is acknowledged and is considered to not impede what this project set out to accomplish. Click the "Next" button.
- Under "Primary VNCI" create a name for you virtual network interface card
- Under "Primary network" choose the "create new virtual cloud network" option
- choose a virtual cloud network name
- Under "Subnet" choose the name of the new subnet being created
- Under "CIDR block" you can leave this as "10.0.0.0/24"
- skip all other option until we reach the SSH portion of configuration
- Under "Add SSH Keys" choose "Generate key pair for me" and then press the "Download private key" and "Download public key" buttons. **KEEP THESE SAFE** and under no circumstances share your private key with anyone else.
- press the "Next" button
- Skip this portion for it is not relevant for this project
- You will be directed to a page to confirm that all the information is correct.
- click the "Create" button. Here you might get any number of errors, although i cannot go through all the errors because it is outside the scope of this document, just know that if you need to change your shape because of availability, do so because it will not change how the rest of this project proceeds and will not impede on the ability to successfully accomplish our task.
- You will be taken to the "Work Request" page, wait until the "State" of your instance says "Succeeded." if Failed, follow oracle's own error messages and help pages.

**5.** Congratulations, you have now an oracle instance, but it is not connected to the internet yet or publicly available to connect to. This step will focus on getting the server visible on the internet and setting up some network configurations.

- Go back to the instance menu
- Click on your instance's name where you will be taken to the controls and configurations of your instance.
- Select the "Networking" category
- You should see a "Public IPv4 address," if you do your instance is connected to the internet! skip to step 6. If not don't worry, its easily fixed.
- scroll down the "Networking" page and under "Quick Actions" select the button "Connect"
- If no VCN (Virtal cloud network) is selected, then create one by clicking the highlighted button.
- leave all other option for now and click the "Connect" button.
- you will now see a IPv4 address on the top of the page, this is your server's IPv4 address.

**6.** Now that we have configured the server to be visible from the public internet, we can SSH into the server and start our configurations. Before we get to connecting, though, we want to figure out what the username is for our ssh session. On the instance's home page, under the "Details" category, we scroll down until we see "Instant Access," there the "Username" will be listed right under "Public IP address." These are the two pieces of information we will use to SSH into our server. For simplicity sake let's say the username is "Ubuntu" for example.

- Open a terminal and navigate to the folder where your private key is located
- change the permission on your private key:

```bash
chmod 600 your-ssh-key.key
```

- Now connect to your server:

  ```bash
  ssh -i your-ssh-key.key Ubuntu@{Your server public IP}
  ```

  the ` -i` option for ssh is the identity key that you should have been downloaded on step 4

- You will be prompted to save the host as trusted, type `yes` and connect
- You have successfully connected to your server!

### Phase 2. VPN Configuration (WireGuard)

Before we can get to the home server configuration we must first set up WireGuard on the cloud bastion. This will leave a door open for our home servers and clients to connect to each other once we finish configuring them.

While connected to your cloud bastion via SSH through the terminal

- Download WireGuard, ufw and nftables
  ```bash
  sudo apt install wireguard ufw nftables
  ```
- make sure that it has been installed correctly

  ```bash
  which wireguard
  which nftables
  which ufw
  ```

- ufw should be disabled by default, but if not disable it for now we will come back to it later.

```bash
sudo ufw disable
```

- Persist ipv4 forwarding, we need this for our cloud bastion to rout traffic for our WireGuard setup.

  ```bash
  echo "net.ipv4.ip_forward=1" | sudo tee /etc/sysctl.d/99-wireguard.conf
  sudo sysctl --system
  ```

- create new rules for your nftables

  ```bash
  sudo nft add table inet filter
  sudo nft add chain inet filter input { type filter hook input priority 0 \; policy drop \; }
  sudo nft add chain inet filter forward { type filter hook forward priority 0 \; policy drop \; }
  sudo nft add chain inet filter output { type filter hook output priority 0 \; policy accept \; }

  ```

### Phase 3: Home Server Deployment

### Phase 4: Firewall & Routing Validation

### Phase 5: Client Onboarding

## Deployment Validation Checklist

- [ ] Oracle bastion hardened and patched
- [ ] SSH Key-only Authentication enforced
- [ ] WireGuard tunnel established
- [ ] Home server unreachable from public internet
- [ ] Immich accessible only via WireGuard
- [ ] Logs generated for access attempts

## Operational Considerations

### Updates

- OS security updates applied on a regularly basis
- Docker images updated manually after review
- WireGuard updates monitored for compatibility

### Backups

- Application data backed up separately
- Backup destinations not publicly accessible
- Restore process tested periodically

### Availability

- VPN dependency acknowledged
- Oracle bastion availability directly impacts access (dependency on Oracle's services and up times)
- No automated failover in current scope

## Failure Scenarios

The deployment accounts for the following scenarios:

- **VPN failure**: Application becomes unreachable but remains secure
- **Oracle bastion compromise**: Access can be revoked by rotating keys
- **Client compromise**: Single peer access can be revoked without affecting other peers

Security is prioritized over availability in failure cases.
