---
title: Inleiding tot de azure-cache voor de laag Redis Premium
description: Meer informatie over het maken en beheren van Redis Persistentie, Redis-clustering en VNET-ondersteuning voor uw Azure-cache voor Azure-cache voor De Premium-laag voor Redis-exemplaren
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: aadcc13d2397f10ea40f06d1259c86b9a179c38b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74121665"
---
# <a name="introduction-to-the-azure-cache-for-redis-premium-tier"></a>Inleiding tot de azure-cache voor de laag Redis Premium
Azure Cache for Redis is een gedistribueerde, beheerde cache die u helpt bij het bouwen van zeer schaalbare en responsieve toepassingen door supersnelle toegang tot uw gegevens te bieden. 

De nieuwe Premium-tier is een Enterprise-ready-laag, die alle standaardfuncties en meer bevat, zoals betere prestaties, grotere workloads, noodherstel, import/export en verbeterde beveiliging. Lees verder voor meer informatie over de extra functies van de Premium-cachelaag.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Betere prestaties in vergelijking met de standaard- of basislaag
**Betere prestaties boven de standaard- of basislaag.** Caches in de Premium-laag worden geïmplementeerd op hardware die snellere processors heeft en betere prestaties biedt in vergelijking met de Basic- of Standard-laag. Premium tier Caches hebben een hogere doorvoer en lagere latencies. 

**De doorvoer voor de cache van dezelfde grootte is hoger in Premium in vergelijking met de standaardlaag.** De doorvoer van een 53 GB P4-cache (Premium) is bijvoorbeeld 250K aanvragen per seconde in vergelijking met 150K voor C6 (Standard).

Zie Veelgestelde vragen over [Azure Cache voor Redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) voor meer informatie over grootte, doorvoer en bandbreedte met premium caches

## <a name="redis-data-persistence"></a>Redis-gegevenspersistentie
Met de Premium-laag u de cachegegevens in een Azure Storage-account blijven gebruiken. In een Basic/Standard-cache worden alle gegevens alleen in het geheugen opgeslagen. In het geval van onderliggende infrastructuurproblemen kan er mogelijk gegevensverlies zijn. We raden u aan de functie voor gegevenspersistentie van Redis in de Premium-laag te gebruiken om de tolerantie tegen gegevensverlies te verhogen. Azure Cache for Redis biedt RDB- en AOF -opties (binnenkort beschikbaar) in [Redis-persistentie.](https://redis.io/topics/persistence) 

Zie [Persistentie configureren voor een Premium Azure-cache voor Redis voor](cache-how-to-premium-persistence.md)instructies voor het configureren van persistentie.

## <a name="redis-cluster"></a>Redis-cluster
Als u caches wilt maken die groter zijn dan 53 GB of gegevens wilt sharden over meerdere Redis-knooppunten, u Redis-clustering gebruiken die beschikbaar is in de Premium-laag. Elk knooppunt bestaat uit een primaire/replica-cachepaar dat door Azure wordt beheerd voor hoge beschikbaarheid. 

**Redis clustering geeft u maximale schaal en doorvoer.** De doorvoer neemt lineair toe naarmate u het aantal shards (knooppunten) in het cluster verhoogt. Bijvoorbeeld Als u een P4-cluster van 10 shards maakt, is de beschikbare doorvoer 250K *10 = 2,5 miljoen aanvragen per seconde. Zie de [veelgestelde vragen over Azure Cache for Redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) voor meer informatie over grootte, doorvoer en bandbreedte met premium caches.

Zie [Clustering configureren voor een Premium Azure-cache voor Redis](cache-how-to-premium-clustering.md)om aan de slag te gaan met clustering.

## <a name="enhanced-security-and-isolation"></a>Verbeterde beveiliging en isolatie
Caches die zijn gemaakt in de laag Basis of Standaard zijn toegankelijk op het openbare internet. De toegang tot de cache is beperkt op basis van de toegangssleutel. Met de Premium-laag u er verder voor zorgen dat alleen clients binnen een bepaald netwerk toegang hebben tot de cache. U Azure Cache voor Redis implementeren in een [Azure Virtual Network (VNet).](https://azure.microsoft.com/services/virtual-network/) U kunt alle functies van VNet, zoals subnetten, toegangscontrolebeleid en andere functies, gebruiken om de toegang tot Redis verder te beperken.

Zie [Ondersteuning voor virtueel netwerk configureren voor een Premium Azure-cache voor Redis voor](cache-how-to-premium-vnet.md)meer informatie.

## <a name="importexport"></a>Import/Export
Importeren/exporteren is een Azure-cache voor Redis-gegevensbeheerbewerking waarmee u gegevens importeren in Azure Cache voor Redis of gegevens uit Azure Cache voor Redis exporteren door een Azure Cache voor Redis Database (RDB)-momentopname te importeren van een premiumcache naar een paginablob in een Azure Storage-account. Hiermee u migreren tussen verschillende Azure-cache voor Redis-exemplaren of de cache vullen met gegevens voordat u deze gebruikt.

Import kan worden gebruikt om Redis-compatibele RDB-bestanden van elke Redis-server te brengen die in elke cloud of omgeving worden uitgevoerd, waaronder Redis die wordt uitgevoerd op Linux, Windows of een cloudprovider zoals Amazon Web Services en anderen. Het importeren van gegevens is een eenvoudige manier om een cache te maken met vooraf ingevulde gegevens. Tijdens het importproces laadt Azure Cache voor Redis de RDB-bestanden uit Azure-opslag in het geheugen en voegt u de sleutels vervolgens in de cache in.

Met exporteren u de gegevens die zijn opgeslagen in Azure Cache voor Redis exporteren naar RDB-bestanden die compatibel zijn met Redis. U deze functie gebruiken om gegevens van de ene Azure-cache voor Redis-instantie naar een andere of naar een andere Redis-server te verplaatsen. Tijdens het exportproces wordt een tijdelijk bestand gemaakt op de VM waarmee de Azure-cache voor de serverinstantie Van Redis wordt gehost en wordt het bestand geüpload naar het aangewezen opslagaccount. Wanneer de exportbewerking is voltooid met een status van succes of fout, wordt het tijdelijke bestand verwijderd.

Zie [Gegevens importeren in en exporteren vanuit Azure Cache voor Redis voor](cache-how-to-import-export-data.md)meer informatie.

## <a name="reboot"></a>Opnieuw opstarten
Met de premiumlaag u een of meer knooppunten van uw cache on-demand opnieuw opstarten. Hiermee u uw toepassing testen op tolerantie in het geval van een storing. U de volgende knooppunten opnieuw opstarten.

* Hoofdknooppunt van uw cache
* Secundair knooppunt van uw cache
* Zowel primaire als secundaire knooppunten van uw cache
* Wanneer u een premium cache met clustering gebruikt, u de primaire, secundaire of beide knooppunten opnieuw opstarten voor afzonderlijke shards in de cache

Zie [Veelgestelde](cache-administration.md#reboot-faq) [vragen opnieuw opstarten](cache-administration.md#reboot) en opnieuw opstarten voor meer informatie.

>[!NOTE]
>Reboot-functionaliteit is nu ingeschakeld voor alle Azure-cache voor Redis-lagen.
>
>

## <a name="schedule-updates"></a>Updates plannen
Met de functie geplande updates u een onderhoudsvenster voor uw cache aanwijzen. Wanneer het onderhoudsvenster is opgegeven, worden in dit venster updates van de Redis-server uitgevoerd. Als u een onderhoudsvenster wilt aanwijzen, selecteert u de gewenste dagen en geeft u het beginuur van het onderhoudsvenster voor elke dag op. Houd er rekening mee dat de tijd van het onderhoudsvenster zich in UTC bevindt. 

Zie [Veelgestelde](cache-administration.md#schedule-updates-faq) [vragen over updates plannen](cache-administration.md#schedule-updates) en updates plannen voor meer informatie.

> [!NOTE]
> Alleen Redis server updates worden uitgevoerd tijdens het geplande onderhoudsvenster. Het onderhoudsvenster is niet van toepassing op Azure-updates of -updates op het VM-besturingssysteem.
> 
> 

## <a name="geo-replication"></a>Geo-replicatie

**Geo-replicatie** biedt een mechanisme voor het koppelen van twee Azure-cache in de Premium-laag voor Redis-exemplaren. De ene cache wordt aangeduid als de primaire gekoppelde cache en de andere als de secundaire gekoppelde cache. De secundaire gekoppelde cache wordt alleen-lezen en gegevens die naar de primaire cache worden geschreven, worden gerepliceerd naar de secundaire gekoppelde cache. Deze functionaliteit kan worden gebruikt om een cache te repliceren in Azure-regio's.

Zie [Geo-replicatie configureren voor Azure-cache voor Redis voor](cache-how-to-geo-replication.md)meer informatie.


## <a name="to-scale-to-the-premium-tier"></a>Schalen naar de premiumlaag
Als u wilt schalen naar de premiumlaag, kiest u gewoon een van de premiumlagen in het **prijsmodel wijzigen.** U uw cache ook schalen naar de premiumlaag met PowerShell en CLI. Zie [Azure-cache schalen voor Redis](cache-how-to-scale.md) en [Een schalingbewerking automatiseren voor](cache-how-to-scale.md#how-to-automate-a-scaling-operation)stapsgewijze instructies.

## <a name="next-steps"></a>Volgende stappen
Maak een cache en ontdek de nieuwe premium tier functies.

* [Persistentie configureren voor een Premium Azure-cache voor Redis](cache-how-to-premium-persistence.md)
* [Ondersteuning voor virtueel netwerk configureren voor een Premium Azure-cache voor Redis](cache-how-to-premium-vnet.md)
* [Clustering configureren voor een Premium Azure-cache voor Redis](cache-how-to-premium-clustering.md)
* [Gegevens importeren in en exporteren vanuit Azure Cache voor Redis](cache-how-to-import-export-data.md)
* [Azure-cache beheren voor Redis](cache-administration.md)

