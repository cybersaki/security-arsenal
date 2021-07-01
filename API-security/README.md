# Under Construction :

---------------------------------------------------------------------------------------------------------------------------------

Enumerating RESTful APIs:
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

Enumerating GraphQL

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
