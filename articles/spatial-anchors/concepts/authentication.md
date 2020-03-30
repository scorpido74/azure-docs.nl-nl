---
title: Verificatie en autorisatie
description: Meer informatie over de verschillende manieren waarop een app of service kan verifiëren voor Azure Spatial Anchors en de controleniveaus die u moet openen tot Azure Spatial Anchors.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: df27a77c202951a6c789703f12712e75bd8b5906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656968"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Verificatie en autorisatie voor azure-ruimtelijke ankers

In deze sectie behandelen we de verschillende manieren waarop u verifiëren voor Azure Spatial Anchors vanuit uw app of webservice en op welke manieren u op rollen gebaseerd toegangsbeheer gebruiken in Azure Directory (Azure AD) om de toegang tot uw Ruimteankers-accounts te beheren.

## <a name="overview"></a>Overzicht

![Een overzicht van verificatie naar Azure Spatial Anchors](./media/spatial-anchors-authentication-overview.png)

Om toegang te krijgen tot een bepaald Azure Spatial Anchors-account, moeten clients eerst een toegangstoken verkrijgen van Azure Mixed Reality Security Token Service (STS). Tokens die zijn verkregen van STS leven 24 uur en bevatten informatie voor de diensten voor ruimtelijke ankers om autorisatiebeslissingen te nemen over het account en ervoor te zorgen dat alleen geautoriseerde opdrachtgevers toegang hebben tot dat account.

Toegangstokens kunnen worden verkregen in ruil voor accountsleutels of van door Azure uitgegeven tokens.

Met accountsleutels u snel aan de slag met het gebruik van de Azure Spatial Anchors-service. Voordat u uw toepassing echter implementeert in productie, wordt u aangeraden uw app bij te werken om verificatie op basis van Azure AD te gebruiken.

Azure AD-verificatietokens kunnen op twee manieren worden verkregen:

- Als u een bedrijfstoepassing bouwt en uw bedrijf Azure AD als identiteitssysteem gebruikt, u op gebruikers gebaseerde Azure AD-verificatie gebruiken in uw app en toegang verlenen tot uw ruimtelijke ankersaccounts met uw bestaande Azure AD-beveiligingsgroepen, of rechtstreeks naar gebruikers in uw organisatie.
- Anders wordt aanbevolen azure AD-tokens te verkrijgen van een webservice die uw app ondersteunt. Het gebruik van een ondersteunende webservice is de aanbevolen verificatiemethode voor productietoepassingen, omdat hiermee wordt voorkomen dat de referenties voor toegang tot Azure Spatial Anchors in uw clienttoepassing worden ingesloten.

## <a name="account-keys"></a>Accountsleutels

Het gebruik van accountsleutels voor toegang tot uw Azure Spatial Anchors-account is de eenvoudigste manier om aan de slag te gaan. U vindt uw accountsleutels op de Azure-portal. Navigeer naar uw account en selecteer het tabblad Sleutels.

![Een overzicht van verificatie naar Azure Spatial Anchors](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


Er worden twee sleutels beschikbaar gesteld, die beide tegelijkertijd geldig zijn voor toegang tot het account Ruimtelijke Ankers. Het wordt aanbevolen dat u de sleutel die u gebruikt om toegang te krijgen tot het account regelmatig bijwerkt; het hebben van twee afzonderlijke geldige sleutels maken dergelijke updates zonder downtime mogelijk; u hoeft alleen de primaire sleutel en de secundaire sleutel bij te werken.

De SDK heeft ingebouwde ondersteuning voor het authenticeren met accountsleutels; u hoeft alleen maar de eigenschap AccountKey in te stellen op uw cloudSession-object.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objc"></a>[ObjC ObjC](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

Zodra dat is gebeurd, zal de SDK de uitwisseling van de accountsleutel voor een toegangstoken en de benodigde caching van tokens voor uw app afhandelen.

> [!WARNING]
> Het gebruik van accountsleutels wordt aanbevolen voor snel instappen, maar alleen tijdens de ontwikkeling/prototyping. Het wordt ten zeerste aanbevolen om uw toepassing niet naar productie te verzenden met behulp van een ingesloten accountsleutel en in plaats daarvan de volgende azure ad-verificatiebenaderingen op basis van de gebruiker of service te gebruiken.

## <a name="azure-ad-user-authentication"></a>Azure AD-gebruikersverificatie

Voor toepassingen die zijn gericht op Azure Active Directory-gebruikers, is de aanbevolen aanpak het gebruik van een Azure AD-token voor de gebruiker, dat u verkrijgen met behulp van de [MSAL-bibliotheek.](../../active-directory/develop/msal-overview.md) Volg de stappen die het [register van een app snelstart,](../../active-directory/develop/quickstart-register-app.md)waaronder:

1. Configuratie in Azure-portal
    1.  Registreer uw toepassing in Azure AD als **Native-toepassing**. Als onderdeel van het registreren moet u bepalen of uw toepassing multi-tenant moet zijn of niet, en de toegestane omleidings-URL's voor uw toepassing verstrekken.
        1.  Overschakelen naar het tabblad **API-machtigingen**
        2.  Een **machtiging toevoegen selecteren**
            1.  **Selecteer Mixed Reality Resource Provider** onder **API's die mijn organisatie gebruikt**
            2.  **Gedelegeerde machtigingen selecteren**
            3.  Check het vakje voor **mixedreality.signin** onder **mixedreality**
            4.  Machtigingen **toevoegen selecteren**
        3.  Toestemming **voor beheerders verlenen selecteren**
    2.  Geef uw toepassing of gebruikers toegang tot uw bron:
        1.  Navigeren naar uw bron voor ruimtelijke ankers in Azure-portal
        2.  Overschakelen naar het tabblad **Toegangsbesturingselement (IAM)**
        3.  Roltoewijzing **toevoegen**
            1.  [Een rol selecteren](#role-based-access-control)
            2.  Voer in het veld **Selecteren** de naam in van de gebruiker(s), groep(en) en/of toepassings(en) waaraan u toegang wilt toewijzen.
            3.  Druk op **Opslaan**.
2. In uw code:
    1.  Zorg ervoor dat u de **toepassings-id** gebruikt en Uri van uw eigen Azure AD-toepassing **omleidt** als de **client-id** en **RedirectUri-parameters** in ADAL
    2.  Stel de tenantgegevens in:
        1.  Als uw toepassing **alleen Mijn organisatie**ondersteunt, vervangt u deze waarde door uw **tenant-id** of **tenantnaam** (bijvoorbeeld contoso.microsoft.com)
        2.  Als uw toepassing **Accounts in een organisatiemap**ondersteunt, vervangt u deze waarde door **Organisaties**
        3.  Als uw toepassing **alle gebruikers van microsoft-account**ondersteunt, vervangt u deze waarde door **Gemeenschappelijk**
    3.  Stel op uw tokenverzoek dehttps://sts.mixedreality.azure.com **resource** in op " ". Deze 'resource' geeft Azure AD aan dat uw toepassing een token aanvraagt voor de Azure Spatial Anchors-service.

Daarmee moet uw toepassing van MSAL een Azure AD-token kunnen verkrijgen; u dat Azure AD-token instellen als het **verificatietoken** voor uw cloudsessie-config-object.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objc"></a>[ObjC ObjC](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Azure AD-serviceverificatie

De aanbevolen optie om apps te implementeren die Gebruikmaken van Azure Spatial Anchors voor productie, is het gebruik maken van een backendservice die verificatieverzoeken voormakelaars mogelijk maakt. De algemene regeling moet worden zoals beschreven in dit diagram:

![Een overzicht van verificatie naar Azure Spatial Anchors](./media/spatial-anchors-aad-authentication.png)

Hier wordt ervan uitgegaan dat uw app zijn eigen mechanisme gebruikt (bijvoorbeeld: Microsoft-account, PlayFab, Facebook, Google ID, aangepaste gebruikersnaam/wachtwoord, enz.) om zich te authenticeren naar de backendservice. Zodra uw gebruikers zijn geverifieerd naar uw backendservice, kan die service een Azure AD-token ophalen, het inruilen voor een toegangstoken voor Azure Spatial Anchors en het terugsturen naar uw clienttoepassing.

Het Azure AD-toegangstoken wordt opgehaald met de [MSAL-bibliotheek.](../../active-directory/develop/msal-overview.md) Volg de stappen die het [register van een app snelstart,](../../active-directory/develop/quickstart-register-app.md)waaronder:

1.  Configuratie in Azure-portal:
    1.  Uw toepassing registreren in Azure AD:
        1.  Navigeer in Azure-portal naar **Azure Active Directory**en selecteer **app-registraties**
        2.  Nieuwe **toepassingsregistratie selecteren**
        3.  Voer de naam van uw toepassing in, selecteer **Web-app / API** als toepassingstype en voer de auth-URL voor uw service in. Druk vervolgens op **Maken**.
        4.  Klik in die toepassing op **Instellingen**en selecteer vervolgens het tabblad **Toetsen.** Voer de naam van uw sleutel in, selecteer een duur en druk op **Opslaan**. Zorg ervoor dat u de sleutelwaarde opslaat die op dat moment wordt weergegeven, omdat u deze in de code van uw webservice moet opnemen.
    2.  Geef uw toepassing en/of gebruikers toegang tot uw bron:
        1.  Navigeren naar uw bron voor ruimtelijke ankers in Azure-portal
        2.  Overschakelen naar het tabblad **Toegangsbesturingselement (IAM)**
        3.  Roltoewijzing **toevoegen**
        1.  [Een rol selecteren](#role-based-access-control)
        2.  Voer **in** het veld Selecteren de naam in van de aanmaak(en) die u hebt gemaakt en waaraan u toegang wilt toewijzen. Als u wilt dat de gebruikers van uw app verschillende rollen hebben ten opzichte van het account Ruimtelijke ankers, moet u meerdere toepassingen registreren in Azure AD en aan elk een afzonderlijke rol toewijzen. Implementeer vervolgens uw autorisatielogica om de juiste rol voor uw gebruikers te gebruiken.
    3.  Druk op **Opslaan**.
2.  In uw code (let op: u het servicevoorbeeld op GitHub gebruiken):
    1.  Zorg ervoor dat u de toepassings-id, toepassingsgeheim en omleidingsuri van uw eigen Azure AD-toepassing gebruikt als de parameters client-id, geheim en RedirectUri in ADAL
    2.  De tenant-id instellen op uw eigen AAAzure ADD-tenant-id in de parameter autoriteit in ADAL
    3.  Stel op uw tokenverzoek dehttps://sts.mixedreality.azure.com **resource** in op "

Hiermee kan uw backendservice een Azure AD-token ophalen. Het kan het dan ruilen voor een MR-token dat het terug zal keren naar de client. Het gebruik van een Azure AD-token om een MR-token op te halen, wordt gedaan via een REST-gesprek. Hier is een voorbeeld oproep:

```
GET https://mrc-auth-prod.trafficmanager.net/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: mrc-auth-prod.trafficmanager.net
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

Wanneer de koptekst Autorisatie als volgt is opgemaakt:`Bearer <accoundId>:<accountKey>`

En het antwoord bevat het MR-token in platte tekst.

Die MR-token wordt vervolgens teruggegeven aan de klant. Uw client-app kan deze vervolgens instellen als toegangstoken in de cloudsessie-config.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objc"></a>[ObjC ObjC](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

Om het toegangsniveau voor toepassingen, services of Azure AD-gebruikers van uw service te beheren, zijn de volgende rollen gemaakt die u zo nodig toewijzen aan uw Azure Spatial Anchors-accounts:

- **Beheervan de accounteigenaar ruimtelijke ankers**: toepassingen of gebruikers die deze rol hebben, kunnen ruimtelijke ankers maken, voor hen zoeken en verwijderen. Wanneer u zich verifieert naar uw account met accountsleutels, wordt de rol De rol Van de eigenaar van de **accountvoorankers** toegewezen aan de geverifieerde hoofdsom.
- **Accountinzender Ruimtelijke ankers:** toepassingen of gebruikers die deze rol hebben, kunnen ruimtelijke ankers maken, er query's voor maken, maar kunnen deze niet verwijderen.
- **Ruimtelijke ankers Account Reader**: toepassingen of gebruikers die deze rol hebben zijn alleen in staat om te zoeken naar ruimtelijke ankers, maar kunnen geen nieuwe maken, bestaande plaatsen of metadata bijwerken op ruimtelijke ankers. Dit wordt meestal gebruikt voor toepassingen waar sommige gebruikers de omgeving beheren, terwijl anderen alleen ankers kunnen herinneren die eerder in die omgeving zijn geplaatst.

## <a name="next-steps"></a>Volgende stappen

Maak uw eerste app met Azure Spatial Anchors.

> [!div class="nextstepaction"]
> [Eenheid (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Eenheid (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Eenheid (Android)](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](../quickstarts/get-started-xamarin-ios.md)
