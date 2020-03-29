---
title: Een waarschuwing gebruiken om een Azure Automation-runbook te activeren
description: Meer informatie over het activeren van een runbook om uit te voeren wanneer een Azure-waarschuwing wordt gegenereerd.
services: automation
ms.subservice: process-automation
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: df28116c588ed77f02c78a42a85feb91ca339e7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75366697"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Een waarschuwing gebruiken om een Azure Automation-runbook te activeren

U [Azure Monitor](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json) gebruiken om statistieken en logboeken op basisniveau te controleren voor de meeste services in Azure. U Azure Automation runbooks aanroepen met behulp van [actiegroepen](../azure-monitor/platform/action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) of door klassieke waarschuwingen te gebruiken om taken te automatiseren op basis van waarschuwingen. In dit artikel ziet u hoe u een runbook configureert en uitvoert met behulp van waarschuwingen.

## <a name="alert-types"></a>Waarschuwingstypen

U automatiseringsrunbooks gebruiken met drie waarschuwingstypen:

* Algemene waarschuwingen
* Waarschuwingen voor activiteitenlogboeken
* Bijna realtime metrische waarschuwingen

> [!NOTE]
> Het algemene waarschuwingsschema standaardiseert vandaag de dag de verbruikservaring voor waarschuwingsmeldingen in Azure. Historisch gezien hebben de drie waarschuwingstypen in Azure vandaag (metrische, logboek- en activiteitenlogboek) hun eigen e-mailsjablonen, webhook-schema's, enz. Zie [Algemeen waarschuwingsschema voor](../azure-monitor/platform/alerts-common-schema.md) meer informatie

Wanneer een waarschuwing een runbook aanroept, is de werkelijke oproep een HTTP POST-verzoek aan de webhook. De hoofdtekst van de POST-aanvraag bevat een object met JSON-indeling dat nuttige eigenschappen heeft die gerelateerd zijn aan de waarschuwing. In de volgende tabel worden koppelingen naar het laadschema voor elk waarschuwingstype weergegeven:

|Waarschuwing  |Beschrijving|Payload-schema  |
|---------|---------|---------|
|[Algemene waarschuwing](../azure-monitor/platform/alerts-common-schema.md?toc=%2fazure%2fautomation%2ftoc.json)|Het algemene waarschuwingsschema dat de verbruikservaring voor waarschuwingsmeldingen in Azure vandaag standaardiseert.|Algemeen waarschuwingslaadschema|
|[Waarschuwing voor activiteitenlogboek](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Hiermee verzendt u een melding wanneer een nieuwe gebeurtenis in het Azure-activiteitenlogboek overeenkomt met specifieke voorwaarden. Wanneer er bijvoorbeeld `Delete VM` een bewerking plaatsvindt in **myProductionResourceGroup** of wanneer een nieuwe Azure Service Health-gebeurtenis met een **Actieve** status wordt weergegeven.| [Laadschema voor waarschuwingsgegevens van het logboek voor activiteit](../azure-monitor/platform/activity-log-alerts-webhook.md)        |
|[Bijna realtime metrische waarschuwing](../azure-monitor/platform/alerts-metric-near-real-time.md?toc=%2fazure%2fautomation%2ftoc.json)    |Hiermee verzendt u sneller een melding dan metrische waarschuwingen wanneer een of meer statistieken op platformniveau aan bepaalde voorwaarden voldoen. Wanneer de waarde voor **CPU-percentage** op een VM bijvoorbeeld groter is dan **90**en de waarde voor **Network In** groter is dan **500 MB** gedurende de afgelopen 5 minuten.| [Bijna realtime metrische waarschuwingslaadschema](../azure-monitor/platform/alerts-webhooks.md#payload-schema)          |

Omdat de gegevens die door elk type waarschuwing worden geleverd, verschillend zijn, wordt elk waarschuwingstype anders behandeld. In de volgende sectie leert u hoe u een runbook maakt om verschillende soorten waarschuwingen te verwerken.

## <a name="create-a-runbook-to-handle-alerts"></a>Een runbook maken om waarschuwingen af te handelen

Als u Automatisering wilt gebruiken met waarschuwingen, hebt u een runbook nodig met logica die de waarschuwingJSON-payload beheert die is doorgegeven aan het runbook. Het volgende voorbeeld runbook moet worden aangeroepen vanuit een Azure-waarschuwing.

Zoals beschreven in de vorige sectie, heeft elk type waarschuwing een ander schema. Het script neemt de webhook-gegevens in de `WebhookData` parameter runbook-invoer op van een waarschuwing. Vervolgens evalueert het script de JSON-payload om te bepalen welk waarschuwingstype is gebruikt.

In dit voorbeeld wordt een waarschuwing van een vm gebruikt. Het haalt de VM-gegevens uit de payload, en vervolgens gebruikt die informatie om de VM te stoppen. De verbinding moet worden ingesteld in het automatiseringsaccount waar het runbook wordt uitgevoerd. Wanneer u waarschuwingen gebruikt om runbooks te activeren, is het belangrijk om de status van de waarschuwing in het runbook dat wordt geactiveerd, te controleren. Het runbook wordt geactiveerd telkens wanneer de waarschuwing wordt weergegeven. Waarschuwingen hebben meerdere toestanden, `Activated` de `Resolved`twee meest voorkomende staten zijn en . Controleer deze status in de logica van uw runbook om ervoor te zorgen dat uw runbook niet meer dan één keer wordt uitgevoerd. Het voorbeeld in dit artikel `Activated` laat zien hoe u alleen naar waarschuwingen zoeken.

De runbook gebruikt de **AzureRunAsConnection** [Run As-account](automation-create-runas-account.md) om te verifiëren met Azure om de beheeractie tegen de VM uit te voeren.

Gebruik dit voorbeeld om een runbook te maken met de naam **Stop-AzureVmInResponsetoVMAlert**. U het PowerShell-script wijzigen en het met veel verschillende bronnen gebruiken.

1. Ga naar uw Azure Automation-account.
2. Selecteer **Runbooks**onder **Procesautomatisering**.
3. Selecteer boven aan de lijst met runbooks de optie **+ Maak een runbook**.
4. Typ Op de pagina **Runbook toevoegen** **Stop-AzureVmInResponsetoVMAlert** voor de naam van het runboek. Selecteer **PowerShell**voor het type runbook . Ten slotte selecteert u **Create**.  
5. Kopieer het volgende PowerShell-voorbeeld naar de pagina **Bewerken.**

    ```powershell-interactive
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )
    $ErrorActionPreference = "stop"

    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema)
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "azureMonitorCommonAlertSchema") {
            # This is the common Metric Alert schema (released March 2019)
            $Essentials = [object] ($WebhookBody.data).essentials
            # Get the first target only as this script doesn't handle multiple
            $alertTargetIdArray = (($Essentials.alertTargetIds)[0]).Split("/")
            $SubId = ($alertTargetIdArray)[2]
            $ResourceGroupName = ($alertTargetIdArray)[4]
            $ResourceType = ($alertTargetIdArray)[6] + "/" + ($alertTargetIdArray)[7]
            $ResourceName = ($alertTargetIdArray)[-1]
            $status = $Essentials.monitorCondition
        }
        elseif ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # Schema not supported
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if (($status -eq "Activated") -or ($status -eq "Fired"))
        {
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Determine code path depending on the resourceType
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is an Resource Manager VM
                Write-Verbose "This is an Resource Manager VM." -Verbose

                # Authenticate to Azure with service principal and certificate and set subscription
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the Resource Manager VM
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType not supported
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated' or 'Fired' so no action taken
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```

6. Selecteer **Publiceren** om het runbook op te slaan en te publiceren.

## <a name="create-the-alert"></a>De waarschuwing maken

Waarschuwingen gebruiken actiegroepen, verzamelingen van acties die door de waarschuwing worden geactiveerd. Runbooks zijn slechts een van de vele acties die u gebruiken met actiegroepen.

1. Selecteer in uw automatiseringsaccount de optie **Waarschuwingen** onder **Bewaken**.
1. Selecteer **+ Nieuwe waarschuwingsregel**.
1. Klik op **Selecteren** onder **Bron**. Selecteer **op** de pagina Een resource selecteren de optie u wilt dat u een waarschuwing wilt afgeven en klik op **Gereed**.
1. Klik **op Voorwaarde toevoegen** onder **Voorwaarde**. Selecteer het signaal dat u wilt gebruiken, bijvoorbeeld **Percentage CPU** en klik op **Gereed**.
1. Voer op de pagina **Signaallogica configureren** de **drempelwaarde** in onder **de logica Waarschuwing**en klik op **Gereed**.
1. Selecteer **Onder Actiegroepen**de optie **Nieuw maken**.
1. Geef uw actiegroep op de pagina **Actiegroep toevoegen** een naam en een korte naam.
1. Geef de actie een naam. Selecteer **Automatiseringsrunboek voor**het actietype .
1. Selecteer **Details bewerken**. Selecteer **Op**de pagina **Runbook configureren** onder **Runbook-bron**de optie Gebruiker .  
1. Selecteer uw **abonnements-** en **automatiseringsaccount**en selecteer vervolgens het **runbook Stop-AzureVmInResponsetoVMAlert.**  
1. Selecteer **Ja** voor **Het algemene waarschuwingsschema inschakelen**.
1. Als u de actiegroep wilt maken, selecteert u **OK**.

    ![Pagina met actiegroep toevoegen](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    U deze actiegroep gebruiken in de [waarschuwingen voor activiteitenlogboeken](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) en [bijna realtime waarschuwingen](../azure-monitor/platform/alerts-overview.md?toc=%2fazure%2fautomation%2ftoc.json) die u maakt.

1. Voeg onder **Waarschuwingsgegevens**een naam en beschrijving van de waarschuwingsregel toe en klik op **Waarschuwingsregel maken**.

## <a name="next-steps"></a>Volgende stappen

* Zie [Een runbook starten vanaf een webhook](automation-webhooks.md)voor meer informatie over het starten van een runbook voor automatisering met behulp van een webhook.
* Zie [Een runbook starten](automation-starting-a-runbook.md)voor meer informatie over verschillende manieren om een runbook te starten.
* Zie [Waarschuwingen voor activiteitenlogboeken maken](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)voor meer informatie over het maken van een waarschuwing voor een activiteitenlogboek.
* Zie [Een waarschuwingsregel maken in de Azure-portal](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json)voor meer informatie over het maken van een bijna realtime waarschuwing.
