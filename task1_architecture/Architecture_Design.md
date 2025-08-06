### Task 1: Architecture Design

### Description 
The goal of our project is to design and implement a secure and scalable cloud infrastructure on Amazon Web Services (AWS) to host a simple web application EC2 and a private database EC2. 
The web server will have an application that will be accessible over the internet, and the database will remain private and protected. 
The architecture will follow AWS best practices for security, availability, and network segmentation.

### Infrastructure Building Plan
       
        Step 1: Set up the VPC 
	        Set up a custom Virtual Private Cloud.
                Create a public subnet.
        	    Create a private subnet. 
        	Create 2 separate availability zones.
            	One availability zone for the private subnet and one availability zone for the public subnet.

        Step 2: Set up the Internet Gateway and NAT Gateway
        	Attach an Internet Gateway
            	Allows anything in the public subnet (like our web server) to connect to the internet.
        	Create a NAT Gateway
            	Provides our database with a way to reach the internet (for things like updates) while remaining private from the internet.
        	Update Route Tables 
            	This tells AWS which gateway to use for sending internet traffic.
            	    Public - Route all outbound traffic through the Internet Gateway.
            	    Private – Route traffic through the NAT Gateway. 	
        
        Step 3: Launch EC2 Instances 
        	Deploy a web application EC2 instance in the public subnet, so users can access it over the internet.
        	Deploy a database EC2 instance in the private subnet, so it’s hidden from the internet.
        
        Step 4: Set up Security Groups 
            Note: Security Groups act like firewalls and control what traffic is allowed or denied. 
           
            Set up a security group for the webserver (public traffic)
    	    Web_Security_Group (Inbound Rules)
	            HTTP  --> Port 80  --> Source: 0.0.0.0/0 
            	HTTPS --> Port 443 --> Source: 0.0.0.0/0 
                    	Indicating all HTTP/HTTPS web traffic is allowed.

            Set up a security group for the database server (private traffic)
        	DB_Security_Group (Inbound Rules):
            	MySQL --> Port 3306 --> Source: Custom  
            	Select the Web_Security_Group from the dropdown.

        Step 5: Elastic IP / Verification 
        	Assign an Elastic IP to the Web Server 
        	An Elastic IP refers to assigning the webserver a static public IP address.
            	This makes it easier to:
                	Access the server without constantly looking up the IP address of the server.
                	Allows us the ability to associate a domain name with the IP address. 
                	Access the webserver using the browser
                	Test that the app is working.
                	Test Private Communication Between Webserver and Database
                	Confirm secure, private communication between the webserver and database server.
