WSO2 INSTALLATION STEPS:
------------------------------------------
-> WSO2 Identity Server requires an Oracle JDK 11 or JDK 8 compliant JDK.

1) Download the wso2 identity server 5.11 from below link:
   https://is.docs.wso2.com/en/5.11.0/setup/installing-on-linux-or-os-x/
   
2) Extract the archive file to a dedicated directory for the Identity Server
3) Setting up JAVA_HOME   
  
 On Linux:
export JAVA_HOME=/usr/java/jdk1.8.0_141
export PATH=${JAVA_HOME}/bin:${PATH}

Save the file.

4) To verify that the JAVA_HOME variable is set correctly, execute the following command:

 On Linux:
echo $JAVA_HOME

5) Go into the bin folder and start the server by using this cmd:
 eraboinanaveen@kubernetes-worker:~/Downloads/wso2is-linux-installer-x64-5.10.0/data/usr/lib/wso2/wso2is/5.10.0/bin$ sh wso2server.sh

6) after successfully running the server,below you will get the some links, click on this url https://localhost:9443/carbon/ 

 you will get the management console window, login management console using "admin" as username and password.

Alternative steps:
------------------

Install the package:
--------------------
sudo apt-get update
sudo apt-get install wso2is-5.11.0


For Ubuntu operating system, WSO2 Identity Server product distribution will install to(CARBON_HOME)
---------------------------------------------------------------------------------------------------
/usr/lib/wso2/wso2is/5.11.0/

start the server:
-----------------
eraboinanaveen@kubernetes-worker:~/Downloads/wso2is-linux-installer-x64-5.10.0/data/usr/lib/wso2/wso2is/5.10.0/bin$ sh wso2server.sh


To uninstall the product, run following command
-----------------------------------------------
sudo apt purge wso2is-6.0.0

-> if you have got the ssl certificate issues, solve those issues by using below steps:

Creating a new keystore:
------------------------
step 1: keytool -genkey -alias newcerts -keyalg RSA -keysize 2048 -keystore newkeystore.jks -dname "CN=localhost, OU=Home,O=Home,L=SL,S=WS,C=LK" -storepass mypassword -keypass mypassword 

step 2: keytool -export -alias newcerts -keystore newkeystore.jks -file cert.pem

step 3: keytool -import -alias newcerts -file cert.pem -keystore client-truststore.jks -storepass wso2carbon

step 4: keytool -import -file wso2am-4.1.0/repository/resources/security/cert.pem -alias newcerts -keystore /usr/lib/jvm/java-11-openjdk-amd64/lib/security/cacerts

-> you can create the users, roles and assign roles to users using management console.

-> Here, i have created users, roles and assign roles to users using SCIM api's. 

-> Rest Template is used to create applications that consume RESTful Web Services. You can use the exchange() method to consume the web services for all HTTP methods.


User API:
-------------
Follow this documenation for Wso2 SCIM apis for Users,Roles,Groups:
-------------------------------------------------------------------
https://is.docs.wso2.com/en/5.11.0/develop/scim2-rest-apis/


Note: you can also create roles by using Groups Api.

In the Intellij IDE:
--------------------
1) i have created spring boot rest template apis for creating users, retrieving users, updating users and deleting users,and integrating wso2 with spring boot by calling the wso2 SCIM User api "https://localhost:9443/t/carbon.super/scim2/Users".
 
2) provide the authorization as admin as username and password in the spring boot application.

 Note: if you wont provide the authorization in spring boot, you will get the 401[no-body] unauthorization error.


Ex:

    private static String getBasicAuthHeader() {
        String credentials = "admin:admin";
        return new String(Base64.encodeBase64(credentials.getBytes()));
    }


 Ex: 
 
    @PostMapping("/user")
    private String CreateUser(@RequestBody Userbody userbody) {
    
        HttpHeaders headers = new HttpHeaders();
        headers.add("Authorization", "Basic " + getBasicAuthHeader());
        headers.setAccept(Arrays.asList(MediaType.APPLICATION_JSON));
       HttpEntity<Userbody> entity = new HttpEntity<Userbody>(userbody, headers);

       System.out.println("create user");
        return restTemplate.exchange("https://localhost:9443/t/carbon.super/scim2/Users",
                HttpMethod.POST,entity, String.class).getBody();


    }
 

3)Use this spring boot uRI in postman "http://localhost:8080/hono/user" , provide the user payload in Postman.
 
  Note: check the user payload in SCIM user api documentation and create the same wso2 SCIM payload in spring boot because SCIM follows wso2 payload structure. 

 you can provide the authorization as "admin" as username and password in Authorization section, and give "admin" as username and password
in Basic auth section. 

 (or)
 
you can convert "admin" as username and password in Base64Encoder converter or use the SCIM User api doc, you can give authorization in header section also.   

Groups API:
-----------
-> Group API is used for creating roles and assigning roles to particular users.


In the Intellij IDE:
--------------------
1) create spring boot rest template apis for creating roles, retrieving roles, updating roles and deleting roles,and integrating wso2 with spring boot by calling the wso2 SCIM Group api "https://localhost:9443/t/carbon.super/scim2/Groups".
 
2) provide the authorization as admin as username and password in the spring boot application.

 Note: if you wont provide the authorization in spring boot, you will get the 401[no-body] unauthorization error.

 Ex:
 
    private static String getBasicAuthHeader() {
        String credentials = "admin:admin";
        return new String(Base64.encodeBase64(credentials.getBytes()));
    }


 Ex: 
 
     @PostMapping("/group")
    private String CreateGole(@RequestBody Groups groups) {
        HttpHeaders headers = new HttpHeaders();
        headers.add("Authorization", "Basic " + getBasicAuthHeader());
        headers.setAccept(Arrays.asList(MediaType.APPLICATION_JSON));
        HttpEntity<Groups> entity = new HttpEntity<Groups>(groups, headers);

        return restTemplate.exchange("https://localhost:9443/t/carbon.super/scim2/Groups",
                HttpMethod.POST,entity, String.class).getBody();

    }
3)Use this spring boot uRI in postman "http://localhost:8080/hono/group" , provide the group payload in Postman.
 
  Note: check the group payload in SCIM group api documentation and create the same wso2 SCIM payload in spring boot because SCIM follows wso2 payload structure. 

 you can provide the authorization as "admin" as username and password in Authorization section, and give "admin" as username and password
in the Basic auth section. 

 (or)
 
you can convert "admin" as username and password in Base64Encoder converter or use the SCIM User api doc, you can give authorization in the header section also.   



Roles API:
----------
-> Role api is used for creating roles and assigning permissions to particular users.


In the Intellij IDE:
--------------------
1) create spring boot rest template apis for creating roles, retrieving roles, updating roles and deleting roles,and integrating wso2 with spring boot by calling the wso2 SCIM Role api "https://localhost:9443/t/carbon.super/scim2/Groups".
 
2) provide the authorization as admin username and password in the spring boot application.

 Note: if you won't provide the authorization in spring boot, you will get the 401[no-body] unauthorization error.

 Ex:
 
    private static String getBasicAuthHeader() {
        String credentials = "admin:admin";
        return new String(Base64.encodeBase64(credentials.getBytes()));
    }


 Ex: 
 
     @PostMapping("/role")
    private String CreateGole(@RequestBody Groups groups) {
        HttpHeaders headers = new HttpHeaders();
        headers.add("Authorization", "Basic " + getBasicAuthHeader());
        headers.setAccept(Arrays.asList(MediaType.APPLICATION_JSON));
        HttpEntity<Groups> entity = new HttpEntity<Groups>(groups, headers);

        return restTemplate.exchange("https://localhost:9443/t/carbon.super/scim2/Roles",
                HttpMethod.POST,entity, String.class).getBody();

    }
    

3)Use this spring boot uRI in postman "http://localhost:8080/hono/role" , provide the role payload in Postman.
 
  Note: check the group payload in SCIM group api documentation and create the same wso2 SCIM payload in spring boot because SCIM follows wso2 payload structure. 

 you can provide the authorization as "admin" as username and password in Authorization section, and give "admin" as username and password
in the Basic auth section. 

 (or)
 
you can convert "admin" as username and password in Base64Encoder converter or use the SCIM User api doc, you can give authorization in the header section also.   

Tenant API:
-----------
Tenant api is used for creating tenants in the management console.


Multi-Tenancy:
--------------
The goal of multi-tenancy is to maximize resource sharing by allowing multiple users (tenants) to log in and use a single server/cluster at the same time.

Here, I have created two tenants "PickupEats" and "PickupTaxis".

-> I have created users, roles for each tenant and assigned roles to particular users.


Follow this documentation for Wso2 Tenant apis:
-----------------------------------------------
https://is.docs.wso2.com/en/5.11.0/develop/tenant-management-rest-api/



creating users for admin:
-------------------------
Url: "https://localhost:9443/t/carbon.super/scim2/Users"
 
 tenant-domain: carbon.super


In the Intellij IDE:
--------------------
1) I have created spring boot rest template apis for creating tenants, retrieving tenants, and deleting tenants,and integrating wso2 with spring boot by calling the wso2 Tenant api.

"https://localhost:9443/api/server/v1/tenants".
 
2) provide the authorization as admin username and password in the spring boot application.

 Note: if you won't provide the authorization in spring boot, you will get the 401[no-body] unauthorization error.

 Ex:
 
    private static String getBasicAuthHeader() {
        String credentials = "admin:admin";
        return new String(Base64.encodeBase64(credentials.getBytes()));
    }


 -> Here, i'm creating tenant "PickupEats"
    
 Ex: 
 
     @PostMapping("/tenant")
    private String CreateTenant(@RequestBody Tenant tenant) {
        HttpHeaders headers = new HttpHeaders();
        headers.add("Authorization", "Basic " + getBasicAuthHeader());
      //  headers.setAccept(Arrays.asList(MediaType.APPLICATION_JSON));
        HttpEntity<Tenant> entity = new HttpEntity<Tenant>(tenant, headers);
        System.out.println(tenant);
      ResponseEntity<Tenant> tenant1  = restTemplate.exchange("https://localhost:9443/api/server/v1/tenants",
                HttpMethod.POST,entity, Tenant.class);
        System.out.println(tenant1.getStatusCode());
      return null;
    }
 

3)Use this spring boot uRI in postman "http://localhost:8080/hono/tenant" , provide the tenant payload in Postman.

 Ex: 
 
     {
    "domain": "Pickup-eats.com",
    "owners": [
        {
            "username": "naveen",
            "password": "naveen123",
            "email": "naveen@gmail.com",
            "firstname": "naveen",
            "lastname": "kumar",
            "provisioningMethod": "inline-password"
        }
    ]
}
 
  Note: check the tenant payload in wso2 tenant api documentation and create the same wso2 tenant payload in spring boot because SCIM follows wso2 payload structure. 

 you can provide the authorization as "admin" as username and password in Authorization section, and give "admin" as username and password
in the Basic auth section. 

 (or)
 
you can convert "admin" as username and password in Base64Encoder converter or use the SCIM User api doc, you can give authorization in the header section also.   

creating users for Pickup-eats.com:
-----------------------------------
Url: "https://localhost:9443/t/pickup-eats.com/scim2/Users"
 
 tenant-domain: pickup-eats.com

In the Intellij IDE:
--------------------
1) i have created spring boot rest template apis for creating users, retrieving users, updating users and deleting users,and integrating wso2 with spring boot by calling the wso2 SCIM User api "https://localhost:9443/t/pickup-eats.com/scim2/Users".
 
2) provide the authorization username as "naveen@pickup-eats.com" and password as "naveen123" in the spring boot application.

 Note: if you won't provide the authorization in spring boot, you will get the 401[no-body] unauthorization error.

 Ex:
 
    private static String getBasicAuthHeaders() {
        String credentials = "naveen@pickup-eats.com:naveen123";
        return new String(Base64.encodeBase64(credentials.getBytes()));
    }

 Ex:
 
    @PostMapping("/myuser")
    private String CreateMyUsers(@RequestBody Userbody userbody) {
        HttpHeaders headers = new HttpHeaders();
        headers.add("Authorization", "Basic " + getBasicAuthHeaders());
        headers.setAccept(Arrays.asList(MediaType.APPLICATION_JSON));
        HttpEntity<Userbody> entity = new HttpEntity<Userbody>(userbody, headers);

        return restTemplate.exchange("https://localhost:9443/t/pickup-eats.com/scim2/Users",
                HttpMethod.POST,entity, String.class).getBody();


    }
 

3)Use this spring boot uRI in postman "http://localhost:8080/hono/myuser" , providing the user payload in Postman.
 
  Note: check the user payload in SCIM user api documentation and create the same wso2 SCIM payload in spring boot because SCIM follows wso2 payload structure. 

 you can provide the authorization username as "naveen@pickup-eats.com" and password as "naveen123" in Authorization section, and give username as "naveen@pickup-eats.com" and password as "naveen123" in Basic auth section. 

 (or)
 
you can convert username as "naveen@pickup-eats.com" and password as "naveen123" in Base64Encoder converter or use the SCIM User api doc, you can give authorization in the header section also.   

Local Claim:
------------
Intellij IDE:
--------------
1)create spring boot rest template apis for creating claims, retrieving claims, updating claims and deleting claims,and integrating wso2 with spring boot by calling the wso2 SCIM claims api 

"https://localhost:9443/t/carbon.super/api/server/v1/claim-dialects/local/claims".
 
2) provide the authorization as admin as username and password in the spring boot application.

 Note: if you won't provide the authorization in spring boot, you will get the 401[no-body] unauthorization error.

Ex:

    private static String getBasicAuthHeader() {
        String credentials = "admin:admin";
        return new String(Base64.encodeBase64(credentials.getBytes()));
    }
    

   @PostMapping("/claims")
    private String CreateClaims(@RequestBody Claims claims) {
        HttpHeaders headers = new HttpHeaders();
        headers.add("Authorization", "Basic " + getBasicAuthHeader());
        headers.setAccept(Arrays.asList(MediaType.APPLICATION_JSON));
        HttpEntity<Claims> entity = new HttpEntity<Claims>(claims, headers);

        return restTemplate.exchange("https://localhost:9443/t/carbon.super/api/server/v1/claim-dialects/local/claims",
                HttpMethod.POST,entity, String.class).getBody();

    }

 

3) Postman: "http://localhost:8080/hono/claims" , provide the claim payload in Postman.
 
  Note: check the claim payload in SCIM claim api documentation and create the same wso2 SCIM payload in spring boot because SCIM follows wso2 payload structure. 

 you can provide the authorization as "admin" as username and password in Authorization section, and give "admin" as username and password
in the Basic auth section. 


Claim Dialect:
--------------
Set of claims is identified as a "Dialect".

Intellij IDE:
--------------
1)create spring boot rest template apis for creating Dialect, retrieving Dialect, updating Dialect and deleting Dialect,and integrating wso2 with spring boot by calling the wso2 SCIM Dialect api "https://localhost:9443/t/carbon.super/api/server/v1/claim-dialects".
 
2) provide the authorization as admin username and password in the spring boot application.

 Note: if you won't provide the authorization in spring boot, you will get the 401[no-body] unauthorization error.

Ex:
	
    private static String getBasicAuthHeader() {
        String credentials = "admin:admin";
        return new String(Base64.encodeBase64(credentials.getBytes()));
    }

 Ex: 
	
   @PostMapping("/dialects")
    private String CreateClaimDialects(@RequestBody ClaimDialects claimDialects) {
        HttpHeaders headers = new HttpHeaders();
        headers.add("Authorization", "Basic " + getBasicAuthHeader());
        headers.setAccept(Arrays.asList(MediaType.APPLICATION_JSON));
        HttpEntity<ClaimDialects> entity = new HttpEntity<ClaimDialects>(claimDialects, headers);

        return restTemplate.exchange("https://localhost:9443/t/carbon.super/api/server/v1/claim-dialects",
                HttpMethod.POST,entity, String.class).getBody();

    }

 

3) Postman: "http://localhost:8080/hono/dialects" , provides the Dialect payload in Postman.
 
  Note: check the Dialect payload in SCIM Dialect api documentation and create the same wso2 SCIM payload in spring boot because SCIM follows wso2 payload structure. 

 you can provide the authorization as "admin" as username and password in Authorization section, and give "admin" as username and password
in the Basic auth section. 


External Claim:
---------------
Firstly, create claim Dialect(Dialect URI), create Local Claim(Claim URI) and then Select a local claim to be mapped to the external claim URI. 

Intellij IDE:
-------------
1)create spring boot rest template apis for creating External Claim, retrieving External Claim, updating External Claim and deleting External Claim,and integrating wso2 with spring boot by calling the wso2 SCIM Dialect api.

 "https://localhost:9443/t/carbon.super/api/server/v1/claim-dialects/"+id+"/claims".
 
2) provide the authorization as admin as username and password in the spring boot application.

 Note: if you won't provide the authorization in spring boot, you will get the 401[no-body] unauthorization error.

Ex:
	
    private static String getBasicAuthHeader() {
        String credentials = "admin:admin";
        return new String(Base64.encodeBase64(credentials.getBytes()));
    }


 Ex: 
	
   @PostMapping("/exclaim/{id}")
    private String CreateExternalClaims(@RequestBody ExternalClaim externalClaim,@PathVariable("id") String id) {
        HttpHeaders headers = new HttpHeaders();
        headers.add("Authorization", "Basic " + getBasicAuthHeader());
        headers.setAccept(Arrays.asList(MediaType.APPLICATION_JSON));
        HttpEntity<ExternalClaim> entity = new HttpEntity<ExternalClaim>(externalClaim, headers);

        return restTemplate.exchange("https://localhost:9443/t/carbon.super/api/server/v1/claim-dialects/"+id+"/claims",HttpMethod.POST,entity, String.class).getBody();
    }
 

3) Postman: "http://localhost:8080/hono/exclaim/aHR0cDovL3dzbzIub3JnL2NsYWltcy90ZWNo" , provide the External Claim payload in Postman.
 
  Note: check the External Claim payload in SCIM Dialect api documentation and create the same wso2 SCIM payload in spring boot because SCIM follows wso2 payload structure. 

 you can provide the authorization as "admin" as username and password in Authorization section, and give "admin" as username and password
in the Basic auth section. 

Register User:
	
This API is used to register a user anonymously.
POST	 https://localhost:9443/t/carbon.super/scim2/Me

Use below documentation to create a user:
https://is.docs.wso2.com/en/latest/apis/scim2-rest-apis/#/Me%20Endpoint/createUserMe


Headers:
Content-Type: application/json
Authorization: Basic <Base64 encoded value of <username>:<password>>

Requestbody:
	
{
   "name": {
   "givenName": "naveen",
   "familyName": "kumar"
 },
 "emails": [
               "naveen35@gmail.com"
           ],
           "phoneNumbers": [
       {
           "value": "555-555-4444",
           "type": "mobile"
       }
   ],
 "userName": "Naveenkumar",
 "password": "abc123"
 }

Authenticate User:
	
POST https://localhost:9443/api/identity/auth/v1.1/authenticate
This API is used to authenticate the user and to get a JWT that can be used to identify the user authenticated.
https://is.docs.wso2.com/en/latest/apis/use-the-authentication-rest-apis/#/Authentication/post_authenticate

Request:
	
curl -k -v -X POST -H "Authorization: Basic YWRtaW46YWRtaW4=" -H "Content-Type: application/json"
"https://localhost:9443/api/identity/auth/v1.1/authenticate"
curl -k -v -X POST -H "Content-Type: application/json" -d '{ "username": "admin","password": "admin"}'
"https://localhost:9443/api/identity/auth/v1.1/authenticate"


Import this curl command in the postman, you will get this url: 

“https://localhost:9443/api/identity/auth/v1.1/authenticate”

Request Body:
	
{
   "username": "admin",
   "password": "admin"
}
Response 
	
{"token": "eyJ4NXQiOiJObUptT0dVeE16WmxZak0yWkRSaE5UWmxZVEExWXpkaFpUUmlPV0UwTldJMk0ySm1PVGMxWkEiLCJraWQiOiJObUptT0dVeE16WmxZak0yWkRSaE5UWmxZVEExWXpkaFpUUmlPV0UwTldJMk0ySm1PVGMxWkEiLCJhbGciOiJSUzI1NiJ9.eyJhdF9oYXNoIjoiX01yYUVvZjlzdXN3aHNabC1sOXkxQSIsImFjciI6InVybjptYWNlOmluY29tbW9uOmlhcDpzaWx2ZXIiLCJzdWIiOiJhZG1pbiIsImF1ZCI6WyJmOVpRR182UFdSbTRidUZIcWkzWGw4SkZpZGNhIl0sImF6cCI6ImY5WlFHXzZQV1JtNGJ1RkhxaTNYbDhKRmlkY2EiLCJpc3MiOiJodHRwczpcL1wvbG9jYWxob3N0Ojk0NDNcL29hdXRoMlwvdG9rZW4iLCJleHAiOjE1Mjg4ODg2NDksImlhdCI6MTUyODg4NTA0OX0.EhOa4TDMroWrZfKFXq0wJU4bLSq79GvTXsZVpb3hJkEFL7OSx0YKZ6A9FhAi4TUcRRpFyti74kNGcU2DcRg_UZVQ9drq4L_YfdPBvqDUfwt8Au0Q3lRVVE-nvNzbJVa3IukxD6KSBMqynua6RtLRv5n3P6MuHy8uWDJR4KxMlDc"}

Login User:
	
POST https://localhost:9443/api/identity/auth/v1.0/login
	
Request:
	
curl -X POST \
  https://<host>:<port>/api/identity/auth/v1.0/login \
  -H 'Content-Type: application/json' \
  -H 'Accept: application/json' \
  -d '{
	"username":"admin",
	"password":"admin"
}'




Headers:

Content-Type: application/json
Authorization: Basic <Base64 encoded value of <username>:<password>>


Import this curl command in the postman, you will get this url: 

“https://localhost:9443/api/identity/auth/v1.0/login”


Request Body:
	
{
   "username": "admin",
   "password": "admin"
}
	
Response: 
	
{
    "status": "success",
    "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJhZG1pbiIsImlhdCI6MTYyMjE4NDY4MywiZXhwIjoxNjIyMTg4MjgzLCJhdXRob3JpdGllcyI6WyJST0xFX0FETUlOIl19.8Fy-lA0NU6F5U6W5U6v5U6x5U6x5U6x5U6v5U6x5U6v5U6v5U6v5U6x5U6x5U6v5U6v5U6x5U6x5U6v5U6v5U6x5U6x5U6v5U6v5U6x5U6v5U6v5U6v5U6x5U6

Logout User:
	
Use below documentation for logout user:
https://is.docs.wso2.com/en/latest/apis/session-mgt-rest-api/

Use this documentation to construct a logout URL so that an application can redirect to a particular logout page when the relying party (RP) sends an OpenID Connect (OIDC) logout request.
	
https://is.docs.wso2.com/en/latest/guides/login/oidc-logout-url-redirection/
curl -v -k --user <username>:<password> -H "Authorization: Basic <Base64 encoded value of <username>:<password>>" -H "Content-Type: application/json" -X DELETE https://<host>:<port>/scim2/Users/<user-id>/Logout

curl -X POST \
  https://localhost:9443/api/identity/auth/v1.0/logout \
  -H 'Content-Type: application/json' \
  -H 'Accept: application/json' \
  -H 'Authorization: Bearer <access_token>'
	
Note:
Replace <username>, <password>, <host>, and <port> with appropriate values.
Also replace <user-id> with the ID of the user you want to log out.
The value for the Authorization header should be the Base64 encoded value of <username>:<password>.

Headers:
Content-Type: application/json
Authorization: Basic <Base64 encoded value of <username>:<password>>

Request Body:
	
{}
Response:
	
HTTP/1.1 204 No Content


Refresh Token API:
	
https://is.docs.wso2.com/en/6.0.0/guides/access-delegation/configure-refresh-token/#!
curl -k -d "grant_type=refresh_token&refresh_token=<refresh_token>" -H "Authorization: Basic <Base64Encoded(Client_Id:Client_Secret)>" -H "Content-Type: application/x-www-form-urlencoded" https://localhost:9443/oauth2/token
	
Note:
Replace <username>, <password>, <host>, and <port> with appropriate values.
Replace <refresh_token> with a valid refresh token.
The value for the Authorization header should be the Base64 encoded value of <username>:<password>.

Headers:
	
Content-Type: application/x-www-form-urlencoded
Authorization: Basic <Base64 encoded value of <username>:<password>>

Request Body:
	
 grant_type=refresh_token &
 refresh_token=<refresh_token>  

 Response:
	
   HTTP/1.1 200 OK
Content-Type: application/json

{
   "access_token":"<access_token>",
   "refresh_token":"<refresh_token>",
   "token_type":"Bearer",
   "expires_in":3600,
   }
	
Validate the token:
Use this documentation for validating the token:

https://is.docs.wso2.com/en/latest/guides/access-delegation/invoke-oauth-introspection-endpoint/

Request:
	
curl -k -u admin:admin -H 'Content-Type: application/x-www-form-urlencoded' -X POST --data 'token=fbc4e794-23db-3394-b1e5-f2c3e511d01f' https://localhost:9443/oauth2/introspect

Headers:
	
Content-Type: application/x-www-form-urlencoded
Authorization: Basic <Base64 encoded value of <username>:<password>>

Response:
{"exp":1464161608,"username":"admin@carbon.super","active":true,"token_type":"Bearer","client_id":"rgfKVdnMQnJSSr_pKFTxj3apiwYa","iat":1464158008}

