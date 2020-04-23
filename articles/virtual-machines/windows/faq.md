---
title: Veelgestelde vragen over Windows VM's in Azure
description: Biedt antwoorden op enkele van de veelgestelde vragen over virtuele Windows-machines die zijn gemaakt met het Resource Manager-model.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: 33d530181f4cc389f81ea83dc42b774ccfc8fd85
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869625"
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Veelgestelde vraag over Windows Virtual Machines
In dit artikel worden enkele veelgestelde vragen beantwoord over virtuele Windows-machines die in Azure zijn gemaakt met behulp van het implementatiemodel Resource Manager. Voor de Linux-versie van dit onderwerp, zie [Veelgestelde vraag over Linux Virtual Machines](../linux/faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="what-can-i-run-on-an-azure-vm"></a>Wat kan ik uitvoeren op een VM van Azure?
Alle abonnees kunnen serversoftware uitvoeren op een virtuele machine van Azure. Zie [Microsoft-serversoftwareondersteuning voor Azure Virtual Machines voor](https://support.microsoft.com/kb/2721672)informatie over het ondersteuningsbeleid voor het uitvoeren van Microsoft-serversoftware in Azure.

Bepaalde versies van Windows 7, Windows 8.1 en Windows 10 zijn beschikbaar voor MSDN Azure-voordeelabonnees en MSDN-Dev- en Test Pay-As-You-Go-abonnees voor ontwikkelings- en testtaken. Zie het Engelstalige blogbericht [Windows Client images for MSDN subscribers](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/) voor meer informatie, zoals instructies en beperkingen. 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Hoeveel opslagruimte kan ik gebruiken met een virtuele machine?
Elke gegevensschijf kan maximaal 32.767 GiB zijn. Het aantal gegevensschijven dat u kunt gebruiken, is afhankelijk van de grootte van de virtuele machine. Zie [Grootten voor virtuele machines](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) voor meer informatie.

Azure Managed Disks zijn de aanbevolen schijfopslagaanbiedingen voor gebruik met Azure Virtual Machines voor permanente opslag van gegevens. U meerdere beheerde schijven gebruiken bij elke virtuele machine. Beheerde schijven bieden twee soorten duurzame opslagopties: Premium en Standard Managed Disks. Zie [Prijzen voor beheerde schijven](https://azure.microsoft.com/pricing/details/managed-disks)voor prijsinformatie .

Azure-opslagaccounts kunnen ook opslag bieden voor de schijf van het besturingssysteem en alle gegevensschijven. Elke schijf is een VHD-bestand dat wordt opgeslagen als een pagina-blob. Zie [deze pagina](https://azure.microsoft.com/pricing/details/storage/) voor prijsinformatie.

## <a name="how-can-i-access-my-virtual-machine"></a>Hoe krijg ik toegang tot mijn virtuele machine?
Een externe verbinding tot stand brengen met RdP (Remote Desktop Connection) voor een Windows-vm. Zie Verbinding [maken en aanmelden bij een virtuele Azure-machine met Windows voor](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)instructies. Er worden maximaal twee gelijktijdige verbindingen ondersteund, tenzij de server is geconfigureerd als sessiehost voor Extern bureaublad-services.  

Zie Problemen [met Extern bureaublad-verbindingen met een Windows-gebaseerde Azure Virtual Machine](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)als u problemen ondervindt met Extern bureaublad. 

Als u bekend bent met Hyper-V, bent u mogelijk op zoek naar een tool die vergelijkbaar is met VMConnect. Azure biedt geen vergelijkbaar hulpmiddel omdat consoletoegang tot een virtuele machine niet wordt ondersteund.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Kan ik de tijdelijke schijf (de D: drive standaard) gebruiken om gegevens op te slaan?
Gebruik de tijdelijke schijf niet om gegevens op te slaan. Het is slechts tijdelijke opslag, dus je zou het risico lopen gegevens te verliezen die niet kunnen worden hersteld. Gegevensverlies kan optreden wanneer de virtuele machine naar een andere host wordt verplaatst. Hiervoor zijn verschillende redenen te bedenken, zoals het aanpassen van de grootte van een virtuele machine, het bijwerken van de host of een hardwarefout op de host.

Als u een toepassing hebt die de d: drive-letter moet gebruiken, u de stationsletters opnieuw toewijzen, zodat de tijdelijke schijf iets anders dan D gebruikt:. Zie [Use the D: drive as a data drive on a Windows VM](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (Het station D: als gegevensstation gebruiken op een Windows-VM) voor instructies.


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Hoe wijzig ik de stationsletter van de tijdelijke schijf?
U de stationsletter wijzigen door het paginabestand te verplaatsen en stationsletters opnieuw toe te voegen, maar u moet ervoor zorgen dat u de stappen in een specifieke volgorde doet. Zie [Use the D: drive as a data drive on a Windows VM](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (Het station D: als gegevensstation gebruiken op een Windows-VM) voor instructies.

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Kan ik een bestaande vm toevoegen aan een beschikbaarheidsset?
Nee. Als u wilt dat uw vm deel uitmaakt van een beschikbaarheidsset, moet u de VM binnen de set maken. Er is momenteel geen manier om een VM toe te voegen aan een beschikbaarheidsset nadat deze is gemaakt.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Kan ik een virtuele machine uploaden naar Azure?
Ja. Zie [On-premises VM's migreren naar Azure voor](on-prem-to-azure.md)instructies.

## <a name="can-i-resize-the-os-disk"></a>Kan ik het formaat van de osschijf wijzigen?
Ja. Zie Het [station van het besturingssysteem van een virtuele machine in een Azure Resource Group uitbreiden](expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor instructies.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Kan ik een bestaande Azure VM kopiëren of klonen?
Ja. Met beheerde afbeeldingen u een afbeelding van een virtuele machine maken en vervolgens de afbeelding gebruiken om meerdere nieuwe VM's te bouwen. Zie Een [aangepaste afbeelding van een virtuele machine maken](tutorial-custom-images.md)voor instructies.

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Waarom zie ik de regio's Canada Central en Canada East niet via Azure Resource Manager?

De twee nieuwe regio's Canada Centraal en Canada East worden niet automatisch geregistreerd voor het maken van virtuele machines voor bestaande Azure-abonnementen. Deze registratie gebeurt automatisch wanneer een virtuele machine via de Azure-portal naar een andere regio wordt geïmplementeerd met Azure Resource Manager. Nadat een virtuele machine is geïmplementeerd in een andere Azure-regio, moeten de nieuwe regio's beschikbaar zijn voor volgende virtuele machines.

## <a name="does-azure-support-linux-vms"></a>Ondersteunt Azure Linux VM's?
Ja. Zie [Een Linux-vm maken op Azure met behulp van de Portal](../linux/quick-create-portal.md)om snel een Linux-vm te maken om uit te proberen.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Kan ik een NIC aan mijn vm toevoegen nadat deze is gemaakt?
Ja, dit is nu mogelijk. De VM eerst moet worden gestopt deallocated. Vervolgens u een NIC toevoegen of verwijderen (tenzij dit de laatste NIC op de VM is). 

## <a name="are-there-any-computer-name-requirements"></a>Zijn er vereisten voor computernamen?
Ja. De computernaam kan maximaal 15 tekens lang zijn. Zie [Regels en beperkingen voor naamgevingsconventies](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging) voor meer informatie over het benoemen van uw bronnen.

## <a name="are-there-any-resource-group-name-requirements"></a>Zijn er vereisten voor de naam van de resourcegroep?
Ja. De naam van de resourcegroep kan maximaal 90 tekens lang zijn. Zie [Regels en beperkingen voor naamgevingsconventies](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming) voor meer informatie over resourcegroepen.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Wat zijn de gebruikersnaamvereisten bij het maken van een vm?

Gebruikersnamen kunnen maximaal 20 tekens lang zijn en kunnen niet eindigen in een periode ("."). 

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


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Wat zijn de wachtwoordvereisten bij het maken van een vm?

Er zijn verschillende vereisten voor de wachtwoordlengte, afhankelijk van het hulpprogramma dat u gebruikt:
 - Portal - tussen 12 - 72 tekens
 - PowerShell - tussen 8 - 123 tekens
 - CLI - tussen 12 - 123

* Kleine letters bevatten
* Hoofdletters bevatten
* Een cijfer bevatten
* Een speciaal teken bevatten (overeenkomst met reguliere expressie [\W_])

De volgende wachtwoorden zijn niet toegestaan:

<table>
    <tr>
        <td>abc@123</td>
        <td>Iloveyou!</td>
        <td>P@$$w0e</td>
        <td>P@ssw0rd</td>
        <td>P@ssword123</td>
    </tr>
    <tr>
        <td>Pa$$word</td>
        <td>pass@word1</td>
        <td>Wachtwoord!</td>
        <td>Wachtwoord1</td>
        <td>Wachtwoord22</td>
    </tr>
</table>