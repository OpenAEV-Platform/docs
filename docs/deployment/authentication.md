# Authentication

## Introduction

This documentation provides details on setting up and utilizing the authentication system, which supports multiple authentication methods to cater to different user needs and security requirements.

## Supported authentication methods

!!! tip "Production deployment"

    Please use the LDAP/Auth0/OpenID/SAML strategy for production deployment.

### Local users

OpenAEV use this strategy as the default, but it's not the one we recommend for security reasons.

| Parameter                 | Environment variable      | Default value         | Description                                                   |
|:--------------------------|:--------------------------|:----------------------|:--------------------------------------------------------------|
| openaev.auth-local-enable | OPENAEV_AUTH-LOCAL-ENABLE | true               | Set this to `true` to enable username/password authentication. |

### OpenID

This method allows to use the [OpenID Connect Protocol](https://openid.net/connect) to handle the authentication.

| Parameter                      | Environment variable       | Default value         | Description                                                   |
|:-------------------------------|:---------------------------|:----------------------|:--------------------------------------------------------------|
| openaev.auth-openid-enable                 | OPENAEV_AUTH-OPENID-ENABLE | false               | Set this to `true` to enable OAuth (OpenID) authentication. |

Example for Auth0:

```properties
SPRING_SECURITY_OAUTH2_CLIENT_PROVIDER_{registrationId}_ISSUER-URI=https://auth.auth0.io
SPRING_SECURITY_OAUTH2_CLIENT_REGISTRATION_{registrationId}_CLIENT-NAME=Login with auth0
SPRING_SECURITY_OAUTH2_CLIENT_REGISTRATION_{registrationId}_CLIENT-ID=
SPRING_SECURITY_OAUTH2_CLIENT_REGISTRATION_{registrationId}_CLIENT-SECRET=
SPRING_SECURITY_OAUTH2_CLIENT_REGISTRATION_{registrationId}_REDIRECT-URI=${openaev.base-url}/login/oauth2/code/{registrationId}
SPRING_SECURITY_OAUTH2_CLIENT_REGISTRATION_{registrationId}_SCOPE=openid,profile,email
```

Example for GitHub (or others pre-handled oauth2 providers):

```properties
SPRING_SECURITY_OAUTH2_CLIENT_REGISTRATION_{registrationId}_CLIENT_NAME=Login with Github
SPRING_SECURITY_OAUTH2_CLIENT_REGISTRATION_{registrationId}_CLIENT_ID=
SPRING_SECURITY_OAUTH2_CLIENT_REGISTRATION_{registrationId}_CLIENT_SECRET=
```

!!! tip "Tips"

      *{registrationId} is an arbitrary identifier you choose.*

#### SAML2

This strategy can be used to authenticate your user with your company SAML.

| Parameter                      | Environment variable           | Default value         | Description                                                   |
|:-------------------------------|:-------------------------------|:----------------------|:--------------------------------------------------------------|
| openaev.auth-saml2-enable                 | OPENAEV_AUTH-SAML2-ENABLE                 | false               | Set this to `true` to enable SAML2 authentication. |
 
Example for Microsoft :
```properties
SPRING_SECURITY_SAML2_RELYINGPARTY_REGISTRATION_{registrationId}_ENTITY-ID=
SPRING_SECURITY_SAML2_RELYINGPARTY_REGISTRATION_{registrationId}_ASSERTINGPARTY_METADATA-URI=
OPENAEV_PROVIDER_{registrationId}_FIRSTNAME_ATTRIBUTE_KEY=
OPENAEV_PROVIDER_{registrationId}_LASTNAME_ATTRIBUTE_KEY=
```

!!! tip "Tips"
     
      *{registrationId} is an arbitrary identifier you choose.*
      metadata-uri is the uri of the xml file given by your identity provider

### Single Sign On URL

#### SAML2

Url for the config of your sso provider
```
${openaev.base-url}/login/saml2/sso/{registrationId}
```

### Map administrators to specific roles (OpenID and SAML 2)

To grant administrative roles, you can utilize OAuth and SAML2 integration. If you opt for this approach, you'll need to include the following variables:

```properties
OPENAEV_PROVIDER_{registrationId}_ROLES_PATH=http://schemas.microsoft.com/ws/2008/06/identity/claims/role
OPENAEV_PROVIDER_{registrationId}_ROLES_ADMIN=
```

However, if you intend to manage administrative roles within the OpenAEV platform itself, there's no need to provide these variables.
