---
title: Azure Service Bus-bericht eenheden automatisch bijwerken
description: In dit artikel wordt beschreven hoe u een Azure Automation runbook kunt gebruiken om automatisch bericht eenheden bij te werken van een Service Bus naam ruimte.
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: spelluru
ms.topic: how-to
ms.date: 05/14/2020
ms.author: spelluru
ms.openlocfilehash: 5ece7beaea709c9b1e52cf2130484663da0aa4ac
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664548"
---
# <a name="automatically-update-messaging-units-of-an-azure-service-bus-namespace"></a>Bericht eenheden van een Azure Service Bus naam ruimte automatisch bijwerken 
Dit artikel laat u zien hoe u automatisch de [Messa ging-eenheden](service-bus-premium-messaging.md) van een service bus naam ruimte kunt bijwerken op basis van resource (CPU of geheugen) gebruik. 

In het voor beeld in dit artikel ziet u hoe u de Messa ging-eenheden voor een Service Bus naam ruimte kunt verg Roten wanneer het CPU-gebruik van de naam ruimte meer dan 75% overschrijdt. De stappen op hoog niveau zijn:

1. Maak een Azure Automation runbook met een Power shell-script waarmee de bericht eenheden voor een Service Bus naam ruimte worden geschaald. 
2. Maak een waarschuwing voor CPU-gebruik op de naam ruimte Service Bus, die het Power shell-script aanroept wanneer het CPU-gebruik van de naam ruimte meer dan 75% overschrijdt. 

> [!IMPORTANT]
> Dit artikel is alleen van toepassing op de **Premium** -laag van Azure service bus. 


## <a name="create-a-service-bus-namespace"></a>Een Service Bus-naamruimte maken
Maak een Service Bus naam ruimte voor een premier-laag. Volg de stappen in het artikel [een naam ruimte maken in het Azure Portal](service-bus-quickstart-portal.md#create-a-namespace-in-the-azure-portal) om de naam ruimte te maken. 

## <a name="create-an-azure-automation-account"></a>Een Azure Automation-account maken
Maak een Azure Automation-account door de instructies in het artikel [een Azure Automation-account maken](../automation/automation-quickstart-create-account.md) te volgen. 

## <a name="import-azservice-module-from-gallery"></a>Importeren AZ. service module uit galerie
Importeer `Az.Accounts` en `Az.ServiceBus` modules vanuit de galerie naar het Automation-account. De `Az.ServiceBus` module is afhankelijk van de `Az.Accounts` module en moet eerst worden geïnstalleerd. 

Zie [een module importeren vanuit de module galerie](../automation/automation-runbook-gallery.md#import-a-module-from-the-module-gallery-with-the-azure-portal)voor stapsgewijze instructies.

## <a name="create-and-publish-a-powershell-runbook"></a>Een Power shell-runbook maken en publiceren

1. Maak een Power shell-runbook door de instructies in het artikel [een Power shell-Runbook maken](../automation/automation-quickstart-create-runbook.md) te volgen. 

    Hier volgt een Power shell-voorbeeld script dat u kunt gebruiken om de berichten eenheden voor een Service Bus naam ruimte te verg Roten. Dit Power shell-script in een Automation-runbook verhoogt MUs van 1 tot 2, 2 tot 4 of 4 tot en met 8. De toegestane waarden voor deze eigenschap zijn: 1, 2, 4 en 8. U kunt een ander runbook maken om de Messa ging-eenheden te verlagen.

    De para meters **Namespace** en **resourceGroupName** worden gebruikt om het script afzonderlijk van een waarschuwings scenario te testen. 
    
    De para meter **WebHookData** is voor de waarschuwing dat er gegevens worden door gegeven, zoals de naam van de resource groep, de resource naam, enzovoort tijdens runtime. 

    ```powershell
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData,
    
        [Parameter (Mandatory = $false)]
        [String] $namespaceName,
    
        [Parameter (Mandatory = $false)]
        [String] $resourceGroupName
    )
    
    
    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    
        # Get the alert schema ID
        $schemaId = $WebhookBody.schemaId

        # If it's a metric alert
        if ($schemaId -eq "AzureMonitorMetricAlert") {

            # Get the resource group name from the alert context
            $resourceGroupName = $AlertContext.resourceGroupName
            
            # Get the namespace name from the alert context
            $namespaceName = $AlertContext.resourceName
        }
    }
    
    # Connect to Azure account
    $connection = Get-AutomationConnection -Name AzureRunAsConnection
    
    while(!($connectionResult) -And ($logonAttempt -le 10))
    {
        $LogonAttempt++
        # Logging in to Azure...
        $connectionResult =    Connect-AzAccount `
                                    -ServicePrincipal `
                                    -Tenant $connection.TenantID `
                                    -ApplicationId $connection.ApplicationID `
                                    -CertificateThumbprint $connection.CertificateThumbprint
    
        Start-Sleep -Seconds 30
    }
    
    # Get the current capacity (number of messaging units) of the namespace
    $sbusns=Get-AzServiceBusNamespace `
        -Name $namespaceName `
        -ResourceGroupName $resourceGroupName
    
    $currentCapacity = $sbusns.Sku.Capacity
    
    # Increase the capacity
    # Capacity can be one of these values: 1, 2, 4, 8
    if ($currentCapacity -eq 1) {
        $newMU = 2
    }
    elseif ($currentCapacity -eq 2) {
        $newMU = 4
    }
    elseif ($currentCapacity -eq 4) {
        $newMU = 8    
    }
    else {
    
    }
    
    # Update the capacity of the namespace
    Set-AzServiceBusNamespace `
            -Location eastus `
            -SkuName Premium `
            -Name $namespaceName `
            -SkuCapacity $newMU `
            -ResourceGroupName $resourceGroupName

    ```
2. [Test de werkmap](../automation/manage-runbooks.md#test-a-runbook) door waarden op te geven voor de para meters **Namespace** en **resourceGroupName** . Controleer of de Messa ging-eenheden in de naam ruimte zijn bijgewerkt. 
3. Nadat u hebt getest, kunt u [de werkmap publiceren](..//automation/manage-runbooks.md#publish-a-runbook) zodat deze beschikbaar is voor het toevoegen van een actie voor een waarschuwing in de naam ruimte. 

## <a name="create-an-alert-on-the-namespace-to-trigger-the-runbook"></a>Een waarschuwing maken voor de naam ruimte om het runbook te activeren
Zie een [waarschuwing gebruiken om een Azure Automation runbook te activeren](../automation/automation-create-alert-triggered-runbook.md) voor het configureren van een waarschuwing op uw service bus naam ruimte om het Automation-runbook te activeren dat u hebt gemaakt. U kunt bijvoorbeeld een waarschuwing maken voor het **CPU-gebruik per naam ruimte** of het gebruik van de **geheugen grootte per** metrische waarde van de naam ruimte en een actie toevoegen om het Automation-runbook te activeren dat u hebt gemaakt. Zie metrische gegevens over het [resource gebruik](service-bus-metrics-azure-monitor.md#resource-usage-metrics)voor meer informatie over deze metrische gegevens.

De volgende procedure laat zien hoe u een waarschuwing maakt waarmee het Automation-runbook wordt geactiveerd wanneer het **CPU-gebruik** van de naam ruimte meer dan **75%** overschrijdt.

1. Selecteer op de pagina **Service Bus naam ruimte** voor uw naam ruimte de optie **waarschuwingen** in het menu links en selecteer vervolgens **+ nieuwe waarschuwings regel** op de werk balk. 
    
    ![Pagina waarschuwingen-knop nieuwe waarschuwings regel](./media/automate-update-messaging-units/alerts-page.png)
2. Klik op de pagina **waarschuwings regel maken** op **voor waarde selecteren**. 

    ![Pagina waarschuwings regel maken-voor waarde selecteren](./media/automate-update-messaging-units/alert-rule-select-condition.png) 
3. Selecteer op de pagina **signaal logica configureren** de optie **CPU** voor het signaal. 

    ![Signaal logica configureren: Selecteer CPU](./media/automate-update-messaging-units/configure-signal-logic.png)
4. Voer een **drempel waarde** in (in dit voor beeld is dit **75%**) en selecteer **gereed**. 

    ![CPU-signaal configureren](./media/automate-update-messaging-units/cpu-signal-configuration.png)
5. Klik nu op de **pagina waarschuwing maken**op **actie groep selecteren**.
    
    ![Actie groep selecteren](./media/automate-update-messaging-units/select-action-group-button.png)
6. Selecteer de knop **actie groep maken** op de werk balk. 

    ![Knop actie groep maken](./media/automate-update-messaging-units/create-action-group-button.png)
7. Voer op de pagina **actie groep toevoegen** de volgende stappen uit:
    1. Voer een **naam** in voor de actie groep. 
    2. Voer een **korte naam** in voor de actie groep.
    3. Selecteer het **abonnement** waarin u deze actie groep wilt maken.
    4. Selecteer de **resource groep**. 
    5. Voer in de sectie **acties** een **naam in voor de actie**en selecteer **Automation-Runbook** voor **actie type**. 

        ![Pagina actie groep toevoegen](./media/automate-update-messaging-units/add-action-group-page.png)
8. Voer op de pagina **Runbook configureren** de volgende stappen uit:
    1. Selecteer voor **Runbook**-bron **gebruiker**. 
    2. Selecteer bij **abonnement**het Azure- **abonnement** met het Automation-account. 
    3. Voor **Automation-account**selecteert u uw **Automation-account**.
    4. Selecteer uw runbook voor **runbook**. 
    5. Selecteer **OK** op de pagina **Runbook configureren** . 
        ![Runbook configureren](./media/automate-update-messaging-units/configure-runbook.png)
9. Selecteer **OK** op de pagina **actie groep toevoegen** . 
5. Voer nu op de pagina **waarschuwings regel maken** een naam in **voor de regel**en selecteer vervolgens **waarschuwings regel maken**. 
    ![Waarschuwingsregel maken](./media/automate-update-messaging-units/create-alert-rule.png)

    > [!NOTE]
    > Wanneer het CPU-gebruik van de naam ruimte meer dan 75, wordt het Automation-runbook door de waarschuwing geactiveerd, waardoor de Messa ging-eenheden van de Service Bus naam ruimte worden verhoogd. Op dezelfde manier kunt u een waarschuwing maken voor een ander Automation-runbook, waardoor de Messa ging-eenheden worden verkleind als het CPU-gebruik van de naam ruimte lager is dan 25. 

## <a name="next-steps"></a>Volgende stappen
Voor meer informatie over Messa ging-eenheden raadpleegt u de [Premium Messa ging](service-bus-premium-messaging.md)