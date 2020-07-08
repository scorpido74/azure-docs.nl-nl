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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77597787"
---
Om de gegevens in uw Azure-bestands shares te beschermen tegen gegevens verlies of-beschadiging, worden in alle Azure-bestands shares meerdere exemplaren van elk bestand opgeslagen wanneer ze zijn geschreven. Afhankelijk van de vereisten van uw workload, kunt u extra mate van redundantie selecteren. Azure Files ondersteunt momenteel de volgende opties voor gegevens redundantie:

- **Lokaal redundant**: lokaal redundante opslag, ook wel LRS genoemd, houdt in dat elk bestand drie keer in een Azure Storage-cluster wordt opgeslagen. Dit beveiligt het verlies van gegevens als gevolg van hardwarestoringen, zoals een beschadigd schijf station.
- **Zone redundant**: zone redundante opslag, vaak aangeduid als ZRS, houdt in dat elk bestand drie keer per drie afzonderlijke Azure Storage-clusters wordt opgeslagen. De drie afzonderlijke Azure Storage-clusters slaan het bestand drie keer op, net als met lokaal redundante opslag, en zijn fysiek geïsoleerd in verschillende Azure- *beschikbaarheids zones*. Beschikbaarheids zones zijn unieke fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Een schrijf bewerking naar opslag wordt pas geaccepteerd als deze is geschreven naar de opslag clusters in alle drie de beschikbaarheids zones. 
- **Geografisch redundante**opslag, ook wel GRS genoemd, is net als lokaal redundante opslag, omdat een bestand drie keer binnen een Azure Storage-cluster in de primaire regio wordt opgeslagen. Alle schrijf bewerkingen worden vervolgens asynchroon gerepliceerd naar een secundaire regio die door micro soft is gedefinieerd. Geografisch redundante opslag biedt zes kopieën van uw gegevens verspreiding tussen twee Azure-regio's. In het geval van een ernstige ramp, zoals het permanent verlies van een Azure-regio als gevolg van een natuur ramp of een andere vergelijk bare gebeurtenis, zal micro soft een failover uitvoeren, zodat de secundaire actief wordt de primaire, zodat alle bewerkingen worden uitgevoerd. Omdat de replicatie tussen de primaire en secundaire regio's asynchroon is, gaan de gegevens die nog niet zijn gerepliceerd naar de secundaire regio verloren als er een grote nood geval is. U kunt ook een hand matige failover uitvoeren van een geografisch redundant opslag account.
- **Geo-zone redundant**: geozone redundante opslag, ook wel GZRS genoemd, is vergelijkbaar met zone-redundante opslag, omdat een bestand negen keer per 3 afzonderlijke opslag clusters in de primaire regio wordt opgeslagen. Alle schrijf bewerkingen worden vervolgens asynchroon gerepliceerd naar een secundaire regio die door micro soft is gedefinieerd. Het failoverproces voor geo-zone-redundante opslag werkt op dezelfde manier als voor geografisch redundante opslag.

Standaard Azure-bestands shares ondersteunen alle vier redundantie typen, terwijl Premium Azure-bestands shares alleen ondersteuning bieden voor lokaal redundante en zone redundante opslag.

GPv2-opslag accounts (General version 2) bieden twee extra redundantie opties die niet worden ondersteund door Azure Files: Lees toegankelijke geo-redundante opslag, ook wel bekend als RA-GRS, en lees toegankelijke geo-zone-redundante opslag, ook wel bekend als RA-GZRS. U kunt Azure-bestands shares inrichten in opslag accounts waarvoor deze opties zijn ingesteld, maar Azure Files geen ondersteuning biedt voor het lezen van de secundaire regio. Azure-bestands shares die zijn geïmplementeerd in een opslag account met een geografische of geografische zone met lees toegang, worden respectievelijk gefactureerd als geo-redundante of geo-zone-redundante opslag.