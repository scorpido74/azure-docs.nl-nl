---
title: Apps & serviceprincipals in Azure AD | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over de relatie tussen hoofdobjecten voor toepassingen en services in Azure Active Directory.
author: rwike77
manager: CelesteDG
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/13/2019
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: a636ff15da09bcf1891618d65270376f26fd3239
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885596"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Toepassings- en service-principal-objecten in Azure Active Directory

Soms kan de betekenis van de term 'toepassing' worden verkeerd begrepen wanneer deze wordt gebruikt in de context van Azure Active Directory (Azure AD). In dit artikel worden de conceptuele en concrete aspecten van azure AD-toepassingsintegratie verduidelijkt, met een illustratie van registratie en toestemming voor een [multi-tenanttoepassing.](developer-glossary.md#multi-tenant-application)

## <a name="overview"></a>Overzicht

Een toepassing die is geïntegreerd met Azure AD heeft implicaties die verder gaan dan het softwareaspect. "Toepassing" wordt vaak gebruikt als een conceptuele term, verwijzend naar niet alleen de toepassingssoftware, maar ook de Azure AD-registratie en rol in authenticatie / autorisatie "gesprekken" tijdens uitvoering.

Per definitie kan een toepassing in deze rollen functioneren:

- [Clientrol](developer-glossary.md#client-application) (een resource verbruiken)
- [Functie resourceserver](developer-glossary.md#resource-server) (API's blootstellen aan clients)
- Zowel clientrol als resourceserverrol

Een [OAuth 2.0 Authorization Grant flow](developer-glossary.md#authorization-grant) definieert het gespreksprotocol, waarmee de client/resource toegang heeft tot de gegevens van een resource.

In de volgende secties ziet u hoe het Azure AD-toepassingsmodel een toepassing vertegenwoordigt op ontwerptijd en run-time.

## <a name="application-registration"></a>Een toepassing registreren

Wanneer u een Azure AD-toepassing registreert in de [Azure-portal,][AZURE-Portal]worden twee objecten gemaakt in uw Azure AD-tenant:

- Een toepassingsobject en
- Een service-principal-object

### <a name="application-object"></a>Toepassingsobject

Een Azure AD-toepassing wordt gedefinieerd door het enige toepassingsobject, dat zich bevindt in de Azure AD-tenant waar de toepassing is geregistreerd, bekend als de 'thuis'-tenant van de toepassing. De entiteit Microsoft Graph [Application][MS-Graph-App-Entity] definieert het schema voor de eigenschappen van een toepassingsobject.

### <a name="service-principal-object"></a>Hoofdobject voor service

Om toegang te krijgen tot bronnen die zijn beveiligd door een Azure AD-tenant, moet de entiteit die toegang nodig heeft, worden vertegenwoordigd door een beveiligingsprincipal. Dit geldt zowel voor gebruikers (user principal) als voor toepassingen (serviceprincipal).

De beveiligingsprincipal definieert het toegangsbeleid en de machtigingen voor de gebruiker/toepassing in de Azure AD-tenant. Dit maakt kernfuncties mogelijk, zoals verificatie van de gebruiker/toepassing tijdens het aanmelden en autorisatie tijdens toegang tot bronnen.

Wanneer een toepassing toestemming krijgt om toegang te krijgen tot bronnen in een tenant (na registratie of [toestemming),](developer-glossary.md#consent)wordt een serviceprincipal-object gemaakt. De entiteit Microsoft Graph [ServicePrincipal][MS-Graph-Sp-Entity] definieert het schema voor de eigenschappen van een serviceprincipalobject.

### <a name="application-and-service-principal-relationship"></a>Relatie tussen toepassing en service-principal

Beschouw het toepassingsobject als de *globale* weergave van uw toepassing voor gebruik voor alle tenants en de serviceprincipal als de *lokale* vertegenwoordiging voor gebruik in een specifieke tenant.

Het toepassingsobject fungeert als de sjabloon waaruit gemeenschappelijke en standaardeigenschappen worden *afgeleid* voor gebruik bij het maken van de bijbehorende service-principal-objecten. Een toepassingsobject heeft daarom een 1:1-relatie met de softwaretoepassing en een 1:00-verhouding met het bijbehorende servicehoofdobject(en).

Er moet een serviceprincipal worden gemaakt in elke tenant waar de toepassing wordt gebruikt, zodat deze een identiteit kan vaststellen voor aanmelding en/of toegang tot resources die door de tenant worden beveiligd. Een toepassing in één tenant heeft slechts één service-principal (in de starttenant) die is gemaakt en waarvoor toestemming is gegeven voor gebruik tijdens de toepassingsregistratie. Een multi-tenant webtoepassing/API heeft ook een serviceprincipal die is gemaakt in elke tenant waar een gebruiker van die tenant toestemming heeft gegeven voor het gebruik ervan.

> [!NOTE]
> Alle wijzigingen die u aanbrengt in uw toepassingsobject, worden ook alleen weergegeven in het hoofdobject van de serviceservice in de huistenant van de toepassing (de tenant waar het is geregistreerd). Voor toepassingen met meerdere tenants worden wijzigingen in het toepassingsobject niet weergegeven in de servicehoofdobjecten van consumententenants, totdat de toegang is verwijderd via het [Toepassingstoegangspaneel](https://myapps.microsoft.com) en opnieuw wordt verleend.
>
> Houd er ook rekening mee dat native applicaties standaard als multi-tenant worden geregistreerd.

## <a name="example"></a>Voorbeeld

In het volgende diagram wordt de relatie weergegeven tussen het toepassingsobject van een toepassing van een toepassing en de bijbehorende servicehoofdobjecten, in de context van een voorbeeldtoepassing met meerdere tenants, **de HR-app.** In dit voorbeeldscenario zijn drie Azure AD-tenants:

- **Adatum** - De huurder die wordt gebruikt door het bedrijf dat de **HR-app** ontwikkelde
- **Contoso** - De huurder gebruikt door de Contoso organisatie, die een consument van de **HR app**
- **Fabrikam** - De huurder gebruikt door de Fabrikam organisatie, die ook de **HR app** verbruikt

![Relatie tussen app-object en serviceprincipalobject](./media/app-objects-and-service-principals/application-objects-relationship.svg)

In dit voorbeeldscenario:

| Stap | Beschrijving |
|------|-------------|
| 1    | Is het proces van het maken van de toepassing en service belangrijkste objecten in de toepassing thuis tenant. |
| 2    | Wanneer de beheerders van Contoso en Fabrikam de toestemming hebben gegeven, wordt een serviceprincipalobject gemaakt in de Azure AD-tenant van hun bedrijf en worden de machtigingen toegewezen die de beheerder heeft verleend. Houd er ook rekening mee dat de HR-app kan worden geconfigureerd /ontworpen om toestemming van gebruikers voor individueel gebruik mogelijk te maken. |
| 3    | De consumentenhuurders van de HR-applicatie (Contoso en Fabrikam) hebben elk hun eigen servicehoofdbezwaar. Elk vertegenwoordigt hun gebruik van een instantie van de toepassing tijdens de runtime, geregeld door de machtigingen die zijn toegestaan door de desbetreffende beheerder. |

## <a name="next-steps"></a>Volgende stappen

- U de [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) gebruiken om zowel de hoofdobjecten van de toepassing als de service op te vragen.
- U hebt toegang tot het toepassingsobject van een toepassing van een toepassing met de Microsoft Graph API, de toepassingsmanifesteditor van de [Azure-portal][AZURE-Portal] of [Azure AD PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), zoals vertegenwoordigd door de entiteit OData-toepassing . [Application entity][MS-Graph-App-Entity]
- U hebt toegang tot het hoofdobject van de service van een toepassing via de Microsoft Graph API of [Azure AD PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), zoals vertegenwoordigd door de OData [ServicePrincipal-entiteit][MS-Graph-Sp-Entity].

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: https://docs.microsoft.com/graph/api/resources/application
[MS-Graph-Sp-Entity]: https://docs.microsoft.com/graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
