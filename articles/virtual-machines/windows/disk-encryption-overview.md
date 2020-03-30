---
title: Azure-schijfversleuteling inschakelen voor Windows VM's
description: In dit artikel vindt u instructies voor het inschakelen van Microsoft Azure Disk Encryption voor Windows VM's.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/05/2019
ms.custom: seodec18
ms.openlocfilehash: 05db717f5d3adc2429431503f588f2cc7f79aef6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266778"
---
# <a name="azure-disk-encryption-for-windows-vms"></a>Azure-schijfversleuteling voor Windows VM's 

Azure Disk Encryption helpt om uw gegevens te beschermen en te beveiligen, zodat u aan de beveiligings- en nalevingsafspraken van uw organisatie voldoet. Het maakt gebruik van de [Bitlocker-functie](https://en.wikipedia.org/wiki/BitLocker) van Windows om volumeversleuteling te bieden voor het besturingssysteem en gegevensschijven van Virtuele Azure-machines (VM's) en is geïntegreerd met [Azure Key Vault](../../key-vault/index.yml) om u te helpen de schijfversleutelingssleutels en -geheimen te beheren. 

Als u [Azure Security Center](../../security-center/index.yml)gebruikt, wordt u gewaarschuwd als u VM's hebt die niet zijn versleuteld. De waarschuwingen worden weergegeven als hoge ernst en de aanbeveling is om deze VM's te versleutelen.

![Waarschuwing voor azure security center-schijfversleuteling](../media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Als u azure-schijfversleuteling eerder met Azure AD hebt gebruikt om een vm te versleutelen, moet u deze optie blijven gebruiken om uw vm te versleutelen. Zie [Azure Disk Encryption with Azure AD (vorige release)](disk-encryption-overview-aad.md) voor meer informatie. 
> - Bepaalde aanbevelingen kunnen het gebruik van gegevens, netwerken of rekenbronnen verhogen, wat resulteert in extra licentie- of abonnementskosten. U moet een geldig actief Azure-abonnement hebben om resources in Azure in de ondersteunde regio's te maken.

U de grondbeginselen van Azure Disk Encryption voor Windows in slechts een paar minuten leren met de [Snellestart van Windows maken en versleutelen met Azure CLI](disk-encryption-cli-quickstart.md) of het maken en versleutelen van een [Windows-vm met Azure Powershell.](disk-encryption-powershell-quickstart.md)

## <a name="supported-vms-and-operating-systems"></a>Ondersteunde VM's en besturingssystemen

### <a name="supported-vm-sizes"></a>Ondersteunde VM-grootten

Windows VM's zijn beschikbaar in [verschillende formaten.](sizes-general.md) Azure Disk Encryption is niet beschikbaar op [Basic, A-serie VM's](https://azure.microsoft.com/pricing/details/virtual-machines/series/)of op virtuele machines met minder dan 2 GB geheugen.

Azure Disk Encryption is ook beschikbaar voor VM's met premium opslag.

### <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

- Windows-client: Windows 8 en hoger.
- Windows Server: Windows Server 2008 R2 en hoger.  
 
> [!NOTE]
> Voor Windows Server 2008 R2 moet de .NET Framework 4.5 worden geïnstalleerd voor versleuteling; installeer het vanuit Windows Update met de optionele update Microsoft .NET Framework 4.5.2 voor Windows Server 2008 R2 x64-systemen[(KB2901983).](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)  
>  
> Voor Windows Server 2012 R2 Core en Windows Server 2016 Core moet het bdehdcfg-onderdeel op de VM worden geïnstalleerd voor versleuteling.


## <a name="networking-requirements"></a>Netwerkvereisten
Als u Azure Disk Encryption wilt inschakelen, moeten de VM's voldoen aan de volgende vereisten voor netwerkeindpuntconfiguratie:
  - Als u een token wilt openen om verbinding te maken met uw sleutelkluis, \[\]moet de Windows-VM verbinding kunnen maken met een Azure Active Directory-eindpunt, login.microsoftonline.com .
  - Als u de versleutelingssleutels naar uw sleutelkluis wilt schrijven, moet de Windows-VM verbinding kunnen maken met het eindpunt van de sleutelkluis.
  - De Windows-VM moet verbinding kunnen maken met een eindpunt voor Azure-opslag dat de Azure-extensierepository host en een Azure-opslagaccount dat de VHD-bestanden host.
  -  Als uw beveiligingsbeleid de toegang van Azure VM's tot internet beperkt, u de voorgaande URI oplossen en een specifieke regel configureren om uitgaande connectiviteit met de IP's toe te staan. Zie [Azure Key Vault achter een firewall voor](../../key-vault/key-vault-access-behind-firewall.md)meer informatie.    


## <a name="group-policy-requirements"></a>Vereisten voor groepsbeleid

Azure Disk Encryption maakt gebruik van de BitLocker externe sleutelbeschermer voor Windows VM's. Voor domeinsamengevoegde VM's, niet duwen geen groepsbeleid dat TPM-beschermers af te dwingen. Zie [Naslaginformatie](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)over het groepsbeleid voor 'BitLocker toestaan zonder compatibele TPM' voor informatie over het groepsbeleid voor 'BitLocker toestaan zonder compatibele TPM'.

BitLocker-beleid voor domein samengevoegde virtuele machines met aangepast groepsbeleid moet de volgende instelling bevatten: [Gebruikersopslag configureren van BitLocker-herstelgegevens > 256-bits herstelsleutel toestaan](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption mislukt wanneer aangepaste groepsbeleidsinstellingen voor BitLocker niet compatibel zijn. Op machines die niet over de juiste beleidsinstelling beschikten, past u het nieuwe beleid toe, dwingt u het nieuwe beleid bij te werken (gpupdate.exe /force) en kan het opnieuw opstarten nodig zijn.

Azure Disk Encryption mislukt als het groepsbeleid op domeinniveau het AES-CBC-algoritme blokkeert, dat wordt gebruikt door BitLocker.

## <a name="encryption-key-storage-requirements"></a>Vereisten voor opslag van versleutelingssleutels  

Azure Disk Encryption vereist een Azure Key Vault voor het beheren en beheren van schijfversleutelingssleutels en -geheimen. Uw sleutelkluis en VM's moeten zich in dezelfde Azure-regio en hetzelfde azure-abonnement bevinden.

Zie Een [sleutelkluis voor Azure Disk Encryption maken en configureren voor](disk-encryption-key-vault.md)meer informatie.

## <a name="terminology"></a>Terminologie
In de volgende tabel worden enkele van de algemene termen gedefinieerd die worden gebruikt in azure-schijfversleutelingsdocumentatie:

| Terminologie | Definitie |
| --- | --- |
| Azure Key Vault | Key Vault is een cryptografische, belangrijke beheerservice die is gebaseerd op gevalideerde hardwarebeveiligingsmodules (Federal Information Processing Standards). Deze standaarden helpen om uw cryptografische sleutels en gevoelige geheimen te beschermen. Zie voor meer informatie de [Azure Key Vault-documentatie](https://azure.microsoft.com/services/key-vault/) en [Het maken en configureren van een sleutelkluis voor Azure Disk Encryption](disk-encryption-key-vault.md). |
| Azure-CLI | [De Azure CLI](/cli/azure/install-azure-cli) is geoptimaliseerd voor het beheren en beheren van Azure-resources vanaf de opdrachtregel.|
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) is een door de industrie erkende Windows-volumeversleutelingstechnologie die wordt gebruikt om schijfversleuteling op Windows VM's in te schakelen. |
| Sleutelversleutelingssleutel (KEK) | De asymmetrische toets (RSA 2048) die u gebruiken om het geheim te beschermen of om te wikkelen. U een met een hardwarebeveiligingsmodule (HSM)-beveiligde sleutel of softwarebeveiligde sleutel leveren. Zie voor meer informatie de [Azure Key Vault-documentatie](https://azure.microsoft.com/services/key-vault/) en [Het maken en configureren van een sleutelkluis voor Azure Disk Encryption](disk-encryption-key-vault.md). |
| PowerShell-cmdlets | Zie [Azure PowerShell-cmdlets voor](/powershell/azure/overview)meer informatie . |


## <a name="next-steps"></a>Volgende stappen

- [Snelstart - Een Windows-vm maken en versleutelen met Azure CLI](disk-encryption-cli-quickstart.md)
- [Snelstart - Een Windows-vm maken en versleutelen met Azure Powershell](disk-encryption-powershell-quickstart.md)
- [Azure Disk Encryption-scenario's voor Windows-VM's](disk-encryption-windows.md)
- [Azure Disk Encryption-vereisten voor CLI-script](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption-vereisten Voor PowerShell-script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Een sleutelkluis voor Azure Disk Encryption maken en configureren](disk-encryption-key-vault.md)


