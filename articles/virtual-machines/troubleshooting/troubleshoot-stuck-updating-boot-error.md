---
title: Azure VM opstarten zit vast bij Windows Update| Microsoft Documenten
description: Meer informatie over het oplossen van het probleem wanneer een Azure VM-opstart vastzit bij Windows-update.
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
ms.openlocfilehash: 8a47131cb4f19cce1664eafa50c67ab1a1171e67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919427"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>Azure VM opstarten zit vast bij Windows-update

In dit artikel u het probleem oplossen wanneer uw virtuele machine (VM) tijdens het opstarten vastzit in de windows-updatefase. 


## <a name="symptom"></a>Symptoom

 Een Windows-vm wordt niet gestart. Wanneer u de schermafbeeldingen in het venster [Diagnostische gegevens opstart](../troubleshooting/boot-diagnostics.md) controleert, ziet u dat het opstarten vastzit in het updateproces. Hieronder volgen voorbeelden van berichten die u mogelijk ontvangt:

- Windows ##% installeren Schakel uw pc niet uit. Dit zal een tijdje duren uw pc zal meerdere keren opnieuw opstarten
- Houd uw pc aan tot dit is gebeurd. Update # van #... installeren 
- We kunnen de updates niet voltooien Wijzigingen ongedaan maken Schakel uw computer niet uit
- Windows-updates terugdraaien niet uitschakelen Uw computer niet uitschakelen
- Fout< foutcode > het toepassen van updatebewerkingen ##### van ##### (\Regist...)
- Fatale fout < foutcode > het toepassen van updatebewerkingen ##### van #####($$...)


## <a name="solution"></a>Oplossing

Afhankelijk van het aantal updates dat wordt geïnstalleerd of opgerold, kan het updateproces een tijdje duren. Laat de VM in deze toestand gedurende 8 uur. Als de VM na die periode nog steeds in deze status staat, start u de VM opnieuw op de Azure-portal en kijkt u of deze normaal kan beginnen. Als deze stap niet werkt, probeert u de volgende oplossing.

### <a name="remove-the-update-that-causes-the-problem"></a>De update verwijderen die het probleem veroorzaakt

1. Maak een momentopname van de OS-schijf van de getroffen VM als back-up. Zie [Momentopname een schijf voor](../windows/snapshot-copy-managed-disk.md)meer informatie . 
2. [Koppel de OS-schijf aan een herstelvm](troubleshoot-recovery-disks-portal-windows.md).
3. Zodra de schijf van het besturingssysteem is aangesloten op de herstel-VM, voert u **diskmgmt.msc** uit om Schijfbeheer te openen en ervoor te zorgen dat de aangesloten schijf **online**is. Let op de stationsletter die is toegewezen aan de bijgevoegde OS-schijf met de map \windows. Als de schijf is versleuteld, decodeert u de schijf voordat u verdergaat met de volgende stappen in dit document.

4. Open een instantie met een opdrachtmeteen voor verhoogde opdracht (Uitvoeren als beheerder). Voer de volgende opdracht uit om de lijst met de updatepakketten op de aangesloten osschijf te krijgen:

        dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt

    Als de gekoppelde osschijf bijvoorbeeld station F is, voert u de volgende opdracht uit:

        dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
5. Open het C:\temp\Patch_level.txt-bestand en lees het van ondernaar boven. Zoek de update die zich in de status **Installeren in behandeling** of verwijderen in afwachting van de status **bevindt.**  Het volgende is een voorbeeld van de updatestatus:

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
    > Afhankelijk van de grootte van het pakket, zal de DISM tool een tijdje duren om de un-installatie te verwerken. Normaal gesproken is het proces binnen 16 minuten voltooid.

7. [Maak de OS-schijf los en maak de VM opnieuw](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk). Controleer vervolgens of het probleem is opgelost.
