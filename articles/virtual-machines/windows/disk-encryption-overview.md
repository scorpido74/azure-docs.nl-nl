---
title: Azure Disk Encryption voor Windows-Vm's inschakelen
description: In dit artikel vindt u instructies voor het inschakelen van Microsoft Azure schijf versleuteling voor Windows-Vm's.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/05/2019
ms.custom: seodec18
ms.openlocfilehash: 05db717f5d3adc2429431503f588f2cc7f79aef6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266778"
---
# <a name="azure-disk-encryption-for-windows-vms"></a>Azure Disk Encryption voor Windows-Vm's 

Azure Disk Encryption helpt uw gegevens te beschermen en beschermen om te voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie. De [BitLocker](https://en.wikipedia.org/wiki/BitLocker) -functie van Windows wordt gebruikt voor volume versleuteling voor het besturings systeem en de gegevens schijven van virtuele Azure-machines (vm's), en is geïntegreerd met [Azure Key Vault](../../key-vault/index.yml) om u te helpen de coderings sleutels en geheimen van de schijf te beheren en te controleren. 

Als u [Azure Security Center](../../security-center/index.yml)gebruikt, wordt u gewaarschuwd als u virtuele machines hebt die niet zijn versleuteld. De waarschuwingen worden weergegeven als hoge urgentie en de aanbeveling is voor het versleutelen van deze VM's.

![Azure Security Center schijf versleuteling waarschuwing](../media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Als u eerder Azure Disk Encryption met Azure AD hebt gebruikt om een virtuele machine te versleutelen, moet u deze optie blijven gebruiken om uw virtuele machine te versleutelen. Zie [Azure Disk Encryption met Azure AD (vorige versie)](disk-encryption-overview-aad.md) voor meer informatie. 
> - Bepaalde aanbevelingen verhogen gegevens-, netwerk- of computerresources, wat resulteert in extra kosten in licentie of abonnement. U moet een geldige actief Azure-abonnement om resources te maken in Azure in de ondersteunde regio's hebben.

Meer informatie over de basis principes van Azure Disk Encryption voor Windows in slechts enkele minuten kunt u het beste een [Windows-VM maken en versleutelen met Azure cli Quick](disk-encryption-cli-quickstart.md) start of [een Windows-VM maken en versleutelen met Azure Power shell Quick](disk-encryption-powershell-quickstart.md)start.

## <a name="supported-vms-and-operating-systems"></a>Ondersteunde Vm's en besturings systemen

### <a name="supported-vm-sizes"></a>Ondersteunde VM-grootten

Virtuele Windows-machines zijn beschikbaar in [verschillende grootten](sizes-general.md). Azure Disk Encryption is niet beschikbaar op [de Basic-, a-Series vm's](https://azure.microsoft.com/pricing/details/virtual-machines/series/)of op virtuele machines met minder dan 2 GB geheugen.

Azure Disk Encryption is ook beschikbaar voor virtuele machines met Premium Storage.

### <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

- Windows-client: Windows 8 en hoger.
- Windows Server: Windows Server 2008 R2 of hoger.  
 
> [!NOTE]
> Voor Windows Server 2008 R2 moet .NET Framework 4,5 zijn geïnstalleerd voor versleuteling. Installeer het van Windows Update met de optionele update Microsoft .NET Framework 4.5.2 voor Windows Server 2008 R2 x64-systemen ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)).  
>  
> Windows Server 2012 R2 core en Windows Server 2016 core vereist dat het onderdeel bdehdcfg op de virtuele machine wordt geïnstalleerd voor versleuteling.


## <a name="networking-requirements"></a>Netwerk vereisten
Om Azure Disk Encryption in te scha kelen, moeten de virtuele machines voldoen aan de volgende vereisten voor netwerk eindpunt configuratie:
  - Als u een token wilt ontvangen om verbinding te maken met uw sleutel kluis, moet de Windows-VM verbinding kunnen maken met een Azure Active Directory-eind punt \[login.microsoftonline.com\].
  - Voor het schrijven van de versleutelings sleutels naar uw sleutel kluis moet de Windows-VM verbinding kunnen maken met het eind punt van de sleutel kluis.
  - De Windows-VM moet verbinding kunnen maken met een Azure Storage-eind punt dat als host fungeert voor de Azure extension-opslag plaats en een Azure-opslag account dat als host fungeert voor de VHD-bestanden.
  -  Als uw beveiligingsbeleid beperkt de toegang van Azure VM's tot het Internet, kunt u deze kunt oplossen door de voorgaande URI en configureren van een specifieke regel voor het toestaan van uitgaande verbinding met de IP-adressen. Zie [Azure Key Vault achter een firewall](../../key-vault/key-vault-access-behind-firewall.md)voor meer informatie.    


## <a name="group-policy-requirements"></a>groepsbeleid vereisten

Azure Disk Encryption gebruikt de externe BitLocker-sleutel beveiliging voor Windows-Vm's. Voor virtuele machines van een domein, niet een Groepsbeleid TPM beveiligingstoepassingen afdwingen pushen. Zie [referentie voor BitLocker-Groepsbeleid](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)voor informatie over het groeps beleid voor het toestaan van BitLocker zonder compatibele TPM.

Het BitLocker-beleid voor virtuele machines die lid zijn van een domein met aangepast groeps beleid moet de volgende instelling hebben: [gebruikers opslag configureren voor BitLocker-herstel gegevens-> herstel sleutel van 256-bits toestaan](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption mislukt wanneer aangepaste groeps beleids instellingen voor BitLocker incompatibel zijn. Het nieuwe beleid toepassen op virtuele machines die niet de juiste instelling hebben, en vervolgens opnieuw te starten is mogelijk vereist afdwingen dat het nieuwe beleid om bij te werken (gpupdate.exe/Force).

Azure Disk Encryption mislukt als groeps beleid op domein niveau het AES-CBC-algoritme blokkeert dat door BitLocker wordt gebruikt.

## <a name="encryption-key-storage-requirements"></a>Opslag vereisten voor de versleutelings sleutel  

Voor Azure Disk Encryption is een Azure Key Vault vereist om sleutels en geheimen voor schijf versleuteling te beheren en te beheren. Uw sleutel kluis en Vm's moeten zich in dezelfde Azure-regio en hetzelfde abonnement bevinden.

Zie [een sleutel kluis maken en configureren voor Azure Disk Encryption](disk-encryption-key-vault.md)voor meer informatie.

## <a name="terminology"></a>Terminologie
In de volgende tabel worden enkele algemene termen gedefinieerd die worden gebruikt in azure Disk Encryption Documentation:

| Terminologie | Definitie |
| --- | --- |
| Azure Key Vault | Key Vault is een cryptografische, key management-service die op Federal Information Processing Standards (FIPS) gevalideerde hardware security modules is gebaseerd. Deze standaarden helpen om uw cryptografische sleutels en gevoelige geheimen te beveiligen. Zie voor meer informatie de [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -documentatie en het [maken en configureren van een sleutel kluis voor Azure Disk Encryption](disk-encryption-key-vault.md). |
| Azure CLI | [De Azure cli](/cli/azure/install-azure-cli) is geoptimaliseerd voor het beheren en beheren van Azure-resources vanaf de opdracht regel.|
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) is een door de branche herkende Windows-coderings technologie die wordt gebruikt om schijf versleuteling in te scha kelen op Windows-vm's. |
| Sleutel versleutelings sleutel (KEK) | De asymmetrische sleutel (RSA 2048) die u kunt gebruiken om het geheim te beveiligen of in te pakken. U kunt opgeven dat een hardware security module (HSM)- of software beschermde sleutel beschermd. Zie voor meer informatie de [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -documentatie en het [maken en configureren van een sleutel kluis voor Azure Disk Encryption](disk-encryption-key-vault.md). |
| PowerShell-cmdlets | Zie [Azure PowerShell-cmdlets](/powershell/azure/overview)voor meer informatie. |


## <a name="next-steps"></a>Volgende stappen

- [Quick Start: een Windows-VM maken en versleutelen met Azure CLI](disk-encryption-cli-quickstart.md)
- [Quick Start: een Windows-VM maken en versleutelen met Azure Power shell](disk-encryption-powershell-quickstart.md)
- [Azure Disk Encryption-scenario's voor Windows-VM's](disk-encryption-windows.md)
- [SysteemAzure Disk Encryption vereisten CLI-script](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption van vereisten Power shell-script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Een sleutelkluis voor Azure Disk Encryption maken en configureren](disk-encryption-key-vault.md)


