---
title: Azure-setvan virtuele machineschaalverticaal schalen
description: Een virtuele machine verticaal schalen als reactie op bewakingswaarschuwingen met Azure Automation
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 16b17421-6b8f-483e-8a84-26327c44e9d3
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: manayar
ms.openlocfilehash: fa1dda2907e8400491c8d18897bb41fb9cff49fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76274436"
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Verticale automatische schaal met virtuele machineschaalsets

In dit artikel wordt beschreven hoe u Azure [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/) verticaal schalen met of zonder opnieuw inrichten. 

Verticale schaling, ook wel *scale up* en *scale down*genoemd, betekent het vergroten of verkleinen van virtuele machine (VM) in reactie op een werkbelasting. Vergelijk dit gedrag met [horizontale schaling](virtual-machine-scale-sets-autoscale-overview.md), ook wel *schaal out* genoemd en *schaal in*, waarbij het aantal VM's wordt gewijzigd afhankelijk van de werkbelasting.

Reprovisioning betekent het verwijderen van een bestaande VM en het vervangen door een nieuwe. Wanneer u de grootte van VM's in een virtuele machineschaalset verhoogt of verlaagt, wilt u in sommige gevallen het formaat van bestaande VM's wijzigen en uw gegevens behouden, terwijl u in andere gevallen nieuwe VM's van de nieuwe grootte moet implementeren. Dit document behandelt beide gevallen.

Verticaal schalen kan handig zijn wanneer:

* Een dienst gebouwd op virtuele machines wordt onderbenut (bijvoorbeeld in het weekend). Het verminderen van de VM-grootte kan de maandelijkse kosten verlagen.
* Het vergroten van de VM-grootte om aan grotere vraag te voldoen zonder extra VM's te maken.

U verticale schaling instellen om te worden geactiveerd op basis van metrische waarschuwingen van uw virtuele machineschaalset. Wanneer de waarschuwing is geactiveerd, wordt een webhook geactiveerd die een runbook activeert die uw schaal kan schalen. Verticale schaling kan worden geconfigureerd door de volgende stappen te volgen:

1. Maak een Azure Automation-account met run-as-mogelijkheden.
2. Azure Automation Vertical Scale runbooks importeren voor virtuele machineschaalsets in uw abonnement.
3. Voeg een webhook toe aan je runbook.
4. Voeg een waarschuwing toe aan de schaalset van uw virtuele machine met behulp van een webhook-melding.

> [!NOTE]
> Vanwege de grootte van de eerste virtuele machine, de maten die het kan worden geschaald, kan worden beperkt als gevolg van de beschikbaarheid van de andere maten in het cluster huidige Virtual Machine is geïmplementeerd in. In de gepubliceerde automatiseringsrunbooks die in dit artikel worden gebruikt, behandelen we deze case en schalen we alleen binnen de onderstaande VM-grootteparen. Dit betekent dat een Standard_D1v2 Virtual Machine niet plotseling wordt opgeschaald naar Standard_G5 of verkleind tot Basic_A0. Ook beperkte Virtuele Machine-formaten omhoog/omlaag worden niet ondersteund. U ervoor kiezen om te schalen tussen de volgende paren van maten:
> 
> | Schaalpaar vm-formaten |  |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_A1_v2 |Standard_A8_v2 |
> | Standard_A2m_v2 |Standard_A8m_v2  |
> | Standard_B1s |Standard_B2s |
> | Standard_B1ms |Standard_B8ms |
> | Standard_D1 |Standard_D4 |
> | Standard_D11 |Standard_D14 |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1_v2 |Standard_D5_v2 |
> | Standard_D11_v2 |Standard_D14_v2 |
> | Standard_DS1_v2 |Standard_DS5_v2 |
> | Standard_DS11_v2 |Standard_DS14_v2 |
> | Standard_D2_v3 |Standard_D64_v3 |
> | Standard_D2s_v3 |Standard_D64s_v3 |
> | Standard_DC2s |Standard_DC4s |
> | Standard_E2_v3 |Standard_E64_v3 |
> | Standard_E2s_v3 |Standard_E64s_v3 |
> | Standard_F1 |Standard_F16 |
> | Standard_F1s |Standard_F16s |
> | Standard_F2sv2 |Standard_F72sv2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> | Standard_H8 |Standard_H16 |
> | Standard_H8m |Standard_H16m |
> | Standard_L4s |Standard_L32s |
> | Standard_L8s_v2 |Standard_L80s_v2 |
> | Standard_M8ms  |Standard_M128ms |
> | Standard_M32ls  |Standard_M64ls |
> | Standard_M64s  |Standard_M128s |
> | Standard_M64  |Standard_M128 |
> | Standard_M64m  |Standard_M128m |
> | Standard_NC6 |Standard_NC24 |
> | Standard_NC6s_v2 |Standard_NC24s_v2 |
> | Standard_NC6s_v3 |Standard_NC24s_v3 |
> | Standard_ND6s |Standard_ND24s |
> | Standard_NV6 |Standard_NV24 |
> | Standard_NV6s_v2 |Standard_NV24s_v2 |
> | Standard_NV12s_v3 |Standard_NV48s_v3 |
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Een Azure Automation-account maken met run-as-mogelijkheden
Het eerste wat u moet doen, is een Azure Automation-account maken dat de runbooks host die worden gebruikt om de exemplaren van de virtuele machineschaal te schalen. Onlangs introduceerde [Azure Automation](https://azure.microsoft.com/services/automation/) de functie 'Als-account uitvoeren' waarmee de ServicePrincipal is ingesteld voor het automatisch uitvoeren van de runbooks namens een gebruiker. Zie voor meer informatie:

* [Runbooks verifiëren met Azure Uitvoeren als-account](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Azure Automation Vertical Scale-runbooks importeren in uw abonnement

De runbooks die nodig zijn om uw virtuele machineschaalsets verticaal te schalen, zijn al gepubliceerd in de Azure Automation Runbook Gallery. Volg de stappen in dit artikel om ze in uw abonnement te importeren:

* [Runbook- en modulegalerieën voor Azure Automation](../automation/automation-runbook-gallery.md)

Kies de optie Bladeren in galerie in het menu Runbooks:

![Runbooks die moeten worden geïmporteerd][runbooks]

De runbooks die moeten worden geïmporteerd, worden weergegeven. Selecteer de runbook op basis van de vraag of u verticale schaling wilt met of zonder opnieuw inrichten:

![Galerie Runbooks][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Een webhook toevoegen aan uw runbook

Zodra u de runbooks hebt geïmporteerd, voegt u een webhook toe aan het runbook, zodat deze kan worden geactiveerd door een waarschuwing van een virtuele machineschaalset. De details van het maken van een webhook voor uw Runbook worden beschreven in dit artikel:

* [Azure Automation-webhooks](../automation/automation-webhooks.md)

> [!NOTE]
> Zorg ervoor dat u de webhook URI kopieert voordat u het webhook-dialoogvenster sluit, omdat u dit adres in de volgende sectie nodig hebt.
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>Een waarschuwing toevoegen aan uw virtuele machineschaalset

Hieronder vindt u een PowerShell-script dat laat zien hoe u een waarschuwing toevoegt aan een virtuele machineschaalset. Raadpleeg het volgende artikel om de naam van de statistiek te krijgen om de waarschuwing af te vuren op: [Azure Monitor automatisch schalen van algemene statistieken](../azure-monitor/platform/autoscale-common-metrics.md).

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [!NOTE]
> Het wordt aanbevolen om een redelijk tijdvenster voor de waarschuwing te configureren om te voorkomen dat verticale schaling en eventuele bijbehorende serviceonderbrekingen te vaak worden geactiveerd. Overweeg een venster van ten minste 20-30 minuten of meer. Overweeg horizontale schaling als u een onderbreking moet voorkomen.
> 
> 

Zie de volgende artikelen voor meer informatie over het maken van waarschuwingen:

* [Voorbeelden van PowerShell-snelstart van Azure Monitor](../azure-monitor/platform/powershell-quickstart-samples.md)
* [Azure Monitor Cross-platform CLI snelstart voorbeelden](../azure-monitor/platform/cli-samples.md)

## <a name="summary"></a>Samenvatting

Dit artikel toonde eenvoudige verticale schaling voorbeelden. Met deze bouwstenen - Automatiseringsaccount, runbooks, webhooks, waarschuwingen - u een rijke verscheidenheid aan evenementen verbinden met een aangepaste reeks acties.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
