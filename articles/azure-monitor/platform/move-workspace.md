---
title: Een Logboekanalysewerkruimte verplaatsen in Azure-monitor | Microsoft Documenten
description: Meer informatie over het verplaatsen van uw Log Analytics-werkruimte naar een ander abonnement of resourcegroep.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2019
ms.openlocfilehash: 9213ddf034e725f6e31c9280d47bd13e4703b3f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659489"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>Een Logboekanalysewerkruimte verplaatsen naar een ander abonnement of resourcegroep

In dit artikel leert u de stappen om de werkruimte Log Analytics te verplaatsen naar een andere resourcegroep of -abonnement in dezelfde regio. U meer informatie vinden over het verplaatsen van Azure-resources via de Azure-portal, PowerShell, azure CLI of de REST API. bij [Resources verplaatsen naar een nieuwe resourcegroep of -abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md). 

> [!IMPORTANT]
> U een werkruimte niet verplaatsen naar een andere regio.

## <a name="verify-active-directory-tenant"></a>Active Directory-tenant verifiëren
De werkruimtebron- en doelabonnementen moeten bestaan binnen dezelfde Azure Active Directory-tenant. Gebruik Azure PowerShell om te controleren of beide abonnementen dezelfde tenant-id hebben.

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="workspace-move-considerations"></a>Overwegingen voor het verplaatsen van werkruimtes
Beheerde oplossingen die in de werkruimte zijn geïnstalleerd, worden verplaatst met de bewerking Logboekanalyse-werkruimte. Verbonden agents blijven verbonden en bewaren na de verhuizing de verzendgegevens naar de werkruimte. Aangezien de verplaatsingsbewerking vereist dat er geen koppeling is van de werkruimte naar een automatiseringsaccount, moeten oplossingen die op die koppeling vertrouwen, worden verwijderd.

Oplossingen die moeten worden verwijderd voordat u uw automatiseringsaccount ontkoppelen:

- Updatebeheer
- Tracering wijzigen
- VM's starten/stoppen buiten kantooruren


### <a name="delete-in-azure-portal"></a>Verwijderen in Azure Portal
Gebruik de volgende procedure om de oplossingen te verwijderen met behulp van de Azure-portal:

1. Open het menu voor de resourcegroep waarin eventuele oplossingen zijn geïnstalleerd.
2. Selecteer de oplossingen die u wilt verwijderen.
3. Klik **op Resources verwijderen** en bevestig de resources die moeten worden verwijderd door op **Verwijderen**te klikken.

![Oplossingen verwijderen](media/move-workspace/delete-solutions.png)

### <a name="delete-using-powershell"></a>Verwijderen met PowerShell

Als u de oplossingen met PowerShell wilt verwijderen, gebruikt u de cmdlet [Remove-AzResource](/powershell/module/az.resources/remove-azresource?view=azps-2.8.0) zoals weergegeven in het volgende voorbeeld:

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

### <a name="remove-alert-rules"></a>Waarschuwingsregels verwijderen
Voor de **VM's starten/stoppen** moet u ook de waarschuwingsregels verwijderen die door de oplossing zijn gemaakt. Gebruik de volgende procedure in de Azure-portal om deze regels te verwijderen.

1. Open het menu **Monitor** en selecteer **Vervolgens Waarschuwingen**.
2. Klik **op Waarschuwingsregels beheren**.
3. Selecteer de volgende drie waarschuwingsregels en klik op **Verwijderen**.

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![Regels verwijderen](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>Koppeling van automatiseringsaccount ontkoppelen
Gebruik de volgende procedure om het automatiseringsaccount los te koppelen aan de werkruimte met behulp van de Azure-portal:

1. Open het menu **Automatiseringsaccounts** en selecteer het account dat u wilt verwijderen.
2. Selecteer **Gekoppelde werkruimte**in de sectie **Gerelateerde bronnen** van het menu . 
3. Klik **op Werkruimte ontkoppelen** om de koppeling van de werkruimte los te koppelen aan uw Automatiseringsaccount.

    ![Werkruimte ontkoppelen](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>Uw werkruimte verplaatsen

### <a name="azure-portal"></a>Azure Portal
Gebruik de volgende procedure om uw werkruimte te verplaatsen met de Azure-portal:

1. Open het menu **Logboekanalyse-werkruimten** en selecteer vervolgens uw werkruimte.
2. Klik **op** de pagina Overzicht op **Wijzigen** naast **de resourcegroep** of **op Een abonnement**.
3. Er wordt een nieuwe pagina geopend met een lijst met bronnen die betrekking hebben op de werkruimte. Selecteer de resources die u wilt verplaatsen naar dezelfde bestemmingsabonnements- en resourcegroep als de werkruimte. 
4. Selecteer een groep **doelabonnement** en **resource**. Als u de werkruimte verplaatst naar een andere resourcegroep in hetzelfde abonnement, ziet u de optie **Abonnement** niet.
5. Klik op **OK** om de werkruimte en geselecteerde resources te verplaatsen.

    ![Portal](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
Als u uw werkruimte wilt verplaatsen met PowerShell, gebruikt u de [Move-AzResource](/powershell/module/AzureRM.Resources/Move-AzureRmResource) zoals in het volgende voorbeeld:

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```



> [!IMPORTANT]
> Na de verplaatsingsbewerking moeten verwijderde oplossingen en de koppeling Automatiseringsaccount opnieuw worden geconfigureerd om de werkruimte terug te brengen naar de vorige status.


## <a name="next-steps"></a>Volgende stappen
- Zie Ondersteuning voor de bewerking [verplaatsen voor resources voor](../../azure-resource-manager/management/move-support-resources.md)een lijst met de ondersteuning van resources .
