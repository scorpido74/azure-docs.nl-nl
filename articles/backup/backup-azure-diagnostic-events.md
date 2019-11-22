---
title: Diagnostische instellingen gebruiken voor Recovery Services kluizen
description: Een artikel waarin wordt beschreven hoe u de oude en nieuwe diagnostische gebeurtenissen voor Azure Backup gebruikt
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 875adb82aeeb56b378a84ca01e716c7189abc64f
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74281101"
---
# <a name="using-diagnostics-settings-for-recovery-services-vaults"></a>Diagnostische instellingen gebruiken voor Recovery Services kluizen

Azure Backup verzendt diagnostische gebeurtenissen die kunnen worden verzameld en gebruikt voor analyse, waarschuwingen en rapportage. 

U kunt Diagnostische instellingen voor een Recovery Services kluis configureren via de Azure Portal door te navigeren naar de kluis en te klikken op de menu opdracht **Diagnostische instellingen** . Als u op de knop **Diagnostische gegevens toevoegen** klikt, kunt u een of meer diagnostische gebeurtenissen verzenden naar een opslag account, Event hub of een log Analytics (La)-werk ruimte.

![Blade instellingen voor diagnostische gegevens](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Diagnostische gebeurtenissen die beschikbaar zijn voor gebruikers van Azure Backup

Azure Backup levert de volgende diagnostische gebeurtenissen, die elk gedetailleerde gegevens over een specifieke set back-upartefacten bieden:
* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage 

[Gegevens model voor diagnostische gebeurtenissen van Azure Backup](https://aka.ms/diagnosticsdatamodel)

Gegevens voor deze gebeurtenissen kunnen worden verzonden naar een opslag account, een LA-werk ruimte of een event hub. Als u deze gegevens naar een LA-werk ruimte verzendt, moet u de **resource-specifieke** wissel knop in het scherm **Diagnostische instellingen** selecteren (Zie meer informatie in de onderstaande secties).

## <a name="using-diagnostics-settings-with-log-analytics-la"></a>Diagnostische instellingen gebruiken met Log Analytics (LA)

Als u het schema van Azure Log Analytics wilt uitlijnen, kunt u met Azure Backup nu diagnostische gegevens van de kluis verzenden naar toegewezen LA-tabellen voor back-up. Deze worden aangeduid als [resource-specifieke tabellen](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific).

De diagnostische gegevens van uw kluis naar LA verzenden:
1.  Navigeer naar uw kluis en klik op **Diagnostische instellingen**. Klik op **Diagnostische instelling toevoegen**.
2.  Geef een naam op voor de diagnostische instelling.
3.  Schakel het selectie vakje **verzenden naar log Analytics** in en selecteer een log Analytics werk ruimte.
4.  Selecteer **resource specifiek** in de wissel knop en schakel de volgende zes gebeurtenissen in: **CoreAzureBackup**, **AddonAzureBackupAlerts**, **AddonAzureBackupProtectedInstance**, **AddonAzureBackupJobs**, **AddonAzureBackupPolicy**en **AddonAzureBackupStorage**.
5.  Klik op **Opslaan**.

![Resource-specifieke modus](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Zodra de gegevens zijn opgeslagen in de werk ruimte LA, worden toegewezen tabellen voor elk van deze gebeurtenissen in uw werk ruimte gemaakt. U kunt elk van deze tabellen rechtstreeks opvragen en zo nodig ook samen voegingen of samen voegingen uitvoeren tussen deze tabellen.

> [!IMPORTANT]
> De bovenstaande zes gebeurtenissen, te weten, CoreAzureBackup, AddonAzureBackupAlerts, AddonAzureBackupProtectedInstance, AddonAzureBackupJobs, AddonAzureBackupPolicy en AddonAzureBackupStorage, worden **alleen** ondersteund in de resource-specifieke modus. **Houd er rekening mee dat als u gegevens voor deze zes gebeurtenissen in Azure Diagnostics modus probeert te verzenden, er geen gegevens naar de werk ruimte LA gaan.**

## <a name="legacy-event"></a>Verouderde gebeurtenis

Normaal gesp roken zijn alle diagnostische gegevens over back-ups voor een kluis opgenomen in één gebeurtenis met de naam ' AzureBackupReport '. De zes gebeurtenissen die hierboven worden beschreven, zijn in essentie een ontleding van alle gegevens in AzureBackupReport. 

Momenteel blijven we de AzureBackupReport-gebeurtenis voor achterwaartse compatibiliteit ondersteunen, in gevallen waarin gebruikers bestaande aangepaste query's voor deze gebeurtenis hebben, bijvoorbeeld aangepaste logboek waarschuwingen, aangepaste visualisaties enzovoort. We raden u echter aan om de nieuwe gebeurtenissen voor alle nieuwe diagnostische instellingen op de kluis te kiezen, omdat hierdoor de gegevens veel gemakkelijker te gebruiken zijn om te werken in logboek query's. Dit biedt een betere detectie van schema's en hun structuur, waardoor de prestaties voor beide opname worden verbeterd. latentie en query tijden. De ondersteuning voor het gebruik van de Azure Diagnostics modus wordt uiteindelijk gefaseerd uitgevoerd en daarom kan het kiezen van de nieuwe gebeurtenissen ertoe bijdragen dat u complexe migraties op een later tijdstip kunt voor komen.

U kunt ervoor kiezen om afzonderlijke diagnostische instellingen voor AzureBackupReport en de zes nieuwe gebeurtenissen te maken, totdat u al uw aangepaste query's hebt gemigreerd voor het gebruik van gegevens uit de nieuwe tabellen. De onderstaande afbeelding toont een voor beeld van een kluis met twee Diagnostische instellingen. Met de eerste instelling, met de naam **Setting1** , worden gegevens van AzureBackupReport gebeurtenis verzonden naar een La-werk ruimte in de modus AzureDiagnostics. De tweede instelling, met de naam **Setting2** , verzendt gegevens van de zes nieuwe Azure backup gebeurtenissen naar een La-werk ruimte in de resource-specifieke modus.

![Twee instellingen](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> De gebeurtenis AzureBackupReport wordt **alleen** ondersteund in de modus Azure Diagnostics. **Houd er rekening mee dat als u gegevens voor deze gebeurtenis in de resource-specifieke modus probeert te verzenden, er geen gegevens naar de werk ruimte LA gaan.**

> [!NOTE]
> De wissel knop voor Azure Diagnostics/resource specifiek wordt alleen weer gegeven als de gebruiker **verzenden naar log Analytics**controleert. Voor het verzenden van gegevens naar een opslag account of een event hub, kan een gebruiker eenvoudigweg de vereiste bestemming selecteren en de gewenste gebeurtenissen controleren, zonder extra invoer. Het is ook raadzaam om de verouderde gebeurtenis AzureBackupReport niet te kiezen.

## <a name="users-sending-azure-site-recovery-events-to-log-analytics-la"></a>Gebruikers die Azure Site Recovery gebeurtenissen verzenden naar Log Analytics (LA)

Azure Backup-en Azure Site Recovery-gebeurtenissen worden vanaf dezelfde Recovery Services kluis verzonden. Als Azure Site Recovery momenteel niet wordt uitgevoerd op resource-specifieke tabellen, worden gebruikers die Azure Site Recovery-gebeurtenissen naar LA willen verzenden **alleen** omgeleid naar het gebruik van Azure Diagnostics modus (Zie de onderstaande afbeelding). **Als u de resource-specifieke modus kiest voor de Azure site Recovery gebeurtenissen, voor komt u dat de vereiste gegevens worden verzonden naar de werk ruimte La**.

![Site Recovery gebeurtenissen](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Als u de bovenstaande nuances wilt samenvatten:

* Als u al LA Diagnostics hebt ingesteld met Azure Diagnostics en aangepaste query's hierop hebt geschreven, houdt u deze instelling **intact**, totdat u uw query's migreert om gegevens uit de nieuwe gebeurtenissen te gebruiken.
* Als u ook op nieuwe tabellen wilt opheffen (zoals aanbevolen), maakt u een **nieuwe** instelling voor diagnostische gegevens, kiest u **resource specifiek** en selecteert u de zes nieuwe gebeurtenissen zoals hierboven is beschreven.
* Als u momenteel Azure Site Recovery-gebeurtenissen naar LA verzendt, kiest u **geen** resource-specifieke modus voor deze gebeurtenissen. anders worden de gegevens voor deze gebeurtenissen niet in uw werk ruimte van de la geplaatst. Maak in plaats daarvan een **extra diagnostische instelling**, selecteer **Azure Diagnostics**en kies de relevante Azure site Recovery gebeurtenissen.

De onderstaande afbeelding toont een voor beeld van een gebruiker die drie Diagnostische instellingen voor een kluis heeft. Met de eerste instelling, met de naam **Setting1** , worden gegevens van AzureBackupReport gebeurtenis verzonden naar een La-werk ruimte in de modus AzureDiagnostics. De tweede instelling, met de naam **Setting2** , verzendt gegevens van de zes nieuwe Azure backup gebeurtenissen naar een La-werk ruimte in de resource-specifieke modus. De derde instelling, met de naam **Setting3**, verzendt gegevens van de Azure site Recovery gebeurtenissen naar een La-werk ruimte in azure Diagnostics modus.

![Drie instellingen](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over het Log Analytics gegevens model voor de diagnostische gebeurtenissen](https://aka.ms/diagnosticsdatamodel)
