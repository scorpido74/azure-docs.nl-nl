---
title: Azure-beveiligingsbeheer - Gegevensherstel
description: Gegevensherstel van beveiligingscontrole
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: c585ebd903d4070f6247456e06efffbc6ec45270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934493"
---
# <a name="security-control-data-recovery"></a>Beveiligingscontrole: gegevensherstel

Zorg ervoor dat er regelmatig een back-up wordt back-ups van alle systeemgegevens, configuraties en geheimen.

## <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zorg voor regelmatige geautomatiseerde back-ups

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 9.1 | 10.1 | Klant |

Schakel Azure Backup in en configureer de back-upbron (Azure VM's, SQL Server of Bestandsshares) en de gewenste frequentie- en bewaarperiode.

Azure Backup inschakelen:

https://docs.microsoft.com/azure/backup/

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Complete systeemback-ups uitvoeren en een back-up maken van beheerde sleutels van een klant

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 9.2 | 10.2 | Klant |

Schakel Azure Backup en target VM(s) in, evenals de gewenste frequentie- en bewaartermijnen. Back-up van door klanten beheerde sleutels binnen Azure Key Vault.

Azure Backup inschakelen:

https://docs.microsoft.com/azure/backup/

Een back-up maken van sleutelkluissleutels in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 9.3 | 10.3 | Klant |

Zorg ervoor dat u periodiek gegevensherstel van inhoud binnen Azure Backup uitvoeren. Test indien nodig herstel naar een geïsoleerde VLAN. Test restauratie van back-up klant beheerde sleutels.

Bestanden herstellen vanaf azure virtual machine-back-up:

https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm

Sleutelkluissleutels herstellen in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zorg voor bescherming van back-ups en door klanten beheerde sleutels

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 9.4 | 10.4 | Klant |

Voor on-premises back-ups wordt versleuteling in rust geboden met de wachtwoordzin die u opgeeft wanneer u een back-up maakt in Azure. Voot Azure-VM’s worden gegevens in rust versleuteld met behulp van SSE (Storage Service Encryption). U Soft-Delete in Key Vault inschakelen om sleutels te beschermen tegen onbedoelde of kwaadwillige verwijdering.

Soft-Delete inschakelen in Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

## <a name="next-steps"></a>Volgende stappen

Bekijk de volgende beveiligingscontrole: [Incident Response](security-control-incident-response.md)
