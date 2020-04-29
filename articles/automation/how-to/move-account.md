---
title: Uw Azure Automation-account verplaatsen naar een ander abonnement
description: In dit artikel wordt beschreven hoe u uw Automation-account kunt verplaatsen naar een ander abonnement.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2dbe7dc171b6e0ec81c99a460a4f997eeb9e27a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681897"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Uw Azure Automation-account verplaatsen naar een ander abonnement

Met Azure Automation kunt u enkele resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement. U kunt resources verplaatsen via de Azure Portal, Power shell, de Azure CLI of de REST API. Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md)voor meer informatie over het proces.

Het Azure Automation-account is een van de resources die u kunt verplaatsen. In dit artikel leert u hoe u Automation-accounts kunt verplaatsen naar een andere resource of een ander abonnement. De stappen op hoog niveau voor het verplaatsen van uw Automation-account zijn:

1. Verwijder uw oplossingen.
2. De werk ruimte ontkoppelen.
3. Verplaats het Automation-account.
4. Verwijder de run as-accounts en maak deze opnieuw.
5. Schakel uw oplossingen opnieuw in.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor de installatie-instructies voor AZ module op uw Hybrid Runbook Worker. Voor uw Automation-account kunt u uw modules bijwerken naar de nieuwste versie met behulp van [het bijwerken van Azure PowerShell-modules in azure Automation](../automation-update-azure-modules.md).

## <a name="remove-solutions"></a>Oplossingen verwijderen

Als u uw werk ruimte wilt ontkoppelen van uw Automation-account, moet u deze oplossingen verwijderen uit uw werk ruimte:

- Wijzigingen bijhouden en Inventaris
- Updatebeheer
- VM's starten/stoppen buiten kantooruren

1. Zoek de resourcegroep in de Azure-portal.
2. Zoek elke oplossing en klik op **verwijderen** op de pagina Resources verwijderen.

    ![Oplossingen van de Azure Portal verwijderen](../media/move-account/delete-solutions.png)

    Als u wilt, kunt u de oplossingen verwijderen met de cmdlet [Remove-AzResource](https://docs.microsoft.com/powershell/module/Az.Resources/Remove-AzResource?view=azps-3.7.0) :

    ```azurepowershell-interactive
    $workspaceName = <myWorkspaceName>
    $resourceGroupName = <myResourceGroup>
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
    ```

### <a name="remove-alert-rules-for-the-startstop-vms-during-off-hours-solution"></a>Waarschuwings regels verwijderen voor de oplossing Vm's starten/stoppen tijdens buiten kantoor uren

Voor de oplossing Vm's starten/stoppen tijdens de buiten-werk uren moet u ook de waarschuwings regels verwijderen die zijn gemaakt door de oplossing.

1. Ga in het Azure Portal naar de resource groep en selecteer **bewakings** > **waarschuwingen** > **beheren waarschuwings regels**.

![Pagina waarschuwingen met een selectie van waarschuwings regels beheren](../media/move-account/alert-rules.png)

2. Op de pagina regels ziet u een lijst met de waarschuwingen die zijn geconfigureerd in die resource groep. De oplossing maakt deze regels:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Selecteer de regels een voor een tegelijk en klik op **verwijderen** om ze te verwijderen.

    ![Pagina regels die de bevestiging van het verwijderen van geselecteerde regels aanvragen](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Als u geen waarschuwings regels ziet op de pagina regels, wijzigt u het veld **status** in uitgeschakeld zodat uitgeschakelde waarschuwingen worden weer gegeven, omdat u ze mogelijk hebt uitgeschakeld.

4. Wanneer de waarschuwings regels worden verwijderd, moet u de actie groep die is gemaakt voor start-en stop Vm's, verwijderen tijdens de meldingen van de oplossing. Selecteer in het Azure Portal **bewaken** > van**waarschuwingen** > controleren**actie groepen beheren**.

5. Selecteer **StartStop_VM_Notification**. 

6. Selecteer op de pagina actie groep de optie **verwijderen**.

    ![Pagina actie groep](../media/move-account/delete-action-group.png)

    Als u wilt, kunt u uw actie groep verwijderen met de cmdlet [Remove-AzActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/remove-azactiongroup?view=azps-3.7.0) :

    ```azurepowershell-interactive
    Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
    ```

## <a name="unlink-your-workspace"></a>Uw werk ruimte ontkoppelen

U kunt nu uw werk ruimte ontkoppelen:

1. Selecteer in de Azure portal aan **Automation-account** > **gerelateerde resources** > **gekoppelde werk ruimte**. 

2. Selecteer **werk ruimte ontkoppelen** om de werk ruimte te ontkoppelen van uw Automation-account.

    ![Een werk ruimte ontkoppelen van een Automation-account](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Uw Automation-account verplaatsen

U kunt nu uw Automation-account en de bijbehorende runbooks verplaatsen. 

1. Blader in het Azure Portal naar de resource groep van uw Automation-account.  > Selecteer verplaatsen**naar ander abonnement**verplaatsen. **Move**

    ![De pagina resource groep, verplaatsen naar een ander abonnement](../media/move-account/move-resources.png)

2. Selecteer de resources in de resource groep die u wilt verplaatsen. Zorg ervoor dat u uw Automation-account, runbooks en Log Analytics werkruimte resources opneemt.

## <a name="recreate-run-as-accounts"></a>Run as-accounts opnieuw maken

[Run as-accounts](../manage-runas-account.md) maken een service-principal in azure Active Directory om te verifiëren met Azure-resources. Wanneer u abonnementen wijzigt, wordt het bestaande uitvoeren als-account niet meer gebruikt voor het Automation-account. De run as-accounts opnieuw maken:

1. Ga naar uw Automation-account in het nieuwe abonnement en selecteer **uitvoeren als-accounts** onder **account instellingen**. U ziet dat de uitvoeren als-accounts nu onvolledig worden weer gegeven.

    ![Run as-accounts zijn onvolledig](../media/move-account/run-as-accounts.png)

2. Verwijder de run as-accounts een voor een tegelijk met de knop **verwijderen** op de pagina Eigenschappen. 

    > [!NOTE]
    > Als u geen machtigingen hebt om de run as-accounts te maken of weer te geven, ziet u `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` het volgende bericht: voor meer informatie over de vereiste machtigingen voor het configureren van een uitvoeren als-account, Zie [machtigingen vereist voor het configureren van run as-accounts](../manage-runas-account.md#permissions).

3. Nadat u de run as-accounts hebt verwijderd, selecteert u **maken** onder **Azure uitvoeren als-account**. 

4. Selecteer op de pagina uitvoeren als-account voor Azure toevoegen de optie **maken** om het uitvoeren als-account en de service-principal te maken. 

5. Herhaal de bovenstaande stappen met het klassieke uitvoeren als-account van Azure.

## <a name="enable-solutions"></a>Oplossingen inschakelen

Nadat u de run as-accounts opnieuw hebt gemaakt, moet u de oplossingen die u hebt verwijderd vóór de verplaatsing opnieuw inschakelen: 

1. Als u de oplossing voor het Wijzigingen bijhouden en de inventaris wilt inschakelen, selecteert u Wijzigingen bijhouden en inventaris in uw Automation-account. Kies de Log Analytics-werk ruimte die u hebt verplaatst en selecteer **inschakelen**.

2. Herhaal stap 1 voor de oplossing Updatebeheer.

    ![Oplossingen opnieuw inschakelen in het verplaatste Automation-account](../media/move-account/reenable-solutions.png)

3. Computers waarop de oplossingen worden uitgevoerd, zijn zichtbaar wanneer u de bestaande Log Analytics-werk ruimte hebt verbonden. Als u de oplossing voor het starten/stoppen van Vm's buiten kantoor uren wilt inschakelen, moet u de oplossing opnieuw implementeren. Onder **gerelateerde resources**selecteert u **Start-en stop vm's** > **meer informatie over en schakelt u de oplossing** > **maken** in om de implementatie te starten.

4. Kies op de pagina oplossing toevoegen de Log Analytics-werk ruimte en het Automation-account.

    ![Menu oplossing toevoegen](../media/move-account/add-solution-vm.png)

5. Configureer de oplossing zoals beschreven in de oplossing voor het [starten/stoppen van vm's buiten kantoor uren in azure Automation](../automation-solution-vm-management.md).

## <a name="verify-the-move"></a>De verplaatsing controleren

Wanneer de verplaatsing is voltooid, controleert u of de hieronder vermelde mogelijkheden zijn ingeschakeld. 

|Mogelijkheid|Testen|Problemen oplossen|
|---|---|---|
|Runbooks|Een runbook kan worden uitgevoerd en er verbinding mee worden gemaakt met Azure-resources.|[Problemen met runbooks oplossen](../troubleshoot/runbooks.md)
|Broncodebeheer|U kunt een hand matige synchronisatie uitvoeren in uw opslag plaats voor broncode beheer.|[Integratie van bronbeheer](../source-control-integration.md)|
|Wijzigingen bijhouden en inventaris|Controleer of er actuele inventaris gegevens van uw computers worden weer gegeven.|[Problemen met het bijhouden van wijzigingen oplossen](../troubleshoot/change-tracking.md)|
|Updatebeheer|Controleer of uw computers worden weer geven en of ze in orde zijn.</br>Voer een test implementatie van de software-update uit.|[Problemen met update beheer oplossen](../troubleshoot/update-management.md)|
|Gedeelde resources|Controleer of u alle gedeelde bronnen ziet, zoals [referenties](../shared-resources/credentials.md), [variabelen](../shared-resources/variables.md)en soort gelijke.|

## <a name="next-steps"></a>Volgende stappen

Zie [resources verplaatsen in azure](../../azure-resource-manager/management/move-support-resources.md)voor meer informatie over het verplaatsen van resources in Azure.
