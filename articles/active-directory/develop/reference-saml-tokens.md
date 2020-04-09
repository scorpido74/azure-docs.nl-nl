---
title: Azure AD-token & claimtypen
description: Een handleiding voor het begrijpen en evalueren van de claims in de SAML 2.0- en JSON Web Tokens (JWT)-tokens die zijn uitgegeven door Azure Active Directory (AAD)
documentationcenter: na
author: rwike77
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/22/2018
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: b7ba4abd45fff8548c361f5e5ed44ef45fe32bbe
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883437"
---
# <a name="azure-ad-saml-token-reference"></a>Azure AD SAML-tokenverwijzing

Azure Active Directory (Azure AD) zendt verschillende typen beveiligingstokens uit in de verwerking van elke verificatiestroom. In dit document worden de indeling, beveiligingskenmerken en inhoud van elk type token beschreven.

## <a name="claims-in-saml-tokens"></a>Claims in SAML-tokens

> [!div class="mx-codeBreakAll"]
> | Name | Gelijkwaardige JWT-claim | Beschrijving | Voorbeeld |
> | --- | --- | --- | ------------|
> |Doelgroep | `aud` |De beoogde ontvanger van het token. De toepassing die het token ontvangt, moet controleren of de doelgroepwaarde correct is en tokens weigeren die zijn bedoeld voor een andere doelgroep. | `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>`  |
> | Verificatiemoment | |Registreert de datum en het tijdstip waarop verificatie heeft plaatsgevonden. | `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` | 
> |Verificatiemethode | `amr` |Hiermee wordt aangegeven hoe het onderwerp van het token is geverifieerd. | `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` |
> |Voornaam | `given_name` |Hiermee geeft u de eerste of "gegeven" naam van de gebruiker op, zoals ingesteld op het Azure AD-gebruikersobject. | `<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">`<br>`<AttributeValue>Frank<AttributeValue>`  |
> |Groepen | `groups` |Biedt object-id's die de groepslidmaatschappen van het onderwerp vertegenwoordigen. Deze waarden zijn uniek (zie Object-ID) en kunnen veilig worden gebruikt voor het beheren van toegang, zoals het afdwingen van autorisatie om toegang te krijgen tot een bron. De groepen die zijn opgenomen in de groepen claim worden geconfigureerd op een per-applicatie basis, via de "groupMembershipClaims" eigenschap van de toepassing manifest. Een waarde van null sluit alle groepen uit, een waarde van 'SecurityGroup' bevat alleen Active Directory Security Group-lidmaatschappen en een waarde van 'Alles' omvat zowel beveiligingsgroepen als Office 365-distributielijsten. <br><br> **Toelichting :** <br> Als het aantal groepen waarin de gebruiker zich bevindt, een limiet overschrijdt (150 voor SAML, 200 voor JWT), wordt een overschrijdingsclaim toegevoegd aan de claimbronnen die naar het graph-eindpunt wijzen met de lijst met groepen voor de gebruiker. (in . | `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` |
> | Indicator overschrijding groepen | `groups:src1` | Voor tokenaanvragen die niet beperkt `hasgroups` zijn (zie hierboven), maar nog steeds te groot voor het token, wordt een koppeling naar de volledige groepenlijst voor de gebruiker opgenomen. Voor SAML wordt dit toegevoegd als een `groups` nieuwe claim in plaats van de claim. | `<Attribute Name=" http://schemas.microsoft.com/claims/groups.link">`<br>`<AttributeValue>https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects<AttributeValue>` |
> |Identiteitsprovider | `idp` |Registreert de identiteitsprovider waarmee het onderwerp van het token is geverifieerd. Deze waarde is identiek aan de waarde van de emittentclaim, tenzij de gebruikersaccount zich in een andere tenant bevindt dan de emittent. | `<Attribute Name=" http://schemas.microsoft.com/identity/claims/identityprovider">`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` |
> |Uitgegevenbij | `iat` |Slaat het tijdstip op waarop het token is uitgegeven. Het wordt vaak gebruikt om token versheid te meten. | `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` |
> |Verlener | `iss` |Hiermee identificeert u de beveiligingstokenservice (STS) die het token construeert en retourneert. In de tokens die Azure AD retourneert, wordt de uitgever sts.windows.net. De GUID in de claimwaarde Van de uitgever is de tenant-id van de Azure AD-map. De tenant-id is een onveranderlijke en betrouwbare id van de directory. | `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` |
> |Achternaam | `family_name` |Hiermee worden de achternaam, achternaam of familienaam van de gebruiker weergegeven zoals gedefinieerd in het Azure AD-gebruikersobject. | `<Attribute Name=" http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">`<br>`<AttributeValue>Miller<AttributeValue>` |
> |Name | `unique_name` |Biedt een voor mensen leesbare waarde waarmee het onderwerp van het token wordt geïdentificeerd. Deze waarde is niet gegarandeerd uniek binnen een tenant en is ontworpen om alleen te worden gebruikt voor weergavedoeleinden. | `<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>`|
> |Object-id | `oid` |Bevat een unieke id van een object in Azure AD. Deze waarde is onveranderlijk en kan niet opnieuw worden toegewezen of opnieuw worden gebruikt. Gebruik de object-id om een object in query's naar Azure AD te identificeren. | `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` |
> |Rollen | `roles` |Vertegenwoordigt alle toepassingsrollen die het onderwerp zowel direct als indirect via groepslidmaatschap is toegekend en kan worden gebruikt om op rollen gebaseerde toegangscontrole af te dwingen. Toepassingsrollen worden per toepassing gedefinieerd via de `appRoles` eigenschap van het toepassingsmanifest. De `value` eigenschap van elke toepassingsrol is de waarde die wordt weergegeven in de rolclaim. | `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`|
> |Onderwerp | `sub` |Hiermee wordt het principal waarhet token informatie over bevestigt, zoals de gebruiker van een toepassing. Deze waarde is onveranderlijk en kan niet opnieuw worden toegewezen of opnieuw worden gebruikt, zodat deze kan worden gebruikt om autorisatiecontroles veilig uit te voeren. Omdat het onderwerp altijd aanwezig is in de tokens die de Azure AD-problemen heeft, raden we aan deze waarde te gebruiken in een autorisatiesysteem voor algemene doeleinden. <br> `SubjectConfirmation`is geen claim. Het beschrijft hoe het onderwerp van het token wordt geverifieerd. `Bearer`geeft aan dat het onderwerp wordt bevestigd door hun bezit van het token. | `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>`|
> |Tenant-id | `tid` |Een onveranderlijke, niet-herbruikbare id die de directorytenant identificeert die het token heeft uitgegeven. U deze waarde gebruiken om toegang te krijgen tot tenantspecifieke directorybronnen in een toepassing met meerdere tenantn. U deze waarde bijvoorbeeld gebruiken om de tenant in een aanroep naar de Graph API te identificeren. | `<Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>`|
> |Levensduur van token | `nbf`, `exp` |Definieert gedurende welke periode een token geldig is. De service die het token valideert, moet controleren of de huidige datum binnen de levensduur van het token valt, anders moet het token worden afgewezen. Met de service kan maximaal vijf minuten buiten het levensduurbereik van het token bereik rekening worden gehouden met eventuele verschillen in kloktijd ("time skew") tussen Azure AD en de service. | `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br>|

## <a name="sample-saml-token"></a>Voorbeeld van SAML-token

Dit is een voorbeeld van een typisch SAML-token.

    <?xml version="1.0" encoding="UTF-8"?>
    <t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
      <t:Lifetime>
        <wsu:Created xmlns:wsu="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
      </t:Lifetime>
      <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
          <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
      </wsp:AppliesTo>
      <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
          <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
          <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
            <ds:SignedInfo>
              <ds:CanonicalizationMethod Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
              <ds:SignatureMethod Algorithm="https://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
              <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                <ds:Transforms>
                  <ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                  <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
                </ds:Transforms>
                <ds:DigestMethod Algorithm="https://www.w3.org/2001/04/xmlenc#sha256" />
                <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
              </ds:Reference>
            </ds:SignedInfo>
            <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
            <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
              <X509Data>
                <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
              </X509Data>
            </KeyInfo>
          </ds:Signature>
          <Subject>
            <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
            <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
          </Subject>
          <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
            <AudienceRestriction>
              <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
            </AudienceRestriction>
          </Conditions>
          <AttributeStatement>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
              <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
              <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
              <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
              <AttributeValue>Admin</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
              <AttributeValue>Sample</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
              <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
              <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
              <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
              <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
              <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
              <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
              <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
              <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
              <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
              <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
              <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
              <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
              <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
              <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
            </Attribute>
          </AttributeStatement>
          <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
            <AuthnContext>
              <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
            </AuthnContext>
          </AuthnStatement>
        </Assertion>
      </t:RequestedSecurityToken>
      <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="https://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedAttachedReference>
      <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="https://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedUnattachedReference>
      <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
      <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
      <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
    </t:RequestSecurityTokenResponse>

## <a name="related-content"></a>Gerelateerde inhoud

* Zie de [beleidsbron](https://docs.microsoft.com/graph/api/resources/policy?view=graph-rest-beta)voor meer informatie over het beheren van tokenlevenslangbeleid met behulp van de Microsoft Graph API.
* Zie [Configureerbare tokenlevensduur in Azure AD](../develop/active-directory-configurable-token-lifetimes.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)voor meer informatie en voorbeelden over beheerbeleid via PowerShell-cmdlets, inclusief voorbeelden. 
* Voeg [aangepaste en optionele claims](../develop/active-directory-optional-claims.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) toe aan de tokens voor uw toepassing.
* Gebruik [Single Sign On (SSO) met SAML](single-sign-on-saml-protocol.md).
* Het [Azure Single Sign Out SAML-protocol gebruiken](single-sign-out-saml-protocol.md)
