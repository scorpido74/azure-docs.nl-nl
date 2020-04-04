---
title: Azure-modules bijwerken in Azure Automation
description: In dit artikel wordt beschreven hoe u nu de gangbare Azure PowerShell-modules bijwerken die standaard worden geleverd in Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: 721bb3579fec10df88be471d67f68c0846aa9432
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632113"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>How to update Azure PowerShell modules in Azure Automation (Azure PowerShell-modules bijwerken in Azure Automation)

Als u de Azure-modules in uw automatiseringsaccount wilt bijwerken, moet u de [runbook Azure-modules](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)bijwerken gebruiken, die beschikbaar is als open source. Als u de runbook **Update-AutomationAzureModulesForAccount** wilt gebruiken om uw Azure-modules bij te werken, downloadt u deze uit de [runbook-opslagplaats](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) bijwerken van Azure-modules op GitHub. U het vervolgens importeren in uw automatiseringsaccount of het als script uitvoeren. Zie [Een runbook importeren](manage-runbooks.md#importing-a-runbook)voor meer informatie over het importeren van een runbook in uw automatiseringsaccount.

De meest voorkomende AzureRM PowerShell-modules worden standaard geleverd in elk Automatiseringsaccount. Het Azure-team werkt de Azure-modules regelmatig bij, dus om up-to-date te blijven, wilt u het runbook [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) gebruiken om de modules in uw Automatiseringsaccounts bij te werken.

Omdat modules regelmatig worden bijgewerkt door de productgroep, kunnen er wijzigingen optreden met de meegeleverde cmdlets. Deze actie kan een negatieve invloed hebben op uw runbooks, afhankelijk van het type wijziging, zoals het hernoemen van een parameter of het volledig afkeuren van een cmdlet.

Om te voorkomen dat uw runbooks en de processen worden beïnvloed, worden ze getest, getest en gevalideerd voordat ze verder gaan. Als u geen speciaal Automatiseringsaccount hebt dat voor dit doel is bedoeld, u overwegen er een te maken, zodat u veel verschillende scenario's testen tijdens de ontwikkeling van uw runbooks. Deze tests moeten iteratieve wijzigingen omvatten, zoals het bijwerken van de PowerShell-modules.

Als u uw scripts lokaal ontwikkelt, wordt aanbevolen om dezelfde moduleversies lokaal te hebben die u in uw automatiseringsaccount hebt wanneer u test om ervoor te zorgen dat u dezelfde resultaten ontvangt. Nadat de resultaten zijn gevalideerd en u de vereiste wijzigingen hebt toegepast, u de wijzigingen in de productie verplaatsen.

> [!NOTE]
> Een nieuw Automation-account bevat mogelijk niet de nieuwste modules.

> [!NOTE]
> U globale modules niet verwijderen - modules die Automation out of the box biedt.

## <a name="considerations"></a>Overwegingen

Hieronder volgen enkele overwegingen om rekening mee te houden wanneer u dit proces gebruikt om uw Azure-modules bij te werken:

* Deze runbook ondersteunt standaard het bijwerken van de **Azure-** en **AzureRm-modules.** Dit runbook ondersteunt het updaten van de **Az** modules ook. Bekijk de [runbook README voor Update Azure-modules](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) voor meer informatie over het bijwerken van `Az` modules met dit runbook. Er zijn nog andere belangrijke factoren waarmee u `Az` rekening moet houden bij het gebruik van de modules in uw automatiseringsaccount, zie [Az-modules gebruiken in uw automatiseringsaccount.](az-modules.md)

* Voordat u deze runbook start, controleert u of uw Automatiseringsaccount een [Azure Run As-accountreferentie](manage-runas-account.md) heeft gemaakt.

* U deze code gebruiken als een gewoon PowerShell-script in plaats van een runbook: meld `-Login $false` u eerst aan bij Azure met de opdracht [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) en vervolgens naar het script.

* Als u dit runbook op de `AzureRmEnvironment` soevereine wolken wilt gebruiken, gebruikt u de parameter om de juiste omgeving door te geven aan het runbook.  Acceptabele waarden zijn **AzureCloud,** **AzureChinaCloud,** **AzureGermanCloud**en **AzureUSGovernment**. Deze waarden kunnen worden `Get-AzureRmEnvironment | select Name`opgehaald uit het gebruik van . Als u geen waarde aan deze parameter doorgeeft, wordt de runbook standaard uitgevoerd op de Azure Public Cloud **AzureCloud**

* Als u een specifieke Azure PowerShell-moduleversie wilt gebruiken in plaats van de meest `ModuleVersionOverrides` recente beschikbare in de PowerShell-galerie, geeft u deze versies door aan de optionele parameter van het runbook **Update-AutomationAzureModulesForAccount.** Zie voor voorbeelden het runbook [Update-AutomationAzureModulesForAccount.ps1.](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) Azure PowerShell-modules die niet `ModuleVersionOverrides` in de parameter worden vermeld, worden bijgewerkt met de nieuwste moduleversies in de PowerShell-galerie. Als u niets `ModuleVersionOverrides` doorgeeft aan de parameter, worden alle modules bijgewerkt met de nieuwste moduleversies in de PowerShell Gallery. Dit gedrag is hetzelfde als de knop **Azure Modules bijwerken.**

## <a name="next-steps"></a>Volgende stappen

Ga naar de runbook voor open [source-update-modules](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) voor meer informatie.
