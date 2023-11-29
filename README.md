# JWT_DEMO

JSON WEB TOKENS:

Authentication: This is the typical scenario for using JWT, once the user is logged in, each subsequent request will include the JWT, allowing the user to access routes, services, and resources that are permitted with that token. Single Sign On is a feature that widely uses JWT nowadays, because of its small overhead and its ability to be easily used among systems of different domains.


Information Exchange: JWTs are a good way of securely transmitting information between parties, because as they can be signed, for example using a public/private key pair, you can be sure that the sender is who they say they are. Additionally, as the signature is calculated using the header and the payload, you can also verify that the content hasn’t changed.


JWTs consist of three parts separated by dots (.), which are:

Header
Payload
Signature
Therefore, a JWT typically looks like the following.

xxxxx.yyyyy.zzzzz

Let’s break down the different parts.

Header
The header typically consists of two parts: the type of the token, which is JWT, and the hashing algorithm such as HMAC SHA256 or RSA.

For example:

{
  'alg': 'HS256',
  'typ': 'JWT'
}
Then, this JSON is Base64Url encoded to form the first part of the JWT.


Payload
The second part of the token is the payload, which contains the claims. Claims are statements about an entity (typically, the user) and additional metadata. There are three types of claims: reserved, public, and private claims.

Reserved claims: These are a set of predefined claims, which are not mandatory but recommended, thought to provide a set of useful, interoperable claims. Some of them are: iss (issuer), exp (expiration time), sub (subject), aud (audience), among others.
Notice that the claim names are only three characters long as JWT is meant to be compact.

Public claims: These can be defined at will by those using JWTs. But to avoid collisions they should be defined in the IANA JSON Web Token Registry or be defined as a URI that contains a collision resistant namespace.
Private claims: These are the custom claims created to share information between parties that agree on using them.
An example of payload could be:

{
  'sub': '1234567890',
  'name': 'John Doe',
  'admin': true
}
The payload is then Base64Url encoded to form the second part of the JWT.


Signature
To create the signature part you have to take the encoded header, the encoded payload, a secret, the algorithm specified in the header, and sign that.

For example if you want to use the HMAC SHA256 algorithm, the signature will be created in the following way.

HMACSHA256(
  base64UrlEncode(header) + '.' +
  base64UrlEncode(payload),
  secret)
The signature is used to verify that the sender of the JWT is who it says it is and to ensure that the message was’t changed in the way.

Putting all together
The output is three Base64 strings separated by dots that can be easily passed in HTML and HTTP environments, while being more compact compared to XML-based standards such as SAML.

The following shows a JWT that has the previous header and payload encoded and it is signed with a secret.


How JSON Web Tokens work?
In authentication, when the user successfully logs in using their credentials, a JSON Web Token will be returned. Since tokens are credentials, great care must be taken to prevent security issues. In general, you should not keep tokens longer than required.

You also should not store sensitive session data in browser storage due to lack of security.

Whenever the user wants to access a protected route, it should send the JWT, typically in the Authorization header using the Bearer schema. Therefore the content of the header should look like the following.

Authorization: Bearer <token>

This is a stateless authentication mechanism as the user state is never saved in the server memory. The server’s protected routes will check for a valid JWT in the Authorization header, and if there is, the user will be allowed. As JWTs are self-contained, all the necessary information is there, reducing the need of going back and forward to the database.

This allows to fully rely on data APIs that are stateless and even make requests to downstream services. It doesn’t matter which domains are serving your APIs, as Cross-Origin Resource Sharing (CORS) won’t be an issue as it doesn’t use cookies.


Why should you use JSON Web Tokens?
Let’s talk about the benefits of JSON Web Tokens (JWT) comparing it to Simple Web Tokens (SWT) and Security Assertion Markup Language Tokens (SAML).

As JSON is less verbose than XML, when it is encoded its size is also smaller; making JWT more compact than SAML. This makes JWT a good choice to be passed in HTML and HTTP environments.

Security-wise, SWT can only be symmetric signed by a shared secret using the HMAC algorithm. While JWT and SAML tokens can also use a public/private key pair in the form of a X.509 certificate to sign them. However, signing XML with XML Digital Signature without introducing obscure security holes is very difficult compared to the simplicity of signing JSON.

JSON parsers are common in most programming languages, because they map directly to objects, conversely XML doesn’t have a natural document-to-object mapping. This makes it easier to work with JWT than SAML assertions.

Regarding usage, JWT is used at an Internet scale. This highlights the ease of client side processing of JWTs on multiple platforms, especially, mobile.


How we use JSON Web Tokens in Auth0?
In Auth0, we issue JWTs as a result of the authentication process. When the user logs in using Auth0, a JWT is created, signed, and sent to the user. Auth0 supports signing JWT with both HMAC and RSA algorithms. This token will be then used to authenticate and authorize with APIs which will grant access to their protected routes and resources.

We also use JWTs to perform authentication and authorization in Auth0’s API v2, replacing the traditional usage of regular opaque API keys. Regarding authorization, JSON Web Tokens allow granular security, that is the ability to specify a particular set of permissions in the token, which improves debuggability.



How to create a JWT
First, you should know three important parts of a JWT:

Header
Payload
Signature
Header
The Header answers the question: How will we calculate JWT?
Now look at an example of header, it’s a JSON object like this:

{
  "typ": "JWT",
  "alg": "HS256"
}
– typ is ‘type’, indicates that Token type here is JWT.
– alg stands for ‘algorithm’ which is a hash algorithm for generating Token signature. In the code above, HS256 is HMAC-SHA256 – the algorithm which uses Secret Key.

Payload
The Payload helps us to answer: What do we want to store in JWT?
This is a payload sample:

{
  "userId": "abcd12345ghijk",
  "username": "bezkoder",
  "email": "contact@bezkoder.com",
  // standard fields
  "iss": "zKoder, author of bezkoder.com",
  "iat": 1570238918,
  "exp": 1570238992
}
In the JSON object above, we store 3 user fields: userId, username, email. You can save any field you want.

We also have some Standart Fields. They are optional.

iss (Issuer): who issues the JWT
iat (Issued at): time the JWT was issued at
exp (Expiration Time): JWT expiration time
You can see more Standard Fields at:
https://en.wikipedia.org/wiki/JSON_Web_Token#Standard_fields

Signature
This part is where we use the Hash Algorithm that I told you above.
Look at the code for getting the Signature below:

const data = Base64UrlEncode(header) + '.' + Base64UrlEncode(payload);
const hashedData = Hash(data, secret);
const signature = Base64UrlEncode(hashedData);
Let’s explain it.
– First, we encode Header and Payload, join them with a dot .

data = '[encodedHeader].[encodedPayload]'
– Next, we make a hash of the data using Hash algorithm (defined at Header) with a secret string.
– Finally, we encode the hashing result to get Signature.

Combine all things
After having Header, Payload, Signature, we’re gonna combine them into JWT standard structure: header.payload.signature.

Following code will illustrate how we do it.

const encodedHeader = base64urlEncode(header);
/* Result */
"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9"

const encodedPayload = base64urlEncode(payload);
/* Result */
"eyJ1c2VySWQiOiJhYmNkMTIzNDVnaGlqayIsInVzZXJuYW1lIjoiYmV6a29kZXIiLCJlbWFpbCI6ImNvbnRhY3RAYmV6a29kZXIuY29tIn0"

const data = encodedHeader + "." + encodedPayload;
const hashedData = Hash(data, secret);
const signature = base64urlEncode(hashedData);
/* Result */
"crrCKWNGay10ZYbzNG3e0hfLKbL7ktolT7GqjUMwi3k"

// header.payload.signature
const JWT = encodedHeader + "." + encodedPayload + "." + signature;
/* Result */
"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VySWQiOiJhYmNkMTIzNDVnaGlqayIsInVzZXJuYW1lIjoiYmV6a29kZXIiLCJlbWFpbCI6ImNvbnRhY3RAYmV6a29kZXIuY29tIn0.5IN4qmZTS3LEaXCisfJQhrSyhSPXEgM1ux-qXsGKacQ"


How JWT secures our data
JWT does NOT secure your data
JWT does not hide, obscure, secure data at all. You can see that the process of generating JWT (Header, Payload, Signature) only encode & hash data, not encrypt data.

The purpose of JWT is to prove that the data is generated by an authentic source.

So, what if there is a Man-in-the-middle attack that can get JWT, then decode user information? Yes, that is possible, so always make sure that your application has the HTTPS encryption.

How Server validates JWT from Client
In previous section, we use a Secret string to create Signature. This Secret string is unique for every Application and must be stored securely in the server side.

When receiving JWT from Client, the Server get the Signature, verify that the Signature is correctly hashed by the same algorithm and Secret string as above. If it matches the Server’s signature, the JWT is valid.

Important!
Experienced programmers can still add or edit Payload information when sending it to the server. What should we do in this case?
We store the Token before sending it to the Client. It can ensure that the JWT transmitted later by the Client is valid.

In addition, saving the user’s Token on the Server will also benefit the Force Logout feature from the system.


Standard fields
Code	          Name	                  Description
Standard        claim fields	          The internet drafts define the following standard fields ("claims") that can be used inside a JWT claim set.
iss	            Issuer	                Identifies principal that issued the JWT.
sub	            Subject	                Identifies the subject of the JWT.
aud	            Audience	              Identifies the recipients that the JWT is intended for. Each principal intended to process the JWT must identify itself with a value in the audience claim. If the principal processing the claim does not identify itself with a value in the aud claim when this claim is present, then the JWT must be rejected.
exp	            Expiration Time	        Identifies the expiration time on and after which the JWT must not be accepted for processing. 
                                        The value must be a NumericDate: either an integer or decimal, representing seconds past 1970-01-01 00:00:00Z.
nbf	            Not Before	             Identifies the time on which the JWT will start to be accepted for processing. The value must be a NumericDate.
iat	            Issued at	Identifies    the time at which the JWT was issued. The value must be a NumericDate.
jti	            JWT ID	                Case-sensitive unique identifier of the token even among different issuers.


1. JWT consists of 3 parts - Header.Payload.Signature
2. The JWT access token is only valid for a limited period of time. Using an expired JWT would cause the operation to fail. This value is normally 1200 seconds or 20 minutes.
3. JWT is essentially a token format. JWT is a token that can be used as part of the OAuth authorization protocol. Server-side and client-side storage are used in OAuth. If you want to make a proper logout, you'll need to use OAuth2. Authentication with a JWT token does not allow you to logout.
4. The JWT is signed with public/private key pairs to ensure that the sender is authenticated and that the payload has not been tampered with. The JSON Web Token, on the other hand, is in plain text.
To encrypt communication, we will require SSL/HTTPS. Attackers can intercept network traffic without SSL/HTTPS and extract the JWT, making your application vulnerable to man in the middle attacks.
5. JSON Web Tokens (JWT) are called stateless because the authorizing server doesn't need to keep track of anything, the token is all that's required to verify a token bearer's authorization. In stateless authentication, no need to store user information in the session.
6. The JWT comprises encoded user information as well as a signature that is checked when decoded to confirm that the token has not been tampered with. After the JWT has been confirmed, instead of sending the user their forgotten password, your application can safely allow them to generate a new password.
7. The AbstractSecurityInterceptor in Spring Security handles the initial authorization of an incoming request.
There are two concrete implementations of the AbstractSecurityInterceptor:
FilterSecurityInterceptor
The Spring Security filter chain's default filter. All authenticated user requests will be authorised by the FilterSecurityInterceptor.
MethodSecurityInterceptor
This is required for method level security to be implemented. It enables us to apply security to our programme at the method level.
8. @PreAuthorize annotation is used to check for authorization before executing the method.
We could alternatively use the @Secured annotation in spring to handle method-level security, however it has several limitations, such as
We cannot have several conditions with the @Secured annotation, i.e. the roles cannot be coupled with an AND/OR condition.
Spring expression language is not supported by the @Secured annotation.

