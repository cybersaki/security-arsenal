## Enumerating RESTful APIs:
fuzzdb /discovery/common-methods/common-methods.txt

RESTful APIs can be challenging to enumerate, we need to guess the resource.

Common resource names:
- Using a list of common RESTful endpoints or a wordlist, start there

Custom resource names
- Knowing the functionality of the app curate some likely endpoints
- Does the API powers a forum? try post, reply

For both
- Send a valid RESTful endpoint to intruder
- Import your wordlist
- Replace the resource name eg /$user$/1/edit

Tools :
Akto : https://github.com/akto-api-security/akto

## Enumerating GraphQL

Follow blog : https://escape.tech/blog/graphql-security-wordlist/?utm_source=tldrsec.com&utm_medium=newsletter&utm_campaign=tl-dr-sec-196-how-secrets-leak-in-ci-cd-ai-threat-modeling-supply-chain


Tools used : 
Goctopus - [Goctopus](https://escape.tech/blog/introducing-goctopus/)
Clairvoyance - https://github.com/nikitastupin/clairvoyance?ref=escape.tech

Graphql security best practices : https://escape.tech/blog/9-graphql-security-best-practices/

Graphql security cheatsheets :
1) Escape 	  : https://escape.tech/blog/should-i-disable-introspection-in-graphql/
2) Bookhacktricks : https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/graphql
3) hdks		  : https://exploit-notes.hdks.org/exploit/web/api/graphql-pentesting/
4) apisecurity	  : https://apisecurity.io/issue-82-common-graphql-vulnerabilities-pentesting-insomnia/
5) gitbook 1	  : https://kathan19.gitbook.io/howtohunt/graphql/graphql
6) gitbook 2	  : https://0xn3va.gitbook.io/cheat-sheets/web-application/graphql-vulnerabilities

-------------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------------

## TLDR : 
GraphQL Voyager for visual representation

API Versioning :
Bugs exist in older.

If you see an endpoint like :
/api/v3/users/1/edit
Check for :
/api/v1/users/1/edit

What bugs are found in APIs ?
Information disclosure
- APIs can sometimes return more information than they need to
- Sometimes this information shouldn't be returned

Authorization issues
- APIs often sit behind some logic
- This means that although the web app might require authorisation, the API does not

Business Logic
- Sometimes APIs do not have the same validation as the client

IDORs
- API's are the great sources of IDORs
- Resources can be accessed directly via the API, even if they shouldn't 

XSS & CSRF :
- API's can be great to proving more impact with bugs like XSS
- By finding CSRF bugs (where an api does not as for some kind of random token)
- And using these to impact a users account via the API

Testing for Information disclosure :
- For information disclosure we are looking for an API that returns back too much info
  - Sometimes an API might return a ton of info but never display it
  - Or only display some of it

How to test ?
- Simply call the API
- Sometimes you may need to find hidden endpoints by enumerating the API
- Make a judgement call
  - Is it a security risk?
  - Could it be used to help a larger attack?
  - Does the target really want this public ?
  - Does it return a large amount of data ?

Testing for Authorisation Issues:
- Authorisation issues are difficult to test for unless you know what you're looking for
- Our aim: Use the API to bypass an authorization process

How to test?
- First understand the authorisation process:
   What API is being called ? What tokens are generated ? What tokens do you need? What endpoints need them?
- Can use the API to generate tokens without the need to login ?
- Can you use another API endpoint to access functionality without needing a token ?


Testing or Business Logic Error 
- Business logic errors are security issues that abuse the normal functionality of an API to cause security risks
- Very common in APIs as often the validation is only client side

- How to test for them ?
  - If you see a number, make it really large
  - If you see a number, make it negative
  - Try to skip over steps, do you really need to pay? Will the API let you move on without paying ?

IDORS:
- IDORs are by far the easiest and most common bugs in APIs
- See InsiderPHD video for more information

How to test for them ?
- Use Burp Repeater to repeat each request to each endpoint
- Remove your cookies, does it still work ?
- Replace your cookies with a lower permission user account, does it still work?

Testing For XSS & CSRF :
- A common defence against XSS attacks is filtering.
  However these can only be implement on the client side.

- How to test for XSS?
  Find a request to the API which takes some input and reflects it.
  Send the XSS payload to the API

- CSRF is also common in APIs, this allows an attacker to perform actions to a victims account

How to test for CSRF ?
- Find an API endpoint which does not have or does not need a CSRF token

Other bugs to keep an eye out for :
APIs aren't just vulnerable to these bugs, keep an eye out for 
- SQL injection 
   Since APIs often access the database sometimes you can find a nice SQL Injection

- Race conditions:
   They can be fiddly to get setup but APIs can be excellent sources of Race conditions

- Memory leaks
   Sometimes you can use special characters like null bytes to get an API to spit out memory

Taking notes: 
- When dealing with APIs note taking is extremely important
- Often you have a large amount of endpoints that must be tested one after another
- Consider a spreadsheet
	- Especially if you are testing a large application for IDORs

Get comforable with APIs:
- CTFs are great to get used to building up your familiarity with APIs
- For GraphQL I recommend the Hacker101 GraphQL levels
	They helped me get my first GraphQL bug!
- For RESTful APIs I highly recommend sitting and building an application
	Not a CTF but it will achieve the same goal.

Learning more about API bug hunting:
- A great source of learning more or even just inspiration when you're stuck is areading
- I highly recommend going to Hackerone's hacktivity and searching for API
- You can also find writeups by looking for medium articles using google.
  - API write up, API [bug classs] write up

Start Hacking!
- I suggest starting out with a mobile application or large web app
- Enumerate the API as basic recon
  - Does it have a developer docs ?

- Start by testing for IDORs specifically
  - This is easier and will help you become more familiar with APIs and how to test them

- While you test keep an eye out for information disclosure
  - Keep asking yourself if what an API returns seems appropriate for the function

- Sometimes normal functionality is a bug


-----------------------------------------------------------------------------------------------------------------------------------------------

### API enumeration

We want to primarily discover new URLs given a certain pattern
We kno wthat /api/v1/users/1 exists, we want to know whether /api/v1/posts/exists
We also want to know what parameters API endpoints might take

Automated
- For this we want to use tools that allow us to brute force a ton of URLs and sort through them
- We also need a list of words to try to bruteforce
- We might want to look at tools designed to find parameters too
- We're going to look at Burp intruder + ffuf and arjun

Manual testing
- For manual testing we want to look and see what the website makes public
- Documentation such as : Developer docs or API specific guides are sometimes provided
- We can also just poke at an application
- We are going to focus on documentation

Specific tools
Burp intruder
Ffuf
Arjun

What's our destination ?
We want our destination to really be URLs we can load up into burp and test for other bugs
But we only want the valid URLs
Out goal is a list
It might also be good to see what each valid endpoint returns so we can check for sensitive data exposure

Okay so what happends after I get the URLs
- Then you hack, knowing you've got a good chunk of the API in burp
- Check out katie's video on the top 10 API bugs for specific
- Keep an eye out for : 
	- Any endpoint returning a lot of info
	- IDs
	- Any signs of internal APIs
	- Reflected input

How to tailor
- Hit enought of the app manually to gather some API endponits
- Figure out which one is the resource name - What changes?
	- This is what we're going to enumerate
- What endpoints exists for that resource + what do they do
	- You should know the main functionality
	- This is what we're going to hack

This is an important first step :
- Without this step we don't have anything to hack
- We can't ust assume what to recon we need to know for sure
- Or there's no point doing it
- We also need to know how the API works
	- What do the endpoints for ach resource do ?
	- What impact does our bug have ?

Easy API recon :
Read the docs

The easiest way to get information out of an API? Just ask
- APIs are sometimes designed to be used by external developers
- So some targets will proudly tell you exactly what you need
- Including :
	- Every endpoint
	- The parameters you need
	- An example request
	- And an example response

## Swagger:
- Swagger is a visualisation tool for APIs
- It's usually accidentally left in when developers make APIs
	- Not always though!
- It's a form of documentation
- Be on the look out for Swagger or sometimes:/__Swagger__/

So how do you hack it
- Once you read the docs test every API endpoint
- With this technique (similar to the others we are looking for the top 10 bugs)
- Hit the end points , see what the responses are 
- You can also test for additional parameters not mentioned in the docs.

Creating and Finding Wordlists:
Good API wordlists, the nahamsec method and more.

What's a wordlist?
- A list of words
- As we'll see there are different types of wordlists
	- But we're focused specifically on APIs not directories or subdomains etc..
- These are our other API resources
	- Eg if we know an endpoint is /api/v1/posts our wordlist might contain users, replies etc
- You should be making your own
	- API endpoints can be specific to a target

When do we use wordlists
- 2 times in API recon specifically
- When we want to enumerate resources to find new endpoints
- When we want to enumerate paraneters to find additional functionality
- Often these require 2 different wordlists!

Public wordlists:
seclists
fuzzdb
secclists raft words

Creating custom wordlists:
- API endpoints can be specific to a target
- Especially as often we are enumerating resource names
- Some might be common eg user/users
- But some are going to be specific to your target
- So let's talk about how to make them

Manual wordlist creation
- Informed might be a better term
- Similar services might have documented APIs - check for those
- Or use waybackmachine method on those APIs
- Make sure you've tried good ol'press all the buttons
- Then start writing sensible words
	- What does the app say ?
	- What actions does the app let you do ?

Enumerating APIs :
Using burp intruder:

Wordlist : seclists : actions-lowercase.txt

In intruder, use it in /api/$users$/6

Also in the /api/users/$6$ for checking more users 

Intro to Ffuf :
- Ffuf is a tool written in go that you can use to enumerate endpoints
- It's quicker than burp intruder
- Not that much more difficult to use!

Command:
ffuf -w wordlist.txt -u url:8000/api/FUZZ/6 -o output.txt -x http://127.0.0.1:8080

Finding additional parameters:
So we have an API endpoint
- /api/v1/users
- But we don't know what we can do with it...
	- We want to find out what parameters it can take, so we can decide to hack it

- To find out we can use a tool called Arjun

Commands:
python arjun.py -u url:8000/api/users --post -o data/result.json -x http://127.0.0.1:8080

(-x will only work if you use insiderphd arjun version : https://github.com/Insiderphd/arjun)

Arjun advantages & disadvantages:
- We can send multiple urls from our recon process and just let it run
- We can save the results to a file and come back to it
- It's quick!

- We can't use other HTTP methods eg PUT/DELETE
- But sometimes we get lucky and find_method

Analysing the results:
- Once we have the results our next step is to figure out if these endpints might be vulnerable
- Remember : the main bug we are looking for is mass assignment, where parameters are assigned even though they shouldn't be
- We should also keep an eye out for ways to increase bug severity
	- Eg turning IDORs -> Account takeovers

-------------------------------------------------------------------------------------------------------------------------------

Top 10 API bugs :

OWASP API Security project

API1 2019 : Broken Object level authorization
API2 2019 : Broken User authenticaiton
API3 2019 : Excessive Data Exposure
API4 2019 : Lack of Resources & Rate limiting
API5 2019 : Broken Function Level Authorization
API6 2019 : Mass Assignment
API7 2019 : Security Misconfiguration
API8 2019 : Injection
API9 2019 : Improper Assets Management
API10 2019 : Insufficient Logging & monitoring

This list is good for bug bounty hunters
- But this is in the language of the defenders
- Some of these might be security risks for a business but not award a bounty or not be testable
- So what are these bugs, in our language?
- How can we find and test for these bugs ?

API1 : Broken Object level Authorization

What does it mean ? - IDOR
That an API isn't checking that you own a resource before you do something to it

What are the signs of this bug ?
You should be keeping an eye out for any ID - numeric or UUID
These are particularly common in RESTful APIs

How can I find it ?
When yo useen an endpoint with an ID, try removing all the cookies and seeing if the endpoint still works.
If it doesn't try replacing the cookies with those of another user and seeing if it works or replacing the ID with something owned by another user.

API2:2019 Broken User Authentication

What does it mean ? 
That an API should have some form of authentication but it doesn't 
That an API can easily generate authentication for other users

What are the signs of this bug ?
API keys found via google dorking
APIs which generate or use some form of token, without securing it

How can I find it?
Google dorking to find API keys(Github is great)
Recon to find internal APIs which might take certain keys - this is great to then test internal APIs and find even more bugs

Always test login systems, especially for password reset + token generation

API3:2019 Excessive Data Exposure :

What does it mean?
An API returns too much information, when it doesn't need to and that info can be a security risk - eg contains PII

What are the signs of this bug ?
Always keep an eye out for any endpoint that reutnrs a ton of stuff
Not always a bug, but always worth investigating

How can I find it ?
API enumeration or press buttons to find API endpoints that you wouldn't normally hit
If you see a lot of information being returned, ask yourself if that is a risk.

API4: 2019 Lack of Resources & Rate limiting

What does it mean ?
You can DOS an API because it doesn't limit the amount of requests you can do
More interestingly you can bruteforce information from an API

What are the signs of this bugs ?
It can be difficult to spot - so just be aware that if an API doesn't return an error it might be vulnerable

How can I find it ?
The best way to find a bug that will pay out : Use it in another attack
For example if an API requires an ID, lack of rate limiting will allow you to just keep testing an endpoint until on works.

API5:2019 Broken Function Level Authorization

What does it mean?
Permission IDOR. You can do an admin action, even though you're regular level user

What are the signs of this bug ?
Any complex permission hierarchy
Plus IDs

How can I find it ?
You can use containers in Firefox (see video) to login to an admin account and user account at the same time
Do admin actions, repeating each request and change the cookies to the regular users
Autorize can do this automatically

API6:2019 Mass Assignment

What does it mean? - ( This is a pure recon bug )
That an API will aceppt additional parameters unintentionally and change them along side the intentional ones
So image an API endpoint that changes your username, if you gave it the right parameters it might also change other properties eg your password

What are the signs of this bug ?
They are more common when an API is built on a framework - often the default setting
Another clue might be additonal parameters with some default values

How can I find it ?
Pure recon, you should use tools to discover additional parameters in requests and see if anything changes.
Eg: For a user try to change the username and check for additional parameters such as email or password - you may be able to bypass checks eg confirm password

API7:2019 Security Misconfiguration

What does it mean?
Whoever is managing the API made some mistakes in configuring the server
- eg by not updating it

What are the signs of this bug ?
Many different bugs with many different signs
But likely to be more common with smaller teams

How can I find it 
Learn the common misconfigurations bugs and how to exploit them
( See description for examples)
CORS + CSRF = nice attack

API8:2019 Injection

What does it mean ?
That injeciton payloads eg SQL injection are not being sanitised by the API
So you can run potentially dangerous payloads via the API

What are the signs of this bug ?
Common on internal or not-for-public-use APIs where bugs might have been left in older applications.

How can I find it ?
Decide on the type of injection eg NoSql, and look at potential payloads - I like PayloadsAllTheThings

API9:2019 Improper Assets Management

What does it mean?
That even though v1 of the API is broken and full of bugs, it's still up and connected to the same data source as v2

What are the signs of this bug ?
Anwhere you see versioning, you should check for higher/ lower versions
/api/v4.5/user

How can I find it ?
Relies on a little bit of recon to find
But once you do find it you should use it to find more bugs or bypass fixes.

API10:2019 Insufficient logging & monitoring
What does it mean?
For the defending team : They need to know what parts of their API are being exploited and how 
If they don't have logs, it's hard to stop the bad bugs

What are the signs of this bug ?
This isn't really a bug bounty bug, it's more of an issue on their side
One sign might be utter confusion when you send in a report because they had no idea that an API existed

How can I find it?
Not really a bug to report - but something to be aware of when you report
It can help up the impact if you could do something super damaging without them noticing

In summary :
- You should be vaguely familiar with them all, but I recommend reading on the bugs you're most interested in 
- Some recommendations if you want to start hunting this weekend
	- Great for beginners :
		- IDORs, information exposure, improper asset management.
	- Great if you like recon:
		- Mass assignment, information exposure, improper asset management
	- Dont bother looking :
		- Injection, insufficient logging


----------------------------------------------------------------------------------------------------------------------------

Finding your first bug : Manual IDOR Hunting

Case study 1 : Deleting other people's tasks
- User account A to create a task, 
- Login with user B, Create a task.
- Now delete user B task, 
- Intercept and change UUID to User A
- Anyone can now delete other user's tasks

Case study 2 : IDOR bug to see hidden slowvote of any user even when you don't have access right
- Create 2 accounts : admin, guest
- Use the admin account to create a poll
- Change the visibility settings so people cannot see the poll
- The guest account can still view the poll id via a request
- Impact: Users can see hidden polls

https://hackerone.com/reports/661978

Case study : Bypass of three other reports - Ability to see fullname associated with other New Relic accounts

- The app has 2 internal APIs with different versions
- Find an endpoint which exists on an earlier API v1
- Use your knowledge of the newer endpoint, on the other domain to poke the older endpoint
- Find a bunch of IDORs
- Impact: From knowing a sequential account id, a malicious user can use the API to view private info about an account

https://hackerone.com/reports/320173
https://www.jonbottarini.com/2018/01/02/abusing-internal-api-to-achieve-idor-in-new-relic/


Case study - Shopify - Replace other user files in inbox messages

- Users can message a store and a store can reply add attach a file
- No protection on overwriting files though
- A second store can upload a document with the same name
- Impact: An attacker could overwrite a bunch of files for a store, assuming that they can guess the names

https://hackerone.com/reports/322661

Case study - Low privileged user able to add new 
Geographical settings to the Admin account

- Create a regular user account with no special permissions
- Poke at the API and find the setting modification endpoints
- Notice that you cannot view/update geo settings
- However, you can make them via this API
- Impact:Low level users can make settings changes to a higher level user

https://hackerone.com/reports/420130

Bonus: IDOR + Business Logic
Case study: Validation message in Bounty award endponit can be used to determine program balances

- Interesting mix of an IDOR and Business Logic
- Hackerone programs have a budget - what they pay you with
- IDOR: ID is not being checked, the endpoint will work on another report that is not being controlled by that program
- Business logic: Realising that this can be used to check the balance of the program

https://hackerone.com/reports/293299

Bonus : IDOR
Case study: IDOR to add secondary users in www.paypal.com/businessmanage/users/api/v1/users

- Not a lot of info but worth highlighting
- $10,500 for this bug which demonstrated big impact
- Impact: A malicious user can add their email to a business account and manage funds eg transfer money out of a business

https://hackerone.com/reports/415081

Tips:
- APIs can be great to find IDORs
	- It never hurts to do some recon on the API endpoints
- Sometimes validation imght only be client side, so worth it to do everything in burp
- IDORs can appear in so many different kinds of web apps
- The impact of an IDOR can range from low to critical
- Make sure you consider non-logged in users AND low-permission users



Practical : Using burp to find IDORs manually

---------------------------------------------------------------------------------------------------------------------------------------

How to use Firefox containers for easy IDOR hunting :

Install firefox containers in firefox
Create another container for another user

















