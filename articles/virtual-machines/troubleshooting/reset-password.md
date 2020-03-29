---
title: Het lokale Linux-wachtwoord opnieuw instellen op Azure VM's | Microsoft Documenten
description: De stappen introduceren om het lokale Linux-wachtwoord opnieuw in te stellen op Azure VM
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71153576"
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Het lokale Linux-wachtwoord in Azure-VM's opnieuw instellen

Dit artikel introduceert verschillende methoden om lokale Linux Virtual Machine (VM) wachtwoorden te resetten. Als het gebruikersaccount is verlopen of als u gewoon een nieuw account wilt maken, u de volgende methoden gebruiken om een nieuw lokaal beheerdersaccount te maken en opnieuw toegang te krijgen tot de VM.

## <a name="symptoms"></a>Symptomen

U zich niet aanmelden bij de virtuele machine en u ontvangt een bericht dat aangeeft dat het wachtwoord dat u hebt gebruikt onjuist is. Bovendien u VMAgent niet gebruiken om uw wachtwoord opnieuw in te stellen op de Azure-portal.

## <a name="manual-password-reset-procedure"></a>Handmatige wachtwoordresetprocedure

> [!NOTE]
> De volgende stappen zijn niet van toepassing op de VM met onbeheerde schijf.

1. Maak een momentopname voor de OS-schijf van de betreffende VM, maak een schijf van de momentopname en voeg de schijf toe aan een probleemoplossingsvm. Zie [Problemen met een Windows-vm oplossen door de OS-schijf aan een herstel-vm te koppelen met behulp van de Azure-portal](troubleshoot-recovery-disks-portal-linux.md)voor meer informatie.

2. Maak verbinding met de VM voor het oplossen van problemen met Extern bureaublad.

3.  Voer de volgende SSH-opdracht uit op de VM voor het oplossen van problemen om een supergebruiker te worden.

    ```bash
    sudo su
    ```

4.  Voer **fdisk -l** uit of bekijk systeemlogboeken om de nieuw aangesloten schijf te vinden. Zoek de naam van het station om te monteren. Kijk vervolgens op de tijdelijke VM in het relevante logboekbestand.

    ```bash
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ```

    Het volgende is voorbeelduitvoer van de grepopdracht:

    ```bash
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ```

5.  Maak een bevestigingspunt genaamd **tempmount**.

    ```bash
    mkdir /tempmount
    ```

6.  Monteer de OS-schijf op het bevestigingspunt. Je moet meestal *sdc1* of *sdc2*monteren. Dit zal afhangen van de hosting partitie in */ etc* directory van de gebroken machine schijf.

    ```bash
    mount /dev/sdc1 /tempmount
    ```

7.  Maak kopieën van de kernreferentiebestanden voordat u wijzigingen aanbrengt:

    ```bash
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ```

8.  Het wachtwoord van de gebruiker opnieuw instellen dat u nodig hebt:

    ```bash
    passwd <<USER>> 
    ```

9.  Verplaats de gewijzigde bestanden naar de juiste locatie op de schijf van de kapotte machine.

    ```bash
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    ```

10. Ga terug naar de wortel en monteer de schijf.

    ```bash
    cd /
    umount /tempmount
    ```

11. Maak in Azure-portal de schijf los van de VM voor het oplossen van problemen.

12. [Wijzig de osschijf voor de betreffende VM](troubleshoot-recovery-disks-portal-linux.md#swap-the-os-disk-for-the-vm).

## <a name="next-steps"></a>Volgende stappen

* [Azure VM oplossen door osschijf aan een andere Azure VM te koppelen](https://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [Azure CLI: een VM verwijderen en opnieuw implementeren van VHD](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd/)
