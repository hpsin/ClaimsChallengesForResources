# ClaimsChallengesForResources
Draft proposal to support passing claims challenges from an RS to an AS via the client. 

See the RFC-style proposed text [here](ClaimsChallengeForRS.txt). 

## Summary

The [claims request as specified in OIDC](https://openid.net/specs/openid-connect-core-1_0.html#ClaimsParameter) allows an RP to indicate to the AS tht they need additional claims in the ID token.  This pattern is useful for dynamic acquisition of data, or to indicate to the AS that a certain standard of authentication is required.  No such solution exists, however, to allow an RS to indicate dynamically that they too need additional claims in the access token.

This proposal covers three items: 
* Expand the `claims` request in OIDC to allow the RP to request additional claims within the `access_token` on behalf of an RS. 
* Standardize on a www-authenticate error used to communicate the claims required by the RS to the RP. 
* Standardize RP behavior whne recieving a "claims challenge" from an RS, to ensure a new token is fetched. 

## Reasoning

With the new CAEP standards coming out, as well as renewed interest in supporting better and more granular RS authorization and authentication patterns, we have identified this as a gap in existing standards.  With CAEP, an RS needs a defined way to communicate back to the RP, and eventually the AS, that they had reason to reject the token used by the user.  Today, we have observed that the majority of RPs, met with a standard 401 error, will simply fail rather than attempt to get a new token.  Using the claims challenge pattern, we look to push clients to silently acquire new tokens, and failing that indicate to the user that they have an authN/Z problem (as opposed to a problem with the RS). 

## Open issues/questions

* Tampering with the claims request by the RP.  Is this an issue? Does there need to be some sort of signing or encryption layer added? 
* Open an issue if you think of others, or a PR. 

## Considerations / other ideas 

* This does not work out of the box with OAuth2, as it relies on an OIDC mechanism.  This may require updating OAuth2 to support this claims request parameter to be fully compliant. 
* This could also be implemented in a back-channel way - RS communicates directly to the AS, indicating that some additional claims and auth behavior are required for a given session, then passes a generic error back to the P that causes re-auth and a new token to be fetched.  This would still rely on some standardized agreement to perform auth and discard existing tokens on the RP side. 

## Existing implementations

Microsoft uses this pattern in two places, in two different ways:
* [Claims challenges for conditional access](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-conditional-access-dev-guide#complying-with-a-conditional-access-policy)
* [Claims challenges for CAEP](https://docs.microsoft.com/en-us/azure/active-directory/develop/claims-challenge)
