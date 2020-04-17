---
title: Tijdsynchronisatie voor Windows VM's in Azure
description: Tijdsynchronisatie voor virtuele Windows-machines.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 33c8d58877a73c1ea3055fe52ef8470d8775b6e3
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458025"
---
# <a name="time-sync-for-windows-vms-in-azure"></a>Tijdsynchronisatie voor Windows VM's in Azure

Tijdsynchronisatie is belangrijk voor de correlatie tussen beveiliging en gebeurtenis. Soms wordt het gebruikt voor gedistribueerde transacties implementatie. Tijdnauwkeurigheid tussen meerdere computersystemen wordt bereikt door synchronisatie. Synchronisatie kan worden beïnvloed door meerdere dingen, waaronder reboots en netwerkverkeer tussen de tijdbron en de computer die de tijd ophaalt. 

Azure wordt nu ondersteund door infrastructuur met Windows Server 2016. Windows Server 2016 heeft algoritmen verbeterd die worden gebruikt om de tijd te corrigeren en de lokale klok te laten synchroniseren met UTC.  Windows Server 2016 heeft ook de VMICTimeSync-service verbeterd die bepaalt hoe VM's synchroniseren met de host voor nauwkeurige tijd. Verbeteringen omvatten een nauwkeurigere initiële tijd bij het starten van VM of VM-herstel en onderbreking van latentiecorrectie voor voorbeelden die aan Windows Time (W32time) worden geleverd. 


>[!NOTE]
>Voor een snel overzicht van de Windows [Time-service](https://aka.ms/WS2016TimeVideo)u deze overzichtsvideo op hoog niveau bekijken.
>
> Zie [Accurate tijd voor Windows Server 2016 voor](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)meer informatie . 

## <a name="overview"></a>Overzicht

Nauwkeurigheid voor een computerklok wordt gemeten over hoe dicht de computerklok is bij de gecoördineerde universele tijd (UTC) tijdstandaard. UTC wordt gedefinieerd door een multinationale steekproef van precieze atoomklokken die slechts een seconde af kunnen zijn in 300 jaar. Maar, het lezen van UTC vereist direct gespecialiseerde hardware. In plaats daarvan worden tijdservers gesynchroniseerd met UTC en worden ze geopend vanaf andere computers om schaalbaarheid en robuustheid te bieden. Elke computer heeft tijdsynchronisatieservice die wordt uitgevoerd en die weet welke tijd servers te gebruiken en periodiek controleert of de computerklok moet worden gecorrigeerd en past de tijd aan indien nodig. 

Azure-hosts worden gesynchroniseerd met interne Microsoft-tijdservers die hun tijd nemen van Stratum 1-apparaten die eigendom zijn van Microsoft, met GPS-antennes. Virtuele machines in Azure kunnen afhankelijk zijn van hun host om de juiste tijd *(hosttijd)* door te geven aan de VM of de VM kan direct tijd krijgen van een tijdserver, of een combinatie van beide. 

Virtuele machine interacties met de host kan ook invloed hebben op de klok. Tijdens [het behoud van het geheugen onderhoud,](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)VM's worden onderbroken voor maximaal 30 seconden. Voordat het onderhoud begint, wordt de VM-klok bijvoorbeeld 10:00:00 uur weergegeven en duurt het 28 seconden. Nadat de VM wordt hervat, wordt de klok op de VM nog steeds 10:00:00 uur weergegeven, wat 28 seconden zou zijn. Om dit te corrigeren, controleert de VMICTimeSync-service wat er op de host gebeurt en vraagt de vm's om wijzigingen te compenseren.

De VMICTimeSync-service werkt in de voorbeeld- of synchronisatiemodus en heeft alleen invloed op de klok vooruit. In de voorbeeldmodus, waarvoor W32time moet worden uitgevoerd, peilt de VMICTimeSync-service de host elke 5 seconden en geeft het tijdsvoorbeelden aan W32time. Ongeveer elke 30 seconden neemt de W32time-service de laatste tijdsteekproef en gebruikt deze om de klok van de gast te beïnvloeden. De synchronisatiemodus wordt geactiveerd als een gast is hervat of als de klok van een gast meer dan 5 seconden achter de klok van de host zweeft. In gevallen waarin de W32time-service goed wordt uitgevoerd, mag dit laatste geval nooit gebeuren.

Zonder dat de tijdsynchronisatie werkt, zou de klok op de VM fouten opstapelen. Wanneer er slechts één vm is, is het effect mogelijk niet significant, tenzij de werkbelasting een zeer nauwkeurige tijdwaarneming vereist. Maar in de meeste gevallen hebben we meerdere, onderling verbonden VM's die tijd gebruiken om transacties bij te houden en de tijd moet consistent zijn gedurende de hele implementatie. Wanneer de tijd tussen VM's anders is, u de volgende effecten zien:

- Verificatie mislukt. Beveiligingsprotocollen zoals Kerberos of certificaatafhankelijke technologie zijn afhankelijk van de tijd consistent zijn in de verschillende systemen. 
- Het is heel moeilijk om erachter te komen wat er gebeurd is in een systeem als logs (of andere gegevens) het niet eens op tijd. Dezelfde gebeurtenis zou eruit zien alsof het zich op verschillende tijdstippen voordeed, waardoor correlatie moeilijk is.
- Als de klok is uitgeschakeld, kan de facturering onjuist worden berekend.

De beste resultaten voor Windows-implementaties worden bereikt door Windows Server 2016 te gebruiken als gastbesturingssysteem, wat ervoor zorgt dat u de nieuwste verbeteringen in tijdsynchronisatie gebruiken.

## <a name="configuration-options"></a>Configuratie-opties

Er zijn drie opties voor het configureren van tijdsynchronisatie voor uw Windows VM's die in Azure worden gehost:

- Gastheer tijd en time.windows.com. Dit is de standaardconfiguratie die wordt gebruikt in Azure Marketplace-afbeeldingen.
- Alleen als gastheer.
- Gebruik een andere externe tijdserver met of zonder gebruikstijd.


### <a name="use-the-default"></a>De standaardinstelling gebruiken

Standaard zijn Windows OS VM-afbeeldingen geconfigureerd voor w32time om te synchroniseren vanuit twee bronnen: 

- De NtpClient provider, die informatie krijgt van time.windows.com.
- De VMICTimeSync-service, die wordt gebruikt om de hosttijd naar de VM's te communiceren en correcties aan te brengen nadat de VM is onderbroken voor onderhoud. Azure-hosts gebruiken Stratum 1-apparaten van Microsoft om de juiste tijd te behouden.

w32time geeft de voorkeur aan de tijdprovider in de volgende volgorde van prioriteit: stratumniveau, wortelvertraging, wortelverspreiding, tijdscompensatie. In de meeste gevallen geeft w32time de voorkeur aan time.windows.com aan de host, omdat time.windows.com lagere laag rapporteert. 

Voor domeinsamengevoegde machines stelt het domein zelf de tijdsynchronisatiehiërarchie vast, maar de forestroot moet nog steeds ergens tijd in beslag nemen en de volgende overwegingen blijven waar.


### <a name="host-only"></a>Alleen voor host 

Omdat time.windows.com een openbare NTP-server is, vereist het synchroniseren van de tijd met het verzenden van verkeer via internet, verschillende pakketvertragingen kunnen een negatieve invloed hebben op de kwaliteit van de tijdsynchronisatie. Als u time.windows.com verwijdert door over te schakelen op host-only synchronisatie, u soms uw resultaten voor tijdsynchronisatie verbeteren.

Overschakelen naar host-only tijdsynchronisatie is zinvol als u problemen ondervindt met tijdsynchronisatie met behulp van de standaardconfiguratie. Probeer de synchronisatie met alleen hosten uit om te zien of dat de tijdsynchronisatie op VM zou verbeteren. 

### <a name="external-time-server"></a>Externe tijdserver

Als u specifieke vereisten voor tijdsynchronisatie hebt, is er ook een optie om externe tijdservers te gebruiken. Externe tijdservers kunnen specifieke tijd bieden, wat handig kan zijn voor testscenario's, waardoor tijduniformiteit met machines die in niet-Microsoft-datacenters worden gehost, wordt gegarandeerd of sprongseconden op een speciale manier worden verwerkt.

U externe servers combineren met de VMICTimeSync-service en VMICTimeProvider om resultaten te leveren die vergelijkbaar zijn met de standaardconfiguratie. 

## <a name="check-your-configuration"></a>Controleer de configuratie


Controleer of de NtpClient-tijdprovider is geconfigureerd voor het gebruik van expliciete NTP-servers (NTP) of domeintijdsynchronisatie (NT5DS).

```
w32tm /dumpreg /subkey:Parameters | findstr /i "type"
```

Als de VM NTP gebruikt, ziet u de volgende uitvoer:

```
Value Name                 Value Type          Value Data
Type                       REG_SZ              NTP
```

Ga als u wilt zien welke tijdserver de NtpClient-tijdprovider gebruikt, op een prompttype met verhoogde opdracht:

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

Als de vm de standaardwaarde gebruikt, ziet de uitvoer er als volgt uit:

```
NtpServer                  REG_SZ              time.windows.com,0x8
```


Om te zien welke tijd provider wordt momenteel gebruikt.

```
w32tm /query /source
```


Hier is de output die je kon zien en wat het zou betekenen:
    
- **time.windows.com** - in de standaardconfiguratie krijgt w32time tijd van time.windows.com. De kwaliteit van de tijdsynchronisatie is afhankelijk van de internetverbinding en wordt beïnvloed door pakketvertragingen. Dit is de gebruikelijke uitvoer van de standaardinstelling.
- **VM IC Time Synchronization Provider** - de VM synchroniseert de tijd van de host. Dit is meestal het resultaat als u zich afziet voor host-only time sync of de NtpServer is niet beschikbaar op dit moment. 
- *Uw domeinserver* - de huidige machine bevindt zich in een domein en het domein definieert de tijdsynchronisatiehiërarchie.
- *Een andere server* - w32time is expliciet geconfigureerd om de tijd te krijgen van dat een andere server. De kwaliteit van de tijdsynchronisatie is afhankelijk van de serverkwaliteit van deze tijd.
- **Lokale CMOS-klok** - klok is niet gesynchroniseerd. U deze uitvoer krijgen als w32time niet genoeg tijd heeft gehad om te beginnen na een herstart of wanneer alle geconfigureerde tijdbronnen niet beschikbaar zijn.


## <a name="opt-in-for-host-only-time-sync"></a>Opt-in voor het synchroniseren van alleen hosten

Azure werkt voortdurend aan het verbeteren van de tijdsynchronisatie op hosts en kan garanderen dat de synchronisatie-infrastructuur van Microsoft altijd is ondergebracht in datacenters die eigendom zijn van Microsoft. Als u problemen hebt met tijdsynchronisatie met de standaardinstelling die time.windows.com liever als primaire tijdbron gebruikt, u de volgende opdrachten gebruiken om u aan te melden voor alleen hosttijdsynchronisatie.

Markeer de VMIC-provider als ingeschakeld. 

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\VMICTimeProvider /v Enabled /t REG_DWORD /d 1 /f
```

Markeer de NTPClient-provider als uitgeschakeld.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\NtpClient /v Enabled /t REG_DWORD /d 0 /f
```

Start de w32time-service opnieuw.

```
net stop w32time && net start w32time
```


## <a name="windows-server-2012-and-r2-vms"></a>Windows Server 2012- en R2-vm's 

Windows Server 2012 en Windows Server 2012 R2 hebben verschillende standaardinstellingen voor tijdsynchronisatie. De w32time is standaard geconfigureerd op een manier die de voorkeur geeft aan een lage overhead van de service boven de precieze tijd. 

Als u uw Windows Server 2012- en 2012 R2-implementaties wilt verplaatsen om de nieuwere standaardinstellingen te gebruiken die de exacte tijd verkiezen, u de volgende instellingen toepassen.

Werk de w32time-enquête- en update-intervallen bij om aan de instellingen van Windows Server 2016 te voldoen.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MinPollInterval /t REG_DWORD /d 6 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MaxPollInterval /t REG_DWORD /d 10 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v UpdateInterval /t REG_DWORD /d 100 /f
w32tm /config /update
```

Voor w32time om de nieuwe poll intervallen te kunnen gebruiken, de NtpServers worden gemarkeerd als het gebruik ervan. Als servers zijn geannoteerd met 0x1 bitflag masker, dat zou dit mechanisme overschrijven en w32time zou gebruik maken van SpecialPollInterval plaats. Zorg ervoor dat opgegeven NTP-servers 0x8-vlag of helemaal geen vlag gebruiken:

Controleer welke vlaggen worden gebruikt voor de gebruikte NTP-servers.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

## <a name="next-steps"></a>Volgende stappen

Hieronder vindt u links naar meer details over de tijdsynchronisatie:

- [Hulpprogramma’s en instellingen voor de Windows Time-service](https://docs.microsoft.com/windows-server/networking/windows-time-service/Windows-Time-Service-Tools-and-Settings)
- [Verbeteringen in Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/windows-server-2016-improvements)
- [Nauwkeurige tijd voor Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)
- [Ondersteuningsgrens voor het configureren van de Windows Time-service voor omgevingen met hoge nauwkeurigheid](https://docs.microsoft.com/windows-server/networking/windows-time-service/support-boundary)


