---
title: Een web-app registreren die gebruikers aantekent - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het registreren van een web-app die zich aanmeldt bij gebruikers
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f2513b1fee022199f040ba0dcf83110c0b3b1365
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701566"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>Web-app die zich aanmeldt bij gebruikers: App-registratie

In dit artikel worden de specificaties van de app-registratie uitgelegd voor een web-app die gebruikers aantekent.

Om uw aanvraag te registreren, u het:

- De [web-app start snel](#register-an-app-by-using-the-quickstarts). Naast het feit dat het een geweldige eerste ervaring met het maken van een toepassing, quickstarts in de Azure portal bevatten een knop met de naam **Maak deze wijziging voor mij**. U deze knop gebruiken om de eigenschappen in te stellen die u nodig hebt, zelfs voor een bestaande app. U moet de waarden van deze eigenschappen aanpassen aan uw eigen behuizing. In het bijzonder zal de URL van de web-API voor uw app waarschijnlijk anders zijn dan de voorgestelde standaardinstelling, wat ook van invloed zal zijn op de afmeldURI.
- De Azure-portal om uw toepassing handmatig te [registreren.](#register-an-app-by-using-the-azure-portal)
- PowerShell- en commandline-gereedschappen.

## <a name="register-an-app-by-using-the-quickstarts"></a>Een app registreren met de quickstarts

U deze koppelingen gebruiken om de creatie van uw webapplicatie op te start zetten:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>Een app registreren met de Azure-portal

> [!NOTE]
> De te gebruiken portal is anders, afhankelijk van of uw toepassing wordt uitgevoerd in de openbare Microsoft Azure-cloud of in een nationale of soevereine cloud. Zie [Nationale wolken voor](./authentication-national-cloud.md#app-registration-endpoints)meer informatie.


1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account. U zich ook aanmelden bij de Azure-portal naar keuze voor de nationale cloud.
1. Als uw account u toegang geeft tot meer dan één tenant, selecteert u uw account in de rechterbovenhoek. Stel vervolgens uw portalsessie in op de gewenste Azure Active Directory-tenant (Azure AD).
1. Selecteer in het linkerdeelvenster de **Azure Active Directory-service** en selecteer **App-registratienieuwe** > **registratie**.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
   1. Kies de ondersteunde accounttypen voor uw toepassing. (Zie [Ondersteunde accounttypen](./v2-supported-account-types.md).)
   1. Voer in de sectie **Naam** een betekenisvolle toepassingsnaam in die wordt weergegeven aan gebruikers van de app. Typ bijvoorbeeld **AspNetCore-WebApp**.
   1. Voeg voor **Omleiding URI**het type toepassing en de URI-bestemming toe die geretourneerde tokenreacties accepteert na succesvolle verificatie. Voer bijvoorbeeld **https://localhost:44321**. Selecteer vervolgens **Registreren**.
1. Selecteer het menu **Verificatie** en voeg dan de volgende gegevens toe:
   1. Voeg voor de **https://localhost:44321/signin-oidc** URL van **antwoord**het type **Web**toe .
   1. Stel in de sectie **Geavanceerde instellingen** de URL **van Aanmelding** in op **https://localhost:44321/signout-oidc**.
   1. Selecteer **id-tokens** onder **Impliciete toekenning**.
   1. Selecteer **Opslaan**.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

1. Wanneer de **pagina Een aanvraag registreren** wordt weergegeven, voert u de registratiegegevens van uw aanvraag in:
   1. Kies de ondersteunde accounttypen voor uw toepassing. (Zie [Ondersteunde accounttypen](./v2-supported-account-types.md).)
   1. Voer in de sectie **Naam** een betekenisvolle toepassingsnaam in die wordt weergegeven aan gebruikers van de app. Voer bijvoorbeeld **MailApp-openidconnect-v2 in.**
   1. Selecteer **web** in het keuzekader en voer in de sectie REDIRECT **https://localhost:44326/** **URI (optioneel)** de volgende omleidingsknop in: .
1. Selecteer **Registreren** om de toepassing te maken.
1. Selecteer het menu **Verificatie.**
1. Selecteer **id-tokens**in de sectie**Impliciete subsidie voor** geavanceerde **instellingen** | . Voor deze steekproef moet de [impliciete subsidiestroom](v2-oauth2-implicit-grant-flow.md) worden ingeschakeld om de gebruiker aan te melden.
1. Selecteer **Opslaan**.

# <a name="java"></a>[Java](#tab/java)

1. Wanneer de **pagina Een aanvraag registreren** wordt weergegeven, voert u een weergavenaam voor de toepassing in. Voer bijvoorbeeld **java-webapp**in.
1. Selecteer **Accounts in een organisatiemap en persoonlijke Microsoft-accounts (bijvoorbeeld Skype, Xbox, Outlook.com)** en selecteer vervolgens **Web-app / API** voor **toepassingstype**.
1. Selecteer **Registreren** om de aanmelding te registreren.
1. Selecteer **verificatie**in het linkermenu . Selecteer Onder **URI's omleiden**de optie **Web**.

1. Voer twee omleidings-URI's in: één voor de aanmeldingspagina en één voor de grafiekpagina. Gebruik voor beide hetzelfde host- en poortnummer, gevolgd door **/msal4jsample/secure/aad** voor de aanmeldingspagina en **msal4jsample/graph/me** voor de gebruikersinformatiepagina.

   Standaard wordt in het voorbeeld het volgende gebruikt:

   - **http://localhost:8080/msal4jsample/secure/aad**
   - **http://localhost:8080/msal4jsample/graph/me**

  Selecteer vervolgens **Opslaan**.

1. Selecteer **Certificaten & geheimen** in het menu.
1. Selecteer in de sectie **Clientgeheimen** de optie **Nieuw clientgeheim**en selecteer vervolgens:

   1. Voer een sleutelbeschrijving in.
   1. Selecteer de sleutelduur **in 1 jaar.**
   1. Selecteer **Toevoegen**.
   1. Wanneer de sleutelwaarde wordt weergegeven, kopieert u deze voor later. Deze waarde wordt niet opnieuw weergegeven of kan op een andere manier worden opgehaald.

# <a name="python"></a>[Python](#tab/python)

1. Wanneer de **pagina Een aanvraag registreren** wordt weergegeven, voert u de registratiegegevens van uw aanvraag in:
   1. Voer in de sectie **Naam** een betekenisvolle toepassingsnaam in die wordt weergegeven aan gebruikers van de app. Voer bijvoorbeeld **python-webapp**in .
   1. **Ondersteunde accounttypen** wijzigen in **accounts in een organisatiemap en persoonlijke Microsoft-accounts (bijvoorbeeld Skype, Xbox, Outlook.com).**
   1. Selecteer **web** in het keuzekader en voer in de sectie REDIRECT **http://localhost:5000/getAToken** **URI (optioneel)** de volgende omleidingsknop in: .
1. Selecteer **Registreren** om de toepassing te maken.
1. Zoek op de **pagina Overzicht** van de app de waarde van de **id-toepassing (client)** en leg deze vast voor later. U hebt het nodig om het configuratiebestand visual studio voor dit project te configureren.
1. Selecteer certificaten & **geheimen**in het linkermenu.
1. Selecteer in de sectie **Clientgeheimen** de optie **Nieuw clientgeheim**en selecteer vervolgens:

   1. Voer een sleutelbeschrijving in.
   1. Selecteer een sleutelduur van **in 1 jaar.**
   1. Selecteer **Toevoegen**.
   1. Wanneer de sleutelwaarde wordt weergegeven, kopieert u deze. U hebt deze later nodig.
---

## <a name="register-an-app-by-using-powershell"></a>Een app registreren met PowerShell

> [!NOTE]
> Momenteel maakt Azure AD PowerShell toepassingen met alleen de volgende ondersteunde accounttypen:
>
> - MyOrg (alleen accounts in deze organisatiemap)
> - AnyOrg (accounts in een organisatiemap)
>
> U een toepassing maken die gebruikers aanmeldt met hun persoonlijke Microsoft-accounts (bijvoorbeeld Skype, Xbox of Outlook.com). Maak eerst een multitenant-toepassing. Ondersteunde accounttypen zijn accounts in een organisatiemap. Wijzig vervolgens `signInAudience` de eigenschap in het toepassingsmanifest van de Azure-portal. Zie [stap 1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) in de ASP.NET Core-zelfstudie voor meer informatie. U deze stap generaliseren naar web-apps in elke taal.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [De codeconfiguratie van de app](scenario-web-app-sign-user-app-configuration.md)
