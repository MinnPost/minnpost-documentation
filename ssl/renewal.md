# Annual SSL Renewal

We have a wildcard SSL certificate - that is, we can use it with any subdomain on minnpost.com. It gets renewed annually by our host, who gives us 60 days to approve its renewal and then installs it on their server.

After they install it, we have to install it on any other server space where we want to use it. Currently, this includes:

1. Heroku (support.minnpost.com)
2. Amazon EC2 (boundaries.minnpost.com)

## Getting the certificate files

With our host, we can submit a support ticket to request the files, or we can email them and they can upload them to a secure file site. Basically, don't do it over email.

We receive a zip file with these contents:

1. name.txt
2. name.cert.txt
3. name.key.txt

Steps to generate what we need:

1. Remove the .txt from the above files and use .crt for intermediate and the .cert
2. Make a PEM file from the key, cert, and intermediate cert in that order. Should be able to do this in a text editor.

## Heroku instructions

http://ryan.mcgeary.org/2011/09/16/how-to-add-a-dnsimple-ssl-certificate-to-heroku/

1. If it's not already there, add the SSL endpoint add-on (`heroku addons:add ssl:endpoint`)
2. Upload the Cert and Private Key to Heroku (`heroku certs:add name.pem name.key`)
3. If updating an existing certificate, instead use (`heroku certs:update name.pem name.key`)
4. Don't forget to specify the app name

## Amazon EC2 Instructions

1. `scp -i ~/Sites/aws/minnpost-aws.pem ~/Sites/wildcard.minnpost.com/2017/intermediate.crt ubuntu@ec2-23-22-85-111.compute-1.amazonaws.com:~`
2. `scp -i ~/Sites/aws/minnpost-aws.pem ~/Sites/wildcard.minnpost.com/2017/wildcard.minnpost.com.crt ubuntu@ec2-23-22-85-111.compute-1.amazonaws.com:~`
3. `scp -i ~/Sites/aws/minnpost-aws.pem ~/Sites/wildcard.minnpost.com/2017/wildcard.minnpost.com.key ubuntu@ec2-23-22-85-111.compute-1.amazonaws.com:~`
4. `scp -i ~/Sites/aws/minnpost-aws.pem ~/Sites/wildcard.minnpost.com/2017/wildcard.minnpost.com.pem ubuntu@ec2-23-22-85-111.compute-1.amazonaws.com:~`
5. `ssh -i ~/Sites/aws/minnpost-aws.pem ubuntu@ec2-23-22-85-111.compute-1.amazonaws.com`
6. Copy or move the above files from ~/ to /etc/nginx/ssl
7. Restart or reload nginx. Can reload config without restarting with `sudo service nginx reload`
