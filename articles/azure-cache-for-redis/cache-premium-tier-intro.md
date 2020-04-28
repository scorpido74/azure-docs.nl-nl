---
title: Inleiding tot de Azure-cache voor de Premium-laag van redis
description: Meer informatie over het maken en beheren van redis-persistentie, redis clustering en VNET-ondersteuning voor uw Azure-cache voor de Premium-laag voor redis-instanties
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: aadcc13d2397f10ea40f06d1259c86b9a179c38b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74121665"
---
# <a name="introduction-to-the-azure-cache-for-redis-premium-tier"></a>Inleiding tot de Azure-cache voor de Premium-laag van redis
Azure cache voor redis is een gedistribueerde, beheerde cache waarmee u zeer schaal bare en snel reagerende toepassingen kunt bouwen door zeer snelle toegang tot uw gegevens te bieden. 

De nieuwe Premium-laag is een bedrijfs klare laag, die alle functies van de Standard-laag bevat, zoals betere prestaties, grotere workloads, herstel na nood gevallen, importeren/exporteren en verbeterde beveiliging. Blijf lezen voor meer informatie over de aanvullende functies van de laag Premium-cache.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Betere prestaties in vergelijking met de laag Standard of Basic
**Betere prestaties ten opzichte van de laag standaard of Basic.** Caches in de Premium-laag worden geïmplementeerd op hardware met snellere processors en levert betere prestaties in vergelijking met de basis-of standaard-laag. Premium-laag caches hebben een hogere door Voer en een lagere latentie. 

**De door Voer voor dezelfde grootte cache is hoger in de Premium-fase, vergeleken met de Standard-laag.** Bijvoorbeeld: de door Voer van een Premium-cache van 53 GB is 250.000 aanvragen per seconde in vergelijking met 150K voor C6 (standaard).

Zie voor meer informatie over de grootte, de door Voer en de band breedte met Premium-caches [Veelgestelde vragen over Azure cache voor redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Redis-gegevenspersistentie
Met de Premium-laag kunt u de cache gegevens in een Azure Storage-account behouden. In een Basic-of Standard-cache worden alle gegevens alleen in het geheugen opgeslagen. In het geval van onderliggende infrastructuur problemen kan er sprake zijn van mogelijk gegevens verlies. We raden u aan de functie voor redis-gegevens persistentie in de Premium-laag te gebruiken om de tolerantie voor gegevens verlies te verg Roten. Azure-cache voor redis biedt de opties RDB en AOF (binnenkort beschikbaar) in [redis-persistentie](https://redis.io/topics/persistence). 

Zie [persistentie configureren voor een Premium Azure-cache voor redis](cache-how-to-premium-persistence.md)voor instructies over het configureren van persistentie.

## <a name="redis-cluster"></a>Redis-cluster
Als u caches wilt maken die groter zijn dan 53 GB of als u gegevens wilt Shard op meerdere redis-knoop punten, kunt u redis clustering gebruiken die beschikbaar is in de laag Premium. Elk knoop punt bestaat uit een primair/replica-cache paar dat wordt beheerd door Azure voor hoge Beschik baarheid. 

**Redis Clustering biedt maximale schaal en door voer.** De door Voer wordt lineair verhoogd naarmate u het aantal Shards (knoop punten) in het cluster verhoogt. Bijvoorbeeld Als u een P4-cluster van 10 Shards maakt, is de beschik bare door Voer 250.000 * 10 = 2.500.000 aanvragen per seconde. Raadpleeg de [Veelgestelde vragen over Azure cache for redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) voor meer informatie over de grootte, de door Voer en de band breedte met Premium-caches.

Zie [clustering configureren voor een Premium Azure-cache voor redis](cache-how-to-premium-clustering.md)om aan de slag te gaan met clusteren.

## <a name="enhanced-security-and-isolation"></a>Verbeterde beveiliging en isolatie
Caches die zijn gemaakt in de Basic-of Standard-laag, zijn toegankelijk via het open bare Internet. Toegang tot de cache wordt beperkt op basis van de toegangs sleutel. Met de Premium-laag kunt u er ook voor zorgen dat alleen clients binnen een opgegeven netwerk toegang hebben tot de cache. U kunt Azure-cache implementeren voor redis in een [Azure-Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/). U kunt alle functies van VNet, zoals subnetten, toegangscontrolebeleid en andere functies, gebruiken om de toegang tot Redis verder te beperken.

Zie [Virtual Network-ondersteuning configureren voor een Premium Azure-cache voor redis](cache-how-to-premium-vnet.md)voor meer informatie.

## <a name="importexport"></a>Import/Export
Import/export is een Azure cache voor redis-gegevens beheer bewerking, waarmee u gegevens kunt importeren in azure cache voor redis of gegevens uit Azure-cache exporteren voor redis door het importeren en exporteren van een Azure-cache voor de redis-data base (RDB) van een Premium-cache naar een pagina-Blob in een Azure Storage-account. Zo kunt u migreren tussen verschillende Azure-cache voor redis-instanties of de cache vullen met gegevens voor gebruik.

Importeren kan worden gebruikt om redis compatibele RDB-bestanden te halen van elke redis-server die in een wille keurige Cloud of omgeving wordt uitgevoerd, waaronder redis die wordt uitgevoerd op Linux, Windows of een andere Cloud provider, zoals Amazon Web Services en anderen. Het importeren van gegevens is een eenvoudige manier om een cache met vooraf gevulde gegevens te maken. Tijdens het import proces laadt Azure cache voor redis de RDB-bestanden vanuit Azure Storage in het geheugen en voegt vervolgens de sleutels in de cache in.

Met exporteren kunt u de gegevens die zijn opgeslagen in azure cache exporteren voor redis naar redis-compatibele RDB-bestanden. U kunt deze functie gebruiken om gegevens van één Azure-cache te verplaatsen voor een redis-exemplaar naar een ander of een andere redis-server. Tijdens het export proces wordt er een tijdelijk bestand gemaakt op de virtuele machine die als host fungeert voor de Azure-cache voor redis server-exemplaar, en wordt het bestand geüpload naar het aangewezen opslag account. Wanneer de export bewerking is voltooid met de status geslaagd of mislukt, wordt het tijdelijke bestand verwijderd.

Zie [gegevens importeren in en exporteren uit Azure cache voor redis](cache-how-to-import-export-data.md)voor meer informatie.

## <a name="reboot"></a>Opnieuw opstarten
Met de Premium-laag kunt u een of meer knoop punten van uw cache op aanvraag opnieuw opstarten. Zo kunt u uw toepassing testen op tolerantie in het geval van een storing. U kunt de volgende knoop punten opnieuw opstarten.

* Hoofd knooppunt van uw cache
* Secundair knoop punt van uw cache
* Zowel primaire als secundaire knoop punten van uw cache
* Wanneer u een Premium-cache gebruikt met clustering, kunt u de primaire, secundaire of beide knoop punten voor afzonderlijke Shards in de cache opnieuw opstarten.

Zie [Veelgestelde vragen over](cache-administration.md#reboot-faq) [opnieuw opstarten](cache-administration.md#reboot) en opnieuw opstarten voor meer informatie.

>[!NOTE]
>De functionaliteit voor opnieuw opstarten is nu ingeschakeld voor alle Azure-cache voor redis-lagen.
>
>

## <a name="schedule-updates"></a>Updates plannen
Met de functie geplande updates kunt u een onderhouds venster voor uw cache aanwijzen. Wanneer het onderhouds venster is opgegeven, worden er redis-server updates gemaakt tijdens dit venster. Als u een onderhouds venster wilt aanwijzen, selecteert u de gewenste dagen en geeft u het onderhouds venster voor elke dag op. Houd er rekening mee dat de tijd van het onderhouds venster in UTC is. 

Zie [updates plannen](cache-administration.md#schedule-updates) en [Veelgestelde vragen over updates plannen](cache-administration.md#schedule-updates-faq)voor meer informatie.

> [!NOTE]
> Er worden alleen redis-server updates gemaakt tijdens het geplande onderhouds venster. Het onderhouds venster is niet van toepassing op Azure-updates of updates voor het VM-besturings systeem.
> 
> 

## <a name="geo-replication"></a>Geo-replicatie

**Geo-replicatie** biedt een mechanisme voor het koppelen van twee Premium-laag Azure-cache voor redis-exemplaren. Eén cache wordt aangeduid als de primaire gekoppelde cache en de andere als de secundaire gekoppelde cache. De secundaire gekoppelde cache wordt alleen-lezen en de gegevens die naar de primaire cache worden geschreven, worden gerepliceerd naar de secundaire gekoppelde cache. Deze functionaliteit kan worden gebruikt om een cache te repliceren tussen Azure-regio's.

Zie [geo-replicatie voor Azure cache configureren voor redis](cache-how-to-geo-replication.md)voor meer informatie.


## <a name="to-scale-to-the-premium-tier"></a>Schalen naar de Premium-laag
Als u wilt schalen naar de Premium-laag, kiest u gewoon een van de Premium-lagen in de Blade **prijs categorie wijzigen** . U kunt uw cache ook schalen naar de Premium-laag met behulp van Power shell en CLI. Zie [Azure-cache schalen voor redis](cache-how-to-scale.md) en [hoe u een schaal bewerking automatiseert](cache-how-to-scale.md#how-to-automate-a-scaling-operation)voor stapsgewijze instructies.

## <a name="next-steps"></a>Volgende stappen
Maak een cache en verken de nieuwe functies van de Premium-laag.

* [Persistentie configureren voor een Premium Azure-cache voor redis](cache-how-to-premium-persistence.md)
* [Virtual Network ondersteuning configureren voor een Premium Azure-cache voor redis](cache-how-to-premium-vnet.md)
* [Clustering configureren voor een Premium Azure-cache voor redis](cache-how-to-premium-clustering.md)
* [Gegevens importeren in en exporteren uit Azure cache voor redis](cache-how-to-import-export-data.md)
* [Azure-cache beheren voor redis](cache-administration.md)

