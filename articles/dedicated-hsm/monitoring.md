---
title: Bewakingsopties - Azure Dedicated HSM | Microsoft Documenten
description: Overzicht van Azure Dedicated HSM-bewakingsopties en controleverantwoordelijkheden
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: 3fde577a6b0efb7584e1c9efd57c95583ebe4ec9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70881417"
---
# <a name="azure-dedicated-hsm-monitoring"></a>Azure Dedicated HSM-bewaking

De Azure Dedicated HSM-service biedt een fysiek apparaat voor uitsluitend gebruik door de klant met volledige beheercontrole en beheerverantwoordelijkheid. Het apparaat ter beschikking gesteld is een [Gemalto SafeNet Luna 7 HSM model A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/).  Microsoft heeft geen beheerderstoegang zodra deze door een klant is ingericht, naast fysieke seriële poortbijlage als controlerol. Als gevolg hiervan zijn klanten verantwoordelijk voor typische operationele activiteiten, waaronder uitgebreide monitoring en loganalyse.
Klanten zijn volledig verantwoordelijk voor toepassingen die gebruik maken van de HSM's en moeten samenwerken met Gemalto voor ondersteuning of advies. Vanwege de mate van klantbezit van operationele hygiëne, is het niet mogelijk voor Microsoft om enige vorm van hoge beschikbaarheid garantie voor deze dienst te bieden. Het is de verantwoordelijkheid van de klant om ervoor te zorgen dat hun toepassingen correct zijn geconfigureerd om een hoge beschikbaarheid te bereiken. Microsoft controleert en onderhoudt de status van het apparaat en de netwerkconnectiviteit.

## <a name="microsoft-monitoring"></a>Microsoft-controle

Het gebruikte Gemalto SafeNet-apparaat heeft standaard SNMP en seriële poort als opties voor het bewaken van het apparaat. Microsoft heeft de seriële poortverbinding gebruikt als een fysiek middel om verbinding te maken met het apparaat om basistelemetrie op de status van het apparaat op te halen. Dit omvat items zoals temperatuur en component status, zoals voedingen en ventilatoren.
Om dit te bereiken, gebruikt Microsoft een niet-administratieve "monitor"-rol die is ingesteld op het Gemalto-apparaat. Deze rol geeft de mogelijkheid om de telemetrie op te halen, maar geeft geen toegang tot het apparaat in termen van administratieve taak of op enigerlei wijze het bekijken van cryptografische informatie. Onze klanten kunnen er zeker van zijn dat hun apparaat echt hun eigen apparaat is om te beheren, beheren en gebruiken voor gevoelige cryptografische sleutelopslag. In het geval dat een klant niet tevreden is met deze minimale toegang voor elementaire gezondheidsmonitoring, hebben ze wel de mogelijkheid om de monitoringaccount uit te schakelen. Het voor de hand liggende gevolg hiervan is dat Microsoft geen informatie zal hebben en dus geen mogelijkheid om een proactieve kennisgeving van problemen met de gezondheid van apparaten te bieden. In deze situatie is de klant verantwoordelijk voor de gezondheid van het apparaat.
De monitorfunctie zelf is ingesteld om het apparaat elke 10 minuten te peilen om gezondheidsgegevens te krijgen. Vanwege de foutgevoelige aard van seriële communicatie, alleen na meerdere negatieve gezondheidsindicatoren over een periode van een uur zou een waarschuwing worden verhoogd. Deze waarschuwing zou uiteindelijk leiden tot een proactieve klantcommunicatie die het probleem op de hoogte brengt.
Afhankelijk van de aard van het probleem zou de juiste aanpak worden genomen om de impact te verminderen en te zorgen voor een laag risico herstel. Een stroomstoring is bijvoorbeeld een hot-swapprocedure zonder resulterende sabotagegebeurtenis, zodat deze kan worden uitgevoerd met een lage impact en een minimaal risico voor gebruik. Andere procedures kunnen vereisen dat een apparaat wordt gezeroiseerd en gedeprovisioneerd om eventuele beveiligingsrisico's voor de klant te minimaliseren. In deze situatie zou een klant een alternatief apparaat inrichten en opnieuw deelnemen aan een koppeling met hoge beschikbaarheid, waardoor apparaatsynchronisatie wordt geactiveerd. De normale werking zou in een minimale tijd worden hervat, met minimale verstoring en het laagste beveiligingsrisico.  

## <a name="customer-monitoring"></a>Klantbewaking

Een waardepropositie van de Dedicated HSM-service is de controle die de klant van het apparaat krijgt, vooral gezien het een cloudgeleverd apparaat is. Een gevolg van deze controle is de verantwoordelijkheid om de gezondheid van het apparaat te controleren en te beheren. Het Gemalto SafeNet-apparaat wordt geleverd met richtlijnen voor de implementatie van SNMP en Syslog. Klanten van de Dedicated HSM-service wordt aangeraden dit te gebruiken, zelfs wanneer het Microsoft-monitoraccount actief blijft en moet het als verplicht worden gesteld als ze het Microsoft-monitoraccount uitschakelen.
Beide beschikbare technieken zou een klant in staat stellen om problemen te identificeren en Microsoft-ondersteuning te bellen om de juiste herstelwerkzaamheden te starten.

## <a name="next-steps"></a>Volgende stappen

Het wordt aanbevolen dat alle belangrijke concepten van de service, zoals hoge beschikbaarheid en beveiliging bijvoorbeeld, goed worden begrepen voordat een apparaat inrichting en applicatie ontwerp of implementatie. Verdere concept niveau onderwerpen:

* [Hoge beschikbaarheid](high-availability.md)
* [Fysieke beveiliging](physical-security.md)
* [Networking](networking.md)
* [Ondersteuning](supportability.md)
