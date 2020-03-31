---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/10/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 427117fe47294a1db1fa8d3fa1e46ee1efb91b4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128720"
---
## <a name="limitations"></a>Beperkingen

- Virtuele machineschaalsets worden momenteel niet ondersteund op speciale hosts.
- De grootte en hardwaretypen die beschikbaar zijn voor toegewijde hosts verschillen per regio. Raadpleeg de [pagina hostprijzen](https://aka.ms/ADHPricing) voor meer informatie.

## <a name="create-a-host-group"></a>Een hostgroep maken

Een **hostgroep** is een nieuwe bron die een verzameling toegewijde hosts vertegenwoordigt. U maakt een hostgroep in een regio en een beschikbaarheidszone en voegt er hosts aan toe. Bij het plannen voor hoge beschikbaarheid zijn er extra opties. U een of beide van de volgende opties gebruiken met uw speciale hosts: 
- Overspannen in meerdere beschikbaarheidszones. In dit geval moet u een hostgroep hebben in elk van de zones die u wilt gebruiken.
- Overspannen meerdere foutdomeinen die zijn toegewezen aan fysieke racks. 
 
In beide gevallen moet u het aantal foutdomeinen voor uw hostgroep verstrekken. Als u foutdomeinen in uw groep niet wilt overspannen, gebruikt u een aantal foutdomeinen van 1. 

U ook besluiten om zowel beschikbaarheidszones als foutdomeinen te gebruiken. 

In dit voorbeeld maken we een hostgroep met 1 beschikbaarheidszone en 2 foutdomeinen. 


1. Open de [Azure-portal](https://portal.azure.com).
1. Selecteer **Een resource maken** in de linkerbovenhoek.
1. Zoek naar **de groep Host** en selecteer vervolgens **Hostgroepen** in de resultaten.
1. Selecteer op de pagina **Hostgroepen** de optie **Maken**.
1. Selecteer het abonnement dat u wilt gebruiken en selecteer **Nieuw maken** om een nieuwe brongroep te maken.
1. Typ *myDedicatedHostsRG* als **naam** en selecteer **OK**.
1. Typ *myHostGroup*voor **de naam van de hostgroep**.
1. Selecteer **Oost-VS**voor **Locatie**.
1. Selecteer **1**voor **beschikbaarheidszone**.
1. Selecteer **2**voor **het aantal foutdomeinen**.
1. Selecteer **Controleren + maken** en wacht vervolgens op validatie.
1. Zodra u het **doorgegeven bericht Validatie ziet,** selecteert u **Maken** om de hostgroep te maken.

Het duurt slechts een paar ogenblikken om de hostgroep te maken.

## <a name="create-a-dedicated-host"></a>Een speciale host maken

Maak nu een speciale host in de hostgroep. Naast een naam voor de host, bent u verplicht om de SKU voor de host te verstrekken. Host SKU legt de ondersteunde VM-serie vast, evenals de hardwaregeneratie voor uw toegewijde host.

Zie [Azure Dedicated Host-prijzen](https://aka.ms/ADHPricing)voor meer informatie over de host-SKU's en -prijzen.

Als u een aantal foutdomeinen instelt voor uw hostgroep, wordt u gevraagd het foutdomein voor uw host op te geven.  

1. Selecteer **Een resource maken** in de linkerbovenhoek.
1. Zoek **naar Dedicated host** en selecteer dedicated **hosts** in de resultaten.
1. Selecteer op de pagina **Dedicated Hosts** de optie **Maken**.
1. Selecteer het abonnement dat u wilt gebruiken.
1. Selecteer *myDedicatedHostsRG* als de **resourcegroep**.
1. Typ in **instantiedetails** *myHost* voor de **naam** en selecteer *Oost-VS* voor de locatie.
1. Selecteer **in hardwareprofiel** *standaard Es3-familie - Type 1* voor de familie **Grootte**, selecteer *myHostGroup* voor de **hostgroep** en selecteer vervolgens *1* voor het **foutdomein**. Laat de standaardwaarden voor de rest van de velden.
1. Wanneer u klaar bent, selecteert u **Controleren + maken** en wachten op validatie.
1. Zodra u het **doorgegeven bericht Validatie ziet,** selecteert u **Maken** om de host te maken.


