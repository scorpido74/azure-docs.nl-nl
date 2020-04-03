---
title: Veelgestelde vragen - Azure Disk Encryption voor Linux VM's
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Microsoft Azure Disk Encryption for Linux IaaS VM's.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 0f3d6580b738a77de9654de0df9b4ce1120fc6eb
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584041"
---
# <a name="azure-disk-encryption-for-iaas-vms-faq"></a>Veelgestelde vragen over Azure Disk Encryption for IaaS VMs

In dit artikel vindt u antwoorden op veelgestelde vragen (FAQ) over Azure Disk Encryption for Linux VM's. Zie [overzicht azure disk-encryptie](disk-encryption-overview.md)voor meer informatie over deze service.

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Waar is Azure Disk Encryption in algemene beschikbaarheid (GA)?

Azure Disk Encryption for Linux VM's is in algemene beschikbaarheid in alle openbare Azure-regio's.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Welke gebruikerservaringen zijn beschikbaar met Azure Disk Encryption?

Azure Disk Encryption GA ondersteunt Azure Resource Manager-sjablonen, Azure PowerShell en Azure CLI. De verschillende gebruikerservaringen geven u flexibiliteit. U hebt drie verschillende opties voor het inschakelen van schijfversleuteling voor uw VM's. Zie [Azure Disk Encryption-scenario's voor Linux voor](disk-encryption-linux.md)meer informatie over de gebruikerservaring en stapsgewijze richtlijnen die beschikbaar zijn in Azure Disk Encryption.

## <a name="how-much-does-azure-disk-encryption-cost"></a>Hoeveel kost Azure Disk Encryption?

Er zijn geen kosten verbonden aan het versleutelen van VM-schijven met Azure Disk Encryption, maar er zijn kosten verbonden aan het gebruik van Azure Key Vault. Zie de [prijspagina Key Vault voor](https://azure.microsoft.com/pricing/details/key-vault/) meer informatie over de kosten van Azure Key Vault.

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Hoe kan ik Azure Disk Encryption gaan gebruiken?

Lees het overzicht [Azure Disk Encryption](disk-encryption-overview.md)om aan de slag te gaan.

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Welke VM-formaten en besturingssystemen ondersteunen Azure Disk Encryption?

In het [overzichtsartikel azure disk-versleuteling](disk-encryption-overview.md) worden de [VM-formaten](disk-encryption-overview.md#supported-vms) en [VM-besturingssystemen](disk-encryption-overview.md#supported-operating-systems) weergegeven die Azure Disk Encryption ondersteunen.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Kan ik zowel opstart- als gegevensvolumes versleutelen met Azure Disk Encryption?

Ja, u zowel opstart- als gegevensvolumes versleutelen, of u het gegevensvolume versleutelen zonder dat u eerst het besturingssysteemvolume hoeft te versleutelen. 

Nadat u het besturingssysteemvolume hebt versleuteld, wordt het uitschakelen van versleuteling op het besturingssysteemvolume niet ondersteund. Voor Linux VM's in een schaalset kan alleen het gegevensvolume worden versleuteld.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Kan ik een niet-gemonteerd volume versleutelen met Azure Disk Encryption?

Nee, Azure Disk Encryption versleutelt alleen gemonteerde volumes.

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Hoe draai ik geheimen of encryptiesleutels?

Als u geheimen wilt roteren, belt u gewoon dezelfde opdracht die u oorspronkelijk hebt gebruikt om schijfversleuteling in te schakelen, waarbij een andere Sleutelkluis wordt opgegeven. Als u de sleutelversleutelingssleutel wilt roteren, roept u dezelfde opdracht aan die u oorspronkelijk hebt gebruikt om schijfversleuteling in te schakelen, waarbij de nieuwe sleutelversleuteling wordt opgegeven. 

>[!WARNING]
> - Als u [azure-schijfversleuteling](disk-encryption-linux-aad.md) eerder hebt gebruikt met de Azure AD-app door Azure AD-referenties op te geven om deze vm te versleutelen, moet u deze optie blijven gebruiken om uw vm te versleutelen. U Azure Disk Encryption op deze versleutelde VM niet gebruiken, omdat dit geen ondersteund scenario is, wat betekent dat het overschakelen van AAD-toepassing voor deze versleutelde VM nog niet wordt ondersteund.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Hoe kan ik een sleutelversleutelingssleutel toevoegen of verwijderen als ik er oorspronkelijk geen heb gebruikt?

Als u een sleutelversleutelingssleutel wilt toevoegen, roept u de opdracht Inschakelen opnieuw aan bij het passeren van de parameter sleutelversleutelingssleutel. Als u een sleutelversleutelingssleutel wilt verwijderen, roept u de opdracht inschakelen opnieuw aan zonder de parameter sleutelversleutelingssleutel.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>u met Azure Disk Encryption uw eigen sleutel (BYOK) meenemen?

Ja, u uw eigen sleutelsleutelen leveren. Deze sleutels worden beveiligd in Azure Key Vault, de sleutelopslag voor Azure Disk Encryption. Zie [Een sleutelkluis voor Azure Disk Encryption maken en configureren voor](disk-encryption-key-vault.md)meer informatie over de ondersteuningsscenario's voor sleutelversleuteling.

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Kan ik een door Azure gemaakte sleutelversleutelingssleutel gebruiken?

Ja, u Azure Key Vault gebruiken om een sleutelversleutelingssleutel te genereren voor het gebruik van Azure-schijfversleuteling. Deze sleutels worden beveiligd in Azure Key Vault, de sleutelopslag voor Azure Disk Encryption. Zie [Een sleutelkluis voor Azure Disk Encryption maken en configureren voor](disk-encryption-key-vault.md)meer informatie over de sleutelversleutelingssleutel.

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Kan ik een on-premises key management service of HSM gebruiken om de encryptiesleutels te beveiligen?

U de on-premises key management service of HSM niet gebruiken om de versleutelingssleutels te beveiligen met Azure Disk Encryption. U de Azure Key Vault-service alleen gebruiken om de versleutelingssleutels te beveiligen. Zie [Een sleutelkluis voor Azure Disk Encryption maken en configureren voor](disk-encryption-key-vault.md)meer informatie over de ondersteuningsscenario's voor sleutelversleuteling.

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Wat zijn de vereisten voor het configureren van Azure Disk Encryption?

Er zijn vereisten voor Azure Disk Encryption. Raadpleeg het artikel [Een sleutelkluis maken en configureren voor Azure Disk Encryption](disk-encryption-key-vault.md) om een nieuwe sleutelkluis te maken of stel een bestaande sleutelkluis in voor toegang tot schijfversleuteling om versleuteling in te schakelen en geheimen en sleutels te beveiligen. Zie [Een sleutelkluis voor Azure Disk Encryption maken en configureren voor](disk-encryption-key-vault.md)meer informatie over de ondersteuningsscenario's voor sleutelversleuteling.

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Wat zijn de vereisten om Azure Disk Encryption te configureren met een Azure AD-app (vorige release)?

Er zijn vereisten voor Azure Disk Encryption. Raadpleeg de [Azure Disk Encryption met Azure AD-inhoud](disk-encryption-linux-aad.md) om een Azure Active Directory-toepassing te maken, een nieuwe sleutelkluis te maken of een bestaande sleutelkluis in te stellen voor toegang tot schijfversleuteling om versleuteling in te schakelen en geheimen en sleutels te beveiligen. Zie [Een sleutelkluis voor Azure Disk Encryption maken en configureren met Azure AD voor](disk-encryption-key-vault-aad.md)meer informatie over de ondersteuningsscenario's voor sleutelversleuteling.

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Wordt Azure Disk Encryption met behulp van een Azure AD-app (vorige release) nog steeds ondersteund?
Ja. Schijfversleuteling met behulp van een Azure AD-app wordt nog steeds ondersteund. Bij het versleutelen van nieuwe VM's wordt echter aanbevolen om de nieuwe methode te gebruiken in plaats van te versleutelen met een Azure AD-app. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Kan ik VM's die zijn versleuteld met een Azure AD-app migreren naar versleuteling zonder een Azure AD-app?
  Momenteel is er geen direct migratiepad voor machines die zijn versleuteld met een Azure AD-app naar versleuteling zonder een Azure AD-app. Bovendien is er geen direct pad van versleuteling zonder een Azure AD-app naar versleuteling met een AD-app. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Welke versie van Azure PowerShell ondersteunt Azure Disk Encryption?

Gebruik de nieuwste versie van de Azure PowerShell SDK om Azure Disk Encryption te configureren. Download de nieuwste versie van [Azure PowerShell.](https://github.com/Azure/azure-powershell/releases) Azure Disk Encryption wordt *niet* ondersteund door Azure SDK versie 1.1.0.

> [!NOTE]
> De Linux Azure disk encryption preview extensie "Microsoft.OSTCExtension.AzureDiskEncryptionForLinux" is afgeschaft. Deze extensie is gepubliceerd voor azure disk encryption preview release. U mag de voorbeeldversie van de extensie niet gebruiken in uw test- of productie-implementatie.

> Voor implementatiescenario's zoals Azure Resource Manager (ARM), waarbij u azure-schijfversleutelingsextensie voor Linux VM moet implementeren om versleuteling op uw Linux IaaS VM mogelijk te maken, moet u de ondersteunde azure-schijfversleutelingsextensie "Microsoft.Azure.Security.AzureDiskEncryptionForLinux" gebruiken.

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Kan ik Azure Disk Encryption toepassen op mijn aangepaste Linux-afbeelding?

U Azure Disk Encryption niet toepassen op uw aangepaste Linux-afbeelding. Alleen de galerij Linux beelden voor de ondersteunde distributies opgeroepen eerder worden ondersteund. Aangepaste Linux-afbeeldingen worden momenteel niet ondersteund.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Kan ik updates toepassen op een Linux Red Hat VM die de yum-update gebruikt?

Ja, u een yum-update uitvoeren op een Red Hat Linux VM.  Zie [Azure Disk Encryption op een geïsoleerd netwerk](disk-encryption-isolated-network.md)voor meer informatie.

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Wat is de aanbevolen Azure-schijfversleutelingsworkflow voor Linux?

De volgende workflow wordt aanbevolen om de beste resultaten op Linux te hebben:
* Start vanaf de ongewijzigde stockgalerijafbeelding die overeenkomt met de benodigde os-distro en -versie
* Een back-up van alle gemonteerde schijven die zullen worden versleuteld.  Deze back-up maakt herstel mogelijk als er een storing optreedt, bijvoorbeeld als de VM opnieuw wordt opgestart voordat de versleuteling is voltooid.
* Versleutelen (kan enkele uren of zelfs dagen duren, afhankelijk van vm-kenmerken en grootte van aangesloten gegevensschijven)
* Pas aan en voeg indien nodig software toe aan de afbeelding.

Als deze werkstroom niet mogelijk is, kan een beroep op [Storage Service Encryption](../../storage/common/storage-service-encryption.md) (SSE) op de accountlaag van het platform een alternatief zijn voor volledige schijfversleuteling met behulp van dm-crypt.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Wat is de schijf "Bek Volume" of "/mnt/azure_bek_disk"?

Het 'Bek-volume' is een lokaal gegevensvolume dat de coderingssleutels voor versleutelde Azure-vm's veilig opslaat.
> [!NOTE]
> Verwijder of bewerk geen inhoud in deze schijf. Maak de schijf niet los omdat de aanwezigheid van de versleutelingssleutel nodig is voor versleutelingsbewerkingen op de IaaS VM.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Welke versleutelingsmethode gebruikt Azure Disk Encryption?

Azure Disk Encryption maakt gebruik van de standaarddecode standaard van aes-xts-plain64 met een 256-bits volumemastersleutel.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Als ik EncryptFormatAll gebruik en alle volumetypen opgeef, worden de gegevens dan gewist op de gegevensstations die we al hebben versleuteld?
Nee, gegevens worden niet gewist uit gegevensstations die al zijn versleuteld met Azure Disk Encryption. Vergelijkbaar met hoe EncryptFormatAll het osstation niet opnieuw heeft versleuteld, zal het het reeds versleutelde gegevensstation niet opnieuw versleutelen. Zie de criteria [EncryptFormatAll](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms)voor meer informatie .        

## <a name="is-xfs-filesystem-supported"></a>Wordt XFS filesystem ondersteund?
XFS-volumes worden alleen ondersteund voor versleuteling van gegevensschijven met de EncryptFormatAll. Dit zal het volume opnieuw opvoeren en alle gegevens die er eerder waren gewist, worden gewist. Zie de criteria [EncryptFormatAll](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms)voor meer informatie .

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Kan ik een back-up maken en een versleutelde VM herstellen? 

Azure Backup biedt een mechanisme voor het back-upen en herstellen van versleutelde VM's binnen hetzelfde abonnement en dezelfde regio.  Zie [Back-upmaken en versleutelde virtuele machines herstellen met Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)voor instructies.  Het herstellen van een versleutelde VM naar een andere regio wordt momenteel niet ondersteund.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Waar kan ik terecht om vragen te stellen of feedback te geven?

U vragen stellen of feedback geven op het [Azure Disk Encryption-forum.](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption)

## <a name="next-steps"></a>Volgende stappen
In dit document leert u meer over de meest voorkomende vragen met betrekking tot Azure Disk Encryption. Zie de volgende artikelen voor meer informatie over deze service:

- [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)
- [Schijfversleuteling toepassen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure-gegevensversleuteling in rust](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
