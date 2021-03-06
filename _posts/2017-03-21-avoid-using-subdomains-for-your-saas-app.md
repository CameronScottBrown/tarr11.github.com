---
layout: post
title: Avoid using subdomains for accounts in your saas app
published: true
category: startups
---

When developing your SaaS application, you will hit a decision on how to implement accounts for companies or other organizations who may be your customers.  

One key decision point that happens very early on, is to either implement accounts using a subdomain (mybiz.saasapp.com) or using only login (www.saasapp.com)  The main value of the subdomain is that it provides a special "landing page" for your company, that can be linked to from intranets or other company documents.

My recommendation is that using subdomains for accounts creates a variety of namespace issues, such as cost, development complexity, and user experience while providing little value to end users.  You are better off avoiding subdomains unless your customers specifically ask for it.  And even in that case, it could be created as a special feature that is enabled for specific users (perhaps even for a cost) as opposed to the common registration path.

## Costs: Hosting
You will need a wildcard SSL domain, which can cost $99 / year or more.  By contrast, single domain certs are now often free (via LetsEncrypt) or very cheap (<$10 / year)   If you use a cloud host like heroku, you may need to have multiple SSL endpoints which increases your cost.

## SEO: Information Leakage and Links
Using subdomains will leak some subset of your customer list.  You can easily guess a company's domain and see if someone has registered it.  Subdomains are often leaked via search engines. (search for *.slack.com on google for example)  

You will need a special "corporate" domain that contains corporate content (Blogs, FAQs, pricing and product info, etc) and ensure that it is the canonical version so you don't have duplicate content in search engines.

Users who are sharing your product may accidentally link to subdomains, which is known to reduce the SEO value of those links, and may also accidentally expose that users identity on social networks.

## UX Problems

### Choosing a subdomain
If subdomains are required, users will need to choose one during onboarding and registration.  The more fields you must choose during registration, the higher your drop-off will be in your registration funnel. 

If you write code that automatically creates a subdomain based on the account name (replacing dashes for spaces, for example), then you may end up with a long, hard to manage subdomain, if the company name is long.   You will also run through an arduous process over many months or years of whitelisting various phrases, unicode characters, etc.

### Changing a subdomain
You will need to implement permissions for your subdomain based on who can change it.  The links to the subdomain will be in places beyond your control, and therefore be unchangeable, which may cause confusion for users.  You won't always be able to do a 301 redirect, since the subdomain may be in use by another entity. 

### Forgot subdomains
Users may also forget the subdomain when logging in.  You will need to have a page on your main site that lets them find their subdomain.  This is extra work for the user and may cause them to drop off.

### Forgot password
If a user can have multiple accounts, then the forgot password page will either require the subdomain, or have to find it (via the "Forgot Subdomain" page.  The login flow gets more complex when users first have to find their subdomain.  

## Development Complexity

### Whitelisting
You will need to whitelist subdomains that are typically reserved for the company or are security concerns (api, admin, ftp, etc, login) or blacklisting profanities.

### Revoking Subdomains
Companies may complain if their subdomain has been taken by a 3rd party.  (ie, google.saasapp.com)  Though rare, you may need to be able to handle this situation, which could involve revoking someone else's account.  

### Security, Login and Cookies
If a user may associate a username or email with multiple accounts, you will require a "choose account" page eventually.  If this page is associated with a subdomain, you will need to find a way to pass credentials from one subdomain to another (usually using some sort of one-time token on a GET request) so that the appropriate cookies are set.

More information on [cross-domain login security considerations here](https://security.stackexchange.com/questions/33851/protecting-against-cross-subdomain-cookie-attacks).

### Mailers
Your mail subsystem will need to ensure that hosts are handled correctly.  For example, if you are using Rails and ActiveMailer, you will need to pass the host to each mailer.   If a user can belong to multiple accounts, you will need to change the default context of your request from a user to a "UserAccount" or similar object that contains enough information to allow you to construct URLs in your emails correctly.

### Development Servers
In development, you will need to simulate subdomains.  This requires editing your hosts files so that you can have account1.local, account2.local.  Editing a host file often requires special permissions, and can make setting up a new development instance more difficult (since it is no longer a seed file with a database entry, but also an associated hosts entry)

Have anything to add or correct for this blog entry?  Feel free to [create an issue](https://github.com/tarr11/tarr11.github.com/issues) or [submit a pull request](https://github.com/tarr11/tarr11.github.com/pulls) 
