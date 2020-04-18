---
title: Netwerkvereisten
description: Netwerkvereisten en beste netwerkpraktijken voor optimale ervaring
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: reference
ms.openlocfilehash: 9bb8818d2bb91c14eafc272774e0b633c51e58e1
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617883"
---
# <a name="network-requirements"></a>Netwerkvereisten

Een stabiele netwerkverbinding met lage latentie met een Azure-datacenter is essentieel voor een goede gebruikerservaring in Azure Remote Rendering. Slechte netwerkomstandigheden kunnen resulteren in verbroken verbindingen, onstabiele, zenuwachtige of 'springende' hologrammen en merkbare vertraging bij het bijwerken van de scènegrafiek aan de serverzijde.

## <a name="guidelines-for-network-connectivity"></a>Richtlijnen voor netwerkconnectiviteit

De exacte netwerkvereisten zijn afhankelijk van uw specifieke use case, zoals het aantal en de frequentie van wijzigingen in de externe scènegrafiek en de complexiteit van de weergegeven weergave, maar er zijn een aantal richtlijnen om ervoor te zorgen dat uw ervaring zo goed mogelijk is:

* Uw internetverbinding moet ten minste **50 Mbps downstream** en **10 Mbps upstream** consistent ondersteunen voor een enkele gebruikerssessie van Azure Remote Rendering, ervan uitgaande dat er geen concurrerend verkeer op het netwerk is. Wij raden hogere tarieven voor betere ervaringen. Met meer gebruikers op hetzelfde netwerk worden deze vereisten navenant opgeschaald.
* Het gebruik van de **5-GHz Wi-Fi-band** zal meestal betere resultaten opleveren dan de 2,4-GHz Wi-Fi-band, hoewel beide zouden moeten werken.
* Als er andere Wi-Fi-netwerken in de buurt zijn, vermijd dan het gebruik van Wi-Fi-kanalen die door deze andere netwerken worden gebruikt. U netwerkscantools zoals [WifiInfoView](https://www.nirsoft.net/utils/wifi_information_view.html) gebruiken om te controleren of de kanalen die uw Wi-Fi-netwerk gebruikt, vrij zijn van concurrerend verkeer.
* Vermijd strikt **het gebruik van Wi-Fi repeaters** of LAN-over-powerline forwarding.
* **Vermijd concurrerende bandbreedte-intens verkeer** - zoals video of game streaming - op hetzelfde Wi-Fi-netwerk.
* Het hebben van **een goede Wi-Fi signaal sterkte** is essentieel. Blijf indien mogelijk dicht bij uw Wi-Fi-toegangspunt en vermijd obstakels tussen uw clientapparaat en de toegangspunten.
* Zorg ervoor dat u altijd verbinding maakt met het **dichtstbijzijnde Azure-datacenter** voor uw [regio.](regions.md) Hoe dichter het datacenter, hoe lager de netwerklatentie, wat een enorm effect heeft op de stabiliteit van het hologram.

> [!NOTE]
> De downstream bandbreedte wordt meestal verbruikt door de videostream, die op zijn beurt is verdeeld tussen kleur- en diepte-informatie (beide 60 Hz, stereo).

## <a name="network-performance-tests"></a>Netwerkprestatietests

Als u een eerste inzicht wilt krijgen in of de kwaliteit van uw netwerkconnectiviteit voldoende is om Azure Remote Rendering uit te voeren, zijn er bestaande online hulpprogramma's die u gebruiken. We raden u ten zeerste aan deze online hulpprogramma's uit te voeren vanaf een redelijk krachtige laptop die is verbonden met dezelfde Wi-Fi als het apparaat waarop u van plan bent uw Azure Remote Rendering-clienttoepassing uit te voeren. De resultaten die zijn verkregen uit het uitvoeren van de tests op een mobiele telefoon of HoloLens2 zijn meestal minder nuttig, omdat ze hebben bewezen aanzienlijke variatie te vertonen op low-powered endpoint-apparaten. De locatie waar u de laptop plaatst, moet ongeveer op dezelfde plaats zijn waarop u het apparaat verwacht te gebruiken waarop uw Azure Remote Rendering-clienttoepassing wordt uitgevoerd.

Hier volgen een paar eenvoudige stappen voor een snelle test van uw netwerkconnectiviteit:

1. **Voer een netwerktesttool uit zoals www.speedtest.net om gegevens te verzamelen over de algehele latentie en upstream/downstreambandbreedte van uw netwerkverbinding.**
Kies een server die het dichtst bij u staat en voer de test uit. Hoewel de server niet het Azure-datacenter is waarmee Azure Remote Rendering verbinding maakt, zijn de resulterende gegevens nog steeds nuttig om de prestaties van uw internetverbinding en Wi-Fi te begrijpen.
   * **Minimale vereiste** voor Azure Remote Rendering: ongeveer 40 Mbps downstream en 5 Mbps upstream.
   * **Aanbevolen** voor Azure Remote Rendering: ongeveer 100 Mbps downstream en 10 Mbps upstream.
We raden aan om de test meerdere keren uit te voeren en de slechtste resultaten te nemen.
1. **Gebruik een tool zoals www.azurespeed.com dat latentie meet naar Azure-datacenters.** Selecteer het Azure-datacenter dat wordt ondersteund door Azure Remote Rendering dat het dichtst bij u staat (zie [ondersteunde regio's)](regions.md)en voer een **latentietest**uit. Als er variatie is in de getallen die u ziet, geeft u de resultaten wat tijd om te stabiliseren.
   * **Minimale vereiste** voor Azure Remote Rendering: latentie moet consistent minder dan 100 ms bedragen.
   * **Aanbevolen** voor Azure Remote Rendering: latentie moet consistent minder dan 70 ms zijn.

Hoewel lage latentie geen garantie is dat Azure Remote Rendering goed zal werken op uw netwerk, hebben we het meestal prima zien presteren in situaties waarin deze tests met succes zijn geslaagd.
Als u artefacten tegenkomt zoals onstabiele, zenuwachtige of springende hologrammen bij het uitvoeren van Azure Remote Rendering, raadpleegt u de [handleiding voor probleemoplossing.](../resources/troubleshoot.md)

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: een model weergeven met Unity](../quickstarts/render-model.md)
