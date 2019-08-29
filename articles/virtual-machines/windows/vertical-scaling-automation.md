---
title: Gebruik Azure Automation om virtuele Windows-machines verticaal te schalen | Microsoft Docs
description: Een virtuele Windows-machine verticaal schalen als reactie op het controleren van waarschuwingen met Azure Automation
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4f964713-fb67-4bcc-8246-3431452ddf7d
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/18/2019
ms.author: kasing
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 71af54f0a1dd7ecd4d4f0dc6f7a465439993db39
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100170"
---
# <a name="vertically-scale-windows-vms-with-azure-automation"></a>Windows-Vm's verticaal schalen met Azure Automation

Verticaal schalen is het proces van het verg Roten of verkleinen van de resources van een machine als reactie op de werk belasting. In azure kunt u dit doen door de grootte van de virtuele machine te wijzigen. Dit kan helpen in de volgende scenario's:

* Als de virtuele machine niet regel matig wordt gebruikt, kunt u het formaat verlagen tot een kleinere grootte om uw maandelijkse kosten te reduceren
* Als de virtuele machine een piek belasting krijgt, kan de grootte worden gewijzigd in een grotere grootte om de capaciteit te verg Roten

Het overzicht voor de stappen die u moet uitvoeren, is als volgt:

1. Setup Azure Automation om toegang te krijgen tot uw Virtual Machines
2. De Azure Automation verticale schaal van runbooks importeren in uw abonnement
3. Een webhook toevoegen aan uw runbook
4. Een waarschuwing toevoegen aan uw virtuele machine


## <a name="scale-limitations"></a>Schaal beperkingen

Omdat de grootte van de eerste virtuele machine kan worden geschaald, kan de grootte worden beperkt door de beschik baarheid van de andere grootten in de cluster huidige virtuele machine wordt geïmplementeerd in. In de gepubliceerde Automation-runbooks die in dit artikel worden gebruikt, wordt dit geval in rekening gebracht en schaalt deze alleen binnen de onderstaande VM-grootte paren. Dit betekent dat een virtuele machine van Standard_D1v2 niet plotseling kan worden geschaald naar Standard_G5 of omlaag geschaald naar Basic_A0. Ook omhoog/omlaag schalen van beperkte virtuele machines wordt niet ondersteund. 

U kunt kiezen tussen de volgende combi Naties van grootten:

* [A-serie](#a-series)
* [B-serie](#b-series)
* [D-serie](#d-series)
* [E-serie](#e-series)
* [F-serie](#f-series)
* [G-serie](#g-series)
* [H-serie](#h-series)
* [L-serie](#l-series)
* [M-serie](#m-series)
* [N-serie](#n-series)

### <a name="a-series"></a>A-serie

| Begin grootte | Grootte omhoog schalen | 
| --- | --- |
| Basic_A0 | Basic_A1 |
| Basic_A1 | Basic_A2 |
| Basic_A2 | Basic_A3 |
| Basic_A3 | Basic_A4 |
| Standard_A0 | Standard_A1 |
| Standard_A1 | Standard_A2 |
| Standard_A2 | Standard_A3 |
| Standard_A3 | Standard_A4 |
| Standard_A5 | Standard_A6 |
| Standard_A6 | Standard_A7 |
| Standard_A8 | Standard_A9 |
| Standard_A10 | Standard_A11 |
| Standard_A1_v2 | Standard_A2_v2 |
| Standard_A2_v2 | Standard_A4_v2 |
| Standard_A4_v2 | Standard_A8_v2 |
| Standard_A2m_v2 | Standard_A4m_v2 |
| Standard_A4m_v2 | Standard_A8m_v2 |

### <a name="b-series"></a>B-serie

| Begin grootte | Grootte omhoog schalen | 
| --- | --- |
| Standard_B1s | Standard_B2s |
| Standard_B1ms | Standard_B2ms |
| Standard_B2ms | Standard_B4ms |
| Standard_B4ms | Standard_B8ms |

### <a name="d-series"></a>D-serie

| Begin grootte | Grootte omhoog schalen | 
| --- | --- |
| Standard_D1 | Standard_D2 |
| Standard_D2 | Standard_D3 |
| Standard_D3 | Standard_D4 |
| Standard_D11 | Standard_D12 |
| Standard_D12 | Standard_D13 |
| Standard_D13 | Standard_D14 |
| Standard_DS1 | Standard_DS2 |
| Standard_DS2 | Standard_DS3 |
| Standard_DS3 | Standard_DS4 |
| Standard_DS11 | Standard_DS12 |
| Standard_DS12 | Standard_DS13 |
| Standard_DS13 | Standard_DS14 |
| Standard_D1_v2 | Standard_D2_v2 |
| Standard_D2_v2 | Standard_D3_v2 |
| Standard_D3_v2 | Standard_D4_v2 |
| Standard_D4_v2 | Standard_D5_v2 |
| Standard_D11_v2 | Standard_D12_v2 |
| Standard_D12_v2 | Standard_D13_v2 |
| Standard_D13_v2 | Standard_D14_v2 |
| Standard_DS1_v2 | Standard_DS2_v2 |
| Standard_DS2_v2 | Standard_DS3_v2 |
| Standard_DS3_v2 | Standard_DS4_v2 |
| Standard_DS4_v2 | Standard_DS5_v2 |
| Standard_DS11_v2 | Standard_DS12_v2 |
| Standard_DS12_v2 | Standard_DS13_v2 |
| Standard_DS13_v2 | Standard_DS14_v2 |
| Standard_D2_v3 | Standard_D4_v3 |
| Standard_D4_v3 | Standard_D8_v3 |
| Standard_D8_v3 | Standard_D16_v3 |
| Standard_D16_v3 | Standard_D32_v3 |
| Standard_D32_v3 | Standard_D64_v3 |
| Standard_D2s_v3 | Standard_D4s_v3 |
| Standard_D4s_v3 | Standard_D8s_v3 |
| Standard_D8s_v3 | Standard_D16s_v3 |
| Standard_D16s_v3 | Standard_D32s_v3 |
| Standard_D32s_v3 | Standard_D64s_v3 |
| Standard_DC2s | Standard_DC4s |

### <a name="e-series"></a>E-serie

| Begin grootte | Grootte omhoog schalen | 
| --- | --- |
| Standard_E2_v3 | Standard_E4_v3 |
| Standard_E4_v3 | Standard_E8_v3 |
| Standard_E8_v3 | Standard_E16_v3 |
| Standard_E16_v3 | Standard_E20_v3 |
| Standard_E20_v3 | Standard_E32_v3 |
| Standard_E32_v3 | Standard_E64_v3 |
| Standard_E2s_v3 | Standard_E4s_v3 |
| Standard_E4s_v3 | Standard_E8s_v3 |
| Standard_E8s_v3 | Standard_E16s_v3 |
| Standard_E16s_v3 | Standard_E20s_v3 |
| Standard_E20s_v3 | Standard_E32s_v3 |
| Standard_E32s_v3 | Standard_E64s_v3 |

### <a name="f-series"></a>F-serie

| Begin grootte | Grootte omhoog schalen | 
| --- | --- |
| Standard_F1 | Standard_F2 |
| Standard_F2 | Standard_F4 |
| Standard_F4 | Standard_F8 |
| Standard_F8 | Standard_F16 |
| Standard_F1s | Standard_F2s |
| Standard_F2s | Standard_F4s |
| Standard_F4s | Standard_F8s |
| Standard_F8s | Standard_F16s |
| Standard_F2s_v2 | Standard_F4s_v2 |
| Standard_F4s_v2 | Standard_F8s_v2 |
| Standard_F8s_v2 | Standard_F16s_v2 |
| Standard_F16s_v2 | Standard_F32s_v2 |
| Standard_F32s_v2 | Standard_F64s_v2 |
| Standard_F64s_v2 | Standard_F7s_v2 |

### <a name="g-series"></a>G-reeks

| Begin grootte | Grootte omhoog schalen | 
| --- | --- |
| Standard_G1 | Standard_G2 |
| Standard_G2 | Standard_G3 |
| Standard_G3 | Standard_G4 |
| Standard_G4 | Standard_G5 |
| Standard_GS1 | Standard_GS2 |
| Standard_GS2 | Standard_GS3 |
| Standard_GS3 | Standard_GS4 |
| Standard_GS4 | Standard_GS5 |

### <a name="h-series"></a>H-serie

| Begin grootte | Grootte omhoog schalen | 
| --- | --- |
| Standard_H8 | Standard_H16 |
| Standard_H8m | Standard_H16m |

### <a name="l-series"></a>L-serie

| Begin grootte | Grootte omhoog schalen | 
| --- | --- |
| Standard_L4s | Standard_L8s |
| Standard_L8s | Standard_L16s |
| Standard_L16s | Standard_L32s |
| Standard_L8s_v2 | Standard_L16s_v2 |
| Standard_L16s_v2 | Standard_L32s_v2 |
| Standard_L32s_v2 | Standard_L64s_v2 |
| Standard_L64s_v2 | Standard_L80s_v2 |

### <a name="m-series"></a>M-serie

| Begin grootte | Grootte omhoog schalen | 
| --- | --- |
| Standard_M8ms | Standard_M16ms |
| Standard_M16ms | Standard_M32ms |
| Standard_M32ms | Standard_M64ms |
| Standard_M64ms | Standard_M128ms |
| Standard_M32ls | Standard_M64ls |
| Standard_M64s | Standard_M128s |
| Standard_M64 | Standard_M128 |
| Standard_M64m | Standard_M128m |

### <a name="n-series"></a>N-serie

| Begin grootte | Grootte omhoog schalen | 
| --- | --- |
| Standard_NC6 | Standard_NC12 |
| Standard_NC12 | Standard_NC24 |
| Standard_NC6s_v2 | Standard_NC12s_v2 |
| Standard_NC12s_v2 | Standard_NC24s_v2 |
| Standard_NC6s_v3 | Standard_NC12s_v3 |
| Standard_NC12s_v3 | Standard_NC24s_v3 |
| Standard_ND6 | Standard_ND12 |
| Standard_ND12 | Standard_ND24 |
| Standard_NV6 | Standard_NV12 |
| Standard_NV12 | Standard_NV24 |
| Standard_NV6s_v2 | Standard_NV12s_v2 |
| Standard_NV12s_v2 | Standard_NV24s_v2 |
| Standard_NV12s_v3 |Standard_NV48s_v3 |

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Setup Azure Automation om toegang te krijgen tot uw Virtual Machines
Het eerste wat u moet doen, is een Azure Automation-account maken dat als host fungeert voor de runbooks die worden gebruikt om een virtuele machine te schalen. De Automation-Service heeft onlangs de functie ' uitvoeren als-account ' geïntroduceerd waarmee de service-principal wordt ingesteld voor het automatisch uitvoeren van runbooks in de gebruikers. Meer informatie hierover vindt u in het volgende artikel:

* [Runbooks verifiëren met een Azure Uitvoeren als-account](../../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>De Azure Automation verticale schaal van runbooks importeren in uw abonnement
De runbooks die nodig zijn voor het verticaal schalen van uw virtuele machine, worden al gepubliceerd in de galerie met Azure Automation Runbook. U moet deze importeren in uw abonnement. Lees het volgende artikel voor meer informatie over het importeren van runbooks.

* [Runbook- en modulegalerieën voor Azure Automation](../../automation/automation-runbook-gallery.md)

De runbooks die moeten worden geïmporteerd, worden weer gegeven in de onderstaande afbeelding

![Runbooks importeren](./media/vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Een webhook toevoegen aan uw runbook
Wanneer u de runbooks hebt geïmporteerd, moet u een webhook toevoegen aan het runbook zodat deze kan worden geactiveerd door een waarschuwing van een virtuele machine. De details van het maken van een webhook voor uw Runbook kunnen hier worden gelezen

* [Azure Automation webhooks](../../automation/automation-webhooks.md)

Zorg ervoor dat u de webhook kopieert voordat u het dialoog venster webhook sluit wanneer u dit nodig hebt in de volgende sectie.

## <a name="add-an-alert-to-your-virtual-machine"></a>Een waarschuwing toevoegen aan uw virtuele machine
1. Instellingen voor virtuele machine selecteren
2. Selecteer waarschuwings regels
3. Selecteer Waarschuwing toevoegen
4. Selecteer een metrische waarde om de waarschuwing te activeren
5. Selecteer een voor waarde, die tijdens de afronding tot gevolg heeft dat de waarschuwing wordt geactiveerd
6. Selecteer in stap 5 een drempel waarde voor de voor waarde. te voldoen
7. Selecteer een periode gedurende welke de bewakings service zal controleren op de voor waarde en drempel waarde in stap 5 & 6
8. Plak de webhook die u hebt gekopieerd uit de vorige sectie.

![Waarschuwing toevoegen aan virtuele machine 1](./media/vertical-scaling-automation/add-alert-webhook-1.png)

![Waarschuwing toevoegen aan virtuele machine 2](./media/vertical-scaling-automation/add-alert-webhook-2.png)

