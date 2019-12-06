---
title: Uw Azure Automation-account verplaatsen naar een ander abonnement
description: In dit artikel wordt beschreven hoe u uw Automation-account kunt verplaatsen naar een ander abonnement
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2d1c747a52a1e8dedd0b5ba411b673eee463a2b6
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849578"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Uw Azure Automation-account verplaatsen naar een ander abonnement

Azure biedt u de mogelijkheid om sommige resources te verplaatsen naar een nieuwe resource groep of een nieuw abonnement. U kunt resources verplaatsen via de Azure Portal, Power shell, de Azure CLI of de REST API. Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../../azure-resource-manager/resource-group-move-resources.md)voor meer informatie over het proces.

Azure Automation accounts zijn een van de resources die kunnen worden verplaatst. In dit artikel leert u de stappen om Automation-accounts te verplaatsen naar een andere resource of een ander abonnement.

De stappen op hoog niveau voor het verplaatsen van uw Automation-account zijn:

1. Verwijder uw oplossingen.
2. De werk ruimte ontkoppelen.
3. Verplaats het Automation-account.
4. Verwijder de run as-accounts en maak deze opnieuw.
5. Schakel uw oplossingen opnieuw in.

## <a name="remove-solutions"></a>Oplossingen verwijderen

Als u uw werk ruimte wilt ontkoppelen van uw Automation-account, moeten deze oplossingen uit uw werk ruimte worden verwijderd:
- **Wijzigingen bijhouden en inventaris**
- **Updatebeheer**
- **Vm's buiten kantoor uren starten/stoppen**

Zoek in uw resource groep elke oplossing en selecteer **verwijderen**. Bevestig op de pagina **resources verwijderen** de resources die moeten worden verwijderd en selecteer **verwijderen**.

![Oplossingen van de Azure Portal verwijderen](../media/move-account/delete-solutions.png)

U kunt dezelfde taak uitvoeren met de cmdlet [Remove-azurermresource gebruiken](/powershell/module/azurerm.resources/remove-azurermresource) , zoals wordt weer gegeven in het volgende voor beeld:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>Aanvullende stappen voor het starten/stoppen van Vm's

Voor de oplossing **Vm's starten/stoppen** moet u ook de waarschuwings regels verwijderen die zijn gemaakt door de oplossing.

Ga in het Azure Portal naar de resource groep en selecteer **bewaking** > **waarschuwingen** > regels voor het **beheren**van waarschuwingen.

![Pagina waarschuwingen met een selectie van waarschuwings regels beheren](../media/move-account/alert-rules.png)

Op de pagina **regels** ziet u een lijst met de waarschuwingen die zijn geconfigureerd in die resource groep. De oplossing **Vm's starten/stoppen** maakt drie waarschuwings regels:

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Selecteer deze drie waarschuwings regels en selecteer vervolgens **verwijderen**. Met deze actie worden deze waarschuwings regels verwijderd.

![Pagina regels die de bevestiging van het verwijderen van geselecteerde regels aanvragen](../media/move-account/delete-rules.png)

> [!NOTE]
> Als u geen waarschuwings regels ziet op de pagina **regels** , wijzigt u de **status** zodat **Uitgeschakelde** waarschuwingen worden weer gegeven, omdat u deze mogelijk hebt uitgeschakeld.

Wanneer de waarschuwings regels worden verwijderd, verwijdert u de actie groep die is gemaakt voor de meldingen van de Vm's van de **Start/Stop-** oplossing.

Selecteer in de Azure Portal > **waarschuwingen** **controleren** > **actie groepen beheren**.

Selecteer **StartStop_VM_Notification** in de lijst. Selecteer op de pagina actie groep de optie **verwijderen**.

![Pagina actie groep, selecteer verwijderen](../media/move-account/delete-action-group.png)

Op dezelfde manier kunt u uw actie groep verwijderen met behulp van Power shell met de cmdlet [Remove-AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) , zoals in het volgende voor beeld wordt weer gegeven:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Uw werk ruimte ontkoppelen

Selecteer in de Azure Portal **Automation-account** > **gerelateerde resources** > **gekoppelde werk ruimte**. Selecteer **werk ruimte ontkoppelen** om de werk ruimte te ontkoppelen van uw Automation-account.

![Een werk ruimte ontkoppelen van een Automation-account](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Uw Automation-account verplaatsen

Nadat u de vorige items hebt verwijderd, kunt u door gaan met het verwijderen van uw Automation-account en de runbooks. Blader in het Azure Portal naar de resource groep van uw Automation-account. Selecteer **verplaatsen** > **verplaatsen naar een ander abonnement**.

![De pagina resource groep, verplaatsen naar een ander abonnement](../media/move-account/move-resources.png)

Selecteer de resources in de resource groep die u wilt verplaatsen. Zorg ervoor dat u uw **Automation-account**, **Runbook**en **log Analytics werkruimte** resources opneemt.

Nadat de verplaatsing is voltooid, zijn er extra stappen vereist om alles wat werk gaat doen.

## <a name="re-create-run-as-accounts"></a>Run as-accounts opnieuw maken

[Run as-accounts](../manage-runas-account.md) maken een service-principal in azure Active Directory om te verifiëren met Azure-resources. Wanneer u abonnementen wijzigt, wordt het bestaande uitvoeren als-account niet meer gebruikt voor het Automation-account.

Ga naar uw Automation-account in het nieuwe abonnement en selecteer **uitvoeren als-accounts** onder **account instellingen**. U ziet dat de uitvoeren als-accounts nu onvolledig worden weer gegeven.

![Run as-accounts zijn onvolledig](../media/move-account/run-as-accounts.png)

Selecteer elk uitvoeren als-account. Selecteer op de pagina **Eigenschappen** de optie **verwijderen** om het uitvoeren als-account te verwijderen.

> [!NOTE]
> Als u geen machtigingen hebt om de run as-accounts te maken of weer te geven, ziet u het volgende bericht: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` voor meer informatie over de vereiste machtigingen voor het configureren van een run as-account, raadpleegt u de [machtigingen die zijn vereist voor het configureren van run as-accounts](../manage-runas-account.md#permissions).

Nadat de run as-accounts zijn verwijderd, selecteert u **maken** onder **Azure uitvoeren als-account**. Selecteer op de pagina **uitvoeren als-account voor Azure toevoegen** de optie **maken** om het uitvoeren als-account en de service-principal te maken. Herhaal de voor gaande stappen met het **klassieke uitvoeren als-account van Azure**.

## <a name="enable-solutions"></a>Oplossingen inschakelen

Nadat u de run as-accounts opnieuw hebt gemaakt, schakelt u de oplossingen die u hebt verwijderd voordat u deze verplaatst opnieuw in. Als u **Wijzigingen bijhouden en inventaris** en **updatebeheer**wilt inschakelen, selecteert u de betreffende mogelijkheid in uw Automation-account. Kies de Log Analytics-werk ruimte die u hebt verplaatst en selecteer **inschakelen**.

![Oplossingen opnieuw inschakelen in het verplaatste Automation-account](../media/move-account/reenable-solutions.png)

Computers waarop de oplossingen worden uitgevoerd, zijn zichtbaar wanneer u de bestaande Log Analytics-werk ruimte hebt verbonden.

Als u de **virtuele machines starten/stoppen** buiten kantoor uren wilt inschakelen, moet u de oplossing opnieuw implementeren. Selecteer onder **gerelateerde resources**de optie **Vm's starten/stoppen** > meer **informatie over en schakel de oplossing** in > **maken** om de implementatie te starten.

Kies op de pagina **oplossing toevoegen** de log Analytics-werk ruimte en het Automation-account.

![Menu oplossing toevoegen](../media/move-account/add-solution-vm.png)

Zie [VM's buiten bedrijfsuren starten/stoppen-oplossing in azure Automation](../automation-solution-vm-management.md)voor gedetailleerde instructies voor het configureren van de oplossing.

## <a name="post-move-verification"></a>Verificatie na verplaatsing

Wanneer de verplaatsing is voltooid, controleert u de volgende taken die moeten worden gecontroleerd:

|Mogelijkheid|Tests|Koppeling voor probleem oplossing|
|---|---|---|
|Runbooks|Een runbook kan worden uitgevoerd en er verbinding mee worden gemaakt met Azure-resources.|[Problemen met runbooks oplossen](../troubleshoot/runbooks.md)
|Broncodebeheer|U kunt een hand matige synchronisatie uitvoeren op uw broncode beheer opslag plaats.|[Integratie van bronbeheer](../source-control-integration.md)|
|Wijzigingen bijhouden en inventaris|Controleer of er actuele inventarisatie gegevens van uw computers worden weer gegeven.|[Problemen met het bijhouden van wijzigingen oplossen](../troubleshoot/change-tracking.md)|
|Updatebeheer|Controleer of u uw computers ziet en ze zijn in orde.</br>Voer een test implementatie van de software-update uit.|[Problemen met update beheer oplossen](../troubleshoot/update-management.md)|
|Gedeelde bronnen|Controleer of u al uw gedeelde bronnen ziet, zoals [referenties](../shared-resources/credentials.md), [variabelen](../shared-resources/variables.md), enzovoort.|

## <a name="next-steps"></a>Volgende stappen

Zie [resources verplaatsen in azure](../../azure-resource-manager/move-support-resources.md)voor meer informatie over het verplaatsen van resources in Azure.
