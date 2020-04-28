---
title: Lokaal Linux-wacht woord opnieuw instellen op virtuele Azure-machines | Microsoft Docs
description: De stappen voor het opnieuw instellen van het lokale Linux-wacht woord op de Azure VM
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 08/20/2019
ms.author: delhan
ms.openlocfilehash: 83751538efe4f3d3af5928caa04b265b6c867442
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "71153576"
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Het lokale Linux-wachtwoord in Azure-VM's opnieuw instellen

In dit artikel worden verschillende methoden geïntroduceerd voor het opnieuw instellen van lokale virtuele Linux-machine wachtwoorden. Als het gebruikers account is verlopen of als u alleen een nieuw account wilt maken, kunt u de volgende methoden gebruiken om een nieuw lokaal beheerders account te maken en opnieuw toegang te krijgen tot de virtuele machine.

## <a name="symptoms"></a>Symptomen

U kunt zich niet aanmelden bij de virtuele machine en er wordt een bericht weer gegeven dat aangeeft dat het wacht woord dat u hebt gebruikt onjuist is. Daarnaast kunt u VMAgent niet gebruiken om uw wacht woord opnieuw in te stellen op de Azure Portal.

## <a name="manual-password-reset-procedure"></a>Procedure voor hand matig opnieuw instellen van wacht woorden

> [!NOTE]
> De volgende stappen zijn niet van toepassing op de virtuele machine met een niet-beheerde schijf.

1. Maak een moment opname van de besturingssysteem schijf van de betreffende virtuele machine, maakt een schijf van de moment opname en koppel de schijf vervolgens aan een virtuele machine voor probleem oplossing. Zie [problemen met een Windows-VM oplossen door de besturingssysteem schijf te koppelen aan een herstel-VM met behulp van de Azure Portal](troubleshoot-recovery-disks-portal-linux.md)voor meer informatie.

2. Maak verbinding met de virtuele machine voor probleem oplossing met behulp van Extern bureaublad.

3.  Voer de volgende SSH-opdracht uit op de virtuele machine voor probleem oplossing om een super gebruiker te worden.

    ```bash
    sudo su
    ```

4.  Voer **fdisk-l** uit of zoek naar systeem Logboeken om de zojuist gekoppelde schijf te vinden. Zoek de naam van het station dat u wilt koppelen. Zoek vervolgens in het relevante logboek bestand op de tijdelijke VM.

    ```bash
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ```

    Hier volgt een voor beeld van de uitvoer van de grep-opdracht:

    ```bash
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ```

5.  Maak een koppel punt met de naam **tempmount**.

    ```bash
    mkdir /tempmount
    ```

6.  Koppel de besturingssysteem schijf op het koppel punt. Normaal gesp roken moet u *sdc1* of *SDC2*koppelen. Dit is afhankelijk van de host-partitie in de map */etc* van de beschadigde machine schijf.

    ```bash
    mount /dev/sdc1 /tempmount
    ```

7.  Maak kopieën van de belangrijkste referentie bestanden voordat u wijzigingen aanbrengt:

    ```bash
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ```

8.  Stel het wacht woord van de gebruiker in dat u nodig hebt:

    ```bash
    passwd <<USER>> 
    ```

9.  Verplaats de gewijzigde bestanden naar de juiste locatie op de schijf van de beschadigde machine.

    ```bash
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    ```

10. Ga terug naar de hoofdmap en ontkoppel de schijf.

    ```bash
    cd /
    umount /tempmount
    ```

11. Ontkoppel de schijf in Azure Portal van de virtuele machine voor probleem oplossing.

12. [Wijzig de besturingssysteem schijf voor de betrokken VM](troubleshoot-recovery-disks-portal-linux.md#swap-the-os-disk-for-the-vm).

## <a name="next-steps"></a>Volgende stappen

* [Problemen met Azure VM oplossen door de besturingssysteem schijf aan een andere Azure-VM te koppelen](https://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [Azure CLI: een virtuele machine uit VHD verwijderen en opnieuw implementeren](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd/)
