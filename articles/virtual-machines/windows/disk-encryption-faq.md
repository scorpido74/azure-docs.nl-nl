---
title: Veelgestelde vragen-Azure Disk Encryption voor Windows-Vm's
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Microsoft Azure schijf versleuteling voor Windows IaaS-Vm's.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 11/01/2019
ms.custom: seodec18
ms.openlocfilehash: ea2a66a6b012664a9596a02ea32c1a0b677ee3ea
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384273"
---
# <a name="azure-disk-encryption-for-windows-vms-faq"></a>Veelgestelde vragen over Azure Disk Encryption voor Windows Vm's

In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Disk Encryption voor Windows-Vm's. Zie [Azure Disk Encryption Overview](disk-encryption-overview.md)voor meer informatie over deze service.

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Waar is Azure Disk Encryption in algemene beschikbaarheid (GA)?

Azure Disk Encryption is algemene Beschik baarheid in alle open bare Azure-regio's.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Welke gebruiker ondervindt zijn beschikbaar met Azure Disk Encryption?

Azure Disk Encryption-algemene beschikbaarheid biedt ondersteuning voor Azure Resource Manager-sjablonen, Azure PowerShell en Azure CLI. De andere gebruikerservaringen bieden flexibiliteit. Er zijn drie verschillende opties voor het inschakelen van schijf versleuteling voor uw virtuele machines. Zie [Azure Disk Encryption scenario's voor Windows](disk-encryption-windows.md)voor meer informatie over de gebruikers ervaring en stapsgewijze richt lijnen die beschikbaar zijn in azure Disk Encryption.

## <a name="how-much-does-azure-disk-encryption-cost"></a>Wat kost Azure Disk Encryption?

Er worden geen kosten in rekening gebracht voor het versleutelen van VM-schijven met Azure Disk Encryption, maar er zijn kosten verbonden aan het gebruik van Azure Key Vault. Zie de pagina met [Key Vault prijzen](https://azure.microsoft.com/pricing/details/key-vault/) voor meer informatie over Azure Key Vault kosten.

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Hoe kan ik beginnen met Azure Disk Encryption?

Lees het [overzicht van Azure Disk Encryption](disk-encryption-overview.md)om aan de slag te gaan.

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Welke VM-grootten en besturings systemen ondersteunen Azure Disk Encryption?

Het [overzichts artikel Azure Disk Encryption](disk-encryption-overview.md) bevat een lijst met de [VM-grootten](disk-encryption-overview.md#supported-vm-sizes) en [VM-besturings systemen](disk-encryption-overview.md#supported-operating-systems) die ondersteuning bieden voor Azure Disk Encryption.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Kan ik volumes van zowel de opstart- en de gegevens met Azure Disk Encryption coderen?

U kunt zowel opstart-als gegevens volumes versleutelen, maar u kunt de gegevens niet versleutelen zonder eerst het volume van het besturings systeem te versleutelen.

Nadat u het volume van het besturings systeem hebt versleuteld, wordt het uitschakelen van versleuteling op het volume van het besturings systeem niet ondersteund.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Kan ik een niet-gekoppeld volume versleutelen met Azure Disk Encryption?

Nee, Azure Disk Encryption worden alleen gekoppelde volumes versleuteld.

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Hoe kan ik geheimen of versleutelings sleutels draaien?

Als u geheimen wilt draaien, roept u dezelfde opdracht aan die u oorspronkelijk hebt gebruikt om schijf versleuteling in te scha kelen, waarbij u een andere Key Vault opgeeft. Als u de sleutel versleuteling wilt roteren, roept u dezelfde opdracht aan die u oorspronkelijk hebt gebruikt om schijf versleuteling in te scha kelen, waarbij u de nieuwe sleutel versleuteling opgeeft. 

>[!WARNING]
> - Als u eerder Azure Disk Encryption hebt gebruikt [met Azure AD-App](disk-encryption-windows-aad.md) door Azure AD-referenties op te geven voor het versleutelen van deze VM, moet u deze optie blijven gebruiken om uw virtuele machine te versleutelen. U kunt Azure Disk Encryption op deze versleutelde VM niet gebruiken omdat dit geen ondersteund scenario is, wat betekent dat het uitschakelen van de AAD-toepassing voor deze versleutelde virtuele machine niet wordt ondersteund.

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

Er zijn vereisten voor Azure Disk Encryption. Zie de [Azure Disk Encryption met Azure AD](disk-encryption-windows-aad.md) -inhoud om een Azure Active Directory toepassing te maken, een nieuwe sleutel kluis te maken of een bestaande sleutel kluis in te stellen voor toegang tot schijf versleuteling om versleuteling in te scha kelen en geheimen en sleutels te beveiligen. Zie [een sleutel kluis maken en configureren voor Azure Disk Encryption met Azure AD](disk-encryption-key-vault-aad.md)voor meer informatie over de ondersteunings scenario's voor sleutel versleutelings sleutels.

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Is Azure Disk Encryption met behulp van een Azure AD-app (vorige versie) nog steeds ondersteund?
Ja. Schijfversleuteling met behulp van een Azure AD-app wordt nog steeds ondersteund. Bij het versleutelen van nieuwe VM's wordt het echter aanbevolen dat u gebruikt de nieuwe methode in plaats van met een Azure AD-app te coderen. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Kan ik virtuele machines die zijn versleuteld met een Azure AD-app voor de versleuteling zonder een Azure AD-app migreren?
  Op dit moment is er geen een directe migratiepad voor machines die zijn versleuteld met een Azure AD-app voor de versleuteling zonder een Azure AD-app. Daarnaast is er geen een pad van de versleuteling zonder een Azure AD-app naar versleuteling met een AD-app. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Welke versie van Azure PowerShell biedt ondersteuning voor Azure Disk Encryption?

De meest recente versie van de SDK van Azure PowerShell gebruiken om te configureren van Azure Disk Encryption. Down load de nieuwste versie van [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Azure Disk Encryption wordt *niet* ondersteund door de Azure SDK-versie 1.1.0.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Wat is de schijf "Bek Volume" of '/ mnt/azure_bek_disk'?

Het ' bek-volume ' is een lokaal gegevens volume waarmee de versleutelings sleutels voor versleutelde Azure-Vm's veilig worden opgeslagen.

> [!NOTE]
> Niet verwijderen of bewerken van alle inhoud van deze schijf. De schijf niet worden ontkoppeld nadat de belangrijkste aanwezigheid codering nodig is voor versleutelingsbewerkingen op de IaaS-VM.

## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Welke versleutelingsmethode maakt gebruik van Azure Disk Encryption?

Azure Disk Encryption selecteert de versleutelings methode in BitLocker op basis van de volgende versie van Windows:

| Windows-versies                 | Versie | Versleutelings methode        |
|----------------------------------|--------|--------------------------|
| Windows Server 2012, Windows 10 of hoger  | > = 1511 |XTS-AES 256-bits           |
| Windows Server 2012, Windows 8, 8,1, 10 | < 1511 |AES 256 bits *              |
| Windows Server-2008R2            |        |AES 256-bit met diffusor |

\* AES 256 bits met diffuser wordt niet ondersteund in Windows 2012 en hoger.

Als u de versie van het Windows-besturings systeem wilt bepalen, voert u het hulp programma winver uit in de virtuele machine.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Als ik EncryptFormatAll gebruiken en alle typen opgeeft, wordt deze de gegevens op de schijven die we al versleuteld wissen?
Nee, wordt niet gegevens worden gewist van schijven die al zijn versleuteld met Azure Disk Encryption. Net als bij hoe EncryptFormatAll opnieuw de besturingssysteemschijf niet versleutelen, deze wordt niet het station al versleutelde gegevens opnieuw versleutelen. 

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Kan ik een back-up maken van een versleutelde VM en deze herstellen? 

Azure Backup biedt een mechanisme voor het maken van back-ups en het herstellen van versleutelde VM'S binnen hetzelfde abonnement en dezelfde regio.  Zie [back-up en herstel van versleutelde virtuele machines met Azure backup](../../backup/backup-azure-vms-encryption.md)voor instructies.  Het herstellen van een versleutelde VM naar een andere regio wordt momenteel niet ondersteund.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Waar vind ik vragen of feedback geven?

U kunt vragen stellen of feedback geven op het [Azure Disk Encryption forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u meer informatie over de meest voorkomende vragen met betrekking tot Azure Disk Encryption geleerd. Zie de volgende artikelen voor meer informatie over deze service:

- [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)
- [Schijf versleuteling Toep assen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure-gegevens versleuteling in rust](../../security/fundamentals/encryption-atrest.md)
