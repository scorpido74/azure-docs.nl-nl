---
title: Azure Disk Encryption voor virtuele Linux-machines inschakelen
description: In dit artikel vindt u instructies voor het inschakelen van Microsoft Azure schijf versleuteling voor virtuele Linux-machines.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 053c52d7d1a0282d72ad76408b77c96aa3b0e3e4
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174709"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Azure Disk Encryption voor Linux-Vm's 

Azure Disk Encryption helpt uw gegevens te beschermen en beschermen om te voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie. Het maakt gebruik van de [DM-cryptografie](https://en.wikipedia.org/wiki/Dm-crypt) functie van Linux om volume versleuteling te bieden voor het besturings systeem en de gegevens schijven van Azure virtual machines (vm's), en is geïntegreerd met [Azure Key Vault](../../key-vault/index.yml) om u te helpen de coderings sleutels en geheimen voor schijven te controleren en te beheren. 

Als u [Azure Security Center](../../security-center/index.yml)gebruikt, wordt u gewaarschuwd als u virtuele machines hebt die niet zijn versleuteld. De waarschuwingen worden weer gegeven als hoge ernst en de aanbeveling is om deze Vm's te versleutelen.

![Waarschuwing voor schijf versleuteling van Azure Security Center](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Als u eerder Azure Disk Encryption met Azure AD hebt gebruikt om een virtuele machine te versleutelen, moet u deze optie blijven gebruiken om uw virtuele machine te versleutelen. Zie [Azure Disk Encryption met Azure AD (vorige versie)](disk-encryption-overview-aad.md) voor meer informatie. 
> - Bepaalde aanbevelingen kunnen leiden tot meer gegevens, het netwerk of het gebruik van reken bronnen, wat leidt tot extra licentie-of abonnements kosten. U moet een geldig actief Azure-abonnement hebben om resources te maken in Azure in de ondersteunde regio's.

Meer informatie over de basis principes van Azure Disk Encryption voor Linux in slechts enkele minuten kunt u met behulp van [een virtuele Linux-machine maken en versleutelen met Azure cli Quick](disk-encryption-cli-quickstart.md) start of [een Linux-VM maken en versleutelen met Azure Power shell Quick](disk-encryption-powershell-quickstart.md)start.

## <a name="supported-vms-and-operating-systems"></a>Ondersteunde Vm's en besturings systemen

### <a name="supported-vm-sizes"></a>Ondersteunde VM-grootten

Virtuele Linux-machines zijn beschikbaar in [verschillende grootten](sizes.md). Azure Disk Encryption is niet beschikbaar op [de Basic-, a-Series vm's](https://azure.microsoft.com/pricing/details/virtual-machines/series/)of op virtuele machines die niet voldoen aan deze minimale geheugen vereisten:

| Virtuele machine | Minimale geheugen vereiste |
|--|--|
| Virtuele Linux-machines wanneer gegevens volumes alleen worden versleuteld| 2 GB |
| Linux-Vm's bij het versleutelen van gegevens en OS-volumes, waarbij het gebruik van het basis-(/) bestands systeem 4 GB of minder is | 8 GB |
| Linux-Vm's bij het versleutelen van gegevens en besturingssysteem volumes, waarbij het gebruik van het root-(/) bestands systeem groter is dan 4 GB | Het root-bestandssysteem gebruik * 2. Een voor beeld: voor een 16 GB aan root File System-gebruik is ten minste GB RAM vereist |

Zodra het versleutelings proces van de besturingssysteem schijf is voltooid op virtuele Linux-machines, kan de virtuele machine worden geconfigureerd om te worden uitgevoerd met minder geheugen. 

Azure Disk Encryption is ook beschikbaar voor virtuele machines met Premium Storage. 

### <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

Azure Disk Encryption wordt ondersteund op een subset van de door [Azure goedgekeurde Linux-distributies](endorsed-distros.md). Dit is een subset van alle mogelijke distributies van Linux server.

![Venn-diagram van Linux-server distributies die ondersteuning bieden voor Azure Disk Encryption](./media/disk-encryption/ade-supported-distros.png)

Linux-server distributies die niet zijn goedgekeurd door Azure, bieden geen ondersteuning voor Azure Disk Encryption; van degenen die zijn goedgekeurd, worden alleen de volgende distributies en versies ondersteund Azure Disk Encryption:

| Linux-distributie | Version | Ondersteund volume type voor versleuteling|
| --- | --- |--- |
| Ubuntu | 18,04| Besturings systeem en gegevens schijf |
| Ubuntu | 16,04| Besturings systeem en gegevens schijf |
| Ubuntu | 14.04.5</br>[met een afgestemde kernel van Azure bijgewerkt tot 4,15 of hoger](disk-encryption-troubleshooting.md) | Besturings systeem en gegevens schijf |
| RHEL | 7,7 | Besturings systeem en gegevens schijf (zie opmerking hieronder) |
| RHEL | 7,6 | Besturings systeem en gegevens schijf (zie opmerking hieronder) |
| RHEL | 7,5 | Besturings systeem en gegevens schijf (zie opmerking hieronder) |
| RHEL | 7,4 | Besturings systeem en gegevens schijf (zie opmerking hieronder) |
| RHEL | 7.3 | Besturings systeem en gegevens schijf (zie opmerking hieronder) |
| RHEL | 7.2 | Besturings systeem en gegevens schijf (zie opmerking hieronder) |
| RHEL | 6.8 | Gegevens schijf (zie opmerking hieronder) |
| RHEL | 6.7 | Gegevens schijf (zie opmerking hieronder) |
| CentOS | 7,7 | Besturings systeem en gegevens schijf |
| CentOS | 7,6 | Besturings systeem en gegevens schijf |
| CentOS | 7,5 | Besturings systeem en gegevens schijf |
| CentOS | 7,4 | Besturings systeem en gegevens schijf |
| CentOS | 7.3 | Besturings systeem en gegevens schijf |
| CentOS | 7,2 n | Besturings systeem en gegevens schijf |
| CentOS | 6.8 | Gegevensschijf |
| openSUSE | 42,3 | Gegevensschijf |
| SLES | 12-SP4 | Gegevensschijf |
| SLES | 12-SP3 | Gegevensschijf |

> [!NOTE]
> De nieuwe ADE-implementatie wordt ondersteund voor RHEL-besturings systeem en gegevens schijf voor RHEL7 betalen per gebruik-installatie kopieën. ADE wordt momenteel niet ondersteund voor RHEL (BYOS)-installatie kopieën van uw eigen abonnement. 

## <a name="additional-vm-requirements"></a>Aanvullende VM-vereisten

Azure Disk Encryption vereist dat de DM-cryptografie-en vfat-modules aanwezig zijn op het systeem. Als u vfat uit de standaard installatie kopie verwijdert of uitschakelt, wordt het sleutel volume niet door het systeem gelezen en wordt de benodigde sleutel voor het ontgrendelen van de schijven bij de volgende keer opnieuw opstarten voor komen. De stappen voor het beveiligen van het systeem die de vfat-module van het systeem verwijderen, zijn niet compatibel met Azure Disk Encryption. 

Voordat u versleuteling inschakelt, moeten de gegevens schijven die moeten worden versleuteld, op de juiste manier worden weer gegeven in/etc/fstab. Gebruik een permanente blok apparaatnaam voor dit item, aangezien apparaatnamen in de indeling '/dev/sdX ' niet kunnen worden gebruikt om te worden gekoppeld aan dezelfde schijf tijdens het opnieuw opstarten, met name nadat de versleuteling is toegepast. Zie voor meer informatie over dit gedrag: [problemen met Linux VM-apparaatnaam wijzigen](troubleshoot-device-names-problems.md)

Controleer of de bestand/etc/fstab-instellingen correct zijn geconfigureerd voor koppelen. Als u deze instellingen wilt configureren, voert u de koppeling-a-opdracht uit of start u de VM opnieuw op. vervolgens wordt de koppeling op die manier geactiveerd. Als dat is voltooid, controleert u de uitvoer van de lsblk-opdracht om te controleren of het station nog steeds is gekoppeld. 
- Als het bestand/etc/fstab-bestand het station niet correct koppelt voordat u versleuteling inschakelt, kan Azure Disk Encryption het niet goed koppelen.
- Het Azure Disk Encryption proces verplaatst de koppelings gegevens uit bestand/etc/fstab en in een eigen configuratie bestand als onderdeel van het versleutelings proces. Geen waarschuwing weer gegeven om te zien welke vermelding ontbreekt in bestand/etc/fstab nadat de versleuteling van de gegevens schijf is voltooid.
- Voordat u begint met het versleutelen, moet u alle services en processen die kunnen schrijven naar gekoppelde gegevens schijven stoppen en uitschakelen, zodat ze niet automatisch opnieuw worden opgestart na het opnieuw opstarten. Hierdoor blijven bestanden geopend op deze partities, waardoor de versleutelings procedure niet opnieuw kan worden gekoppeld, waardoor de versleuteling mislukt. 
- Nadat het systeem opnieuw is opgestart, zal het Azure Disk Encryption proces de zojuist versleutelde schijven te koppelen. Ze zijn niet onmiddellijk beschikbaar na het opnieuw opstarten. Het proces heeft tijd nodig om de versleutelde stations te starten, te ontgrendelen en vervolgens te koppelen voordat andere processen kunnen worden geopend. Dit proces kan meer dan een minuut duren na het opnieuw opstarten, afhankelijk van de systeem kenmerken.

Een voor beeld van opdrachten die kunnen worden gebruikt om de gegevens schijven te koppelen en de benodigde bestand/etc/fstab-vermeldingen te maken, vindt u in het [Azure Disk Encryption vereisten CLI-script](https://github.com/ejarvi/ade-cli-getting-started) (regels 244-248) en de [Azure Disk Encryption vereisten Power shell script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts). 

## <a name="networking-requirements"></a>Netwerk vereisten

Als u de functie Azure Disk Encryption wilt inschakelen, moeten de virtuele Linux-machines voldoen aan de volgende vereisten voor netwerk eindpunt configuratie:
  - Om een token te krijgen om verbinding te maken met uw sleutel kluis, moet de virtuele Linux-machine verbinding kunnen maken met een Azure Active Directory-eind punt @no__t -0login. microsoftonline. com @ no__t-1.
  - Als u de versleutelings sleutels naar uw sleutel kluis wilt schrijven, moet de virtuele Linux-machine verbinding kunnen maken met het eind punt van de sleutel kluis.
  - De virtuele Linux-machine moet verbinding kunnen maken met een Azure Storage-eind punt dat als host fungeert voor de Azure extension-opslag plaats en een Azure Storage-account dat de VHD-bestanden host.
  -  Als uw beveiligings beleid de toegang tot het Internet beperkt met Azure-Vm's, kunt u de voor gaande URI omzetten en een specifieke regel configureren om uitgaande connectiviteit met de IP-adressen toe te staan. Zie [Azure Key Vault achter een firewall](../../key-vault/key-vault-access-behind-firewall.md)voor meer informatie.  

## <a name="encryption-key-storage-requirements"></a>Opslag vereisten voor de versleutelings sleutel  

Voor Azure Disk Encryption is een Azure Key Vault vereist om sleutels en geheimen voor schijf versleuteling te beheren en te beheren. Uw sleutel kluis en Vm's moeten zich in dezelfde Azure-regio en hetzelfde abonnement bevinden.

Zie [een sleutel kluis maken en configureren voor Azure Disk Encryption](disk-encryption-key-vault.md)voor meer informatie.

## <a name="terminology"></a>Terminologie
In de volgende tabel worden enkele algemene termen gedefinieerd die worden gebruikt in azure Disk Encryption Documentation:

| Terminologie | Definitie |
| --- | --- |
| Azure Key Vault | Key Vault is een cryptografische service voor sleutel beheer die is gebaseerd op FIPS (Federal Information Processing Standards) gevalideerde hardware security modules. Deze standaarden helpen u bij het beveiligen van uw cryptografische sleutels en gevoelige geheimen. Zie voor meer informatie de [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -documentatie en het [maken en configureren van een sleutel kluis voor Azure Disk Encryption](disk-encryption-key-vault.md). |
| Azure CLI | [De Azure cli](/cli/azure/install-azure-cli) is geoptimaliseerd voor het beheren en beheren van Azure-resources vanaf de opdracht regel.|
| DM-cryptografie |[DM-cryptografie](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) is het op Linux gebaseerde, transparante schijf versleutelings subsysteem dat wordt gebruikt om schijf versleuteling in te scha kelen op Linux vm's. |
| Sleutel versleutelings sleutel (KEK) | De asymmetrische sleutel (RSA 2048) die u kunt gebruiken om het geheim te beveiligen of in te pakken. U kunt een door HSM (Hardware Security module) beveiligde of met software beschermde sleutel opgeven. Zie voor meer informatie de [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -documentatie en het [maken en configureren van een sleutel kluis voor Azure Disk Encryption](disk-encryption-key-vault.md). |
| PowerShell-cmdlets | Zie [Azure PowerShell-cmdlets](/powershell/azure/overview)voor meer informatie. |


## <a name="next-steps"></a>Volgende stappen

- [Snelstartgids: een virtuele Linux-machine maken en versleutelen met Azure CLI](disk-encryption-cli-quickstart.md)
- [Snelstartgids: een virtuele Linux-machine maken en versleutelen met Azure Power shell](disk-encryption-powershell-quickstart.md)
- [Azure Disk Encryption scenario's voor Linux-Vm's](disk-encryption-linux.md)
- [SysteemAzure Disk Encryption vereisten CLI-script](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption van vereisten Power shell-script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Een sleutel kluis voor Azure Disk Encryption maken en configureren](disk-encryption-key-vault.md)


