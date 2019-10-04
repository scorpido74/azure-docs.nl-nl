---
title: Azure Disk Encryption met App-vereisten voor Azure AD (vorige versie)
description: Dit artikel bevat vereisten voor het gebruik van Microsoft Azure-schijfversleuteling voor IaaS-VM's.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: b0e3e683b2c103bc7f9b6812115e2e7a5d871034
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828667"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure Disk Encryption met Azure AD (vorige versie)

**De nieuwe versie van Azure Disk Encryption wordt voorkomen dat de vereiste voor het ontwikkelen van een Azure AD-toepassing-parameter voor schijfversleuteling van VM inschakelen. Met de nieuwe versie kunt u niet langer moet Azure AD-referenties opgeven tijdens de stap van de versleuteling inschakelen. Alle nieuwe virtuele machines moeten worden versleuteld zonder de parameters van Azure AD-toepassing met behulp van de nieuwe versie. Zie [Azure Disk Encryption voor virtuele Linux-machines](disk-encryption-overview.md)voor informatie over het inschakelen van VM-schijf versleuteling met de nieuwe versie. Virtuele machines die al zijn versleuteld met Azure AD-toepassing parameters worden nog steeds ondersteund en worden onderhouden met de syntaxis van de AAD moeten blijven.**

In dit artikel vindt u een aanvulling op [Azure Disk Encryption voor Linux-vm's](disk-encryption-overview.md) met aanvullende vereisten en voor waarden voor Azure Disk Encryption met Azure AD (eerdere versie).

De informatie in deze secties blijft hetzelfde:

- [Ondersteunde Vm's en besturings systemen](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Aanvullende VM-vereisten](disk-encryption-overview.md#additional-vm-requirements)


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="networking-and-group-policy"></a>Netwerken en groepsbeleid

**Syntaxis van de parameter, de IaaS-VM's moet voldoen aan de volgende configuratievereisten voor netwerk-eindpunt voor de Azure Disk Encryption inschakelen met behulp van de oudere AAD:** 
  - Als u een token voor het verbinding maken met uw key vault, moet de IaaS-VM geen verbinding maken met een Azure Active Directory-eindpunt, zijn \[login.microsoftonline.com\].
  - Als de versleutelingssleutels wilt opslaan op uw key vault, moet de IaaS-VM geen verbinding maken met het eindpunt van de sleutelkluis.
  - De IaaS-VM moet geen verbinding maken met een Azure storage-eindpunt dat als host fungeert voor de Azure-extensie-opslagplaats en een Azure storage-account dat als host fungeert voor de VHD-bestanden.
  -  Als uw beveiligingsbeleid beperkt de toegang van Azure VM's tot het Internet, kunt u deze kunt oplossen door de voorgaande URI en configureren van een specifieke regel voor het toestaan van uitgaande verbinding met de IP-adressen. Zie voor meer informatie, [Azure Key Vault achter een firewall](../../key-vault/key-vault-access-behind-firewall.md).
  - Op Windows, als TLS 1.0 expliciet is uitgeschakeld en de .NET-versie niet naar 4.6 of hoger bijgewerkt is, kunnen de volgende wijziging in het register ADE de recentere TLS-versie selecteren:
    
        [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001

        [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001` 
     

**Groepsbeleid:**
 - De Azure Disk Encryption-oplossing maakt gebruik van de externe BitLocker-sleutelbeveiliging voor Windows IaaS-VM's. Voor virtuele machines van een domein, niet een Groepsbeleid TPM beveiligingstoepassingen afdwingen pushen. Zie voor meer informatie over het groepsbeleid voor "Toestaan BitLocker zonder een compatibele TPM" [BitLocker Group Policy Reference](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  Het BitLocker-beleid voor virtuele machines die lid zijn van een domein met aangepast groeps beleid moet de volgende instelling bevatten: [Gebruikers opslag configureren van BitLocker-herstel gegevens-> 256-bits herstel sleutel toestaan](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption mislukt wanneer aangepaste groeps beleids instellingen voor BitLocker incompatibel zijn. Het nieuwe beleid toepassen op virtuele machines die niet de juiste instelling hebben, en vervolgens opnieuw te starten is mogelijk vereist afdwingen dat het nieuwe beleid om bij te werken (gpupdate.exe/Force).  

## <a name="encryption-key-storage-requirements"></a>Opslag vereisten voor de versleutelings sleutel  

Voor Azure Disk Encryption is een Azure Key Vault vereist om sleutels en geheimen voor schijf versleuteling te beheren en te beheren. Uw sleutel kluis en Vm's moeten zich in dezelfde Azure-regio en hetzelfde abonnement bevinden.

Zie [een sleutel kluis maken en configureren voor Azure Disk Encryption met Azure AD (vorige versie)](disk-encryption-key-vault-aad.md)voor meer informatie.
 
## <a name="next-steps"></a>Volgende stappen

- [Een sleutel kluis maken en configureren voor Azure Disk Encryption met Azure AD (vorige versie)](disk-encryption-key-vault-aad.md)
- [Azure Disk Encryption met Azure AD inschakelen op virtuele Linux-machines (vorige versie)](disk-encryption-linux-aad.md)
- [SysteemAzure Disk Encryption vereisten CLI-script](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption van vereisten Power shell-script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)