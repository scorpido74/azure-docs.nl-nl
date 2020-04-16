---
title: Azure-schijfversleuteling met Azure AD (vorige release)
description: In dit artikel vindt u voorwaarden voor het gebruik van Microsoft Azure Disk Encryption voor IaaS VM's.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: cb9d697c11427c7ebbf811f9cc05740347c74452
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417556"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure-schijfversleuteling met Azure AD (vorige release)

**De nieuwe release van Azure Disk Encryption elimineert de vereiste voor het verstrekken van een Azure AD-toepassingsparameter om VM-schijfversleuteling in te schakelen. Met de nieuwe release hoeft u geen Azure AD-referenties meer op te geven tijdens de stap versleuteling inschakelen. Alle nieuwe VM's moeten worden versleuteld zonder de Azure AD-toepassingsparameters met behulp van de nieuwe versie. Zie [Azure Disk Encryption for Windows VM](disk-encryption-overview.md)wilt u instructies weergeven om VM-schijfversleuteling in te schakelen met de nieuwe versie. VM's die al zijn versleuteld met Azure AD-toepassingsparameters worden nog steeds ondersteund en moeten worden gehandhaafd met de syntaxis van AAD.**

Dit artikel vult [Azure Disk Encryption voor Windows VM's aan](disk-encryption-overview.md) met aanvullende vereisten en vereisten voor Azure Disk Encryption met Azure AD (vorige release). De [sectie Ondersteunde VM's en besturingssystemen](disk-encryption-overview.md#supported-vms-and-operating-systems) blijft hetzelfde.

## <a name="networking-and-group-policy"></a>Netwerk- en groepsbeleid

**Als u de functie Azure-schijfversleuteling wilt inschakelen met de oudere aadparametersyntaxis, moeten de IaaS VM's voldoen aan de volgende vereisten voor netwerkeindpuntconfiguratie:** 
  - Als u een token wilt krijgen om verbinding te maken met uw sleutelkluis, \[moet\]de IaaS-vm verbinding kunnen maken met een Azure Active Directory-eindpunt, login.microsoftonline.com .
  - Om de versleutelingssleutels van uw sleutelkluis te schrijven, moet de IaaS VM verbinding kunnen maken met het eindpunt van de sleutelkluis.
  - De IaaS VM moet verbinding kunnen maken met een Azure-opslageindpunt dat de Azure-extensierepository host en een Azure-opslagaccount dat de VHD-bestanden host.
  -  Als uw beveiligingsbeleid de toegang van Azure VM's tot internet beperkt, u de voorgaande URI oplossen en een specifieke regel configureren om uitgaande connectiviteit met de IP's toe te staan. Zie [Azure Key Vault achter een firewall voor](../../key-vault/key-vault-access-behind-firewall.md)meer informatie.
  - De vm die moet worden versleuteld, moet zijn geconfigureerd om TLS 1.2 als standaardprotocol te gebruiken. Als TLS 1.0 expliciet is uitgeschakeld en de .NET-versie niet is bijgewerkt naar 4.6 of hoger, kan ADE met de volgende registerwijziging de recentere TLS-versie selecteren:
    
        [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001

        [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001` 
     

**Groepsbeleid:**
 - De Azure Disk Encryption-oplossing maakt gebruik van de BitLocker externe sleutelbeveiliging voor Windows IaaS VM's. Voor domeinsamengevoegde VM's, niet duwen geen groepsbeleid dat TPM-beschermers af te dwingen. Zie [Naslaginformatie](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)over het groepsbeleid voor 'BitLocker toestaan zonder compatibele TPM' voor informatie over het groepsbeleid voor 'BitLocker toestaan zonder compatibele TPM'.

-  BitLocker-beleid voor domein samengevoegde virtuele machines met aangepast groepsbeleid moet de volgende instelling bevatten: [Gebruikersopslag configureren van BitLocker-herstelgegevens > 256-bits herstelsleutel toestaan](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption mislukt wanneer aangepaste groepsbeleidsinstellingen voor BitLocker niet compatibel zijn. Op machines die niet over de juiste beleidsinstelling beschikten, past u het nieuwe beleid toe, dwingt u het nieuwe beleid bij te werken (gpupdate.exe /force) en kan het opnieuw opstarten nodig zijn.  

## <a name="encryption-key-storage-requirements"></a>Vereisten voor opslag van versleutelingssleutels  

Azure Disk Encryption vereist een Azure Key Vault voor het beheren en beheren van schijfversleutelingssleutels en -geheimen. Uw sleutelkluis en VM's moeten zich in dezelfde Azure-regio en hetzelfde azure-abonnement bevinden.

Zie Een [sleutelkluis voor Azure Disk Encryption maken en configureren met Azure AD (vorige release).](disk-encryption-key-vault-aad.md)
 
## <a name="next-steps"></a>Volgende stappen

- [Een sleutelkluis voor Azure-schijfversleuteling maken en configureren met Azure AD (vorige release)](disk-encryption-key-vault-aad.md)
- [Azure-schijfversleuteling inschakelen met Azure AD op Windows VM's (vorige release)](disk-encryption-windows-aad.md)
- [Azure Disk Encryption-vereisten voor CLI-script](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption-vereisten Voor PowerShell-script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
