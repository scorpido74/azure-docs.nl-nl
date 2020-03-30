---
title: Referentie voor API-api's voor cloudpartners | Azure Marketplace
description: Beschrijving van, voorwaarden om te gebruiken en lijst van marketplace API-bewerkingen.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 296c667876807bdd05a6281de461df76207b5490
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288543"
---
<a name="cloud-partner-portal-api-reference"></a>Api-verwijzing naar cloudpartnersportal
==================================

De API's van Cloud Partner Portal REST maken het programmatisch ophalen en manipuleren van workloads, aanbiedingen en uitgeversprofielen mogelijk. De API's gebruiken op rollen gebaseerd toegangscontrole (RBAC) om de juiste machtigingen af te dwingen tijdens het verwerken.

Deze referentie bevat de technische details voor de API's van cloudpartnerportal REST. De payload monsters in dit document zijn alleen ter referentie en zijn onderhevig aan verandering als nieuwe functionaliteit wordt toegevoegd.


<a name="prerequisites-and-considerations"></a>Voorwaarden en overwegingen
-------------------------------

Voordat u de API's gebruikt, moet u het:

- Het artikel [Voorwaarden](./cloud-partner-portal-api-prerequisites.md) voor meer informatie over het toevoegen van een serviceprincipal aan uw account en het krijgen van een Azure Active Directory-toegangstoken (Azure AD) voor verificatie. 
- De twee [gelijktijdigheidscontrole](./cloud-partner-portal-api-concurrency-control.md).
strategieën die beschikbaar zijn om deze API's te bellen.
- Aanvullende [API-overwegingen,](./cloud-partner-portal-api-considerations.md)zoals versiebeheer en foutafhandeling.


<a name="common-tasks"></a>Algemene taken
------------
Deze verwijzing bevat API's om de volgende algemene taken uit te voeren.


### <a name="offers"></a>Aanbiedingen

-   [Alle aanbiedingen ophalen](./cloud-partner-portal-api-retrieve-offers.md)
-   [Een specifieke aanbieding ophalen](./cloud-partner-portal-api-retrieve-specific-offer.md)
-   [Aanbiedingsstatus ophalen](./cloud-partner-portal-api-retrieve-offer-status.md)
-   [Een aanbieding maken](./cloud-partner-portal-api-creating-offer.md)
-   [Een aanbieding publiceren](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Bewerkingen

-   [Bewerkingen ophalen](./cloud-partner-portal-api-retrieve-operations.md)
-   [Bewerkingen annuleren](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Een app publiceren

-   [Live gaan](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Overige taken

-   [Prijsinstellen voor aanbiedingen voor virtuele machines](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Problemen oplossen

-   [Problemen met verificatiefouten oplossen](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
