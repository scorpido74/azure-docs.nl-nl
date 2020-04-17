---
title: Regionaal redundantie- en failoverherstel met Azure HPC-cache
description: Technieken om failovermogelijkheden te bieden voor noodherstel met Azure HPC-cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 21074ae6bc4959da031bc7065cd7d0639ec2a14f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537267"
---
# <a name="use-multiple-caches-for-regional-failover-recovery"></a>Meerdere caches gebruiken voor regionaal failoverherstel

Elke Azure HPC-cache-instantie wordt uitgevoerd binnen een bepaald abonnement en in één regio. Dit betekent dat uw cacheworkflow mogelijk kan worden verstoord als de regio een volledige storing heeft.

In dit artikel wordt een strategie beschreven om het risico op werkonderbreking te verminderen door een tweede regio te gebruiken voor cachefailover.

De sleutel maakt gebruik van back-endopslag die toegankelijk is vanuit meerdere regio's. Deze opslag kan een on-premises NAS-systeem zijn met de juiste DNS-ondersteuning of Azure Blob-opslag die zich in een andere regio dan de cache bevindt.

Naarmate uw werkstroom in uw primaire regio verloopt, worden gegevens opgeslagen in de langetermijnopslag buiten de regio. Als het cachegebied niet meer beschikbaar is, u een dubbele Azure HPC-cache-instantie maken in een secundair e-gebied, verbinding maken met dezelfde opslag en het werk hervatten vanuit de nieuwe cache.

## <a name="planning-for-regional-failover"></a>Planning voor regionale failover

Voer de volgende stappen uit om een cache in te stellen die is voorbereid op mogelijke failover:

1. Zorg ervoor dat uw back-endopslag toegankelijk is in een tweede regio.
1. Wanneer u van plan bent de primaire cache-instantie te maken, moet u zich ook voorbereiden op het repliceren van dit installatieproces in het tweede gebied. Neem deze objecten op:

   1. Virtuele netwerk- en subnetstructuur
   1. Cachecapaciteit
   1. Opslagdoelgegevens, namen en naamruimtepaden
   1. Details over clientmachines, als ze zich in dezelfde regio bevinden als de cache
   1. Opdracht monteren voor gebruik door cacheclients

   > [!NOTE]
   > Azure HPC-cache kan programmatisch worden gemaakt, hetzij via een [Azure Resource Manager-sjabloon,](../azure-resource-manager/templates/overview.md) hetzij door rechtstreeks toegang te krijgen tot de API. Neem contact op met het Azure HPC-cacheteam voor meer informatie.

## <a name="failover-example"></a>Voorbeeld van failover

Stel u bijvoorbeeld voor dat u uw Azure HPC-cache wilt vinden in de Oost-Amerikaanse regio van Azure. Het heeft toegang tot gegevens die zijn opgeslagen in uw on-premises datacenter.

U een cache in de regio West US 2 gebruiken als een failoverback.

Wanneer u de cache in Oost-VS maakt, bereidt u een tweede cache voor voor implementatie in West US 2. U scripting of sjablonen gebruiken om deze voorbereiding te automatiseren.

In het geval van een regio-brede storing in Oost-VS, maak de cache die u hebt voorbereid in de regio West US 2.

Nadat de cache is gemaakt, voegt u opslagdoelen toe die naar dezelfde on-premises gegevensopslag wijzen en gebruikt u dezelfde geaggregeerde naamruimtepaden als de opslagdoelen van de oude cache.

Als de oorspronkelijke clients worden beïnvloed, maakt u nieuwe clients in de regio West US 2 voor gebruik met de nieuwe cache.

Alle clients moeten de nieuwe cache monteren, zelfs als de clients niet zijn getroffen door de regiostoring. De nieuwe cache heeft verschillende mount adressen van de oude.

## <a name="learn-more"></a>Meer informatie

De handleiding voor azure-toepassingsarchitectuur bevat meer informatie over het [herstellen van een serviceonderbreking in de hele regio.](<https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region>)
