Hi,

Thanks a lot for enrolling into HTTPS with Let's Encrypt!

This document contains reference links to all websites mentioned on video and
a complete history of every command we run together in this course.

Thanks for being a student,
Nick Janetakis
https://nickjanetakis.com
@nickjanetakis



*******************************************************************************
1. Course Introduction and Preparation
*******************************************************************************

-------------------------------------------------------------------------------
Meet Your Instructor
-------------------------------------------------------------------------------

- Blog posts and courses for developer oriented topics:
    https://nickjanetakis.com

- Dive Into Docker learning platform with blog posts and free / paid courses:
    https://diveintodocker.com

- Follow me on Twitter for general developer and Docker tips:
    https://twitter.com/nickjanetakis

-------------------------------------------------------------------------------
Get Set up with SSH and SCP
-------------------------------------------------------------------------------

...............................................................................
MacOS, Linux and Bash on Windows
...............................................................................

# Create an SSH key pair
ssh-keygen -t rsa
[Use the defaults for everything (press enter a few times), skip the passphrase]

# Confirm you have SCP installed
scp
[If you don't get similar output as me, install it for your OS by Googling]

...............................................................................
Windows (without Bash on Windows)
...............................................................................

- (Recommended) Create an awesome Linux development environment in Windows:
    https://nickjanetakis.com/blog/create-an-awesome-linux-development-environment-in-windows-with-vmware

- (Alternative to VM) Windows SSH instructions for PuTTY:
    https://www.digitalocean.com/community/tutorials/how-to-use-ssh-keys-with-putty-on-digitalocean-droplets-windows-users
    [Follow up to and including the "Create an SSH Key Pair" section]

- (Alternative to VM) Windows instructions for downloading and configuring WinSCP / PSCP:
    WinSCP:
      https://winscp.net/eng/download.php
      https://winscp.net/eng/docs/guide_digitalocean
    PSCP:
      https://the.earth.li/~sgtatham/putty/latest/w64/pscp.exe
      http://www.binbert.com/blog/2011/04/secure-file-transfer-from-windows-to-linux-using-rsa-key/



*******************************************************************************
2. Understanding HTTPS and SSL Certificates
*******************************************************************************



*******************************************************************************
3. Setting up a Server with DigitalOcean
*******************************************************************************

-------------------------------------------------------------------------------
Why I Picked DigitalOcean
-------------------------------------------------------------------------------

- Redeem $10 credits for DigitalOcean:
    https://m.do.co/c/0a14c0d916b3


-------------------------------------------------------------------------------
Copying the Course Material to Your Server
-------------------------------------------------------------------------------

# Move into the src/ directory that came with this course and then run:
scp -r $PWD root@hostname:~

# UPDATE November 2019:
#   On video, the above command uses "." instead of $PWD but "." no longer
#   works due to newer versions of scp preventing that.

# Make the scripts executable:
ssh root@YOUR_SERVER_IP

# UPDATE November 2019:
#   Due to the $PWD change, the files will be uploaded into a src/ directory
#   instead of directly into the home directory, so we need to move the files
#   back 1 directory. That's what this mv command does. It's important that
#   you run this even though it's not run on video.
#
#   Everything else beyond this point is the same and works nicely as of today!
mv src/* . && rm -rf src

ls -la
chmod +x *.sh
chmod +x lets-encrypt/usr/local/bin/*



*******************************************************************************
4. Installing and Configuring nginx for A+ SSL Ratings
*******************************************************************************

-------------------------------------------------------------------------------
Getting nginx Installed and Confirm It Works
-------------------------------------------------------------------------------

ssh root@YOUR_SERVER_IP
./01-install-nginx.sh


-------------------------------------------------------------------------------
Creating a Custom Website
-------------------------------------------------------------------------------

ssh root@YOUR_SERVER_IP
./02-demo-website.sh

- Check out a few Pixel Cave themes:
    http://bit.ly/pixelcave


-------------------------------------------------------------------------------
Configuring nginx to Serve a Website
-------------------------------------------------------------------------------

- Official nginx documentation:
    https://nginx.org/en/docs/


-------------------------------------------------------------------------------
Generating Self Signed SSL Certificates
-------------------------------------------------------------------------------

ssh root@YOUR_SERVER_IP
./03-self-signed-certificates.sh

- Diffie Hellman optional reading:
    https://security.stackexchange.com/a/38207

- Diffie Hellman even more optional reading:
    https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange


-------------------------------------------------------------------------------
Configuring nginx for A+ SSL Ratings
-------------------------------------------------------------------------------

ssh root@YOUR_SERVER_IP
./04-nginx-with-ssl.sh

- OSCP Stapling optional reading:
    https://www.digicert.com/enabling-ocsp-stapling.htm



*******************************************************************************
5. Associating a Domain Name to Your Server
*******************************************************************************

-------------------------------------------------------------------------------
Follow Along for Free without a Domain Name
-------------------------------------------------------------------------------

- Create fully qualified domain names for free:
    http://nip.io/


-------------------------------------------------------------------------------
A Crash Course in DNS
-------------------------------------------------------------------------------

# Look up the IP address associated to a domain:
host nickjanetakis.com
host diveintodocker.com

- Debugging DNS issues:
    https://nickjanetakis.com/blog/debugging-lets-encrypt-errors-sometimes-its-not-your-fault


-------------------------------------------------------------------------------
How to Pick a Good Domain Name Registrar
-------------------------------------------------------------------------------

- NameSilo, save 11% with coupon code "nickjj" at checkout:
    https://www.namesilo.com/?rid=4404761du

- Internet.BS, but has a worse UI and you can't edit your SPF records:
     https://internet.bs



*******************************************************************************
6. Getting Let's Encrypt Working and Automated
*******************************************************************************

-------------------------------------------------------------------------------
What Is Let's Encrypt?
-------------------------------------------------------------------------------

- Let's Encrypt website:
    https://letsencrypt.org/


-------------------------------------------------------------------------------
Moving a Few Scripts to Their Correct Paths
-------------------------------------------------------------------------------

ssh root@YOUR_SERVER_IP
./05-install-lets-encrypt.sh

- Very small and efficient open source ACME client:
    https://github.com/diafygi/acme-tiny


-------------------------------------------------------------------------------
Accepting Challenges with nginx
-------------------------------------------------------------------------------

ssh root@YOUR_SERVER_IP
./06-nginx-with-lets-encrypt-ssl.sh


-------------------------------------------------------------------------------
Going Over the Issue SSL Certificate Script
-------------------------------------------------------------------------------

# Edit and run the script to issue a Let's Encrypt SSL certificate:
nano /usr/local/bin/issue-certificate.sh
issue-certificate.sh

- Let's Encrypt rate limit details:
    https://letsencrypt.org/docs/rate-limits/

# Investigate the ACME client logs:
cat /var/log/acme-tiny/acme-tiny.log


-------------------------------------------------------------------------------
Adjusting nginx and Verifying Our Website Works
-------------------------------------------------------------------------------

ssh root@YOUR_SERVER_IP

# Make changes to the demo config and restart the process:
nano /etc/nginx/sites-available/demo.conf
service nginx restart

# Make changes to the issue certificate script and issue a new certificate:
nano /usr/local/bin/issue-certificate.sh
issue-certificate.sh

# Do the same thing again, but this time edit your demo.conf's cert names.

- Verify what your SSL rating is:
    https://www.ssllabs.com/ssltest/


-------------------------------------------------------------------------------
Automating Certificate Renewal with a Cronjob
-------------------------------------------------------------------------------

ssh root@YOUR_SERVER_IP

# Set the cronjob for the root user:
crontab -e

0 0 1 * * /usr/local/bin/issue-certificate.sh



*******************************************************************************
Applying Let's Encrypt to Other Examples
*******************************************************************************

-------------------------------------------------------------------------------
Configuring nginx to Secure Multiple Domains
-------------------------------------------------------------------------------

ssh root@YOUR_SERVER_IP
./07-nginx-multi.sh

# Make changes to the hello config:
nano /etc/nginx/sites-available/hello.conf

# Make changes to the issue certificate script and issue a new certificate:
nano /usr/local/bin/issue-certificate.sh
issue-certificate.sh


-------------------------------------------------------------------------------
Configuring nginx to Secure Any Web Application
-------------------------------------------------------------------------------

ssh root@YOUR_SERVER_IP
./08-nginx-reverse-proxy.sh

# Make changes to the demo config and restart the process:
nano /etc/nginx/sites-available/demo.conf
service nginx restart

# Kill the Python server:
ps aux | grep python
kill -9 [PID of the Python process]

# Revert to the backup if you want to:
mv /etc/nginx/sites-available/demo-original.conf \
   /etc/nginx/sites-available/demo.conf
service nginx restart


-------------------------------------------------------------------------------
Configuring Apache for A+ SSL Ratings
-------------------------------------------------------------------------------

ssh root@YOUR_SERVER_IP
./09-install-apache-with-lets-encrypt-ssl.sh

# Make changes to the demo config and restart the process:
nano /etc/apache2/sites-available/demo.conf
service apache2 restart



*******************************************************************************
Where to Go Next
*******************************************************************************

-------------------------------------------------------------------------------
Congrats on Finishing This Course
-------------------------------------------------------------------------------

- Please share:
    https://httpswithletsencrypt.com
