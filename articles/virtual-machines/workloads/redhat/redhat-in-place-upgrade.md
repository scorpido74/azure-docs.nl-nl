---
title: In-place upgrade van Red Hat Enterprise Linux-installatie kopieën op Azure
description: Zoek stappen voor het uitvoeren van in-place upgrade van Red Hat Enter prise 7. x-installatie kopieën naar de meest recente versie van 8. x
author: mathapli
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 48884e6faa5f26f027c772b44d5f960979a40d1d
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94447725"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Red Hat Enterprise Linux in-place Upgrades

In dit artikel vindt u stapsgewijze instructies voor het uitvoeren van een in-place upgrade van Red Hat Enterprise Linux 7 tot Red Hat Enterprise Linux 8 met het hulp programma Leapp in Azure. Tijdens de in-place upgrade wordt het bestaande RHEL 7-besturings systeem vervangen door RHEL 8-versie.

>[!Note] 
> SQL Server op Red Hat Enterprise Linux aanbiedingen bieden geen ondersteuning voor in-place upgrade op Azure.

## <a name="what-to-expect-during-the-upgrade"></a>Wat u tijdens de upgrade kunt verwachten
Het systeem wordt een paar keer opnieuw opgestart tijdens de upgrade en dat is normaal. Bij de laatste keer opnieuw opstarten wordt de VM bijgewerkt naar RHEL 8 laatste secundaire release.

## <a name="preparations-for-the-upgrade"></a>Voor bereidingen voor de upgrade
In-place Upgrades zijn de officieel aanbevolen manier van Red Hat en Azure om klanten in staat te stellen uw systeem bij te werken naar de volgende primaire versie. Voordat u de upgrade uitvoert, moet u zich bewust zijn van de volgende zaken en rekening houden. 

>[!Important] 
> Maak een moment opname van de installatie kopie voordat u de upgrade uitvoert.

>[!NOTE]
> De opdrachten in dit artikel moeten worden uitgevoerd met behulp van het hoofd account

* Zorg ervoor dat u de nieuwste versie van RHEL 7 gebruikt, die momenteel RHEL 7,9 is. Als u een vergrendelde versie gebruikt en niet kunt upgraden naar RHEL 7,9, kunt u de [stappen hier gebruiken om over te scha kelen naar een niet-Eus-opslag plaats](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/redhat-rhui#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock).

* Voer de onderstaande opdracht uit om te weten hoe uw upgrade wordt uitgevoerd en als deze wordt voltooid. De opdracht moet een bestand genereren onder '/var/log/leapp/leapp-report.txt ' waarin het proces wordt uitgelegd en wat er gebeurt en als de upgrade mogelijk is of niet
    ```bash
    leapp preupgrade --no-rhsm
    ```

## <a name="steps-for-performing-the-upgrade"></a>Stappen voor het uitvoeren van de upgrade

Voer deze stappen zorgvuldig uit. U wordt geadviseerd om de upgrade op een test machine uit te proberen vóór productie-exemplaren.

1. Voer een yum-update uit om de meest recente client pakketten op te halen.
    ```bash
    yum update
    ```

1. Installeer het leapp-client-package.
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. Gebruik het bestand leapp-data. tar. gz met repomap.csv en pes-events.jsop, aanwezig in de [RedHat-Portal](https://access.redhat.com/articles/3664871)en pak deze uit. 
    1. Down load het bestand.
    1. Pak de inhoud uit en verwijder het bestand met de volgende opdracht:
    ```bash
     tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```
    


1. Voeg het bestand ' Answers ' voor ' Leapp ' toe.
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ```
    
1. PermitRootLogin in/etc/ssh/inschakelen sshd_config
    1. Open het bestand/etc/ssh/sshd_config
    1. Zoeken naar ' #PermitRootLogin ja '
    1. ' # ' Verwijderen uit Opmerking



1. Voer de upgrade ' Leapp ' uit.
    ```bash
    leapp upgrade --no-rhsm
    ```
1. De sshd-service opnieuw starten om de wijzigingen van kracht te laten worden
    ```bash
    systemctl restart sshd
    ```
1. PermitRootLogin in/etc/ssh/-sshd_config opnieuw opmerking plaatsen
    1. Open het bestand/etc/ssh/sshd_config
    1. Zoeken naar ' #PermitRootLogin ja '
    1. ' # ' Aan opmerking toevoegen

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de [Red Hat-afbeeldingen in azure](./redhat-images.md).
* Meer informatie over de [infra structuur voor Red Hat-updates](./redhat-rhui.md).
* Meer informatie over de [RHEL BYOS-aanbieding](./byos.md).
* Informatie over de Red Hat in-place upgrade-processen vindt u in de documentatie van Red Hat en [voert u een upgrade uit van RHEL 7 naar RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index)
* Informatie over Red Hat-ondersteunings beleid voor alle versies van RHEL vindt u op de pagina [levens cyclus van Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .