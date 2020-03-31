---
title: App-rollen toevoegen en deze ophalen via een token | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het toevoegen van app-rollen in een toepassing die is geregistreerd `roles` in Azure Active Directory, gebruikers en groepen aan deze rollen toewijzen en deze ontvangen in de claim in het token.
services: active-directory
documentationcenter: ''
author: kkrishna
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 3a911db36fd03ebcb5e0fc53d4d7f36d68648249
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399085"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>How to: App-rollen toevoegen aan uw toepassing en deze ontvangen in het token

Role-based access control (RBAC) is een populair mechanisme om autorisatie in toepassingen af te dwingen. Bij het gebruik van RBAC verleent een beheerder machtigingen voor rollen en niet aan individuele gebruikers of groepen. De beheerder kan vervolgens rollen toewijzen aan verschillende gebruikers en groepen om te bepalen wie toegang heeft tot welke inhoud en functionaliteit.

Met Behulp van RBAC met toepassingsrollen en rolclaims kunnen ontwikkelaars de autorisatie in hun apps veilig afdwingen met weinig moeite van hun kant.

Een andere benadering is het gebruik van Azure AD-groepen en groepsclaims, zoals weergegeven in [WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet). Azure AD-groepen en toepassingsrollen sluiten elkaar geenszins uit; ze kunnen worden gebruikt in tandem om nog fijnere korrelige toegangscontrole te bieden.

## <a name="declare-roles-for-an-application"></a>Rollen voor een toepassing declareren

Deze toepassingsrollen worden gedefinieerd in de [Azure-portal](https://portal.azure.com) in het registratiemanifest van de toepassing.  Wanneer een gebruiker zich aanmeldt bij de `roles` toepassing, zendt Azure AD een claim uit voor elke rol die de gebruiker afzonderlijk heeft gekregen aan de gebruiker en vanuit zijn groepslidmaatschap.  Toewijzing van gebruikers en groepen aan rollen kan worden gedaan via de gebruikersinterface van de portal of programmatisch met Behulp van [Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/azuread-identity-access-management-concept-overview).

### <a name="declare-app-roles-using-azure-portal"></a>App-rollen declareren met Azure-portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het pictogram **Directory + Abonnement** op de werkbalk van de portal.
1. Kies **in** de lijst Favorieten of **Alle mappen** de Active Directory-tenant waar u uw aanvraag wilt registreren.
1. Zoek en selecteer **Azure Active Directory**in de Azure-portal.
1. Selecteer **app-registraties** in het deelvenster **Azure Active Directory** om een lijst met al uw toepassingen weer te geven.
1. Selecteer de toepassing waarin u app-rollen wilt definiëren. Selecteer vervolgens **Manifest**.
1. Bewerk het app-manifest `appRoles` door de instelling te lokaliseren en al uw toepassingsrollen toe te voegen.

     > [!NOTE]
     > Elke app-roldefinitie in dit manifest moet een andere geldige GUID hebben in de context van het manifest voor de `id` eigenschap.
     >
     > De `value` eigenschap van elke app-roldefinitie moet exact overeenkomen met de tekenreeksen die worden gebruikt in de code in de toepassing. De `value` eigenschap mag geen spaties bevatten. Als dit het wel het zo is, ontvangt u een foutmelding wanneer u het manifest opslaat.

1. Bewaar het manifest.

### <a name="examples"></a>Voorbeelden

In het volgende `appRoles` voorbeeld ziet `users`u de aan wie u toewijzen.

> [!NOTE]
>Het `id` moet een unieke GUID zijn.

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-98f8-45fd-aa4a-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

> [!NOTE]
>Deze `displayName` mogen geen spaties bevatten.

U app-rollen `users` `applications`definiëren om te targeten, of beide. Wanneer beschikbaar `applications`voor , worden app-rollen weergegeven als toepassingsmachtigingen in het blad **Vereiste machtigingen.** In het volgende voorbeeld ziet `Application`u een app-rol die is gericht op een .

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-859a-4941-89c9-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

Het aantal gedefinieerde rollen is van invloed op de limieten die het toepassingsmanifest heeft. Ze zijn uitvoerig besproken op de [manifestlimietenpagina.](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest#manifest-limits)

### <a name="assign-users-and-groups-to-roles"></a>Gebruikers en groepen toewijzen aan rollen

Zodra u app-rollen in uw toepassing hebt toegevoegd, u gebruikers en groepen aan deze rollen toewijzen.

1. Selecteer In het deelvenster **Azure Active Directory** de optie **Enterprise-toepassingen** in het navigatiemenu **azure Active Directory** met de linker.
1. Selecteer **Alle toepassingen** selecteren om een lijst met al uw toepassingen weer te geven.

     Als u de gewenste toepassing niet ziet, gebruikt u de verschillende filters boven aan de lijst **Alle toepassingen** om de lijst te beperken of omlaag te scrollen om uw toepassing te vinden.

1. Selecteer de toepassing waarin u gebruikers of beveiligingsgroepen aan rollen wilt toewijzen.
1. Selecteer het deelvenster **Gebruikers en groepen** in het linkernavigatiemenu van de toepassing.
1. Selecteer boven aan de lijst **Gebruikers en groepen** de knop Gebruiker **toevoegen** om het deelvenster **Toewijzing toevoegen** te openen.
1. Selecteer de selectie **voor gebruikers en groepen** in het deelvenster Toewijzing **toevoegen.**

     Een lijst met gebruikers en beveiligingsgroepen wordt weergegeven samen met een tekstvak om een bepaalde gebruiker of groep te zoeken en te lokaliseren. Met dit scherm u meerdere gebruikers en groepen in één keer selecteren.

1. Zodra u klaar bent met het selecteren van de gebruikers en groepen, drukt u op de knop **Selecteren** onderaan om naar het volgende deel te gaan.
1. Kies de **kiezer voor rol selecteren** in het deelvenster Toewijzing **toevoegen.** Alle eerder in het app-manifest gedeclareerde rollen worden weergegeven.
1. Kies een rol en druk op de knop **Selecteren.**
1. Druk op de knop **Toewijzen** onderaan om de toewijzingen van gebruikers en groepen aan de app te voltooien.
1. Controleer of de gebruikers en groepen die u hebt toegevoegd, worden weergegeven in de lijst met bijgewerkte **gebruikers en groepen.**

## <a name="more-information"></a>Meer informatie

- [Autorisatie toevoegen met behulp van app-rollen & rollen claims naar een ASP.NET Core web-app](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Beveiligingsgroepen en toepassingsrollen gebruiken in uw apps (Video)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Azure Active Directory, nu met groepsclaims en toepassingsrollen](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Azure Active Directory-app-manifest](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)
- [AAD Access-tokens](access-tokens.md)
- [Aad`id_tokens`](id-tokens.md)
