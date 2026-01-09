## Lessons Learned

### Why This Document Exists

This project was intentionally ambitious for my current stage as a college undergraduate.
The goal of this document is to reflect honestly on what I learned, what challenged me, and how this project shaped my understanding of cybersecurity beyond just theory and NIST web pages.

Rather than presenting this system as "perfect," this file captures the lessons gained from building, breaking, fixing and documenting a real-world security focused project.

## 1. Secure Design Starts with Reducing Exposure

One of the biggest lessons from this project was realizing how much risk can be reduced simply by not exposing services to the internet at all.

Early on, I assumed security would mainly invilve adding protections after deployment. Instead, the most impactful decision was architectural, keeping the application private and accessible only through a VPN.

From this what I learned:

- Fewer exposed services means fewer things to defend
- Architecture decisions matter more than individual tools
- Security can often be improved by removing features, not adding them

If I where to start this project now I would spend more time in architectural planning than figuring it out as I went along.

## 2. Networking Is More Complex Than It Looks in Diagrams

Reviewing network architecture on websites was much easier than making it work in practice. VPN routing, NAT behaviour, and firewall rules behaved differently across all the different environments. This was honestly the most frustrating part.

What I learned:

- Small networking mistakes can completely break access
- The order of iptables and firewall rules **matter**
- Troubleshooting requires patience, methodical testing and an amazing online community
- **MAKE BACKUPS** (probably the most important lesson)

This part of the project really deepened my understanding of how real world networks behave and taught me so many good network configuration practices.

## 3. Cloud Bastions Are a Practical Security Patterns

I first learned of the word Bastion through Oracle's own cloud infrastructure diagram.
![Oracle Bastion Overview](~/code/home-cloud-secure-architecture/Images/bastion-overview-diagram.png "An overview of Oracle's cloud infrastructure (Regional)")

Using a cloud bastion as a relay felt easy at first due to that diagram, but proved to be much more complex than imagined. Even so, this was probably the most valuable part of this design.

What I learned:

- A cloud bastion can protect home infrastructure without exposing it
- Cloud firewalls add an extra security layer
- Centralized routing simplifies access management, but convoluted to setup.

This website helped me so much in designing the architecture of this project and deserves a shout out:

https://docs.oracle.com/en-us/iaas/Content/Bastion/Concepts/bastionoverview.htm

## 4. Containers Are Helpful, but Not Automatically Secure

Docker made deployment easier, but it also introduced a new risk if not configured carefully.

What I learned:

- Containers don't replace good security practices
- Default networking settings can expose services unintentionally
- Isolation is only effective when it's intentionally configured

This reinforced the idea that tools are only as secure as how they are used. A common pattern in cybersecurity.

## 5. Most Problems Come From Misconfiguration

Throughout this project, the biggest issues I encountered were caused by my own misconfigurations. A security risk echoed by the biggest players in the cybersecurity game (NIST, MSTIC, MITRE, to name a few).

What I learned:

- Firewalls and NAT rules require precision
- Documentation prevents repeated mistakes
- Security failures are most likely self-inflicting
- Order matters, create a configuration checklist in order

This experience made me more careful and disciplined when making system changes.

## 6. Logging Matters

At first, logging felt secondary compared to prevention. During troubleshooting, it became essential. Never underestimate good logging practices.

What I learned:

- Logs are critical for understanding system behaviour
- Even the most basic of logs provide impactful insight.
- Detection and visibility should be considered early.

This changed how I think about monitoring in secure systems.

## 7. Security Is About Tradeoffs

Every major decision involved tradeoffs between security, usability, complexity, and convenience.

Examples:

- VPN-only access increased security, sure, but reduced convenience and increased complexity
- No public DNS reduced exposure but limited flexibility
- Manual key management improved control but added overhead and time consuption
- iptables and ufw configurations limited the attack surface but turbocharged complexity

What I learned:

Good security design is about making informed compromises and documenting them.

## How This Project Changed My Perspective

Before this project, cybersecurity was nothing more than just homework for class. It felt abstract and tool-focused. After completing this project, I better understand that security is about:

- Thoughtful system design
- Clear trust boundaries
- Understanding how things fail and how to fix them
- Learning from mistakes
- Patience
- Logging and analysis

This project pushed me well outside my comfort zone and confirmed my passion in continuing to learn and grow in the cybersecurity field. It helped me shape my understanding of secure systems, and taught me so much about what cybersecurity truly means.

## Looking Ahead

This project initially started because I wanted to break away from my dependency on BigTech. I wanted to take all my most valuable photos and videos and store them securely at my house, where I could have more control over my memories, but I didn't want to loose that convenience of remote access from anywhere in the world.

Now that I have achieved that initial desire while still maintaining it as secure if not more secure, I can start looking into other ways that I can increase my independence. Now that I have my photos and videos securely backed up at home, I can look forward now into adding a file vault, like Google's Drive application. I could add a github like service for my future projects and documentation. Maybe in the far future, my encrypted identification and encrypted credit card information (this one needs more planning and industry level security).

In the near future, though, I look forward into adding:

- Centralized logging behaviour
- Automate configuration validation
- Improve key management process, even automate it
- Intrusion detection solutions

These are areas I'm actively interested in learning more about and look forward into improving this system with.

## Final Thoughts

This project represents a learning experience, not a finished product.
It reflects curiosity, persistence, and a genuine interest in understanding how secure systems are designed and operated in practice.

As a student, this project helped bridge the gap between classroom concepts and real-world security challenges. It motivated me to keep building, breaking, and learning.
