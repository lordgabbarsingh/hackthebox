# Unified

#### The first task is to discover the services
    - 22
    - 6789
    - 8080
    - 8443

#### The next task required the version of the software running on port 8443. Nmap did not provide the information despite using the `-sV` flag.
    - But since it's a TCP protocol, we use the browser.
    - We direct to `https://[IP]:8443`.
    - This loads a login page with version mentioned = `6.5.54`.

#### On a little recon for Unifi Network Vulnerabilties for the given version we found:
    - We found a security vulnerability in a third party application.
    - The CVE for this issue is `CVE-2021-44228`.
    - The third party library is log4j and the CVE details can be found on NVD.
        - An attacker who can control log messages or log message parameters can execute arbitrary code loaded from LDAP servers when message lookup substitution is enabled. The vulnerability is also called Log4Shell or Jog4Jam.
        - More details on how to exploit can be found here. https://www.sprocketsecurity.com/resources/another-log4j-on-the-fire-unifi

#### To understand the exploit we must understand few key terms:
    - JNDI = Java Naming And Directory Interface - API that allows applications to communicate with other applications such as LDAP, DNS, NIS, NDS, RMI and CORBA.
        - LDAP - Lightweight Directory Access Protocol
        - DNS - Domain Naming System
        - NIS - Network Information Service
        - RMI - Remote Method Invocation
        - CORBA - Common Object Request Broker Architecture

#### Exploit
    - We will use the code found here. https://github.com/black9/Log4shell_JNDIExploit
    - Basically starts the LDAP and HTTP server.
    - 

