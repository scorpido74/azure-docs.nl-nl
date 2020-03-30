---
title: Diagnostische instellingen gebruiken voor Vaults van Recovery Services
description: Een artikel waarin wordt beschreven hoe de oude en nieuwe diagnostische gebeurtenissen voor Azure Backup kunnen worden gebruikt
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: e3919d120e5f741af6cd30dd27e5a1dfa2b06cf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136936"
---
# <a name="using-diagnostics-settings-for-recovery-services-vaults"></a>Diagnostische instellingen gebruik voor Recovery Services-kluizen

Azure Backup verzendt diagnostische gebeurtenissen die kunnen worden verzameld en gebruikt voor analyse, waarschuwingen en rapportage. 

U diagnostische instellingen voor een Vault voor Herstelservices configureren via de Azure-portal, door naar de kluis te navigeren en op het **menu-item Diagnostische instellingen** te klikken. Als u op **+ Diagnostische instelling toevoegen** klikt, u een of meer diagnostische gebeurtenissen verzenden naar een opslagaccount, gebeurtenishub of een werkruimte voor Logboekanalyse (LA).

![Blade diagnostische instellingen](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Diagnostische gebeurtenissen beschikbaar voor Azure Backup Users

Azure Backup biedt de volgende diagnostische gebeurtenissen, die elk gedetailleerde gegevens bevatten over een specifieke set back-upgerelateerde artefacten:

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage

[Gegevensmodel voor Azure Backup Diagnostics-gebeurtenissen](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)

Gegevens voor deze gebeurtenissen kunnen worden verzonden naar een opslagaccount, een LA-werkruimte of een gebeurtenishub. Als u deze gegevens naar een LA Workspace verzendt, moet u de optie **Resourcespecifiek** selecteren in het scherm **Diagnostische instelling** (zie meer informatie in de onderstaande secties).

## <a name="using-diagnostics-settings-with-log-analytics-la"></a>Diagnostische instellingen gebruiken met Log Analytics (LA)

Azure Backup sluit zich aan op de Azure Log Analytics-roadmap en stelt u nu in staat om brondiagnostische gegevens naar speciale LA-tabellen voor back-up te verzenden. Deze worden [resourcespecifieke tabellen](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific)genoemd.

Ga als u uw kluisdiagnosegegevens naar LA sturen:

1.  Navigeer naar uw kluis en klik op **Diagnostische instellingen**. Klik **op + Diagnostische instelling toevoegen**.
2.  Geef een naam aan de instelling Diagnostische gegevens.
3.  Schakel het selectievakje **Verzenden naar logboekanalyse** in en selecteer een Logboekanalysewerkruimte.
4.  Selecteer **Resourcespecifiek** in de schakeloptie en controleer de volgende zes gebeurtenissen - **CoreAzureBackup**, **AddonAzureBackupAlerts**, **AddonAzureBackupProtectedInstance**, **AddonAzureBackupJobs**, **AddonAzureBackupPolicy**en **AddonAzureBackupStorage**.
5.  Klik op **Opslaan**.

![Resourcespecifieke modus](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Zodra gegevens in de LA Workspace worden ingevoerd, worden er speciale tabellen voor elk van deze gebeurtenissen gemaakt in uw werkruimte. U een van deze tabellen rechtstreeks opvragen en indien nodig ook joins of vakbonden tussen deze tabellen uitvoeren.

> [!IMPORTANT]
> De bovenstaande zes gebeurtenissen, namelijk CoreAzureBackup, AddonAzureBackupAlerts, AddonAzureBackupProtectedInstance, AddonAzureBackupJobs, AddonAzureBackupPolicy en AddonAzureBackupStorage, worden **alleen** ondersteund in resourcespecifieke modus. **Houd er rekening mee dat als u gegevens probeert te verzenden voor deze zes gebeurtenissen in de Azure Diagnostics Mode, er geen gegevens naar de LA Workspace worden verzonden.**

## <a name="legacy-event"></a>Legacy-evenement

Traditioneel zijn alle back-upgerelateerde diagnostische gegevens voor een kluis opgenomen in één gebeurtenis genaamd 'AzureBackupReport'. De zes hierboven beschreven gebeurtenissen zijn in wezen een ontleding van alle gegevens in AzureBackupReport. 

Momenteel blijven we de AzureBackupReport-gebeurtenis ondersteunen voor achterwaartse compatibiliteit, in gevallen waarin gebruikers bestaande aangepaste query's op deze gebeurtenis hebben, bijvoorbeeld aangepaste logboekwaarschuwingen, aangepaste visualisaties enz. We raden echter **aan om zo vroeg mogelijk naar de nieuwe gebeurtenissen te gaan,** omdat dit de gegevens veel gemakkelijker maakt om mee te werken in logboekquery's, een betere vindbaarheid van schema's en hun structuur biedt, de prestaties verbetert in zowel de innamelaten als de querytijden. **Ondersteuning voor het gebruik van de Azure Diagnostics-modus wordt uiteindelijk geleidelijk afgeschaft en dus kan het kiezen van de nieuwe gebeurtenissen u helpen om complexe migraties op een later tijdstip te voorkomen.**

Het ingebouwde beleid van Azure Backup gebruiken om een nieuwe diagnostische instelling toe te voegen aan de 6 nieuwe gebeurtenissen, voor al uw kluizen in een bepaald bereik: [Vault Diagnostics Settings op schaal configureren](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)

U ervoor kiezen om afzonderlijke diagnostische instellingen voor AzureBackupReport en de zes nieuwe gebeurtenissen te maken, totdat u al uw aangepaste query's hebt gemigreerd om gegevens uit de nieuwe tabellen te gebruiken. De onderstaande afbeelding toont een voorbeeld van een kluis met twee diagnostische instellingen. De eerste instelling, met de naam **Instelling1,** verzendt gegevens van de gebeurtenis AzureBackupReport naar een LA Workspace in de AzureDiagnostics-modus. De tweede instelling, met de naam **Instelling2,** verzendt gegevens van de zes nieuwe Azure Backup-gebeurtenissen naar een LA Workspace in de modus Resourcespecifiek.

![Twee instellingen](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> De gebeurtenis AzureBackupReport wordt **alleen** ondersteund in de Azure Diagnostics-modus. **Houd er rekening mee dat als u gegevens voor deze gebeurtenis probeert te verzenden in resourcespecifieke modus, er geen gegevens naar de LA Workspace worden verzonden.**

> [!NOTE]
> De schakelaar voor Azure Diagnostics / Resource Specific wordt alleen weergegeven als de gebruiker **Send to Log Analytics**controleert. Als u gegevens naar een opslagaccount of een gebeurtenishub wilt verzenden, kan een gebruiker eenvoudig de vereiste bestemming selecteren en een van de gewenste gebeurtenissen controleren, zonder extra invoer. Nogmaals, het wordt aanbevolen om de verouderde gebeurtenis AzureBackupReport niet te kiezen, in de toekomst.

## <a name="users-sending-azure-site-recovery-events-to-log-analytics-la"></a>Gebruikers die Azure Site Recovery Events verzenden naar Log Analytics (LA)

Azure Backup- en Azure Site Recovery-gebeurtenissen worden verzonden vanuit dezelfde Vault voor Herstelservices. Aangezien Azure Site Recovery momenteel niet is aangesloten op resourcespecifieke tabellen, worden gebruikers die Azure Site Recovery Events naar LA willen **verzenden,** alleen naar azure diagnostics Mode geleid (zie afbeelding hieronder). **Als u resourcespecifieke modus kiest voor de azure-siteherstelgebeurtenissen, wordt voorkomen dat de vereiste gegevens naar de LA Workspace worden verzonden.**

![Gebeurtenissen voor siteherstel](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Ga als een samenvatting van de bovenstaande nuances:

* Als u al LA-diagnose hebt ingesteld met Azure Diagnostics en er aangepaste query's op hebt geschreven, houdt u die instelling **intact**totdat u uw query's migreert om gegevens uit de nieuwe gebeurtenissen te gebruiken.
* Als u ook aan boord wilt gaan naar nieuwe tabellen (zoals aanbevolen), maakt u een **nieuwe** diagnostische instelling, kiest **u Resourcespecifiek** en selecteert u de zes nieuwe gebeurtenissen zoals hierboven aangegeven.
* Als u momenteel Azure Site Recovery Events naar LA verzendt, kiest **u geen** resourcespecifieke modus voor deze gebeurtenissen, anders vloeien gegevens voor deze gebeurtenissen niet in uw LA Workspace. Maak in plaats daarvan een **aanvullende diagnostische instelling,** selecteer **Azure Diagnostics**en kies de relevante Azure Site Recovery-gebeurtenissen.

De onderstaande afbeelding toont een voorbeeld van een gebruiker met drie diagnostische instellingen voor een kluis. De eerste instelling, met de naam **Instelling1,** verzendt gegevens van de gebeurtenis AzureBackupReport naar een LA Workspace in de AzureDiagnostics-modus. De tweede instelling, met de naam **Instelling2,** verzendt gegevens van de zes nieuwe Azure Backup-gebeurtenissen naar een LA Workspace in de modus Resourcespecifiek. De derde instelling, **setting3**genaamd, verzendt gegevens van de azure-siteherstelgebeurtenissen naar een LA Workspace in azure-diagnosemodus.

![Drie instellingen](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over het gegevensmodel log-analyse voor de diagnostische gebeurtenissen](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
