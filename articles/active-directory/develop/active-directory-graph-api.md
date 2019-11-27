---
title: Azure Active Directory Graph API | Microsoft Docs
description: Een overzicht en Snelstartgids voor Azure AD Graph API, waarmee programmatische toegang tot Azure AD via REST API-eind punten mogelijk wordt.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: dkershaw, sureshja
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30c1b5a3600c48dc548561df3cd2f955347a7e64
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533040"
---
# <a name="azure-active-directory-graph-api"></a>Azure Active Directory Graph API

> [!IMPORTANT]
> We raden u ten zeerste aan [Microsoft Graph](https://developer.microsoft.com/graph) te gebruiken in plaats van azure AD Graph API om toegang te krijgen tot Azure Active Directory-resources (Azure AD). We richten ons momenteel op ontwikkelingen in Microsoft Graph. Voor Azure AD Graph API zijn geen verdere verbeteringen gepland. Er zijn een zeer beperkt aantal scenario's waarvoor Azure AD Graph API mogelijk nog steeds geschikt is. Zie voor meer informatie de [Microsoft Graph of het](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) blog bericht van Azure AD Graph en [Migreer Azure AD Graph-apps naar Microsoft Graph](https://docs.microsoft.com/graph/migrate-azure-ad-graph-overview).

Dit artikel is van toepassing op Azure AD-Graph API. Zie [de Microsoft Graph-API gebruiken](https://docs.microsoft.com/graph/use-the-api)voor vergelijk bare informatie met betrekking tot Microsoft Graph-API.

De Azure Active Directory Graph API biedt programmatische toegang tot Azure AD via REST API-eind punten. Toepassingen kunnen Azure AD Graph API gebruiken om bewerkingen voor maken, lezen, bijwerken en verwijderen uit te voeren op Directory gegevens en-objecten. Azure AD Graph API ondersteunt bijvoorbeeld de volgende algemene bewerkingen voor een gebruikers object:

* Een nieuwe gebruiker in een directory maken
* De gedetailleerde eigenschappen van een gebruiker, zoals hun groepen, ophalen
* De eigenschappen van een gebruiker, zoals de locatie en het telefoon nummer, bijwerken of het wacht woord wijzigen
* Het groepslid maatschap van een gebruiker voor op rollen gebaseerde toegang controleren
* Het account van een gebruiker uitschakelen of geheel verwijderen

Daarnaast kunt u vergelijk bare bewerkingen uitvoeren op andere objecten, zoals groepen en toepassingen. Als u Azure AD-Graph API wilt aanroepen in een directory, moet uw toepassing zijn geregistreerd bij Azure AD. Uw toepassing moet ook toegang krijgen tot Azure AD Graph API. Deze toegang wordt normaal gesp roken bereikt via de toestemming stroom van een gebruiker of beheerder.

Als u de Azure Active Directory Graph API wilt gaan gebruiken, raadpleegt u de [hand leiding voor Azure ad Graph API Snelstartgids](active-directory-graph-api-quickstart.md)of raadpleegt u de [interactieve documentatie over Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="features"></a>Functies

Azure AD Graph API biedt de volgende functies:

* **Rest API-eind punten**: Azure AD-Graph API is een rest-service die bestaat uit eind punten die worden geopend met standaard HTTP-aanvragen. Azure AD Graph API ondersteunt XML-of JSON-inhouds typen (Java Script object Notation) voor aanvragen en antwoorden. Zie overzicht van [Azure AD Graph rest API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)voor meer informatie.
* **Verificatie met Azure AD**: elke aanvraag voor azure AD Graph API moet worden geverifieerd door een JSON Web token (JWT) toe te voegen in de autorisatie-header van de aanvraag. Dit token wordt verkregen door een aanvraag uit te voeren voor het token eindpunt van Azure AD en geldige referenties op te geven. U kunt de OAuth 2,0-client referenties stroom of de autorisatie code subsidie stroom gebruiken om een token te verkrijgen om de grafiek aan te roepen. Voor meer informatie, [OAuth 2,0 in azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* **Op rollen gebaseerde autorisatie (RBAC)** : beveiligings groepen worden gebruikt voor het uitvoeren van RBAC in azure AD-Graph API. Als u bijvoorbeeld wilt bepalen of een gebruiker toegang heeft tot een specifieke resource, kan de toepassing de bewerking voor het [controleren van groepslid maatschappen (transitief)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/functions-and-actions#checkMemberGroups) aanroepen. dit retourneert waar of onwaar.
* **Differentiële query**: met differentiële query's kunt u wijzigingen in een map bijhouden tussen twee Peri Oden zonder dat u regel matig query's moet uitvoeren op Azure AD Graph API. Dit type aanvraag retourneert alleen de wijzigingen die zijn aangebracht tussen de vorige differentiële query aanvraag en de huidige aanvraag. Zie [Azure AD Graph API Differential query](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query)voor meer informatie.
* **Directory-extensies**: u kunt aangepaste eigenschappen toevoegen aan Directory-objecten zonder een extern gegevens archief. Als voor uw toepassing bijvoorbeeld een Skype ID-eigenschap voor elke gebruiker is vereist, kunt u de nieuwe eigenschap in de Directory registreren en deze beschikbaar voor gebruik op elk gebruikers object. Zie [Azure AD Graph API Directory-schema-uitbrei dingen](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)voor meer informatie.
* **Beveiligd met een machtigings bereik**: Azure AD Graph API maakt toestemmings zoekbereiken mogelijk die beveiligde toegang tot Azure AD-gegevens met behulp van OAuth 2,0. Het ondersteunt diverse typen client-apps, waaronder:
  
  * gebruikers interfaces waarvoor gedelegeerde toegang tot gegevens is verleend via de autorisatie van de aangemelde gebruiker (gedelegeerde)
  * Service/daemon-toepassingen die op de achtergrond werken zonder dat er een aangemelde gebruiker aanwezig is en gebruikmaakt van door de toepassing gedefinieerde toegangs beheer op basis van rollen
    
    Zowel gedelegeerde als toepassings machtigingen vertegenwoordigen een bevoegdheid die wordt weer gegeven door de Azure AD-Graph API en kan worden aangevraagd door client toepassingen via de functies voor toepassings registratie van machtigingen in de [Azure Portal](https://portal.azure.com). [Azure AD Graph API-machtigings bereik](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) biedt informatie over wat er beschikbaar is voor gebruik door uw client toepassing.

## <a name="scenarios"></a>Scenario 's

Azure AD Graph API maakt veel toepassings scenario's mogelijk. De volgende scenario's zijn het meest gangbaar:

* **Line-of-Business-toepassing (één Tenant)** : in dit scenario werkt een Enter prise-ontwikkelaar voor een organisatie met een Office 365-abonnement. De ontwikkelaar bouwt een webtoepassing die samenwerkt met Azure AD om taken uit te voeren, zoals het toewijzen van een licentie aan een gebruiker. Voor deze taak is toegang tot de Azure AD-Graph API vereist. de ontwikkelaar registreert dus de toepassing voor één Tenant in azure AD en configureert Lees-en schrijf machtigingen voor Azure AD-Graph API. Vervolgens wordt de toepassing geconfigureerd voor het gebruik van eigen referenties of van de gebruiker die momenteel is aangemeld voor het verkrijgen van een token om de Azure AD-Graph API aan te roepen.
* **Software als een service toepassing (multi tenant)** : in dit scenario ontwikkelt een onafhankelijke software leverancier (ISV) een gehoste multi tenant-webtoepassing die gebruikers beheer functies biedt voor andere organisaties die gebruikmaken van Azure AD. Deze functies vereisen toegang tot Directory-objecten, zodat de toepassing de Azure AD-Graph API moet aanroepen. De ontwikkelaar registreert de toepassing in azure AD, configureert deze om Lees-en schrijf machtigingen voor Azure AD-Graph API te vereisen, waarna externe toegang wordt ingeschakeld, zodat andere organisaties toestemming kunnen geven om de toepassing in hun Directory te gebruiken. Wanneer een gebruiker in een andere organisatie zich voor de eerste keer verifieert bij de toepassing, wordt er een dialoog venster met toestemming weer gegeven met de machtigingen die de toepassing aanvraagt. Als u toestemming verleent, geeft u de toepassing de gewenste machtigingen voor Azure AD-Graph API in de map van de gebruiker. Zie [overzicht van het toestemming raamwerk](consent-framework.md)voor meer informatie over het toestemming raamwerk.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen om te beginnen met het gebruik van de Azure Active Directory Graph API:

* [Hand leiding voor Azure AD Graph API Snelstartgids](active-directory-graph-api-quickstart.md)
* [Documentatie over de REST van Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
