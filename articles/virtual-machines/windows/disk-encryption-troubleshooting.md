---
title: Handleiding voor het oplossen van problemen met Azure Disk Encryption
description: In dit artikel vindt u tips voor probleemoplossing voor Microsoft Azure Disk Encryption voor Windows VM's.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 0d4e76f4d02b0287770243bfddf995a19f90d232
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73749445"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Handleiding voor het oplossen van problemen met Azure Disk Encryption

Deze handleiding is voor IT-professionals, informatiebeveiligingsanalisten en cloudbeheerders waarvan de organisaties Azure Disk Encryption gebruiken. Dit artikel is om te helpen bij het oplossen van problemen met schijfversleuteling.

Voordat u een van de onderstaande stappen uitvoert, moet u er eerst voor zorgen dat de VM's die u probeert te versleutelen tot de [ondersteunde VM-formaten en besturingssystemen](disk-encryption-overview.md#supported-vms-and-operating-systems)behoren en dat u aan alle vereisten hebt voldaan:

- [Netwerkvereisten](disk-encryption-overview.md#networking-requirements)
- [Vereisten voor groepsbeleid](disk-encryption-overview.md#group-policy-requirements)
- [Vereisten voor opslag van versleutelingssleutels](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Azure-schijfversleuteling achter een firewall oplossen

Wanneer de connectiviteit wordt beperkt door een firewall- of proxy-vereisten of NSG-instellingen (Network Security Group), kan de mogelijkheid van de extensie om benodigde taken uit te voeren worden verstoord. Deze onderbreking kan resulteren in statusberichten zoals 'Extensiestatus niet beschikbaar op de vm'. In verwachte scenario's kan de versleuteling niet worden voltooid. De secties die volgen hebben een aantal veelvoorkomende firewallproblemen die u mogelijk onderzoekt.

### <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen
Instellingen voor netwerkbeveiligingsgroepen die worden toegepast, moeten het eindpunt nog steeds toestaan om te voldoen aan de [vereiste vereisten](disk-encryption-overview.md#networking-requirements) voor netwerkconfiguratie voor schijfversleuteling.

### <a name="azure-key-vault-behind-a-firewall"></a>Azure Key Vault achter een firewall

Wanneer versleuteling wordt ingeschakeld met [Azure AD-referenties,](disk-encryption-windows-aad.md#)moet de doel-vm connectiviteit toestaan voor zowel Azure Active Directory-eindpunten als Key Vault-eindpunten. Huidige Azure Active Directory-verificatieeindpunten worden onderhouden in secties 56 en 59 van de documentatie over [Office 365-URL's en IP-adresbereiken.](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) Key Vault-instructies worden gegeven in de documentatie over het [openen van Azure Key Vault achter een firewall.](../../key-vault/key-vault-access-behind-firewall.md)

### <a name="azure-instance-metadata-service"></a>Azure-instantiemetagegevensservice 
De VM moet toegang hebben tot het eindpunt van de [Azure Instance Metadata-service](../windows/instance-metadata-service.md) dat gebruikmaakt van een bekend niet-routable IP-adres (`169.254.169.254`) dat alleen toegankelijk is vanuit de VM.  Proxyconfiguraties die lokaal HTTP-verkeer naar dit adres wijzigen (bijvoorbeeld het toevoegen van een X-Forwarded-For-header) worden niet ondersteund.

## <a name="troubleshooting-windows-server-2016-server-core"></a>Problemen met Windows Server 2016 Server Core oplossen

Op Windows Server 2016 Server Core is de bdehdcfg-component standaard niet beschikbaar. Dit onderdeel is vereist door Azure Disk Encryption. Het wordt gebruikt om het systeemvolume te splitsen van het OS-volume, wat slechts één keer wordt gedaan voor de levensduur van de VM. Deze binaire bestanden zijn niet vereist tijdens latere versleutelingsbewerkingen.

Als u dit probleem wilt oplossen, kopieert u de volgende vier bestanden van een VM voor het Windows Server 2016-datacenter naar dezelfde locatie in Server Core:

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

1. Voer de volgende opdracht in:

   ```
   bdehdcfg.exe -target default
   ```

1. Met deze opdracht wordt een systeempartitie van 550 MB aanmaken. Start het systeem opnieuw op.

1. Gebruik DiskPart om de volumes te controleren en ga vervolgens verder.  

Bijvoorbeeld:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```

## <a name="troubleshooting-encryption-status"></a>Versleutelingsstatus oplossen 

De portal kan een schijf als versleuteld weergeven, zelfs nadat deze is onversleuteld binnen de VM.  Dit kan gebeuren wanneer opdrachten op laag niveau worden gebruikt om de schijf rechtstreeks te ontsleutelen vanuit de VM, in plaats van de opdrachten voor azure-schijfversleuteling op een hoger niveau te gebruiken.  De hogere niveau opdrachten niet alleen ontsleutelen van de schijf vanuit de VM, maar buiten de VM ze ook updaten belangrijke platform niveau encryptie-instellingen en extensie-instellingen in verband met de VM.  Als deze niet op één lijn worden gehouden, kan het platform de versleutelingsstatus niet correct rapporteren of de VM inrichten.   

Als u Azure Disk Encryption wilt uitschakelen met PowerShell, gebruikt u [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) gevolgd door [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Als u Remove-AzVMDiskEncryptionExtension uitvoert voordat de versleuteling is uitgeschakeld, mislukt deze.

Als u Azure Disk Encryption met CLI wilt uitschakelen, schakelt [u az vm-versleuteling uit.](/cli/azure/vm/encryption) 

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u meer te weten komen over enkele veelvoorkomende problemen in Azure Disk Encryption en hoe u deze problemen oplossen. Zie de volgende artikelen voor meer informatie over deze service en de bijbehorende mogelijkheden:

- [Schijfversleuteling toepassen in Azure Security Center](../../security-center/security-center-apply-disk-encryption.md)
- [Azure-gegevensversleuteling in rust](../../security/fundamentals/encryption-atrest.md)
