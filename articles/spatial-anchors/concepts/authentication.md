---
title: Verificatie en autorisatie
description: Meer informatie over de verschillende manieren waarop een app of service kan worden geverifieerd voor Azure spatiale ankers en de niveaus van beheer die u nodig hebt om toegang te krijgen tot ruimtelijke ankers van Azure.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 8a64c8cabe91bb7bbfb533b38a32f58a82fd3351
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84434381"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Verificatie en autorisatie voor Azure spatiale ankers

In deze sectie worden de verschillende manieren beschreven waarop u kunt verifiëren voor Azure spatiale ankers van uw app of webservice, en de manieren waarop u Access Control op basis van rollen in azure Directory (Azure AD) kunt gebruiken om de toegang tot uw spatiale ankers-accounts te beheren.

## <a name="overview"></a>Overzicht

![Een overzicht van verificatie voor Azure spatiale ankers](./media/spatial-anchors-authentication-overview.png)

Om toegang te krijgen tot een bepaald Azure spatiale-anker account moeten clients eerst een toegangs token verkrijgen van de Azure Mixed Reality-beveiligings token service (STS). Tokens die zijn verkregen van STS Live gedurende 24 uur en die informatie bevatten over de spatiale ankers Services om autorisatie beslissingen te nemen voor het account en ervoor te zorgen dat alleen geautoriseerde principals toegang hebben tot dat account.

Toegangs tokens kunnen worden verkregen in Exchange van een van beide account sleutels of van Azure AD-uitgegeven tokens.

Met account sleutels kunt u snel aan de slag met de Azure spatiale-ankers service. voordat u uw toepassing implementeert voor productie, wordt u echter aangeraden uw app bij te werken voor het gebruik van verificatie op basis van Azure AD.

Azure AD-verificatie tokens kunnen op twee manieren worden verkregen:

- Als u een bedrijfs toepassing bouwt en uw bedrijf Azure AD als identiteits systeem gebruikt, kunt u gebruikmaken van Azure AD-verificatie op basis van gebruikers in uw app en toegang verlenen tot uw ruimtelijke-anker accounts met behulp van uw bestaande Azure AD-beveiligings groepen of rechtstreeks aan gebruikers in uw organisatie.
- Anders is het raadzaam om Azure AD-tokens te verkrijgen van een webservice die uw app ondersteunt. Het gebruik van een ondersteunende webservice is de aanbevolen verificatie methode voor productie toepassingen, omdat hiermee wordt voor komen dat de referenties worden inge sloten voor toegang tot Azure spatiale ankers in uw client toepassing.

## <a name="account-keys"></a>Account sleutels

Het gebruik van account sleutels voor toegang tot uw Azure spatiale-ankers account is de eenvoudigste manier om aan de slag te gaan. U vindt uw account sleutels op het Azure Portal. Navigeer naar uw account en selecteer het tabblad sleutels.

![Een overzicht van verificatie voor Azure spatiale ankers](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)

Er worden twee sleutels beschikbaar gemaakt, die beide gelijktijdig geldig zijn voor toegang tot het ruimtelijke-ankers account. Het is raadzaam om de sleutel die u gebruikt voor toegang tot het account, regel matig bij te werken. Als er twee afzonderlijke geldige sleutels zijn, kunnen deze updates zonder downtime worden ingeschakeld. u hoeft alleen de primaire sleutel en de secundaire sleutel bij te werken.

De SDK heeft ingebouwde ondersteuning voor verificatie met account sleutels; u hoeft alleen de eigenschap AccountKey in te stellen voor uw cloudSession-object.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objc"></a>[ObjC](#tab/objc)

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

# <a name="c-ndk"></a>[NDK VOOR C++](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrt"></a>[WinRT van C++](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

Als dat is gebeurd, wordt de uitwisseling van de account sleutel voor een toegangs token door de SDK verwerkt en worden de tokens die u nodig hebt in de cache opgeslagen voor uw app.

> [!WARNING]
> Het gebruik van account sleutels wordt aanbevolen voor een snelle on-boarding, maar tijdens ontwikkeling/prototypen. Het wordt ten zeerste aanbevolen om uw toepassing niet naar productie te verzenden met behulp van een Inge sloten account sleutel. in plaats daarvan wordt gebruikgemaakt van de op gebruikers of service gebaseerde Azure AD-verificatie benaderingen die hieronder worden weer gegeven.

## <a name="azure-ad-user-authentication"></a>Gebruikers verificatie van Azure AD

Voor toepassingen die zijn gericht Azure Active Directory gebruikers, is de aanbevolen benadering een Azure AD-token voor de gebruiker te gebruiken, dat u kunt verkrijgen met behulp van de [MSAL-bibliotheek](../../active-directory/develop/msal-overview.md). Voer de stappen uit die worden vermeld in de [Snelstartgids een app registreren](../../active-directory/develop/quickstart-register-app.md), waaronder:

1. Configuratie in Azure Portal
    1.  Registreer uw toepassing in azure AD als **systeem eigen toepassing**. Als onderdeel van de registratie moet u bepalen of uw toepassing multi tenant moet zijn of niet, en de omleidings-Url's opgeven die voor uw toepassing zijn toegestaan.
        1.  Overschakelen naar het tabblad **API-machtigingen**
        2.  Selecteer **een machtiging toevoegen**
            1.  Selecteer **micro soft Mixed Reality** onder **api's mijn organisatie gebruikt** tabblad
            2.  **Gedelegeerde machtigingen** selecteren
            3.  Schakel het selectie vakje in voor **mixedreality. signin** onder **mixedreality**
            4.  **Machtigingen toevoegen** selecteren
        3.  **Toestemming van beheerder verlenen** selecteren
    2.  Verleen uw toepassing of gebruikers toegang tot uw resource:
        1.  Ga naar de resource met ruimtelijke ankers in Azure Portal
        2.  Overschakelen naar het tabblad **toegangs beheer (IAM)**
        3.  **Toewijzing van roltoewijzing toevoegen**
            1.  [Een rol selecteren](#role-based-access-control)
            2.  Voer in het veld **selecteren** de naam in van de gebruiker (s), groep (en) en/of toepassing (en) waaraan u toegang wilt toewijzen.
            3.  Druk op **Opslaan**.
2. In uw code:
    1.  Zorg ervoor dat u de **toepassings-id** en **omleidings-URI** van uw eigen Azure AD-toepassing gebruikt als de **client-id** en **RedirectUri** para meters in MSAL
    2.  Stel de Tenant gegevens in:
        1.  Als uw toepassing **alleen mijn organisatie**ondersteunt, vervangt u deze waarde door uw **Tenant-id** of **Tenant naam** (bijvoorbeeld contoso.Microsoft.com)
        2.  Als uw toepassing **accounts in een organisatorische Directory**ondersteunt, vervangt u deze waarde door **organisaties**
        3.  Als uw toepassing **alle Microsoft-account gebruikers**ondersteunt, vervangt u deze waarde door **common**
    3.  Stel op uw token aanvraag het **bereik** in op ' https://sts.mixedreality.azure.com//.default '. Met dit bereik wordt aan Azure AD aangegeven dat uw toepassing een token aanvraagt voor de STS (Mixed Reality Security Token Service).

Met dat moet uw toepassing MSAL een Azure AD-token kunnen verkrijgen. u kunt dat Azure AD-token instellen als de **authenticationToken** in uw Cloud sessie configuratie object.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

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

# <a name="c-ndk"></a>[NDK VOOR C++](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrt"></a>[WinRT van C++](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Verificatie van Azure AD-service

De aanbevolen optie voor het implementeren van apps met behulp van Azure spatiale ankers naar productie is het gebruik van een back-end-service die Broker-verificatie aanvragen zal gebruiken. Het algemene schema moet worden beschreven in dit diagram:

![Een overzicht van verificatie voor Azure spatiale ankers](./media/spatial-anchors-aad-authentication.png)

Hierbij wordt ervan uitgegaan dat uw app een eigen mechanisme gebruikt (bijvoorbeeld: Microsoft-account, PlayFab, Facebook, Google ID, aangepaste gebruikers naam/wacht woord, enzovoort) om te verifiëren bij de back-upservice. Zodra uw gebruikers zijn geverifieerd voor uw back-end-service, kan die service een Azure AD-Token ophalen, dit uitwisselen voor een toegangs token voor Azure spatiale ankers en het weer terugsturen naar uw client toepassing.

Het Azure AD-toegangs token wordt opgehaald met behulp van de [MSAL-bibliotheek](../../active-directory/develop/msal-overview.md). Voer de stappen uit die worden vermeld in de [Snelstartgids een app registreren](../../active-directory/develop/quickstart-register-app.md), waaronder:

1.  Configuratie in Azure Portal:
    1.  Registreer uw toepassing in azure AD:
        1.  Ga in Azure Portal naar **Azure Active Directory**en selecteer app- **registraties**
        2.  **Nieuwe toepassings registratie** selecteren
        3.  Voer de naam van uw toepassing in, selecteer **Web-app/API** als het toepassings type en voer de verificatie-URL voor uw service in. Klik vervolgens op **maken**.
        4.  Klik in deze toepassing op **instellingen**en selecteer vervolgens het tabblad **sleutels** . Voer de naam van de sleutel in, selecteer een duur en druk op **Opslaan**. Zorg ervoor dat u de sleutel waarde opslaat die op dat moment wordt weer gegeven, omdat u deze moet gebruiken in de code van de webservice.
    2.  Verleen uw toepassing en/of gebruikers toegang tot uw resource:
        1.  Ga naar de resource met ruimtelijke ankers in Azure Portal
        2.  Overschakelen naar het tabblad **toegangs beheer (IAM)**
        3.  **Toewijzing van roltoewijzing toevoegen**
        1.  [Een rol selecteren](#role-based-access-control)
        2.  Voer in het veld **selecteren** de naam in van de toepassing (en) die u hebt gemaakt en waaraan u toegang wilt toewijzen. Als u wilt dat de gebruikers van uw app verschillende rollen hebben voor het ruimtelijke-ankers account, moet u meerdere toepassingen in azure AD registreren en aan elke afzonderlijke rol toewijzen. Implementeer vervolgens uw autorisatie logica om de juiste rol voor uw gebruikers te gebruiken.
    3.  Druk op **Opslaan**.
2.  In uw code (Opmerking: u kunt het service voorbeeld gebruiken dat is opgenomen in GitHub):
    1.  Zorg ervoor dat u de toepassings-ID, het toepassings geheim en de omleidings-URI van uw eigen Azure AD-toepassing gebruikt als de client-ID, het geheim en de RedirectUri-para meters in MSAL
    2.  Stel de Tenant-ID in op uw eigen Azure ADD Tenant-ID in de para meter Authority in MSAL.
    3.  Stel op uw token aanvraag het **bereik** in op https://sts.mixedreality.azure.com//.default

Met dat kan uw back-end-service een Azure AD-Token ophalen. Vervolgens kan het worden uitgewisseld voor een MR-token dat wordt teruggestuurd naar de client. Het gebruik van een Azure AD-token om een MR-token op te halen, wordt uitgevoerd via een REST-aanroep. Hier volgt een voor beeld van een oproep:

```
GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: sts.mixedreality.azure.com
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

Als de autorisatie-header er als volgt uitziet:`Bearer <Azure_AD_token>`

En het antwoord bevat het MR-token in tekst zonder opmaak.

Dat MR-token vervolgens wordt geretourneerd naar de client. Uw client-app kan deze vervolgens instellen als toegangs token in de Cloud sessie configuratie.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

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

# <a name="c-ndk"></a>[NDK VOOR C++](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrt"></a>[WinRT van C++](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

Om het niveau van toegang te beheren dat wordt verleend aan toepassingen, services of Azure AD-gebruikers van uw service, zijn de volgende rollen gemaakt zodat u deze kunt toewijzen aan uw Azure Spatial-ankers-accounts:

- **Eigenaar van ruimtelijk ankers account**: toepassingen of gebruikers die deze rol hebben, kunnen ruimtelijke ankers maken, query's uitvoeren en deze verwijderen. Wanneer u uw account met behulp van account sleutels verifieert, wordt de rol van de **account eigenaar van ruimtelijke ankers** toegewezen aan de geverifieerde principal.
- **Inzender voor ruimtelijke ankers**: toepassingen of gebruikers die deze rol hebben, kunnen ruimtelijke ankers maken, er query's op uitvoeren, maar ze kunnen ze niet verwijderen.
- **Account lezer van ruimtelijk ankers**: toepassingen of gebruikers die deze rol hebben, kunnen alleen een query uitvoeren op ruimtelijke ankers, maar kunnen geen nieuwe maken, bestaande verwijderen of meta gegevens bijwerken op ruimtelijke ankers. Dit wordt doorgaans gebruikt voor toepassingen waarbij sommige gebruikers de omgeving opzeggen, terwijl anderen alleen ankers kunnen intrekken die eerder in deze omgeving zijn geplaatst.

## <a name="next-steps"></a>Volgende stappen

Maak uw eerste app met Azure spatiale ankers.

> [!div class="nextstepaction"]
> [Eenheid (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](../quickstarts/get-started-unity-android.md)

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
