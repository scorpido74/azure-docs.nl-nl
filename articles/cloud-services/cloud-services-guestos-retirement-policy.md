---
title: Handleiding voor ondersteunings- en pensioenbeleid voor Azure Guest OS | Microsoft Documenten
description: Biedt informatie over wat Microsoft zal ondersteunen met betrekking tot het Azure Guest OS dat wordt gebruikt door Cloud Services.
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 919dd781-4dc6-4e50-bda8-9632966c5458
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 9/20/2017
ms.author: raiye
ms.openlocfilehash: fbe9b3379799fe3cf0a56d921ab257bc87606ca9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68945454"
---
# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Ondersteuning en pensioenbeleid voor Azure Guest OS
De informatie op deze pagina heeft betrekking op het Azure Guest-besturingssysteem[(Guest OS)](cloud-services-guestos-update-matrix.md)voor cloudservices-werknemers en webrollen (PaaS). Het is niet van toepassing op Virtual Machines (IaaS).

Microsoft heeft een gepubliceerd [ondersteuningsbeleid voor het gastbesturingssysteem](https://support.microsoft.com/gp/azure-cloud-lifecycle-faq). De pagina die u nu leest, beschrijft hoe het beleid wordt geïmplementeerd.

Het beleid is

1. Microsoft zal ten **minste de laatste twee families van het Gast-besturingssysteem**ondersteunen. Wanneer een gezin met pensioen is, hebben klanten 12 maanden vanaf de officiële pensioendatum om te updaten naar een nieuwere ondersteunde Guest OS-familie.
2. Microsoft ondersteunt **ten minste de laatste twee versies van de ondersteunde Guest OS-families.**
3. Microsoft ondersteunt **ten minste de laatste twee versies van de Azure SDK.** Wanneer een versie van de SDK is teruggetrokken, hebben klanten 12 maanden vanaf de officiële pensioendatum de tijd om bij te werken naar een nieuwere versie.

Soms kunnen meer dan twee gezinnen of releases worden ondersteund. Er worden officiële ondersteuningsinformatie voor gastbesturingssysteemweergegeven weergegeven op de [Azure Guest OS Releases en SDK Compatibility Matrix.](cloud-services-guestos-update-matrix.md)

## <a name="when-a-guest-os-version-is-retired"></a>Wanneer een gastversie van het besturingssysteem is uitgeschakeld
Nieuwe Guest OS **versies** worden geïntroduceerd ongeveer elke maand om de nieuwste MSRC updates op te nemen. Vanwege de reguliere maandelijkse updates wordt een Guest OS-versie normaal gesproken ongeveer 60 dagen na de release uitgeschakeld. Met deze activiteit zijn ten minste twee gast-besturingssysteemversies voor elk gezin beschikbaar voor gebruik.

### <a name="process-during-a-guest-os-family-retirement"></a>Proces tijdens een gastOS-gezinspensioen
Zodra de pensionering wordt aangekondigd, klanten hebben een 12 maanden "overgangsperiode" voordat de oudere familie officieel wordt verwijderd uit dienst. Deze overgangsperiode kan naar goeddunken van Microsoft worden verlengd. Updates worden geplaatst op de [Azure Guest OS Releases en SDK Compatibility Matrix.](cloud-services-guestos-update-matrix.md)

Een geleidelijk pensioenproces zal beginnen zes (6) maanden in de overgangsperiode. Gedurende deze tijd:

1. Microsoft zal klanten op de hoogte stellen van de pensionering.
2. De nieuwere versie van de Azure SDK biedt geen ondersteuning voor de gepensioneerde Gast-OS-familie.
3. Nieuwe implementaties en herschikkingen van Cloud Services zijn niet toegestaan in het gepensioneerde gezin

Microsoft zal doorgaan met de invoering van nieuwe Guest OS versie waarin de nieuwste MSRC updates tot de laatste dag van de overgangsperiode, bekend als de "vervaldatum". Op de vervaldatum worden cloudservices die nog steeds worden uitgevoerd, niet ondersteund onder de Azure SLA. Microsoft heeft de discretionaire bevoegdheid om deze services na die datum te upgraden, te verwijderen of te stoppen.

### <a name="process-during-a-guest-os-version-retirement"></a>Proces tijdens een gastOS-versie pensioen
Als klanten hun gastbesturingssysteem automatisch bijwerken, hoeven ze zich nooit zorgen te maken over het omgaan met gastbesturingssysteemversies. Ze zullen altijd met behulp van de nieuwste Guest OS versie.

Guest OS Versies worden elke maand uitgebracht. Door de snelheid van reguliere releases heeft elke versie een vaste levensduur.

Na 60 dagen in de levensduur is een versie "*uitgeschakeld*". 'Uitgeschakeld' betekent dat de versie uit de portal wordt verwijderd. De versie kan niet meer worden ingesteld vanuit het CSCFG-configuratiebestand. Bestaande implementaties blijven actief. Maar nieuwe implementaties en code- en configuratie-updates voor bestaande implementaties zijn niet toegestaan.

Enige tijd nadat het "uitgeschakeld" is, is de Gast-OS-versie "verloopt" en alle installaties die nog steeds worden uitgevoerd en die verlopen versie zijn blootgesteld aan beveiligings- en beveiligingsproblemen. Over het algemeen wordt expiratie in batches gedaan, zodat de periode van uitschakeling tot vervaldatum kan variëren.

Klanten die hun services configureren om het gastbesturingssysteem handmatig bij te werken, moeten ervoor zorgen dat hun services worden uitgevoerd op een ondersteund gastbesturingssysteem. Als een service is geconfigureerd om het gastbesturingssysteem automatisch bij te werken, zorgt het onderliggende platform voor naleving en wordt het geüupgradet naar het nieuwste gastbesturingssysteem.

Deze perioden kunnen naar eigen goeddunken van Microsoft langer worden gemaakt om de overgang van klanten te vergemakkelijken. Eventuele wijzigingen worden gecommuniceerd op de [Azure Guest OS Releases en SDK Compatibility Matrix.](cloud-services-guestos-update-matrix.md)

### <a name="notifications-during-retirement"></a>Meldingen tijdens pensionering
* **Gezinspensioen** <br>Microsoft gebruikt blogberichten en portalmeldingen. Klanten die nog steeds gebruik maken van een gepensioneerde Gast OS-familie, worden via directe communicatie (e-mail, portalberichten, telefoongesprek) op de hoogte gebracht van toegewezen servicebeheerders. Alle wijzigingen worden geplaatst in de [Azure Guest OS Releases en SDK Compatibility Matrix.](cloud-services-guestos-update-matrix.md)
* **Versie Pensioen** <br>Alle wijzigingen en de datums die worden uitgevoerd, worden geboekt op de [Azure Guest OS Releases en SDK Compatibility Matrix,](cloud-services-guestos-update-matrix.md)inclusief release, uitgeschakeld en vervaldatum. Servicesbeheerders ontvangen e-mails als ze implementaties hebben die worden uitgevoerd op een uitgeschakelde gast-os-versie of -familie. De timing van deze e-mails kan variëren. Over het algemeen zijn ze ten minste een maand voor de uitschakeling, hoewel deze timing is niet een officiële SLA.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen
**Hoe kan ik de gevolgen van migratie verzachten?**

We raden u aan de nieuwste Gast-OS-familie te gebruiken voor het ontwerpen van uw Cloud Services.

1. Begin vroeg met het plannen van uw migratie naar een nieuwer gezin.
2. Stel tijdelijke testimplementaties in om uw Cloud Service te testen die op de nieuwe familie wordt uitgevoerd.
3. Stel uw Gast-OS-versie in **op Automatisch** (osVersion=* in het [.cscfg-bestand),](cloud-services-model-and-package.md#cscfg) zodat de migratie naar nieuwe Gast-OS-versies automatisch plaatsvindt.

**Wat gebeurt er als mijn webapplicatie een diepere integratie met het besturingssysteem vereist?**

Als de architectuur van uw webtoepassing afhankelijk is van onderliggende functies van het besturingssysteem, gebruikt u platformondersteunde mogelijkheden zoals [opstarttaken](cloud-services-startup-tasks.md) of andere uitbreidbaarheidsmechanismen. U ook [Azure Virtual Machines](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS – Infrastructure as a Service) gebruiken, waar u verantwoordelijk bent voor het onderhoud van het onderliggende besturingssysteem.

## <a name="next-steps"></a>Volgende stappen
Bekijk de nieuwste [Gast OS releases](cloud-services-guestos-update-matrix.md).
