---
title: Apps & service-principals in azure AD | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over de relatie tussen toepassings-en Service-Principal-objecten in Azure Active Directory.
author: rwike77
manager: CelesteDG
services: active-directory
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/13/2019
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: 19085346fb5797245c9f71911f8178df0a1b742a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370284"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Toepassings-en Service-Principal-objecten in Azure Active Directory

Soms kan de betekenis van de term ' Application ' in de context van Azure Active Directory (Azure AD) worden gebruikt. In dit artikel worden de conceptuele en concrete aspecten van de integratie van Azure AD-toepassingen verduidelijkt, met een afbeelding van registratie en toestemming voor een [multi tenant-toepassing](developer-glossary.md#multi-tenant-application).

## <a name="overview"></a>Overzicht

Een toepassing die is geïntegreerd met Azure AD heeft gevolgen voor meer dan het software-aspect. "Toepassing" wordt vaak gebruikt als een conceptuele term en verwijst naar niet alleen de toepassings software, maar ook de Azure AD-registratie en rol bij de verificatie/autorisatie ' conversaties ' tijdens runtime.

Een toepassing kan per definitie worden gebruikt in de volgende rollen:

- [Client](developer-glossary.md#client-application) functie (verbruiken van een resource)
- [Resource server](developer-glossary.md#resource-server) functie (api's beschikbaar stellen aan clients)
- Zowel de client functie als de resource serverrol

Een [OAuth 2,0-autorisatie](developer-glossary.md#authorization-grant) toewijzings stroom definieert het conversatie protocol, waarmee de client/resource de gegevens van een resource respectievelijk kan openen/beveiligen.

In de volgende secties ziet u hoe het Azure AD-toepassings model een toepassing vertegenwoordigt tijdens de ontwerp fase en runtime.

## <a name="application-registration"></a>Een toepassing registreren

Wanneer u een Azure AD-toepassing registreert in de [Azure Portal][AZURE-Portal], worden er twee objecten gemaakt in uw Azure AD-Tenant:

- Een toepassings object en
- Een Service-Principal-object

### <a name="application-object"></a>Toepassings object

Een Azure AD-toepassing wordt gedefinieerd door de ene en enige toepassings object, die zich bevindt in de Azure AD-Tenant waar de toepassing is geregistreerd, ook wel de ' Home '-Tenant van de toepassing genoemd. De entiteit van de Microsoft Graph- [toepassing][MS-Graph-App-Entity] definieert het schema voor de eigenschappen van een toepassings object.

### <a name="service-principal-object"></a>Service-Principal-object

Om toegang te krijgen tot bronnen die worden beveiligd door een Azure AD-Tenant, moet de entiteit die toegang vereist, worden vertegenwoordigd door een beveiligingsprincipal. Dit geldt voor zowel gebruikers (gebruikers-principal) als toepassingen (Service-Principal).

De beveiligingsprincipal definieert het toegangs beleid en de machtigingen voor de gebruiker/toepassing in de Azure AD-Tenant. Hierdoor kunnen kern functies, zoals verificatie van de gebruiker/toepassing tijdens het aanmelden, en autorisatie tijdens de toegang tot bronnen worden ingeschakeld.

Wanneer een toepassing toestemming krijgt om toegang te krijgen tot bronnen in een Tenant (na registratie of [toestemming](developer-glossary.md#consent)), wordt er een Service-Principal-object gemaakt. De [entiteit Microsoft Graph ServicePrincipal][MS-Graph-Sp-Entity] definieert het schema voor de eigenschappen van een Service Principal-object.

### <a name="application-and-service-principal-relationship"></a>Relatie tussen toepassing en Service-Principal

Overweeg het toepassings object als *globale* weer gave van uw toepassing voor gebruik in alle tenants en de Service-Principal als de *lokale* weer gave voor gebruik in een specifieke Tenant.

Het object Application fungeert als de sjabloon waarvan de algemene en standaard eigenschappen zijn *afgeleid* voor gebruik bij het maken van bijbehorende service-principal-objecten. Een toepassings object heeft daarom een 1:1-relatie met de software toepassing en een 1: veel relaties met de bijbehorende service-principal-object (en).

Een Service-Principal moet worden gemaakt in elke Tenant waar de toepassing wordt gebruikt, waardoor er een identiteit kan worden ingesteld voor aanmelding en/of toegang tot bronnen die worden beveiligd door de Tenant. Een toepassing met één Tenant heeft slechts één service-principal (in de eigen Tenant), die is gemaakt en wordt ingestemd voor gebruik tijdens de registratie van de toepassing. Een webtoepassing/API met meerdere tenants bevat ook een service-principal die is gemaakt in elke Tenant, waar een gebruiker van die Tenant toestemming heeft gegeven om het te gebruiken.

> [!NOTE]
> Alle wijzigingen die u aanbrengt in het toepassings object, worden ook weer gegeven in het Service-Principal-object in de thuis Tenant van de toepassing (de Tenant waar deze is geregistreerd). Voor multi tenant-toepassingen worden wijzigingen aan het toepassings object niet weer gegeven in de Service-Principal-objecten van een consumenten Tenant, totdat de toegang wordt verwijderd via het [toegangs venster](https://myapps.microsoft.com) van de toepassing en opnieuw wordt toegewezen.
>
> Houd er rekening mee dat systeem eigen toepassingen standaard worden geregistreerd als multi tenant.

## <a name="example"></a>Voorbeeld

In het volgende diagram ziet u de relatie tussen het toepassings object van een toepassing en de bijbehorende service-principal-objecten, in de context van een voor beeld van een multi tenant-toepassing met de naam **HR-app**. Er zijn drie Azure AD-tenants in dit voorbeeld scenario:

- **Adatum** : de Tenant die wordt gebruikt door het bedrijf dat de **HR-app** heeft ontwikkeld
- **Contoso** : de Tenant die wordt gebruikt door de contoso-organisatie, die een consument is van de **HR-app**
- **Fabrikam** : de Tenant die wordt gebruikt door de fabrikam-organisatie, die ook de **HR-app** verbruikt

![Relatie tussen object App-object en Service-Principal](./media/app-objects-and-service-principals/application-objects-relationship.svg)

In dit voorbeeld scenario:

| Stap | Beschrijving |
|------|-------------|
| 1    | Is het proces van het maken van de toepassing en Service-Principal-objecten in de thuis Tenant van de toepassing. |
| 2    | Wanneer Contoso en fabrikam beheerders toestemming geven, wordt een Service-Principal-object gemaakt in de Azure AD-Tenant van het bedrijf en worden de machtigingen toegewezen die de beheerder heeft verleend. Houd er ook rekening mee dat de HR-app kan worden geconfigureerd/ontworpen om toestemming door gebruikers voor individueel gebruik toe te staan. |
| 3    | De tenants van de consument van de HR-toepassing (Contoso en fabrikam) hebben elk een eigen Service-Principal-object. Elk voor beeld hiervan is het gebruik van een exemplaar van de toepassing tijdens runtime, met de machtigingen die de respectieve beheerder heeft ingestemd. |

## <a name="next-steps"></a>Volgende stappen

- U kunt de [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) gebruiken om de toepassing en Service-Principal-objecten op te vragen.
- U kunt toegang krijgen tot het toepassings object van een toepassing met behulp van de Microsoft Graph-API, de manifest-editor [van de Azure Portal][AZURE-Portal] of [Azure AD Power shell-cmdlets](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), zoals wordt vertegenwoordigd door de entiteit van de OData- [toepassing][MS-Graph-App-Entity].
- U kunt toegang krijgen tot het Service-Principal-object van een toepassing via de Microsoft Graph-API of [Azure AD Power shell-cmdlets](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), zoals wordt aangegeven door de OData [ServicePrincipal-entiteit][MS-Graph-Sp-Entity].

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: https://docs.microsoft.com/graph/api/resources/application
[MS-Graph-Sp-Entity]: https://docs.microsoft.com/graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
