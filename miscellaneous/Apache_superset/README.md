POC for CVE submission.

Apache 3.0.1 and lower versions has Server side template injection vulnerability which works on the below API endpoint through POST based header : 

URI : /api/v1/chart/data

In data source where users can pass queries, an attacker can intercept a query and find "extras" field where they can manually alter the text for any personalized query. 
By adding a simple {{2*2}} SSTI injection in a sample text will execute the query as SAMPLE4CODE in the response body.
