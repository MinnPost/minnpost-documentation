# Annual SSL Renewal

We currently have a wildcard SSL certificate - that is, we can use it with any subdomain on minnpost.com. It gets renewed annually by our host, who gives us 60 days to approve its renewal and then installs it on their server.

After they install it, we have to install it on any other server space where we want to use it. Currently, this includes:

1. ~~Heroku (support.minnpost.com)~~
2. Amazon EC2 (boundaries.minnpost.com)
3. Amazon EC2 (elections-scraper.minnpost.com)

## Getting the certificate files

We don't currently have a host that will sell us an SSL certificate. We can purchase one from any number of vendors, but hopefully won't have to do this.

### If we have a zip file of a wildcard certificate

With our current host, we can submit a support ticket to request the files, or we can email them and they can upload them to a secure file site. Basically, don't do it over email.

We receive a zip file with these contents:

1. name.txt
2. name.cert.txt
3. name.key.txt

Steps to generate what we need:

1. Remove the .txt from the above files and use .crt for intermediate and the .cert
2. Make a PEM file from the key, cert, and intermediate cert in that order. Should be able to do this in a text editor.

## Heroku instructions

1. We should no longer have to do anything for this. Heroku enabled [Automated Certificate Management](https://devcenter.heroku.com/articles/automated-certificate-management) and we switched to it, and it appears to work seamlessly.

## Amazon EC2 Instructions

### If we can use Let's Encrypt script from EFF

1. `ssh -i ~/Sites/aws/minnpost-aws.pem ubuntu@whatever`
1. `sudo apt-get update`
1. `sudo apt-get install software-properties-common`
1. `sudo add-apt-repository universe`
1. `sudo add-apt-repository ppa:certbot/certbot`
1. `sudo apt-get update`
1. `sudo apt-get install certbot python-certbot-nginx`
1. Once the domain is pointing to the right instance: `sudo certbot --nginx`
1. Test automatic renewal with `sudo certbot renew --dry-run`
1. Check https://www.ssllabs.com/ssltest/.

### If we have a wildcard certificate

1. `scp -i ~/Sites/aws/minnpost-aws.pem ~/Sites/wildcard.minnpost.com/2017/intermediate.crt ubuntu@whatever:~`
2. `scp -i ~/Sites/aws/minnpost-aws.pem ~/Sites/wildcard.minnpost.com/2017/wildcard.minnpost.com.crt ubuntu@whatever:~`
3. `scp -i ~/Sites/aws/minnpost-aws.pem ~/Sites/wildcard.minnpost.com/2017/wildcard.minnpost.com.key ubuntu@whatever:~`
4. `scp -i ~/Sites/aws/minnpost-aws.pem ~/Sites/wildcard.minnpost.com/2017/wildcard.minnpost.com.pem ubuntu@whatever:~`
5. `ssh -i ~/Sites/aws/minnpost-aws.pem ubuntu@whatever`
6. Copy or move the above files from ~/ to /etc/nginx/ssl
7. Restart or reload nginx. Can reload config without restarting with `sudo service nginx reload`
