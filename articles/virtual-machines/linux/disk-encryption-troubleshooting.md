---
title: Probleem oplossing-Azure Disk Encryption voor Linux-Vm's
description: In dit artikel vindt u tips voor het oplossen van problemen met Microsoft Azure schijf versleuteling voor Linux-Vm's.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: dd21b6520dc68a6f7faa5500054b2865556e3dfb
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/01/2020
ms.locfileid: "78205905"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Probleemoplossingsgids voor Azure Disk Encryption

Deze handleiding is bedoeld voor IT-specialisten, informatiebeveiligingsanalisten en cloudbeheerders die willen Azure Disk Encryption gebruiken. Dit artikel is om te helpen bij het oplossen van schijf-versleuteling-gerelateerde problemen.

Voordat u een van de volgende stappen uitvoert, moet u eerst controleren of de virtuele machines die u probeert te versleutelen, worden ondersteund door de [ondersteunde VM-grootten en-besturings systemen](disk-encryption-overview.md#supported-vms-and-operating-systems)en dat u aan alle vereisten hebt voldaan:

- [Aanvullende vereisten voor Vm's](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Netwerk vereisten](disk-encryption-overview.md#networking-requirements)
- [Opslag vereisten voor de versleutelings sleutel](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>Oplossen van problemen met schijfversleuteling van Linux-besturingssysteem

Schijfversleuteling voor Linux-besturingssysteem (OS) moet de besturingssysteemschijf voordat het wordt uitgevoerd via het versleutelingsproces volledige schijf ontkoppelen. Als de schijf, een foutbericht van deze kan niet ontkoppelen ' kan niet ontkoppelen na... ' waarschijnlijk optreedt.

Deze fout kan optreden wanneer versleuteling van de besturingssysteem schijf wordt uitgevoerd op een virtuele machine met een omgeving die is gewijzigd vanuit de ondersteunde installatie kopie van de Stock galerie. Afwijkingen van de installatiekopie van het ondersteunde kunnen leiden tot problemen met de mogelijkheid van de extensie ontkoppelen van de besturingssysteemschijf. Voorbeelden van afwijkingen kunnen de volgende items zijn:
- Aangepaste installatiekopieën is niet meer overeen met een ondersteund bestandssysteem of het partitieschema.
- Grote toepassingen zoals SAP, MongoDB, Apache Cassandra en Docker worden niet ondersteund wanneer ze geïnstalleerd en worden uitgevoerd in het besturingssysteem voor versleuteling zijn. Azure Disk Encryption is niet afgesloten deze processen veilig zoals vereist ter voorbereiding van de besturingssysteemschijf voor schijfversleuteling. Als er nog steeds actieve processen bestandsingangen openen om de besturingssysteemschijf te houden, is de besturingssysteemschijf kan niet worden ontkoppeld, wat resulteert in een fout opgetreden bij het versleutelen van de besturingssysteemschijf. 
- Aangepaste scripts die worden uitgevoerd in de buurt sluiten tijd aan de versleuteling wordt ingeschakeld, of als een andere wijzigingen worden aangebracht op de virtuele machine tijdens het versleutelingsproces. Dit conflict kan gebeuren als u een Azure Resource Manager-sjabloon definieert meerdere extensies voor het gelijktijdig uitvoeren of als een extensie voor aangepaste scripts of een andere actie wordt gelijktijdig naar schijfversleuteling. Tijdens het serialiseren en te isoleren van deze stappen kunnen los het probleem.
- Security Enhanced Linux (SELinux) nog niet zijn uitgeschakeld voordat u de versleuteling, is ingeschakeld, zodat de stap ontkoppelen is mislukt. SELinux kan worden ingeschakeld nadat de codering is voltooid.
- De besturingssysteemschijf maakt gebruik van een schema logische Volume Manager (LVM). Hoewel beperkte LVM gegevens schijfondersteuning beschikbaar is, is er een besturingssysteemschijf LVM niet.
- Aan de minimale geheugenvereisten niet worden voldaan (7 GB wordt aanbevolen voor versleuteling van de OS-schijf).
- Schijven worden recursief gekoppeld onder de map /mnt/ of elke andere (bijvoorbeeld /mnt/data1, /mnt/data2, /data3 + /data3/data4).

## <a name="update-the-default-kernel-for-ubuntu-1404-lts"></a>De standaard-kernel voor Ubuntu 14,04 LTS bijwerken

De Ubuntu 14,04 LTS-installatie kopie wordt geleverd met een standaard versie van de kernel van 4,4. Deze kernel-versie heeft een bekend probleem waarbij onvoldoende geheugen-Killer de opdracht DD tijdens het versleutelings proces van het besturings systeem niet correct beëindigt. Deze bug is opgelost in de meest recente Azure-afgestemde Linux-kernel. Als u deze fout wilt voor komen, moet u, voordat u versleuteling inschakelt voor de installatie kopie, bijwerken naar de door [Azure afgestemde kernel 4,15](https://packages.ubuntu.com/trusty/linux-azure) of later met de volgende opdrachten:

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

Nadat de VM opnieuw is opgestart in de nieuwe kernel, kan de nieuwe versie van de kernel worden bevestigd met:

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>De versie van de Azure virtual machine-agent en-extensie bijwerken

Azure Disk Encryption bewerkingen kunnen mislukken op installatie kopieën van virtuele machines met niet-ondersteunde versies van de virtuele machine agent van Azure. Linux-installatie kopieën met eerdere agent versies dan 2.2.38 moeten worden bijgewerkt voordat u versleuteling inschakelt. Zie [How to update the Azure Linux agent](../extensions/update-linux-agent.md) (Engelstalig) voor meer informatie over het bijwerken van een [virtuele machine en de minimale versie van de ondersteuning van](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)de VM-agents in Azure.

De juiste versie van de extensie micro soft. Azure. Security. AzureDiskEncryption of micro soft. Azure. Security. AzureDiskEncryptionForLinux Guest agent is ook vereist. Extensie versies worden automatisch onderhouden en bijgewerkt door het platform wanneer aan de vereisten van de Azure virtual machine-agent wordt voldaan en er een ondersteunde versie van de Virtual Machine agent wordt gebruikt.

De extensie micro soft. OSTCExtensions. AzureDiskEncryptionForLinux is afgeschaft en wordt niet meer ondersteund.  

## <a name="unable-to-encrypt-linux-disks"></a>Kan Linux-schijven niet versleutelen

In sommige gevallen kan is de Linux-schijfversleuteling lijkt te zijn vastgelopen bij 'OS-schijf versleuteling aan de slag'- en SSH uitgeschakeld. De versleuteling van 3-16 uur eindigen op een afbeelding voorraad kan duren. Als multi-terabyte-formaat gegevensschijven worden toegevoegd, kan dit proces kan dagen duren.

De volgorde van Linux-besturingssysteem schijf versleuteling losgekoppeld tijdelijk van de besturingssysteemschijf. Blok-voor-blokverificatie versleuteling van een schijf met het volledige besturingssysteem, wordt vervolgens uitgevoerd voordat deze het remounts in de versleutelde status. Linux-schijf versleuteling staat niet toe dat de virtuele machine gelijktijdig wordt gebruikt terwijl de versleuteling wordt uitgevoerd. De prestatiekenmerken van de virtuele machine kunnen maken van een aanzienlijk verschil zijn in de tijd die nodig is om versleuteling te voltooien. Deze kenmerken zijn de grootte van de schijf en of het opslagaccount is standard of premium (SSD)-opslag.

Als u de versleutelings status wilt controleren, controleert u het veld **ProgressMessage** dat wordt geretourneerd door de opdracht [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . Terwijl de OS-schijf wordt versleuteld, wordt de virtuele machine wordt de status onderhoud ingeschakeld en schakelt SSH om te voorkomen dat een onderbreking van het continue proces. Het **EncryptionInProgress** -bericht rapporteert voor het meren deel van de tijd terwijl de versleuteling wordt uitgevoerd. Enkele uren later, een **VMRestartPending** -bericht wordt u gevraagd de VM opnieuw op te starten. Bijvoorbeeld:


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

Nadat u wordt gevraagd naar de virtuele machine opnieuw opstarten en nadat de virtuele machine opnieuw is opgestart, moet u wachten 2-3 minuten voor het opnieuw opstarten en de laatste stappen voor het doel moet worden uitgevoerd. Het bericht gewijzigd wanneer de codering ten slotte is voltooid. Nadat dit bericht beschikbaar is, wordt verwacht dat het versleutelde station met besturingssysteem klaar voor gebruik en de virtuele machine is gereed om te worden gebruikt.

U wordt aangeraden dat u de virtuele machine terug naar de momentopname- of back-up direct vóór de versleuteling wilt herstellen in de volgende gevallen:
   - Als de takenreeks opnieuw opstarten, hierboven, niet het geval.
   - Als u de opstartgegevens, voortgangsbericht of andere foutindicatoren dat versleuteling-besturingssysteem rapporteren is in het midden van dit proces mislukt. Een voorbeeld van een bericht is de fout "het ontkoppelen is mislukt", die in deze handleiding wordt beschreven.

Evalueer de kenmerken van de virtuele machine en zorg ervoor dat aan alle vereisten wordt voldaan voordat u de volgende poging.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Oplossen van problemen met Azure Disk Encryption achter een firewall

Zie [schijf versleuteling op een geïsoleerd netwerk](disk-encryption-isolated-network.md)

## <a name="troubleshooting-encryption-status"></a>Problemen met de versleutelings status oplossen 

De portal kan een schijf weer geven als versleuteld, zelfs nadat deze is ontsleuteld in de virtuele machine.  Dit kan gebeuren wanneer opdrachten op laag niveau worden gebruikt om de schijf rechtstreeks van de virtuele machine te ontsleutelen, in plaats van de Azure Disk Encryption beheer opdrachten op een hoger niveau te gebruiken.  Met de opdrachten op een hoger niveau wordt de schijf niet alleen ontsleuteld vanuit de virtuele machine, maar buiten de virtuele machine worden ook belang rijke versleutelings instellingen en extensie-instellingen van het platform bijgewerkt die zijn gekoppeld aan de virtuele machine.  Als deze niet in de uitlijning worden bewaard, kan het platform geen versleutelings status rapporteren of de virtuele machine op de juiste wijze inrichten.   

Als u Azure Disk Encryption wilt uitschakelen met Power shell, gebruikt u [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) gevolgd door [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Het uitvoeren van Remove-AzVMDiskEncryptionExtension voordat de versleuteling is uitgeschakeld, mislukt.

Gebruik [AZ VM Encryption Disable](/cli/azure/vm/encryption)om Azure Disk Encryption uit te scha KELEN met cli. 

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd informatie over enkele veelvoorkomende problemen in Azure Disk Encryption en hoe deze problemen op te lossen. Zie voor meer informatie over deze service en de mogelijkheden ervan, de volgende artikelen:

- [Schijf versleuteling Toep assen in Azure Security Center](../../security-center/security-center-apply-disk-encryption.md)
- [Azure-gegevens versleuteling in rust](../../security/fundamentals/encryption-atrest.md)
