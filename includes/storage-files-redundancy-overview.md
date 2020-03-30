---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 87457bb103f49be4ca3e7bf9f463c5bf63f3a119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597787"
---
Om de gegevens in uw Azure-bestandsshares te beschermen tegen gegevensverlies of beschadiging, slaan alle Azure-bestandsshares meerdere kopieën van elk bestand op zoals ze zijn geschreven. Afhankelijk van de vereisten van uw werkbelasting u extra redundantieniveaus selecteren. Azure Files ondersteunt momenteel de volgende opties voor gegevensredundantie:

- **Lokaal redundant:** Lokaal redundante opslag, vaak lrs genoemd, betekent dat elk bestand drie keer wordt opgeslagen in een Azure-opslagcluster. Dit beschermt tegen verlies van gegevens als gevolg van hardwarefouten, zoals een slechte schijf.
- **Zone redundant**: Zone redundante opslag, vaak aangeduid als ZRS, betekent dat elk bestand drie keer wordt opgeslagen in drie verschillende Azure-opslagclusters. De drie afzonderlijke Azure-opslagclusters slaan het bestand elk drie keer op, net als bij lokaal redundante opslag, en zijn fysiek geïsoleerd in verschillende *Azure-beschikbaarheidszones.* Beschikbaarheidszones zijn unieke fysieke locaties binnen een Azure-gebied. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Een schrijven naar opslag wordt pas geaccepteerd als deze naar de opslagclusters in alle drie de beschikbaarheidszones is geschreven. 
- **Geo-redundant:** Georedundant-opslag, vaak GRS genoemd, is net als lokaal redundante opslag, omdat een bestand drie keer wordt opgeslagen in een Azure-opslagcluster in het primaire gebied. Alle schrijfbewerkingen worden vervolgens asynchroon gerepliceerd naar een door Microsoft gedefinieerde secundaire regio. Georedundante opslag biedt 6 kopieën van uw gegevensverspreid over twee Azure-regio's. In het geval van een grote ramp, zoals het permanente verlies van een Azure-regio als gevolg van een natuurramp of een andere soortgelijke gebeurtenis, zal Microsoft een failover uitvoeren, zodat de secundaire in feite de primaire wordt, waarbij alle bewerkingen worden weergegeven. Aangezien de replicatie tussen de primaire en secundaire regio's asynchroon zijn, gaan in het geval van een grote ramp gegevens die nog niet zijn gerepliceerd naar het secundaire gebied verloren. U ook een handmatige failover van een geo-redundante opslagaccount uitvoeren.
- **Geo-zone redundant**: Geo-zone redundante opslag, vaak aangeduid als GZRS, is als zone redundante opslag, in die zin dat een bestand wordt opgeslagen negen keer in 3 verschillende opslag clusters in de primaire regio. Alle schrijfbewerkingen worden vervolgens asynchroon gerepliceerd naar een door Microsoft gedefinieerde secundaire regio. Het failoverproces voor geo-zoneredundante opslag werkt hetzelfde als voor georedundante opslag.

Standaard Azure-bestandsshares ondersteunen alle vier redundantietypen, terwijl premium Azure-bestandsshares alleen lokaal redundante en zoneredundante opslag ondersteunen.

GPv2-opslagaccounts (Version 2) voor algemeen gebruik bieden twee extra redundantieopties die niet worden ondersteund door Azure Files: lees toegankelijke georedundante opslag, vaak RA-GRS genoemd, en lees toegankelijke geozoneredundante opslag, vaak ra-gzrs. U Azure-bestandsshares inopslaginopslaginopslag met deze opties ingesteld, maar Azure Files biedt geen ondersteuning voor lezen vanuit de secundaire regio. Azure-bestandsshares die worden geïmplementeerd in lees-toegankelijke geo- of geozoneredundante opslagaccounts, worden gefactureerd als respectievelijk georedundante of geozoneredundante opslag.