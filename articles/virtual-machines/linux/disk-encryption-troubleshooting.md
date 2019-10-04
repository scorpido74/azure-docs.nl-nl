---
title: Probleem oplossing-Azure Disk Encryption voor Linux-Vm's
description: In dit artikel vindt u tips voor het oplossen van problemen met Microsoft Azure schijf versleuteling voor Linux-Vm's.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 864c806f146d7da4e45cc2b58159e5cad23364f8
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828719"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Probleemoplossingsgids voor Azure Disk Encryption

Deze handleiding is bedoeld voor IT-specialisten, informatiebeveiligingsanalisten en cloudbeheerders die willen Azure Disk Encryption gebruiken. Dit artikel is om te helpen bij het oplossen van schijf-versleuteling-gerelateerde problemen.

Voordat u een van de volgende stappen uitvoert, moet u eerst controleren of de virtuele machines die u probeert te versleutelen, worden ondersteund door de [ondersteunde VM-grootten en-besturings systemen](disk-encryption-overview.md#supported-vms-and-operating-systems)en dat u aan alle vereisten hebt voldaan:

- [Aanvullende vereisten voor Vm's](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Netwerk vereisten](disk-encryption-overview.md#networking-requirements)
- [Opslag vereisten voor de versleutelings sleutel](disk-encryption-overview.md#encryption-key-storage-requirements)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

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

Als u de versleutelings status wilt controleren, controleert u het veld **ProgressMessage** dat wordt geretourneerd door de opdracht [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . Terwijl de OS-schijf wordt versleuteld, wordt de virtuele machine wordt de status onderhoud ingeschakeld en schakelt SSH om te voorkomen dat een onderbreking van het continue proces. De **EncryptionInProgress** rapporten voor het merendeel van de tijd weergegeven terwijl de versleuteling uitgevoerd wordt. Enkele uren later een **VMRestartPending** bericht u vraagt om de virtuele machine opnieuw opstarten. Bijvoorbeeld:


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

Wanneer verbinding wordt beperkt door een firewall of proxy vereiste netwerkinstellingen security group (NSG), kan het vermogen van de extensie noodzakelijke taken worden onderbroken. Deze wordt onderbroken, kan resulteren in statusberichten, zoals "Status van extensie niet beschikbaar op de virtuele machine." In de verwachte scenario's, is de versleuteling niet kan worden voltooid. De volgende secties zijn enkele veelvoorkomende problemen voor de firewall die u mogelijk onderzoeken.

### <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen
Network security groepsinstellingen die worden toegepast moeten wel wilt toestaan dat het eindpunt om te voldoen aan de configuratie van de gedocumenteerde [vereisten](disk-encryption-overview.md#networking-requirements) voor schijfversleuteling.

### <a name="azure-key-vault-behind-a-firewall"></a>Azure Key Vault achter een firewall

Wanneer versleuteling wordt ingeschakeld met [Azure AD-referenties](disk-encryption-linux-aad.md#), moet de doel-VM verbinding kunnen maken met zowel Azure Active Directory-eind punten als Key Vault-eind punten. De huidige Azure Active Directory-verificatie-eind punten worden onderhouden in de secties 56 en 59 van de documentatie voor [Office 365-url's en IP-](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) adresbereiken. Key Vault instructies vindt u in de documentatie over het [verkrijgen van toegang tot Azure Key Vault achter een firewall](../../key-vault/key-vault-access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service 
De virtuele machine moet toegang hebben tot de [meta gegevens service](../windows/instance-metadata-service.md) -eind punt van Azure, die gebruikmaakt van een bekende niet-Routeer bare IP-adres (`169.254.169.254`) dat alleen vanuit de virtuele machine kan worden geopend.  Proxy configuraties waarmee lokaal HTTP-verkeer naar dit adres wordt gewijzigd (bijvoorbeeld het toevoegen van een X-doorgestuurd-for-header) worden niet ondersteund.

### <a name="linux-package-management-behind-a-firewall"></a>Linux-Pakketbeheer achter een firewall

Tijdens runtime, is Azure Disk Encryption voor Linux afhankelijk van de doel-distributie pakketbeheersysteem benodigde vereiste componenten installeren voordat het inschakelen van versleuteling. Als de firewall-instellingen te voorkomen de virtuele machine kunnen deze onderdelen te downloaden en installeren dat, worden klikt u vervolgens opeenvolgende fouten verwacht. De stappen voor het configureren van deze pakketbeheersysteem kunnen per distributie verschillen. Red Hat, wanneer er een proxy is vereist, moet u ervoor zorgen dat de abonnement-manager en yum zijn juist ingesteld. Zie voor meer informatie, [het oplossen van problemen met abonnement-manager en yum](https://access.redhat.com/solutions/189533).  

## <a name="troubleshooting-encryption-status"></a>Problemen met de versleutelings status oplossen 

De portal kan een schijf weer geven als versleuteld, zelfs nadat deze is ontsleuteld in de virtuele machine.  Dit kan gebeuren wanneer opdrachten op laag niveau worden gebruikt om de schijf rechtstreeks van de virtuele machine te ontsleutelen, in plaats van de Azure Disk Encryption beheer opdrachten op een hoger niveau te gebruiken.  Met de opdrachten op een hoger niveau wordt de schijf niet alleen ontsleuteld vanuit de virtuele machine, maar buiten de virtuele machine worden ook belang rijke versleutelings instellingen en extensie-instellingen van het platform bijgewerkt die zijn gekoppeld aan de virtuele machine.  Als deze niet in de uitlijning worden bewaard, kan het platform geen versleutelings status rapporteren of de virtuele machine op de juiste wijze inrichten.   

Als u Azure Disk Encryption wilt uitschakelen met Power shell, gebruikt u [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) gevolgd door [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Het uitvoeren van Remove-AzVMDiskEncryptionExtension voordat de versleuteling is uitgeschakeld, mislukt.

Gebruik [AZ VM Encryption Disable](/cli/azure/vm/encryption)om Azure Disk Encryption uit te scha KELEN met cli. 

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd informatie over enkele veelvoorkomende problemen in Azure Disk Encryption en hoe deze problemen op te lossen. Zie voor meer informatie over deze service en de mogelijkheden ervan, de volgende artikelen:

- [Schijfversleuteling in Azure Security Center toepassen](../../security-center/security-center-apply-disk-encryption.md)
- [Azure gegevensversleuteling in rust](../../security/fundamentals/encryption-atrest.md)
