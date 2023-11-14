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
