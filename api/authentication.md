## Authentication
To secure the API we use HTTP Basic Authentication over SSL. 
On every request you make to the API you will need to include the header 
Authorization using the Basic authentication.

    Authorization: Basic {TOKEN}
            
TOKEN (string) - string base 64 encode of the APIKEY and SECRET separated by colon.
            
    base64(APIKEY + ':' + SECRET)

Let's assume the following values
+ APIKEY =  “eb9SQx7s3KEu7Hm1qsg”
+ SECRET = “OsgabyCUtq2JYMIZaog”

You need to encode the String: “eb9SQx7s3KEu7Hm1qsg:OsgabyCUtq2JYMIZaog”

    base64(eb9SQx7s3KEu7Hm1qsg + ':' + OsgabyCUtq2JYMIZaog)
    
and the header then becomes
    
    Authorization: Basic ZWI5U1F4N3MzS0V1N0htMXFzZzpPc2dhYnlDVXRxMkpZTUlaYW9n
