---
title: Gegevens die moeten worden verzameld wanneer u een case opent voor Microsoft Azure automatisering | Microsoft Docs
description: In dit artikel worden enkele van de basis gegevens beschreven die u moet verzamelen voordat u een case opent voor Azure Automation met Microsoft Azure-ondersteuning.
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
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849374"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Te verzamelen gegevens wanneer u een case opent voor Microsoft Azure Automation

In dit artikel worden enkele van de basis gegevens beschreven die u moet verzamelen voordat u een case opent voor Azure Automation met Microsoft Azure-ondersteuning. Deze informatie is niet vereist om de aanvraag te openen. Het kan micro soft echter helpen om uw probleem sneller op te lossen. Het is ook mogelijk dat u voor deze gegevens wordt gevraagd door de ondersteunings technicus nadat u de aanvraag hebt geopend.

## <a name="collect-more-information"></a>Meer informatie verzamelen

Voordat u een aanvraag voor Microsoft Azure automatiserings ondersteuning opent, wordt u aangeraden de volgende informatie te verzamelen.

### <a name="basic-data-collection"></a>Basis gegevens verzamelen

Verzamel de gegevens die worden beschreven in het volgende Knowledge Base-artikel:

* [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) procedures voor het vastleggen van diagnostische gegevens van Azure Automation script

## <a name="collect-data-depending-on-issue"></a>Gegevens verzamelen, afhankelijk van het probleem
 
### <a name="for-issues-that-affect-update-management-on-linux"></a>Voor problemen die van invloed zijn op Updatebeheer in Linux

1. Naast de items die worden vermeld in KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), voert u het volgende hulp programma voor het verzamelen van Logboeken uit:

   [Logboek verzamelaar van OMS Linux-agent](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Comprimeer de inhoud van de volgende map en verzend vervolgens het gecomprimeerde bestand naar Azure-ondersteuning:

   ``/var/opt/microsoft/omsagent/run/automationworker/``
 
3. Controleer of de werk ruimte-ID waaraan de OMS Linux-agent wordt gerapporteerd, overeenkomt met de werk ruimte die wordt gecontroleerd op updates.

### <a name="for-issues-that-affect-update-management-on-windows"></a>Voor problemen die van invloed zijn op Updatebeheer in Windows

Naast de items die in [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)worden vermeld, gaat u als volgt te werk:

1. Exporteer de volgende gebeurtenis logboeken naar de EVTX-indeling:

   * Systeem
   * Toepassing
   * Beveiliging
   * Operations Manager
   * Micro soft-SMA/operationeel

2. Controleer of de werk ruimte-ID waarmee de agent wordt gerapporteerd, overeenkomt met de werk ruimte die wordt bewaakt door Windows-updates.

### <a name="for-issues-that-affect-a-job"></a>Voor problemen die van invloed zijn op een taak

Naast de items die worden vermeld in [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), verzamelt u de volgende gegevens:

1. Verzamel het **JobID** -nummer (voor de taak die een probleem ondervindt):

   1. Ga op het Azure Portal naar de Blade **Automation-accounts** .
   2. Selecteer het **Automation-account** dat u wilt oplossen.
   3. Selecteer **taken**.
   4. Selecteer de taak die u wilt oplossen.
   5. Zoek onder **taak overzicht**naar een **taak-id** (GUID).

   ![Taak-ID in deel venster taak overzicht](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

2. De account naam verzamelen:

   1. Ga op het Azure Portal naar de Blade **Automation-accounts** .
   2. Haal de naam op van het **Automation-account** dat u wilt oplossen.

3. Verzamel voor beeld van het script dat u uitvoert.

4. De logboek bestanden verzamelen:

   1. Ga op het Azure Portal naar de Blade **Automation-accounts** .
   2. Selecteer het **Automation-account** dat u wilt oplossen.
   3. Selecteer **taken**.
   4. Selecteer de taak die u wilt oplossen.
   5. Selecteer **alle logboeken**.
   6. Verzamel de gegevens op de Blade die wordt gegenereerd.

   ![Gegevens die worden vermeld in alle logboeken](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

### <a name="for-issues-that-affect-modules"></a>Voor problemen die van invloed zijn op modules

Naast de items onder basis gegevens verzamelen, moet u de volgende informatie verzamelen:

* De stappen die u hebt gevolgd om het probleem te reproduceren.
* Een scherm opname van eventuele fout berichten.
* Een scherm afbeelding van de huidige modules en de bijbehorende versie nummers.

## <a name="next-steps"></a>Volgende stappen

Als u op elk gewenst moment meer hulp nodig hebt, neemt u contact op met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/).

U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.
