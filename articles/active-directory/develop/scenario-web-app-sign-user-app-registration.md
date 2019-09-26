---
title: Web-app die gebruikers aanmeldt (app-registratie)-micro soft Identity-platform
description: Meer informatie over het bouwen van een web-app die zich aanmeldt bij gebruikers (app-registratie)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ab7c3699abdd5c094b1b14cd53f76023fa8c1ac
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309604"
---
# <a name="web-app-that-signs-in-users---app-registration"></a>Web-app die zich aanmeldt bij gebruikers-app-registratie

Deze pagina geeft uitleg over de app-registratie Details voor een web-app die gebruikers aanmeldt.

Als u uw toepassing wilt registreren, kunt u het volgende gebruiken:

- De [Snelstartgids voor web-apps](#register-an-app-using-the-quickstarts) : naast een fantastische eerste ervaring met het maken van een toepassing, Quick starts in de Azure Portal bevatten een knop met de naam **deze wijziging voor mij maken**. U kunt deze knop gebruiken om de eigenschappen in te stellen die u nodig hebt, zelfs voor een bestaande app. U moet de waarden van deze eigenschappen aanpassen aan uw eigen case. Met name de Web API-URL voor uw app is waarschijnlijk anders dan de voorgestelde standaard waarde, die ook van invloed is op de afmeldings-URI.
- De Azure Portal [uw toepassing hand matig te registreren](#register-an-app-using-azure-portal)
- Power shell en opdracht regel Programma's

## <a name="register-an-app-using-the-quickstarts"></a>Een app registreren met de Quick starts

Als u naar deze koppeling navigeert, kunt u een Boots trap maken om uw webtoepassing te maken:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-using-azure-portal"></a>Een app registreren met Azure Portal

> [!NOTE]
> de te gebruiken Portal verschilt, afhankelijk van of uw toepassing wordt uitgevoerd in de Microsoft Azure open bare Cloud of in een nationale of soevereine Cloud. Zie [National Clouds](./authentication-national-cloud.md#app-registration-endpoints) (Engelstalig) voor meer informatie.


1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account. U kunt zich ook aanmelden bij de nationale Cloud Azure Portal van de keuze.
1. Als uw account u toegang geeft tot meer dan één Tenant, selecteert u uw account in de rechter bovenhoek en stelt u uw portal sessie in op de gewenste Azure AD-Tenant.
1. Selecteer in het navigatie deel venster aan de linkerkant de **Azure Active Directory** -service en selecteer vervolgens **app-registraties** > **nieuwe registratie**.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

4. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
   1. Kies de ondersteunde account typen voor uw toepassing (Zie [ondersteunde account typen](./v2-supported-account-types.md))
   1. Voer in de sectie **Naam** een beschrijvende toepassingsnaam. Deze wordt zichtbaar voor gebruikers van de app. Bijvoorbeeld: `AspNetCore-WebApp`.
   1. Voeg in de **omleidings-URI**het type toepassing toe en de URI-bestemming die geretourneerde token Reacties accepteert na verificatie. Bijvoorbeeld `https://localhost:44321/`.  Selecteer **Registreren**.
1. Selecteer het menu **Verificatie** en voeg dan de volgende gegevens toe:
   1. In **antwoord-URL**, `https://localhost:44321/signin-oidc` add van het type Web.
   1. Bij **Geavanceerde instellingen** stelt u de **afmeldings-URL** in op `https://localhost:44321/signout-oidc`.
   1. Bij **Impliciete toekenning** controleert u de **id-tokens**.
   1. Selecteer **Opslaan**.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

4. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
   1. Kies de ondersteunde account typen voor uw toepassing (Zie [ondersteunde account typen](./v2-supported-account-types.md))
   - Voer in de sectie **Naam** een beschrijvende toepassingsnaam. Deze wordt zichtbaar voor gebruikers van de app. Bijvoorbeeld: `MailApp-openidconnect-v2`.
   - Selecteer in de sectie de omleidings-URI (optioneel) **Web** in de keuze lijst met invoervak en voer de `https://localhost:44326/`volgende omleidings-uri's in:.
1. Selecteer **Registreren** om de toepassing te maken.
1. Selecteer het menu **Verificatie** en voeg dan de volgende gegevens toe:
   - Controleer in de sectie **Geavanceerde instellingen** | **impliciete toekenning** de **id-tokens** als voor dit voor beeld moet de [impliciete toekennings stroom](v2-oauth2-implicit-grant-flow.md) zijn ingeschakeld om u aan te melden bij de gebruiker.
1. Selecteer **Opslaan**.

# <a name="javatabjava"></a>[Java](#tab/java)

4. Wanneer de **pagina een toepassing registreren** wordt weer gegeven, voert u een beschrijvende naam in voor de toepassing, bijvoorbeeld ' Java-webapp ', selecteert u accounts in een organisatie Directory en persoonlijke micro soft-accounts (bijvoorbeeld Skype, Xbox, Outlook.com) en selecteert u web-app /API ' als het *toepassings type*.
1. Klik op **registreren** om de toepassing te registreren.
1. Klik in het menu aan de linkerkant op **verificatie**en selecteer onder *uri's omleiden*op ' Web '. U moet twee verschillende omleidings-Uri's invoeren: één voor de aanmeldings pagina en één voor de pagina Graph-gebruikers. Voor beide moet u dezelfde host en hetzelfde poort nummer gebruiken, gevolgd door '/msal4jsample/Secure/Aad ' voor de aanmeldings pagina en ' msal4jsample/Graph/gebruikers ' voor de pagina gebruikers.
 Het voor beeld maakt standaard gebruik van: 

    - `http://localhost:8080/msal4jsample/secure/aad`. 
    - `http://localhost:8080/msal4jsample/graph/users`

Klik op **Opslaan**.

# <a name="pythontabpython"></a>[Python](#tab/python)

4. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
   - Voer in de sectie **Naam** een beschrijvende toepassingsnaam. Deze wordt zichtbaar voor gebruikers van de app. Bijvoorbeeld: `python-webapp`.
   - Wijzig **ondersteunde account typen** **in accounts in een organisatorische map en persoonlijke micro soft-accounts (bijvoorbeeld Skype, Xbox, Outlook.com)** .
   - Selecteer in de sectie de omleidings-URI (optioneel) **Web** in de keuze lijst met invoervak en voer de `http://localhost:5000/getAToken`volgende omleidings-uri's in:.
1. Selecteer **Registreren** om de toepassing te maken.
1. Zoek op de pagina app- **overzicht** de waarde van de **toepassing (client)** en noteer deze voor later. U hebt deze nodig voor het configureren van het Visual Studio-configuratie bestand voor dit project.
1. Selecteer de sectie **verificatie** op de pagina overzicht van de app.
   - Stel in het gedeelte **Geavanceerde instellingen** de **afmeldings-URL** in op`http://localhost:5000/logout`
1. Selecteer **Opslaan**.

---

## <a name="register-an-app-using-powershell"></a>Een app registreren met Power shell

> [!NOTE]
> Momenteel maakt Azure AD Power shell alleen toepassingen met de volgende ondersteunde account typen:
>
> - MyOrg (alleen accounts in deze organisatie Directory)
> - AnyOrg (accounts in een organisatie Directory).
>
> Als u een toepassing wilt maken die zich aanmeldt als gebruikers met hun persoonlijke micro soft-accounts (bijvoorbeeld Skype, XBox, Outlook.com), kunt u eerst een toepassing met meerdere tenants maken (ondersteunde account typen = accounts in een organisatie Directory) en vervolgens wijzigen de `signInAudience` eigenschap in het manifest van de toepassing van de Azure Portal. Dit wordt uitgelegd in stap [1,3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) van de ASP.net core zelf studie (en kan worden gegeneraliseerd met web apps in elke taal).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Configuratie van de app-code](scenario-web-app-sign-user-app-configuration.md)
