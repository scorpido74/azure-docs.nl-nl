---
title: Aanbiedings instellingen voor een advies service-aanbieding definiëren | Azure Marketplace
description: Definieer aanbiedings instellingen in een Azure-of Dynamics 365 consulting service-aanbieding in het Cloud Partner-portal voor de Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 8a89c1c548469b568a34521b240683af285c2342
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73485188"
---
# <a name="offer-settings-tab"></a>Tabblad voor aanbiedingsinstellingen

In het scherm **nieuwe aanbieding** is de eerste stap het maken van de aanbiedings identiteit. De identiteit van de aanbieding bestaat uit drie delen: **aanbiedings-id**, **uitgevers-id**en **naam**. Elk van deze onderdelen wordt behandeld in de volgende secties.

![Een nieuw advies service-aanbod maken-aanbod instellingen tabblad](media/consultingoffer-settings-tab.png)


### <a name="offer-id"></a>Aanbiedings-ID *

Deze id is een unieke naam die u maakt wanneer u de aanbieding voor het eerst verzendt. De naam mag alleen bestaan uit kleine letters, streepjes of onderstrepings tekens. De **aanbiedings-id** is zichtbaar in de URL en is van invloed op de resultaten van de zoek machine. Een voor beeld is *yourcompanyname_exampleservice*.

Zoals in het voor beeld wordt weer gegeven, wordt de **aanbiedings-id** toegevoegd aan de uitgevers-id om een unieke id te maken. Deze unieke id wordt weer gegeven als een permanente koppeling die kan worden geboekt en wordt geïndexeerd door de zoek machines.

>[!Note]
>Nadat een aanbieding Live is, kan de id ervan niet worden bijgewerkt.


### <a name="publisher-id"></a>Uitgevers-ID *

Deze id is gerelateerd aan uw account. Nadat u zich hebt aangemeld met uw organisatie account, wordt uw **uitgevers-id** weer gegeven in de vervolg keuzelijst.


### <a name="name"></a>Naam

Deze teken reeks wordt weer gegeven als de naam van het aanbod op AppSource of in de Azure Marketplace. Het vak **naam** is beperkt tot 50 tekens. De revisor moet mogelijk uw titel bewerken om de duur en het aanbiedings type toe te voegen aan de naam van uw aanbieding.

In het volgende voor beeld ziet u hoe de naam van het aanbod wordt geassembleerd. 

![Een nieuwe advies service-aanbieding maken](media/cppsampleconsultingoffer.png)

De naam van het aanbod bestaat uit vier delen:

-   **Duur:** Gedefinieerd op het tabblad Details van de **winkel** van de editor. De duur kan worden uitgedrukt in uren, dagen of weken.
-   **Type service:** Gedefinieerd op het tabblad Details van de **winkel** van de editor. Typen services zijn `Assessment`, `Briefing`, `Implementation`, `Proof of concept`en `Workshop`.
-   Voor **positie:** Ingevoegd door de revisor.
-   **Naam:** Gedefinieerd op de pagina **aanbiedings instellingen** .

>[!Note]
>Het vak **naam** is beperkt tot 50 tekens. De revisor moet mogelijk uw titel bewerken om de duur en het aanbiedings type toe te voegen aan de naam van uw aanbieding.

De volgende lijst bevat verschillende namen van aanbiedingen van de naam:

-   Essentiële zaken voor professionele services: 1-HR-informatie
-   Cloudmigratie-platform: 1-HR-informatie
-   PowerApps en Microsoft Flow: 1 dag workshop
-   Azure Machine Learning: haalbaarheids test voor 3 weken
-   Brick en klik op Retail-oplossing: 1-HR-informatie
-   Uw eigen gegevens meenemen: 1 WK workshop
-   Cloud Analytics: 3-daags workshop
-   Power BI training: 3-daags workshop
-   Verkoop beheer oplossing: implementatie van 1 week
-   CRM-Snelstartgids: 1 dag workshop
-   Dynamics 365 voor verkoop: evaluatie van 2 dagen

Nadat u het tabblad Instellingen voor de **aanbieding** hebt ingevuld, slaat u uw inzending op. De naam van de aanbieding wordt nu boven de editor weer gegeven en u kunt deze in **alle aanbiedingen**vinden.

## <a name="next-steps"></a>Volgende stappen

U kunt nu [Details van de winkel opgeven en bepalen of u wilt publiceren in azure Marketplace of op AppSource](./cpp-consulting-service-storefront-details.md).
