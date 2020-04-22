---
title: Gegevens die u moet verzamelen wanneer u een aanvraag opent voor Microsoft Azure Automation| Microsoft Documenten
description: In dit artikel worden enkele gegevens beschreven die u moet verzamelen voordat u een aanvraag opent voor Azure Automation met Microsoft Azure Support.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: v-miegge
ms.openlocfilehash: e1d5d791a58f301991819b41757b9021f6e30fc0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679408"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Te verzamelen gegevens wanneer u een case opent voor Microsoft Azure Automation

In dit artikel worden enkele gegevens beschreven die u moet verzamelen voordat u een aanvraag opent voor Azure Automation met Microsoft Azure Support. Deze informatie is niet vereist om de aanvraag te openen. Het kan Microsoft echter helpen uw probleem sneller op te lossen. Ook kan u worden gevraagd om deze gegevens door de support engineer nadat u de aanvraag hebt geopend.

## <a name="basic-data"></a>Basisgegevens

Verzamel de basisgegevens die worden beschreven in het Knowledge Base-artikel [4034605 - Hoe maak je azure automation-scripted diagnostics vast .](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)

## <a name="data-for-update-management-issues-on-linux"></a>Problemen met het beheer van gegevens voor updates op Linux

1. Voer naast de items die in KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)worden aangeboden, het volgende gereedschap logboekverzameling uit:

   [OMS Linux Agent Log Collector](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Comprimeer de inhoud van de **map /var/opt/microsoft/omsagent/run/automationworker/map** en stuur het gecomprimeerde bestand vervolgens naar Azure Support.
 
3. Controleer of de id voor de werkruimte waaraan de Log Analytics-agent voor Linux rapporteert, dezelfde is als de id voor de werkruimte die wordt gecontroleerd op updates.

## <a name="data-for-update-management-issues-on-windows"></a>Problemen met gegevens voor updatebeheer op Windows

1. Gegevens verzamelen voor de items in [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Exporteer de volgende gebeurtenislogboeken naar de EVTX-indeling:

   * Systeem
   * Toepassing
   * Beveiliging
   * Operations Manager
   * Microsoft-SMA/Operationeel

3. Controleer of de id van de werkruimte waaraan de agent rapporteert, dezelfde is als de id voor de werkruimte die wordt bewaakt door Windows Updates.

## <a name="data-for-job-issues"></a>Gegevens voor taakproblemen

1. Gegevens verzamelen voor de items in [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Verzamel de taak-id voor de taak die een probleem heeft:

   1. Ga in de Azure-portal naar **Automatiseringsaccounts**.
   2. Selecteer het automatiseringsaccount dat u probleemoplossing biedt en noteer de naam.
   3. Selecteer **Vacatures**.
   4. Kies de taak die u probleemoplossing voor komt.
   5. Zoek in het deelvenster Taakoverzicht naar de GUID-waarde in **Taak-ID**.

   ![Taak-id in het deelvenster Overzicht van taken](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. Verzamel een voorbeeld van het script dat u uitvoert.

4. Verzamel de logbestanden:

   1. Ga in de Azure-portal naar **Automatiseringsaccounts**.
   2. Selecteer het automatiseringsaccount dat u probleemoplossing biedt.
   3. Selecteer **Vacatures**.
   4. Kies de taak die u probleemoplossing voor komt.
   5. Selecteer **Alle logboeken**.
   6. Verzamel de gegevens in het resulterende deelvenster.

   ![Gegevens die worden vermeld onder Alle logboeken](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>Gegevens voor moduleproblemen

Naast de [basisgegevens items](#basic-data), verzamelen van de volgende informatie:

* De stappen die u hebt gevolgd, zodat het probleem kan worden gereproduceerd.
* Schermafbeeldingen van foutberichten.
* Screenshots van de huidige modules en hun versienummers.

## <a name="next-steps"></a>Volgende stappen

Als je meer hulp nodig hebt:

* Krijg antwoorden van Azure-experts via [Azure Forums.](https://azure.microsoft.com/support/forums/)
* Maak [@AzureSupport](https://twitter.com/azuresupport)verbinding met het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.
* Een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.
