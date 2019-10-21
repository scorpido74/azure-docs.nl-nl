---
title: Failover en patching-Azure cache voor redis | Microsoft Docs
description: Meer informatie over failover, patching en het update proces voor Azure cache voor redis.
services: cache
author: asasine
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: adsasine
ms.openlocfilehash: 305511efe86d2b241ef5014d9c3f0501cfd3fbdc
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675900"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Failover en Patching voor Azure cache voor redis

Als u wilt weten wat een failover is in verband met de Azure-cache voor de redis-service, is het essentieel om robuuste en succes volle client toepassingen te bouwen. Een veelvoorkomende oorzaak van een cache-failover is afkomstig van de-beheer service waarmee de binaire bestanden van redis worden bijgewerkt. In dit artikel wordt beschreven wat een failover is, hoe deze optreden tijdens patches en hoe u een flexibele client toepassing bouwt.

## <a name="what-is-a-failover"></a>Wat is een failover?

### <a name="a-quick-summary-of-our-architecture"></a>Een snelle samen vatting van onze architectuur

Een cache bestaat uit meerdere virtuele machines met afzonderlijke privé Ip's. Elke virtuele machine, ook wel een knoop punt genoemd, is verbonden met een gedeelde load balancer met één virtueel IP-adres. Elk knoop punt voert het redis-server proces uit en is toegankelijk via de hostnaam en de redis-poorten. Elk knoop punt wordt beschouwd als een hoofd-of replica knooppunt. Wanneer een client toepassing verbinding maakt met een cache, loopt het verkeer via deze load balancer en wordt het automatisch doorgestuurd naar het hoofd knooppunt.

In een Basic-cache is het één knoop punt altijd een Master. In een Standard-of Premium-cache zijn er twee knoop punten waar een wordt gekozen voor de Master en de andere de replica. Omdat de standaard-en Premium-cache meerdere knoop punten hebben, is het mogelijk dat er een knoop punt niet beschikbaar is terwijl de andere aanvragen blijven verwerken. Geclusterde caches worden gemaakt van veel Shards, elk met afzonderlijke hoofd-en replica knooppunten. Een Shard is mogelijk niet actief terwijl de andere beschikbaar blijven.

> [!NOTE]
> Een Basic-cache heeft geen meerdere knoop punten en biedt geen SLA op Beschik baarheid. Basis caches worden alleen aanbevolen voor ontwikkelings-en test doeleinden. Gebruik een Standard-of Premium-cache voor een implementatie met meerdere knoop punten om de beschik baarheid te verg Roten.

### <a name="a-failover-explained"></a>Een failover wordt uitgelegd

Er treedt een failover op wanneer een replica-knoop punt wordt gepromoveerd tot een hoofd knooppunt, waarna bestaande verbindingen worden gesloten met het oude hoofd knooppunt. Nadat het hoofd knooppunt een back-up heeft gemaakt, ziet u de wijziging in rollen en degraderen zichzelf om een replica te worden. Vervolgens wordt er verbinding gemaakt met de nieuwe master en worden gegevens gesynchroniseerd. Een failover kan gepland of niet-gepland zijn.

Een geplande failover vindt plaats tijdens systeem updates, zoals redis patches of besturingssysteem upgrades en beheer bewerkingen, zoals schalen en opnieuw opstarten. Omdat de knoop punten een geavanceerde kennisgeving van de update krijgen, kunnen ze gezamenlijk rollen wisselen en snel de load balancer van de wijziging bijwerken. Een geplande failover moet in minder dan 1 seconde worden voltooid.

Een niet-geplande failover kan optreden als gevolg van hardwarestoringen, netwerk storingen of andere onverwachte storingen in het hoofd knooppunt. Het replica knooppunt bevordert zichzelf tot de hoofd server, maar het proces neemt langer tijd in beslag. Een replica knooppunt moet eerst het hoofd knooppunt detecteren, maar dit is niet beschikbaar voordat het failover-proces kan worden gestart. Het replica knooppunt moet er ook voor zorgen dat deze niet-geplande fout niet tijdelijk of lokaal is om een failover te voor komen. Deze vertraging in de detectie betekent dat een niet-geplande failover doorgaans binnen 10 tot 15 seconden wordt voltooid.

## <a name="how-does-patching-occur"></a>Hoe wordt patching uitgevoerd?

De Azure cache for redis-service biedt regel matig onderhoud voor het bijwerken van uw cache met de nieuwste platform functies en-oplossingen. Als u een cache wilt patchen, volgt de service de volgende stappen:

1. De Management service selecteert één knoop punt waarvoor een patch moet worden uitgevoerd.
1. Als het geselecteerde knoop punt een hoofd knooppunt is, bevordert het replica knooppunt zichzelf samen. Deze promotie wordt beschouwd als een geplande failover.
1. Het geselecteerde knoop punt wordt opnieuw opgestart om de nieuwe wijzigingen te maken en er wordt een back-up van gemaakt als replica knooppunt. Replica knooppunten maken verbinding met het hoofd knooppunt en synchroniseren gegevens.
1. Wanneer de gegevens synchronisatie is voltooid, wordt het patch proces herhaald voor de resterende knoop punten.

Omdat patches een geplande failover is, wordt het replica-knoop punt snel gepromoveerd om een Master te worden, en worden er aanvragen voor onderhoud en nieuwe verbindingen gestart. Basis caches hebben geen replica knooppunt en zijn pas beschikbaar als de update is voltooid. Elk Shard van een geclusterde cache wordt afzonderlijk gerepareerd en sluit geen verbindingen met een andere Shard.

> [!IMPORTANT]
> Knoop punten worden een voor een patch uitgevoerd om gegevens verlies te voor komen. Voor basis caches geldt een verlies van gegevens. Geclusterde caches worden één Shard per keer patched.

Meerdere caches in dezelfde resource groep en regio worden ook een patch per keer uitgevoerd.  Voor caches die zich in verschillende resource groepen of verschillende regio's bevinden, kan tegelijkertijd een patch worden uitgevoerd.

Omdat volledige gegevens synchronisatie plaatsvindt voordat het proces wordt herhaald, treedt er waarschijnlijk geen gegevens verlies op wanneer u een Standard-of Premium-cache gebruikt. U kunt gegevens verlies verder beveiligen door gegevens te [exporteren](cache-how-to-import-export-data.md#export) en [persistentie](cache-how-to-premium-persistence.md)in te scha kelen.

### <a name="additional-cache-load"></a>Extra cache belasting

Wanneer een failover optreedt, moeten de standaard-en Premium-caches gegevens repliceren van het ene knoop punt naar het andere. Deze replicatie veroorzaakt een aantal belasting verhogingen in het server geheugen en de CPU. Als de cache-instantie al zwaar is geladen, kunnen client toepassingen een grotere latentie krijgen. In uitzonderlijke gevallen kunnen client toepassingen time-outuitzonderingen ontvangen. [Configureer](cache-configure.md#memory-policies) de `maxmemory-reserved`-instelling van de cache om de impact van deze extra belasting te verminderen.

## <a name="how-does-a-failover-impact-my-client-application"></a>Wat is de invloed van een failover op mijn client toepassing?

Het aantal fouten dat door de client toepassing wordt weer gegeven, is afhankelijk van het aantal bewerkingen dat in die verbinding in behandeling was op het moment van de failover. Voor alle verbindingen die via het knoop punt worden doorgestuurd, worden fouten weer geven. Veel client bibliotheken kunnen verschillende typen fouten genereren, waaronder time-outuitzonderingen, verbindings uitzonderingen of socket-uitzonde ringen wanneer een verbinding wordt verbroken. Het aantal en type uitzonde ringen is afhankelijk van waar in het codepad de aanvraag zich bevindt wanneer de cache de verbindingen sluit. Een bewerking die bijvoorbeeld een aanvraag verzendt, maar geen antwoord heeft ontvangen wanneer de failover optreedt, kan een time-outuitzondering krijgen. Nieuwe aanvragen voor het object closed Connection ontvangen verbindings uitzonderingen totdat de verbinding opnieuw wordt gemaakt.

De meeste client bibliotheken proberen opnieuw verbinding te maken met de cache als dit zo is geconfigureerd, maar onvoorziene fouten kunnen af en toe de bibliotheek objecten naar een onherstelbare status worden geplaatst. Als de fouten langer duren dan een vooraf geconfigureerde hoeveelheid tijd, moet het verbindings object opnieuw worden gemaakt. In .NET en andere objectgeoriënteerd talen kunt u de verbinding opnieuw maken zonder dat de toepassing opnieuw hoeft te worden opgestart met behulp van [een luie \<T \> patroon](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#reconnecting-with-lazyt-pattern).

### <a name="what-should-i-do-in-my-application"></a>Wat moet ik doen in mijn toepassing?

Omdat een failover niet volledig kan worden vermeden, moeten client toepassingen worden geschreven voor tolerantie voor verbindings onderbrekingen en mislukte aanvragen. Ondanks dat de meeste client bibliotheken automatisch opnieuw verbinding maken met het cache-eind punt, proberen enkele client bibliotheken mislukte aanvragen opnieuw uit te voeren. Afhankelijk van het toepassings scenario kan het zinvol zijn om een logische poging te maken.

Als u de tolerantie van een client toepassing wilt testen, gebruikt u [opnieuw opstarten](cache-administration.md#reboot) als hand matige trigger voor verbindings onderbrekingen. Daarnaast is het raadzaam updates op een cache te plannen om ervoor te zorgen dat de Management service de redis runtime patches toepast tijdens de opgegeven wekelijkse Windows- [versies](cache-administration.md#schedule-updates) . Deze vensters worden meestal gekozen als Peri Oden wanneer het verkeer van de client toepassing lager is om potentiële incidenten te voor komen.

### <a name="client-network-configuration-changes"></a>Wijzigingen van client netwerk configuratie

Bepaalde wijzigingen in de netwerk configuratie van de client kunnen de fouten ' geen verbinding beschikbaar ' veroorzaken.  Het verwisselen van het virtuele IP-adres van een client toepassing tussen staging-en productie sleuven of het schalen van de grootte/het aantal exemplaren van uw toepassing kan een connectiviteits probleem veroorzaken dat minder dan een minuut duurt. De client toepassing verliest waarschijnlijk de verbinding met andere externe netwerk bronnen naast redis.

## <a name="next-steps"></a>Volgende stappen

- [Updates](cache-administration.md#schedule-updates) voor uw cache plannen.
- Toepassings tolerantie testen door de [computer opnieuw](cache-administration.md#reboot)op te starten.
- Geheugen reserveringen en-beleid [configureren](cache-configure.md#memory-policies) .
