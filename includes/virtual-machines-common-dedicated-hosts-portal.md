---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/09/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 77eb54f5d7194f3006ce463fc5f905165bdfc659
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75833938"
---
## <a name="limitations"></a>Beperkingen

- Virtuele-machine schaal sets worden momenteel niet ondersteund op toegewezen hosts.
- De eerste versie ondersteunt de volgende VM-reeksen: DSv3 en ESv3. 

## <a name="create-a-host-group"></a>Een hostgroep aanmaken

Een **hostgroep** is een nieuwe resource die een verzameling toegewezen hosts vertegenwoordigt. U maakt een hostgroep in een regio en een beschikbaarheids zone en voegt hierop hosts toe. Bij het plannen van hoge Beschik baarheid zijn er extra opties. U kunt een of beide van de volgende opties gebruiken met uw toegewezen hosts: 
- Beschik over meerdere beschikbaarheids zones. In dit geval moet u een hostgroep hebben in elk van de zones die u wilt gebruiken.
- Over meerdere fout domeinen die zijn toegewezen aan fysieke racks. 
 
In beide gevallen moet u het aantal fout domeinen voor uw hostgroep opgeven. Als u geen fout domeinen in uw groep wilt beslaan, gebruikt u het aantal fouten domein 1. 

U kunt er ook voor kiezen om zowel beschikbaarheids zones als fout domeinen te gebruiken. 

In dit voor beeld maken we een hostgroep met 1 beschikbaarheids zone en twee fout domeinen. 


1. Open Azure [Portal](https://portal.azure.com).
1. Selecteer in de linkerbovenhoek **een resource maken** .
1. Zoek naar **hostgroep** en selecteer **hostgroepen** in de resultaten.

    ![Zoek resultaat van hostgroepen.](./media/virtual-machines-common-dedicated-hosts-portal/host-group.png)
1. Selecteer op de pagina **hostgroepen** de optie **maken**.
1. Selecteer het abonnement dat u wilt gebruiken en selecteer vervolgens **Nieuw maken** om een nieuwe resource groep te maken.
1. Typ *myDedicatedHostsRG* als **naam** en selecteer **OK**.
1. Typ *myHostGroup*voor de naam van de **hostgroep**.
1. Selecteer voor **locatie**de optie **VS Oost**.
1. Selecteer voor de **beschikbaarheids zone**de optie **1**.
1. Selecteer **2**bij **aantal fout domeinen**.
1. Selecteer **controleren + maken** en wacht vervolgens op validatie.

    ![Instellingen van hostgroep](./media/virtual-machines-common-dedicated-hosts-portal/host-group-settings.png)
1. Zodra u het bericht **validatie is voltooid** ziet, selecteert u **maken** om de hostgroep te maken.

Het kan even duren voordat de hostgroep is gemaakt.

## <a name="create-a-dedicated-host"></a>Een toegewezen host maken

Maak nu een toegewezen host in de hostgroep. Naast een naam voor de host, moet u de SKU voor de host opgeven. Host SKU legt de ondersteunde VM-serie en de generatie van de hardware voor uw specifieke host vast. De volgende SKU-waarden voor de host worden ondersteund: DSv3_Type1 en ESv3_Type1.

Zie voor meer informatie over de Sku's en prijzen van de host de [Azure dedicated host prijzen](https://aka.ms/ADHPricing).

Als u het aantal fout domeinen voor uw hostgroep instelt, wordt u gevraagd om het fout domein voor uw host op te geven.  

1. Selecteer in de linkerbovenhoek **een resource maken** .
1. Zoek naar een **specifieke host** en selecteer vervolgens **toegewezen hosts** uit de resultaten.

    ![Zoek resultaat van hostgroepen.](./media/virtual-machines-common-dedicated-hosts-portal/host.png)
1. Selecteer op de pagina **gereserveerde hosts** de optie **maken**.
1. Selecteer het abonnement dat u wilt gebruiken.
1. Selecteer *myDedicatedHostsRG* als de **resource groep**.
1. In **Details**van het exemplaar typt u *MyHost* voor de **naam** en selecteert u *VS-Oost* voor de locatie.
1. Selecteer in **hardwareprofiel** *standaard Es3 Family-type 1* voor de **grootte familie**, selecteer *myHostGrup* voor de **hostgroep** en selecteer vervolgens *1* voor het **fout domein**. Laat de standaard waarden voor de rest van de velden ongewijzigd.
1. Wanneer u klaar bent, selecteert u **controleren + maken** en wacht u op validatie.

    ![Host-instellingen](./media/virtual-machines-common-dedicated-hosts-portal/host-settings.png)
1. Zodra u het bericht **validatie is voltooid** ziet, selecteert u **maken** om de host te maken.


