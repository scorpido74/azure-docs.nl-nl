---
title: Azure Disk Encryption voor virtuele Linux-machines inschakelen
description: In dit artikel vindt u instructies voor het inschakelen van Microsoft Azure schijf versleuteling voor virtuele Linux-machines.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: ff1b37c3053ffa91dcb432cd97a7dd6fd71dad1c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79250424"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Azure Disk Encryption voor Linux-Vm's 

Azure Disk Encryption helpt uw gegevens te beschermen en beschermen om te voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie. Het maakt gebruik van de [DM-cryptografie](https://en.wikipedia.org/wiki/Dm-crypt) functie van Linux om volume versleuteling te bieden voor het besturings systeem en de gegevens schijven van Azure virtual machines (vm's), en is geïntegreerd met [Azure Key Vault](../../key-vault/index.yml) om u te helpen de coderings sleutels en geheimen voor schijven te controleren en te beheren. 

Als u [Azure Security Center](../../security-center/index.yml)gebruikt, wordt u gewaarschuwd als u virtuele machines hebt die niet zijn versleuteld. De waarschuwingen worden weergegeven als hoge urgentie en de aanbeveling is voor het versleutelen van deze VM's.

![Azure Security Center schijf versleuteling waarschuwing](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Als u eerder Azure Disk Encryption met Azure AD hebt gebruikt om een virtuele machine te versleutelen, moet u deze optie blijven gebruiken om uw virtuele machine te versleutelen. Zie [Azure Disk Encryption met Azure AD (vorige versie)](disk-encryption-overview-aad.md) voor meer informatie. 
> - Bepaalde aanbevelingen verhogen gegevens-, netwerk- of computerresources, wat resulteert in extra kosten in licentie of abonnement. U moet een geldige actief Azure-abonnement om resources te maken in Azure in de ondersteunde regio's hebben.
> - Virtuele machines van generatie 2 bieden geen ondersteuning voor Azure Disk Encryption. Zie [ondersteuning voor virtuele machines van de tweede generatie op Azure](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) voor meer informatie.

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

| Linux-distributie | Version | Volumetype wordt ondersteund voor versleuteling|
| --- | --- |--- |
| Ubuntu | 18,04| Besturingssysteem- en schijf |
| Ubuntu | 16.04| Besturingssysteem- en schijf |
| Ubuntu | 14.04.5</br>[met een afgestemde kernel van Azure bijgewerkt tot 4,15 of hoger](disk-encryption-troubleshooting.md) | Besturingssysteem- en schijf |
| RHEL | 7,7 | Besturings systeem en gegevens schijf (zie opmerking hieronder) |
| RHEL | 7,6 | Besturings systeem en gegevens schijf (zie opmerking hieronder) |
| RHEL | 7.5 | Besturings systeem en gegevens schijf (zie opmerking hieronder) |
| RHEL | 7.4 | Besturings systeem en gegevens schijf (zie opmerking hieronder) |
| RHEL | 7.3 | Besturings systeem en gegevens schijf (zie opmerking hieronder) |
| RHEL | 7.2 | Besturings systeem en gegevens schijf (zie opmerking hieronder) |
| RHEL | 6.8 | Gegevens schijf (zie opmerking hieronder) |
| RHEL | 6.7 | Gegevens schijf (zie opmerking hieronder) |
| CentOS | 7,7 | Besturingssysteem- en schijf |
| CentOS | 7,6 | Besturingssysteem- en schijf |
| CentOS | 7.5 | Besturingssysteem- en schijf |
| CentOS | 7.4 | Besturingssysteem- en schijf |
| CentOS | 7.3 | Besturingssysteem- en schijf |
| CentOS | 7.2n | Besturingssysteem- en schijf |
| CentOS | 6.8 | Gegevensschijf |
| openSUSE | 42,3 | Gegevensschijf |
| SLES | 12-SP4 | Gegevensschijf |
| SLES | 12-SP3 | Gegevensschijf |

> [!NOTE]
> De nieuwe Azure Disk Encryption-implementatie wordt ondersteund voor RHEL-besturings systeem en gegevens schijf voor RHEL7 betalen per gebruik-installatie kopieën.  
>
> ADE wordt ook ondersteund voor RHEL-uw eigen abonnementen, maar pas **nadat** het abonnement is geregistreerd. Zie [Red Hat Enterprise Linux uw gouden installatie kopieën met uw eigen abonnement in azure](../workloads/redhat/byos.md#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) voor meer informatie.

## <a name="additional-vm-requirements"></a>Aanvullende VM-vereisten

Azure Disk Encryption vereist dat de DM-cryptografie-en vfat-modules aanwezig zijn op het systeem. Als u vfat uit de standaard installatie kopie verwijdert of uitschakelt, wordt het sleutel volume niet door het systeem gelezen en wordt de benodigde sleutel voor het ontgrendelen van de schijven bij de volgende keer opnieuw opstarten voor komen. De stappen voor het beveiligen van het systeem die de vfat-module van het systeem verwijderen, zijn niet compatibel met Azure Disk Encryption. 

Voordat u versleuteling inschakelt, moeten de gegevens schijven die moeten worden versleuteld, op de juiste manier worden weer gegeven in/etc/fstab. Gebruik een permanente blok apparaatnaam op voor deze vermelding als apparaat namen in de indeling '/ dev/sdX' kunnen niet worden gebruikt om te worden gekoppeld aan dezelfde schijf tijdens opnieuw opstarten, met name als versleuteling wordt toegepast. Zie voor meer informatie over dit gedrag: [problemen met Linux VM-apparaatnaam wijzigen](troubleshoot-device-names-problems.md)

Zorg ervoor dat de/etc/fstab-instellingen juist zijn geconfigureerd voor koppelen. Deze instellingen configureren, uitvoeren van de opdracht mount- of opnieuw opstarten van de virtuele machine en activeren van de beschadigingsgebeurtenissen op die manier. Zodra dat is voltooid, controleert u de uitvoer van de opdracht lsblk om te controleren dat de schijf nog steeds is gekoppeld. 
- Als het bestand/etc/fstab niet naar behoren het station koppelen voordat versleuteling is ingeschakeld, kunnen Azure Disk Encryption correct koppelen niet mogelijk.
- De Azure Disk Encryption-proces worden de mount-gegevens uit/etc/fstab en in een eigen configuratiebestand als onderdeel van het versleutelingsproces verplaatst. Geen worden zorgen om te zien van de vermelding ontbreekt in/etc/fstab nadat gegevens stationsversleuteling is voltooid.
- Voordat u begint met het versleutelen, moet u alle services en processen die kunnen schrijven naar gekoppelde gegevens schijven stoppen en uitschakelen, zodat ze niet automatisch opnieuw worden opgestart na het opnieuw opstarten. Hierdoor blijven bestanden geopend op deze partities, waardoor de versleutelings procedure niet opnieuw kan worden gekoppeld, waardoor de versleuteling mislukt. 
- Het duurt na opnieuw opstarten, tijd voor de Azure Disk Encryption-proces om de zojuist versleutelde schijven te koppelen. Ze zijn niet meteen beschikbaar na het opnieuw opstarten. Het proces tijd om te beginnen, ontgrendelen en koppel vervolgens de versleutelde schijven voordat deze beschikbaar is voor andere processen voor toegang tot nodig heeft. Dit proces duurt langer dan een minuut na opnieuw opstarten, afhankelijk van de kenmerken van het systeem.

Een voor beeld van opdrachten die kunnen worden gebruikt om de gegevens schijven te koppelen en de benodigde bestand/etc/fstab-vermeldingen te maken, vindt u in het [Azure Disk Encryption vereisten CLI-script](https://github.com/ejarvi/ade-cli-getting-started) (regels 244-248) en het [Power shell-script voor de Azure Disk Encryption vereisten](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts). 

## <a name="networking-requirements"></a>Netwerk vereisten

Als u de functie Azure Disk Encryption wilt inschakelen, moeten de virtuele Linux-machines voldoen aan de volgende vereisten voor netwerk eindpunt configuratie:
  - Om een token te krijgen om verbinding te maken met uw sleutel kluis, moet de virtuele Linux-machine verbinding kunnen maken met een Azure Active Directory-eind punt \[login.microsoftonline.com\].
  - Als u de versleutelings sleutels naar uw sleutel kluis wilt schrijven, moet de virtuele Linux-machine verbinding kunnen maken met het eind punt van de sleutel kluis.
  - De virtuele Linux-machine moet verbinding kunnen maken met een Azure Storage-eind punt dat als host fungeert voor de Azure extension-opslag plaats en een Azure Storage-account dat de VHD-bestanden host.
  -  Als uw beveiligingsbeleid beperkt de toegang van Azure VM's tot het Internet, kunt u deze kunt oplossen door de voorgaande URI en configureren van een specifieke regel voor het toestaan van uitgaande verbinding met de IP-adressen. Zie [Azure Key Vault achter een firewall](../../key-vault/key-vault-access-behind-firewall.md)voor meer informatie.  

## <a name="encryption-key-storage-requirements"></a>Opslag vereisten voor de versleutelings sleutel  

Voor Azure Disk Encryption is een Azure Key Vault vereist om sleutels en geheimen voor schijf versleuteling te beheren en te beheren. Uw sleutel kluis en Vm's moeten zich in dezelfde Azure-regio en hetzelfde abonnement bevinden.

Zie [een sleutel kluis maken en configureren voor Azure Disk Encryption](disk-encryption-key-vault.md)voor meer informatie.

## <a name="terminology"></a>Terminologie
In de volgende tabel worden enkele algemene termen gedefinieerd die worden gebruikt in azure Disk Encryption Documentation:

| Terminologie | Definitie |
| --- | --- |
| Azure Key Vault | Key Vault is een cryptografische, key management-service die op Federal Information Processing Standards (FIPS) gevalideerde hardware security modules is gebaseerd. Deze standaarden helpen om uw cryptografische sleutels en gevoelige geheimen te beveiligen. Zie voor meer informatie de [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -documentatie en het [maken en configureren van een sleutel kluis voor Azure Disk Encryption](disk-encryption-key-vault.md). |
| Azure CLI | [De Azure cli](/cli/azure/install-azure-cli) is geoptimaliseerd voor het beheren en beheren van Azure-resources vanaf de opdracht regel.|
| DM-Crypt |[DM-cryptografie](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) is het op Linux gebaseerde, transparante schijf versleutelings subsysteem dat wordt gebruikt om schijf versleuteling in te scha kelen op Linux vm's. |
| Sleutel versleutelings sleutel (KEK) | De asymmetrische sleutel (RSA 2048) die u kunt gebruiken om het geheim te beveiligen of in te pakken. U kunt opgeven dat een hardware security module (HSM)- of software beschermde sleutel beschermd. Zie voor meer informatie de [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -documentatie en het [maken en configureren van een sleutel kluis voor Azure Disk Encryption](disk-encryption-key-vault.md). |
| PowerShell-cmdlets | Zie [Azure PowerShell-cmdlets](/powershell/azure/overview)voor meer informatie. |


## <a name="next-steps"></a>Volgende stappen

- [Snelstartgids: een virtuele Linux-machine maken en versleutelen met Azure CLI](disk-encryption-cli-quickstart.md)
- [Snelstartgids: een virtuele Linux-machine maken en versleutelen met Azure Power shell](disk-encryption-powershell-quickstart.md)
- [Azure Disk Encryption-scenario's voor Linux-VM's](disk-encryption-linux.md)
- [SysteemAzure Disk Encryption vereisten CLI-script](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption van vereisten Power shell-script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Een sleutelkluis voor Azure Disk Encryption maken en configureren](disk-encryption-key-vault.md)


