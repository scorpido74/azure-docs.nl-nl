---
title: Azure-modules bijwerken in Azure Automation
description: In dit artikel wordt beschreven hoe u nu algemene Azure PowerShell-modules kunt bijwerken die standaard worden meegeleverd in Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 06/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 23475fb77210eeea0568bb996529c81458db9c6c
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382764"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Azure PowerShell-modules bijwerken in Azure Automation

Als u de Azure-modules in uw Automation-account wilt bijwerken, moet u het [Runbook Azure modules bijwerken](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)gebruiken. Dit is beschikbaar als open source. Als u het **Update-AutomationAzureModulesForAccount-** runbook wilt gebruiken om uw Azure-modules bij te werken, kunt u het downloaden van de [Azure modules runbook-opslag plaats](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) in github bijwerken. U kunt deze vervolgens importeren in uw Automation-account of als een script uitvoeren. Zie [een Runbook importeren](manage-runbooks.md#import-a-runbook)voor meer informatie over het importeren van een runbook in uw Automation-account.

De meest voorkomende AzureRM Power shell-modules zijn standaard opgenomen in elk Automation-account. Het Azure-team werkt de Azure-modules regel matig bij, daarom kunt u het beste de [Update AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) runbook gebruiken om de modules in uw Automation-accounts bij te werken.

Omdat modules regel matig worden bijgewerkt door de product groep, kunnen er wijzigingen optreden met de meegeleverde cmdlets. Deze actie kan een negatieve invloed hebben op uw runbooks, afhankelijk van het type wijziging, zoals het wijzigen van de naam van een para meter of het volledig afstoten van een cmdlet.

Om te voor komen dat uw runbooks en de processen worden geautomatiseerd, getest en gevalideerd voordat u doorgaat. Als u geen speciaal Automation-account hebt dat is bedoeld voor dit doel, kunt u overwegen om een groot aantal verschillende scenario's te testen tijdens de ontwikkeling van uw runbooks. Deze test moet terugkerende wijzigingen bevatten, zoals het bijwerken van de Power shell-modules.

Als u uw scripts lokaal ontwikkelt, is het raadzaam om dezelfde module versies lokaal te hebben als in uw Automation-account bij het testen om te controleren of u dezelfde resultaten krijgt. Nadat de resultaten zijn gevalideerd en u alle vereiste wijzigingen hebt toegepast, kunt u de wijzigingen naar de productie verplaatsen.

> [!NOTE]
> Een nieuw Automation-account bevat mogelijk niet de meest recente modules.

> [!NOTE]
> Het is niet mogelijk om algemene modules te verwijderen. de modules die door Automation worden geboden, zijn niet meer beschikbaar.

## <a name="considerations"></a>Overwegingen

Hier volgen enkele aandachtspunten waarmee u rekening moet houden wanneer u dit proces gebruikt om uw Azure-modules bij te werken:

* Dit runbook ondersteunt standaard het bijwerken van de **Azure** -en **AzureRm** -modules. Dit runbook ondersteunt ook het bijwerken van de **AZ** -modules. Raadpleeg het [Leesmij-bestand voor Azure-modules bijwerken](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) voor meer `Az` informatie over het bijwerken van modules met dit runbook. Er zijn extra belang rijke factoren die u in aanmerking moet nemen wanneer u de `Az` modules in uw Automation-account gebruikt. Zie [using AZ modules in uw Automation-account](az-modules.md)voor meer informatie.

* Voordat u met dit runbook begint, moet u ervoor zorgen dat er een [Azure run as-account referentie](manage-runas-account.md) is gemaakt voor uw Automation-account.

* U kunt deze code als een normaal Power shell-script gebruiken in plaats van een runbook: Meld u eerst aan bij Azure met de opdracht [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) en voer vervolgens door `-Login $false` met het script.

* Als u dit runbook wilt gebruiken voor de soevereine Clouds, gebruikt u de `AzureRmEnvironment` para meter om de juiste omgeving door te geven aan het runbook.  Acceptabele waarden zijn **Cloud**, **AzureChinaCloud**, **AzureGermanCloud**en **AzureUSGovernment**. Deze waarden kunnen worden opgehaald uit het gebruik `Get-AzureRmEnvironment | select Name`. Als u geen waarde door geven aan deze para meter, wordt het runbook standaard ingesteld op de open bare Azure-Cloud **Cloud**

* Als u een specifieke Azure PowerShell module versie wilt gebruiken in plaats van de meest recente beschik bare PowerShell Gallery, geeft u deze versies door `ModuleVersionOverrides` aan de optionele para meter van het **Update-AutomationAzureModulesForAccount** runbook. Zie het [runbook update-AutomationAzureModulesForAccount. ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) voor voor beelden. Azure PowerShell-modules die niet in de `ModuleVersionOverrides` para meter worden vermeld, worden bijgewerkt met de nieuwste module versies op de PowerShell Gallery. Als u niets doorgeeft aan `ModuleVersionOverrides` de para meter, worden alle modules bijgewerkt met de nieuwste module versies op het PowerShell Gallery. Dit gedrag is hetzelfde als de knop **Azure-modules bijwerken** .

## <a name="next-steps"></a>Volgende stappen

Ga naar het runbook open source [Update Azure modules](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) voor meer informatie hierover.
