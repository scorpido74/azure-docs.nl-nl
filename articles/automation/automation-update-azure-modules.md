---
title: Azure-modules bijwerken in Azure Automation
description: In dit artikel wordt beschreven hoe u nu de gangbare Azure PowerShell-modules bijwerken die standaard worden geleverd in Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: ba926ccbb069c8620259514e0a64c56957529a0f
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617476"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>How to update Azure PowerShell modules in Azure Automation (Azure PowerShell-modules bijwerken in Azure Automation)

Als u de Azure-modules in uw Automatiseringsaccount wilt bijwerken, moet u de [runbook Azure-modules](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)bijwerken , die beschikbaar is als open source. Als u de runbook **Update-AutomationAzureModulesForAccount** wilt gebruiken om uw Azure-modules bij te werken, downloadt u deze uit de [runbook-opslagplaats](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) bijwerken van Azure-modules op GitHub. U het vervolgens importeren in uw Automatiseringsaccount of het als script uitvoeren. Zie [Een runbook importeren](manage-runbooks.md#importing-a-runbook)voor meer informatie over het importeren van een runbook in uw automatiseringsaccount.

De meest voorkomende PowerShell-modules worden standaard geleverd in elk Automation-account. Het Azure-team werkt de Azure-modules regelmatig bij. Daarom moet u het runbook [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) gebruiken om de modules in uw Automatiseringsaccounts up-to-date te houden.

Omdat modules regelmatig worden bijgewerkt door de productgroep, kunnen er wijzigingen optreden met de meegeleverde cmdlets. Deze wijzigingen kunnen bijvoorbeeld een wijziging van een parameter of het volledig afkeuren van een cmdlet een negatieve invloed hebben op uw runbooks.

Om te voorkomen dat uw runbooks en de processen worden beïnvloed, worden ze getest, getest en gevalideerd voordat ze verder gaan. Als u geen speciaal Automatiseringsaccount hebt dat voor dit doel is bedoeld, u overwegen er een te maken, zodat u veel verschillende scenario's testen tijdens de ontwikkeling van uw runbooks. Deze tests moeten iteratieve wijzigingen omvatten, zoals het bijwerken van de PowerShell-modules.

Als u uw scripts lokaal ontwikkelt, wordt aanbevolen om dezelfde moduleversies lokaal te hebben die u in uw Automatiseringsaccount hebt wanneer u test om ervoor te zorgen dat u dezelfde resultaten ontvangt. Nadat de resultaten zijn gevalideerd en u de vereiste wijzigingen hebt toegepast, u de wijzigingen in de productie verplaatsen.

> [!NOTE]
> Een nieuw Automation-account bevat mogelijk niet de nieuwste modules.

> [!NOTE]
> U geen globale modules verwijderen, die modules zijn die Automation out of the box biedt.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. 

## <a name="considerations"></a>Overwegingen

Hieronder volgen enkele overwegingen om rekening mee te houden wanneer u dit artikel gebruikt om uw Azure-modules bij te werken:

* De runbook die in dit artikel wordt beschreven, ondersteunt standaard het bijwerken van de Azure-, AzureRM- en Az-modules. Bekijk de [runbook README voor Update Azure-modules](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) voor meer informatie over het bijwerken van Az.Automation-modules met dit runbook. Er zijn nog andere belangrijke factoren waarmee u rekening moet houden bij het gebruik van de Az-modules in uw Automatiseringsaccount. Zie [Az-modules gebruiken in uw Automatiseringsaccount](az-modules.md)voor meer informatie.

* Voordat u deze runbook start, controleert u of uw Automatiseringsaccount een [Azure Run As-accountreferentie](manage-runas-account.md) heeft gemaakt.

* U deze code gebruiken als een gewoon PowerShell-script in plaats van een runbook: meld `-Login $false` u eerst aan bij Azure met de [connect-AzAccount-cmdlet](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) en vervolgens naar het script.

* Als u dit runbook op de `AzEnvironment` soevereine wolken wilt gebruiken, gebruikt u de parameter om de juiste omgeving door te geven aan het runbook.  Acceptabele waarden zijn AzureCloud (Azure public cloud), AzureChinaCloud, AzureGermanCloud en AzureUSGovernment. Deze waarden kunnen worden `Get-AzEnvironment | select Name`opgehaald met behulp van . Als u geen waarde aan deze cmdlet doorgeeft, wordt de runbook standaard uitgevoerd op AzureCloud.

* Als u een specifieke Azure PowerShell-moduleversie wilt gebruiken in plaats van de nieuwste `ModuleVersionOverrides` module die beschikbaar is in de PowerShell-galerie, geeft u deze versies door aan de optionele parameter van het runbook **Update-AutomationAzureModulesForAccount.** Zie voor voorbeelden het runbook [Update-AutomationAzureModulesForAccount.ps1.](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) Azure PowerShell-modules die niet `ModuleVersionOverrides` in de parameter worden vermeld, worden bijgewerkt met de nieuwste moduleversies in de PowerShell-galerie. Als u niets `ModuleVersionOverrides` doorgeeft aan de parameter, worden alle modules bijgewerkt met de nieuwste moduleversies in de PowerShell Gallery. Dit gedrag is hetzelfde als de knop **Azure Modules bijwerken.**

## <a name="next-steps"></a>Volgende stappen

Ga naar de runbook voor open [source-update-modules](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) voor meer informatie.
