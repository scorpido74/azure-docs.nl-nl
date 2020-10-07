---
title: 'Quickstart: Account voor Microsoft Identify Platform-apps wijzigen | Azure'
description: In deze quickstart gaat u een toepassing configureren die is geregistreerd bij het Microsoft Identity Platform om te wijzigen wie, of welke accounts, toegang hebben tot de toepassing.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: d143bde9c22bc726f00b5c209d1b7fbc131905b0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91258010"
---
# <a name="quickstart-modify-the-accounts-supported-by-an-application"></a>Quickstart: De accounts wijzigen die worden ondersteund door een toepassing

Bij het registreren van een toepassing op het Microsoft Identity Platform, wilt u mogelijk dat uw toepassing alleen kan worden geopend door gebruikers in uw organisatie. Ook kan het zijn dat u wilt dat uw toepassing toegankelijk is voor gebruikers in externe organisaties, of gebruikers in externe organisaties evenals de gebruikers die niet noodzakelijkerwijs deel uitmaken van een organisatie (persoonlijke accounts).

In deze snelstart leert u hoe u de configuratie van uw toepassing aanpast om te wijzigen wie, of welke accounts, toegang hebben tot de toepassing.

## <a name="prerequisites"></a>Vereisten

* Voltooiing van [quickstart: Een toepassing registreren bij het Microsoft-identiteitsplatform](quickstart-register-app.md)

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Aanmelden bij de Azure-portal en de app selecteren

Voordat u de app kunt configureren, volgt u deze stappen:

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Als u via uw account toegang hebt tot meer dan één tenant, selecteert u uw account in de rechterbovenhoek en stelt u de portalsessie in op de gewenste Azure Active Directory-tenant.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory**-service en selecteer vervolgens **App-registraties**.
1. Zoek en selecteer de toepassing die u wilt configureren. Wanneer u de app hebt geselecteerd, ziet u de pagina **Overzicht** of de hoofdregistratiepagina van de toepassing.
1. Volg de stappen voor het [wijzigen van de registratie van de toepassing om verschillende accounts te ondersteunen](#change-the-application-registration-to-support-different-accounts).
1. Als u een toepassing met één pagina hebt, [schakelt u impliciete goedkeuring voor OAuth 2.0 in](#enable-oauth-20-implicit-grant-for-single-page-applications).

## <a name="change-the-application-registration-to-support-different-accounts"></a>De registratie van de toepassing wijzigen om verschillende accounts te ondersteunen

Als u een toepassing ontwikkelt die u beschikbaar wilt maken voor uw klanten of partners buiten uw organisatie, moet u de definitie van de toepassing bijwerken in Azure Portal.

> [!IMPORTANT]
> Azure Active Directory vereist dat de URI van de toepassings-id van multitenant toepassingen wereldwijd uniek is. De URI van de app-id is een van de manieren waarop een toepassing wordt geïdentificeerd in protocolberichten. Voor een toepassing met één tenant is het voldoende dat de URI van de app-id uniek is binnen die tenant. Voor een multitenant toepassing moet deze wereldwijd uniek zijn, zodat Azure Active Directory de toepassing in alle tenants kan vinden. Wereldwijde uniekheid wordt afgedwongen door te vereisen dat de URI van de app-id een hostnaam heeft die overeenkomt met een geverifieerd domein van de Azure Active Directory-tenant. Als de naam van uw tenant bijvoorbeeld contoso.onmicrosoft.com is, zou `https://contoso.onmicrosoft.com/myapp` een geldige URI voor de app-id zijn. Als uw tenant een geverifieerd domein van contoso.com heeft, zou `https://contoso.com/myapp` ook een geldige URI voor de app-id zijn. Als de URI van de app-id dit patroon niet volgt, mislukt het instellen van een multitenant toepassing.

### <a name="to-change-who-can-access-your-application"></a>Wijzigen wie toegang heeft tot uw toepassing

1. Selecteer op de pagina **Overzicht** van de app de sectie **Verificatie** en wijzig de waarde die is geselecteerd onder **Ondersteunde accounttypen**.
    * Selecteer **Alleen accounts in deze map** als u een Line-Of-Business-toepassing (LOB) bouwt. Deze optie is niet beschikbaar als de toepassing niet is geregistreerd in een map.
    * Selecteer **Accounts in een organisatieadreslijst** als uw doelgroep bestaat uit zakelijke klanten of klanten in het onderwijs.
    * Selecteer **Accounts in een organisatieadreslijst en persoonlijke Microsoft-accounts** om de meest uitgebreide set klanten te targeten.
1. Selecteer **Opslaan**.

## <a name="enable-oauth-20-implicit-grant-for-single-page-applications"></a>Impliciete goedkeuring voor OAuth 2.0 inschakelen voor toepassingen met één pagina

Toepassingen met één pagina (SPA's) zijn doorgaans geordend met een front-end met veel JavaScript die wordt uitgevoerd in de browser, die de back-end van de web-API van de toepassing aanroept om de bedrijfslogica uit te voeren. Voor SPA's die worden gehost in Azure Active Directory gebruikt u impliciete goedkeuring voor OAuth 2.0 om de gebruiker bij Azure Active Directory te verifiëren en een token te verkrijgen dat u kunt gebruiken om aanroepen van de JavaScript-client van de toepassing naar de web-API van de back-end te beveiligen.

Nadat de gebruiker toestemming heeft verleend, kan ditzelfde verificatieprotocol worden gebruikt om tokens op te halen voor het beveiligen van aanroepen tussen de client en andere web-API-resources die voor de toepassing zijn geconfigureerd. Lees meer over de impliciete goedkeuringsstroom voor OAuth 2.0 in Azure Active Directory [v1.0](../azuread-dev/v1-oauth2-implicit-grant-flow.md) en [v2.0](v2-oauth2-implicit-grant-flow.md) voor meer informatie over de impliciete goedkeuringsstroom en om te bepalen of dit geschikt is voor uw toepassingsscenario.

Impliciete goedkeuring voor OAuth 2.0 is standaard uitgeschakeld voor toepassingen. U kunt impliciete goedkeuring voor OAuth 2.0 voor uw toepassing inschakelen door de onderstaande stappen te volgen.

### <a name="to-enable-oauth-20-implicit-grant"></a>Impliciete goedkeuring voor OAuth 2.0 inschakelen

1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory**-service en selecteer vervolgens **App-registraties**.
1. Zoek en selecteer de toepassing die u wilt configureren. Wanneer u de app hebt geselecteerd, ziet u de pagina **Overzicht** of de hoofdregistratiepagina van de toepassing.
1. Selecteer op de pagina **Overzicht** van de app de sectie **Verificatie**.
1. Zoek onder **Geavanceerde instellingen** de sectie **Impliciete toewijzing** op.
1. Selecteer **ID-tokens**, **Toegangstokens** of beide.
1. Selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Huisstijlrichtlijnen voor apps](howto-add-branding-in-azure-ad-apps.md)
