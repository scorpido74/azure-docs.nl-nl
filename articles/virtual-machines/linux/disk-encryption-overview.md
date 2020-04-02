---
title: Azure Disk Encryption inschakelen voor Linux-VM's
description: In dit artikel vindt u instructies voor het inschakelen van Microsoft Azure Disk Encryption voor Linux VM's.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: d058ff5f9863642f73725db3472c942161447f25
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548438"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Azure-schijfversleuteling voor Linux-VM's 

Azure Disk Encryption helpt om uw gegevens te beschermen en te beveiligen, zodat u aan de beveiligings- en nalevingsafspraken van uw organisatie voldoet. Het maakt gebruik van de [DM-Crypt-functie](https://en.wikipedia.org/wiki/Dm-crypt) van Linux om volumeversleuteling te bieden voor het besturingssysteem en gegevensschijven van Virtuele Azure-machines (VM's) en is geïntegreerd met [Azure Key Vault](../../key-vault/index.yml) om u te helpen de schijfversleutelingssleutels en -geheimen te beheren. 

Als u [Azure Security Center](../../security-center/index.yml)gebruikt, wordt u gewaarschuwd als u VM's hebt die niet zijn versleuteld. De waarschuwingen worden weergegeven als hoge ernst en de aanbeveling is om deze VM's te versleutelen.

![Waarschuwing voor azure security center-schijfversleuteling](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Als u azure-schijfversleuteling eerder met Azure AD hebt gebruikt om een vm te versleutelen, moet u deze optie blijven gebruiken om uw vm te versleutelen. Zie [Azure Disk Encryption with Azure AD (vorige release)](disk-encryption-overview-aad.md) voor meer informatie. 
> - Bepaalde aanbevelingen kunnen het gebruik van gegevens, netwerken of rekenbronnen verhogen, wat resulteert in extra licentie- of abonnementskosten. U moet een geldig actief Azure-abonnement hebben om resources in Azure in de ondersteunde regio's te maken.
> - Momenteel ondersteunen Generatie 2 VM's geen Azure Disk Encryption. Zie [Ondersteuning voor Generatie 2 VM's op Azure](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) voor meer informatie.

U de grondbeginselen van Azure Disk Encryption voor Linux in slechts een paar minuten leren met de [SnelleStart van Een Linux-VM maken en versleutelen met Azure CLI](disk-encryption-cli-quickstart.md) of de [Linux-VM maken en versleutelen met Azure Powershell.](disk-encryption-powershell-quickstart.md)

## <a name="supported-vms-and-operating-systems"></a>Ondersteunde VM's en besturingssystemen

### <a name="supported-vms"></a>Ondersteunde VM's

Linux VM's zijn beschikbaar in [verschillende formaten.](sizes.md) Azure Disk Encryption is niet beschikbaar op [Basic,A-serie VM's](https://azure.microsoft.com/pricing/details/virtual-machines/series/)of op virtuele machines die niet voldoen aan deze minimale geheugenvereisten:

| Virtuele machine | Minimale geheugenvereiste |
|--|--|
| Linux VM's wanneer alleen het versleutelen van gegevensvolumes| 2 GB |
| Linux VM's bij het versleutelen van zowel gegevens als OS-volumes, en waar het gebruik van het root(/) bestandssysteem 4 GB of minder is | 8 GB |
| Linux VM's bij het versleutelen van zowel gegevens als BE-volumes, en waar het gebruik van het root(/) bestandssysteem groter is dan 4 GB | Het gebruik van het hoofdbestandssysteem * 2. Bijvoorbeeld, een 16 GB root-bestandssysteem gebruik vereist ten minste 32GB RAM |

Zodra het os-schijfversleutelingsproces is voltooid op virtuele Linux-machines, kan de VM worden geconfigureerd om met minder geheugen te worden uitgevoerd. 

Azure Disk Encryption is ook beschikbaar voor VM's met premium opslag.

Azure Disk Encryption is niet beschikbaar op [Generatie 2 VM's](generation-2.md#generation-1-vs-generation-2-capabilities)) en [LSV2-serie VM's](../lsv2-series.md)). Zie [Azure Disk Encryption: Niet-ondersteunde scenario's voor](disk-encryption-linux.md#unsupported-scenarios)meer uitzonderingen .

### <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

Azure Disk Encryption wordt ondersteund op een subset van de [door Azure goedgekeurde Linux-distributies](endorsed-distros.md), die zelf een subset is van alle mogelijke Linux-serverdistributies.

![Venn Diagram van Linux-serverdistributies die Azure Disk Encryption ondersteunen](./media/disk-encryption/ade-supported-distros.png)

Linux-serverdistributies die niet door Azure worden goedgekeurd, bieden geen ondersteuning voor Azure Disk Encryption. van degenen die zijn goedgekeurd, ondersteunen alleen de volgende distributies en versies Azure Disk Encryption:

| Linux-distributie | Versie | Volumetype ondersteund voor versleuteling|
| --- | --- |--- |
| Ubuntu | 18.04| BE en gegevensschijf |
| Ubuntu | 16.04| BE en gegevensschijf |
| Ubuntu | 14.04.5</br>[azure-afgestemde kernel bijgewerkt naar 4,15 of hoger](disk-encryption-troubleshooting.md) | BE en gegevensschijf |
| RHEL | 7.7 | BE en gegevensschijf (zie opmerking hieronder) |
| RHEL | 7.6 | BE en gegevensschijf (zie opmerking hieronder) |
| RHEL | 7,5 | BE en gegevensschijf (zie opmerking hieronder) |
| RHEL | 7.4 | BE en gegevensschijf (zie opmerking hieronder) |
| RHEL | 7.3 | BE en gegevensschijf (zie opmerking hieronder) |
| RHEL | 7.2 | BE en gegevensschijf (zie opmerking hieronder) |
| RHEL | 6.8 | Gegevensschijf (zie opmerking hieronder) |
| RHEL | 6.7 | Gegevensschijf (zie opmerking hieronder) |
| CentOS | 7.7 | BE en gegevensschijf |
| CentOS | 7.6 | BE en gegevensschijf |
| CentOS | 7,5 | BE en gegevensschijf |
| CentOS | 7.4 | BE en gegevensschijf |
| CentOS | 7.3 | BE en gegevensschijf |
| CentOS | 7.2n | BE en gegevensschijf |
| CentOS | 6.8 | Gegevensschijf |
| openSUSE | 42.3 | Gegevensschijf |
| SLES | 12-SP4 | Gegevensschijf |
| SLES | 12-SP3 | Gegevensschijf |

> [!NOTE]
> De nieuwe Azure Disk Encryption-implementatie wordt ondersteund voor RHEL OS en gegevensschijf voor RHEL7 Pay-As-You-Go-afbeeldingen.  
>
> ADE wordt ook ondersteund voor RHEL Bring-Your-Own-Subscription Gold Images, maar pas **nadat** het abonnement is geregistreerd. Zie [Red Hat Enterprise Linux Bring-Your-Own-Subscription Gold Images in Azure](../workloads/redhat/byos.md#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) voor meer informatie

## <a name="additional-vm-requirements"></a>Aanvullende VM-vereisten

Azure Disk Encryption vereist dat de dm-crypt- en vfat-modules aanwezig zijn op het systeem. Als u vfat uit de standaardafbeelding verwijdert of uitschakelt, voorkomt u dat het systeem het sleutelvolume kan lezen en de sleutel krijgt die nodig is om de schijven te ontgrendelen bij volgende reboots. Systeemverhardingsstappen waarmee de vfat-module uit het systeem worden verwijderd, zijn niet compatibel met Azure Disk Encryption. 

Voordat versleuteling wordt ingeschakeld, moeten de te versleutelen gegevensschijven correct worden weergegeven in /etc/fstab. Gebruik een permanente naam van het blokapparaat voor dit item, omdat apparaatnamen in de indeling "/dev/sdX" niet kunnen worden vertrouwd om te worden gekoppeld aan dezelfde schijf voor alle reboots, vooral nadat versleuteling is toegepast. Zie Problemen [met de naam van linux VM-apparaatwijzigingen](troubleshoot-device-names-problems.md) voor meer informatie over dit gedrag

Zorg ervoor dat de /etc/fstab-instellingen goed zijn geconfigureerd voor montage. Als u deze instellingen wilt configureren, voert u de opdracht mount -a uit of start u de VM opnieuw op en activeert u de nieuwe bevestiging op die manier. Zodra dat is voltooid, controleer de uitvoer van de lsblk opdracht om te controleren of het station nog steeds is gemonteerd. 
- Als het /etc/fstab-bestand het station niet goed monteert voordat versleuteling wordt inschakelt, kan Azure Disk Encryption het niet goed monteren.
- Het Azure Disk Encryption-proces verplaatst de mount-informatie uit /etc/fstab en naar het eigen configuratiebestand als onderdeel van het versleutelingsproces. Wees niet gealarmeerd om te zien dat de vermelding ontbreekt van / etc / fstab na data drive encryptie is voltooid.
- Voordat u versleuteling start, moet u alle services en processen die kunnen schrijven naar gemonteerde gegevensschijven stoppen en uitschakelen, zodat ze niet automatisch opnieuw worden opgestart na een herstart. Deze kunnen bestanden open houden op deze partities, waardoor de versleutelingsprocedure wordt voorkomen om ze opnieuw te monteren, waardoor de versleuteling mislukt. 
- Na het opnieuw opstarten duurt het tijd voor het Azure Disk Encryption-proces om de nieuw versleutelde schijven te monteren. Ze zijn niet onmiddellijk beschikbaar na een reboot. Het proces heeft tijd nodig om de versleutelde schijven te starten, te ontgrendelen en vervolgens te monteren voordat ze beschikbaar zijn voor andere processen om toegang te krijgen. Dit proces kan meer dan een minuut duren na het opnieuw opstarten, afhankelijk van de systeemkenmerken.

Een voorbeeld van opdrachten die kunnen worden gebruikt om de gegevensschijven te monteren en de benodigde /etc/fstab-vermeldingen te maken, is te vinden in het [CLI-script voor Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started) (regels 244-248) en het [PowerShell-script voor Azure Disk Encryption.](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts) 

## <a name="networking-requirements"></a>Netwerkvereisten

Als u de functie Azure Disk Encryption wilt inschakelen, moeten de Linux-VM's voldoen aan de volgende vereisten voor netwerkeindpuntconfiguratie:
  - Als u een token wilt krijgen om verbinding te maken met uw sleutelkluis, \[\]moet de Linux-VM verbinding kunnen maken met een Azure Active Directory-eindpunt, login.microsoftonline.com .
  - Om de versleutelingssleutels naar uw sleutelkluis te schrijven, moet de Linux-VM verbinding kunnen maken met het eindpunt van de sleutelkluis.
  - De Linux-VM moet verbinding kunnen maken met een eindpunt voor Azure-opslag dat de Azure-extensierepository host en een Azure-opslagaccount dat de VHD-bestanden host.
  -  Als uw beveiligingsbeleid de toegang van Azure VM's tot internet beperkt, u de voorgaande URI oplossen en een specifieke regel configureren om uitgaande connectiviteit met de IP's toe te staan. Zie [Azure Key Vault achter een firewall voor](../../key-vault/key-vault-access-behind-firewall.md)meer informatie.  

## <a name="encryption-key-storage-requirements"></a>Vereisten voor opslag van versleutelingssleutels  

Azure Disk Encryption vereist een Azure Key Vault voor het beheren en beheren van schijfversleutelingssleutels en -geheimen. Uw sleutelkluis en VM's moeten zich in dezelfde Azure-regio en hetzelfde azure-abonnement bevinden.

Zie Een [sleutelkluis voor Azure Disk Encryption maken en configureren voor](disk-encryption-key-vault.md)meer informatie.

## <a name="terminology"></a>Terminologie
In de volgende tabel worden enkele van de algemene termen gedefinieerd die worden gebruikt in azure-schijfversleutelingsdocumentatie:

| Terminologie | Definitie |
| --- | --- |
| Azure Key Vault | Key Vault is een cryptografische, belangrijke beheerservice die is gebaseerd op gevalideerde hardwarebeveiligingsmodules (Federal Information Processing Standards). Deze standaarden helpen om uw cryptografische sleutels en gevoelige geheimen te beschermen. Zie voor meer informatie de [Azure Key Vault-documentatie](https://azure.microsoft.com/services/key-vault/) en [Het maken en configureren van een sleutelkluis voor Azure Disk Encryption](disk-encryption-key-vault.md). |
| Azure CLI | [De Azure CLI](/cli/azure/install-azure-cli) is geoptimaliseerd voor het beheren en beheren van Azure-resources vanaf de opdrachtregel.|
| DM-Crypt |[DM-Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) is het linux-gebaseerde, transparante schijfversleutelingssubsysteem dat wordt gebruikt om schijfversleuteling op Linux VM's mogelijk te maken. |
| Sleutelversleutelingssleutel (KEK) | De asymmetrische toets (RSA 2048) die u gebruiken om het geheim te beschermen of om te wikkelen. U een met een hardwarebeveiligingsmodule (HSM)-beveiligde sleutel of softwarebeveiligde sleutel leveren. Zie voor meer informatie de [Azure Key Vault-documentatie](https://azure.microsoft.com/services/key-vault/) en [Het maken en configureren van een sleutelkluis voor Azure Disk Encryption](disk-encryption-key-vault.md). |
| PowerShell-cmdlets | Zie [Azure PowerShell-cmdlets voor](/powershell/azure/overview)meer informatie . |


## <a name="next-steps"></a>Volgende stappen

- [Quickstart - Een Linux-vm maken en versleutelen met Azure CLI](disk-encryption-cli-quickstart.md)
- [Quickstart - Een Linux-vm maken en versleutelen met Azure Powershell](disk-encryption-powershell-quickstart.md)
- [Azure Disk Encryption-scenario's voor Linux-VM's](disk-encryption-linux.md)
- [Azure Disk Encryption-vereisten voor CLI-script](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption-vereisten Voor PowerShell-script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Een sleutelkluis voor Azure Disk Encryption maken en configureren](disk-encryption-key-vault.md)


