---
title: Archief &-rapport met Azure Monitor - Azure AD-rechtenbeheer
description: Meer informatie over het archiveren van logboeken en het maken van rapporten met Azure Monitor in Azure Active Directory-rechtenbeheer.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/14/2020
ms.author: barclayn
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: d59a508d03730a51e793a5e30e2c99a91af77ce8
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380195"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Archieflogboeken en rapportage over Azure AD-rechtenbeheer in Azure Monitor

Azure AD slaat controlegebeurtenissen voor maximaal 30 dagen op in het controlelogboek. U de controlegegevens echter langer bewaren dan de standaardbewaarperiode, beschreven in [Hoe lang slaat Azure AD rapportagegegevens op?](../reports-monitoring/reference-reports-data-retention.md)Door deze te routeren naar een Azure Storage-account of door Azure Monitor te gebruiken. U vervolgens werkmappen en aangepaste query's en rapporten over deze gegevens gebruiken.


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Azure AD configureren om Azure Monitor te gebruiken
Voordat u de werkmappen van Azure Monitor gebruikt, moet u Azure AD configureren om een kopie van de controlelogboeken naar Azure Monitor te verzenden.

Voor het archiveren van Azure AD-controlelogboeken moet u Azure Monitor in een Azure-abonnement hebben. U meer lezen over de vereisten en geschatte kosten van het gebruik van Azure Monitor in [Azure AD-activiteitslogboeken in Azure Monitor.](../reports-monitoring/concept-activity-logs-azure-monitor.md)

**Vereiste rol**: Globale beheerder

1. Meld u aan bij de Azure-portal als een gebruiker die een globale beheerder is. Zorg ervoor dat u toegang hebt tot de brongroep met de Azure Monitor-werkruimte.
 
1. Selecteer **Azure Active Directory** en klik op Diagnostische **instellingen** onder Controle in het linkernavigatiemenu. Controleer of er al een instelling is om de controlelogboeken naar die werkruimte te verzenden.

1. Als er nog geen instelling is ingesteld, klikt u op **Diagnostische instelling toevoegen**. Gebruik de instructies in het artikel [Azure AD-logboeken integreren met Azure Monitor-logboeken](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor) om het Azure AD-controlelogboek naar de Azure Monitor-werkruimte te verzenden.

    ![Het deelvenster Diagnostische instellingen](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. Nadat het logboek naar Azure Monitor is verzonden, selecteert u **Logboekanalysewerkruimten**en selecteert u de werkruimte die de Azure AD-controlelogboeken bevat.

1. Selecteer **Gebruiks- en geschatte kosten** en klik op **Gegevensbehoud**. Wijzig de schuifregelaar in het aantal dagen dat u de gegevens wilt bewaren om aan uw controlevereisten te voldoen.

    ![Deelvenster Logboekanalysewerkruimtes](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

1. Later u de werkmap *Gearchiveerde logboekdatumbereik* gebruiken om het bereik met datums in uw werkruimte te bekijken:  
    
    1. Selecteer **Azure Active Directory** en klik op **Werkmappen**. 
    
    1. Vouw de sectie **Azure Active Directory Troubleshooting uit**en klik op Gearchiveerd **logboekdatumbereik**. 


## <a name="view-events-for-an-access-package"></a>Gebeurtenissen voor een toegangspakket weergeven  

Als u gebeurtenissen voor een toegangspakket wilt weergeven, moet u toegang hebben tot de onderliggende Azure-monitorwerkruimte (zie [Toegang beheren tot logboekgegevens en werkruimten in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions) voor informatie) en in een van de volgende rollen: 

- Globale beheerder  
- Beveiligingsbeheerder  
- Beveiligingslezer  
- Rapportlezer  
- Toepassingsbeheerder  

Gebruik de volgende procedure om gebeurtenissen weer te geven: 

1. Selecteer azure active **directory** in de Azure Active Directory en klik vervolgens op **Werkmappen**. Als u slechts één abonnement hebt, gaat u verder met stap 3. 

1. Als u meerdere abonnementen hebt, selecteert u het abonnement dat de werkruimte bevat.  

1. Selecteer de werkmap met de naam *Access Package Activity*. 

1. Selecteer in die werkmap een tijdsbereik (wijzigen in **Alles** als dit niet zeker is) en selecteer een toegangspakket-id in de vervolgkeuzelijst van alle toegangspakketten die in dat tijdsbereik activiteit hadden. De gebeurtenissen met betrekking tot het toegangspakket die tijdens het geselecteerde tijdsbereik zijn opgetreden, worden weergegeven.  

    ![Access-pakketgebeurtenissen weergeven](./media/entitlement-management-logs-and-reporting/view-events-access-package.png) 

    Elke rij bevat de tijd, toegangspakket-id, de naam van de bewerking, de object-id, UPN en de weergavenaam van de gebruiker die de bewerking heeft gestart.  Aanvullende details zijn opgenomen in JSON.   


## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Aangepaste Azure Monitor-query's maken met de Azure-portal
U uw eigen query's maken voor Azure AD-controlegebeurtenissen, waaronder beheergebeurtenissen voor rechten.  

1. Klik in Azure Active Directory van de Azure-portal op **Logboeken** onder de sectie Controle in het linkernavigatiemenu om een nieuwe querypagina te maken.

1. Uw werkruimte moet linksboven op de querypagina worden weergegeven. Als u meerdere Azure Monitor-werkruimten hebt en de werkruimte die u gebruikt om Azure AD-controlegebeurtenissen op te slaan, wordt niet weergegeven, klikt u op **Bereik selecteren**. Selecteer vervolgens het juiste abonnement en werkruimte.

1. Verwijder vervolgens in het tekstgebied van de query de tekenreeks 'zoeken *' en vervang deze door de volgende query:

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. Klik vervolgens op **Uitvoeren**. 

    ![Klik op Uitvoeren om query te starten](./media/entitlement-management-logs-and-reporting/run-query.png)

In de tabel worden standaard de gebeurtenissen van het controlelogboek voor het beheer van rechten van het laatste uur weergegeven. U de instelling 'Tijdbereik' wijzigen om oudere gebeurtenissen weer te geven. Als u deze instelling wijzigt, worden er echter alleen gebeurtenissen weergegeven die zijn opgetreden nadat Azure AD is geconfigureerd om gebeurtenissen naar Azure Monitor te verzenden.

Als u de oudste en nieuwste controlegebeurtenissen in Azure Monitor wilt weten, gebruikt u de volgende query:

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

Zie [Het Azure AD-controlelogboekenschema interpreteren in Azure Monitor](../reports-monitoring/reference-azure-monitor-audit-log-schema.md)voor meer informatie over de kolommen die zijn opgeslagen voor controlegebeurtenissen in Azure Monitor.

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Aangepaste Azure Monitor-query's maken met Azure PowerShell

U hebt toegang tot logboeken via PowerShell nadat u Azure AD hebt geconfigureerd om logboeken naar Azure Monitor te verzenden. Verzend vervolgens query's van scripts of de PowerShell-opdrachtregel, zonder dat u een globale beheerder in de tenant hoeft te zijn. 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>Ervoor zorgen dat de gebruiker of serviceprincipal de juiste roltoewijzing heeft

Zorg ervoor dat u, de gebruiker of serviceprincipal die zich verifieert bij Azure AD, zich in de juiste Azure-rol bevindt in de werkruimte Log Analytics. De rolopties zijn Log Analytics Reader of de Log Analytics Contributor. Als u zich al in een van deze rollen bevindt, gaat u vervolgens naar [Logboekanalyse-id ophalen met één Azure-abonnement.](#retrieve-log-analytics-id-with-one-azure-subscription)

Ga als volgt te werk om de roltoewijzing in te stellen en een query te maken:

1. Zoek in de Azure-portal de [werkruimte Log Analytics](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
).

1. Selecteer **Toegangsbeheer (IAM)**.

1. Klik vervolgens op **Toevoegen** om een roltoewijzing toe te voegen.

    ![Een roltoewijzing toevoegen](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Azure PowerShell-module installeren

Zodra u de juiste roltoewijzing hebt, start u PowerShell en [installeert u de Azure PowerShell-module](/powershell/azure/install-az-ps?view=azps-3.3.0) (als u dat nog niet hebt gedaan), door te typen:

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
Nu u zich verifiëren bij Azure AD en de id ophalen van de werkruimte Log Analytics die u opvraagt.

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>Logboekanalyse-id ophalen met één Azure-abonnement
Als u slechts één Azure-abonnement en één Log Analytics-werkruimte hebt, typt u het volgende om te verifiëren bij Azure AD, maakt u verbinding met dat abonnement en haalt u die werkruimte op:
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>Logboekanalyse-id ophalen met meerdere Azure-abonnementen

 [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) werkt in één abonnement tegelijk. Als u dus meerdere Azure-abonnementen hebt, wilt u ervoor zorgen dat u verbinding maakt met de werkruimte Logboekanalyse met de Azure AD-logboeken. 
 
 Met de volgende cmdlets wordt een lijst met abonnementen weergegeven en wordt de id van het abonnement met de werkruimte Log Analytics gevonden:
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
U uw PowerShell-sessie opnieuw verifiëren en aan `Connect-AzAccount –Subscription $subs[0].id`dat abonnement koppelen met een opdracht zoals. Zie [Aanmelden bij Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-3.3.0&viewFallbackFrom=azps-2.5.0
)voor meer informatie over hoe u zich verifiëren voor Azure vanuit PowerShell, inclusief niet-interactief.

Als u meerdere Log Analytics-werkruimten in dat abonnement hebt, retourneert de cmdlet [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) de lijst met werkruimten. Vervolgens u degene vinden met de Azure AD-logboeken. Het `CustomerId` veld dat door deze cmdlet wordt geretourneerd, is hetzelfde als de waarde van de werkruimte-id die wordt weergegeven in de Azure-portal in het overzicht van de werkruimte log-analyse.
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>De query verzenden naar de werkruimte Log Analytics
Ten slotte u, zodra u een werkruimte hebt geïdentificeerd, [Invoke-AzOperationalInsightsQuery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery?view=azps-3.3.0
) gebruiken om een Kusto-query naar die werkruimte te verzenden. Deze query's zijn geschreven in [kusto-querytaal](https://docs.microsoft.com/azure/kusto/query/).
 
U bijvoorbeeld het datumbereik van de controlegebeurtenisrecords ophalen uit de werkruimte Log Analytics, met PowerShell-cmdlets om een query te verzenden zoals:
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

U ook beheergebeurtenissen voor rechten ophalen met een query zoals:

```azurepowershell
$bQuery = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>Volgende stappen:
- [Interactieve rapporten maken met Azure Monitor-werkmappen](../../azure-monitor/app/usage-workbooks.md) 

