---
title: Azure AD-toestemmingskader
titleSuffix: Microsoft identity platform
description: Meer informatie over het toestemmingskader in Azure Active Directory en hoe het eenvoudig is om web- en native clienttoepassingen met meerdere tenant's te ontwikkelen.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/30/2018
ms.author: ryanwi
ms.reviewer: zachowd, lenalepa, jesakowi
ms.custom: aaddev
ms.openlocfilehash: cb9441e6ce19094ff72e902cdeea151041ceb963
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161125"
---
# <a name="azure-active-directory-consent-framework"></a>Azure Active Directory consent framework

Met het Azure Active Directory-toestemmingskader (Azure Active Directory) u eenvoudig web- en native clienttoepassingen met meerdere tenant's ontwikkelen. Met deze toepassingen kunnen u zich aanmelden door gebruikersaccounts van een Azure AD-tenant die verschilt van de tenant waarin de toepassing is geregistreerd. Ze moeten mogelijk ook toegang krijgen tot web-API's, zoals de Microsoft Graph API (om toegang te krijgen tot Azure AD, Intune en services in Office 365) en andere API's van Microsoft-services, naast uw eigen web-API's.

Het framework is gebaseerd op een gebruiker of een beheerder die toestemming geeft voor een toepassing die vraagt om te worden geregistreerd in hun directory, waarbij mogelijk toegang wordt verkregen tot directorygegevens. Als een webclienttoepassing bijvoorbeeld agenda-informatie over de gebruiker uit Office 365 moet lezen, moet die gebruiker eerst toestemming geven voor de clienttoepassing. Nadat toestemming is gegeven, kan de clienttoepassing namens de gebruiker de Microsoft Graph API aanroepen en de agenda-informatie gebruiken als dat nodig is. De [Microsoft Graph API](https://developer.microsoft.com/graph) biedt toegang tot gegevens in Office 365 (zoals agenda's en berichten van Exchange, sites en lijsten van SharePoint, documenten van OneDrive, notitieblokken van OneNote, taken van Planner en werkmappen van Excel), evenals gebruikers en groepen van Azure AD en andere gegevensobjecten uit meer Microsoft-cloudservices.

Het toestemmingskader is gebaseerd op OAuth 2.0 en de verschillende stromen, zoals autorisatiecodeverlening en toekenning van clientreferenties, met behulp van openbare of vertrouwelijke clients. Door OAuth 2.0 te gebruiken, maakt Azure AD het mogelijk om veel verschillende soorten clienttoepassingen te bouwen, zoals op een telefoon, tablet, server of een webtoepassing - en toegang te krijgen tot de vereiste bronnen.

Zie [Toegang tot webtoepassingen beheren met OAuth 2.0 en Azure AD- en](v2-oauth2-auth-code-flow.md) [Verificatiescenario's voor Azure AD](authentication-scenarios.md)voor meer informatie over het gebruik van het toestemmingskader met OAuth2.0-autorisatieverlening. Zie [App-verificatie met Microsoft Graph](https://developer.microsoft.com/graph/docs/authorization/auth_overview)voor informatie over het verkrijgen van geautoriseerde toegang tot Office 365 via Microsoft Graph.

## <a name="consent-experience---an-example"></a>Toestemmingservaring - een voorbeeld

In de volgende stappen ziet u hoe de toestemmingservaring werkt voor zowel de ontwikkelaar van de toepassing als de gebruiker.

1. Stel dat u een webclienttoepassing hebt die specifieke machtigingen moet aanvragen voor toegang tot een bron/API. U leert hoe u deze configuratie doen in de volgende sectie, maar in wezen wordt de Azure-portal gebruikt om toestemmingsaanvragen te declareren tijdens de configuratie. Net als andere configuratie-instellingen worden ze onderdeel van de Azure AD-registratie van de toepassing:

    ![Machtigingen voor andere toepassingen](./media/consent-framework/permissions.png)

1. Houd er rekening mee dat de machtigingen van uw toepassing zijn bijgewerkt, dat de toepassing wordt uitgevoerd en dat een gebruiker op het punt staat deze voor de eerste keer te gebruiken. Eerst moet de toepassing een autorisatiecode verkrijgen `/authorize` van het eindpunt van Azure AD. De autorisatiecode kan vervolgens worden gebruikt bij het verkrijgen van een nieuw token voor toegang en vernieuwen.

1. Als de gebruiker nog niet is geverifieerd, vraagt het eindpunt van `/authorize` Azure AD de gebruiker om zich aan te melden.

    ![Aanmelding door gebruiker of beheerder bij Azure AD](./media/consent-framework/usersignin.png)

1. Nadat de gebruiker zich heeft aangemeld, bepaalt Azure AD of de gebruiker een toestemmingspagina moet worden weergegeven. Hierbij wordt gecontroleerd of de gebruiker (of de beheerder in de organisatie) de toepassing al toestemming heeft gegeven. Als er nog geen toestemming is verleend, vraagt Azure AD de gebruiker om toestemming en geeft het de vereiste machtigingen weer die moeten worden uitgevoerd. De set machtigingen die worden weergegeven in het toestemmingsdialoogvenster komt overeen met de machtigingen die zijn geselecteerd in de **gedelegeerde machtigingen** in de Azure-portal.

    ![Toont een voorbeeld van machtigingen die worden weergegeven in het dialoogvenster toestemming](./media/consent-framework/consent.png)

1. Nadat de gebruiker toestemming heeft verleend, wordt een autorisatiecode teruggestuurd naar uw toepassing, die wordt ingewisseld om een toegangstoken te verkrijgen en een token te vernieuwen. Zie [OAuth 2.0-autorisatiecodestroom](v2-oauth2-auth-code-flow.md)voor meer informatie over deze stroom.

1. Beheerders kunnen toestemming geven voor de gedelegeerde machtigingen van een toepassing voor alle gebruikers in de tenant. Beheerderstoestemming voorkomt dat het toestemmingsdialoogvenster wordt weergegeven voor elke gebruiker in de tenant en kan worden uitgevoerd in de [Azure-portal](https://portal.azure.com) door gebruikers met de beheerdersrol. Zie [Beheerdersrolmachtigingen in Azure AD](../users-groups-roles/directory-assign-admin-roles.md)voor meer informatie over welke beheerdersrollen kunnen instemmen met gedelegeerde machtigingen.

    **Toestemming geven voor de gedelegeerde machtigingen van een app**

   1. Ga naar de pagina **API-machtigingen** voor uw toepassing
   1. Klik op de **toestemmingsknop voor beheerders van de beheerder.**

      ![Machtigingen verlenen voor expliciete toestemming van beheerders](./media/consent-framework/grant-consent.png)

   > [!IMPORTANT]
   > Het verlenen van expliciete toestemming via de knop **Machtigingen voor subsidies** is momenteel vereist voor toepassingen met één pagina (SPA) die ADAL.js gebruiken. Als dit niet gebeurt, treedt er een fout op in de toepassing wanneer het toegangstoken wordt aangevraagd.

## <a name="next-steps"></a>Volgende stappen

* Bekijk [hoe u een app converteert naar multitenant](howto-convert-app-to-be-multi-tenant.md)
* Voor meer informatie, lees [hoe toestemming wordt ondersteund op de OAuth 2.0-protocollaag tijdens de autorisatiecode-subsidiestroom.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)
