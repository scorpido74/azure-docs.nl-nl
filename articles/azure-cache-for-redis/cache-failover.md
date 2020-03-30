---
title: Failover en patching - Redis Cache
description: Meer informatie over failover, patchen en het updateproces voor Azure Cache voor Redis.
author: asasine
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: adsasine
ms.openlocfilehash: 6ff33bd594181aabc4fd7d55ce33f780a0d06086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74122201"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Failover en patchen voor Azure Cache voor Redis

Om veerkrachtige en succesvolle clienttoepassingen te bouwen, is het van cruciaal belang om failover te begrijpen in de context van de Azure Cache for Redis-service. Een failover kan deel uitmaken van geplande beheerbewerkingen of kan worden veroorzaakt door ongeplande hardware- of netwerkfouten. Een veelvoorkomend gebruik van cachefailover komt wanneer de beheerservice de Azure-cache voor Redis-binaire bestanden patches. In dit artikel wordt ingegaan op wat een failover is, hoe deze optreedt tijdens het patchen en hoe u een veerkrachtige clienttoepassing bouwen.

## <a name="what-is-a-failover"></a>Wat is een failover?

Laten we beginnen met een overzicht van failover voor Azure Cache voor Redis.

### <a name="a-quick-summary-of-cache-architecture"></a>Een korte samenvatting van cachearchitectuur

Een cache is opgebouwd uit meerdere virtuele machines met afzonderlijke, privé IP-adressen. Elke virtuele machine, ook wel een knooppunt genoemd, is verbonden met een gedeelde load balancer met één virtueel IP-adres. Elk knooppunt voert het Redis-serverproces uit en is toegankelijk via de hostnaam en de Redis-poorten. Elk knooppunt wordt beschouwd als een master of een replica knooppunt. Wanneer een clienttoepassing verbinding maakt met een cache, gaat het verkeer via deze load balancer en wordt deze automatisch doorgestuurd naar het hoofdknooppunt.

In een Basic-cache is het enkele knooppunt altijd een master. In een standaard- of Premium-cache zijn er twee knooppunten: de ene is gekozen als het stramien en de andere is de replica. Omdat standaard- en Premium-caches meerdere knooppunten hebben, is één knooppunt mogelijk niet beschikbaar terwijl het andere knooppunt aanvragen blijft verwerken. Geclusterde caches zijn gemaakt van vele scherven, elk met verschillende master- en replicaknooppunten. Een scherf kan naar beneden, terwijl de anderen beschikbaar blijven.

> [!NOTE]
> Een Basic-cache heeft niet meerdere knooppunten en biedt geen service-level overeenkomst (SLA) voor de beschikbaarheid ervan. Basiscaches worden alleen aanbevolen voor ontwikkelings- en testdoeleinden. Gebruik een Standaard- of Premium-cache voor een implementatie met meerdere nodes om de beschikbaarheid te vergroten.

### <a name="explanation-of-a-failover"></a>Uitleg van een failover

Een failover treedt op wanneer een replicaknooppunt zichzelf promoot om een hoofdknooppunt te worden en het oude hoofdknooppunt bestaande verbindingen sluit. Nadat het hoofdknooppunt weer omhoog komt, merkt het de verandering in rollen op en degradeert zichzelf om een replica te worden. Het maakt vervolgens verbinding met de nieuwe master en synchroniseert gegevens. Een failover kan worden gepland of ongepland.

Een *geplande failover* vindt plaats tijdens systeemupdates, zoals Redis-patching of OS-upgrades, en beheerbewerkingen, zoals schalen en opnieuw opstarten. Omdat de knooppunten van tevoren op de hoogte worden gesteld van de update, kunnen ze gezamenlijk rollen wisselen en de load balancer van de wijziging snel bijwerken. Een geplande failover eindigt meestal in minder dan 1 seconde.

Er kan *een ongeplande failover* optreden als gevolg van hardwarefouten, netwerkstoringen of andere onverwachte storingen aan het hoofdknooppunt. Het replicaknooppunt bevordert zichzelf om te beheersen, maar het proces duurt langer. Een replicaknooppunt moet eerst detecteren dat het hoofdknooppunt niet beschikbaar is voordat het failoverproces kan worden gestart. Het replicaknooppunt moet ook controleren of deze ongeplande fout niet van voorbijgaande aard of lokaal is, om een onnodige failover te voorkomen. Deze vertraging in detectie betekent dat een ongeplande failover meestal binnen 10 tot 15 seconden eindigt.

## <a name="how-does-patching-occur"></a>Hoe treedt patchen voor?

De Azure Cache for Redis-service werkt uw cache regelmatig bij met de nieuwste platformfuncties en -oplossingen. Als u een cache wilt patchen, volgt de service de volgende stappen:

1. De beheerservice selecteert één knooppunt dat moet worden gepatcht.
1. Als het geselecteerde knooppunt een hoofdknooppunt is, promoot het bijbehorende replicaknooppunt zichzelf coöperatief. Deze promotie wordt beschouwd als een geplande failover.
1. Het geselecteerde knooppunt wordt opnieuw opgestart om de nieuwe wijzigingen te nemen en wordt weer opgenomen als een replicaknooppunt.
1. Het replicaknooppunt maakt verbinding met het hoofdknooppunt en synchroniseert gegevens.
1. Wanneer de gegevenssynchronisatie is voltooid, wordt het patchproces herhaald voor de resterende knooppunten.

Omdat patchen een geplande failover is, promoot het replicaknooppunt zichzelf snel om een master te worden en begint het onderhoud van aanvragen en nieuwe verbindingen. Basiscaches hebben geen replicaknooppunt en zijn niet beschikbaar totdat de update is voltooid. Elke scherf van een geclusterde cache wordt afzonderlijk gepatcht en sluit geen verbindingen met een andere scherf.

> [!IMPORTANT]
> Knooppunten worden één voor één gepatcht om gegevensverlies te voorkomen. Basiscaches hebben gegevensverlies. Geclusterde caches worden één scherf tegelijk gepatcht.

Meerdere caches in dezelfde resourcegroep en regio worden ook één voor één gepatcht.  Caches die zich in verschillende resourcegroepen of verschillende regio's bevinden, kunnen tegelijkertijd worden gepatcht.

Omdat volledige gegevenssynchronisatie plaatsvindt voordat het proces zich herhaalt, is het onwaarschijnlijk dat gegevensverlies optreedt wanneer u een Standaard- of Premium-cache gebruikt. U zich verder beschermen tegen gegevensverlies door gegevens [te exporteren](cache-how-to-import-export-data.md#export) en [persistentie](cache-how-to-premium-persistence.md)mogelijk te maken.

## <a name="additional-cache-load"></a>Extra cachebelasting

Wanneer er een failover optreedt, moeten de Standard- en Premium-caches gegevens van het ene knooppunt naar het andere repliceren. Deze replicatie zorgt voor enige toename van de belasting in zowel servergeheugen als CPU. Als de cache-instantie al zwaar is geladen, kunnen clienttoepassingen een verhoogde latentie ervaren. In extreme gevallen kunnen clienttoepassingen uitzonderingen op een time-out ontvangen. Configureer [de](cache-configure.md#memory-policies) `maxmemory-reserved` instelling van de cache om de impact van deze extra belasting te beperken.

## <a name="how-does-a-failover-affect-my-client-application"></a>Welke invloed heeft een failover op mijn clienttoepassing?

Het aantal fouten dat door de clienttoepassing wordt gezien, is afhankelijk van het aantal bewerkingen dat op die verbinding in behandeling was op het moment van de failover. Elke verbinding die wordt doorgestuurd via het knooppunt dat de verbindingen heeft gesloten, ziet fouten. Veel clientbibliotheken kunnen verschillende soorten fouten weggooien wanneer verbindingen worden verbroken, waaronder time-outuitzonderingen, verbindingsuitzonderingen of socketuitzonderingen. Het aantal en het type uitzonderingen zijn afhankelijk van waar in het codepad de aanvraag zich bevindt wanneer de cache de verbindingen sluit. Een bewerking die bijvoorbeeld een aanvraag verzendt, maar geen antwoord heeft ontvangen wanneer de failover optreedt, kan een time-outuitzondering krijgen. Nieuwe aanvragen voor het gesloten verbindingsobject ontvangen verbindingsuitzonderingen totdat de herverbinding is voltooid.

De meeste clientbibliotheken proberen opnieuw verbinding te maken met de cache als ze zijn geconfigureerd om dit te doen. Onvoorziene bugs kunnen de bibliotheekobjecten echter af en toe in een onherstelbare status plaatsen. Als fouten langer dan een vooraf geconfigureerde tijd blijven bestaan, moet het verbindingsobject opnieuw worden gemaakt. In Microsoft.NET en andere objectgeoriënteerde talen kan het opnieuw maken van de verbinding zonder de toepassing opnieuw op te starten worden bereikt met behulp van [een Lazy\<T-patroon.\> ](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#reconnecting-with-lazyt-pattern)

### <a name="how-do-i-make-my-application-resilient"></a>Hoe maak ik mijn toepassing veerkrachtig?

Omdat u failovers niet volledig voorkomen, schrijft u uw clienttoepassingen voor tolerantie voor verbindingsonderbrekingen en mislukte aanvragen. Hoewel de meeste clientbibliotheken automatisch opnieuw verbinding maken met het eindpunt van de cache, proberen weinigen van hen mislukte aanvragen opnieuw te proberen. Afhankelijk van het toepassingsscenario is het mogelijk om logica opnieuw proberen met back-off te gebruiken.

Als u de tolerantie van een clienttoepassing wilt testen, gebruikt u een [reboot](cache-administration.md#reboot) als handmatige trigger voor verbindingsonderbrekingen. Daarnaast raden we je aan [om updates in te plannen](cache-administration.md#schedule-updates) voor een cache. Vertel de beheerservice om Redis runtime-patches toe te passen tijdens opgegeven wekelijkse vensters. Deze vensters zijn meestal perioden waarin clienttoepassingsverkeer laag is, om mogelijke incidenten te voorkomen.

### <a name="client-network-configuration-changes"></a>Wijzigingen in clientnetwerkconfiguratie

Bepaalde wijzigingen in de netwerkconfiguratie aan de clientzijde kunnen fouten veroorzaken zonder verbinding. Dergelijke wijzigingen kunnen bestaan uit:

- Het virtuele IP-adres van een clienttoepassing verwisselen tussen faserings- en productiesleuven.
- Schalen van de grootte of het aantal exemplaren van uw toepassing.

Dergelijke wijzigingen kunnen leiden tot een verbindingsprobleem dat minder dan een minuut duurt. Uw clienttoepassing verliest waarschijnlijk de verbinding met andere externe netwerkbronnen naast de Azure Cache for Redis-service.

## <a name="next-steps"></a>Volgende stappen

- [Plan updates](cache-administration.md#schedule-updates) voor uw cache.
- Test toepassingstolerantie met behulp van een [reboot.](cache-administration.md#reboot)
- Geheugenreserveringen en -beleid [configureren.](cache-configure.md#memory-policies)
