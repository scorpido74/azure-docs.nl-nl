---
title: Een web-app registreren die zich aanmeldt bij gebruikers-micro soft Identity platform | Azure
description: Meer informatie over het registreren van een web-app die wordt aangemeld bij gebruikers
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7d2eb5356b1abc54508fd6bf8d35fd9fc39d02ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80881576"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>Web-app die zich aanmeldt bij gebruikers: app-registratie

In dit artikel wordt uitgelegd wat de app-registratie specifiek is voor een web-app die zich aanmeldt bij gebruikers.

Als u uw toepassing wilt registreren, kunt u het volgende gebruiken:

- De [Snelstartgids voor web-apps](#register-an-app-by-using-the-quickstarts). Naast een fantastische eerste ervaring met het maken van een toepassing, kunnen Quick starts in de Azure Portal een knop bevatten met de naam **deze wijziging voor mij maken**. U kunt deze knop gebruiken om de eigenschappen in te stellen die u nodig hebt, zelfs voor een bestaande app. U moet de waarden van deze eigenschappen aanpassen aan uw eigen case. Met name de Web API-URL voor uw app is waarschijnlijk anders dan de voorgestelde standaard waarde, die ook van invloed is op de afmeldings-URI.
- De Azure Portal [uw toepassing hand matig te registreren](#register-an-app-by-using-the-azure-portal).
- Power shell en opdracht regel Programma's.

## <a name="register-an-app-by-using-the-quickstarts"></a>Een app registreren met behulp van de Quick starts

U kunt deze koppelingen gebruiken om het maken van uw webtoepassing te Boots trappen:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>Een app registreren met behulp van de Azure Portal

> [!NOTE]
> De te gebruiken Portal verschilt, afhankelijk van of uw toepassing wordt uitgevoerd in de Microsoft Azure open bare Cloud of in een nationale of soevereine Cloud. Zie [National Clouds](./authentication-national-cloud.md#app-registration-endpoints)(Engelstalig) voor meer informatie.


1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account. U kunt zich ook aanmelden bij de Azure Portal keuze voor de nationale Cloud.
1. Als uw account u toegang geeft tot meer dan één Tenant, selecteert u uw account in de rechter bovenhoek. Stel vervolgens uw portal-sessie in op de gewenste Azure Active Directory-Tenant (Azure AD).
1. Selecteer in het linkerdeel venster de **Azure Active Directory** -service en selecteer vervolgens **app-registraties** > **nieuwe registratie**.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
   1. Kies de ondersteunde account typen voor uw toepassing. (Zie [ondersteunde account typen](./v2-supported-account-types.md).)
   1. Voer in de sectie **naam** een zinvolle toepassings naam in die wordt weer gegeven voor gebruikers van de app. Voer bijvoorbeeld **AspNetCore-webapp**in.
   1. Voor **omleidings-URI**voegt u het type toepassing toe en de URI-bestemming die geretourneerde token Reacties accepteert na een geslaagde verificatie. Voer **https://localhost:44321**bijvoorbeeld in. Selecteer vervolgens **registreren**.
1. Selecteer het menu **Verificatie** en voeg dan de volgende gegevens toe:
   1. Voor **antwoord-URL**, **https://localhost:44321/signin-oidc** add van het type **Web**.
   1. Stel in het gedeelte **Geavanceerde instellingen** de **afmeldings-URL** in op **https://localhost:44321/signout-oidc**.
   1. Selecteer **id-tokens** onder **Impliciete toekenning**.
   1. Selecteer **Opslaan**.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

1. Wanneer de **pagina een toepassing registreren** wordt weer gegeven, voert u de registratie gegevens van uw toepassing in:
   1. Kies de ondersteunde account typen voor uw toepassing. (Zie [ondersteunde account typen](./v2-supported-account-types.md).)
   1. Voer in de sectie **naam** een zinvolle toepassings naam in die wordt weer gegeven voor gebruikers van de app. Voer bijvoorbeeld **MailApp-openidconnect-v2**in.
   1. Selecteer in de sectie de **omleidings-URI (optioneel)** **Web** in de keuze lijst met invoervak en voer **https://localhost:44326/** de volgende omleidings-URI in:.
1. Selecteer **Registreren** om de toepassing te maken.
1. Selecteer het menu **verificatie** .
1. Selecteer in de sectie**impliciete verleende** **instellingen** | de optie **id-tokens**. Voor dit voor beeld moet de [impliciete toekennings stroom](v2-oauth2-implicit-grant-flow.md) zijn ingeschakeld om u aan te melden bij de gebruiker.
1. Selecteer **Opslaan**.

# <a name="java"></a>[Java](#tab/java)

1. Wanneer de **pagina een toepassing registreren** wordt weer gegeven, voert u een weergave naam in voor de toepassing. Voer bijvoorbeeld **Java-webapp**in.
1. Selecteer **accounts in een organisatorische map en persoonlijke micro soft-accounts (bijvoorbeeld Skype, Xbox, Outlook.com)** en selecteer **Web-app/API** voor **toepassings type**.
1. Selecteer **registreren** om de toepassing te registreren.
1. Selecteer in het menu links **verificatie**. Onder **omleidings-uri's**, selecteert u **Web**.

1. Voer twee omleidings-Uri's in: één voor de aanmeldings pagina en een voor de grafiek pagina. Gebruik voor beide dezelfde host en hetzelfde poort nummer, gevolgd door **/msal4jsample/Secure/Aad** voor de aanmeldings pagina en **msal4jsample/Graph/me** voor de pagina met gebruikers informatie.

   Het voor beeld maakt standaard gebruik van:

   - **http://localhost:8080/msal4jsample/secure/aad**
   - **http://localhost:8080/msal4jsample/graph/me**

  Selecteer vervolgens **Opslaan**.

1. Selecteer **certificaten & geheimen** in het menu.
1. Selecteer in de sectie **client geheimen** de optie **Nieuw client geheim**en voer vervolgens de volgende handelingen uit:

   1. Voer een beschrijving van de sleutel in.
   1. Selecteer de sleutel duur **in 1 jaar**.
   1. Selecteer **Toevoegen**.
   1. Wanneer de sleutel waarde wordt weer gegeven, kopieert u deze voor later. Deze waarde wordt niet opnieuw weer gegeven of kan op een andere manier worden opgehaald.

# <a name="python"></a>[Python](#tab/python)

1. Wanneer de **pagina een toepassing registreren** wordt weer gegeven, voert u de registratie gegevens van uw toepassing in:
   1. Voer in de sectie **naam** een zinvolle toepassings naam in die wordt weer gegeven voor gebruikers van de app. Voer bijvoorbeeld **python-webapp**in.
   1. Wijzig **ondersteunde account typen** **in accounts in een organisatorische map en persoonlijke micro soft-accounts (bijvoorbeeld Skype, Xbox, Outlook.com)**.
   1. Selecteer in de sectie de **omleidings-URI (optioneel)** **Web** in de keuze lijst met invoervak en voer **http://localhost:5000/getAToken**de volgende omleidings-URI in:.
1. Selecteer **Registreren** om de toepassing te maken.
1. Zoek op de **overzichts** pagina van de app naar de waarde van de **toepassings-id** en noteer deze voor later. U hebt deze nodig voor het configureren van het Visual Studio-configuratie bestand voor dit project.
1. Selecteer in het linkermenu **certificaten & geheimen**.
1. Selecteer in de sectie **client geheimen** de optie **Nieuw client geheim**en voer vervolgens de volgende handelingen uit:

   1. Voer een beschrijving van de sleutel in.
   1. Selecteer een sleutel duur van **in één jaar**.
   1. Selecteer **Toevoegen**.
   1. Wanneer de sleutel waarde wordt weer gegeven, kopieert u deze. U hebt deze later nodig.
---

## <a name="register-an-app-by-using-powershell"></a>Een app registreren met behulp van Power shell

> [!NOTE]
> Op dit moment maakt Azure AD Power shell toepassingen met alleen de volgende ondersteunde account typen:
>
> - MyOrg (alleen accounts in deze organisatie Directory)
> - AnyOrg (accounts in elke organisatie Directory)
>
> U kunt een toepassing maken die zich aanmeldt bij gebruikers met hun persoonlijke micro soft-accounts (bijvoorbeeld Skype, Xbox of Outlook.com). Maak eerst een multi tenant-toepassing. Ondersteunde account typen zijn accounts in elke organisatie Directory. Wijzig vervolgens de `signInAudience` eigenschap in het manifest van de toepassing uit het Azure Portal. Zie [stap 1,3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) in de ASP.net core zelf studie voor meer informatie. U kunt deze stap generaliseren voor web-apps in elke gewenste taal.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Configuratie van de app-code](scenario-web-app-sign-user-app-configuration.md)
