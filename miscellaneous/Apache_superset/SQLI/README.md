Apache 3.0.1 and lower versions has SQL Injection vulnerability which works on the below API endpoint through POST based header :

URI : /api/v1/chart/data?form_data

In a data source where users can pass queries, an attacker can intercept a query and find "sqlexpression" field where they can manually alter the text for any personalized query. By adding a simple apostrophe " ' "  value at the end of the query can break the sql query resulting in a sql error. 

Additionally, by passing a sleep sql command fed into the query will result in delayed response and a successful time based sql injection attack.
