---
title: Monitor uw StorSimple 8000-serie apparaat
description: Beschrijft hoe u de StorSimple Device Manager-service gebruiken om het gebruik, de I/O-prestaties en het capaciteitsgebruik te controleren.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 10/17/2017
ms.author: alkohli
ms.openlocfilehash: b3b77024606c5cdb02ff7bdd357c2d14a2415efa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277055"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>Gebruik de StorSimple Device Manager-service om uw StorSimple-apparaat te controleren

## <a name="overview"></a>Overzicht
U de StorSimple Device Manager-service gebruiken om specifieke apparaten in uw StorSimple-oplossing te controleren. U aangepaste grafieken maken op basis van I/O-prestaties, capaciteitsbenutting, netwerkdoorvoer en apparaatprestatiestatistieken en deze vastmaken aan het dashboard. Ga voor meer informatie naar [het aanpassen van uw portaldashboard.](../azure-portal/azure-portal-dashboards.md)

Als u de bewakingsgegevens voor een specifiek apparaat wilt weergeven, selecteert u in de Azure-portal de StorSimple Device Manager-service. Selecteer in de lijst met apparaten uw apparaat en ga vervolgens naar **Monitor**. Vervolgens u de **grafieken Capaciteit,** **Gebruik**en **Prestaties** voor het geselecteerde apparaat weergeven.

## <a name="capacity"></a>Capaciteit
**Capaciteit** volgt de ingerichte ruimte en de resterende ruimte op het apparaat. De resterende capaciteit wordt vervolgens weergegeven als lokaal vastgemaakt of gelaagd.

De ingerichte en resterende capaciteit wordt verder uitgesplitst naar gelaagde en lokaal vastgemaakte volumes. Voor elk volume worden de ingerichte capaciteit en de resterende capaciteit op het apparaat weergegeven.

![IO-capaciteit](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>Gebruik
**Gebruik** houdt statistieken bij met betrekking tot de hoeveelheid gegevensopslagruimte die wordt gebruikt door de volumes, volumecontainers of het apparaat. U rapporten maken op basis van het capaciteitsgebruik van uw primaire opslag, uw cloudopslag of uw apparaatopslag. De bezettingsgraad kan worden gemeten op een bepaald volume, een specifieke volumecontainer of alle volumecontainers.
Standaard wordt het gebruik van de afgelopen 24 uur gerapporteerd. U de grafiek bewerken om de duur te wijzigen waarover het gebruik wordt gerapporteerd door uit te kiezen:
* Afgelopen 24 uur
* Afgelopen 7 dagen
* Afgelopen 30 dagen
* Afgelopen 90 dagen
* Afgelopen jaar

Twee belangrijke metrische gegevens, groei en bereik worden gerapporteerd voor de gebruiksgrafieken. Bereik verwijst naar de maximale waarde en de minimumwaarden van het gebruik gerapporteerd over de geselecteerde duur (fo instantie, Afgelopen 7 dagen).

Groei verwijst naar de toename van het gebruik van de eerste dag tot de laatste dag over de geselecteerde duur. 

Groei en bereik kunnen ook worden vertegenwoordigd door de volgende vergelijkingen:

```
Range = {Usage(minimum), Usage(maximum)}

Growth = Usage(Last day) - Usage(first day)

Growth (%) = [{Usage(last day) - Usage(first day)} X 100]/Usage(first day)
```

De gebruikte primaire, cloud- en lokale opslag kan als volgt worden omschreven:

### <a name="primary-storage-usage"></a>Primair opslaggebruik
Deze grafieken tonen de hoeveelheid gegevens die naar StorSimple-volumes is geschreven voordat de gegevens worden gededupliceerd en gecomprimeerd. U de primaire opslag bekijken die door alle volumes wordt gebruikt in een volumecontainer of voor één volume. De gebruikte primaire opslag wordt verder uitgesplitst naar primaire trapstenopslag en primaire lokaal vastgemaakte opslag.

De volgende grafieken tonen de primaire opslag die wordt gebruikt voor een StorSimple-apparaat voor en na het maken van een cloudmomentopname. Aangezien dit slechts volumegegevens zijn, mag een cloudmomentopname de primaire opslag niet wijzigen. Zoals u zien, toont de grafiek geen verschil in de primaire gelaagde of lokaal vastgemaakte opslag die wordt gebruikt als gevolg van het maken van een cloudmomentopname. De cloud momentopname begon rond 23:50 uur op dat apparaat.

![Primaire capaciteitsbenutting na cloudmomentopname](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

Als u nu IO uitvoert op de host die is verbonden met uw StorSimple-apparaat, wordt een toename van primaire gelaagde opslag of primaire lokaal vastgemaakte opslag gebruikt, afhankelijk van de volumes (trapsgewijs of lokaal vastgemaakt) waar u de gegevens naar schrijft. Hier zijn de primaire opslaggebruikgrafieken voor een StorSimple-apparaat. Op dit apparaat begon de StorSimple-host rond 14:30 uur te serveren op een gelaagd volume op het apparaat. U de piek zien in de schrijfbytes/s die overeenkomen met de IO die op de host wordt uitgevoerd.

![Prestaties wanneer IO draait op gelaagde volumes](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

Als u kijkt naar de gebruikte primaire trapstenopslag, is dat gestegen terwijl het primaire lokaal vastgemaakte gebruik ongewijzigd blijft omdat er geen schrijfbewerkingen worden weergegeven aan de lokaal vastgemaakte volumes op het apparaat.

![Primaire capaciteitsbenutting wanneer IO op gelaagde volumes wordt uitgevoerd](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

Als u update 3 of hoger uitvoert, u de primaire opslagcapaciteit opsplitsen met een individueel volume, alle volumes, alle gelaagde volumes en alle lokaal vastgemaakte volumes zoals hieronder weergegeven. Als u alle lokaal vastgemaakte volumes opsplitsen, u snel nagaan hoeveel van de lokale laag is opgebruikt.

![Primaire capaciteitsbenutting voor alle gelaagde volumes](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![Primaire capaciteitsbenutting voor alle lokaal vastgemaakte volumes](./media/storsimple-8000-monitor-device/monitor-usage4.png)

U verder klikken op elk van de volumes in de lijst en zie het bijbehorende gebruik.

![Primaire capaciteitsbenutting voor alle lokaal vastgemaakte volumes](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>Gebruik van cloudopslag
Deze grafieken tonen de hoeveelheid gebruikte cloudopslag. Deze gegevens worden gededuplicate en gecomprimeerd. Dit bedrag omvat cloudmomentopnamen die gegevens kunnen bevatten die niet in een primair volume worden weergegeven en worden bewaard voor oudere of vereiste bewaardoeleinden. U de verbruikscijfers voor primaire en cloudopslag vergelijken om een idee te krijgen van de gegevensreductiesnelheid, hoewel het aantal niet exact zal zijn.

De volgende grafieken tonen het gebruik van cloudopslag van een StorSimple-apparaat wanneer een cloudmomentopname is gemaakt.

* De cloudmomentopname begon rond 11:50 uur op dat apparaat en u zien dat er vóór de cloudmomentopname geen cloudopslag werd gebruikt. 
* Zodra de cloudmomentopname is voltooid, is het gebruik van cloudopslag met 0,89 GB omhoog geschoten. 
* Terwijl de cloudmomentopname aan de gang was, is er ook een overeenkomstige piek in de IO van apparaat naar cloud.

    ![Gebruik van cloudopslag vóór cloudmomentopname](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![Gebruik van cloudopslag na cloudmomentopname](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![IO van apparaat naar cloud tijdens een cloudmomentopname](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>Lokaal opslaggebruik
Deze grafieken tonen het totale gebruik voor het apparaat, dat meer dan primaire opslaggebruik zal zijn omdat het de lineaire laag VAN SSD omvat. Deze laag bevat een hoeveelheid gegevens die ook bestaat op de andere lagen van het apparaat. De capaciteit in de lineaire SSD-laag wordt zo gefietst dat wanneer nieuwe gegevens binnenkomen, de oude gegevens worden verplaatst naar de HDD-laag (op welk moment deze wordt gededupliceerd en gecomprimeerd) en vervolgens naar de cloud.

Na verloop van tijd zullen de gebruikte primaire opslag en de gebruikte lokale opslag waarschijnlijk samen toenemen totdat de gegevens naar de cloud worden gegelaagdeerd. Op dat moment zal de gebruikte lokale opslag waarschijnlijk beginnen te plateau, maar de primaire opslag gebruikt zal toenemen naarmate er meer gegevens worden geschreven.

De volgende grafieken tonen de primaire opslag die wordt gebruikt voor een StorSimple-apparaat wanneer een cloudmomentopname is gemaakt. De cloudmomentopname begon om 11:50 uur en de lokale opslag begon op dat moment af te nemen. De gebruikte lokale opslag daalde van 1.445 GB naar 1,09 GB. Dit geeft aan dat waarschijnlijk de niet-gecomprimeerde gegevens in de lineaire SSD-laag zijn gededupliceerd, gecomprimeerd en naar de HDD-laag zijn verplaatst. Houd er rekening mee dat als het apparaat al een grote hoeveelheid gegevens heeft in zowel de SSD- als de HDD-lagen, u deze daling mogelijk niet ziet. In dit voorbeeld heeft het apparaat een kleine hoeveelheid gegevens.

![Lokaal opslaggebruik na cloudmomentopname](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>Prestaties
**Prestaties** houden statistieken bij met betrekking tot het aantal lees- en schrijfbewerkingen tussen de iSCSI-initiator-interfaces op de hostserver en het apparaat of het apparaat en de cloud. Deze prestaties kunnen worden gemeten voor een specifiek volume, een specifieke volumecontainer of alle volumecontainers. Prestaties omvatten ook CPU-gebruik en netwerkdoorvoer voor de verschillende netwerkinterfaces op uw apparaat.

### <a name="io-performance-for-initiator-to-device"></a>I/O-prestaties voor initiator naar apparaat
De onderstaande grafiek toont de I/O voor de initiator van uw apparaat voor alle volumes voor een productieapparaat. De uitgezette statistieken worden gelezen en schrijf bytes per seconde. U lees-, schrijf- en uitstekende IO-gegevens ook in kaart brengen of latenlaten lezen en schrijven.

![IO-prestaties voor initiator naar apparaat](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>I/O-prestaties voor device to cloud
Voor hetzelfde apparaat worden de I/O-bewerkingen uitgezet voor de gegevens van het apparaat naar de cloud voor alle volumecontainers. Op dit apparaat zijn de gegevens alleen in de lineaire laag en is er niets naar de cloud gelekt. Er vinden geen leesbewerkingen plaats van apparaat naar cloud. Daarom zijn de pieken in de grafiek met een interval van 5 minuten die overeenkomt met de frequentie waarmee de hartslag wordt gecontroleerd tussen het apparaat en de service.

Voor hetzelfde apparaat is vanaf 11:50 uur een cloudmomentopname gemaakt voor volumegegevens. Dit resulteerde in gegevens die van het apparaat naar de cloud stroomden. Schrijft werden geserveerd aan de wolk in deze duur. De IO-grafiek toont een piek in de schrijfbytes/s die overeenkomt met het tijdstip waarop de momentopname is gemaakt.

![IO van apparaat naar cloud tijdens een cloudmomentopname](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>Netwerkdoorvoer voor apparaatnetwerkinterfaces
**Netwerkdoorvoer** houdt statistieken bij met betrekking tot de hoeveelheid gegevens die wordt overgedragen van de iSCSI-initiatornetwerkinterfaces op de hostserver en het apparaat en tussen het apparaat en de cloud. U deze statistiek controleren voor elk van de iSCSI-netwerkinterfaces op uw apparaat.

In de volgende grafieken wordt de netwerkdoorvoer weergegeven voor het data0-, 1 1 GbE-netwerk op uw apparaat, dat zowel cloudingeschakeld (standaard) als iSCSI-enabled was. Op dit apparaat op 14 juni rond 21.00 uur werden gegevens in de cloud gelaagd (er werden op dat moment geen cloudsnapshots gemaakt, wat wijst op tiering als het mechanisme om de gegevens naar de cloud te verplaatsen), wat resulteerde in io die naar de cloud werd geserveerd. Er is een overeenkomstige piek in de netwerkdoorvoergrafiek voor dezelfde tijd en het grootste deel van het netwerkverkeer is uitgaand naar de cloud.

![Netwerkdoorvoer voor gegevens 0](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

Als we kijken naar de Data 1 interface doorvoer grafiek, een andere 1 GbE netwerkinterface die alleen iSCSI-enabled was, dan was er vrijwel geen netwerkverkeer in deze duur.

![Netwerkdoorvoer voor gegevens 1](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>CPU-gebruik voor apparaat
**CPU-gebruik** houdt statistieken bij met betrekking tot de CPU die op uw apparaat wordt gebruikt. In de volgende grafiek worden de CPU-gebruiksstatistieken weergegeven voor een apparaat in productie.

![CPU-gebruik voor apparaat](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het gebruik van het dashboard van [de StorSimple Device Manager-serviceapparaat](storsimple-device-dashboard.md).
* Meer informatie over het [gebruik van de StorSimple Device Manager-service om uw StorSimple-apparaat te beheren.](storsimple-manager-service-administration.md)

