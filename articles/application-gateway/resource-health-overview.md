---
title: Overzicht van azure application gateway-bronstatus
description: Dit artikel is een overzicht van de functie resourcestatus voor Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: db29551a8150b70e797d45fe659482470c8aca2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67659500"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Overzicht van azure application gateway-bronstatus

[Azure Resource Health](../service-health/resource-health-overview.md) helpt u bij het stellen van diagnoses en bij het verkrijgen van ondersteuning wanneer een Azure-serviceprobleem gevolgen heeft voor uw resources. Het informeert u over de huidige en vroegere gezondheid van uw middelen. En het biedt technische ondersteuning om u te helpen problemen te beperken.

Voor Application Gateway is Resource Health gebaseerd op signalen die door de gateway worden uitgezonden om te beoordelen of deze in orde is of niet. Als de gateway niet in orde is, analyseert Resource Health aanvullende informatie om de bron van het probleem te bepalen. Het identificeert ook acties die Microsoft onderneemt of wat u doen om het probleem op te lossen.

Raadpleeg de volledige lijst met resourcetypen en statuscontroles in [Azure Resource Health](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways)voor meer informatie over hoe de status wordt beoordeeld.


De status van Application Gateway wordt weergegeven als een van de volgende statussen:

## <a name="available"></a>Beschikbaar

Een **status beschikbaar** betekent dat de service geen gebeurtenissen heeft gedetecteerd die van invloed zijn op de status van de resource. U ziet de **onlangs opgeloste** melding in gevallen waarin de gateway is hersteld van ongeplande downtime in de afgelopen 24 uur.

![Beschikbare status](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>Niet beschikbaar

Een **niet-beschikbare** status betekent dat de service een doorlopend platform of niet-platformgebeurtenis heeft gedetecteerd die de status van de gateway beïnvloedt.

### <a name="platform-events"></a>Platformevenementen

Platformgebeurtenissen worden geactiveerd door meerdere onderdelen van de Azure-infrastructuur. Ze omvatten zowel geplande acties (bijvoorbeeld gepland onderhoud) als onverwachte incidenten (bijvoorbeeld een ongeplande herstart van de host).

Resourcestatus bevat aanvullende details over de gebeurtenis en het herstelproces. Het stelt u ook in staat om contact op te nemen met ondersteuning, zelfs als u geen actieve Microsoft-ondersteuningsovereenkomst hebt.

![Niet-beschikbare status](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Onbekend

De **status Onbekende** status geeft aan dat Resource Health al meer dan 10 minuten geen informatie over de gateway heeft ontvangen. Deze status is geen definitieve indicatie van de status van de gateway. Maar het is een belangrijk gegevenspunt in het probleemoplossingsproces.

Als de gateway wordt uitgevoerd zoals verwacht, wordt de status na enkele minuten gewijzigd in **Beschikbaar.**

Als u problemen ondervindt, kan de **status Onbekende** suggereren dat een gebeurtenis in het platform van invloed is op de gateway.

![Onbekende status](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>Verminderd beschikbaar

De **status Gedegradeerde** status geeft aan dat uw gateway een prestatieverlies heeft gedetecteerd, hoewel deze nog steeds beschikbaar is voor gebruik.

![Degrated status](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>Volgende stappen

Zie [WebApplication Firewall (WAF) voor Azure Application Gateway voor](web-application-firewall-troubleshoot.md)meer informatie over het oplossen van problemen met Application Gateway (WAF).