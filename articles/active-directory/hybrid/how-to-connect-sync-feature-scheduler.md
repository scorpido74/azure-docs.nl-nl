---
title: 'Azure AD Connect-synchronisatie: scheduler | Microsoft Documenten'
description: In dit onderwerp wordt de ingebouwde schedulerfunctie beschreven in Azure AD Connect-synchronisatie.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b1a598f-89c0-4244-9b20-f4aaad5233cf
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/01/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 309adfbebd4f4b615ac1f4061823ca01f3d3ee15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261071"
---
# <a name="azure-ad-connect-sync-scheduler"></a>Azure AD Connect sync: Scheduler (Azure AD Connect-synchronisatie: planning)
In dit onderwerp wordt de ingebouwde scheduler beschreven in Azure AD Connect-synchronisatie (synchronisatieengine).

Deze functie werd geïntroduceerd met build 1.1.105.0 (uitgebracht in februari 2016).

## <a name="overview"></a>Overzicht
Azure AD Connect synchroniseert wijzigingen die plaatsvinden in uw on-premises directory met behulp van een planner. Er zijn twee schedulerprocessen, een voor wachtwoordsynchronisatie en een voor object/attribuutsynchronisatie en onderhoudstaken. Dit onderwerp behandelt het laatste.

In eerdere versies was de planner voor objecten en kenmerken extern aan de synchronisatie-engine. Het gebruikte Windows-taakplanner of een aparte Windows-service om het synchronisatieproces te activeren. De scheduler is met de 1.1 releases ingebouwd in de sync engine en doen toestaan dat sommige aanpassing. De nieuwe standaardsynchronisatiefrequentie is 30 minuten.

De planner is verantwoordelijk voor twee taken:

* **Synchronisatiecyclus**. Het proces voor het importeren, synchroniseren en exporteren van wijzigingen.
* **Onderhoudstaken**. Sleutels en certificaten verlengen voor het opnieuw instellen van wachtwoorden en de Service voor apparaatregistratie (DRS). Oude vermeldingen wissen in het logboek van bewerkingen.

De planner zelf wordt altijd uitgevoerd, maar kan worden geconfigureerd om slechts een of geen van deze taken uit te voeren. Als u bijvoorbeeld uw eigen synchronisatiecyclusproces moet hebben, u deze taak uitschakelen in de planner, maar toch de onderhoudstaak uitvoeren.

## <a name="scheduler-configuration"></a>Schedulerconfiguratie
Ga naar PowerShell en voer de `Get-ADSyncScheduler`uitvoering uit om uw huidige configuratie-instellingen te bekijken. Het toont je zoiets als deze foto:

![GetSyncScheduler](./media/how-to-connect-sync-feature-scheduler/getsynccyclesettings2016.png)

Als u **de synchronisatieopdracht of cmdlet ziet** die niet beschikbaar is wanneer u deze cmdlet uitvoert, wordt de PowerShell-module niet geladen. Dit probleem kan optreden als u Azure AD Connect uitvoert op een domeincontroller of op een server met hogere PowerShell-beperkingsniveaus dan de standaardinstellingen. Als u deze fout `Import-Module ADSync` ziet, voert u deze uit om de cmdlet beschikbaar te maken.

* **AllowedSyncCycleInterval**. Het kortste tijdsinterval tussen synchronisatiecycli toegestaan door Azure AD. U niet vaker synchroniseren dan deze instelling en worden nog steeds ondersteund.
* **MomenteelEffectiveSyncCycleInterval**. Het schema dat momenteel van kracht is. Het heeft dezelfde waarde als CustomizedSyncInterval (indien ingesteld) als het niet vaker voorkomt dan AllowedSyncInterval. Als u een build vóór 1.1.281 gebruikt en customizedsynccycleinterval wijzigt, wordt deze wijziging van kracht na de volgende synchronisatiecyclus. Vanaf build 1.1.281 treedt de wijziging onmiddellijk in werking.
* **CustomizedSyncCycleInterval**. Als u wilt dat de planner op een andere frequentie wordt uitgevoerd dan de standaard30 minuten, configureert u deze instelling. In de afbeelding hierboven is de planner ingesteld om elk uur in plaats daarvan uit te voeren. Als u deze instelling instelt op een waarde die lager is dan ToegestaanSyncInterval, wordt deze laatste gebruikt.
* **NextSyncCyclePolicyType**. Delta of Initial. Hiermee bepaalt u of de volgende run alleen deltawijzigingen moet verwerken of dat de volgende run een volledige import en synchronisatie moet uitvoeren. Deze laatste zou ook nieuwe of gewijzigde regels opnieuw verwerken.
* **NextsyncCycleStartTimeInUTC**. De volgende keer dat de planner de volgende synchronisatiecyclus start.
* **PurgeRunHistoryInterval**. De logboeken van de tijdsbewerking moeten worden bijgehouden. Deze logboeken kunnen worden beoordeeld in de synchronisatieservicemanager. De standaard is om deze logs te houden voor 7 dagen.
* **SyncCycleIngeschakeld**. Hiermee geeft u aan of de planner de import-, synchronisatie- en exportprocessen uitvoert als onderdeel van de bewerking.
* **MaintenanceEnabled**. Hiermee wordt weergegeven of het onderhoudsproces is ingeschakeld. Het werkt de certificaten/sleutels bij en zuivert het logboek van bewerkingen.
* **StagingModeEnabled**. Hiermee wordt weergegeven of [de faseringsmodus](how-to-connect-sync-staging-server.md) is ingeschakeld. Als deze instelling is ingeschakeld, wordt de uitvoer van uitvoeren onderdrukt, maar wordt nog steeds geïmporteerd en gesynchroniseerd.
* **SchedulerSuspended**. Stel in door Connect tijdens een upgrade om de planner tijdelijk te blokkeren.

U een aantal `Set-ADSyncScheduler`van deze instellingen wijzigen met. De volgende parameters kunnen worden gewijzigd:

* AangepastSyncCycleInterval
* NextsyncCyclePolicyType
* PurgeRunHistoryInterval
* SyncCycleIngeschakeld
* MaintenanceEnabled

In eerdere builds van Azure AD Connect werd **isStagingModeEnabled** weergegeven in Set-ADSyncScheduler. Het wordt **niet ondersteund** om deze eigenschap in te stellen. De eigenschap **SchedulerSuspended** mag alleen worden gewijzigd door Connect. Het wordt **niet ondersteund** om dit rechtstreeks met PowerShell in te stellen.

De configuratie van de planner wordt opgeslagen in Azure AD. Als u een staging-server hebt, heeft elke wijziging op de primaire server ook gevolgen voor de staging-server (behalve IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>AangepastSyncCycleInterval
Syntaxis:`Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d - dagen, HH - uren, mm - minuten, ss - seconden

Voorbeeld: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Hiermee wijzigt u de planner om elke 3 uur uit te voeren.

Voorbeeld: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
Als u de planner wijzigt, wordt deze dagelijks uitgevoerd.

### <a name="disable-the-scheduler"></a>De planner uitschakelen  
Als u configuratiewijzigingen moet aanbrengen, wilt u de planner uitschakelen. Bijvoorbeeld wanneer u [filtering configureert](how-to-connect-sync-configure-filtering.md) of [wijzigingen aanbrengt in synchronisatieregels.](how-to-connect-sync-change-the-configuration.md)

Als u de planner `Set-ADSyncScheduler -SyncCycleEnabled $false`wilt uitschakelen, voert u .

![De planner uitschakelen](./media/how-to-connect-sync-feature-scheduler/schedulerdisable.png)

Wanneer u uw wijzigingen hebt aangebracht, vergeet dan niet `Set-ADSyncScheduler -SyncCycleEnabled $true`om de planner opnieuw in te schakelen met .

## <a name="start-the-scheduler"></a>De planner starten
De planner wordt standaard elke 30 minuten uitgevoerd. In sommige gevallen wilt u misschien een synchronisatiecyclus uitvoeren tussen de geplande cycli of moet u een ander type uitvoeren.

### <a name="delta-sync-cycle"></a>Delta-synchronisatiecyclus
Een deltasynchronisatiecyclus bevat de volgende stappen:


- Delta importeren op alle connectors
- Delta-synchronisatie op alle connectors
- Exporteren op alle connectors

### <a name="full-sync-cycle"></a>Volledige synchronisatiecyclus
Een volledige synchronisatiecyclus bevat de volgende stappen:

- Volledige import op alle connectors
- Volledige synchronisatie op alle connectors
- Exporteren op alle connectors

Het kan zijn dat u een dringende wijziging hebt die onmiddellijk moet worden gesynchroniseerd, daarom moet u handmatig een cyclus uitvoeren. 

Als u handmatig een synchronisatiecyclus moet uitvoeren, `Start-ADSyncSyncCycle -PolicyType Delta`voert u vanuit PowerShell uit.

Als u een volledige `Start-ADSyncSyncCycle -PolicyType Initial` synchronisatiecyclus wilt starten, voert u een PowerShell-prompt uit.   

Het uitvoeren van een volledige synchronisatiecyclus kan zeer tijdrovend zijn, lees de volgende sectie om te lezen hoe u dit proces optimaliseren.

### <a name="sync-steps-required-for-different-configuration-changes"></a>Synchronisatiestappen vereist voor verschillende configuratiewijzigingen
Verschillende configuratiewijzigingen vereisen verschillende synchronisatiestappen om ervoor te zorgen dat de wijzigingen correct worden toegepast op alle objecten.

- Meer objecten of kenmerken toegevoegd die uit een bronmap moeten worden geïmporteerd (door de synchronisatieregels toe te voegen/wijzigen)
    - Een volledige import is vereist op de connector voor die bronmap
- Wijzigingen aangebracht in de synchronisatieregels
    - Een volledige synchronisatie is vereist op de connector voor de gewijzigde synchronisatieregels
- Gewijzigd [filteren,](how-to-connect-sync-configure-filtering.md) zodat een ander aantal objecten moet worden opgenomen
    - Een volledige import is vereist op de connector voor elke AD-connector, tenzij u filtering op basis van kenmerken gebruikt op basis van kenmerken die al in de synchronisatie-engine worden geïmporteerd

### <a name="customizing-a-sync-cycle-run-the-right-mix-of-delta-and-full-sync-steps"></a>Een synchronisatiecyclus aanpassen, de juiste mix van delta- en volledige synchronisatiestappen uitvoeren
Om te voorkomen dat u een volledige synchronisatiecyclus uitvoert, u specifieke connectors markeren om een volledige stap uit te voeren met behulp van de volgende cmdlets.

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullSyncRequired $true`

`Get-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid>` 

Voorbeeld: Als u wijzigingen hebt aangebracht in de synchronisatieregels voor Connector 'AD Forest A' waarvoor geen nieuwe kenmerken moeten worden geïmporteerd, voert u de volgende cmdlets uit om een deltasynchronisatiecyclus uit te voeren die ook een volledige synchronisatiestap voor die connector heeft uitgevoerd.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`

Voorbeeld: Als u wijzigingen hebt aangebracht in de synchronisatieregels voor Connector 'AD Forest A', zodat er nu een nieuw kenmerk moet worden geïmporteerd, zou u de volgende cmdlets uitvoeren om een deltasynchronisatiecyclus uit te voeren die ook een stap Volledig importeren, Volledig synchroniseren voor die connector heeft uitgevoerd.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`


## <a name="stop-the-scheduler"></a>De planner stoppen
Als de planner momenteel een synchronisatiecyclus uitvoert, moet u deze mogelijk stoppen. Bijvoorbeeld als u de installatiewizard start en u deze fout krijgt:

![SyncCycleRunningError](./media/how-to-connect-sync-feature-scheduler/synccyclerunningerror.png)

Wanneer een synchronisatiecyclus wordt uitgevoerd, u geen configuratiewijzigingen aanbrengen. U wachten tot de planner het proces heeft voltooid, maar u het ook stoppen, zodat u uw wijzigingen onmiddellijk aanbrengen. Het stoppen van de huidige cyclus is niet schadelijk en in afwachting van wijzigingen worden verwerkt met de volgende run.

1. Begin met het vertellen van de planner om de `Stop-ADSyncSyncCycle`huidige cyclus te stoppen met de PowerShell-cmdlet .
2. Als u een build vóór 1.1.281 gebruikt, stopt het stoppen van de planner de huidige connector niet van de huidige taak. Als u de connector wilt stoppen, ![neemt u de volgende acties: StopAConnector](./media/how-to-connect-sync-feature-scheduler/stopaconnector.png)
   * Start **Synchronisatieservice** vanuit het startmenu. Ga naar **Connectors,** markeer de connector met de status **Actief**en selecteer **Stoppen** in de acties.

De planner is nog steeds actief en begint opnieuw bij de volgende verkoopkans.

## <a name="custom-scheduler"></a>Aangepaste planner
De cmdlets gedocumenteerd in deze sectie zijn alleen beschikbaar in build [1.1.130.0](reference-connect-version-history.md#111300) en hoger.

Als de ingebouwde planner niet aan uw vereisten voldoet, u de connectors plannen met PowerShell.

### <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile
U op deze manier een profiel voor een connector starten:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

De namen die u wilt gebruiken voor [connectornamen](how-to-connect-sync-service-manager-ui-connectors.md) en [Profielnamen uitvoeren,](how-to-connect-sync-service-manager-ui-connectors.md#configure-run-profiles) zijn te vinden in de [gebruikersinterface van Synchronisatieservicebeheer.](how-to-connect-sync-service-manager-ui.md)

![Run-profiel aanroepen](./media/how-to-connect-sync-feature-scheduler/invokerunprofile.png)  

De `Invoke-ADSyncRunProfile` cmdlet is synchroon, dat wil zeggen, het geeft geen controle terug totdat de connector de bewerking heeft voltooid, met succes of met een fout.

Wanneer u uw connectors plant, is de aanbeveling om ze in de volgende volgorde in te plannen:

1. (Vol/Delta) Importeren uit on-premises mappen, zoals Active Directory
2. (Vol/Delta) Importeren uit Azure AD
3. (Vol/Delta) Synchronisatie van on-premises mappen, zoals Active Directory
4. (Vol/Delta) Synchronisatie vanuit Azure AD
5. Exporteren naar Azure AD
6. Exporteren naar on-premises mappen, zoals Active Directory

Deze volgorde is de manier waarop de ingebouwde scheduler de Connectors uitvoert.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
U ook de synchronisatie-engine controleren om te zien of deze bezet of niet actief is. Deze cmdlet retourneert een leeg resultaat als de synchronisatieengine niet actief is en geen connector draait. Als een connector wordt uitgevoerd, wordt de naam van de connector geretourneerd.

```
Get-ADSyncConnectorRunStatus
```

![Status connectorrun](./media/how-to-connect-sync-feature-scheduler/getconnectorrunstatus.png)  
In de bovenstaande afbeelding is de eerste regel afkomstig van een status waarin de synchronisatie-engine niet actief is. De tweede regel vanaf het moment dat de Azure AD Connector wordt uitgevoerd.

## <a name="scheduler-and-installation-wizard"></a>Wizard Scheduler en installatie
Als u de wizard installatie start, wordt de planner tijdelijk opgeschort. Dit gedrag is omdat wordt aangenomen dat u configuratiewijzigingen aanbrengt en deze instellingen niet kunnen worden toegepast als de synchronisatieengine actief wordt uitgevoerd. Laat daarom de installatiewizard niet open staan, omdat de synchronisatieengine geen synchronisatieacties meer uitvoert.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de [synchronisatieconfiguratie van Azure AD Connect.](how-to-connect-sync-whatis.md)

Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
