---
title: Azure-schijfversleuteling met vereisten voor Azure AD-apps (vorige release)
description: In dit artikel vindt u voorwaarden voor het gebruik van Microsoft Azure Disk Encryption voor IaaS VM's.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: f38fd7c8e14f58052912f68a277f194fd3866f2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970586"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure-schijfversleuteling met Azure AD (vorige release)

De nieuwe versie van Azure Disk Encryption elimineert de vereiste voor het verstrekken van een Azure Active Directory (Azure AD) toepassingsparameter om VM-schijfversleuteling in te schakelen. Met de nieuwe release hoeft u geen Azure AD-referenties meer op te geven tijdens de stap versleuteling inschakelen. Alle nieuwe VM's moeten worden versleuteld zonder de Azure AD-toepassingsparameters met behulp van de nieuwe versie. Zie [Azure Disk Encryption for Linux VM's voor](disk-encryption-overview.md)instructies voor het inschakelen van VM-schijfversleuteling met behulp van de nieuwe versie. VM's die al zijn versleuteld met Azure AD-toepassingsparameters worden nog steeds ondersteund en moeten worden gehandhaafd met de syntaxis van AAD.

In dit artikel vindt u aanvullingen op [Azure Disk Encryption voor Linux VM's](disk-encryption-overview.md) met aanvullende vereisten en vereisten voor Azure Disk Encryption met Azure AD (vorige release).

De informatie in deze secties blijft hetzelfde:

- [Ondersteunde VM's en besturingssystemen](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Aanvullende VM-vereisten](disk-encryption-overview.md#additional-vm-requirements)


 

## <a name="networking-and-group-policy"></a>Netwerk- en groepsbeleid

Als u de functie Azure-schijfversleuteling wilt inschakelen met behulp van de oudere AAD-parametersyntaxis, moet de VM's voor infrastructuur als service (IaaS) voldoen aan de volgende vereisten voor netwerkeindpuntconfiguratie: 
  - Als u een token wilt krijgen om verbinding te maken met uw sleutelkluis, \[\]moet de IaaS-VM verbinding kunnen maken met een Azure AD-eindpunt, login.microsoftonline.com .
  - Om de versleutelingssleutels van uw sleutelkluis te schrijven, moet de IaaS VM verbinding kunnen maken met het eindpunt van de sleutelkluis.
  - De IaaS VM moet verbinding kunnen maken met een Azure-opslageindpunt dat de Azure-extensierepository host en een Azure-opslagaccount dat de VHD-bestanden host.
  -  Als uw beveiligingsbeleid de toegang van Azure VM's tot internet beperkt, u de voorgaande URI oplossen en een specifieke regel configureren om uitgaande connectiviteit met de IP's toe te staan. Zie [Azure Key Vault achter een firewall voor](../../key-vault/key-vault-access-behind-firewall.md)meer informatie.
  - Als TLS 1.0 in Windows expliciet is uitgeschakeld en de .NET-versie niet is bijgewerkt naar 4.6 of hoger, kan Azure Disk Encryption met de volgende registerwijziging de recentere TLS-versie selecteren:
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001` 
         
    
### <a name="group-policy"></a>Groepsbeleid
 - De Azure Disk Encryption-oplossing maakt gebruik van de BitLocker externe sleutelbeveiliging voor Windows IaaS VM's. Voor vm's die zijn verbonden met een domein, moet u geen groepsbeleid pushen dat TPM-beschermers afdwingt. Zie Verwijzing naar [BitLocker-groepsbeleid](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)voor informatie over het groepsbeleid voor de optie **BitLocker toestaan zonder compatibele TPM**.

- BitLocker-beleid voor virtuele machines met een domein met een aangepast groepsbeleid moet de volgende instelling bevatten: [Gebruikersopslag configureren van BitLocker-herstelgegevens > 256-bits herstelsleutel toestaan](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption mislukt wanneer aangepaste groepsbeleidsinstellingen voor BitLocker niet compatibel zijn. Op machines die niet de juiste beleidsinstelling hebben, past u het nieuwe beleid toe, dwingt u het nieuwe beleid bij te werken (gpupdate.exe /force) en start u opnieuw op als dit nodig is. 

## <a name="encryption-key-storage-requirements"></a>Vereisten voor opslag van versleutelingssleutels 

Azure Disk Encryption vereist Azure Key Vault voor het beheren en beheren van schijfversleutelingssleutels en -geheimen. Uw sleutelkluis en VM's moeten zich in dezelfde Azure-regio en hetzelfde azure-abonnement bevinden.

Zie [Een sleutelkluis voor Azure Disk Encryption maken en configureren met Azure AD (vorige release)](disk-encryption-key-vault-aad.md)voor meer informatie.
 
## <a name="next-steps"></a>Volgende stappen

- [Een sleutelkluis voor Azure-schijfversleuteling maken en configureren met Azure AD (vorige release)](disk-encryption-key-vault-aad.md)
- [Azure-schijfversleuteling inschakelen met Azure AD op Linux VM's (vorige release)](disk-encryption-linux-aad.md)
- [Azure Disk Encryption-vereisten voor CLI-script](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption-vereisten Voor PowerShell-script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)