---
title: Klassieke metrische waarschuwingen maken, weergeven en beheren met Azure Monitor
description: Meer informatie over het gebruik van Azure portal, CLI of Powershell om klassieke metrische waarschuwingsregels te maken, weer te geven en te beheren.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/18/2018
ms.openlocfilehash: b770b9bd34c8267889db536ec81332de32cb8776
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668312"
---
# <a name="create-view-and-manage-classic-metric-alerts-using-azure-monitor"></a>Klassieke metrische waarschuwingen maken, weergeven en beheren met Azure Monitor

Klassieke metrische waarschuwingen in Azure Monitor bieden een manier om een melding te krijgen wanneer een van uw statistieken een drempel overschrijdt. Klassieke metrische waarschuwingen is een oudere functionaliteit die het mogelijk maakt om alleen te waarschuwen voor niet-dimensionale statistieken. Er is een bestaande nieuwere functionaliteit genaamd Metrische waarschuwingen die de functionaliteit ten opzichte van klassieke metrische waarschuwingen heeft verbeterd. Meer informatie over de nieuwe functionaliteit voor metrische waarschuwingen vindt u in [het overzicht van metrische waarschuwingen.](../../azure-monitor/platform/alerts-metric-overview.md) In dit artikel beschrijven we hoe u klassieke metrische waarschuwingsregels maken, bekijken en beheren via Azure portal, Azure CLI en Powershell.

## <a name="with-azure-portal"></a>Met Azure-portal

1. Zoek in de [portal](https://portal.azure.com/)de resource die u wilt controleren en selecteer deze.

2. Selecteer **in** de sectie TOEZICHT de optie **Waarschuwingen (Klassiek).** De tekst en het pictogram kunnen enigszins variëren voor verschillende bronnen. Als u hier geen **waarschuwingen (Klassiek)** vindt, u deze vinden in **Waarschuwingen** of **Waarschuwingsregels**.

    ![Bewaking](media/alerts-classic-portal/AlertRulesButton.png)

3. Selecteer de opdracht **Metrische waarschuwing (klassiek) toevoegen** en vul de velden in.

    ![Waarschuwing toevoegen](media/alerts-classic-portal/AddAlertOnlyParamsPage.png)

4. Geef uw waarschuwingsregel een **naam.** Kies vervolgens een **beschrijving,** die ook wordt weergegeven in e-mails met meldingen.

5. Selecteer de **statistiek** die u wilt controleren. Kies vervolgens een **waarde voorwaarde** en **drempelwaarde** voor de statistiek. Kies ook de **periode** waarin aan de metrische regel moet worden voldaan voordat de waarschuwing wordt geactiveerd. Als u bijvoorbeeld de periode "Over de laatste 5 minuten" gebruikt en uw waarschuwing zoekt naar een CPU boven de 80%, wordt de waarschuwing geactiveerd wanneer de CPU gedurende 5 minuten consistent boven de 80% is geweest. Nadat de eerste trigger optreedt, wordt deze opnieuw geactiveerd wanneer de CPU gedurende 5 minuten onder de 80% blijft. De CPU metrische meting gebeurt elke minuut.

6. Selecteer **E-maileigenaren...** als u wilt dat beheerders en medebeheerders e-mailmeldingen ontvangen wanneer de waarschuwing wordt geactiveerd.

7. Als u meldingen naar extra e-mailadressen wilt verzenden wanneer de waarschuwing wordt geactiveerd, voegt u deze toe in het veld **E-mail(s) extra beheerders.** Scheid meerdere e-mails met puntkomma's, in de volgende indeling: *e-mail\@contoso.com;email2\@contoso.com*

8. Plaats een geldige URI in het **veld Webhook** als u wilt dat deze wordt aangeroepen wanneer de waarschuwing wordt geactiveerd.

9. Als u Azure Automation gebruikt, u een runbook selecteren dat moet worden uitgevoerd wanneer de waarschuwing wordt geactiveerd.

10. Selecteer **OK** om de waarschuwing te maken.

Binnen een paar minuten is de waarschuwing actief en wordt geactiveerd zoals eerder beschreven.

Nadat u een waarschuwing hebt gemaakt, u deze selecteren en een van de volgende taken uitvoeren:

* Een grafiek weergeven met de metrische drempel waarde en de werkelijke waarden van de vorige dag.
* Bewerken of verwijderen.
* **Schakel deze in** of schakel deze **in** als u meldingen voor die waarschuwing tijdelijk wilt stoppen of hervatten.

## <a name="with-azure-cli"></a>Met Azure CLI

In de vorige secties beschreven hoe u metrische waarschuwingsregels maken, weergeven en beheren met Azure-portal. In deze sectie wordt beschreven hoe u hetzelfde doen met behulp van azure [CLI met](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)cross-platform. De snelste manier om Azure CLI te gebruiken is via [Azure Cloud Shell.](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)

### <a name="get-all-classic-metric-alert-rules-in-a-resource-group"></a>Alle klassieke metrische waarschuwingsregels in een resourcegroep weergeven

```azurecli
az monitor alert list --resource-group <group name>
```

### <a name="see-details-of-a-particular-classic-metric-alert-rule"></a>Details van een bepaalde klassieke metrische waarschuwingsregel bekijken

```azurecli
az monitor alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-classic-metric-alert-rule"></a>Een klassieke metrische waarschuwingsregel maken

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-a-classic-metric-alert-rule"></a>Een klassieke metrische waarschuwingsregel verwijderen

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="with-powershell"></a>Met PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

In deze secties ziet u hoe u PowerShell-opdrachten gebruiken bij het maken, weergeven en beheren van klassieke metrische waarschuwingen. De voorbeelden in het artikel illustreren hoe u Azure Monitor-cmdlets gebruiken voor klassieke metrische waarschuwingen.

1. Als u dit nog niet hebt gedaan, stelt u PowerShell in om op uw computer te worden uitgevoerd. Zie [PowerShell installeren en configureren](/powershell/azure/overview)voor meer informatie. U ook de volledige lijst met Azure Monitor PowerShell-cmdlets bekijken op [Azure Monitor (Insights) Cmdlets.](https://docs.microsoft.com/powershell/module/az.applicationinsights)

2. Log eerst in bij uw Azure-abonnement.

    ```powershell
    Connect-AzAccount
    ```

3. U ziet een inlogscherm. Zodra u zich aanmeldt voor uw account, worden TenantID en standaard abonnements-id weergegeven. Alle Azure-cmdlets werken in de context van uw standaardabonnement. Als u de lijst met abonnementen wilt bekijken waartoe u toegang hebt, gebruikt u de volgende opdracht:

    ```powershell
    Get-AzSubscription
    ```

4. Als u uw werkcontext wilt wijzigen in een ander abonnement, gebruikt u de volgende opdracht:

    ```powershell
    Set-AzContext -SubscriptionId <subscriptionid>
    ```

5. U alle klassieke metrische waarschuwingsregels voor een resourcegroep ophalen:

    ```powershell
    Get-AzAlertRule -ResourceGroup montest
    ```

6. U details van een klassieke metrische waarschuwingsregel weergeven

    ```powershell
    Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
    ```

7. U alle waarschuwingsregels ophalen die zijn ingesteld voor een doelbron. Alle waarschuwingsregels die op een vm zijn ingesteld, zijn bijvoorbeeld ingesteld.

    ```powershell
    Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
    ```

8. Klassieke waarschuwingsregels kunnen niet langer worden gemaakt via PowerShell. Als u een waarschuwingsregel wilt maken, moet u de nieuwe opdracht ['Add-AzMetricAlertRule'](/powershell/module/az.monitor/add-azmetricalertrule) gebruiken.

## <a name="next-steps"></a>Volgende stappen

- [Maak een klassieke metrische waarschuwing met een resourcemanagersjabloon](../../azure-monitor/platform/alerts-enable-template.md).
- [Laat een klassieke metrische waarschuwing een niet-Azure-systeem melden met behulp van een webhook.](../../azure-monitor/platform/alerts-webhooks.md)
