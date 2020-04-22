---
title: Uw Azure Automation-account verplaatsen naar een ander abonnement
description: In dit artikel wordt beschreven hoe u uw Automation-account verplaatst naar een ander abonnement.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2dbe7dc171b6e0ec81c99a460a4f997eeb9e27a5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681897"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Uw Azure Automation-account verplaatsen naar een ander abonnement

Met Azure Automation u bepaalde resources verplaatsen naar een nieuwe brongroep of -abonnement. U resources verplaatsen via de Azure-portal, PowerShell, de Azure CLI of de REST-API. Zie Resources verplaatsen naar [een nieuwe resourcegroep of -abonnement voor](../../azure-resource-manager/management/move-resource-group-and-subscription.md)meer informatie over het proces.

Het Azure Automation-account is een van de bronnen die u verplaatsen. In dit artikel leert u automatiseringsaccounts te verplaatsen naar een andere bron of abonnement. De stappen op hoog niveau voor het verplaatsen van uw Automatiseringsaccount zijn:

1. Verwijder uw oplossingen.
2. Koppel de koppeling van uw werkruimte los.
3. Verplaats het automatiseringsaccount.
4. Verwijder en maak de Run As-accounts opnieuw.
5. Maak uw oplossingen opnieuw in schakelen.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](../automation-update-azure-modules.md)

## <a name="remove-solutions"></a>Oplossingen verwijderen

Als u uw werkruimte wilt loskoppelen van uw Automatiseringsaccount, moet u deze oplossingen uit uw werkruimte verwijderen:

- Wijzigingen bijhouden en Inventaris
- Updatebeheer
- VM's starten/stoppen buiten kantooruren

1. Zoek de resourcegroep in de Azure-portal.
2. Zoek elke oplossing en klik op **Verwijderen** op de pagina Resources verwijderen.

    ![Oplossingen verwijderen uit de Azure-portal](../media/move-account/delete-solutions.png)

    U desgevraagd de oplossingen verwijderen met de cmdlet [Remove-AzResource:](https://docs.microsoft.com/powershell/module/Az.Resources/Remove-AzResource?view=azps-3.7.0)

    ```azurepowershell-interactive
    $workspaceName = <myWorkspaceName>
    $resourceGroupName = <myResourceGroup>
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
    ```

### <a name="remove-alert-rules-for-the-startstop-vms-during-off-hours-solution"></a>Waarschuwingsregels voor de VM's starten/stoppen tijdens de oplossing voor buitenkantoor

Voor de vm's starten/stoppen tijdens de oplossing voor buitenkantooruren moet u ook de waarschuwingsregels verwijderen die door de oplossing zijn gemaakt.

1. Ga in de Azure-portal naar uw resourcegroep en selecteer > **Waarschuwingsregels bewaken** > **Beheren**. **Monitoring**

![Pagina Waarschuwingen met selectie van waarschuwingsregels beheren](../media/move-account/alert-rules.png)

2. Op de pagina Regels ziet u een lijst met de waarschuwingen die zijn geconfigureerd in die brongroep. De oplossing creëert de volgende regels:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Selecteer de regels één voor één en klik op **Verwijderen** om ze te verwijderen.

    ![Regelspagina met het verzoek om bevestiging van verwijdering voor geselecteerde regels](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Als u geen waarschuwingsregels ziet op de pagina Regels, wijzigt u het veld **Status** in Uitgeschakeld om uitgeschakelde waarschuwingen weer te geven, omdat u deze mogelijk hebt uitgeschakeld.

4. Wanneer de waarschuwingsregels worden verwijderd, moet u de actiegroep verwijderen die is gemaakt voor VM's voor start/stop tijdens meldingen van bepaalde oplossingen buiten kantooruren. Selecteer in de Azure-portal de optie > **Monitorwaarschuwingen Actiegroepen** > **beheren**. **Monitor**

5. Selecteer **StartStop_VM_Notification**. 

6. Selecteer **verwijderen**op de pagina met de actiegroep .

    ![Pagina actiegroep](../media/move-account/delete-action-group.png)

    U desgevraagd uw actiegroep verwijderen met de cmdlet [Remove-AzActionGroup:](https://docs.microsoft.com/powershell/module/az.monitor/remove-azactiongroup?view=azps-3.7.0)

    ```azurepowershell-interactive
    Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
    ```

## <a name="unlink-your-workspace"></a>De koppeling van uw werkruimte ontkoppelen

Nu u de koppeling van uw werkruimte ontkoppelen:

1. Selecteer in de Azure-portal de optie De**werkruimte Gekoppelde werkruimte**voor **automatiseringsaccount** > gerelateerde**resources** > . 

2. Selecteer **Werkruimte ontkoppelen** om de koppeling van de werkruimte los te koppelen aan uw Automatiseringsaccount.

    ![Een werkruimte loskoppelen van een Automatiseringsaccount](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Uw Automatiseringsaccount verplaatsen

U nu uw Automation-account en de bijbehorende runbooks verplaatsen. 

1. Blader in de Azure-portal naar de brongroep van uw Automatiseringsaccount. Selecteer **Verplaatsen** > **naar een ander abonnement**selecteren .

    ![Pagina Resourcegroep, naar een ander abonnement gaan](../media/move-account/move-resources.png)

2. Selecteer de resources in uw resourcegroep die u wilt verplaatsen. Zorg ervoor dat u uw automatiseringsaccount, runbooks en logboekanalysewerkruimtebronnen opneemt.

## <a name="recreate-run-as-accounts"></a>Run as-accounts opnieuw maken

[Uitvoeren Als accounts](../manage-runas-account.md) een serviceprincipal maken in Azure Active Directory om te verifiëren met Azure-bronnen. Wanneer u abonnementen wijzigt, maakt het automatiseringsaccount geen gebruik meer van het bestaande Run As-account. Ga als volgt te werk om de accounts uitvoeren als opnieuw te maken:

1. Ga naar uw Automatiseringsaccount in het nieuwe abonnement en selecteer **Uitvoeren als accounts** onder **Accountinstellingen**. U ziet dat de accounts uitvoeren als nu als onvolledig worden weergegeven.

    ![Uitvoeren Omdat accounts onvolledig zijn](../media/move-account/run-as-accounts.png)

2. Verwijder de run As-accounts één voor één met de knop **Verwijderen** op de pagina Eigenschappen. 

    > [!NOTE]
    > Als u geen machtigingen hebt om de Run As-accounts te maken `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` of weer te geven, ziet u het volgende bericht: Zie Machtigingen die nodig zijn om Run As-accounts te configureren voor meer informatie over de machtigingen die nodig zijn om [Run As-accounts te configureren.](../manage-runas-account.md#permissions)

3. Nadat u de Run As-accounts hebt verwijderd, selecteert u **Maken** onder **Azure Run As-account**. 

4. Selecteer op de pagina Azure Run As-account toevoegen de optie **Maken** om de run as-account en serviceprincipal te maken. 

5. Herhaal de bovenstaande stappen met het Azure Classic Run As-account.

## <a name="enable-solutions"></a>Oplossingen inschakelen

Nadat u de accounts uitvoeren als hebt gemaakt, moet u de oplossingen die u hebt verwijderd voordat u de overstap hebt verwijderd, opnieuw inschakelen: 

1. Als u de oplossing Voor het bijhouden en inventaris wijzigen wilt inschakelen, selecteert u Bijhouden en voorraad wijzigen in uw automatiseringsaccount. Kies de werkruimte Log Analytics die u hebt verplaatst en selecteer **Inschakelen**.

2. Herhaal stap 1 voor de updatebeheeroplossing.

    ![Oplossingen opnieuw inschakelen in uw verplaatste automatiseringsaccount](../media/move-account/reenable-solutions.png)

3. Machines die zijn aangesloten op uw oplossingen zijn zichtbaar wanneer u de bestaande Log Analytics-werkruimte hebt aangesloten. Als u de VM's start/stop tijdens de oplossing voor buitenkantoor wilt inschakelen, moet u de oplossing opnieuw implementeren. Selecteer **VMs** >  **starten/stoppen**onder Gerelateerde resources**Meer informatie over en schakel de oplossing** > **Maken** in om de implementatie te starten.

4. Kies op de pagina Oplossing toevoegen uw log Analytics-werkruimte en automatiseringsaccount.

    ![Menu Oplossing toevoegen](../media/move-account/add-solution-vm.png)

5. Configureer de oplossing zoals beschreven in [VM's starten/stoppen tijdens de oplossing voor buitenkantoor in Azure Automation.](../automation-solution-vm-management.md)

## <a name="verify-the-move"></a>De verplaatsing verifiëren

Controleer bij het voltooien van de verplaatsing of de onderstaande mogelijkheden zijn ingeschakeld. 

|Mogelijkheid|Testen|Problemen oplossen|
|---|---|---|
|Runbooks|Een runbook kan met succes worden uitgevoerd en verbinding maken met Azure-bronnen.|[Problemen met runbooks oplossen](../troubleshoot/runbooks.md)
|Broncodebeheer|U een handmatige synchronisatie uitvoeren op uw bronbeheeropslagplaats.|[Integratie van bronbeheer](../source-control-integration.md)|
|Tracking en voorraad wijzigen|Controleer of u actuele voorraadgegevens van uw machines ziet.|[Problemen met het bijhouden van wijzigingen oplossen](../troubleshoot/change-tracking.md)|
|Updatebeheer|Controleer of u uw machines ziet en of ze gezond zijn.</br>Een implementatie van testsoftware-updates uitvoeren.|[Problemen met updatebeheer oplossen](../troubleshoot/update-management.md)|
|Gedeelde resources|Controleer of u al uw gedeelde bronnen, zoals [referenties,](../shared-resources/credentials.md) [variabelen](../shared-resources/variables.md)en dergelijke, ziet.|

## <a name="next-steps"></a>Volgende stappen

Zie Resources verplaatsen in Azure voor meer informatie over het verplaatsen van resources [in Azure.](../../azure-resource-manager/management/move-support-resources.md)
