---
title: DSC-configuraties samenstellen in Azure Automation State Configuration met behulp van samengestelde resources
description: Meer informatie over het samenstellen van configuraties met samengestelde resources in Azure Automation State Configuration.
keywords: powershell dsc, gewenste statusconfiguratie, powershell dsc azure, samengestelde resources
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 7e7625ae95d5355ae1122a16ea4828eed5f78c73
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682932"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-using-composite-resources"></a>DSC-configuraties samenstellen in Azure Automation State Configuration met behulp van samengestelde resources

Wanneer u resources met meer dan één gewenste statusconfiguratie (DSC) moet beheren, u het beste [samengestelde resources](/powershell/scripting/dsc/resources/authoringresourcecomposite)gebruiken. Een samengestelde resource is een geneste en geparameteriseerde configuratie die wordt gebruikt als Een DSC-bron in een andere configuratie. Met het gebruik van samengestelde resources u complexe configuraties maken, terwijl de onderliggende samengestelde resources individueel kunnen worden beheerd en gebouwd.

Azure Automation maakt het [importeren en samenstellen van samengestelde resources](automation-dsc-compile.md)mogelijk. Zodra u samengestelde resources hebt geïmporteerd in uw Automatiseringsaccount, u Azure Automation State Configuration gebruiken via de **Statusconfiguratie (DSC-functie** in de Azure-portal.

## <a name="composing-a-configuration-from-composite-resources"></a>Een configuratie samenstellen uit samengestelde resources

Voordat u een configuratie toewijzen die is gemaakt van samengestelde resources in de Azure-portal, moet u de configuratie opstellen. Compositie gebruikt **Compositie configuratie op** de pagina Configuratie van de status (DSC) terwijl u op het tabblad **Configuraties** of **gecompileerde configuraties** staat.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Klik aan de linkerkant op **Alle bronnen** en vervolgens op de naam van uw automatiseringsaccount.
1. Selecteer op de pagina Automatiseringsaccount de optie **Statusconfiguratie (DSC)** onder **Configuratiebeheer**.
1. Klik op de pagina Statusconfiguratie (DSC) op het tabblad **Configuraties** of **Gecompileerde configuraties** en klik vervolgens op **Configuratie opstellen** in het menu boven aan de pagina.
1. Geef in de stap **Basisbeginselen** de nieuwe configuratienaam op (vereist) en klik ergens in de rij van elke samengestelde resource die u in uw nieuwe configuratie wilt opnemen, klik vervolgens op **Volgende** of klik op de stap **Broncode.** Voor de volgende stappen `PSExecutionPolicy` `RenameAndDomainJoin` hebben we resources geselecteerd en samengesteld.
   ![Schermafbeelding van de basisstap van de configuratiepagina samenstellen](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. In de stap **Broncode** ziet u hoe de samengestelde samenstelling van de geselecteerde samengestelde resources eruit ziet. U het samenvoegen van alle parameters en de manier waarop deze worden doorgegeven aan de samengestelde resource. Wanneer u klaar bent met het controleren van de nieuwe broncode, klikt u op **Volgende** of klikt u op de stap **Parameters.**
   ![Schermafbeelding van de broncodestap van de configuratiepagina samenstellen](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. In de stap **Parameters** wordt de parameter voor elke samengestelde resource weergegeven, zodat waarden kunnen worden opgegeven. Als een parameter een beschrijving heeft, wordt deze weergegeven naast het parameterveld. Als een parameter `PSCredential` van type is, bevat de vervolgkeuzelijst een lijst met **referentieobjecten** in het huidige automatiseringsaccount. A **+ Een referentieoptie toevoegen** is ook beschikbaar. Zodra alle vereiste parameters zijn opgegeven, klikt u op **Opslaan en compileren**.
   ![Schermafbeelding van de parametersstap van de configuratiepagina samenstellen](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

Zodra de nieuwe configuratie is opgeslagen, wordt deze ingediend voor compilatie. Status van de compilatietaak kan worden weergegeven zoals elke geïmporteerde configuratie. Zie [Een compilatietaak weergeven](automation-dsc-getting-started.md#viewing-a-compilation-job)voor meer informatie .

Wanneer de compilatie is voltooid, wordt de nieuwe configuratie weergegeven op het tabblad **Gecompileerde configuraties.** Vervolgens u de configuratie toewijzen aan een beheerd knooppunt, met behulp van de stappen in [Het toewijzen van een knooppunt aan een andere knooppuntconfiguratie.](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration)

## <a name="next-steps"></a>Volgende stappen

- Zie [Aan de slag met Azure Automation State Configuration](automation-dsc-getting-started.md)om aan de slag te gaan.
- Zie [Onboarding-machines voor beheer door Azure Automation State Configuration voor](automation-dsc-onboarding.md)meer informatie over het inschepen van knooppunten.
- Zie [Configuraties compileren in Azure Automation State Configuration](automation-dsc-compile.md)voor meer informatie over het samenstellen van DSC-configuraties, zodat u ze toewijzen aan doelknooppunten.
- Zie Cmdlets azure [automation state configuration](/powershell/module/azurerm.automation/#automation)voor PowerShell-cmdlet .
- Zie Azure Automation State Configuration Pricing voor prijsinformatie voor [prijsinformatie.](https://azure.microsoft.com/pricing/details/automation/)
- Zie [Continue implementatie met Azure Automation State Configuration en Chocolatey](automation-dsc-cd-chocolatey.md)voor een voorbeeld van het gebruik van Azure Automation State Configuration in a continuous deployment pipeline.
