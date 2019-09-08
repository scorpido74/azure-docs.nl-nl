---
title: Wat is Azure Disk Encryption?
description: Dit artikel bevat een overzicht van Azure Disk Encryption
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 09/05/2019
ms.custom: seodec18
ms.openlocfilehash: 7855b1048826a9146e206226ae7326661e4609cb
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764403"
---
# <a name="azure-disk-encryption-overview"></a>Overzicht van Azure Disk Encryption

Azure Disk Encryption helpt uw gegevens te beschermen en beschermen om te voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie. Het maakt gebruik van de [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) -functie van Windows en de [DM-cryptografie](https://en.wikipedia.org/wiki/Dm-crypt) functie van Linux om volume versleuteling voor het besturings systeem en de gegevens schijven van Azure virtual machines (vm's) te bieden. Het is ook geïntegreerd met [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) om u te helpen de coderings sleutels en geheimen van de schijf te controleren en te beheren, en ervoor te zorgen dat alle gegevens op de VM-schijven in rust worden versleuteld terwijl ze zich in azure Storage bevinden. Azure Disk Encryption voor virtuele Windows-en Linux-machines is in algemene Beschik baarheid in alle open bare Azure-regio's en Azure Government regio's voor standaard-Vm's en Vm's met Azure Premium Storage. 

Als u Azure Security Center gebruikt, bent u gewaarschuwd als er virtuele machines die niet zijn versleuteld. De waarschuwingen worden weergegeven als hoge urgentie en de aanbeveling is voor het versleutelen van deze VM's.

![Azure Security Center schijf versleuteling waarschuwing](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Bepaalde aanbevelingen verhogen gegevens, netwerk, of gebruik van de compute-bronnen en leiden tot extra kosten in licentie of abonnement.


## <a name="encryption-scenarios"></a>Scenario's voor versleuteling

Met Azure Disk Encryption kunt u de vereisten voor de beveiliging en naleving van een organisatie aanpakken door uw Azure-Vm's op rest te beveiligen met behulp van de industrie standaard versleutelings technologie. U kunt Vm's ook configureren voor opstarten onder door de klant beheerde sleutels en beleids regels (BYOK) en het gebruik van deze sleutels in uw sleutel kluis controleren.

Azure Disk Encryption ondersteunt de volgende klanten scenario's:

* Het inschakelen en uitschakelen van versleuteling op nieuwe virtuele machines die zijn gemaakt op basis van de ondersteunde installatie kopieën van Azure galerie.
* Het inschakelen en uitschakelen van versleuteling op bestaande virtuele machines die worden uitgevoerd in Azure.
* Het inschakelen en uitschakelen van versleuteling op nieuwe Windows-Vm's die zijn gemaakt op basis van vooraf versleutelde VHD-en versleutelings sleutels.
* Het in-en uitschakelen van versleuteling op virtuele-machine schaal sets van Windows.
* Het inschakelen en uitschakelen van versleuteling op gegevens stations voor virtuele Linux-machine schaal sets.
* Versleuteling van virtuele machines met beheerde schijven in-en uitschakelen.
* De versleutelings instellingen van een bestaande versleutelde Premium-en niet-Premium Storage VM bijwerken.
* Back-ups maken en herstellen van versleutelde Vm's.
* Neem uw eigen versleuteling (BYOE) en til uw eigen sleutel (BYOK)-scenario's in, waarbij de klanten hun eigen versleutelings sleutels gebruiken en opslaan in een Azure-sleutel kluis.

Het biedt ook ondersteuning voor de volgende scenario's voor virtuele machines wanneer deze zijn ingeschakeld in Microsoft Azure:

* Integratie met Azure Key Vault.
* [Virtuele machines](https://azure.microsoft.com/pricing/details/virtual-machines/) uit de Standard-laag die voldoen aan de [minimale geheugen vereiste](azure-security-disk-encryption-prerequisites.md#supported-vm-sizes). 
* Het inschakelen van versleuteling op virtuele Windows-en Linux-machines, Managed disk en scale set-Vm's van de ondersteunde installatie kopieën van Azure galerie.
* Versleuteling uitschakelen voor besturings systeem en gegevens stations voor Windows-Vm's, Vm's in schaal sets en virtuele machines met beheerde schijven.
* Versleuteling uitschakelen op gegevens stations voor Linux Vm's, schaal sets Vm's en beheerde schijf-Vm's.
* Versleuteling inschakelen op Vm's waarop het Windows-client besturingssysteem wordt uitgevoerd.
* Versleuteling inschakelen voor volumes met koppel paden.
* Het inschakelen van versleuteling op Linux-Vm's die zijn geconfigureerd met schijf striping (RAID) met behulp van mdadm.
* Het inschakelen van versleuteling op Linux Vm's die gebruikmaken van LVM voor gegevens schijven.
* Versleuteling inschakelen op het besturings systeem en de gegevens schijven van de Linux-VM.

   > [!NOTE]
   > OS-stationsversleuteling voor bepaalde Linux-distributies wordt niet ondersteund. Zie voor meer informatie de [Azure Disk Encryption ondersteunde besturings systemen: Linux](azure-security-disk-encryption-prerequisites.md#linux).
   
* Het inschakelen van versleuteling voor virtuele machines die zijn geconfigureerd met Windows-opslag ruimten die beginnen met Windows Server 2016. Opslagruimten Direct (S2D) wordt nog niet ondersteund.
* Maak een back-up en herstel van versleutelde Vm's voor zowel de versleutelings sleutel (KEK) als niet-KEK scenario's.

Azure Disk Encryption werkt niet voor de volgende scenario's, functies en technologie:

* Het versleutelen van de virtuele machine van de Basic-laag of de virtuele machines die zijn gemaakt met de methode klassieke VM
* Versleuteling uitschakelen op een OS-station of gegevens station van een virtuele Linux-machine wanneer het station van het besturings systeem is versleuteld.
* OS-station voor schaal sets van virtuele Linux-machines versleutelen.
* Versleutelen van virtuele Windows-machines die zijn geconfigureerd met RAID-systemen op basis van software.
* Aangepaste installatie kopieën op Linux-Vm's versleutelen.
* Integratie met een on-premises sleutel beheersysteem.
* Azure-bestanden (gedeelde bestandssysteem).
* Network File System (NFS).
* Dynamische volumes.
* Windows Server-containers, waarmee dynamische volumes worden gemaakt voor elke container.
* Tijdelijke OS-schijven.
* Versleuteling van gedeelde/gedistribueerde bestands systemen zoals (maar niet beperkt tot): DFS, GFS, DRDB, CephFS, etc

## <a name="encryption-features"></a>Versleutelingsfuncties

Wanneer u Azure Disk Encryption voor virtuele Azure-machines inschakelt en implementeert, kunt u de volgende mogelijkheden configureren om in te scha kelen:

* Het volume van het besturings systeem versleutelen om het opstart volume in de rest van uw opslag ruimte te beveiligen.
* Gegevens volumes versleutelen om de gegevens volumes in de rest van uw opslag ruimte te beveiligen.
* Versleuteling uitschakelen voor het besturings systeem en de gegevens stations voor Windows-Vm's.
* Versleuteling uitschakelen op de gegevens stations voor virtuele Linux-machines (alleen wanneer het station van het besturings systeem niet is versleuteld).
* Beveiliging van de versleutelings sleutels en geheimen in uw Azure Key Vault-abonnement.
* Rapportage van de versleutelings status van de versleutelde virtuele machine.
* De configuratie-instellingen voor schijf versleuteling van de virtuele machine worden verwijderd.
* Back-up en herstel van de versleutelde Vm's met behulp van de Azure Backup-service.

Azure Disk Encryption voor Vm's voor Windows en Linux omvat:

* [De schijf versleutelings extensie voor Windows](../virtual-machines/extensions/azure-disk-enc-windows.md).
* [De schijf versleutelings extensie voor Linux](../virtual-machines/extensions/azure-disk-enc-linux.md).
* [De Power shell-cmdlets voor schijf versleuteling](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.2.0).
* [De Azure cli-schijf versleutelings-cmdlets](/cli/azure/vm/encryption?view=azure-cli-latest).
* [De Azure Resource Manager sjabloon voor het versleutelen van schijven](azure-security-disk-encryption-appendix.md#resource-manager-templates).

> [!NOTE]
> Er zijn geen extra kosten voor het versleutelen van VM-schijven met Azure Disk Encryption. Standard [prijzen voor Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) is van toepassing op de sleutelkluis die wordt gebruikt voor het opslaan van de versleutelingssleutels. 

## <a name="encryption-workflow"></a>Werkstroom voor versleuteling

Om in te schakelen disk encryption voor Windows en Linux-machines, moet u de volgende stappen uitvoeren:

1. U meldt zich aan het inschakelen van versleuteling van schijf via de Azure Disk Encryption Resource Manager-sjabloon, PowerShell-cmdlets of de Azure CLI en geeft u de versleutelingsconfiguratie.

   * Voor het scenario van de VHD klant versleuteld door de versleutelde VHD te uploaden naar uw opslagaccount en de versleuteling sleutelmateriaal tot uw key vault. Geef vervolgens de versleutelings configuratie op om versleuteling in te scha kelen voor een nieuwe virtuele machine.
   * Voor nieuwe virtuele machines die worden gemaakt op basis van ondersteunde galerie-installatie kopieën en bestaande Vm's die al in Azure worden uitgevoerd, geeft u de versleutelings configuratie op om versleuteling in te scha kelen op de virtuele machine.

1. Verleen toegang tot het Azure-platform voor het lezen van het versleutelings sleutel materiaal (BitLocker-versleutelings sleutels voor Windows-systemen en wachtwoordzin voor Linux) van uw sleutel kluis om versleuteling in te scha kelen op de virtuele machine.

1. Azure werkt bij het VM-servicemodel met versleuteling en de configuratie van de key vault en stelt u de versleutelde VM.

   ![Microsoft Antimalware in Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>Werkstroom voor ontsleuteling
Als u schijf versleuteling voor Vm's wilt uitschakelen, voert u de volgende stappen op hoog niveau uit:

1. Kies voor het uitschakelen van versleuteling (ontsleutelen) op een actieve virtuele machine in Azure en geef de ontsleutelings configuratie op. U kunt uitschakelen via de Azure Disk Encryption Resource Manager-sjabloon, PowerShell-cmdlets of de Azure CLI.

   Met deze stap wordt versleuteling van het besturings systeem of het gegevens volume of van beide op de actieve Windows-VM uitgeschakeld. Zoals vermeld in de vorige sectie, wordt niet uitschakelen van OS-schijfversleuteling voor Linux ondersteund. De stap ontsleuteling is alleen toegestaan voor schijven op virtuele Linux-machines, zolang de besturingssysteemschijf wordt niet versleuteld.

1. Azure werkt het VM-service model bij en de VM is gemarkeerd als ontsleuteld. De inhoud van de virtuele machine worden niet meer in rust versleuteld.

   > [!NOTE]
   > Versleuteling met de bewerking verwijderd niet uit uw key vault en de versleuteling sleutelmateriaal (BitLocker-versleutelingssleutels voor Windows-systemen) of de wachtwoordzin voor Linux.
   >
   > Uitschakelen van OS-schijfversleuteling voor Linux wordt niet ondersteund. De stap ontsleuteling is alleen toegestaan voor schijven op virtuele Linux-machines.
   >
   > Uitschakelen van de schijf van gegevensversleuteling voor Linux wordt niet ondersteund als de besturingssysteemschijf is versleuteld.


## <a name="encryption-workflow-previous-release"></a>Versleuteling werkstroom (vorige versie)

De nieuwe versie van Azure Disk Encryption wordt voorkomen dat de vereiste voor een parameter van de toepassing Azure Active Directory (Azure AD) om in te schakelen van VM-schijfversleuteling. Met de nieuwe release, bent u niet langer vereist voor een Azure AD-referenties opgeven tijdens de stap van de versleuteling inschakelen. Alle nieuwe virtuele machines moeten worden versleuteld zonder de parameters van de toepassing Azure AD wanneer u de nieuwe versie. Virtuele machines die al zijn versleuteld met Azure AD-toepassing parameters worden nog steeds ondersteund en worden onderhouden met de syntaxis van de Azure AD moeten blijven. Om in te schakelen disk encryption voor Windows en Linux-machines (vorige versie), voer de volgende stappen uit:

1. Kies een versleutelingsscenario in de scenario's die worden vermeld in de [versleutelingsscenario](#encryption-scenarios) sectie.

1. U meldt zich aan het inschakelen van versleuteling van schijf via de Azure Disk Encryption Resource Manager-sjabloon, PowerShell-cmdlets of de Azure CLI en geeft u de versleutelingsconfiguratie.

   * Voor het scenario van de VHD klant versleuteld door de versleutelde VHD te uploaden naar uw opslagaccount en de versleuteling sleutelmateriaal tot uw key vault. Geef vervolgens de versleutelings configuratie op om versleuteling in te scha kelen voor een nieuwe virtuele machine.
   * Voor nieuwe Vm's die zijn gemaakt op basis van de Marketplace en bestaande Vm's die al in Azure worden uitgevoerd, geeft u de versleutelings configuratie op om versleuteling in te scha kelen op de virtuele machine.

1. Verleen toegang tot het Azure-platform voor het lezen van het versleutelings sleutel materiaal (BitLocker-versleutelings sleutels voor Windows-systemen en wachtwoordzin voor Linux) van uw sleutel kluis om versleuteling in te scha kelen op de virtuele machine.

1. Geef de Azure AD-toepassings-id voor het schrijven van de versleutelingssleutel materiaal naar uw key vault. Met deze stap wordt versleuteling ingeschakeld op de virtuele machine voor de scenario's die worden vermeld in stap 2.

1. Azure werkt bij het VM-servicemodel met versleuteling en de configuratie van de key vault en stelt u de versleutelde VM.


## <a name="terminology"></a>Terminologie
In de volgende tabel worden enkele algemene termen gedefinieerd die worden gebruikt in azure Disk Encryption Documentation:

| Terminologie | Definitie |
| --- | --- |
| Azure AD | Een [Azure AD](https://azure.microsoft.com/documentation/services/active-directory/) account wordt gebruikt om te verifiëren, opslaan en ophalen van geheimen van een key vault. |
| Azure Key Vault | Key Vault is een cryptografische, key management-service die op Federal Information Processing Standards (FIPS) gevalideerde hardware security modules is gebaseerd. Deze standaarden helpen om uw cryptografische sleutels en gevoelige geheimen te beveiligen. Zie voor meer informatie de [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) documentatie. |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) is een door de branche herkende Windows-coderings technologie die wordt gebruikt om schijf versleuteling in te scha kelen op Windows-vm's. |
| BEK | BitLocker-versleutelingssleutels (BEK) worden gebruikt voor het versleutelen van het opstartvolume OS en gegevensvolumes. BEKs worden beveiligd in een key vault als geheimen. |
| Azure-CLI | [De Azure CLI](/cli/azure/install-azure-cli) is geoptimaliseerd voor het beheren en beheer van Azure-resources vanaf de opdrachtregel.|
| DM-Crypt |[DM-cryptografie](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) is het op Linux gebaseerde, transparante schijf versleutelings subsysteem dat wordt gebruikt om schijf versleuteling in te scha kelen op Linux vm's. |
| Sleutel versleutelings sleutel (KEK) | De asymmetrische sleutel (RSA 2048) die u kunt gebruiken om het geheim te beveiligen of in te pakken. U kunt opgeven dat een hardware security module (HSM)- of software beschermde sleutel beschermd. Zie voor meer informatie de [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) documentatie. |
| PowerShell-cmdlets | Zie voor meer informatie, [Azure PowerShell-cmdlets](/powershell/azure/overview). |

## <a name="next-steps"></a>Volgende stappen

Als u aan de slag wilt gaan, raadpleegt u de [Azure Disk Encryption vereisten](azure-security-disk-encryption-prerequisites.md).

