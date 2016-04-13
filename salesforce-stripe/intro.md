# Salesforce and Stripe connection

MinnPost uses Stripe for payment processing, and uses Salesforce to store records for each donation, and a Python/Flask app to tie it together. We've worked with partner orgs to build this stuff.

## Salesforce

On top of this, we use the Nonprofit Starter Pack's Recurring Donations functionality to send recurring donations to Stripe for processing (monthly, yearly, etc.). Each donation is an Oppportunity attached to a Contact, and it has a status of Pending or Closed Won (or Failed) that indicates whether Stripe has been charged or not.

There are also fields to indicate whether users have received a receipt, and there are workflow rules to update those fields and send the receipts.

Inside Salesforce, there's a Site that runs to handle requests from Stripe. It is a managed package.

## Stripe

When users fill out a form, we first create (or update, if it exists based on email address or customer ID) a Customer in Stripe, and attach their credit card to it.

When an Opportunity is passed to Stripe, it uses this information to create a Payment.

## Python / Flask

Tying all this together, we have a Python/Flask app that users interact with. It is hosted on Heroku, with a celery worker, and a PostgreSQL and Redis database (these two don't do a whole lot, but they do run), and Papertrail and Opeat for logging.

This app handles creating or updating the Stripe Customer and the Salesforce Contact, and then it passes information to Salesforce to create Opportunities or Recurring Donations.

Celery runs at intervals to find out if there are Pending Opportunities (regardless of whether they're attached to Recurring Donations) that need to be charged. When there are, it passes that information to Stripe.

## Heroku

Currently the only thing we pay Heroku for is SSL, so our support.minnpost.com subdomain works successfully (we use the wildcad SSL from our site host).

From time to time we see inexplicable errors, ranging in severity from the site being down temporarily to duplicate charges on users for no reason. These can be fixed (as long as there's not a code error) by running `heroku restart`. This is problematic. Heroku suggests that we could get around this by paying for their stuff.

Heroku offers paid tiers on their hosting itself, as well as on the PostgreSQL and Redis products. If we ventured into paid products there, we're looking at these minimums:

1. Dynos (the hosting) - $7 per month
2. PostgreSQL - $9 per month (it's possible we would still have reliability issues here as it's still a "hobby" account until you get to $50 per month).
3. Redis - $15 per month
4. Plus the $20 per month we pay for the ability to use our own SSL certificate.

We've also gotten a recommendation to store all of our Papertrail logs at Amazon S3, which in itself would cost additional money. S3's pricing is a mystery to me, so it's unclear how much we'd be looking at for this. The free logs are unsearchable after 72 hours (though they are maintained for a week).

## Issues

There are a few issues with this entire model, and it's unclear thus far if it is sustainable.

1. **The random errors with Heroku.** As far as we know these don't happen often; we think we get pretty good error notifications from Papertrail (or Opbeat, though Opbeat rarely has errors). However it's sketchy because it does happen, and there seems to be no reason for it other than that we are using free tiers.
2. **The possible amount of money** that would be required to get off those free tiers. If we started to pay for each product at a minimum, we'd be looking at $51 per month, plus the fees Stripe charges, the hosting we already pay for, etc. We don't have enough donate traffic to make this necessary, but the errors may end up requiring it.
3. **Points of failure.** We have no good way of handling for failures on the third party services we're using. If the Salesforce API fails for any reason - a field or record we're using was deleted, for example - the Contact or Opportunity could fail to be created, or they could fail to be sent to Stripe. Some of this stuff happens asynchronously, so users would have no idea there was a problem. The Stripe API is much more helpful, at least in the way this app is architected, and we can show its error messages (card declined, missing information, etc.) to users almost every time. This doesn't even consider the possible failure of PostgreSQL or Redis, locally.
4. **Expandibility / scalability.** We can expand this indefinitely, in theory, but it all requires lots of code. We have to create new pieces of Flask (templates, .py files, etc.), but often tie into the same functions other pieces are using, creating a lot of ties together that have to be tested each time. Also, it's not usable for non-tech folks, beyond the .env files (which are theoretically editable by anyone, but only in Heroku) and the URL query strings (which we have to account for in advance, creating more code).
5. **Integration with CMS.** All this has to run separately from whatever CMS. This makes it harder for other folks to know what's happening, but also causes potential difficulties for users. For example, a user currently can't update their credit card on file. This would be relatively easy from a data standpoint (load the Customer ID from Stripe, add a new Card, done) but is relatively hard from an authorization standpoint (if the user logs into the CMS, they have not logged into the Python app). It's possible we could resolve this by making tokens and such, but this is a big ball of wax to solve, and is maddening when we consider that the CMS already knows who users are, including their Salesforce and Stripe Contact and Customer IDs.