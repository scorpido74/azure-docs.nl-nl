---
title: DSC-configuraties in Azure Automation State Configuration (DSC) samen stellen met samengestelde resources
description: Meer informatie over het opstellen van configuraties met behulp van samengestelde resources in Azure Automation State Configuration (DSC)
keywords: Power shell DSC, desired state Configuration, Power shell DSC Azure, samengestelde resources
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 08/21/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d9d16d9d9967af5bf8f9e13406e13d5637325f52
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243357"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-dsc-using-composite-resources"></a>DSC-configuraties in Azure Automation State Configuration (DSC) samen stellen met samengestelde resources

Wanneer een resource moet worden beheerd met meer dan een configuratie met één desired state Configuration (DSC), is het beste pad om [samengestelde resources](/powershell/scripting/dsc/resources/authoringresourcecomposite)te gebruiken. Een samengestelde resource is een geneste en geparametriseerde configuratie die wordt gebruikt als een DSC-resource in een andere configuratie. Hierdoor kan het maken van complexe configuraties worden toegestaan, terwijl de onderliggende samengestelde bronnen (geparametriseerde configuraties) afzonderlijk kunnen worden beheerd en gebouwd.

Azure Automation maakt het mogelijk om [samengestelde resources te importeren en te compileren](automation-dsc-compile.md#compiling-configurations-in-azure-automation-that-contain-composite-resources).
Zodra er samengestelde resources zijn geïmporteerd in uw Automation-account, kunt u de ervaring voor het opstellen van de **configuratie** gebruiken op de pagina **status configuratie (DSC)** .

## <a name="composing-a-configuration-from-composite-resources"></a>Een configuratie uit samengestelde resources opstellen

Voordat u een configuratie kunt toewijzen van samengestelde resources in de Azure Portal, moet u deze samen stellen. U kunt dit doen met behulp van de **opstel configuratie** op de pagina **status configuratie (DSC)** en op de tabbladen **configuraties** of **gecompileerde configuraties** .

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Klik aan de linkerkant op **alle resources** en vervolgens op de naam van uw Automation-account.
1. Selecteer op de pagina **Automation-account** **status configuratie (DSC)** onder **configuratie beheer**.
1. Op de pagina **status configuratie (DSC)** klikt u op het tabblad **configuraties** of **gecompileerde configuraties** en klikt u vervolgens op **configuratie opstellen** in het menu boven aan de pagina.
1. Geef in de sectie **basis beginselen** de nieuwe configuratie naam (vereist) op en klik op een wille keurige plaats in de rij van elke samengestelde resource die u wilt toevoegen aan de nieuwe configuratie. Klik vervolgens op **volgende** of op de stap **bron code** . Voor de volgende stappen hebben we **PSExecutionPolicy** -en **RenameAndDomainJoin** -samengestelde resources geselecteerd.
   ![Screenshot van de stap basis beginselen van de pagina voor het opstellen van de configuratie @ no__t-1
1. De **bron code** stap laat zien hoe de bestaande configuratie van de geselecteerde samengestelde resources eruit ziet. U kunt het samen voegen van alle para meters bekijken en bepalen hoe ze worden door gegeven aan de samengestelde resource. Wanneer u klaar bent met het controleren van de nieuwe bron code, klikt u op **volgende** of op de stap **para meters** .
   ![Screenshot van de stap van de bron code van de pagina voor het opstellen van de configuratie @ no__t-1
1. In de stap **para meters** is de para meter waarmee elke samengestelde resource wordt weer gegeven, zodat deze kan worden opgegeven. Als een para meter een beschrijving heeft, wordt deze weer gegeven naast het veld para meter. Als een veld een **PSCredential** -type parameter is, geeft de vervolg keuzelijst voor configureren een lijst met **referentie** objecten in het huidige Automation-account. Er is ook een optie voor het **toevoegen van referenties** beschikbaar. Als alle vereiste para meters zijn opgegeven, klikt u op **opslaan en compileren**.
   ![Screenshot van de stap para meters van de pagina opstellen configuratie @ no__t-1

Zodra de nieuwe configuratie is opgeslagen, wordt deze voor compilatie verzonden. De status van de compilatie taak kan worden weer gegeven zoals bij elke geïmporteerde configuratie. Zie [een compilatie taak weer geven](automation-dsc-getting-started.md#viewing-a-compilation-job)voor meer informatie.

Wanneer de compilatie is voltooid, wordt de nieuwe configuratie weer gegeven op het tabblad **gecompileerde configuraties** . Zodra deze op dit tabblad wordt weer gegeven, kan deze worden toegewezen aan een beheerd knoop punt met behulp van de stappen in [een knoop punt opnieuw toewijzen aan een andere knooppunt configuratie](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Volgende stappen

- Zie aan de slag [met de configuratie van de Azure Automation-status](automation-dsc-getting-started.md) om aan de slag te gaan.
- Voor meer informatie over het voorbereiden van knoop punten, Zie [onboarding machines voor beheer door Azure Automation status configuratie](automation-dsc-onboarding.md)
- Zie [configuraties compileren in azure Automation status configuratie](automation-dsc-compile.md) voor meer informatie over het compileren van DSC-configuraties zodat u ze aan doel knooppunten kunt toewijzen.
- Zie [Azure Automation status configuratie-cmdlets](/powershell/module/azurerm.automation/#automation) voor informatie over de Power shell-cmdlet.
- Zie [prijzen voor Azure Automation status configuratie](https://azure.microsoft.com/pricing/details/automation/) voor prijs informatie.
- Voor een voor beeld van het gebruik van Azure Automation status configuratie in een pijp lijn voor continue implementatie gaat u naar [continue implementatie met behulp van Azure Automation-status configuratie en chocolade](automation-dsc-cd-chocolatey.md)
