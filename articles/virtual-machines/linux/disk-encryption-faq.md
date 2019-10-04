---
title: Veelgestelde vragen-Azure Disk Encryption voor Linux-Vm's | Microsoft Docs
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Microsoft Azure schijf versleuteling voor virtuele Linux IaaS-machines.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: f8d31c8df4d073ccd744e792d1316ce02e4bf387
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828645"
---
# <a name="azure-disk-encryption-for-iaas-vms-faq"></a>Veelgestelde vragen over Azure Disk Encryption voor IaaS Vm's

In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Disk Encryption voor Linux-Vm's. Zie [Azure Disk Encryption Overview](disk-encryption-overview.md)voor meer informatie over deze service.

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Waar is Azure Disk Encryption in algemene beschikbaarheid (GA)?

Azure Disk Encryption voor Linux-Vm's is in algemene Beschik baarheid in alle open bare Azure-regio's.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Welke gebruiker ondervindt zijn beschikbaar met Azure Disk Encryption?

Azure Disk Encryption-algemene beschikbaarheid biedt ondersteuning voor Azure Resource Manager-sjablonen, Azure PowerShell en Azure CLI. De andere gebruikerservaringen bieden flexibiliteit. Er zijn drie verschillende opties voor het inschakelen van schijf versleuteling voor uw virtuele machines. Zie [Azure Disk Encryption scenario's voor Linux](disk-encryption-linux.md)voor meer informatie over de gebruikers ervaring en stapsgewijze richt lijnen die beschikbaar zijn in azure Disk Encryption.

## <a name="how-much-does-azure-disk-encryption-cost"></a>Wat kost Azure Disk Encryption?

Er worden geen kosten in rekening gebracht voor het versleutelen van VM-schijven met Azure Disk Encryption, maar er zijn kosten verbonden aan het gebruik van Azure Key Vault. Zie voor meer informatie over de kosten voor Azure Key Vault, de [prijzen voor Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) pagina.

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Hoe kan ik beginnen met Azure Disk Encryption?

Aan de slag, lees de [overzicht van Azure Disk Encryption](disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Welke VM-grootten en besturings systemen ondersteunen Azure Disk Encryption?

Het [overzichts artikel Azure Disk Encryption](disk-encryption-overview.md) bevat een lijst met de [VM-grootten](disk-encryption-overview.md#supported-vm-sizes) en [VM-besturings systemen](disk-encryption-overview.md#supported-operating-systems) die ondersteuning bieden voor Azure Disk Encryption.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Kan ik volumes van zowel de opstart- en de gegevens met Azure Disk Encryption coderen?

Ja, u kunt zowel opstart-als gegevens volumes versleutelen, of u kunt het gegevens volume versleutelen zonder eerst het volume van het besturings systeem te hoeven versleutelen. 

Nadat u het volume van het besturings systeem hebt versleuteld, wordt het uitschakelen van versleuteling op het volume van het besturings systeem niet ondersteund. Voor Linux-Vm's in een schaalset kan alleen het gegevens volume worden versleuteld.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Kan ik een niet-gekoppeld volume versleutelen met Azure Disk Encryption?

Nee, Azure Disk Encryption worden alleen gekoppelde volumes versleuteld.

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Hoe kan ik geheimen of versleutelings sleutels draaien?

Als u geheimen wilt draaien, roept u dezelfde opdracht aan die u oorspronkelijk hebt gebruikt om schijf versleuteling in te scha kelen, waarbij u een andere Key Vault opgeeft. Als u de sleutel versleuteling wilt roteren, roept u dezelfde opdracht aan die u oorspronkelijk hebt gebruikt om schijf versleuteling in te scha kelen, waarbij u de nieuwe sleutel versleuteling opgeeft. 

>[!WARNING]
> - Als u eerder Azure Disk Encryption hebt gebruikt [met Azure AD-App](disk-encryption-linux-aad.md) door Azure AD-referenties op te geven voor het versleutelen van deze VM, moet u deze optie blijven gebruiken om uw virtuele machine te versleutelen. U kunt Azure Disk Encryption op deze versleutelde VM niet gebruiken omdat dit geen ondersteund scenario is, wat betekent dat het uitschakelen van de AAD-toepassing voor deze versleutelde virtuele machine niet wordt ondersteund.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Hoe kan ik een sleutel versleutelings sleutel toevoegen of verwijderen als u deze niet oorspronkelijk hebt gebruikt?

U kunt een sleutel versleutelings sleutel toevoegen door de opdracht inschakelen opnieuw aan te roepen door de para meter Key Encryption Key door te geven. Als u de sleutel versleutelings sleutel wilt verwijderen, roept u de opdracht Enable opnieuw aan zonder de sleutel parameter Key encryption.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Azure Disk Encryption bent u in staat uw eigen sleutel (BYOK)?

Ja, kunt u uw eigen sleutels key-versleuteling opgeven. Deze sleutels worden beveiligd in Azure Key Vault, die het sleutelarchief voor Azure Disk Encryption is. Zie [een sleutel kluis maken en configureren voor Azure Disk Encryption](disk-encryption-key-vault.md)voor meer informatie over de ondersteunde scenario's voor sleutel versleutelings sleutels.

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Kan ik een sleutel gemaakt van Azure key-versleuteling gebruiken?

Ja, u kunt Azure Key Vault gebruiken voor het genereren van een sleutel van versleutelingssleutel voor Azure disk encryption gebruik. Deze sleutels worden beveiligd in Azure Key Vault, die het sleutelarchief voor Azure Disk Encryption is. Zie voor meer informatie over de sleutel versleutelings sleutel [een sleutel kluis maken en configureren voor Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Kan ik een on-premises-service voor sleutelbeheer of een HSM gebruiken ter bescherming van de versleutelingssleutels?

U kunt de service voor sleutelbeheer on-premises of de HSM niet gebruiken ter bescherming van de versleutelingssleutels met Azure Disk Encryption. U kunt alleen de Azure Key Vault-service gebruiken ter bescherming van de versleutelingssleutels. Zie [een sleutel kluis maken en configureren voor Azure Disk Encryption](disk-encryption-key-vault.md)voor meer informatie over de ondersteunings scenario's voor sleutel versleutelings sleutels.

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Wat zijn de vereisten voor het configureren van Azure Disk Encryption?

Er zijn vereisten voor Azure Disk Encryption. Zie het artikel [een sleutel kluis maken en configureren voor Azure Disk Encryption](disk-encryption-key-vault.md) om een nieuwe sleutel kluis te maken of een bestaande sleutel kluis in te stellen voor toegang tot schijf versleuteling om versleuteling in te scha kelen en geheimen en sleutels te beveiligen. Zie [een sleutel kluis maken en configureren voor Azure Disk Encryption](disk-encryption-key-vault.md)voor meer informatie over de ondersteunings scenario's voor sleutel versleutelings sleutels.

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Wat zijn de vereisten voor Azure Disk Encryption configureren met een Azure AD-app (vorige versie)?

Er zijn vereisten voor Azure Disk Encryption. Zie de [Azure Disk Encryption met Azure AD](disk-encryption-linux-aad.md) -inhoud om een Azure Active Directory toepassing te maken, een nieuwe sleutel kluis te maken of een bestaande sleutel kluis in te stellen voor toegang tot schijf versleuteling om versleuteling in te scha kelen en geheimen en sleutels te beveiligen. Zie [een sleutel kluis maken en configureren voor Azure Disk Encryption met Azure AD](disk-encryption-key-vault-aad.md)voor meer informatie over de ondersteunings scenario's voor sleutel versleutelings sleutels.

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Is Azure Disk Encryption met behulp van een Azure AD-app (vorige versie) nog steeds ondersteund?
Ja. Schijfversleuteling met behulp van een Azure AD-app wordt nog steeds ondersteund. Bij het versleutelen van nieuwe VM's wordt het echter aanbevolen dat u gebruikt de nieuwe methode in plaats van met een Azure AD-app te coderen. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Kan ik virtuele machines die zijn versleuteld met een Azure AD-app voor de versleuteling zonder een Azure AD-app migreren?
  Op dit moment is er geen een directe migratiepad voor machines die zijn versleuteld met een Azure AD-app voor de versleuteling zonder een Azure AD-app. Daarnaast is er geen een pad van de versleuteling zonder een Azure AD-app naar versleuteling met een AD-app. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Welke versie van Azure PowerShell biedt ondersteuning voor Azure Disk Encryption?

De meest recente versie van de SDK van Azure PowerShell gebruiken om te configureren van Azure Disk Encryption. Download de nieuwste versie van [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Azure Disk Encryption is *niet* ondersteund door Azure SDK versie 1.1.0.

> [!NOTE]
> De voorbeeld uitbreiding micro soft. OSTCExtension. AzureDiskEncryptionForLinux van Linux Azure Disk Encryption is afgeschaft. Deze uitbrei ding is gepubliceerd voor de preview-versie van Azure Disk Encryption. Gebruik de preview-versie van de uitbrei ding niet in uw test-of productie-implementatie.

> Voor implementatie scenario's als Azure Resource Manager (ARM), waar u de Azure Disk Encryption-extensie voor Linux VM moet implementeren om versleuteling in te scha kelen op uw Linux IaaS VM, moet u de Azure Disk Encryption-extensie productie ondersteund gebruiken ' Micro soft. Azure. Security. AzureDiskEncryptionForLinux.

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Kan ik Azure Disk Encryption toepassen op mijn aangepaste installatiekopie van Linux?

U kunt geen Azure Disk Encryption toepassen op uw aangepaste installatiekopie van Linux. Alleen de galerie met Linux-installatiekopieën voor de ondersteunde distributies eerder genoemd, worden ondersteund. Aangepaste Linux-installatiekopieën worden momenteel niet ondersteund.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Kan ik updates toepassen op een Linux Red Hat virtuele machine die gebruikmaakt van de update yum?

Ja, u kunt een yum-update uitvoeren op een Red Hat Linux-VM.  Zie [Linux-pakket beheer achter een firewall](disk-encryption-troubleshooting.md#linux-package-management-behind-a-firewall)voor meer informatie.

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Wat is de aanbevolen Azure disk encryption-werkstroom voor Linux?

De volgende werkstroom wordt aanbevolen om de beste resultaten op Linux:
* Starten vanuit de ongewijzigd aandelen galerijafbeelding overeenkomt met de benodigde OS-distributie en -versie
* Back-up van gekoppelde stations die worden versleuteld.  Terug hierdoor tot herstellen als er een storing optreedt, bijvoorbeeld als de virtuele machine opnieuw opgestart is voordat de codering is voltooid.
* Versleutelen (duurt enkele uren of zelfs dagen, afhankelijk van de kenmerken van de virtuele machine en de grootte van alle gekoppelde gegevensschijven)
* Aanpassen en software toevoegen aan de installatiekopie, indien nodig.

Als deze werkstroom niet mogelijk is, is afhankelijk [Storage Service Encryption](../../storage/common/storage-service-encryption.md) (SSE) op de platform-opslag is mogelijk account laag een alternatief voor volledige schijfversleuteling met behulp van dm-crypt.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Wat is de schijf "Bek Volume" of '/ mnt/azure_bek_disk'?

Het ' bek-volume ' is een lokaal gegevens volume waarmee de versleutelings sleutels voor versleutelde Azure-Vm's veilig worden opgeslagen.
> [!NOTE]
> Niet verwijderen of bewerken van alle inhoud van deze schijf. De schijf niet worden ontkoppeld nadat de belangrijkste aanwezigheid codering nodig is voor versleutelingsbewerkingen op de IaaS-VM.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Welke versleutelingsmethode maakt gebruik van Azure Disk Encryption?

Azure Disk Encryption maakt gebruik van de ontsleutelen standaard van AES-XTS-plain64 met een 256-bits volume hoofd sleutel.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Als ik EncryptFormatAll gebruiken en alle typen opgeeft, wordt deze de gegevens op de schijven die we al versleuteld wissen?
Nee, wordt niet gegevens worden gewist van schijven die al zijn versleuteld met Azure Disk Encryption. Net als bij hoe EncryptFormatAll opnieuw de besturingssysteemschijf niet versleutelen, deze wordt niet het station al versleutelde gegevens opnieuw versleutelen. Zie voor meer informatie de [EncryptFormatAll criteria](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).        

## <a name="is-xfs-filesystem-supported"></a>Wordt XFS-bestands systeem ondersteund?
XFS-volumes worden alleen ondersteund voor versleuteling van de gegevens schijf met de EncryptFormatAll. Hiermee wordt het volume opnieuw geformatteerd en worden alle eerder aanwezige gegevens gewist. Zie voor meer informatie de [EncryptFormatAll criteria](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Kan ik een back-up maken van een versleutelde VM en deze herstellen? 

Azure Backup biedt een mechanisme voor het maken van back-ups en het herstellen van versleutelde VM'S binnen hetzelfde abonnement en dezelfde regio.  Zie [back-up en herstel van versleutelde virtuele machines met Azure backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)voor instructies.  Het herstellen van een versleutelde VM naar een andere regio wordt momenteel niet ondersteund.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Waar vind ik vragen of feedback geven?

U kunt vragen of feedback geven over de [forum voor Azure Disk Encryption](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u meer informatie over de meest voorkomende vragen met betrekking tot Azure Disk Encryption geleerd. Zie de volgende artikelen voor meer informatie over deze service:

- [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)
- [Schijfversleuteling in Azure Security Center toepassen](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure gegevensversleuteling in rust](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
