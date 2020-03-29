---
title: Azure-schijfversleuteling voor Linux VM's oplossen
description: In dit artikel vindt u tips voor probleemoplossing voor Microsoft Azure Disk Encryption voor Linux VM's.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: eeacea9e3305865881747801100dc17770b7df63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970499"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Handleiding voor het oplossen van problemen met Azure Disk Encryption

Deze handleiding is voor IT-professionals, informatiebeveiligingsanalisten en cloudbeheerders waarvan de organisaties Azure Disk Encryption gebruiken. Dit artikel is om te helpen bij het oplossen van problemen met schijfversleuteling.

Voordat u een van de onderstaande stappen uitvoert, moet u er eerst voor zorgen dat de VM's die u probeert te versleutelen tot de [ondersteunde VM-formaten en besturingssystemen](disk-encryption-overview.md#supported-vms-and-operating-systems)behoren en dat u aan alle vereisten hebt voldaan:

- [Aanvullende vereisten voor VM's](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Netwerkvereisten](disk-encryption-overview.md#networking-requirements)
- [Vereisten voor opslag van versleutelingssleutels](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>Problemen met Linux OS-schijfversleuteling oplossen

Linux-besturingssysteem (OS) schijfversleuteling moet het os-station losmaken voordat het door het volledige schijfversleutelingsproces wordt uitgevoerd. Als het station niet kan worden losgemaakt, kan een foutmelding van "niet worden losgemaakt na ..." waarschijnlijk optreden.

Deze fout kan optreden wanneer os-schijfversleuteling wordt geprobeerd op een vm met een omgeving die is gewijzigd van de ondersteunde stockgalerijafbeelding. Afwijkingen van de ondersteunde afbeelding kunnen het vermogen van de extensie om het station van het besturingssysteem los te maken, verstoren. Voorbeelden van afwijkingen kunnen de volgende items zijn:
- Aangepaste afbeeldingen komen niet meer overeen met een ondersteund bestandssysteem of partitioneringsschema.
- Grote toepassingen zoals SAP, MongoDB, Apache Cassandra en Docker worden niet ondersteund wanneer ze zijn geïnstalleerd en worden uitgevoerd in het besturingssysteem voordat ze worden gesprocedeerd. Azure Disk Encryption kan deze processen niet veilig afsluiten, zoals vereist ter voorbereiding van het osstation voor schijfversleuteling. Als er nog steeds actieve processen zijn die open bestandsgrepen op het besturingssysteem weergeven, kan het station van het besturingssysteem niet worden ontkoppeld, waardoor het besturingssysteem niet wordt versleuteld. 
- Aangepaste scripts die in de nabijheid van de versleuteling worden uitgevoerd of als er andere wijzigingen op de VM worden aangebracht tijdens het versleutelingsproces. Dit conflict kan optreden wanneer een Azure Resource Manager-sjabloon meerdere extensies definieert die tegelijkertijd moeten worden uitgevoerd of wanneer een aangepaste scriptextensie of andere actie gelijktijdig wordt uitgevoerd op schijfversleuteling. Het serialiseren en isoleren van dergelijke stappen kan het probleem oplossen.
- Security Enhanced Linux (SELinux) is niet uitgeschakeld voordat encryptie wordt ingeschakeld, dus de uitdemontagestap mislukt. SELinux kan opnieuw worden ingeschakeld nadat versleuteling is voltooid.
- De OS-schijf maakt gebruik van een LVM-schema (Logical Volume Manager). Hoewel er beperkte ondersteuning voor LVM-gegevensschijf beschikbaar is, is een LVM OS-schijf dat niet.
- Aan de minimumgeheugenvereisten wordt niet voldaan (7 GB wordt voorgesteld voor os-schijfversleuteling).
- Gegevensstations worden recursief gemonteerd onder de /mnt/directory, of elkaar (bijvoorbeeld /mnt/data1, /mnt/data2, /data3 + /data3/data4).

## <a name="update-the-default-kernel-for-ubuntu-1404-lts"></a>De standaardkernel voor Ubuntu 14.04 LTS bijwerken

De Ubuntu 14.04 LTS afbeelding wordt geleverd met een standaard kernel versie van 4.4. Deze kernel versie heeft een bekend probleem waarbij Out of Memory Killer ten onrechte eindigt de dd commando tijdens het OS encryptie proces. Deze bug is opgelost in de meest recente Azure tuned Linux kernel. Update naar de [azure tuned kernel 4.15](https://packages.ubuntu.com/trusty/linux-azure) of hoger met de volgende opdrachten om deze fout te voorkomen voordat u versleuteling van de afbeelding inschakelt:

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

Nadat de VM opnieuw is gestart in de nieuwe kernel, kan de nieuwe kernelversie worden bevestigd met behulp van:

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>De Azure Virtual Machine Agent en extensieversies bijwerken

Azure Disk Encryption operations may fail on virtual machine images using unsupported versions of the Azure Virtual Machine Agent. Linux-afbeeldingen met agentversies die eerder dan 2.2.38 zijn, moeten worden bijgewerkt voordat versleuteling wordt inschakelt. Zie [De Azure Linux Agent bijwerken op een VM](../extensions/update-linux-agent.md) en Minimale [versieondersteuning voor virtuele machineagents in Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)voor meer informatie.

De juiste versie van de microsoft.azure.security.AzureDiskEncryption of Microsoft.Azure.Security.AzureDiskEncryptionForLinux guest agent extension is ook vereist. Extensieversies worden automatisch onderhouden en bijgewerkt door het platform wanneer aan de vereisten van de Azure Virtual Machine-agent is voldaan en een ondersteunde versie van de virtuele machineagent wordt gebruikt.

De Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux-extensie is afgeschaft en wordt niet meer ondersteund.  

## <a name="unable-to-encrypt-linux-disks"></a>Kan Linux-schijven niet versleutelen

In sommige gevallen lijkt de Linux-schijfversleuteling vast te zitten bij "OS-schijfversleuteling gestart" en is SSH uitgeschakeld. Het versleutelingsproces kan tussen 3-16 uur duren om te voltooien op een stock galerij afbeelding. Als gegevensschijven ter grootte van meerdere terabytes worden toegevoegd, kan het proces dagen duren.

De Linux OS-schijfversleutelingsreeks maakt de os-schijf tijdelijk los. Het voert vervolgens block-by-block encryptie van de gehele OS-schijf, voordat het opnieuw in zijn versleutelde staat. Linux Disk Encryption staat geen gelijktijdig gebruik van de VM toe terwijl de versleuteling aan de gang is. De prestatiekenmerken van de VM kunnen een significant verschil maken in de tijd die nodig is om versleuteling te voltooien. Deze kenmerken omvatten de grootte van de schijf en of het opslagaccount standaard of premium (SSD) opslag is.

Als u de versleutelingsstatus wilt controleren, peilt u het veld **ProgressMessage** dat is geretourneerd vanuit de opdracht [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) Terwijl de os-schijf wordt versleuteld, voert de VM een onderhoudsstatus in en schakelt ssh uit om onderbrekingen in het lopende proces te voorkomen. Het **Bericht EncryptionInProgress** rapporteert voor het grootste deel van de tijd terwijl de versleuteling aan de gang is. Enkele uren later wordt u door een **VMRestartPending-bericht** gevraagd de VM opnieuw te starten. Bijvoorbeeld:


```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

Nadat u wordt gevraagd om de VM opnieuw op te starten en nadat de VM opnieuw is opgestart, moet u 2-3 minuten wachten voordat de herstart en de laatste stappen op het doel worden uitgevoerd. Het statusbericht verandert wanneer de versleuteling eindelijk is voltooid. Nadat dit bericht beschikbaar is, is het versleutelde osstation naar verwachting klaar voor gebruik en is de VM klaar om opnieuw te worden gebruikt.

In de volgende gevallen raden we u aan de VM terug te zetten naar de momentopname of back-up die vlak voor versleuteling is gemaakt:
   - Als de eerder beschreven rebootreeks niet gebeurt.
   - Als de opstartgegevens, voortgangsberichten of andere foutindicatoren melden dat de versleuteling van het besturingssysteem is mislukt in het midden van dit proces. Een voorbeeld van een bericht is de fout 'kan niet worden losgemaakt' die in deze handleiding wordt beschreven.

Evalueer vóór de volgende poging de kenmerken van de VM opnieuw en zorg ervoor dat aan alle vereisten wordt voldaan.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Azure-schijfversleuteling achter een firewall oplossen

[Schijfversleuteling op een geïsoleerd netwerk bekijken](disk-encryption-isolated-network.md)

## <a name="troubleshooting-encryption-status"></a>Versleutelingsstatus oplossen 

De portal kan een schijf als versleuteld weergeven, zelfs nadat deze is onversleuteld binnen de VM.  Dit kan gebeuren wanneer opdrachten op laag niveau worden gebruikt om de schijf rechtstreeks te ontsleutelen vanuit de VM, in plaats van de opdrachten voor azure-schijfversleuteling op een hoger niveau te gebruiken.  De hogere niveau opdrachten niet alleen ontsleutelen van de schijf vanuit de VM, maar buiten de VM ze ook updaten belangrijke platform niveau encryptie-instellingen en extensie-instellingen in verband met de VM.  Als deze niet op één lijn worden gehouden, kan het platform de versleutelingsstatus niet correct rapporteren of de VM inrichten.   

Als u Azure Disk Encryption wilt uitschakelen met PowerShell, gebruikt u [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) gevolgd door [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Als u Remove-AzVMDiskEncryptionExtension uitvoert voordat de versleuteling is uitgeschakeld, mislukt deze.

Als u Azure Disk Encryption met CLI wilt uitschakelen, schakelt [u az vm-versleuteling uit.](/cli/azure/vm/encryption) 

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u meer te weten komen over enkele veelvoorkomende problemen in Azure Disk Encryption en hoe u deze problemen oplossen. Zie de volgende artikelen voor meer informatie over deze service en de bijbehorende mogelijkheden:

- [Schijfversleuteling toepassen in Azure Security Center](../../security-center/security-center-apply-disk-encryption.md)
- [Azure-gegevensversleuteling in rust](../../security/fundamentals/encryption-atrest.md)
