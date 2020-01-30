---
title: Gebruikers met sociale identiteiten migreren
titleSuffix: Azure AD B2C
description: Bekijk de belangrijkste concepten met betrekking tot de migratie van gebruikers met sociale identiteiten in Azure AD B2C met behulp van Graph API.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2018
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 96bb4ef11b960a517054009f14f18b7af23fac14
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848835"
---
# <a name="azure-active-directory-b2c-migrate-users-with-social-identities"></a>Azure Active Directory B2C: gebruikers met sociale identiteiten migreren
Wanneer u van plan bent om uw ID-provider naar Azure AD B2C te migreren, moet u mogelijk ook gebruikers met sociale identiteiten migreren. In dit artikel wordt uitgelegd hoe u bestaande accounts voor sociale identiteiten, zoals Facebook-, LinkedIn-, micro soft-en Google-accounts migreert naar Azure AD B2C. Dit artikel is ook van toepassing op federatieve identiteiten, maar deze migraties zijn minder gangbaar. Voor de rest van dit artikel moet u rekening houden met alles wat van toepassing is op sociale accounts om ook te gelden voor andere typen federatieve accounts.

## <a name="prerequisites"></a>Vereisten
Dit artikel is een voortzetting van het artikel migratie van gebruikers en richt zich op de migratie van sociale identiteiten. Lees de [gebruikers migratie](user-migration.md)voordat u begint.

## <a name="social-identities-migration-introduction"></a>Inleiding tot migratie van sociale identiteiten

* Bij Azure AD B2C worden de namen van de **lokale accounts** (gebruikers naam of e-mail adres) opgeslagen in de `signInNames` verzameling in de gebruikers record. Het `signInNames` bevat een of meer `signInName` records waarmee de aanmeldings namen voor de gebruiker worden opgegeven. Elke aanmeldings naam moet uniek zijn binnen de Tenant.

* Identiteiten van **sociale accounts** worden opgeslagen in `userIdentities` verzameling. De vermelding geeft de `issuer` (naam van de identiteits provider), zoals facebook.com en de `issuerUserId`, een unieke gebruikers-id voor de certificaat verlener. Het kenmerk `userIdentities` bevat een of meer UserIdentity-records waarmee het type sociale-account en de unieke gebruikers-id van de sociale ID-provider worden opgegeven.

* **Combi neer een lokaal account met sociale identiteit**. Zoals vermeld, worden de namen van de lokale accounts en de identiteit van het sociale account in verschillende kenmerken opgeslagen. `signInNames` wordt gebruikt voor een lokaal account, terwijl `userIdentities` wordt gebruikt voor sociale accounts. Een enkele Azure AD B2C account kan alleen een lokaal account, alleen een sociaal account of een lokaal account met een of meer sociale identiteiten in één gebruikers record zijn. Dit gedrag stelt u in staat om één account te beheren, terwijl een gebruiker zich kan aanmelden met de referenties van een lokaal account of met de sociale identiteiten.

* `UserIdentity` type: bevat informatie over de identiteit van een gebruiker van een sociaal account in een Azure AD B2C Tenant:
  * `issuer` de teken reeks representatie van de ID-provider die de gebruikers-id heeft uitgegeven, zoals facebook.com.
  * `issuerUserId` de unieke gebruikers-id die wordt gebruikt door de ID-provider voor sociale netwerken in base64-gecodeerde indeling.

    ```JSON
    "userIdentities": [{
          "issuer": "Facebook.com",
          "issuerUserId": "MTIzNDU2Nzg5MA=="
      }
    ]
    ```

* Afhankelijk van de ID-provider is de **gebruikers-id** van de verlener een unieke waarde voor een bepaalde gebruiker per toepassing of ontwikkelings account. Configureer het Azure AD B2C-beleid met dezelfde toepassings-ID die eerder is toegewezen door de sociale provider of een andere toepassing binnen hetzelfde ontwikkelings account.

## <a name="use-graph-api-to-migrate-users"></a>Graph API gebruiken om gebruikers te migreren
U maakt het Azure AD B2C gebruikers account via de [Graph API](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet).
Als u met de Graph API wilt communiceren, moet u eerst een service account met beheerders bevoegdheden hebben. In azure AD registreert u een toepassing en verificatie voor Azure AD. De referenties van de toepassing zijn toepassings-ID en toepassings geheim. De toepassing fungeert als een gebruiker om de Graph API aan te roepen. Volg de instructies in stap 1 van het artikel [gebruikers migratie](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-user-migration) .

## <a name="required-properties"></a>Vereiste eigenschappen
De volgende lijst bevat de eigenschappen die vereist zijn wanneer u een gebruiker maakt.
* **accountEnabled** -True
* **DisplayName** : de naam die in het adres boek voor de gebruiker moet worden weer gegeven.
* **passwordProfile** : het wachtwoord profiel voor de gebruiker.

> [!NOTE]
> Alleen voor sociale accounts (zonder referenties voor lokale accounts) moet u het wacht woord opgeven. Azure AD B2C negeert het wacht woord dat u opgeeft voor sociale accounts.

* **userPrincipalName** -de user principal name (someuser@contoso.com). De user principal name moet een van de geverifieerde domeinen voor de Tenant bevatten. Als u de UPN wilt opgeven, moet u een nieuwe GUID-waarde genereren, samen voegen met `@` en de naam van uw Tenant.
* **mailNickname** -de e-mail alias voor de gebruiker. Deze waarde kan dezelfde ID zijn die u voor de userPrincipalName gebruikt.
* **signInNames** : een of meer SignInName-records waarmee de aanmeldings namen voor de gebruiker worden opgegeven. Elke aanmeldings naam moet uniek zijn binnen het bedrijf/de Tenant. Voor alleen een sociaal account mag deze eigenschap leeg blijven.
* **userIdentities** : een of meer UserIdentity-records die het type sociale-account opgeven en de unieke gebruikers-id van de ID-provider voor sociale netwerken.
* Beschrijving **otherMails** : alleen voor het e-mail adres van de gebruiker

Zie voor meer informatie: [Graph API Reference](/previous-versions/azure/ad/graph/api/users-operations#CreateLocalAccountUser)

## <a name="migrate-social-account-only"></a>Sociaal account migreren (alleen)
Als u alleen een sociaal account wilt maken, zonder lokale account referenties, stuurt u een HTTPS POST-aanvraag naar Graph API. De aanvraag tekst bevat de eigenschappen van de gebruiker die het sociaal account maakt. U moet mini maal de vereiste eigenschappen opgeven.


**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

De volgende formulier gegevens verzenden:

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8",
    "signInNames": [],
    "creationType": null,
    "displayName": "Sara Bell",
    "givenName": "Sara",
    "surname": "Bell",
    "passwordProfile": {
        "password": "Test1234",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": null,
    "userIdentities": [{
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ],
    "otherMails": ["sara@live.com"],
    "userPrincipalName": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8@tenant-name.onmicrosoft.com"
}
```
## <a name="migrate-social-account-with-local-account"></a>Sociaal account migreren met lokaal account
Als u een gecombineerd lokaal account wilt maken met sociale identiteiten, verzendt u een HTTPS POST-aanvraag naar de Graph API. De aanvraag tekst bevat de eigenschappen van de gebruiker van het sociaal-account om te maken, inclusief de aanmeldings naam voor het lokale account. U moet mini maal de vereiste eigenschappen opgeven.

**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Volgende formulier gegevens verzenden:

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "5164db16-3eee-4629-bfda-dcc3326790e9",
    "signInNames": [{
            "type": "emailAddress",
            "value": "david@contoso.com"
        }
    ],
    "creationType": "LocalAccount",
    "displayName": "David Hor",
    "givenName": "David",
    "surname": "Hor",
    "passwordProfile": {
        "password": "1234567",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": "DisablePasswordExpiration,DisableStrongPassword",
    "userIdentities": [{
            "issuer": "contoso.com",
            "issuerUserId": "ZGF2aWRAY29udG9zby5jb20="
        }
    ],
    "otherMails": [],
    "userPrincipalName": "5164db16-3eee-4629-bfda-dcc3326790e9@tenant-name.onmicrosoft.com"
}
```

## <a name="frequently-asked-questions"></a>Veelgestelde vragen
### <a name="how-can-i-know-the-issuer-name"></a>Hoe kan ik de naam van de verlener weten?
De naam van de verlener of de naam van de identiteits provider is geconfigureerd in uw beleid. Als u niet weet welke waarde u moet opgeven in `issuer`, volgt u deze procedure:
1. Meld u aan met een van de sociale accounts
2. Kopieer de `sub` waarde vanuit het JWT-token. De `sub` bevat meestal de object-ID van de gebruiker in Azure AD B2C. Of van Azure Portal, open de eigenschappen van de gebruiker en kopieer de object-ID.
3. Open [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net)
4. Meld u aan met uw beheerder.
5. Voer de volgende GET-aanvraag uit. Vervang de userObjectId door de gebruikers-ID die u hebt gekopieerd. https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId **ophalen**
6. Zoek het `userIdentities`-element in het JSON-resultaat van Azure AD B2C.
7. Beschrijving Misschien wilt u ook de `issuerUserId` waarde decoderen.

> [!NOTE]
> Gebruik een B2C Tenant beheerders account dat lokaal is voor de B2C-Tenant. De syntaxis van de account naam is admin@tenant-name.onmicrosoft.com.

### <a name="is-it-possible-to-add-a-social-identity-to-an-existing-user"></a>Is het mogelijk om een sociale identiteit toe te voegen aan een bestaande gebruiker?
Ja. U kunt de sociale identiteit toevoegen nadat het Azure AD B2C account is gemaakt (of dat een lokaal of sociaal account is, of een combi natie hiervan). Voer een HTTPS-PATCH aanvraag uit. Vervang de userObjectId door de gebruikers-ID die u wilt bijwerken.

**PATCH** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Volgende formulier gegevens verzenden:

```JSON
{
    "userIdentities": [
        {
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

### <a name="is-it-possible-to-add-multiple-social-identities"></a>Is het mogelijk meerdere sociale identiteiten toe te voegen?
Ja. U kunt meerdere sociale identiteiten voor één Azure AD B2C account toevoegen. Voer een HTTPS-PATCH aanvraag uit. Vervang de userObjectId door de gebruikers-ID.

**PATCH** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Volgende formulier gegevens verzenden:

```JSON
{
    "userIdentities": [
        {
            "issuer": "google.com",
            "issuerUserId": "MjQzMjE2NTc4NTQ="
        },
        {
            "issuer": "facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

## <a name="optional-user-migration-application-sample"></a>Beschrijving Voor beeld van toepassing voor gebruikers migratie
[Down load de voor beeld-app v2 en voer deze uit](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). De voor beeld-app v2 gebruikt een JSON-bestand dat dummy gebruikers gegevens bevat, waaronder: lokaal account, sociaal account en lokale & sociale identiteiten in één account.  Als u het JSON-bestand wilt bewerken, opent u de `AADB2C.UserMigration.sln` Visual Studio-oplossing. Open in het `AADB2C.UserMigration` project het `UsersData.json` bestand. Het bestand bevat een lijst met gebruikers entiteiten. Elke gebruikers entiteit heeft de volgende eigenschappen:
* **signInName** : voor het lokale account, het e-mail adres voor aanmelding
* **DisplayName** : weergave naam van gebruiker
* **FirstName** -de voor naam van de gebruiker
* **LastName** : de achternaam van de gebruiker
* **wacht woord** Voor het lokale account, het wacht woord van de gebruiker (kan leeg zijn)
* **verlener** : voor sociaal account, de naam van de ID-provider
* **issuerUserId** : de unieke gebruikers-id die wordt gebruikt door de ID-provider voor sociale netwerken. De waarde moet in Lees bare tekst zijn. De voor beeld-app codeert deze waarde in base64.
* **e-mail adres** Alleen voor het e-mail adres van de gebruiker (niet gecombineerd)

```JSON
{
  "userType": "emailAddress",
  "Users": [
    {
      // Local account only
      "signInName": "James@contoso.com",
      "displayName": "James Martin",
      "firstName": "James",
      "lastName": "Martin",
      "password": "Pass!w0rd"
    },
    {
      // Social account only
      "issuer": "Facebook.com",
      "issuerUserId": "1234567890",
      "email": "sara@contoso.com",
      "displayName": "Sara Bell",
      "firstName": "Sara",
      "lastName": "Bell"
    },
    {
      // Combine local account with social identity
      "signInName": "david@contoso.com",
      "issuer": "Facebook.com",
      "issuerUserId": "0987654321",
      "displayName": "David Hor",
      "firstName": "David",
      "lastName": "Hor",
      "password": "Pass!w0rd"
    }
  ]
}
```

> [!NOTE]
> Als u het bestand UsersData. json in het voor beeld niet bijwerkt met uw gegevens, kunt u zich aanmelden met de referenties van het voor beeld van een lokaal account, maar niet met de voor beelden van het sociale account. Geef echte gegevens op om uw sociale accounts te migreren.

Zie [Azure Active Directory B2C: gebruikers migratie](user-migration.md) voor meer informatie over het gebruik van de voor beeld-app.
