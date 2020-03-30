---
title: Azure FXT Edge Filer bewaken
description: Hardwarestatus controleren voor de hybride opslagcache van Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 3f422339af2040ad81c585c0e193e6cb3667b135
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72254885"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>Status azure FXT Edge Filer-hardware bewaken

Het Azure FXT Edge Filer hybride opslagcachesysteem heeft meerdere statuslampjes ingebouwd in het chassis om beheerders te helpen begrijpen hoe de hardware werkt.

## <a name="system-health-status"></a>Status van systeemstatus

Als u cachebewerkingen op een hoger niveau wilt controleren, gebruikt u de **dashboardpagina** van het configuratiescherm van de software, zoals beschreven in de [dashboardhandleiding voor](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html) het Configuratiescherm

## <a name="hardware-status-leds"></a>Hardwarestatus-LED's

In dit gedeelte worden de verschillende statuslampjes uitgelegd die zijn ingebouwd in de Azure FXT Edge Filer-hardware.

### <a name="hard-drive-status-leds"></a>LED's met de status van harde schijf

![afbeelding van de harde schijf voor, horizontaal, met de aanroeplabels 2 (linkerbovenhoek), 1 (linkerbenedenhoek) en 3 (rechterzijde)](media/fxt-monitor/fxt-drive-callouts.png)

Elke aandrijfdrager heeft twee status-LED's: een activiteitsindicator (1) en een statusindicator (2). 

* De activiteit LED (1) licht wanneer de drive in gebruik is.  
* De status-LED (2) geeft de staat van het station aan met behulp van de codes in de onderstaande tabel.

| LED-status drive-status              | Betekenis  |
|-------------------------------------|----------------------------------------------------------|
| Knippert twee keer per seconde groen      | Station *of* <br> Voorbereiden de drive voor verwijdering  |
| Uit (onverlicht)                         | Het systeem is nog niet klaar met opstarten *of* <br>Station is klaar om te worden verwijderd |
| Knippert groen, oranje en uit       | Schijfstoring wordt voorspeld   |
| Knippert vier keer per seconde oranje | Station is mislukt   |
| Effen groen                         | Station is online |

De rechterkant van het station (3) is gelabeld met de capaciteit van de schijf en andere informatie.

Stationnummers worden afgedrukt op de ruimte tussen stations. In de Azure FXT Edge Filer is schijf 0 het schijfje linksboven en schijf 1 zit er direct onder. Nummering gaat verder in dat patroon. 

![foto van één bay voor harde schijf in het FXT-chassis, met schijfnummers en capaciteitslabels](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>Linkerconfiguratiescherm

Het linker bedieningspaneel aan de voorzijde heeft verschillende status-LED-indicatoren (1) en een grote verlichte systeemstatusindicator (2). 

![linkerstatuspaneel, met 1 labelstatusindicatoren aan de linkerkant en 2 met de labeling van het grote systeemstatusindicatorlampje aan de rechterkant](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>Statusindicatoren van het Configuratiescherm 

De statusindicatoren aan de linkerkant tonen een massief oranje licht als er een fout in dat systeem optreedt. De onderstaande tabel beschrijft mogelijke oorzaken en oplossingen voor de fouten. 

Als u de fout nog steeds hebt nadat u deze oplossingen hebt geprobeerd, neemt [u contact op met de ondersteuning](fxt-support-ticket.md) voor hulp. 

| Pictogram | Beschrijving | Foutvoorwaarde | Mogelijke oplossingen |
|----------------|---------------|--------------------|----------------------|
| ![pictogram station](media/fxt-monitor/fxt-hd-icon.jpg) | Status van station | Schijffout | Controleer het systeemgebeurtenislogboek om te bepalen of er een fout op het station is opgetreden of <br>Voer de juiste online diagnostische test uit; het systeem opnieuw opstarten en embedded diagnostics (ePSA) uitvoeren, of <br>Als de stations zijn geconfigureerd in een RAID-array, start u het systeem opnieuw op en voert u het hulpprogramma voor configuratie van de hostadapter in |
|![pictogram temperatuur](media/fxt-monitor/fxt-temp-icon.jpg) | Temperatuurstatus | Thermische fout - bijvoorbeeld, een ventilator is uitgevallen of de omgevingstemperatuur is buiten bereik | Controleer op de volgende adresseerbare voorwaarden: <br>Een koelventilator ontbreekt of is mislukt <br>De hoes, luchtmantel, geheugenmodule leeg of achtervulbeugel van het systeem worden verwijderd <br>De omgevingstemperatuur is te hoog <br>Externe luchtstroom wordt belemmerd |
|![elektriciteitspictogram](media/fxt-monitor/fxt-electric-icon.jpg) | Elektrische status | Elektrische fout - bijvoorbeeld spanning buiten bereik, defecte voeding of een defecte spanningsregelaar |  Controleer het systeemgebeurtenislogboek of systeemberichten op het specifieke probleem. Als er een PSU probleem, controleer de PSU status LED en reseat de PSU indien nodig. | 
|![geheugenpictogram](media/fxt-monitor/fxt-memory-icon.jpg) | Geheugenstatus | Geheugenfout | Controleer het systeemgebeurtenislogboek of systeemberichten op de locatie van het mislukte geheugen. plaats de geheugenmodule opnieuw. |
|![PICTOGRAM PCIe](media/fxt-monitor/fxt-pcie-icon.jpg) | PCIe-status | PCIe-kaartfout | Start het systeem opnieuw op; PCIe-kaartstuurprogramma's bijwerken; de kaart opnieuw installeren |


### <a name="system-health-status-indicator"></a>Indicator van de status van het systeem

De grote verlichte knop rechts van het linker bedieningspaneel geeft de algehele systeemstatus aan en wordt ook gebruikt als unit locator-licht in de systeem-ID-modus.

Druk op de systeemstatus en id-knop om te schakelen tussen de systeem-ID-modus en de systeemstatus.

|Status van systeemstatus | Voorwaarde |
|-------------------------------------------|-----------------------------------------------|
| Effen blauw | Normale werking: het systeem is ingeschakeld, werkt normaal en de systeem-ID-modus is niet actief. <br/>Druk op de systeemstatus en id-knop als u wilt overschakelen naar de systeem-ID-modus. |
| Knipperend blauw | De systeem-id-modus is actief. Druk op de systeemstatus en systeem-ID-knop als u wilt overschakelen naar de systeemstatusmodus. |
| Massief oranje | Het systeem bevindt zich in de fail-safe modus. Als het probleem zich blijft voordoen, neemt [u contact op met de klantenservice en ondersteuning van Microsoft.](fxt-support-ticket.md) |
| Knipperend oranje | Systeemfout. Controleer het systeemgebeurtenislogboek op specifieke foutberichten. Zie de pagina Foutcode opzoeken op qrl.dell.com voor informatie over de gebeurtenis- en foutberichten die worden gegenereerd door de systeemfirmware en agents die systeemonderdelen controleren. |


