### Group 1 AWS Group Project

### Task 4: Security consideration 

### Description 
When creating a safe cloud architecture on AWS, it's critical to recognize possible security threats and put policies in place to reduce them. The following lists the three main security issues that our solution addresses and the precise techniques we employed to resolve them.


### 1. Preventing unauthorized public access to the databases 

    Challenge:
        Attackers frequently target databases because they hold sensitive information. The risk of illegal access, 
        data breaches, and software vulnerability exploitation rises when a database server is directly exposed 
        to the internet.

    Solution:
        -  Private subnet: Since the database EC2 instance is set up in a private subnet, 
        it cannot be accessed from the public internet and does not have a public IP address.
        - Security group rules: We established a database security group (DB_Security_Group) 
        that restricts incoming connections from the web server's security group to port 3306 (MySQL). 
        This implies that the database can only be accessed by the web server and not by the internet.
        - No direct internet access : A NAT Gateway is used to transport outbound internet access from the database 
        (for updates, etc.), allowing the database to establish outgoing connections without being exposed to 
        incoming internet traffic.

### 2. Minimizing attack surface of public-facing components

    Challenge 
        The web server is more vulnerable to assaults like malware insertion, brute-force login attempts, 
        and denial-of-service (DoS) attacks since it is reachable via the internet.

    Solutions 
        - Strict security groups: The security group of the web server (Web_Security_Group) only permits necessary 
        inbound traffic from any location (HTTP on port 80, HTTPS on port 443). By default, all additional protocols 
        and ports are blocked.
        - Least privilege principle: Additionally, outbound rules are limited to permitting just necessary traffic.
        - Regular updates: We lower the possibility of exploitation by putting the web server in a public subnet and 
        limiting all other types of access.
        - Separation of roles: There is never any indirect internet exposure to the database server.
        - Explanation: Only the precise traffic required for the application to operate is let in by the firewall-like 
        restrictions (security groups); all other traffic is banned, reducing the likelihood of assaults.

### 3. Protecting data in transit and ensuring secure management
    Challenge 
        When data travels between components or when administrators connect to servers for maintenance, it may be 
        intercepted or altered. Sensitive data, including customer information and login credentials, 
        are exposed in unencrypted communications.

    Solutions 
        HTTPS For Web Traffic: 
        - User data is safeguarded throughout its transmission over the internet since the web server supports 
        secured HTTPS connections.
        - Internal Encryption : The private AWS network, which is separate from the public internet, 
        is used for communication between the web server and database.
        - Secure SSH Access: Only trusted IP addresses (a suggested extra step for production) and a key pair, 
        not a password, are permitted to access the web server over SSH.


### Extra recommendation for security 
    - Enable AWS CloudTrail to audit all API Activity for Accountability
    - Enable Multifactor Authentication for AWS Accounts 
    - Regularly Update and patch your servers 
