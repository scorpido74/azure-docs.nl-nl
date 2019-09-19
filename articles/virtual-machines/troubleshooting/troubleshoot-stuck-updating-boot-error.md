---
title: Het opstarten van de Azure-VM is vastgelopen op Windows Update | Microsoft Docs
description: Informatie over het oplossen van het probleem wanneer een Azure-VM wordt gestart op Windows Update.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: genli
ms.openlocfilehash: 226151d81319dc4e6f132e76ce2d310f88a484e8
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087026"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>Opstarten van Azure VM is vastgelopen op Windows Update

Dit artikel helpt u bij het oplossen van het probleem wanneer uw virtuele machine (VM) tijdens het opstarten vastloopt in de Windows Update fase. 

> [!NOTE] 
> Azure heeft twee verschillende implementatiemodellen voor het maken van en werken met resources: [Resource Manager en het klassieke model](../../azure-resource-manager/resource-manager-deployment-model.md). In dit artikel wordt beschreven hoe u het Resource Manager-implementatie model gebruikt. U wordt aangeraden dit model te gebruiken voor nieuwe implementaties in plaats van het klassieke implementatie model te gebruiken.

## <a name="symptom"></a>Symptoom

 Een Windows-VM start niet. Wanneer u de scherm opnamen in het venster [Diagnostische gegevens over opstarten](../troubleshooting/boot-diagnostics.md) inschakelt, ziet u dat het opstarten is vastgelopen in het update proces. Hier volgen enkele voor beelden van berichten die kunnen worden weer gegeven:

- Windows # #% wordt niet meer op uw PC geïnstalleerd. Dit duurt even voordat de PC meerdere keren opnieuw wordt opgestart
- Blijf op de hoogte van uw PC totdat dit is voltooid. Update # van #... installeren... 
- De wijzigingen die worden aangebracht, kunnen niet worden voltooid de computer niet uitschakelen
- Fout bij het configureren van wijzigingen in Windows-updates de computer niet uitschakelen
- Fout < fout code > het Toep assen van update bewerkingen # # # # van # # # # # (\Regist...)
- Onherstelbare fout < fout code > het Toep assen van update bewerkingen # # # # # # # # # # ($ $...)


## <a name="solution"></a>Oplossing

Het update proces kan enige tijd in beslag nemen, afhankelijk van het aantal updates dat wordt geïnstalleerd of teruggedraaid. De VM in deze status gedurende 8 uur verlaten. Als de VM na die periode nog steeds in deze status is, start u de VM opnieuw op via de Azure Portal en controleert u of deze normaal kan worden gestart. Als deze stap niet werkt, voert u de volgende oplossing uit.

### <a name="remove-the-update-that-causes-the-problem"></a>De update die het probleem veroorzaakt, verwijderen

1. Maak een moment opname van de besturingssysteem schijf van de betrokken VM als back-up. Zie voor meer informatie, [momentopname maken van een schijf](../windows/snapshot-copy-managed-disk.md). 
2. [De besturingssysteemschijf koppelen aan een virtuele machine voor herstel](troubleshoot-recovery-disks-portal-windows.md).
3. Zodra de besturingssysteem schijf is gekoppeld op de herstel-VM, voert u **diskmgmt. msc** uit om schijf beheer te openen en ervoor te zorgen dat de gekoppelde schijf **online**is. Noteer de stationsletter die is toegewezen aan de gekoppelde besturingssysteem schijf die de map \Windows heeft. Als de schijf is versleuteld, ontsleutelt u de schijf voordat u verdergaat met de volgende stappen in dit document.

4. Open een opdracht prompt exemplaar met verhoogde bevoegdheid (als administrator uitvoeren). Voer de volgende opdracht uit om de lijst op te halen van de update pakketten die zich op de gekoppelde besturingssysteem schijf bevinden:

        dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt

    Als de gekoppelde besturingssysteem schijf bijvoorbeeld station F is, voert u de volgende opdracht uit:

        dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
5. Open het C:\temp\Patch_level.txt-bestand en lees het van de onderkant. Zoek de update die in **behandeling** is of verwijder de status in **behandeling** .  Hier volgt een voor beeld van de update status:

     ```
    Package Identity : Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    State : Install Pending
    Release Type : Security Update
    Install Time :
    ```
6. Verwijder de update die het probleem heeft veroorzaakt:
    
    ```
    dism /Image:<Attached OS disk>:\ /Remove-Package /PackageName:<PACKAGE NAME TO DELETE>
    ```
    Voorbeeld: 

    ```
    dism /Image:F:\ /Remove-Package /PackageName:Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    ```

    > [!NOTE] 
    > Afhankelijk van de grootte van het pakket, neemt het hulp programma DISM even de tijd om de verwijdering te verwerken. Normaal gesp roken wordt het proces binnen 16 minuten voltooid.

7. [De OS-schijf loskoppelen en opnieuw maken van de virtuele machine](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk). Controleer vervolgens of het probleem is opgelost.
