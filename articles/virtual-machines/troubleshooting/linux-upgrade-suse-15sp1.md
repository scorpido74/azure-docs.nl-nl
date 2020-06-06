---
title: Een Azure-VM upgraden met SUSE Linux Enterprise Server naar SUSE 15 SP1 | Microsoft Docs
description: In dit artikel worden algemene stappen beschreven voor het gebruik van SUSE-distributie migratie systeem voor het upgraden van SUSE Linux Enter prise server naar SUSE 15 SP1 voor een virtuele machine van Azure.
services: virtual-machines-linux
documentationcenter: ''
author: amkarmak
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 05/21/2020
ms.author: arremana
ms.openlocfilehash: 079574ab4c5846eae6266d1e13240baf99428a69
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84449240"
---
# <a name="upgrade-azure-vm-with-sles-12-to-sles-15-sp1"></a>Een upgrade uitvoeren voor een Azure-VM met SLES 12 naar SLES 15 SP1

Dit artikel bevat algemene stappen voor het uitvoeren van een upgrade van SUSE Linux Enter prise server (SLES) 12 naar SLES 15 SP1 voor een virtuele Azure-machine (VM). Zie voor meer informatie [het hulp programma SuSE-distributie migratie systeem](https://documentation.suse.com/suse-distribution-migration-system/1.0/single-html/distribution-migration-system/index.html) en [SuSE Linux Enterprise server upgrade handleiding voor 15 SP1](https://documentation.suse.com/sles/15-SP1/single-html/SLES-upgrade/index.html#sec-update-preparation-update)gebruiken.

## <a name="supported-upgrade-paths"></a>Ondersteunde upgradepaden
De huidige SLES-versie moet SLES 12 SP4 of 12 SP5 zijn voordat u kunt door gaan met SLES 15 SP1.

![De scherm afbeelding over een ondersteund upgradepad](./media/linux-upgrade-suse-15sp1/upgrade-path.png)

## <a name="prerequisites"></a>Vereisten

- Plan de migratie activiteit volgens het venster goedgekeurde downtime. Dit komt doordat de VM opnieuw wordt opgestart tijdens de migratie.
- Maak vóór de migratie activiteit een volledige back-up van de virtuele machine.
- Als er geen back-up is geconfigureerd, maakt u een moment opname van de besturingssysteem schijf.
- [Controleer of de VM generatie v1 of generatie v2 is](#check-the-generation-version-for-a-vm).

## <a name="upgrade-from-suse-12-sp4-or-sp5-to-suse-15-sp1"></a>Upgrade van SUSE 12 SP4 of SP5 naar SUSE 15 SP1

1. Installeer het nieuwste pakket voor de VM:

    ```
    zypper clean --all
    zypper refresh
    zypper update
    ```

2. Nadat de installatie is voltooid, start u de VM opnieuw op.

3. Controleer de versie van de kernel en het besturings systeem. Zorg ervoor dat de versie SUSE 12 SP4 of SUSE 12 SP5 is.

    ```
    uname -a
    cat /etc/os-release
    ```

4. Installeer de **SuSE-Migration-sle15-activering**. Wanneer het pakket **SuSE-Migration-sle15-activering** wordt geïnstalleerd, wordt een ander pakket **SLES15-migratie** automatisch geïnstalleerd als een afhankelijkheids pakket. 

   ```
   zypper install suse-migration-sle15-activation
   ```

5. Nadat de installatie is voltooid, voert `reboot` u de opdracht uit om de VM opnieuw op te starten.

6. Ga naar de [Azure Portal](https://portal.azure.com), selecteer de virtuele machine en selecteer vervolgens **seriële console**. U ziet dat het systeem stopt bij ' opnieuw opstarten: systeem opnieuw opstarten '. Dit proces neemt ongeveer 15-45 minuten in beslag. Voor virtuele machines van de tweede generatie is het mogelijk vastgelopen in het scherm ' opnieuw opstarten: systeem opnieuw opstarten '. Wacht in dit geval 45 minuten. Als het nog steeds niet wordt uitgevoerd, gaat u naar de **overzichts** pagina van de virtuele machine in de Azure Portal, stopt u de virtuele machine en start u deze opnieuw.

     ![De scherm afbeelding over de berichten in de seriële console](./media/linux-upgrade-suse-15sp1/reboot-message.png)

8. Nadat het systeem opnieuw is opgestart met een nieuwe kernel, wordt het volgende bericht weer gegeven.

     ![De scherm afbeelding over de berichten in de seriële console](./media/linux-upgrade-suse-15sp1/output-message.png)
9. Controleer de versie van de kernel en het besturings systeem om te controleren of de upgrade van het systeem is geslaagd.

    ```
    uname -a
    cat /etc/os-release
    ```

## <a name="check-the-generation-version-for-a-vm"></a>De versie van de generatie voor een VM controleren

U kunt een van de volgende methoden gebruiken om de versie van de generatie te controleren:

- Voer in de SLES-Terminal de opdracht uit `dmidecode | grep -i hyper` . Als het een generatie v1 VM is, wordt er geen uitvoer geretourneerd. Voor de generaties v2 Vm's ziet u de volgende uitvoer:

     ![De scherm afbeelding over uitvoer voor virtuele machine van de 2e generatie](./media/linux-upgrade-suse-15sp1/output-gen2.png)
- Ga in het [Azure Portal](https://portal.azure.com)naar **Eigenschappen** van de virtuele machine en controleer het veld **VM-generatie** .
