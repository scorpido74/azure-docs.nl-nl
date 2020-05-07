---
title: Azure PowerShell-modules in Azure Automation bijwerken
description: In dit artikel wordt beschreven hoe u nu algemene Azure PowerShell-modules kunt bijwerken die standaard worden meegeleverd in Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: 3f2ceda6e87ce16e910f3d215c1fabe81d522f32
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855558"
---
# <a name="update-azure-powershell-modules-in-azure-automation"></a>Azure PowerShell-modules in Azure Automation bijwerken

Als u de Azure-modules in uw Automation-account wilt bijwerken, moet u het [Runbook Azure modules bijwerken](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)gebruiken. Dit is beschikbaar als open source. Als u het **Update-AutomationAzureModulesForAccount-** runbook wilt gebruiken om uw Azure-modules bij te werken, kunt u het downloaden van de [Azure modules runbook-opslag plaats](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) in github bijwerken. U kunt deze vervolgens importeren in uw Automation-account of als een script uitvoeren. Zie [een Runbook importeren](manage-runbooks.md#import-a-runbook)voor meer informatie over het importeren van een runbook in uw Automation-account.

De meest voorkomende Power shell-modules zijn standaard opgenomen in elk Automation-account. Het Azure-team werkt de Azure-modules regel matig bij. Daarom moet u het runbook [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) gebruiken om de modules in uw Automation-accounts up-to-date te houden.

Omdat modules regel matig worden bijgewerkt door de product groep, kunnen er wijzigingen optreden met de meegeleverde cmdlets. Deze wijzigingen, zoals het wijzigen van de naam van een para meter of het volledig terugvallen van een cmdlet, kunnen een negatieve invloed hebben op uw runbooks.

Om te voor komen dat uw runbooks en de processen worden geautomatiseerd, getest en gevalideerd voordat u doorgaat. Als u geen speciaal Automation-account hebt dat is bedoeld voor dit doel, kunt u overwegen om een groot aantal verschillende scenario's te testen tijdens de ontwikkeling van uw runbooks. Deze test moet terugkerende wijzigingen bevatten, zoals het bijwerken van de Power shell-modules.

Als u uw scripts lokaal ontwikkelt, is het raadzaam om dezelfde module versies lokaal te hebben als in uw Automation-account bij het testen om te controleren of u dezelfde resultaten krijgt. Nadat de resultaten zijn gevalideerd en u alle vereiste wijzigingen hebt toegepast, kunt u de wijzigingen naar de productie verplaatsen.

> [!NOTE]
> Een nieuw Automation-account bevat mogelijk niet de meest recente modules.

> [!NOTE]
> Het is niet mogelijk om globale modules te verwijderen. Dit zijn modules die automatisch door Automation worden verstrekt.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor de installatie-instructies voor AZ module op uw Hybrid Runbook Worker. 

## <a name="considerations"></a>Overwegingen

Hier volgen enkele overwegingen waarmee u rekening moet houden wanneer u dit artikel gebruikt om uw Azure-modules bij te werken:

* Het runbook dat in dit artikel wordt beschreven, ondersteunt standaard het bijwerken van de Azure-, AzureRM-en AZ-modules. Raadpleeg het [Leesmij-bestand voor Azure-modules bijwerken](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) voor meer informatie over het bijwerken van AZ. Automation-modules met dit runbook. Er zijn extra belang rijke factoren die u moet uitvoeren wanneer u de AZ-modules in uw Automation-account gebruikt. Zie [modules beheren in azure Automation](shared-resources/modules.md)voor meer informatie.

* Voordat u met dit runbook begint, moet u ervoor zorgen dat er een [Azure run as-account referentie](manage-runas-account.md) is gemaakt voor uw Automation-account.

* U kunt deze code als een normaal Power shell-script gebruiken in plaats van een runbook: Meld u eerst aan bij Azure met de cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) en geef het script door `-Login $false` .

* Als u dit runbook wilt gebruiken voor de soevereine Clouds, gebruikt u de `AzEnvironment` para meter om de juiste omgeving door te geven aan het runbook.  Acceptabele waarden zijn Cloud (open bare Azure-Cloud), AzureChinaCloud, AzureGermanCloud en AzureUSGovernment. Deze waarden kunnen worden opgehaald met `Get-AzEnvironment | select Name`. Als u geen waarde aan deze cmdlet doorgeeft, wordt het runbook standaard ingesteld op Cloud.

* Als u een specifieke Azure PowerShell module versie wilt gebruiken in plaats van de meest recente module die beschikbaar is op de PowerShell Gallery, geeft u deze `ModuleVersionOverrides` versies door aan de optionele para meter van het runbook **Update-AutomationAzureModulesForAccount** . Zie het runbook [Update-AutomationAzureModulesForAccount. ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) voor voor beelden. Azure PowerShell-modules die niet in de `ModuleVersionOverrides` para meter worden vermeld, worden bijgewerkt met de nieuwste module versies op de PowerShell Gallery. Als u niets doorgeeft aan `ModuleVersionOverrides` de para meter, worden alle modules bijgewerkt met de nieuwste module versies op het PowerShell Gallery. Dit gedrag is hetzelfde als de knop **Azure-modules bijwerken** .

## <a name="next-steps"></a>Volgende stappen

Ga naar het runbook open source [Update Azure modules](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) voor meer informatie hierover.
