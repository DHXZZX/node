# authentication

 "Authentication" is the process of establishing a principal is who they claim to be (a "principal" generally means a user, device or some other system which can perform an action in your application). 

# authorization

"Authorization" refers to the process of deciding whether a principal is allowed to perform an action within your application. 

Spring Security currently supports authentication integration with all of these technologies: 

- HTTP BASIC authentication headers (an IETF RFC-based standard)
- HTTP Digest authentication headers (an IETF RFC-based standard)
- HTTP X.509 client certificate exchange (an IETF RFC-based standard)
- LDAP (a very common approach to cross-platform authentication needs, especially in large environments)
- Form-based authentication (for simple user interface needs)
- OpenID authentication
- Authentication based on pre-established request headers (such as Computer Associates Siteminder)
- Jasig Central Authentication Service (otherwise known as CAS, which is a popular open source single sign-on system)
- Transparent authentication context propagation for Remote Method Invocation (RMI) and HttpInvoker (a Spring remoting protocol)
- Automatic "remember-me" authentication (so you can tick a box to avoid re-authentication for a predetermined period of time)
- Anonymous authentication (allowing every unauthenticated call to automatically assume a particular security identity)
- Run-as authentication (which is useful if one call should proceed with a different security identity)
- Java Authentication and Authorization Service (JAAS)
- Java EE container authentication (so you can still use Container Managed Authentication if desired)
- Kerberos
- Java Open Source Single Sign-On (JOSSO) *
- OpenNMS Network Management Platform *
- AppFuse *
- AndroMDA *
- Mule ESB *
- Direct Web Request (DWR) *
- Grails *
- Tapestry *
- JTrac *
- Jasypt *
- Roller *
- Elastic Path *
- Atlassian Crowd *
- Your own authentication systems (see below)



# Project Modules

## Core - spring-security-core.jar

- `org.springframework.security.core`
- `org.springframework.security.access`
- `org.springframework.security.authentication`
- `org.springframework.security.provisioning`

## Remoting - spring-security-remoting.jar

`org.springframework.security.remoting `

## Web - spring-security-web.jar

`org.springframework.security.web `

## Config - spring-security-config.jar

`org.springframework.security.config `

## LDAP - spring-security-ldap.jar

`org.springframework.security.ldap `

## OAuth 2.0 Core - spring-security-oauth2-core.jar

`spring-security-oauth2-core.jar `

## OAuth 2.0 Client - spring-security-oauth2-client.jar

`spring-security-oauth2-client.jar `

## OAuth 2.0 JOSE - spring-security-oauth2-jose.jar

`spring-security-oauth2-jose.jar `

- JSON Web Token (JWT)
- JSON Web Signature (JWS)
- JSON Web Encryption (JWE)
- JSON Web Key (JWK)

- `org.springframework.security.oauth2.jwt`
- `org.springframework.security.oauth2.jose`

## ACL - spring-security-acl.jar

`org.springframework.security.acls `

## CAS - spring-security-cas.jar

`org.springframework.security.cas `

## OpenID - spring-security-openid.jar

`org.springframework.security.openid `

