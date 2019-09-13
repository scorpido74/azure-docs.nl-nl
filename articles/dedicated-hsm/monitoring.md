---
title: Controle-opties - Azure toegewezen HSM | Microsoft Docs
description: Overzicht van Azure toegewezen HSM controle-opties en de controle van verantwoordelijkheden
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
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881417"
---
# <a name="azure-dedicated-hsm-monitoring"></a>Azure toegewezen HSM-bewaking

De Azure toegewezen HSM-Service biedt een fysiek apparaat voor gebruik door de enige klant met volledige controle en beheer van beheertaken. Het apparaat beschikbaar gesteld is een [Gemalto SafeNet Luna 7 HSM model A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/).  Microsoft heeft geen beheerderstoegang tot één keer worden ingericht door een klant, dan fysieke seriële poort bijlage als een controle rol. Als gevolg hiervan klanten zijn verantwoordelijk voor het normale operationele activiteiten, met inbegrip van uitgebreide bewaking en meld u analyse.
Klanten zijn zelf verantwoordelijk voor toepassingen die gebruikmaken van de HSM's en werkt met Gemalto voor ondersteuning of advies assistentie. Vanwege de omvang van de klant eigendom van operationele hygiëne is het niet mogelijk voor Microsoft te bieden van welke aard dan ook van hoge beschikbaarheidsgarantie voor deze service. Het is de verantwoordelijkheid van de klant om te controleren of hun toepassingen correct zijn geconfigureerd voor hoge beschikbaarheid. Microsoft zal bewaken en onderhouden van apparaat status en de netwerkverbinding.

## <a name="microsoft-monitoring"></a>Bewaking van Microsoft

Standaard SNMP-en seriële poort is op het apparaat Gemalto SafeNet in gebruik als opties voor het bewaken van het apparaat. Microsoft heeft de verbinding van de seriële poort die wordt gebruikt als een fysieke betekent verbinding maken met het apparaat om op te halen van basic telemetrie over de apparaatstatus. Dit omvat items zoals temperatuur en onderdeel de status, zoals voedingen en ventilatoren.
Microsoft gebruikt om dit te bereiken, een niet-beheerders 'controleren' rol op het apparaat Gemalto instellen. Deze rol biedt de mogelijkheid om op te halen van de telemetrie, maar geen toegang geeft tot het apparaat in termen van administratieve taken of op geen enkele manier cryptografische-informatie weergeven. Onze klanten kunnen hun apparaat is echt hun eigen te beheren, te beheren en te gebruiken voor gevoelige opslag van cryptografische sleutels worden gewaarborgd. Als een klant niet voldaan aan minimale toegang voor eenvoudige statuscontrole is, hebben ze de optie voor het uitschakelen van de account voor controle. De hand liggende gevolg hiervan is dat Microsoft geen informatie heeft en kan daarom geen mogelijkheid voor proactieve meldingen van de apparaatstatus problemen. In dit geval zijn de klant is verantwoordelijk voor de status van het apparaat.
De monitor-functie zelf is ingesteld om te pollen van het apparaat om de 10 minuten om de van gezondheidsgegevens. Vanwege de fout gevoelig aard van de seriële, pas na meerdere negatieve statusindicatoren gedurende een periode van één uur wordt een waarschuwing worden gegenereerd. Deze waarschuwing wordt uiteindelijk leiden tot een proactieve mededeling om het probleem te informeren.
Afhankelijk van de aard van het probleem zou worden verminderd en zorg ervoor dat met een laag risico herstel de juiste loop van de actie uitgevoerd. Een stroomstoring is bijvoorbeeld een hot-swap-procedure met geen resulterende gebeurtenis, zodat kan worden uitgevoerd met weinig impact en minimale risico's om de bewerking te kunnen knoeien. Overige procedures kunnen vereisen dat een apparaat worden zeroized en de inrichting ongedaan gemaakt om te beperken van eventuele beveiligingsrisico's naar de klant. In dit geval zou een klant een ander apparaat inrichten, weer een hoge beschikbaarheid koppelen dus activeren van de synchronisatie van apparaten. Normale werking weer in een minimale tijd, met minimale onderbreking en laagste beveiligingsrisico's.  

## <a name="customer-monitoring"></a>Bewaking van de klant

Een toegevoegde waarde van de toegewezen HSM-service is het besturingselement dat de klant opgehaald van het apparaat, met name overweegt is een cloud apparaat afgeleverd. Een gevolg van dit besturingselement is de verantwoordelijkheid om te controleren en beheren van de status van het apparaat. Het apparaat Gemalto SafeNet wordt geleverd met richtlijnen voor SNMP- en Syslog-implementatie. Klanten van de toegewezen HSM-service worden aanbevolen voor gebruik deze instelling als de Microsoft-account voor monitor actief blijft en moet rekening houden met het verplichte als ze het Microsoft-account voor monitor uitschakelen.
Een van beide techniek beschikbaar zou kunnen klanten om problemen te identificeren en contact opnemen met Microsoft ondersteuning voor het starten van de juiste herstel werk.

## <a name="next-steps"></a>Volgende stappen

Het is raadzaam dat alle belangrijke concepten van de service, zoals hoge beschikbaarheid en beveiliging bijvoorbeeld duidelijk zijn voordat een apparaat wordt ingericht en toepassingsontwerp of implementatie. Verdere concept niveau onderwerpen:

* [Hoge beschikbaarheid](high-availability.md)
* [Fysieke beveiliging](physical-security.md)
* [Netwerken](networking.md)
* [Ondersteuning](supportability.md)
