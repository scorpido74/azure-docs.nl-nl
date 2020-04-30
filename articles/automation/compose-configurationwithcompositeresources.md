---
title: DSC-configuraties in de Azure Automation status configuratie samen stellen met samengestelde resources
description: Meer informatie over het opstellen van configuraties met behulp van samengestelde resources in Azure Automation status configuratie.
keywords: Power shell DSC, desired state Configuration, Power shell DSC Azure, samengestelde resources
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 7e7625ae95d5355ae1122a16ea4828eed5f78c73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81682932"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-using-composite-resources"></a>DSC-configuraties in de Azure Automation status configuratie samen stellen met samengestelde resources

Wanneer u resources moet beheren met meer dan één desired state Configuration (DSC), is het beste om [samengestelde resources](/powershell/scripting/dsc/resources/authoringresourcecomposite)te gebruiken. Een samengestelde resource is een geneste en geparametriseerde configuratie die wordt gebruikt als een DSC-resource in een andere configuratie. Met samengestelde resources kunt u complexe configuraties maken, terwijl de onderliggende samengestelde resources afzonderlijk kunnen worden beheerd en gebouwd.

Azure Automation maakt het mogelijk om [samengestelde resources te importeren en te compileren](automation-dsc-compile.md). Zodra u samengestelde resources hebt geïmporteerd in uw Automation-account, kunt u Azure Automation status configuratie gebruiken via de **status configuratie (DSC-** functie in de Azure Portal.

## <a name="composing-a-configuration-from-composite-resources"></a>Een configuratie uit samengestelde resources opstellen

Voordat u een configuratie kunt toewijzen van samengestelde resources in de Azure Portal, moet u de configuratie opstellen. Samen stelling maakt gebruik van de **opstel configuratie** op de pagina State Configuration (DSC) en op het tabblad **configuraties** of de **gecompileerde configuraties** .

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Klik aan de linkerkant op **alle resources** en vervolgens op de naam van uw Automation-account.
1. Selecteer op de pagina Automation-account **status configuratie (DSC)** onder **configuratie beheer**.
1. Klik op de pagina status configuratie (DSC) op het tabblad **configuraties** of **gecompileerde configuraties** en klik vervolgens op **configuratie opstellen** in het menu boven aan de pagina.
1. Geef in de sectie **basis beginselen** de nieuwe configuratie naam (vereist) op en klik op een wille keurige plaats in de rij van elke samengestelde resource die u wilt toevoegen aan de nieuwe configuratie. Klik vervolgens op **volgende** of op de stap **bron code** . Voor de volgende stappen zijn geselecteerde en `PSExecutionPolicy` `RenameAndDomainJoin` samengestelde resources geselecteerd.
   ![Scherm afbeelding van de stap basis beginselen van de pagina opstellen van de configuratie](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. De **bron code** stap laat zien hoe de bestaande configuratie van de geselecteerde samengestelde resources eruit ziet. U kunt het samen voegen van alle para meters bekijken en bepalen hoe ze worden door gegeven aan de samengestelde resource. Wanneer u klaar bent met het controleren van de nieuwe bron code, klikt u op **volgende** of op de stap **para meters** .
   ![Scherm afbeelding van de stap bron code van de pagina opstellen van de configuratie](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. In de stap **para meters** wordt de para meter voor elke samengestelde resource weer gegeven, zodat waarden kunnen worden opgegeven. Als een para meter een beschrijving heeft, wordt deze weer gegeven naast het veld para meter. Als een para meter van `PSCredential` het type is, geeft de vervolg keuzelijst een lijst met **referentie** objecten in het huidige Automation-account. Er is ook een optie voor het **toevoegen van referenties** beschikbaar. Als alle vereiste para meters zijn opgegeven, klikt u op **opslaan en compileren**.
   ![Scherm afbeelding van de stap para meters van de pagina opstellen van de configuratie](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

Zodra de nieuwe configuratie is opgeslagen, wordt deze voor compilatie verzonden. De status van de compilatie taak kan worden weer gegeven zoals bij elke geïmporteerde configuratie. Zie [een compilatie taak weer geven](automation-dsc-getting-started.md#viewing-a-compilation-job)voor meer informatie.

Wanneer de compilatie is voltooid, wordt de nieuwe configuratie weer gegeven op het tabblad **gecompileerde configuraties** . Vervolgens kunt u de configuratie toewijzen aan een beheerd knoop punt met behulp van de stappen in [een knoop punt opnieuw toewijzen aan een andere knooppunt configuratie](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Volgende stappen

- Zie aan de slag [met de configuratie van de Azure Automation-status](automation-dsc-getting-started.md)om aan de slag te gaan.
- Zie voor meer informatie over het voorbereiden van knoop punten op [onboarding machines voor beheer door Azure Automation status configuratie](automation-dsc-onboarding.md).
- Zie [configuraties compileren in azure Automation status configuratie](automation-dsc-compile.md)voor meer informatie over het compileren van DSC-configuraties zodat u ze aan doel knooppunten kunt toewijzen.
- Zie [Azure Automation status configuratie-cmdlets](/powershell/module/azurerm.automation/#automation)voor informatie over de Power shell-cmdlet.
- Zie [prijzen voor Azure Automation status configuratie](https://azure.microsoft.com/pricing/details/automation/)voor prijs informatie.
- Voor een voor beeld van het gebruik van Azure Automation status configuratie in een pijp lijn voor continue implementatie raadpleegt u [continue implementatie met behulp van Azure Automation-status configuratie en Choco lade](automation-dsc-cd-chocolatey.md).
