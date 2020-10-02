---
title: Hoge Beschik baarheid voor Azure cache voor redis
description: Meer informatie over Azure cache voor redis-functies en-opties voor hoge Beschik baarheid
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: yegu
ms.openlocfilehash: 145be11436eb4d0c4f6b892e5239ccacd838d780
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91654128"
---
# <a name="high-availability-for-azure-cache-for-redis"></a>Hoge Beschik baarheid voor Azure cache voor redis

Azure cache voor redis heeft ingebouwde hoge Beschik baarheid. Het doel van de architectuur met hoge Beschik baarheid is ervoor te zorgen dat uw beheerde redis-exemplaar werkt, zelfs wanneer de onderliggende virtuele machines (Vm's) worden beïnvloed door geplande of ongeplande uitval. Het biedt veel meer percentages dan wat kan worden bereikt door redis op één virtuele machine te hosten.

Azure cache voor redis implementeert hoge Beschik baarheid door gebruik te maken van meerdere Vm's, *knoop punten*genoemd, voor een cache. Deze knoop punten worden zo geconfigureerd dat gegevens replicatie en failover op gecoördineerde wijze plaatsvinden. Daarnaast worden er onderhouds bewerkingen, zoals redis-software patches, beheerd. Er zijn verschillende opties voor hoge Beschik baarheid beschikbaar in de lagen Standard en Premium:

| Optie | Beschrijving | Beschikbaarheid | Standard | Premium |
| ------------------- | ------- | ------- | :------: | :---: |
| [Standaard replicatie](#standard-replication)| Configuratie met twee knoop punten gerepliceerd in één Data Center of beschikbaarheids zone (AZ), met automatische failover | 99,9% |✔|✔|
| [Meerdere replica's](#multiple-replicas) | Gerepliceerde configuratie met meerdere knoop punten in een of meer AZs, met automatische failover | 99,95% (met zone redundantie) |-|✔|
| [Zone redundantie](#zone-redundancy) | Gerepliceerde configuratie met meerdere knoop punten op AZs, met automatische failover | 99,95% (met meerdere replica's) |-|✔|
| [Geo-replicatie](#geo-replication) | Gekoppelde cache-instanties in twee regio's met door de gebruiker beheerde failover | 99,9% (voor een enkele regio) |-|✔|

## <a name="standard-replication"></a>Standaard replicatie

Een Azure-cache voor redis in de laag Standard of Premium wordt standaard uitgevoerd op een paar redis-servers. De twee servers worden gehost op toegewezen Vm's. Open-source redis staat slechts één server toe om gegevens schrijf aanvragen af te handelen. Deze server is het *primaire* knoop punt, terwijl de andere *replica*. Nadat de server knooppunten zijn ingericht, worden de primaire en replica-rollen door Azure cache voor redis toegewezen. Het primaire knoop punt is doorgaans verantwoordelijk voor het onderhoud van schrijf bewerkingen en het lezen van aanvragen van redis-clients. Bij een schrijf bewerking voert het een nieuwe sleutel en een sleutel update door naar het interne geheugen en de antwoorden direct naar de client. De bewerking wordt asynchroon doorgestuurd naar de replica.

:::image type="content" source="media/cache-high-availability/replication.png" alt-text="Gegevens replicatie instellen":::
   
>[!NOTE]
>Normaal gesp roken communiceert een redis-client met het primaire knoop punt in een redis-cache voor alle Lees-en schrijf aanvragen. Bepaalde redis-clients kunnen worden geconfigureerd om te lezen van het replica knooppunt.
>
>

Als het primaire knoop punt in een redis-cache niet beschikbaar is, wordt de replica automatisch bevorderd om de nieuwe primaire te worden. Dit proces wordt een *failover*genoemd. Wanneer het primaire knoop punt snel wordt hersteld, wacht de replica voldoende lang. Als er een failover wordt uitgevoerd, wordt in azure cache voor redis een nieuwe VM ingericht en aan de cache toegevoegd als het replica knooppunt. De replica voert een volledige gegevens synchronisatie met het primaire uit, zodat het een andere kopie van de cache gegevens heeft.

Een primair knoop punt kan buiten gebruik worden genomen als onderdeel van een geplande onderhouds activiteit, zoals de redis-software of de update van het besturings systeem. Het kan ook niet meer werken vanwege niet-geplande gebeurtenissen, zoals storingen in onderliggende hardware, software of netwerk. [Failover en Patching voor Azure cache voor redis](cache-failover.md) biedt een gedetailleerde uitleg over de typen redis-failovers. Een Azure-cache voor redis gaat tijdens de levens duur over veel failovers. De architectuur met hoge Beschik baarheid is zo ontworpen dat deze wijzigingen in een cache zo doorzichtig mogelijk worden gemaakt voor de clients.

## <a name="multiple-replicas"></a>Meerdere replica's

>[!NOTE]
>Dit is beschikbaar als een preview-versie.
>
>

Met Azure cache voor redis worden extra replica knooppunten in de Premium-laag toegestaan. Een [cache met meerdere replica's](cache-how-to-multi-replicas.md) kan worden geconfigureerd met Maxi maal drie replica knooppunten. Als u meer replica's hebt, wordt de tolerantie in het algemeen verbeterd vanwege de extra knoop punten die een back-up maken van de primaire. Zelfs met meer replica's kan een Azure-cache voor redis-exemplaar nog steeds ernstige gevolgen ondervinden van een storing in een Data Center of AZ-Wide. U kunt de beschik baarheid van de cache verhogen met behulp van meerdere replica's in combi natie met [zone redundantie](#zone-redundancy).

## <a name="zone-redundancy"></a>Zoneredundantie

>[!NOTE]
>Dit is beschikbaar als een preview-versie.
>
>

Azure cache voor redis ondersteunt zone redundante configuraties in de Premium-laag. Een [zone-redundante cache](cache-how-to-zone-redundancy.md) kan zijn knoop punten in verschillende [Azure-beschikbaarheidszones](https://docs.microsoft.com/azure/availability-zones/az-overview) in dezelfde regio plaatsen. Het elimineert Data Center of AZ-uitval als Single Point of Failure en verhoogt de algehele Beschik baarheid van uw cache.

In het volgende diagram ziet u de zone redundante configuratie:

:::image type="content" source="media/cache-high-availability/zone-redundancy.png" alt-text="Gegevens replicatie instellen":::
   
Met Azure cache voor redis worden knoop punten in een zone-redundante cache gedistribueerd op basis van de AZs die u hebt geselecteerd. Het bepaalt ook welk knoop punt in eerste instantie als primair moet fungeren.

Een zone-redundante cache biedt automatische failover. Wanneer het huidige primaire knoop punt niet beschikbaar is, gaat een van de replica's over. Uw toepassing kan de reactie tijd van de cache verhogen als het nieuwe primaire knoop punt zich in een andere AZ bevindt. AZs zijn geografisch gescheiden. Als u overschakelt van de ene AZ naar een andere, verandert de fysieke afstand tussen de locatie waar uw toepassing en cache wordt gehost. Deze wijziging is van invloed op netwerk latenties van uw toepassing naar de cache. De extra latentie wordt verwacht binnen een acceptabel bereik voor de meeste toepassingen. We raden u aan uw toepassing te testen om er zeker van te zijn dat deze goed kan worden uitgevoerd met een zone-redundante cache.

## <a name="geo-replication"></a>Geo-replicatie

Geo-replicatie is hoofd zakelijk ontworpen voor herstel na nood gevallen. Het biedt u de mogelijkheid om een Azure-cache te configureren voor een redis-exemplaar in een andere Azure-regio, om een back-up van uw primaire cache te maken. [Geo-replicatie voor Azure cache instellen voor redis](cache-how-to-geo-replication.md) geeft een gedetailleerde uitleg over de werking van geo-replicatie.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het configureren van Azure cache voor redis opties voor hoge Beschik baarheid.

* [Azure-cache voor redis Premium-Service lagen](cache-overview.md#service-tiers)
* [Replica's toevoegen aan Azure-cache geheugen voor redis](cache-how-to-multi-replicas.md)
* [Zone redundantie voor Azure cache voor redis inschakelen](cache-how-to-zone-redundancy.md)
* [Geo-replicatie voor Azure cache instellen voor redis](cache-how-to-geo-replication.md)
