---
title: Een probleem met een Azure-VM oplossen met geneste virtualisatie in azure | Microsoft Docs
description: Problemen met een Azure VM oplossen met geneste virtualisatie in azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: ad359a19cb42bf115189aca7905d1908d0dc5284
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087061"
---
# <a name="troubleshoot-a-problem-azure-vm-by-using-nested-virtualization-in-azure"></a>Een probleem met een Azure-VM oplossen met geneste virtualisatie in azure

In dit artikel wordt beschreven hoe u een geneste Virtualization Environment maakt in Microsoft Azure, zodat u de schijf van de probleem-VM kunt koppelen op de Hyper-V-host (herstel-VM) voor het oplossen van problemen.

## <a name="prerequisites"></a>Vereisten

Als u de probleem-VM wilt koppelen, moet de virtuele machine voor herstel voldoen aan de volgende vereisten:

-   De herstel-VM moet zich op dezelfde locatie bevindt als de probleem-VM.

-   De virtuele machine voor herstel moet zich in dezelfde resource groep bevindt als de probleem-VM.

-   De herstel-VM moet gebruikmaken van hetzelfde type opslag account (Standard of Premium) als de probleem-VM.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>Stap 1: Een herstel-VM maken en de Hyper-V-functie installeren

1.  Een nieuwe herstel-VM maken:

    -  Besturingssysteem: Windows Server 2016 Datacenter

    -  Grootte: Een v3-serie met ten minste twee kernen die ondersteuning bieden voor geneste virtualisatie. Zie [Inleiding tot de nieuwe Dv3-en EV3 VM-grootten](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)voor meer informatie.

    -  Dezelfde locatie, hetzelfde opslag account en dezelfde resource groep als de probleem-VM.

    -  Selecteer hetzelfde opslag type als de probleem-VM (Standard of Premium).

2.  Nadat de herstel-VM is gemaakt, wordt extern bureau blad naar de virtuele machine voor herstel.

3.  In Serverbeheer klikt u op **beheer** > **toevoegen rollen en onderdelen**.

4.  Selecteer in de sectie **type installatie** de optie installatie die op de **functie of het onderdeel is gebaseerd**.

5.  Controleer in de sectie de **doel server selecteren** of de virtuele machine voor herstel is geselecteerd.

6.  Selecteer de **Hyper-V-functie** > **onderdelen toevoegen**.

7.  Selecteer **volgende** in het gedeelte **functies** .

8.  Als er een virtuele switch beschikbaar is, selecteert u deze. Anders selecteert u **volgende**.

9.  Selecteer in de sectie **migratie** de optie **volgende**

10. Selecteer **volgende**in het gedeelte **standaard archieven** .

11. Schakel het selectie vakje in als u de server automatisch opnieuw wilt opstarten als dat nodig is.

12. Selecteer **Installeren**.

13. Hiermee staat u toe dat de server de Hyper-V-functie installeert. Dit duurt enkele minuten en de server wordt automatisch opnieuw opgestart.

## <a name="step-2-create-the-problem-vm-on-the-rescue-vms-hyper-v-server"></a>Stap 2: De probleem-VM maken op de Hyper-V-Server van de virtuele machine voor herstel

1.  Noteer de naam van de schijf in de probleem-VM en verwijder vervolgens de probleem-VM. Zorg ervoor dat u alle bijgevoegde schijven behoudt. 

2.  Koppel de besturingssysteem schijf van uw probleem-VM als een gegevens schijf van de virtuele machine voor herstel.

    1.  Nadat de probleem-VM is verwijderd, gaat u naar de virtuele machine voor herstel.

    2.  Selecteer **schijven**en voeg vervolgens **gegevens schijf toe**.

    3.  Selecteer de schijf van de virtuele machine van het probleem en selecteer vervolgens **Opslaan**.

3.  Nadat de schijf is gekoppeld, is het externe bureau blad naar de virtuele machine voor herstel.

4.  Open schijf beheer (diskmgmt. msc). Zorg ervoor dat de schijf van de probleem-VM is ingesteld op **offline**.

5.  Open Hyper-V-beheer: In **Serverbeheer**selecteert u de **Hyper-V-rol**. Klik met de rechter muisknop op de server en selecteer **Hyper-V-beheer**.

6.  Klik in Hyper-V-beheer met de rechter muisknop op de**virtuele** > machine voor herstel en selecteer vervolgens **nieuwe** > VM**volgende**.

7.  Typ een naam voor de virtuele machine en selecteer vervolgens **volgende**.

8.  Selecteer **generatie 1**.

9.  Stel het opstart geheugen in op 1024 MB of meer.

10. Als dit van toepassing is, selecteert u de Hyper-V-netwerk switch die is gemaakt. Ga anders naar de volgende pagina.

11. Selecteer **later een virtuele harde schijf koppelen**.

    ![de afbeelding over de optie later een virtuele harde schijf koppelen](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

12. Selecteer **volt ooien** wanneer de virtuele machine wordt gemaakt.

13. Klik met de rechter muisknop op de virtuele machine die u hebt gemaakt en selecteer **instellingen**.

14. Selecteer **IDE-controller 0**, selecteer **harde schijf**en klik vervolgens op **toevoegen**.

    ![de afbeelding over het toevoegen van nieuwe harde schijf](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

15. Selecteer bij **fysieke harde schijf**de schijf van de probleem-VM die u hebt gekoppeld aan de Azure VM. Als er geen schijven worden weer gegeven, controleert u of de schijf is ingesteld op offline met behulp van schijf beheer.

    ![de afbeelding over het koppelen van de schijf](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


17. Selecteer **Apply** en vervolgens **OK**.

18. Dubbel klik op de virtuele machine en start deze.

19. Nu kunt u op de virtuele machine werken als de on-premises VM. U kunt de stappen voor probleem oplossing volgen die u nodig hebt.

## <a name="step-3-re-create-your-azure-vm-in-azure"></a>Stap 3: Uw Azure-VM opnieuw maken in azure

1.  Nadat u de VM weer online hebt gezet, sluit u de virtuele machine in Hyper-V-beheer af.

2.  Ga naar de [Azure Portal](https://portal.azure.com) en selecteer de virtuele machine voor herstel > schijven, kopieer de naam van de schijf. U gebruikt de naam in de volgende stap. Ontkoppel de vaste schijf van de virtuele machine voor herstel.

3.  Ga naar **alle resources**, zoek naar de naam van de schijf en selecteer de schijf.

     ![de afbeelding over het zoeken naar de schijf](media/troubleshoot-vm-by-use-nested-virtualization/search-disk.png)     

4. Klik op **virtuele machine maken**.

     ![de afbeelding over het maken van een VM op basis van de schijf](media/troubleshoot-vm-by-use-nested-virtualization/create-vm-from-vhd.png) 

U kunt Azure PowerShell ook gebruiken om de virtuele machine te maken op basis van de schijf. Zie [de nieuwe virtuele machine maken op basis van een bestaande schijf met behulp van Power shell](../windows/create-vm-specialized.md#create-the-new-vm)voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen

Zie [problemen met RDP-verbindingen met een Azure VM oplossen](troubleshoot-rdp-connection.md)als u problemen ondervindt bij het maken van verbinding met uw virtuele machine. Zie problemen met [toepassings connectiviteit oplossen op een Windows-VM](troubleshoot-app-connection.md)voor problemen met het openen van toepassingen die op uw virtuele machine worden uitgevoerd.
