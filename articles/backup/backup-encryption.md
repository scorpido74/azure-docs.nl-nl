---
title: Versleuteling in Azure Backup
description: Meer informatie over hoe versleutelings functies in Azure Backup u helpen uw back-upgegevens te beschermen en te voldoen aan de beveiligings behoeften van uw bedrijf.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: ca570cfdc6e78e712715ba075168f4b06c55e4af
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116553"
---
# <a name="encryption-in-azure-backup"></a>Versleuteling in Azure Backup

Al uw back-upgegevens worden automatisch versleuteld wanneer deze worden opgeslagen in de Cloud met behulp van Azure Storage versleuteling, waarmee u kunt voldoen aan de verplichtingen voor beveiliging en naleving. Deze gegevens in rust worden versleuteld met 256-bits AES-versleuteling, een van de krach tigste blok cijfers die beschikbaar zijn en is compatibel met FIPS 140-2.

Naast versleuteling in rust worden al uw back-upgegevens overgebracht via HTTPS. Het blijft altijd op het Azure-backbone-netwerk.

Zie [Azure Storage versleuteling voor Data-at-rest](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)voor meer informatie. Raadpleeg de [Veelgestelde vragen over Azure backup](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) voor het beantwoorden van vragen die u mogelijk hebt over versleuteling.

## <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Versleuteling van back-upgegevens met door het platform beheerde sleutels

Standaard worden al uw gegevens versleuteld met sleutels die door het platform worden beheerd. U hoeft geen expliciete actie van uw end uit te voeren om deze versleuteling in te scha kelen en is van toepassing op alle werk belastingen waarvan een back-up wordt gemaakt naar uw Recovery Services kluis.

## <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Versleuteling van back-upgegevens met door de klant beheerde sleutels

Wanneer u een back-up maakt van uw Azure-Virtual Machines, kunt u uw gegevens nu versleutelen met sleutels die eigendom zijn van en worden beheerd door u. Met Azure Backup kunt u uw RSA-sleutels gebruiken die zijn opgeslagen in de Azure Key Vault voor het versleutelen van uw back-ups. De versleutelings sleutel die wordt gebruikt voor het versleutelen van back-ups is mogelijk anders dan die voor de bron. De gegevens worden beveiligd met behulp van een AES 256-gegevens versleutelings sleutel (DEK), die op zijn beurt wordt beveiligd met uw sleutels. Hiermee krijgt u volledige controle over de gegevens en de sleutels. Als u versleuteling wilt toestaan, moet de Recovery Services-kluis toegang krijgen tot de versleutelings sleutel in de Azure Key Vault. U kunt de sleutel uitschakelen of toegang intrekken wanneer dit nodig is. U moet echter versleuteling inschakelen met behulp van uw sleutels voordat u de items in de kluis probeert te beveiligen.

>[!NOTE]
>Deze functie is momenteel beperkt beschikbaar. Vul [deze enquête](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u) in en stuur ons een e-mail AskAzureBackupTeam@microsoft.com naar als u uw back-upgegevens wilt versleutelen met door de klant beheerde sleutels. Houd er rekening mee dat de mogelijkheid om deze functie te gebruiken afhankelijk is van de Azure Backup service.

## <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>Back-up van virtuele machines met beheerde schijven versleuteld met door de klant beheerde sleutels

Met Azure Backup kunt u ook een back-up maken van uw Azure-Vm's die gebruikmaken van uw sleutel voor het [versleutelen van opslag Services](https://docs.microsoft.com/azure/storage/common/storage-service-encryption). De sleutel die wordt gebruikt voor het versleutelen van de schijven wordt opgeslagen in de Azure Key Vault en door u worden beheerd. Storage Service Encryption (SSE) met door de klant beheerde sleutels verschilt van Azure Disk Encryption, omdat ADE gebruikmaakt van BitLocker (voor Windows) en DM-crypt (voor Linux) voor het uitvoeren van in-gast versleuteling, worden gegevens in de opslag service door SSE versleuteld, zodat u elk besturings systeem of installatie kopieën voor uw virtuele machines kunt gebruiken. Raadpleeg voor meer informatie de [versleuteling van Managed disks met door de klant beheerde sleutels](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys) .

## <a name="infrastructure-level-encryption-for-backup-data"></a>Versleuteling op infrastructuur niveau voor back-upgegevens

Naast het versleutelen van uw gegevens in de Recovery Services kluis met door de klant beheerde sleutels, kunt u er ook voor kiezen om een extra laag versleuteling te configureren voor de opslag infrastructuur. Deze infra structuur versleuteling wordt beheerd door het platform en samen met de versleutelings functie met door de klant beheerde sleutels, is het mogelijk om twee lagen versleuteling van uw back-upgegevens te maken. U moet erop gewezen dat infrastructuur versleuteling alleen kan worden geconfigureerd als u ervoor kiest om uw eigen sleutels te gebruiken voor versleuteling in rust. Infrastructuur versleuteling maakt gebruik van door het platform beheerde sleutels voor het versleutelen van gegevens.

>[!NOTE]
>Infrastructuur versleuteling is momenteel in een beperkte preview-versie en is alleen beschikbaar in de regio's VS-Oost, VS-West2 en VS Zuid-Centraal. Als u de functie in een van deze regio's wilt gebruiken, vult u [dit formulier](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUN0VHNEpJS0ZUWklUNVdJSTEzR0hIOVRMVC4u) in en gaat u een e-mail sturen naar [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="backup-of-vms-encrypted-using-ade"></a>Back-up van virtuele machines die zijn versleuteld met ADE

Met Azure Backup kunt u ook een back-up maken van uw virtuele Azure-machines waarvan het besturings systeem of de gegevens schijven zijn versleuteld met behulp van Azure Disk Encryption. ADE maakt gebruik van BitLocker voor Windows-Vm's en DM-crypt voor Linux-Vm's voor het uitvoeren van in-gast versleuteling. Zie [back-up en herstel van versleutelde virtuele machines met Azure backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Back-ups maken en herstellen van een versleutelde Azure VM](backup-azure-vms-encryption.md)
