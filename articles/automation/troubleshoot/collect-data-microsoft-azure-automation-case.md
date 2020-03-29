---
title: Gegevens die u moet verzamelen wanneer u een aanvraag opent voor Microsoft Azure Automation| Microsoft Documenten
description: In dit artikel worden enkele basisgegevens beschreven die u moet verzamelen voordat u een aanvraag opent voor Azure Automation met Microsoft Azure Support.
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
ms.openlocfilehash: 4839ce7a0188c782656fd3a4c42cbdd116b165e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849374"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Te verzamelen gegevens wanneer u een case opent voor Microsoft Azure Automation

In dit artikel worden enkele basisgegevens beschreven die u moet verzamelen voordat u een aanvraag opent voor Azure Automation met Microsoft Azure Support. Deze informatie is niet vereist om de aanvraag te openen. Het kan Microsoft echter helpen uw probleem sneller op te lossen. Ook kan u worden gevraagd om deze gegevens door de support engineer nadat u de aanvraag hebt geopend.

## <a name="collect-more-information"></a>Meer informatie verzamelen

Voordat u een aanvraag opent voor Microsoft Azure Automation Support, raden we u aan de volgende gegevens te verzamelen.

### <a name="basic-data-collection"></a>Basisgegevensverzameling

Verzamel de gegevens beschreven in het volgende Knowledge Base-artikel:

* [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) Hoe u diagnoses met Azure Automation-scripted vastleggen

## <a name="collect-data-depending-on-issue"></a>Gegevens verzamelen, afhankelijk van het probleem
 
### <a name="for-issues-that-affect-update-management-on-linux"></a>Voor problemen die van invloed zijn op updatebeheer op Linux

1. Voer naast de items die in KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)worden aangeboden, het volgende gereedschap logboekverzameling uit:

   [OMS Linux Agent Log Collector](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Comprimeer de inhoud van de volgende map en stuur het gecomprimeerde bestand naar Azure Support:

   ``/var/opt/microsoft/omsagent/run/automationworker/``
 
3. Controleer of de werkruimte-id waaraan de OMS Linux Agent rapporteert, dezelfde is als de werkruimte die wordt gecontroleerd op updates.

### <a name="for-issues-that-affect-update-management-on-windows"></a>Voor problemen die van invloed zijn op updatebeheer in Windows

Naast de items die worden vermeld in [4034605,](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)ga dan als volgt te werk:

1. Exporteer de volgende gebeurtenislogboeken naar de EVTX-indeling:

   * Systeem
   * Toepassing
   * Beveiliging
   * Operations Manager
   * Microsoft-SMA/Operationeel

2. Controleer of de werkruimte-id waaraan de agent rapporteert, dezelfde is als de werkruimte die wordt bewaakt door Windows Updates.

### <a name="for-issues-that-affect-a-job"></a>Voor problemen die van invloed zijn op een taak

Naast de items die worden vermeld in [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), verzamelen de volgende informatie:

1. Verzamel het **JobID-nummer** (voor de taak die een probleem ondervindt):

   1. Ga op de Azure-portal naar het blade **Automatiseringsaccounts.**
   2. Selecteer het **automatiseringsaccount** dat u probleemoplossing biedt.
   3. Selecteer **Vacatures**.
   4. Selecteer de taak die u probleemoplossing voor komt.
   5. Zoek **onder Functieoverzicht**naar een **taak-ID** (GUID).

   ![Taak-id in het deelvenster Overzicht van taken](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

2. Verzamel de accountnaam:

   1. Ga op de Azure-portal naar het blade **Automatiseringsaccounts.**
   2. Krijg de naam van het **automatiseringsaccount** waarmee u problemen oplost.

3. Verzamel een voorbeeld van het script dat u uitvoert.

4. Verzamel de logbestanden:

   1. Ga op de Azure-portal naar het blade **Automatiseringsaccounts.**
   2. Selecteer het **automatiseringsaccount** dat u probleemoplossing biedt.
   3. Selecteer **Vacatures**.
   4. Selecteer de taak die u probleemoplossing voor komt.
   5. Selecteer **Alle logboeken**.
   6. Op het resulterende mes, het verzamelen van de gegevens.

   ![Gegevens die worden vermeld onder Alle logboeken](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

### <a name="for-issues-that-affect-modules"></a>Voor problemen die van invloed zijn op modules

Naast de items onder "Basisgegevensverzameling", verzamel je de volgende informatie:

* De stappen die u hebt gevolgd, zodat het probleem kan worden gereproduceerd.
* Een screenshot van eventuele foutmeldingen.
* Een screenshot van de huidige modules en hun versienummers.

## <a name="next-steps"></a>Volgende stappen

Als u op enig moment in dit artikel meer hulp nodig hebt, neemt u contact op met de Azure-experts op [de forums MSDN Azure en Stack Overflow.](https://azure.microsoft.com/support/forums/)

U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.
