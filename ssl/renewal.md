# Annual SSL Renewal

We have a wildcard SSL certificate - that is, we can use it with any subdomain on minnpost.com. It gets renewed annually by our host, who gives us 60 days to approve its renewal and then installs it on their server.

After they install it, we have to install it on any other server space where we want to use it. Currently, this includes:

1. Heroku (support.minnpost.com)
2. Amazon EC2 (boundaries.minnpost.com)

## Getting the certificate files

With our host, we can submit a support ticket to request the files, or we can email them and they can upload them to a secure file site. Basically, don't do it over email.

We receive a zip file with these contents:

1. intermediate.crt
2. wildcard.minnpost.com.crt
3. wildcard.minnpost.com.csr
4. wildcard.minnpost.com.key
5. wildcard.minnpost.com.pem

## Heroku instructions

## Amazon EC2 Instructions
