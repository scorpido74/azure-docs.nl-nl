---
title: Azure-beveiligingsbeheer - Gegevensherstel
description: Gegevensherstel van Azure Security Control
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 4f3e8540902809f951a441aa2fe8d00026c44d82
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408600"
---
# <a name="security-control-data-recovery"></a>Beveiligingscontrole: gegevensherstel

Zorg ervoor dat er regelmatig een back-up wordt back-ups van alle systeemgegevens, configuraties en geheimen.

## <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zorg voor regelmatige geautomatiseerde back-ups

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 9.1 | 10.1 | Klant |

Schakel Azure Backup in en configureer de back-upbron (Azure VM's, SQL Server of Bestandsshares) en de gewenste frequentie- en bewaarperiode.

- [Azure Backup inschakelen](https://docs.microsoft.com/azure/backup/)

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Complete systeemback-ups uitvoeren en een back-up maken van beheerde sleutels van een klant

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 9.2 | 10.2 | Klant |

Schakel Azure Backup en target VM(s) in, evenals de gewenste frequentie- en bewaartermijnen. Back-up van door klanten beheerde sleutels binnen Azure Key Vault.

- [Azure Backup inschakelen](https://docs.microsoft.com/azure/backup/)

- [Een back-up maken van sleutelkluissleutels in Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 9.3 | 10.3 | Klant |

Zorg ervoor dat u periodiek gegevensherstel van inhoud binnen Azure Backup uitvoeren. Test restauratie van back-up klant beheerde sleutels.

- [Bestanden herstellen vanaf Azure Virtual Machine-back-up](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm)

- [Sleutelkluissleutels herstellen in Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zorg voor bescherming van back-ups en door klanten beheerde sleutels

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 9.4 | 10.4 | Klant |

Voor on-premises back-ups wordt versleuteling in rust geboden met de wachtwoordzin die u opgeeft wanneer u een back-up maakt in Azure. Voot Azure-VM’s worden gegevens in rust versleuteld met behulp van SSE (Storage Service Encryption). Gebruik op rollen gebaseerd toegangscontrole om back-ups en door klanten beheerde sleutels te beschermen.  

Schakel Soft-Delete en verwijderingsbeveiliging in Key Vault in om sleutels te beschermen tegen onbedoelde of kwaadwillige verwijdering.  Als Azure Storage wordt gebruikt om back-ups op te slaan, schakelt u soft delete in om uw gegevens op te slaan en te herstellen wanneer blobs of blobmomentopnamen worden verwijderd. 

- [Azure RBAC begrijpen](https://docs.microsoft.com/azure/role-based-access-control/overview)

- [Soft-Delete- en zuiveringsbeveiliging inschakelen in Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [Voorlopig verwijderen voor Azure Storage-blobs](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)


## <a name="next-steps"></a>Volgende stappen

- Bekijk de volgende beveiligingscontrole: [incidentrespons](security-control-incident-response.md)