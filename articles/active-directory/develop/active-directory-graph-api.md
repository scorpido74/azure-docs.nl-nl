---
title: Azure Active Directory Graph API | Microsoft Documenten
description: Een overzicht en quickstart-handleiding voor Azure AD Graph API, waarmee programmatische toegang tot Azure AD via REST API-eindpunten kan worden weergegeven.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: dkershaw, sureshja
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: ef042b9eb625a0d0de5d5dcb883b823c3a499aa9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76698914"
---
# <a name="azure-active-directory-graph-api"></a>Azure Active Directory Graph API

> [!IMPORTANT]
> We raden u ten zeerste aan [Microsoft Graph](https://developer.microsoft.com/graph) te gebruiken in plaats van Azure AD Graph API om toegang te krijgen tot Azure Active Directory-resources (Azure AD). We richten ons momenteel op ontwikkelingen in Microsoft Graph. Voor Azure AD Graph API zijn geen verdere verbeteringen gepland. Er zijn een zeer beperkt aantal scenario's waarvoor de Azure AD Graph API nog geschikt kan zijn; Zie voor meer informatie het [blogbericht Microsoft Graph of Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) en Migratie van Azure AD [Graph-apps naar Microsoft Graph](https://docs.microsoft.com/graph/migrate-azure-ad-graph-overview).

Dit artikel is van toepassing op de Azure AD Graph API. Zie De Microsoft Graph API gebruiken voor vergelijkbare informatie met betrekking tot De API van [Microsoft Graph.](https://docs.microsoft.com/graph/use-the-api)

De Azure Active Directory Graph API-biedt programmatische toegang tot Azure AD via REST API-eindpunten. Toepassingen kunnen Azure AD Graph API gebruiken om crud-bewerkingen (maken, lezen, bijwerken en verwijderen) uit te voeren op directorygegevens en -objecten. Azure AD Graph API ondersteunt bijvoorbeeld de volgende algemene bewerkingen voor een gebruikersobject:

* Een nieuwe gebruiker maken in een map
* De gedetailleerde eigenschappen van een gebruiker, zoals de groepen,
* De eigenschappen van een gebruiker bijwerken, zoals de locatie en het telefoonnummer, of het wachtwoord wijzigen
* Het groepslidmaatschap van een gebruiker controleren op toegang op basis van rollen
* Het account van een gebruiker uitschakelen of volledig verwijderen

Bovendien u vergelijkbare bewerkingen uitvoeren op andere objecten, zoals groepen en toepassingen. Als u Azure AD Graph API wilt aanroepen in een map, moet uw toepassing zijn geregistreerd bij Azure AD. Uw toepassing moet ook toegang krijgen tot de Azure AD Graph API. Deze toegang wordt normaal gesproken bereikt via een toestemmingsstroom van een gebruiker of beheerder.

Zie de [quickstart-handleiding azure AD Graph API](active-directory-graph-api-quickstart.md)of bekijk de [interactieve AD Graph API-referentiedocumentatie](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)voor Azure Active Directory Graph.

## <a name="features"></a>Functies

Azure AD Graph API biedt de volgende functies:

* **REST API-eindpunten**: Azure AD Graph API is een RESTful-service die bestaat uit eindpunten die worden geopend met behulp van standaard HTTP-aanvragen. Azure AD Graph API ondersteunt JSON-inhoudstypen (XML- of Javascript-objectnotatie) voor aanvragen en antwoorden. Zie [Api-verwijzing naar Azure AD Graph REST API voor](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)meer informatie .
* **Verificatie met Azure AD**: elke aanvraag voor Azure AD Graph API moet worden geverifieerd door een JSON Web Token (JWT) toe te nemen in de autorisatiekop van de aanvraag. Dit token wordt verkregen door een aanvraag in te dienen voor het tokeneindpunt van Azure AD en geldige referenties te verstrekken. U de stroom van OAuth 2.0-clientreferenties of de autorisatiecodesubsidiestroom gebruiken om een token te verkrijgen om de grafiek aan te roepen. Voor meer informatie, [OAuth 2.0 in Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* **Op rollen gebaseerde autorisatie (RBAC)**: Beveiligingsgroepen worden gebruikt om RBAC uit te voeren in azure AD Graph API. Als u bijvoorbeeld wilt bepalen of een gebruiker toegang heeft tot een specifieke bron, kan de toepassing de bewerking [Groepslidmaatschap controleren (transitief)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/functions-and-actions#checkMemberGroups) aanroepen, die waar of onwaar retourneert.
* **Differentiële query:** met differentiële query u wijzigingen in een map tussen twee perioden bijhouden zonder dat u regelmatig query's hoeft aan te maken voor de Azure AD Graph API. Dit type aanvraag retourneert alleen de wijzigingen die zijn aangebracht tussen de vorige differentiële queryaanvraag en de huidige aanvraag. Zie [Azure AD Graph API differentieelquery](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query)voor meer informatie .
* **Directory-extensies:** u aangepaste eigenschappen toevoegen aan directoryobjecten zonder dat er een extern gegevensarchief nodig is. Als uw toepassing bijvoorbeeld een Skype ID-eigenschap voor elke gebruiker vereist, u de nieuwe eigenschap in de map registreren en is deze beschikbaar voor gebruik op elk gebruikersobject. Zie Azure [AD Graph API directory-map-extensies](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)voor meer informatie .
* **Beveiligd door machtigingsscopes:** Azure AD Graph API stelt machtigingsscopen bloot waarmee beveiligde toegang tot Azure AD-gegevens met OAuth 2.0 mogelijk is. Het ondersteunt verschillende typen client-app's, waaronder:
  
  * gebruikersinterfaces die gedelegeerde toegang tot gegevens krijgen via autorisatie van de aangemelde gebruiker (gedelegeerd)
  * service/daemon-toepassingen die op de achtergrond werken zonder dat een aangemelde gebruiker aanwezig is en toepassingsgedefinieerde role-based toegangscontrole gebruiken
    
    Zowel gedelegeerde als toepassingsmachtigingen vertegenwoordigen een privilege die wordt weergegeven door de Azure AD Graph API en kan worden aangevraagd door clienttoepassingen via functies voor toepassingsregistratiemachtigingen in de [Azure-portal.](https://portal.azure.com) [De machtigingenscopes voor Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) bevatten informatie over wat beschikbaar is voor gebruik door uw clienttoepassing.

## <a name="scenarios"></a>Scenario's

Azure AD Graph API maakt veel toepassingsscenario's mogelijk. De volgende scenario's komen het meest voor:

* **Line of Business (Single Tenant) Toepassing:** In dit scenario werkt een bedrijfsontwikkelaar voor een organisatie met een Office 365-abonnement. De ontwikkelaar bouwt een webtoepassing die samenwerkt met Azure AD om taken uit te voeren, zoals het toewijzen van een licentie aan een gebruiker. Deze taak vereist toegang tot de Azure AD Graph API, zodat de ontwikkelaar de enkele tenanttoepassing registreert in Azure AD en lees- en schrijfmachtigingen configureert voor Azure AD Graph API. Vervolgens is de toepassing geconfigureerd om zijn eigen referenties te gebruiken of die van de momenteel aanmeldingsgebruiker om een token te verkrijgen om de Azure AD Graph API aan te roepen.
* **Software as a Service Application (Multi-Tenant)**: In dit scenario ontwikkelt een onafhankelijke softwareleverancier (ISV) een gehoste multi-tenant webtoepassing die gebruikersbeheerfuncties biedt voor andere organisaties die Azure AD gebruiken. Deze functies vereisen toegang tot directoryobjecten, dus de toepassing moet de Azure AD Graph API aanroepen. De ontwikkelaar registreert de toepassing in Azure AD, configureert deze om lees- en schrijfmachtigingen voor Azure AD Graph API te vereisen en schakelt vervolgens externe toegang in, zodat andere organisaties toestemming kunnen geven om de toepassing in hun directory te gebruiken. Wanneer een gebruiker in een andere organisatie zich voor het eerst bij de toepassing verifieert, krijgt deze een toestemmingsdialoogvenster te zien met de machtigingen die de toepassing aanvraagt. Als u toestemming verleent, geeft u de toepassing vervolgens de gevraagde machtigingen voor azure AD Graph API in de gebruikersmap. Zie [Overzicht van het toestemmingskader](consent-framework.md)voor meer informatie over het toestemmingskader .

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen om de API voor Azure Active Directory Graph te gaan gebruiken:

* [QuickStart-handleiding voor Azure AD Graph API](active-directory-graph-api-quickstart.md)
* [Azure AD Graph REST-documentatie](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
