---
title: Cloud Partner-portal API-verwijzing | Azure Marketplace
description: Beschrijving van, vereiste te gebruiken en lijst met Marketplace API-bewerkingen.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: b6591e1780d03cbfaff70fbd19ec3dfd274fae79
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819633"
---
<a name="cloud-partner-portal-api-reference"></a>Naslag informatie over Cloud Partner-portal-API
==================================

Met de Cloud Partner-portal REST Api's kan werk belastingen, aanbiedingen en Publisher-profielen programmatisch worden opgehaald en gemanipuleerd. De Api's gebruiken op rollen gebaseerd toegangs beheer (RBAC) om de juiste machtigingen af te dwingen tijdens de verwerkings tijd.

Deze Naslag informatie bevat technische Details voor de Cloud Partner-portal REST-Api's. De payload-voor beelden in dit document zijn alleen ter referentie en zijn onderhevig aan wijzigingen wanneer er nieuwe functionaliteit wordt toegevoegd.


<a name="prerequisites-and-considerations"></a>Vereisten en overwegingen
-------------------------------

Voordat u de Api's kunt gebruiken, moet u het volgende controleren:

- Het artikel [vereisten](./cloud-partner-portal-api-prerequisites.md) vindt u informatie over het toevoegen van een service-principal aan uw account en het verkrijgen van een toegangs token voor Azure Active Directory (Azure AD) voor verificatie. 
- Het twee [gelijktijdigheids beheer](./cloud-partner-portal-api-concurrency-control.md).
strategieën die beschikbaar zijn voor het aanroepen van deze Api's.
- Aanvullende API- [overwegingen](./cloud-partner-portal-api-considerations.md), zoals versie beheer en fout afhandeling.


<a name="common-tasks"></a>Algemene taken
------------
Deze referentie Details Api's om de volgende algemene taken uit te voeren.


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

### <a name="other-tasks"></a>Andere taken

-   [Prijzen voor aanbiedingen van virtuele machines instellen](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Problemen oplossen

-   [Problemen met verificatiefouten oplossen](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
