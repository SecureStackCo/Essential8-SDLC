# Essential 8 and the Software Development Lifecycle (SDLC)

The [Essential 8](https://www.cyber.gov.au/acsc/view-all-content/essential-eight) was established from ASD's experience dealing with cyber intrusions. ASD mostly operate in government and enterprise sectors. These sectors are usually Microsoft environments, and the Essential 8 reflect this. The Essential 8 are:

* [Application Control](#application-control)
* [Patch Applications](#patch-applications)
* [Configure Microsoft Office Macro Settings](#microsoft-office)
* [User Application Hardening](#user-application)
* [Restrict Administrative Privileges](#restrict-admin)
* [Patch Operating Systems](#patch-os)
* [Multi-Factor Authentication](#mfa)
* [Regular Backups](#backups)

The E8 helps with securing standard enterprise setups. But software development is anything but standard. We at SafeStack believe the underlying principles can be adapted to software development. This document will help introduce SSDLC concepts for each control.

## Background

Software development has changed. The Essential Eight was written in a world where C#, MVC, VMWare, and dedicated on-premise windows hosts were the norm. Today, cloud environments are the default, DevOps is standard practice, JavaScript runs everything, and Users operate primarily through a web browser. We are not debating whether E8 is effective, but we are introducing ways to make it relevant. For a software engineering world.

**So, what follows are the Essential 8 mitigations reframed in this new modern, web context.**

**We need to consider two contexts, the Browser, and the Server**

<h2 id="application-control">1. Application Control</h2>

Users interact with web applications through the browser. Browsers give tremendous freedom to users, across a multitude of devices, and in a consistent but tailored experience. The browser is king, and adversaries know that. 

Application Control prevents the execution of applications that haven't explicitly been allowed to. It can specify which browsers are okay, their versions, their plugins, and more. As a desktop administrator, we can control this but we cannot easily control the interpretation of data received by the browser.

As a software engineer though, we can help browsers safely interpret content to protect our users.

1. Introduce Subresource Integrity > Application Control of JavaScript Libraries
2. Introduce a Content-Security-Policy > Application Control of Remotely Served Content
3. Use the Sanitizer API > Application Control of Locally Server Content

By adopting these controls, we are controlling what can execute in a browser. Subresource Integrity only allows execution of content if the hash matches. This prevents execution of libraries with tampered content. Content-Security Policies specify what type of content can execute, and where that content can come from. If used with the strict-dynamic property, you can prevent execution of content from sources outside what you specify. Finally, if you send all received content to the Sanitiser API that is being built into browsers as this is written, you can prevent browsers from executing content by only allowing safe characters and safe markup.

**Implications for SDLC:**

This control is all about defining what applications can run on computers that affect the company.  The language specifically calls out “servers and workstations” but really any compute that is owned by, managed by, or affects the production of an orgs systems is in scope.  This section here expands the scope of this section dramatically:  **“Allowed and blocked executions are centrally logged and protected from unauthorised modification and deletion, monitored for signs of compromise, and actioned when cyber security events are detected**

**Controls:**

1. Are applications logging all access, good or bad, to a central place?
2. Has your org defined and limited what can run in your CI/CD?  Each one of these steps is a binary and has unlimited access to the internet.  Do you have controls around this?

<h2 id="patch-applications">2. Patch Applications</h2>

Web applications are collections of technologies bundled together as a "stack".  This "stack" involves multiple components some of which the application developers control and some that they don't.  For those parts of the application that they **DO** control, they should make sure that they are patching and updating those components frequently.

**Implications for SDLC:**

This section is ostensibly about patching applications, but really is expanded, especially in the highest maturity model, to include identifying vulnerabilities.

**Controls:**

1. Web vulnerability analysis is enabled for internet-facing services
2. Out of date, insecure or malicious web components are patched or removed
3. Un-needed libraries, frameworks and components are removed
4. All CI/CD environments use up to date applications
5. All container environments use up to date applications
6. All application environments are built via automation so that latest and most secure application versions are being deployed

<h3 id="microsoft-office">3. Configure Microsoft Office Macro Setting</h2>

Yeah, naw.

<h3 id="user-application">4. User Application Hardening</h2>

Because the vast majority of applications that users interact with are delivered via a browser it’s important that we harden these applications.    The language of this section talks entirely about a browser but misses 95% of modern browser security.  Instead it talks about a few Microsoft specific technologies like .NET and PowerShell, or older tech like Java and Flash.  It ignores for example insecure Javascript and other client side technologies that are 10X more commonly used than .NET.

Because many InfoSec professionals don’t yet understand how different modern web applications are build and run, they over index on old server side technologies and ignore the most common attack vectors.

**Implications for SDLC:**

The core of this section is protecting the browser experience and should be expanded to *all* browser protections including CSP, HSTS, and other controls that are delivered with a HTTP response header.  Further, this section should cover TLS/SSL and making sure that the comms a browser uses are forced to be private and secure.

**Controls:**

1. Enforce end to end encryption on all communication in transit.  Certificate, redirects,
2. Use modern and secure TLS version
3. Create a customized Content Security Policy (CSP)
4. Remove all out of date, insecure and malicious web components, frameworks and libraries

<h2 id="restrict-admin">5. Restrict administrative privileges</h2>

Modern web applications and SaaS solutions allow users to authenticate to use the functionality therein.  This authentication decides if the user can log in, but it does not decided what the user can access which is a function of authorization.  Authentication and authorization are two different things but in many SaaS solutions there is no differentiation of users or the role that users provide.  So, it’s an important part of the Essential 8 AppSec strategy to determine if a web app has the ability to give users different levels of access via a role or similar method.  If they don't then there is no way to restrict administrative privileges.

**Implications for SDLC:**

Least permission needs to be applied everywhere.  This includes on obvious places covered in the original Essential 8 like servers and the users desktop, but also in the CI/CD and build processes, cloud environments, and in the access controls in the web applications that our customers use.

**Controls:**

1. Development, testing, and operational environments shall be separated to reduce the risks of unauthorized access or changes to the operational environment
2. Explicit User and group/teams access is applied to source code repository
3. Explicit user and group/teams permission is applied to cloud resources

<h2 id="patch-os">6. Patch Operating Systems</h2>

The underlying operating system beneath an application is often thought of out of the control of the application developers.  If the stack that the development team are using means that a server and its operating system are managed by them, then they need to make sure that the OS is patched and updated frequently.  Typically in a cloud context the use of IaaS means that the OS is self managed, but in the context of PaaS and SaaS the OS is managed by the hosting provider.   Applications built using IaaS or similar means that the relationship of other tech stack components.

**Implications for SDLC:**

This section is ostensibly about patching operating systems, but really is expanded, especially in the highest maturity model, to include identifying vulnerabilities in operating systems wherever they are used.  This includes physical or virtualized servers, compute environments for containers and CI/CD or build environments.

**Controls:**

1. System vulnerability analysis is enabled for all servers
2. Out of date, insecure or malicious operating system components are patched or removed
3. All CI/CD environments use up to date and patched operating systems
4. All container environments use up to date and patched
5. All compute environments are automated using IaaC or similar so that latest and most secure OS versions are being deployed

<h2 id="mfa">7. Multi-factor authentication</h2>

Multi-factor authentication (MFA) is the single greatest security control you can add to an application environment to protect the user and their data.  MFA is relatively easy to integrate into modern web applications, as well as infrastructure platforms and cloud providers.  If sensitive data of any kind is housed in your applications you should enforce MFA for all of your users.

**Implications for SDLC:**

 There are several ways to embed MFA into the SDLC that are easy and accessible for both developers and the end users.

**Controls:**

1. MFA should be enabled by default for any web applications that store sensitive data.
2. MFA should be enabled for all source code management processes including git push, and pull requests.
3. MFA should be enabled for all important third party dependencies like identity access providers (IDP), cloud resources, and logging platforms.
4. All anomalous MFA events are logged and sent to central logging platform

<h2 id="backups">8. Regular Backups</h2>

Backups are the single most important thing that a company can do to make themselves more resilient.   Backups should occur for all important parts of the environment.

**Implications for SDLC:**

Source code is the crown jewels of any companies intellectual portfolio assets.  So, making sure that source code, and the way to deploy it is backed up is important.

**Controls:**

1. All source code is version controlled using standard version controlled systems (VCS)
2. All important credentials are stored in resilient storage system
3. The build and deploy processes should be version controlled using VCS
4. Secure infrastructure should be version controlled using VCS  

### Essential 8 mapping to ISM

------

| Mitigation Strategy | Maturity Level Two | Maturity Level Three | SDLC |
| :--- | :---   | :---   | :---   |
| Application control | 0843, 1490, 1657, 1660, 1661 | 0843, 1490, 1656, 1657, 1658, 1544, 1659, 1582, 1660, 1661, 1662, 1663 | 1536, 1757 |
| Patch applications | 1690, 1691, 1693, 1698, 1699, 1700, 1704 | 1690, 1691, 1692, 1693, 1698, 1699, 1700, 1704, 0304 | 0380, 0402, 0938, 1467, 1483, 1754 |
| Configure Microsoft Office macro settings | 1671, 1488, 1672, 1673, 1489, 1677 | 1671, 1674, 1487, 1675, 1676, 1488, 1672, 1673, 1489, 1677, 1678 |  |
| User application hardening | 1486, 1485, 1666, 1667, 1668, 1669, 1542, 1670, 1412, 1585, 1664 | 1486, 1485, 1654, 1667, 1668, 1669, 1542, 1670, 1412, 1585, 1655, 1621, 1622, 1664, 1665 | 1139, 1424, 1552 |
| Restrict administrative privileges | 1507, 1647, 1648, 1175, 1380, 1687, 1688, 1689, 1387, 1685, 1509, 1650 | 1507, 1647, 1648, 1508, 1175, 1653, 1380, 1687, 1688, 1689, 1649, 1387, 1685, 1686, 1509, 1651, 1650, 1652 | 0400, 1419, 1422 |
| Patch operating systems | 1694, 1695, 1701, 1702, 1501 | 1694, 1695, 1696, 1701, 1702, 1407, 1501 | 0380 |
| Multi-factor authentication | 1504, 1679, 1680, 1681, 1173, 1401, 1683 | 1504, 1679, 1680, 1681, 1173, 1505, 1401, 1682, 1683, 1684 | 1546 |
| Regular backups | 1511, 1515, 1705, 1707 | 1511, 1515, 1705, 1706, 1707, 1708 | 1419 |

### Sponsors

------

Sponsored with 💜  by
<a href="https://securestack.com" target=”_blank” rel="noopener noreferrer"><center><img src="https://securestack.com/wp-content/uploads/2021/09/securestack-horizontal.png" width="500"/></center></a>
