---
title: Uw Azure Automation-account verplaatsen naar een ander abonnement
description: In dit artikel wordt beschreven hoe u uw Automation-account verplaatsen naar een ander abonnement
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1aa759a2984764169eb28935e095d0f7c0f90c08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969835"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Uw Azure Automation-account verplaatsen naar een ander abonnement

Azure biedt u de mogelijkheid om bepaalde resources te verplaatsen naar een nieuwe brongroep of -abonnement. U resources verplaatsen via de Azure-portal, PowerShell, de Azure CLI of de REST-API. Zie Resources verplaatsen naar [een nieuwe resourcegroep of -abonnement voor](../../azure-resource-manager/management/move-resource-group-and-subscription.md)meer informatie over het proces.

Azure Automation-accounts zijn een van de resources die kunnen worden verplaatst. In dit artikel leert u de stappen om Automatiseringsaccounts naar een andere bron of abonnement te verplaatsen.

De stappen op hoog niveau om uw Automatiseringsaccount te verplaatsen zijn:

1. Verwijder uw oplossingen.
2. Koppel de koppeling van uw werkruimte los.
3. Verplaats het automatiseringsaccount.
4. Verwijder en maak de Run As-accounts opnieuw.
5. Maak uw oplossingen opnieuw in schakelen.

## <a name="remove-solutions"></a>Oplossingen verwijderen

Als u uw werkruimte wilt loskoppelen van uw Automatiseringsaccount, moeten deze oplossingen uit uw werkruimte worden verwijderd:
- **Wijzigingen bijhouden en Inventaris**
- **Updatebeheer**
- **Vm's starten/stoppen tijdens de vrije uren**

Zoek in uw resourcegroep elke oplossing en selecteer **Verwijderen**. Bevestig op de pagina **Resources verwijderen** de te verwijderen resources en selecteer **Verwijderen**.

![Oplossingen verwijderen uit de Azure-portal](../media/move-account/delete-solutions.png)

U dezelfde taak uitvoeren met de cmdlet [Remove-AzureRmResource](/powershell/module/azurerm.resources/remove-azurermresource) zoals weergegeven in het volgende voorbeeld:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>Aanvullende stappen voor VM's starten/stoppen

Voor de **VM's starten/stoppen** moet u ook de waarschuwingsregels verwijderen die door de oplossing zijn gemaakt.

Ga in de Azure-portal naar uw resourcegroep en selecteer > **Waarschuwingsregels bewaken** > **Beheren**. **Monitoring**

![Pagina Waarschuwingen met selectie van waarschuwingsregels beheren](../media/move-account/alert-rules.png)

Op de pagina **Regels** ziet u een lijst met de waarschuwingen die zijn geconfigureerd in die brongroep. Met **de OPLOSSING Vm's starten/stoppen** worden drie waarschuwingsregels gemaakt:

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Selecteer deze drie waarschuwingsregels en selecteer **Verwijderen**. Met deze actie worden deze waarschuwingsregels verwijderd.

![Regelspagina met het verzoek om bevestiging van verwijdering voor geselecteerde regels](../media/move-account/delete-rules.png)

> [!NOTE]
> Als u geen waarschuwingsregels ziet op de pagina **Regels,** wijzigt u de **status** om **uitgeschakelde** waarschuwingen weer te geven, omdat u deze mogelijk hebt uitgeschakeld.

Wanneer de waarschuwingsregels worden verwijderd, verwijdert u de actiegroep die is gemaakt voor de meldingen **van vm's voor start/stop.**

Selecteer in de Azure-portal de optie > **Monitorwaarschuwingen Actiegroepen** > **beheren**. **Monitor**

Selecteer **StartStop_VM_Notification** in de lijst. Selecteer **verwijderen**op de pagina met de actiegroep .

![Actiegroeppagina, selecteer verwijderen](../media/move-account/delete-action-group.png)

U ook uw actiegroep verwijderen met powershell met de cmdlet [Remove-AzureRmActionGroup,](/powershell/module/azurerm.insights/remove-azurermactiongroup) zoals te zien is in het volgende voorbeeld:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>De koppeling van uw werkruimte ontkoppelen

Selecteer in de Azure-portal de optie De**werkruimte Gekoppelde werkruimte**voor **automatiseringsaccount** > gerelateerde**resources** > . Selecteer **Werkruimte ontkoppelen** om de koppeling van de werkruimte los te koppelen aan uw Automatiseringsaccount.

![Een werkruimte loskoppelen van een Automatiseringsaccount](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Uw Automatiseringsaccount verplaatsen

Nadat u de vorige items hebt verwijderd, u uw Automatiseringsaccount en de bijbehorende runbooks blijven verwijderen. Blader in de Azure-portal naar de brongroep van uw Automatiseringsaccount. Selecteer **Verplaatsen** > **naar een ander abonnement**selecteren .

![Pagina Resourcegroep, naar een ander abonnement gaan](../media/move-account/move-resources.png)

Selecteer de resources in uw resourcegroep die u wilt verplaatsen. Zorg ervoor dat u uw **automatiseringsaccount,** **runbook**en **logboekanalysewerkruimtebronnen** opneemt.

Nadat de verhuizing is voltooid, zijn er extra stappen nodig om alles te laten werken.

## <a name="re-create-run-as-accounts"></a>Run As-accounts opnieuw maken

[Uitvoeren Als accounts](../manage-runas-account.md) een serviceprincipal maken in Azure Active Directory om te verifiëren met Azure-bronnen. Wanneer u abonnementen wijzigt, maakt het automatiseringsaccount geen gebruik meer van het bestaande Run As-account.

Ga naar uw Automatiseringsaccount in het nieuwe abonnement en selecteer **Uitvoeren als accounts** onder **Accountinstellingen**. U ziet dat de accounts uitvoeren als nu als onvolledig worden weergegeven.

![Uitvoeren Omdat accounts onvolledig zijn](../media/move-account/run-as-accounts.png)

Selecteer elke Run As-account. Selecteer **op** de pagina Eigenschappen de optie **Verwijderen** om het account Uitvoeren als te verwijderen.

> [!NOTE]
> Als u geen machtigingen hebt om de Run As-accounts te maken `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` of weer te geven, ziet u het volgende bericht: Zie Machtigingen die nodig zijn om Run As-accounts te configureren voor meer informatie over de machtigingen die nodig zijn om [Run As-accounts te configureren.](../manage-runas-account.md#permissions)

Nadat de Run As-accounts zijn verwijderd, selecteert u **Maken** onder **Azure Run As-account**. Selecteer op de pagina **Azure Run As-account toevoegen** de optie **Maken** om de run as-account en serviceprincipal te maken. Herhaal de voorgaande stappen met het **Azure Classic Run As-account**.

## <a name="enable-solutions"></a>Oplossingen inschakelen

Nadat u de run As-accounts opnieuw hebt gemaakt, schakelt u de oplossingen die u vóór de verhuizing hebt verwijderd, opnieuw in. Als u **Wijzigingstracking en voorraadbeheer** **wilt**inschakelen, selecteert u de desbetreffende mogelijkheid in uw automatiseringsaccount. Kies de werkruimte Log Analytics die u hebt verplaatst en selecteer **Inschakelen**.

![Oplossingen opnieuw inschakelen in uw verplaatste automatiseringsaccount](../media/move-account/reenable-solutions.png)

Machines die zijn aangesloten bij uw oplossingen zijn zichtbaar wanneer u de bestaande Log Analytics-werkruimte hebt aangesloten.

Als u de **VM's start/stop wilt** inschakelen tijdens off-hours oplossing, moet u de oplossing opnieuw implementeren. Selecteer **VMs** >  **starten/stoppen**onder Gerelateerde resources**Meer informatie over en schakel de oplossing** > **Maken** in om de implementatie te starten.

Kies op de pagina **Oplossing toevoegen** uw Log Analytics Workspace- en Automation-account.

![Menu Oplossing toevoegen](../media/move-account/add-solution-vm.png)

Zie [Vm's starten/stoppen tijdens off-hours oplossing in Azure Automation](../automation-solution-vm-management.md)voor gedetailleerde instructies over het configureren van de oplossing.

## <a name="post-move-verification"></a>Verificatie na verplaatsing

Controleer de volgende lijst met taken die moeten worden geverifieerd wanneer de verhuizing is voltooid:

|Mogelijkheid|Testen|Koppeling probleemoplossing|
|---|---|---|
|Runbooks|Een runbook kan met succes worden uitgevoerd en verbinding maken met Azure-bronnen.|[Runbooks oplossen van problemen](../troubleshoot/runbooks.md)
|Broncodebeheer|U een handmatige synchronisatie uitvoeren op uw bronbesturingselementrepo.|[Integratie van bronbeheer](../source-control-integration.md)|
|Tracking en voorraad wijzigen|Controleer of u actuele voorraadgegevens van uw machines ziet.|[Problemen met het bijhouden van wijzigingen oplossen](../troubleshoot/change-tracking.md)|
|Updatebeheer|Controleer of je je machines ziet en dat ze gezond zijn.</br>Een implementatie van testsoftware-updates uitvoeren.|[Problemen met updatebeheer oplossen](../troubleshoot/update-management.md)|
|Gedeelde resources|Controleer of u al uw gedeelde bronnen ziet, zoals [referenties,](../shared-resources/credentials.md) [variabelen,](../shared-resources/variables.md)enz.|

## <a name="next-steps"></a>Volgende stappen

Zie Resources verplaatsen in Azure voor meer informatie over het verplaatsen van resources [in Azure.](../../azure-resource-manager/management/move-support-resources.md)
