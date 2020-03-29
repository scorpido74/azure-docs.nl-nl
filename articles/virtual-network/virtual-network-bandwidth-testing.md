---
title: Azure VM-netwerkdoorvoer testen
titlesuffix: Azure Virtual Network
description: Meer informatie over het testen van de netwerkdoorvoer van Azure-virtuele machines.
services: virtual-network
documentationcenter: na
author: steveesp
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/21/2017
ms.author: steveesp
ms.openlocfilehash: 80e8a5e5de1da2098d895e09b36fb209050743a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60743071"
---
# <a name="bandwidththroughput-testing-ntttcp"></a>Testen bandbreedte/doorvoer (NTTTCP)

Bij het testen van de netwerkdoorvoerprestaties in Azure u het beste een tool gebruiken die het netwerk target voor tests en het gebruik van andere resources minimaliseert die van invloed kunnen zijn op de prestaties. NTTTCP wordt aanbevolen.

Kopieer het gereedschap naar twee Azure VM's van dezelfde grootte. De ene VM fungeert als VERZENDER en de andere als ONTVANGER.

#### <a name="deploying-vms-for-testing"></a>VM's implementeren voor tests
Voor de toepassing van deze test moeten de twee VM's zich in dezelfde CloudService of dezelfde beschikbaarheidsset bevinden, zodat we hun interne IP's kunnen gebruiken en de Load Balancers van de test kunnen uitsluiten. Het is mogelijk om te testen met de VIP, maar dit soort testen valt buiten het bereik van dit document.

Noteer het IP-adres van de ontvanger. Laten we dat IP "a.b.c.r" noemen.

Noteer het aantal cores op de VM. Laten we dit\#"num\_cores" noemen.

Voer de NTTTCP-test 300 seconden (of 5 minuten) uit op de vm en ontvanger VM van de afzender.

Tip: Wanneer u deze test voor de eerste keer instelt, u een kortere testperiode proberen om eerder feedback te krijgen. Zodra het gereedschap werkt zoals verwacht, verlengt u de testperiode tot 300 seconden voor de meest nauwkeurige resultaten.

> [!NOTE]
> De verzender **en** ontvanger moeten dezelfde parameter voor **de** testduur (-t) opgeven.

Een enkele TCP-stream gedurende 10 seconden testen:

Ontvangerparameters: ntttcp -r -t 10 -P 1

Verzendparameters: ntttcp -s10.27.33.7 -t 10 -n 1 -P 1

> [!NOTE]
> Het vorige voorbeeld mag alleen worden gebruikt om uw configuratie te bevestigen. Geldige voorbeelden van testen worden later in dit document behandeld.

## <a name="testing-vms-running-windows"></a>VM's met WINDOWS testen:

#### <a name="get-ntttcp-onto-the-vms"></a>Download NTTTCP op de VM's.

Download de nieuwste versie:<https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769>

Of zoek ernaar als <https://www.bing.com/search?q=ntttcp+download> \< verplaatst: -- moet eerst worden geraakt

Overweeg om NTTTCP in een\\aparte map te plaatsen, zoals c: gereedschappen

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>NTTTCP toestaan via de Windows-firewall
Maak op de ontvanger een regel Toestaan op de Windows Firewall zodat het NTTTCP-verkeer kan binnenkomen. Het is het gemakkelijkst om het hele NTTTCP-programma op naam toe te staan in plaats van specifieke TCP-poorten toe te staan.

Ntttcp toestaan via de Windows Firewall als volgt:

netsh advfirewall firewall add\<\>\\rule program= PATH ntttcp.exe name="ntttcp" protocol=any dir=in action=allow enable=yes profile=ANY

Als u bijvoorbeeld ntttcp.exe hebt gekopieerd naar\\de map 'c: gereedschappen', is dit de opdracht: 

netsh advfirewall firewall add rule\\\\program=c: tools ntttcp.exe name="ntttcp" protocol=any dir=in action=allow enable=yes profile=ANY

#### <a name="running-ntttcp-tests"></a>NTTTCP-tests uitvoeren

Start NTTTCP op de ontvanger **(uitgevoerd vanaf CMD**, niet van PowerShell):

ntttcp -r –m\*\#\_[2\*num cores], a.b.c.r -t 300

Als de VM vier cores en een IP-adres van 10.0.0.4 heeft, zou het er als volgt uitzien:

ntttcp -r –m\*8, 10.0.0.4 -t 300


NtTTCP starten op de afzender **(uitvoeren vanaf CMD**, niet vanaf PowerShell):

ntttcp -s –m\*8, 10.0.0.4 -t 300 

Wacht op de resultaten.


## <a name="testing-vms-running-linux"></a>VM's testen met LINUX:

Gebruik nttcp-for-linux. Het is verkrijgbaar bij<https://github.com/Microsoft/ntttcp-for-linux>

Voer op de Linux VM's (zowel AFZENDER als ONTVANGER) deze opdrachten uit om ntttcp-for-linux op uw VM's voor te bereiden:

CentOS - Git installeren:
``` bash
  yum install gcc -y  
  yum install git -y
```
Ubuntu - Installeer Git:
``` bash
 apt-get -y install build-essential  
 apt-get -y install git
```
Maken en installeren op beide:
``` bash
 git clone https://github.com/Microsoft/ntttcp-for-linux
 cd ntttcp-for-linux/src
 make && make install
```

Net als in het Windows-voorbeeld gaan we ervan uit dat het IP-adres van de Linux-ontvanger 10.0.0.4 is

NttTCP-for-Linux starten op de ontvanger:

``` bash
ntttcp -r -t 300
```

En op de AFZENDER, uitvoeren:

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
Testlengte standaard tot 60 seconden als er geen tijdparameter wordt gegeven

## <a name="testing-between-vms-running-windows-and-linux"></a>Testen tussen VM's met Windows en LINUX:

Bij deze scenario's moeten we de no-sync-modus inschakelen, zodat de test kan worden uitgevoerd. Dit wordt gedaan met behulp van de **-N vlag** voor Linux, en **-ns vlag** voor Windows.

#### <a name="from-linux-to-windows"></a>Van Linux naar Windows:

Ontvanger \<Windows>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Windows server IP>
```

Afzender \<Linux>:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Windows server IP> -N -t 300
```

#### <a name="from-windows-to-linux"></a>Van Windows tot Linux:

Ontvanger \<Linux>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Linux server IP>
```

Afzender \<Windows>:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Linux  server IP> -ns -t 300
```
## <a name="testing-cloud-service-instances"></a>Cloudservice-exemplaren testen:
U moet de volgende sectie toevoegen aan uw ServiceDefinition.csdef
```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" />
</Endpoints> 
```

## <a name="next-steps"></a>Volgende stappen
* Afhankelijk van de resultaten kan er ruimte zijn om [netwerkdoorvoermachines](virtual-network-optimize-network-bandwidth.md) te optimaliseren voor uw scenario.
* Lees hoe [bandbreedte wordt toegewezen aan virtuele machines](virtual-machine-network-throughput.md)
* Meer informatie met veelgestelde vragen over [Azure Virtual Network (FAQ)](virtual-networks-faq.md)
