---
title: Verificatie
description: Geeft uitleg over de werking van verificatie
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: how-to
ms.openlocfilehash: 8f3b144a7790c3122d59d27183b3037998ddadd1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85565851"
---
# <a name="configure-authentication"></a>Verificatie configureren

Azure remote rendering maakt gebruik van hetzelfde authenticatie mechanisme als [Azure spatiale ankers (ASA)](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp). Clients moeten *een* van de volgende opties instellen om de rest-api's te kunnen aanroepen:

* **AccountKey**: kan worden verkregen op het tabblad sleutels voor het externe rendering-account op de Azure Portal. Account sleutels worden alleen aanbevolen voor ontwikkeling/prototypen.
    ![Account-id](./media/azure-account-primary-key.png)

* **AuthenticationToken**: is een Azure AD-token, dat kan worden verkregen met behulp van de [MSAL-bibliotheek](https://docs.microsoft.com/azure/active-directory/develop/msal-overview). Er zijn meerdere stromen beschikbaar om gebruikers referenties te accepteren en deze referenties te gebruiken voor het verkrijgen van een toegangs token.

* **MRAccessToken**: is een Mr-token dat kan worden verkregen door de Azure Mixed Reality-beveiligings token service (STS). Opgehaald uit het `https://sts.mixedreality.azure.com` eind punt met behulp van een rest-aanroep die vergelijkbaar is met de onderstaande aanroep:

    ```rest
    GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
    Host: sts.mixedreality.azure.com
    Connection: Keep-Alive

    HTTP/1.1 200 OK
    Date: Tue, 24 Mar 2020 09:09:00 GMT
    Content-Type: application/json; charset=utf-8
    Content-Length: 1153
    Accept: application/json
    MS-CV: 05JLqWeKFkWpbdY944yl7A.0
    {"AccessToken":"eyJhbGciOiJSUzI1<truncated>uLkO2FvA"}
    ```

    Waarbij de autorisatie-header als volgt is ingedeeld: `Bearer <Azure_AD_token>` of `Bearer <accoundId>:<accountKey>` . De eerste is de voor keur voor beveiliging. Het token dat door deze REST-aanroep wordt geretourneerd, is het MR-toegangs token.

## <a name="authentication-for-deployed-applications"></a>Verificatie voor geïmplementeerde toepassingen

Account sleutels worden aanbevolen voor snel prototypen, tijdens de ontwikkeling. Het is raadzaam om uw toepassing niet naar productie te verzenden met behulp van een Inge sloten account sleutel. De aanbevolen methode is om een Azure AD-verificatie methode op basis van gebruikers of service te gebruiken.

 Azure AD-verificatie wordt beschreven in het gedeelte [Azure AD-gebruikers verificatie](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication) van de [Azure spatiale ankers (ASA)-](https://docs.microsoft.com/azure/spatial-anchors/) service.

 Zie voor meer informatie de [zelf studie: Azure remote rendering en model Storage-Azure Active Directory-verificatie beveiligen](../tutorials/unity/security/security.md#azure-active-directory-azure-ad-authentication)

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

Gebruik de volgende rollen bij het verlenen van toegang op basis van rollen om het toegangs niveau voor uw service te bepalen:

* **Externe rendering-beheerder**: biedt gebruikers de mogelijkheid om de mogelijkheden voor het omzetten van sessies, rendering en diagnose te beheren voor externe rendering van Azure.
* **Client voor externe rendering**: biedt gebruikers de mogelijkheid voor het beheren van sessies, rendering en diagnose voor de externe rendering van Azure.

## <a name="next-steps"></a>Volgende stappen

* [Een account maken](create-an-account.md)
* [De Azure frontend-Api's gebruiken voor verificatie](frontend-apis.md)
* [PowerShell-voorbeeldscripts](../samples/powershell-example-scripts.md)
