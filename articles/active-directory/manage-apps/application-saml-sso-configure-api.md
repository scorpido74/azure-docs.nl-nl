---
title: Microsoft Graph-Api's gebruiken om eenmalige aanmelding op basis van SAML te configureren
titleSuffix: Azure Active Directory
description: Moet u eenmalige aanmelding op basis van SAML instellen voor meerdere exemplaren van een toepassing? Meer informatie over hoe u tijd kunt besparen door gebruik te maken van de Microsoft Graph Api's om de configuratie van eenmalige aanmelding op basis van SAML te automatiseren.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: luleon
ms.openlocfilehash: c766fb84fa14eab09143cfb5b71f7aefc9f51d24
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85077767"
---
# <a name="automate-saml-based-sso-app-configuration-with-microsoft-graph-api"></a>Configuratie van op SAML gebaseerde SSO-app automatiseren met Microsoft Graph-API

In dit artikel leert u hoe u een toepassing kunt maken en configureren vanuit de galerie Azure Active Directory (Azure AD). In dit artikel wordt AWS als voor beeld gebruikt, maar u kunt de stappen in dit artikel voor elke op SAML gebaseerde app in de Azure AD-galerie gebruiken.

**Stappen voor het gebruik van Microsoft Graph-Api's voor het automatiseren van de configuratie van eenmalige aanmelding op basis van SAML**

| Stap  | Details  |
|---------|---------|
| [1. de galerie toepassing maken](#step-1-create-the-gallery-application) | Meld u aan bij de API-client <br> De galerie toepassing ophalen <br> De galerie toepassing maken|
| [2. eenmalige aanmelding configureren](#step-2-configure-single-sign-on) | App-object-ID en object-ID van Service-Principal ophalen <br> Modus voor eenmalige aanmelding instellen <br> Basis-SAML-Url's instellen, zoals id, antwoord-URL, aanmeldings-URL <br> App-rollen toevoegen (optioneel)|
| [3. claim toewijzing configureren](#step-3-configure-claims-mapping) | Claim toewijzings beleid maken <br> Claim toewijzings beleid toewijzen aan Service-Principal|
| [4. handtekening certificaat configureren](#step-4-configure-signing-certificate) | Een certificaat maken <BR> Een aangepaste handtekening sleutel toevoegen <br> De aangepaste handtekening sleutel activeren|
| [5. gebruikers toewijzen](#step-5-assign-users) | Gebruikers en groepen toewijzen aan de toepassing
| [6. de kant van de toepassing configureren](#step-6-configure-the-application-side)| Azure AD SAML-meta gegevens ophalen

**Lijst met alle Api's die in de documentatie worden gebruikt**

Zorg ervoor dat u over de juiste machtigingen beschikt om de volgende Api's aan te roepen.

|Resourcetype |Methode |
|---------|---------|
|[applicationTemplate](https://docs.microsoft.com/graph/api/resources/applicationtemplate?view=graph-rest-beta)|[ApplicationTemplate weer geven](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) <br>[ApplicationTemplate instantiëren](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http)|
|[servicePrincipals](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-1.0)|[ServicePrincipal bijwerken](https://docs.microsoft.com/graph/api/serviceprincipal-update?view=graph-rest-1.0&tabs=http) <br> [AppRoleAssignments maken](https://docs.microsoft.com/graph/api/serviceprincipal-post-approleassignments?view=graph-rest-1.0&tabs=http) <br> [ClaimsMappingPolicies toewijzen](https://docs.microsoft.com/graph/api/serviceprincipal-post-claimsmappingpolicies?view=graph-rest-beta&tabs=http)|
|[toepassingen](https://docs.microsoft.com/graph/api/resources/application?view=graph-rest-1.0)|[Toepassing bijwerken](https://docs.microsoft.com/graph/api/application-update?view=graph-rest-1.0&tabs=http)|
|[claimsMappingPolicy](https://docs.microsoft.com/graph/api/resources/claimsmappingpolicy?view=graph-rest-beta)| [ClaimsMappingPolicy maken](https://docs.microsoft.com/graph/api/claimsmappingpolicy-post-claimsmappingpolicies?view=graph-rest-beta&tabs=http)

>[!NOTE]
>De antwoord objecten die in dit artikel worden weer gegeven, kunnen worden inge kort op Lees baarheid. Alle eigenschappen worden geretourneerd op basis van een daad werkelijke aanroep.

## <a name="step-1-create-the-gallery-application"></a>Stap 1: de galerie toepassing maken

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Meld u aan bij Microsoft Graph Explorer (aanbevolen), Postman of een andere API-client die u gebruikt

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) starten
2. Selecteer **Aanmelden bij micro soft** en meld u aan met een globale Azure AD-beheerder of de referenties van de app-beheerder.
3. Wanneer de aanmelding is geslaagd, worden de gegevens van het gebruikers account in het linkerdeel venster weer gegeven.

### <a name="retrieve-the-gallery-application-template-identifier"></a>De id van de galerie toepassings sjabloon ophalen

Toepassingen in de Azure AD-toepassings galerie hebben elk een [toepassings sjabloon](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) waarmee de meta gegevens voor die toepassing worden beschreven. Met deze sjabloon kunt u een exemplaar van de toepassing en Service-Principal in uw Tenant maken voor beheer.

#### <a name="request"></a>Aanvraag

<!-- {
  "blockType": "request",
  "name": "get_applicationtemplates"
}-->

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates
```

#### <a name="response"></a>Antwoord

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "Amazon Web Services (AWS)",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": null    
  
}
```

### <a name="create-the-gallery-application"></a>De galerie toepassing maken

[Maak een instantie](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http) van de toepassing en Service-Principal in uw Tenant met behulp van de sjabloon-id die u in de laatste stap voor uw toepassing hebt opgehaald.

#### <a name="request"></a>Aanvraag

<!-- {
  "blockType": "request",
  "name": "applicationtemplate_instantiate"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/8b1025e4-1dd2-430b-a150-2ef79cd700f5/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>Antwoord


<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationServicePrincipal"
} -->

```http
HTTP/1.1 201 OK
Content-type: application/json


{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-configure-single-sign-on"></a>Stap 2: eenmalige aanmelding configureren

### <a name="retrieve-app-object-id-and-service-principal-object-id"></a>App-object-ID en object-ID van Service-Principal ophalen

Gebruik de reactie van de vorige aanroep om de object-ID van de toepassing en de object-ID van de Service-Principal op te halen en op te slaan.

```
"application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63"
}
"servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e"
}
```
### <a name="set-single-sign-on-mode"></a>Modus voor eenmalige aanmelding instellen

In dit voor beeld stelt u in `saml` als de modus voor eenmalige aanmelding in het [resource type servicePrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-1.0). Andere SAML SSO-eigenschappen die u kunt configureren zijn: `notificationEmailAddresses` , `loginUrl` en`samlSingleSignOnSettings.relayState`

#### <a name="request"></a>Aanvraag

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e
Content-type: servicePrincipal/json

{
    "preferredSingleSignOnMode": "saml"
}
```

#### <a name="response"></a>Antwoord

<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 204
```

### <a name="set-basic-saml-urls-such-as-identifier-reply-url-sign-on-url"></a>Basis-SAML-Url's instellen, zoals id, antwoord-URL, aanmeldings-URL

Stel de id en antwoord-Url's in voor AWS in het toepassings object.

#### <a name="request"></a>Aanvraag

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/applications/cbc071a6-0fa5-4859-8g55-e983ef63df63
Content-type: applications/json

{
    "web": {
        "redirectUris": [
            "https://signin.aws.amazon.com/saml"
        ] 
    },
    "identifierUris": [
        "https://signin.aws.amazon.com/saml"
    ]    
}
```
#### <a name="response"></a>Antwoord

<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 204
```
### <a name="add-app-roles-optional"></a>App-rollen toevoegen (optioneel)

Als voor de toepassing de functie gegevens in het token zijn vereist, voegt u de definitie van de rollen in het toepassings object toe. Voor AWS kunt u het [inrichten van gebruikers inschakelen](../app-provisioning/application-provisioning-configure-api.md) om alle functies van dat AWS-account op te halen. 

Lees [Configure the Role claim die is uitgegeven in het SAML-token](../develop/active-directory-enterprise-app-role-management.md) voor meer informatie.

> [!NOTE] 
> Wanneer u app-rollen toevoegt, wijzigt u niet de standaard-app-rollen msiam_access. 

#### <a name="request"></a>Aanvraag

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/serviceprincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e
Content-type: serviceprincipals/json

{
"appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "454dc4c2-8176-498e-99df-8c4efcde41ef",
            "isEnabled": true,
            "value": "arn:aws:iam::212743507312:role/accountname-aws-admin,arn:aws:iam::212743507312:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Finance,WAAD",
            "displayName": "Finance,WAAD",
            "id": "8642d5fa-18a3-4245-ab8c-a96000c1a217",
            "isEnabled": true,
            "value": "arn:aws:iam::212743507312:role/accountname-aws-finance,arn:aws:iam::212743507312:saml-provider/WAAD"
        }
    ]

}
```

#### <a name="response"></a>Antwoord

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

## <a name="step-3-configure-claims-mapping"></a>Stap 3: claim toewijzing configureren

### <a name="create-claims-mapping-policy"></a>Claim toewijzings beleid maken

Naast de basis claims configureert u de volgende claims voor Azure AD voor het verzenden van het SAML-token:

| Claim naam | Bron  |
|---------|---------|
| `https://aws.amazon.com/SAML/Attributes/Role` | assignedroles| 
| `https://aws.amazon.com/SAML/Attributes/RoleSessionName` | userPrincipalName |
| `https://aws.amazon.com/SAML/Attributes/SessionDuration` | "900" |
| rolls | assignedroles |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` | userPrincipalName |

Zie voor meer informatie [claims aanpassen die zijn verzonden in token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).

#### <a name="request"></a>Aanvraag

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/v1.0/policies/claimsMappingPolicies
Content-type: claimsMappingPolicies/json

{
    "definition":[
            "{\"ClaimsMappingPolicy\": {
                \"Version\": 1,
                \"IncludeBasicClaimSet\": \"true\",
                \"ClaimsSchema\": [
                    {
                        \"Source\": \"user\",
                        \"ID\": \"assignedroles\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/Role\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"userprincipalname\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/RoleSessionName\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"900\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/SessionDuration\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"assignedroles\",
                        \"SamlClaimType\": \"appRoles\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"userprincipalname\",
                        \"SamlClaimType\": \"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\"
                    }
                ]
            }
        }"

    ],
    "displayName": "AWS Claims policy",
    "isOrganizationDefault": false
}
```

#### <a name="response"></a>Antwoord

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.claimsMappingPolicies",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: claimsMappingPolicies/json

{
    "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#policies/claimsMappingPolicies/$entity",
    "id": "6b33aa8e-51f3-41a6-a0fd-d660d276197a",
    "definition": [
        "{\"ClaimsMappingPolicy\": {\"Version\": 1,\"IncludeBasicClaimSet\": \"true\",\"ClaimsSchema\": [{\"Source\": \"user\",\"ID\": \"assignedroles\",\"SamlClaimType\":\"https://aws.amazon.com/SAML/Attributes/Role\"\r\n                    },{\"Source\": \"user\",\"ID\": \"userprincipalname\",\"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/RoleSessionName\"},{\"Source\": \"user\",\"ID\": \"900\",\"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/SessionDuration\"},{\"Source\": \"user\",\"ID\": \"assignedroles\",\"SamlClaimType\":\"appRoles\"},{\"Source\": \"user\",\"ID\": \"userprincipalname\",\"SamlClaimType\": \"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\"}]}}"
    ],
    "displayName": "AWS Claims policy",
    "isOrganizationDefault": false
}
```

### <a name="assign-claims-mapping-policy-to-service-principal"></a>Claim toewijzings beleid toewijzen aan Service-Principal

#### <a name="request"></a>Aanvraag

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e/claimsMappingPolicies/$ref

Content-type: claimsMappingPolicies/json

{
  "@odata.id":"https://graph.microsoft.com/v1.0/policies/claimsMappingPolicies/6b33aa8e-51f3-41a6-a0fd-d660d276197a"
}
```

#### <a name="response"></a>Antwoord

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

## <a name="step-4-configure-signing-certificate"></a>Stap 4: handtekening certificaat configureren

Het gebruik van de [applicationTemplate](https://docs.microsoft.com/graph/api/resources/applicationtemplate?view=graph-rest-beta) -API maakt standaard geen handtekening certificaat. Maak uw aangepaste handtekening certificaat en wijs dit toe aan de toepassing. 

### <a name="create-a-custom-signing-certificate"></a>Een aangepast handtekening certificaat maken

Als u wilt testen, kunt u de volgende Power shell-opdracht gebruiken om een zelfondertekend certificaat op te halen. Gebruik de best mogelijke beveiligings procedure van uw bedrijf voor het maken van een handtekening certificaat voor productie.

```powershell
Param(
    [Parameter(Mandatory=$true)]
    [string]$fqdn,
    [Parameter(Mandatory=$true)]
    [string]$pwd,
    [Parameter(Mandatory=$true)]
    [string]$location
) 

if (!$PSBoundParameters.ContainsKey('location'))
{
    $location = "."
} 

$cert = New-SelfSignedCertificate -certstorelocation cert:\currentuser\my -DnsName $fqdn
$pwdSecure = ConvertTo-SecureString -String $pwd -Force -AsPlainText
$path = 'cert:\currentuser\my\' + $cert.Thumbprint
$cerFile = $location + "\\" + $fqdn + ".cer"
$pfxFile = $location + "\\" + $fqdn + ".pfx" 

Export-PfxCertificate -cert $path -FilePath $pfxFile -Password $pwdSecure
Export-Certificate -cert $path -FilePath $cerFile
```

### <a name="add-a-custom-signing-key"></a>Een aangepaste handtekening sleutel toevoegen

Voeg de volgende informatie toe aan de Service-Principal:

* Persoonlijke sleutel
* Wachtwoord
* Open bare sleutel 

Pak de met base64 gecodeerde persoonlijke en open bare sleutel uit het PFX-bestand uit. Lees voor meer informatie over de eigenschappen het [resource type sleutel referentie](https://docs.microsoft.com/graph/api/resources/keycredential?view=graph-rest-1.0).

Zorg ervoor dat de keyId voor de sleutel referentie die wordt gebruikt voor ' Sign ' overeenkomt met de keyId van de passwordCredential.

U kunt de genereren `customkeyIdentifier` door de hash van de vinger afdruk van het certificaat op te halen.

```csharp
  public string GetSha256FromThumbprint(string thumbprint)
  {
      var message = Encoding.ASCII.GetBytes(thumbprint);
      SHA256Managed hashString = new SHA256Managed();
      return Convert.ToBase64String(hashString.ComputeHash(message));
  }
```

#### <a name="request"></a>Aanvraag

> [!NOTE]
> De "Key"-waarde in de eigenschap voor sleutel referenties is inge kort voor de Lees baarheid. De waarde is base 64 gecodeerd. De eigenschap `usage` is "ondertekenen" voor de persoonlijke sleutel. De eigenschap `usage` is ' controleren ' voor de open bare sleutel.

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json

{
    "keyCredentials":[
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Sign",
            "key":"MIIKIAIBAz.....HBgUrDgMCERE20nuTptI9MEFCh2Ih2jaaLZBZGeZBRFVNXeZmAAgIH0A==",
            "displayName": "CN=awsAPI"
        },
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "e35a7d11-fef0-49ad-9f3e-aacbe0a42c42",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "key": "MIIDJzCCAg+gAw......CTxQvJ/zN3bafeesMSueR83hlCSyg==",
            "displayName": "CN=awsAPI"
        }

    ],
    "passwordCredentials": [
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "endDateTime": "2022-01-27T19:40:33Z",
            "startDateTime": "2020-04-20T19:40:33Z",
            "secretText": "61891f4ee44d"
        }
    ]
}
```

#### <a name="response"></a>Antwoord

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

### <a name="activate-the-custom-signing-key"></a>De aangepaste handtekening sleutel activeren

U moet de eigenschap instellen `preferredTokenSigningKeyThumbprint` op de vinger afdruk van het certificaat dat door Azure AD moet worden gebruikt om de SAML-respons te ondertekenen. 

#### <a name="request"></a>Aanvraag

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json

{
    "preferredTokenSigningKeyThumbprint": "AC09FEF18DDE6983EE2A164FBA3C4DD7518BD787"
}
```

#### <a name="response"></a>Antwoord

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```
## <a name="step-5-assign-users"></a>Stap 5: gebruikers toewijzen

### <a name="assign-users-and-groups-to-the-application"></a>Gebruikers en groepen toewijzen aan de toepassing

Wijs de volgende gebruiker toe aan de Service-Principal en wijs de AWS_Role1 toe. 

| Name  | Id  |
|---------|---------|
| Gebruikers-ID (principalId) | 6cad4079-4e79-4a3f-9efb-ea30a14bdb26 |
| Type (principalType) | Gebruiker |
| App-functie-ID (appRoleId) | 454dc4c2-8176-498e-99df-8c4efcde41ef |
| servicePrincipalID (resourceId) | 515f62cb-d18a-4dca-bec3-bb0bf31deeea |

#### <a name="request"></a>Aanvraag

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e/appRoleAssignments

Content-type: appRoleAssignments/json

{
  "principalId": "6cad4079-4e79-4a3f-9efb-ea30a14bdb26",
  "principalType": "User",
  "appRoleId":"454dc4c2-8176-498e-99df-8c4efcde41ef",
  "resourceId":"515f62cb-d18a-4dca-bec3-bb0bf31deeea"
}
```
#### <a name="response"></a>Antwoord

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 201 
Content-type: appRoleAssignments/json

{
    "id": "rq7hyzl4yECaNZleMrTpDV-OCe5TEl5Ao_o76XMrRFU",
    "creationTimestamp": "2020-04-23T17:38:13.2508567Z",
    "appRoleId": "454dc4c2-8176-498e-99df-8c4efcde41ef",
    "principalDisplayName": "User 1",
    "principalId": "6cad4079-4e79-4a3f-9efb-ea30a14bdb26",
    "principalType": "User",
    "resourceDisplayName": "AWS API Created",
    "resourceId": "515f62cb-d18a-4dca-bec3-bb0bf31deeea"
}
```

Zie [appRoleAssignment](https://docs.microsoft.com/graph/api/resources/approleassignment?view=graph-rest-1.0) resource type voor meer informatie.

## <a name="step-6-configure-the-application-side"></a>Stap 6: de toepassings zijde configureren

### <a name="get-azure-ad-saml-metadata"></a>Azure AD SAML-meta gegevens ophalen

Gebruik de volgende URL om de Azure AD SAML-meta gegevens op te halen voor de specifieke geconfigureerde toepassing. De meta gegevens bevatten onder andere gegevens zoals het handtekening certificaat, Azure AD entityID en Azure AD SingleSignOnService.

https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id}

## <a name="next-steps"></a>Volgende stappen
- [Microsoft Graph-Api's gebruiken om het inrichten van gebruikers te configureren](../app-provisioning/application-provisioning-configure-api.md)
- [Het rapport AD FS toepassings activiteit gebruiken om toepassingen te migreren naar Azure AD](migrate-adfs-application-activity.md)
