---
title: Tijdsynchronisatie voor Linux VM's in Azure
description: Tijdsynchronisatie voor Linux virtuele machines.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: c3571d9ba94e1803259457d473ed3f1669ea67ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330585"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Tijdsynchronisatie voor Linux VM's in Azure

Tijdsynchronisatie is belangrijk voor de correlatie tussen beveiliging en gebeurtenis. Soms wordt het gebruikt voor gedistribueerde transacties implementatie. Tijdnauwkeurigheid tussen meerdere computersystemen wordt bereikt door synchronisatie. Synchronisatie kan worden beïnvloed door meerdere dingen, waaronder reboots en netwerkverkeer tussen de tijdbron en de computer die de tijd ophaalt. 

Azure wordt ondersteund door infrastructuur met Windows Server 2016. Windows Server 2016 heeft algoritmen verbeterd die worden gebruikt om de tijd te corrigeren en de lokale klok te laten synchroniseren met UTC.  De functie Nauwkeurige tijd van Windows Server 2016 is sterk verbeterd hoe de VMICTimeSync-service die VM's met de host regelt voor nauwkeurige tijd. Verbeteringen omvatten een nauwkeurigere initiële tijd bij het starten van vm's of VM-herstel en onderbreking van latentiecorrectie. 

>[!NOTE]
>Voor een snel overzicht van de Windows [Time-service](https://aka.ms/WS2016TimeVideo)u deze overzichtsvideo op hoog niveau bekijken.
>
> Zie [Accurate tijd voor Windows Server 2016 voor](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)meer informatie . 

## <a name="overview"></a>Overzicht

Nauwkeurigheid voor een computerklok wordt gemeten over hoe dicht de computerklok is bij de gecoördineerde universele tijd (UTC) tijdstandaard. UTC wordt gedefinieerd door een multinationale steekproef van precieze atoomklokken die slechts een seconde af kunnen zijn in 300 jaar. Maar, het lezen van UTC vereist direct gespecialiseerde hardware. In plaats daarvan worden tijdservers gesynchroniseerd met UTC en worden ze geopend vanaf andere computers om schaalbaarheid en robuustheid te bieden. Elke computer heeft tijdsynchronisatieservice die wordt uitgevoerd en die weet welke tijd servers te gebruiken en periodiek controleert of de computerklok moet worden gecorrigeerd en past de tijd aan indien nodig. 

Azure-hosts worden gesynchroniseerd met interne Microsoft-tijdservers die hun tijd nemen van Stratum 1-apparaten die eigendom zijn van Microsoft, met GPS-antennes. Virtuele machines in Azure kunnen afhankelijk zijn van hun host om de juiste tijd *(hosttijd)* door te geven aan de VM of de VM kan direct tijd krijgen van een tijdserver, of een combinatie van beide. 

Op stand-alone hardware, de Linux OS leest alleen de host hardware klok op de boot. Daarna wordt de klok onderhouden met behulp van de interrupt timer in de Linux kernel. In deze configuratie zal de klok in de loop van de tijd afglijden. In nieuwere Linux-distributies op Azure kunnen VM's de VMICTimeSync-provider gebruiken, die is opgenomen in de Linux-integratieservices (LIS), om vaker klokupdates van de host op te vragen.

Virtuele machine interacties met de host kan ook invloed hebben op de klok. Tijdens [het behoud van het geheugen onderhoud,](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)VM's worden onderbroken voor maximaal 30 seconden. Voordat het onderhoud begint, wordt de VM-klok bijvoorbeeld 10:00:00 uur weergegeven en duurt het 28 seconden. Nadat de VM wordt hervat, wordt de klok op de VM nog steeds 10:00:00 uur weergegeven, wat 28 seconden zou zijn. Om dit te corrigeren, controleert de VMICTimeSync-service wat er op de host gebeurt en vraagt de vm's om wijzigingen te compenseren.

Zonder dat de tijdsynchronisatie werkt, zou de klok op de VM fouten opstapelen. Wanneer er slechts één vm is, is het effect mogelijk niet significant, tenzij de werkbelasting een zeer nauwkeurige tijdwaarneming vereist. Maar in de meeste gevallen hebben we meerdere, onderling verbonden VM's die tijd gebruiken om transacties bij te houden en de tijd moet consistent zijn gedurende de hele implementatie. Wanneer de tijd tussen VM's anders is, u de volgende effecten zien:

- Verificatie mislukt. Beveiligingsprotocollen zoals Kerberos of certificaatafhankelijke technologie zijn afhankelijk van de tijd consistent zijn in de verschillende systemen.
- Het is heel moeilijk om erachter te komen wat er gebeurd is in een systeem als logs (of andere gegevens) het niet eens op tijd. Dezelfde gebeurtenis zou eruit zien alsof het zich op verschillende tijdstippen voordeed, waardoor correlatie moeilijk is.
- Als de klok is uitgeschakeld, kan de facturering onjuist worden berekend.



## <a name="configuration-options"></a>Configuratie-opties

Er zijn over het algemeen drie manieren om tijdsynchronisatie te configureren voor uw Linux-vm's die in Azure worden gehost:

- De standaardconfiguratie voor Azure Marketplace-afbeeldingen gebruikt zowel NTP-tijd als VMICTimeSync-hosttijd. 
- Alleen host met VMICTimeSync.
- Gebruik een andere externe tijdserver met of zonder vMICTimeSync-hosttijd te gebruiken.


### <a name="use-the-default"></a>De standaardinstelling gebruiken

Standaard zijn de meeste Azure Marketplace-afbeeldingen voor Linux geconfigureerd om te synchroniseren vanuit twee bronnen: 

- NTP als primair, die tijd krijgt van een NTP-server. Ubuntu 16.04 LTS Marketplace-afbeeldingen gebruiken bijvoorbeeld **ntp.ubuntu.com.**
- De VMICTimeSync-service als secundair, gebruikt om de hosttijd naar de VM's te communiceren en correcties aan te brengen nadat de VM is onderbroken voor onderhoud. Azure-hosts gebruiken Stratum 1-apparaten van Microsoft om de juiste tijd te behouden.

In nieuwere Linux-distributies maakt de VMICTimeSync-service gebruik van het precision time protocol (PTP), maar eerdere distributies ondersteunen mogelijk geen PTP en vallen terug naar NTP voor het verkrijgen van tijd van de host.

Als u wilt bevestigen dat NTP `ntpq -p` correct synchroniseert, voert u de opdracht uit.

### <a name="host-only"></a>Alleen voor host 

Omdat NTP-servers zoals time.windows.com en ntp.ubuntu.com openbaar zijn, vereist het synchroniseren van de tijd met hen het verzenden van verkeer via het internet. Verschillende pakketvertragingen kunnen een negatieve invloed hebben op de kwaliteit van de tijdsynchronisatie. Als u NTP verwijdert door over te schakelen op synchronisatie met alleen host, u soms uw resultaten voor tijdsynchronisatie verbeteren.

Overschakelen naar host-only tijdsynchronisatie is zinvol als u problemen ondervindt met tijdsynchronisatie met behulp van de standaardconfiguratie. Probeer de synchronisatie met alleen hosten uit om te zien of dat de tijdsynchronisatie op uw vm zou verbeteren. 

### <a name="external-time-server"></a>Externe tijdserver

Als u specifieke vereisten voor tijdsynchronisatie hebt, is er ook een optie om externe tijdservers te gebruiken. Externe tijdservers kunnen specifieke tijd bieden, wat handig kan zijn voor testscenario's, waardoor tijduniformiteit met machines die in niet-Microsoft-datacenters worden gehost, wordt gegarandeerd of sprongseconden op een speciale manier worden verwerkt.

U een externe tijdserver combineren met de VMICTimeSync-service om resultaten te leveren die vergelijkbaar zijn met de standaardconfiguratie. Het combineren van een externe tijdserver met VMICTimeSync is de beste optie voor het afhandelen van problemen die kunnen worden veroorzaakt wanneer VM's worden onderbroken voor onderhoud. 

## <a name="tools-and-resources"></a>Hulpprogramma's en informatiebronnen

Er zijn een aantal basisopdrachten voor het controleren van uw tijdsynchronisatieconfiguratie. Documentatie voor Linux-distributie zal meer details over de beste manier om tijdsynchronisatie voor die distributie te configureren.

### <a name="integration-services"></a>Integratieservices

Controleer of de integratieservice (hv_utils) is geladen.

```bash
lsmod | grep hv_utils
```
Je zou iets dergelijks moeten zien:

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

Kijk of de Hyper-V integratieservices daemon draait.

```bash
ps -ef | grep hv
```

Je zou iets dergelijks moeten zien:

```
root        229      2  0 17:52 ?        00:00:00 [hv_vmbus_con]
root        391      2  0 17:52 ?        00:00:00 [hv_balloon]
```


### <a name="check-for-ptp"></a>Controleren op PTP

Bij nieuwere versies van Linux is een Precision Time Protocol (PTP) klokbron beschikbaar als onderdeel van de VMICTimeSync-provider. Op oudere versies van Red Hat Enterprise Linux of CentOS 7.x kunnen de [Linux Integration Services](https://github.com/LIS/lis-next) worden gedownload en gebruikt om het bijgewerkte stuurprogramma te installeren. Bij gebruik van PTP is het Linux-apparaat van het formulier /dev/ptp*x.* 

Bekijk welke PTP-klokbronnen beschikbaar zijn.

```bash
ls /sys/class/ptp
```

In dit voorbeeld is de geretourneerde waarde *ptp0,* dus we gebruiken die om de naam van de klok te controleren. Als u het apparaat wilt verifiëren, controleert u de naam van de klok.

```bash
cat /sys/class/ptp/ptp0/clock_name
```

Dit moet **terugkeren hyperv**.

### <a name="chrony"></a>Chrony

Op Ubuntu 19.10 en later versies, Red Hat Enterprise Linux en CentOS 7.x, is [chrony](https://chrony.tuxfamily.org/) geconfigureerd om een PTP-bronklok te gebruiken. In plaats van chrony, oudere Linux releases gebruik maken van de Network Time Protocol daemon (ntpd), die geen ondersteuning voor PTP bronnen. Om PTP in die versies in te schakelen, moet chrony handmatig worden geïnstalleerd en geconfigureerd (in chrony.conf) met behulp van de volgende code:

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```

Zie [Tijdsynchronisatie](https://help.ubuntu.com/lts/serverguide/NTP.html)voor meer informatie over Ubuntu en NTP.

Zie [NTP configureren voor](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/s1-configure_ntp)meer informatie over Red Hat en NTP. 

Zie [Chrony gebruiken voor](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/sect-using_chrony)meer informatie over chrony.

Als zowel chrony- als TimeSync-bronnen tegelijkertijd zijn ingeschakeld, u er een markeren als **de voorkeur**, waardoor de andere bron als back-up wordt ingesteld. Omdat NTP-services de klok niet bijwerken voor grote scheeftrekkingen, behalve na een lange periode, herstelt de VMICTimeSync de klok veel sneller van onderbroken VM-gebeurtenissen dan alleen NTP-gebaseerde hulpprogramma's.

Standaard versnelt of vertraagt chronyd de systeemklok om elke tijdsafwijking op te lossen. Als de drift te groot wordt, slaagt chrony er niet in om de drift te repareren. Om dit te `makestep` overwinnen, kan de parameter in **/etc/chrony.conf** worden gewijzigd om een timesync te forceren als de drift de opgegeven drempel overschrijdt.

 ```bash
makestep 1.0 -1
```

Hier forceert chrony een tijdsupdate als de drift groter is dan 1 seconde. Ga als volgt te werk om de wijzigingen toe te passen, start de chronyd-service opnieuw op:

```bash
systemctl restart chronyd
```

### <a name="systemd"></a>systemd 

Op SUSE- en Ubuntu-releases vóór 19.10 uur is tijdsynchronisatie geconfigureerd met behulp van [systemd.](https://www.freedesktop.org/wiki/Software/systemd/) Zie [Tijdsynchronisatie voor](https://help.ubuntu.com/lts/serverguide/NTP.html)meer informatie over Ubuntu. Zie sectie 4.5.8 in [SUSE Linux Enterprise Server 12 SP3 Release Notes](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement)voor meer informatie over SUSE.

## <a name="next-steps"></a>Volgende stappen

Zie [Accurate tijd voor Windows Server 2016 voor](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)meer informatie .


