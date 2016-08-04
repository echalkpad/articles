# RichardKnop/go-oauth2-server

[Original URL](https://github.com/RichardKnop/go-oauth2-server)

> Go OAuth2 Server This service implements OAuth 2.0 specification. Excerpts from the specification are included in this README file to describe different grant types. Please read the full spec for...

[![Build Status](https://camo.githubusercontent.com/90026677c99785e2b5c5d4962e79629d901a5601/68747470733a2f2f7472617669732d63692e6f72672f526963686172644b6e6f702f676f2d6f61757468322d7365727665722e7376673f6272616e63683d6d6173746572 "Build Status")](https://travis-ci.org/RichardKnop/go-oauth2-server)

## [](https://github.com/RichardKnop/go-oauth2-server#go-oauth2-server)Go OAuth2 Server

This service implements [OAuth 2.0 specification](http://tools.ietf.org/html/rfc6749#section-4.3). Excerpts from the specification are included in this README file to describe different grant types. Please read the full spec for more detailed information.

## [](https://github.com/RichardKnop/go-oauth2-server#index)Index

## [](https://github.com/RichardKnop/go-oauth2-server#api)API

## [](https://github.com/RichardKnop/go-oauth2-server#oauth-20)OAuth 2.0

### [](https://github.com/RichardKnop/go-oauth2-server#client-authentication)Client Authentication

<http://tools.ietf.org/html/rfc6749#section-3.2.1>

Clients must authenticate with client credentials (client ID and secret) when issuing requests to `/oauth/api/v1/tokens` endpoint. Basic HTTP authentication should be used.

### [](https://github.com/RichardKnop/go-oauth2-server#grant-types)Grant Types

#### [](https://github.com/RichardKnop/go-oauth2-server#authorization-code)Authorization Code

<http://tools.ietf.org/html/rfc6749#section-4.1>

The authorization code grant type is used to obtain both access tokens and refresh tokens and is optimized for confidential clients. Since this is a redirection-based flow, the client must be capable of interacting with the resource owner's user-agent (typically a web browser) and capable of receiving incoming requests (via redirection) from the authorization server.

```
+----------+
| Resource |
| Owner |
| |
+----------+
 ^
 |
 (B)
+----|-----+ Client Identifier +---------------+
| -+----(A)-- & Redirection URI ---->| |
| User- | | Authorization |
| Agent -+----(B)-- User authenticates --->| Server |
| | | |
| -+----(C)-- Authorization Code ---<| |
+-|----|---+ +---------------+
 | | ^ v
 (A) (C) | |
 | | | |
 ^ v | |
+---------+ | |
| |>---(D)-- Authorization Code ---------' |
| Client | & Redirection URI |
| | |
| |<---(E)----- Access Token -------------------'
+---------+ (w/ Optional Refresh Token)
```

The client initiates the flow by directing the resource owner's user-agent to the authorization endpoint. The client includes its client identifier, requested scope, local state, and a redirection URI to which the authorization server will send the user-agent back once access is granted (or denied).

```
http://localhost:8080/web/authorize?client_id=test_client&redirect_uri=https%3A%2F%2Fwww.example.com&response_type=code&state=somestate
```

The authorization server authenticates the resource owner (via the user-agent).

[![Log In page screenshot](https://raw.githubusercontent.com/RichardKnop/assets/master/go-oauth2-server/login_screenshot.png)](https://raw.githubusercontent.com/RichardKnop/assets/master/go-oauth2-server/login_screenshot.png)

The authorization server then establishes whether the resource owner grants or denies the client's access request.

[![Authorize page screenshot](https://raw.githubusercontent.com/RichardKnop/assets/master/go-oauth2-server/authorize_screenshot.png)](https://raw.githubusercontent.com/RichardKnop/assets/master/go-oauth2-server/authorize_screenshot.png)

If the request fails due to a missing, invalid, or mismatching redirection URI, or if the client identifier is missing or invalid, the authorization server SHOULD inform the resource owner of the error and MUST NOT automatically redirect the user-agent to the invalid redirection URI.

If the resource owner denies the access request or if the request fails for reasons other than a missing or invalid redirection URI, the authorization server informs the client by adding the error parameter to the query component of the redirection URI.

```
https://www.example.com/?error=access_denied&state=somestate
```

Assuming the resource owner grants access, the authorization server redirects the user-agent back to the client using the redirection URI provided earlier (in the request or during client registration). The redirection URI includes an authorization code and any local state provided by the client earlier.

```
https://www.example.com/?code=7afb1c55-76e4-4c76-adb7-9d657cb47a27&state=somestate
```

The client requests an access token from the authorization server's token endpoint by including the authorization code received in the previous step. When making the request, the client authenticates with the authorization server. The client includes the redirection URI used to obtain the authorization code for verification.

```
$ curl -v localhost:8080/oauth/api/v1/tokens \
 -u test_client:test_secret \
 -d "grant_type=authorization_code" \
 -d "code=7afb1c55-76e4-4c76-adb7-9d657cb47a27"
```

The authorization server authenticates the client, validates the authorization code, and ensures that the redirection URI received matches the URI used to redirect the client before. If valid, the authorization server responds back with an access token and, optionally, a refresh token.

```
{
 "id": 1,
 "access_token": "00ccd40e-72ca-4e79-a4b6-67c95e2e3f1c",
 "expires_in": 3600,
 "token_type": "Bearer",
 "scope": "foo bar",
 "refresh_token": "6fd8d272-375a-4d8a-8d0f-43367dc8b791"
}
```

#### [](https://github.com/RichardKnop/go-oauth2-server#implicit)Implicit

<http://tools.ietf.org/html/rfc6749#section-4.2>

The implicit grant type is used to obtain access tokens (it does not support the issuance of refresh tokens) and is optimized for public clients known to operate a particular redirection URI. These clients are typically implemented in a browser using a scripting language such as JavaScript.

Since this is a redirection-based flow, the client must be capable of interacting with the resource owner's user-agent (typically a web browser) and capable of receiving incoming requests (via redirection) from the authorization server.

Unlike the authorization code grant type, in which the client makes separate requests for authorization and for an access token, the client receives the access token as the result of the authorization request.

The implicit grant type does not include client authentication, and relies on the presence of the resource owner and the registration of the redirection URI. Because the access token is encoded into the redirection URI, it may be exposed to the resource owner and other applications residing on the same device.

```
+----------+
| Resource |
| Owner |
| |
+----------+
 ^
 |
 (B)
+----|-----+ Client Identifier +---------------+
| -+----(A)-- & Redirection URI --->| |
| User- | | Authorization |
| Agent -|----(B)-- User authenticates -->| Server |
| | | |
| |<---(C)--- Redirection URI ----<| |
| | with Access Token +---------------+
| | in Fragment
| | +---------------+
| |----(D)--- Redirection URI ---->| Web-Hosted |
| | without Fragment | Client |
| | | Resource |
| (F) |<---(E)------- Script ---------<| |
| | +---------------+
+-|--------+
 | |
 (A) (G) Access Token
 | |
 ^ v
+---------+
| |
| Client |
| |
+---------+
```

The client initiates the flow by directing the resource owner's user-agent to the authorization endpoint. The client includes its client identifier, requested scope, local state, and a redirection URI to which the authorization server will send the user-agent back once access is granted (or denied).

```
http://localhost:8080/web/authorize?client_id=test_client&redirect_uri=https%3A%2F%2Fwww.example.com&response_type=token&state=somestate
```

The authorization server authenticates the resource owner (via the user-agent).

[![Log In page screenshot](https://raw.githubusercontent.com/RichardKnop/assets/master/go-oauth2-server/login_screenshot.png)](https://raw.githubusercontent.com/RichardKnop/assets/master/go-oauth2-server/login_screenshot.png)

The authorization server then establishes whether the resource owner grants or denies the client's access request.

[![Authorize page screenshot](https://raw.githubusercontent.com/RichardKnop/assets/master/go-oauth2-server/authorize_screenshot.png)](https://raw.githubusercontent.com/RichardKnop/assets/master/go-oauth2-server/authorize_screenshot.png)

If the request fails due to a missing, invalid, or mismatching redirection URI, or if the client identifier is missing or invalid, the authorization server SHOULD inform the resource owner of the error and MUST NOT automatically redirect the user-agent to the invalid redirection URI.

If the resource owner denies the access request or if the request fails for reasons other than a missing or invalid redirection URI, the authorization server informs the client by adding the following parameters to the fragment component of the redirection URI.

```
https://www.example.com/#error=access_denied&state=somestate
```

Assuming the resource owner grants access, the authorization server redirects the user-agent back to the client using the redirection URI provided earlier. The redirection URI includes he access token in the URI fragment.

```
https://www.example.com/#access_token=087902d5-29e7-417b-a339-b57a60d6742a&expires_in=3600&state=somestate&token_type=Bearer
```

The user-agent follows the redirection instructions by making a request to the web-hosted client resource (which does not include the fragment per [RFC2616]). The user-agent retains the fragment information locally.

The web-hosted client resource returns a web page (typically an HTML document with an embedded script) capable of accessing the full redirection URI including the fragment retained by the user-agent, and extracting the access token (and other parameters) contained in the fragment.

The user-agent executes the script provided by the web-hosted client resource locally, which extracts the access token.

The user-agent passes the access token to the client.

#### [](https://github.com/RichardKnop/go-oauth2-server#resource-owner-password-credentials)Resource Owner Password Credentials

<http://tools.ietf.org/html/rfc6749#section-4.3>

The resource owner password credentials grant type is suitable in cases where the resource owner has a trust relationship with the client, such as the device operating system or a highly privileged application. The authorization server should take special care when enabling this grant type and only allow it when other flows are not viable.

This grant type is suitable for clients capable of obtaining the resource owner's credentials (username and password, typically using an interactive form). It is also used to migrate existing clients using direct authentication schemes such as HTTP Basic or Digest authentication to OAuth by converting the stored credentials to an access token.

```
+----------+
| Resource |
| Owner |
| |
+----------+
 v
 | Resource Owner
 (A) Password Credentials
 |
 v
+---------+ +---------------+
| |>--(B)---- Resource Owner ------->| |
| | Password Credentials | Authorization |
| Client | | Server |
| |<--(C)---- Access Token ---------<| |
| | (w/ Optional Refresh Token) | |
+---------+ +---------------+
```

The resource owner provides the client with its username and password.

The client requests an access token from the authorization server's token endpoint by including the credentials received from the resource owner. When making the request, the client authenticates with the authorization server.

```
$ curl -v localhost:8080/oauth/api/v1/tokens \
 -u test_client:test_secret \
 -d "grant_type=password" \
 -d "username=test@username" \
 -d "password=test_password" \
 -d "scope=read_write"
```

The authorization server authenticates the client and validates the resource owner credentials, and if valid, issues an access token.

```
{
 "id": 1,
 "access_token": "00ccd40e-72ca-4e79-a4b6-67c95e2e3f1c",
 "expires_in": 3600,
 "token_type": "Bearer",
 "scope": "foo bar",
 "refresh_token": "6fd8d272-375a-4d8a-8d0f-43367dc8b791"
}
```

#### [](https://github.com/RichardKnop/go-oauth2-server#client-credentials)Client Credentials

<http://tools.ietf.org/html/rfc6749#section-4.4>

The client can request an access token using only its client credentials (or other supported means of authentication) when the client is requesting access to the protected resources under its control, or those of another resource owner that have been previously arranged with the authorization server (the method of which is beyond the scope of this specification).

The client credentials grant type MUST only be used by confidential clients.

```
+---------+ +---------------+
| | | |
| |>--(A)- Client Authentication --->| Authorization |
| Client | | Server |
| |<--(B)---- Access Token ---------<| |
| | | |
+---------+ +---------------+
```

The client authenticates with the authorization server and requests an access token from the token endpoint.

```
$ curl -v localhost:8080/oauth/api/v1/tokens \
 -u test_client:test_secret \
 -d "grant_type=client_credentials" \
 -d "scope=read_write"
```

The authorization server authenticates the client, and if valid, issues an access token.

```
{
 "id": 1,
 "access_token": "00ccd40e-72ca-4e79-a4b6-67c95e2e3f1c",
 "expires_in": 3600,
 "token_type": "Bearer",
 "scope": "foo bar",
 "refresh_token": "6fd8d272-375a-4d8a-8d0f-43367dc8b791"
}
```

### [](https://github.com/RichardKnop/go-oauth2-server#refreshing-an-access-token)Refreshing An Access Token

<http://tools.ietf.org/html/rfc6749#section-6>

If the authorization server issued a refresh token to the client, the client can make a refresh request to the token endpoint in order to refresh the access token.

```
$ curl -v localhost:8080/oauth/api/v1/tokens \
 -u test_client:test_secret \
 -d "grant_type=refresh_token" \
 -d "refresh_token=6fd8d272-375a-4d8a-8d0f-43367dc8b791"
```

The authorization server MUST:

- require client authentication for confidential clients or for any client that was issued client credentials (or with other authentication requirements),

- authenticate the client if client authentication is included and ensure that the refresh token was issued to the authenticated client, and

- validate the refresh token.

If valid and authorized, the authorization server issues an access token.

```
{
 "id": 1,
 "access_token": "1f962bd5-7890-435d-b619-584b6aa32e6c",
 "expires_in": 3600,
 "token_type": "Bearer",
 "scope": "foo bar",
 "refresh_token": "3a6b45b8-9d29-4cba-8a1b-0093e8a2b933"
}
```

The authorization server MAY issue a new refresh token, in which case the client MUST discard the old refresh token and replace it with the new refresh token. The authorization server MAY revoke the old refresh token after issuing a new refresh token to the client. If a new refresh token is issued, the refresh token scope MUST be identical to that of the refresh token included by the client in the request.

## [](https://github.com/RichardKnop/go-oauth2-server#development)Development

## [](https://github.com/RichardKnop/go-oauth2-server#dependencies)Dependencies

According to [Go 1.5 Vendor experiment](https://docs.google.com/document/d/1Bz5-UB7g2uPBdOx-rw5t9MxJwkfpx90cqG9AFL0JAYo), all dependencies are stored in a vendor directory. This approach is called "vendoring" and is the best practice for Go projects to lock versions of dependencies in order to achieve reproducible builds.

To update dependencies during development:

```
$ make update-deps
```

To install dependencies:

```
$ make install-deps
```

## [](https://github.com/RichardKnop/go-oauth2-server#setup)Setup

If you are developing on OSX, install `etcd`, `Postgres`:

```
$ brew install etcd
$ brew install postgres
```

You might want to create a `Postgres` database:

```
$ createuser --createdb go_oauth2_server
$ createdb -U go_microservice_example go_oauth2_server
```

Load a configuration into `etcd`:

```
$ curl -L http://127.0.0.1:4001/v2/keys/config/go_oauth2_server.json -XPUT -d value='{
 "Database": {
 "Type": "postgres",
 "Host": "127.0.0.1",
 "Port": 5432,
 "User": "go_oauth2_server",
 "Password": "",
 "DatabaseName": "go_oauth2_server"
 },
 "Oauth": {
 "AccessTokenLifetime": 3600,
 "RefreshTokenLifetime": 1209600,
 "AuthCodeLifetime": 3600 
 },
 "Session": {
 "Secret": "test_secret",
 "Path": "/",
 "MaxAge": 604800,
 "HTTPOnly": true
 },
 "TrustedClient": {
 "ClientID": "test_client",
 "Secret": "test_secret"
 }
}'
```

Run migrations:

```
$ go run main.go migrate
```

And finally, run the app:

```
$ go run main.go runserver
```

## [](https://github.com/RichardKnop/go-oauth2-server#test-data)Test Data

You might want to insert some test data if you are testing locally using curl:

```
insert into scopes(scope, is_default) values('read', true);
insert into scopes(scope, is_default) values('read_write', false);

insert into clients(client_id, secret) values('test_client', '$2a$10$CUoGytf1pR7CC6Y043gt/.vFJUV4IRqvH5R6F0VfITP8s2TqrQ.4e');

insert into users(username, password) values('test@username', '$2a$10$4J4t9xuWhOKhfjN0bOKNReS9sL3BVSN9zxIr2.VaWWQfRBWh1dQIS');
```

## [](https://github.com/RichardKnop/go-oauth2-server#testing)Testing

Some of the tests are functional. You need to have `sqlite` and `etcd` installed and running in order to run the tests.

To run tests:

```
make test
```
