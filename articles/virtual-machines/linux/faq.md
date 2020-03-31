---
title: Veelgestelde vragen voor Linux VM's in Azure
description: Biedt antwoorden op enkele van de veelgestelde vragen over virtuele Linux-machines die zijn gemaakt met het Resource Manager-model.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: 3c6a5e011a536cc9c34565d4f72a9bee6c6a5254
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945172"
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Veelgestelde vraag over Linux Virtual Machines
In dit artikel worden enkele veelgestelde vragen beantwoord over virtuele Linux-machines die in Azure zijn gemaakt met behulp van het implementatiemodel Resource Manager. Zie [Veelgestelde vraag over Virtuele Windows-machines voor](../windows/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) de Windows-versie van dit onderwerp

## <a name="what-can-i-run-on-an-azure-vm"></a>Wat kan ik uitvoeren op een VM van Azure?
Alle abonnees kunnen serversoftware uitvoeren op een virtuele machine van Azure. Zie [Linux op door Azure goedgekeurde distributies](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) voor meer informatie

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Hoeveel opslagruimte kan ik gebruiken met een virtuele machine?
Elke gegevensschijf kan maximaal 32.767 GiB zijn. Het aantal gegevensschijven dat u kunt gebruiken, is afhankelijk van de grootte van de virtuele machine. Zie [Grootten voor virtuele machines](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) voor meer informatie.

Azure Managed Disks zijn de aanbevolen schijfopslagaanbiedingen voor gebruik met Azure Virtual Machines voor permanente opslag van gegevens. U meerdere beheerde schijven gebruiken bij elke virtuele machine. Beheerde schijven bieden twee soorten duurzame opslagopties: Premium en Standard Managed Disks. Zie [Prijzen voor beheerde schijven](https://azure.microsoft.com/pricing/details/managed-disks)voor prijsinformatie .

Azure-opslagaccounts kunnen ook opslag bieden voor de schijf van het besturingssysteem en alle gegevensschijven. Elke schijf is een VHD-bestand dat wordt opgeslagen als een pagina-blob. Zie [deze pagina](https://azure.microsoft.com/pricing/details/storage/) voor prijsinformatie.

## <a name="how-can-i-access-my-virtual-machine"></a>Hoe krijg ik toegang tot mijn virtuele machine?
Stel een externe verbinding op om u aan te melden bij de virtuele machine met Behulp van Secure Shell (SSH). Bekijk de instructies voor het maken van verbinding [via Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) of van Linux [en Mac.](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) SSH biedt standaard ondersteuning voor maximaal tien gelijktijdige verbindingen. U kunt dit aantal verhogen door het configuratiebestand te bewerken.

Als u problemen ondervindt, raadpleegt u [Problemen met Secure Shell -verbindingen (SSH).](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Kan ik de tijdelijke schijf (/dev/sdb1) gebruiken om gegevens op te slaan?
Gebruik de tijdelijke schijf (/dev/sdb1) niet om gegevens op te slaan. Het is er alleen voor tijdelijke opslag. U loopt het risico gegevens te verliezen die niet kunnen worden hersteld.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Kan ik een bestaande Azure VM kopiëren of klonen?
Ja. Zie Een [kopie van een virtuele Linux-machine maken in het implementatiemodel Resource Manager](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)voor instructies.

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Waarom zie ik de regio's Canada Central en Canada East niet via Azure Resource Manager?
De twee nieuwe regio's Canada Centraal en Canada East worden niet automatisch geregistreerd voor het maken van virtuele machines voor bestaande Azure-abonnementen. Deze registratie gebeurt automatisch wanneer een virtuele machine via de Azure-portal naar een andere regio wordt geïmplementeerd met Azure Resource Manager. Nadat een virtuele machine is geïmplementeerd in een andere Azure-regio, moeten de nieuwe regio's beschikbaar zijn voor volgende virtuele machines.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Kan ik een NIC aan mijn vm toevoegen nadat deze is gemaakt?
Ja, dit is nu mogelijk. De VM eerst moet worden gestopt deallocated. Vervolgens u een NIC toevoegen of verwijderen (tenzij dit de laatste NIC op de VM is). 

## <a name="are-there-any-computer-name-requirements"></a>Zijn er vereisten voor computernamen?
Ja. De computernaam kan maximaal 64 tekens lang zijn. Zie [Regels en beperkingen voor naamgevingsconventies](/azure/architecture/best-practices/resource-naming) voor meer informatie over het benoemen van uw bronnen.

## <a name="are-there-any-resource-group-name-requirements"></a>Zijn er vereisten voor de naam van de resourcegroep?
Ja. De naam van de resourcegroep kan maximaal 90 tekens lang zijn. Zie [Regels en beperkingen voor naamgevingsconventies](/azure/architecture/best-practices/resource-naming) voor meer informatie over resourcegroepen.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Wat zijn de gebruikersnaamvereisten bij het maken van een vm?

Gebruikersnamen moeten 1 - 32 tekens lang zijn.

De volgende gebruikersnamen zijn niet toegestaan:

| | | | |
|-----------------|-----------|--------------------|----------|
| `administrator` | `admin`   | `user`             | `user1`  |
| `test`          | `user2`   | `test1`            | `user3`  |
| `admin1`        | `1`       | `123`              | `a`      |
| `actuser`       | `adm`     | `admin2`           | `aspnet` |
| `backup`        | `console` | `david`            | `guest`  |
| `john`          | `owner`   | `root`             | `server` |
| `sql`           | `support` | `support_388945a0` | `sys`    |
| `test2`         | `test3`   | `user4`            | `user5`  |
| `video`         |

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Wat zijn de wachtwoordvereisten bij het maken van een vm?

Er zijn verschillende vereisten voor de wachtwoordlengte, afhankelijk van het hulpprogramma dat u gebruikt:
 - Portal - tussen 12 - 72 tekens
 - PowerShell - tussen 8 - 123 tekens
 - CLI - tussen 12 - 123
 

Wachtwoorden moeten ook voldoen aan 3 van de volgende 4 complexiteitsvereisten:

* Kleine letters bevatten
* Hoofdletters bevatten
* Een cijfer bevatten
* Een speciaal teken bevatten (overeenkomst met reguliere expressie [\W_])

De volgende wachtwoorden zijn niet toegestaan:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0e</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Pa$$word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Wachtwoord!</td>
        <td style="text-align:center">Wachtwoord1</td>
        <td style="text-align:center">Wachtwoord22</td>
        <td style="text-align:center">Iloveyou!</td>
    </tr>
</table>