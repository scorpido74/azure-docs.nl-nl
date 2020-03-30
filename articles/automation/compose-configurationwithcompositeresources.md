---
title: DSC-configuraties samenstellen in Azure Automation State Configuration (DSC) met composietresources
description: Meer informatie over het samenstellen van configuraties met samengestelde resources in Azure Automation State Configuration (DSC)
keywords: powershell dsc, gewenste statusconfiguratie, powershell dsc azure, samengestelde resources
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: e5083ec55ee0a57cd7defd466f5baf1704336320
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77370662"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-dsc-using-composite-resources"></a>DSC-configuraties samenstellen in Azure Automation State Configuration (DSC) met composietresources

Wanneer een resource moet worden beheerd met meer dan één gewenste configuratie (DSC), u het beste [samengestelde resources](/powershell/scripting/dsc/resources/authoringresourcecomposite)gebruiken. Een samengestelde resource is een geneste en geparameteriseerde configuratie die wordt gebruikt als Een DSC-bron in een andere configuratie. Hierdoor kunnen complexe configuraties worden gemaakt, terwijl de onderliggende samengestelde resources (geparameteriseerde configuraties) individueel kunnen worden beheerd en gebouwd.

Azure Automation maakt het [importeren en samenstellen van samengestelde resources](automation-dsc-compile.md)mogelijk.
Zodra samengestelde resources zijn geïmporteerd in uw Automatiseringsaccount, u de **configuratie-ervaring samenstellen** gebruiken op de pagina **DSC (State Configuration).**

## <a name="composing-a-configuration-from-composite-resources"></a>Een configuratie samenstellen uit samengestelde resources

Voordat u een configuratie toewijzen die is gemaakt van samengestelde resources in de Azure-portal, moet u deze configureren. Dit kan met **de configuratie Samenstellen** op de pagina Configuratie van de staat **(DSC)** op de tabbladen **Configuraties** of **Gecompileerde configuraties.**

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Klik aan de linkerkant op **Alle bronnen** en vervolgens op de naam van uw automatiseringsaccount.
1. Selecteer op de pagina **Automatiseringsaccount** de optie **Statusconfiguratie (DSC)** onder **Configuratiebeheer**.
1. Klik op de pagina **Statusconfiguratie (DSC)** op het tabblad **Configuraties** of **Gecompileerde configuraties** en klik vervolgens op **Configuratie opstellen** in het menu boven aan de pagina.
1. Geef in de stap **Basisbeginselen** de nieuwe configuratienaam op (vereist) en klik ergens in de rij van elke samengestelde resource die u in uw nieuwe configuratie wilt opnemen, klik vervolgens op **Volgende** of klik op de stap **Broncode.** Voor de volgende stappen hebben we **PSExecutionPolicy** en **RenameAndDomainJoin-samengestelde** resources geselecteerd.
   ![Schermafbeelding van de basisstap van de configuratiepagina samenstellen](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. In de stap **Broncode** ziet u hoe de samengestelde samenstelling van de geselecteerde samengestelde resources eruit ziet. U het samenvoegen van alle parameters en de manier waarop deze worden doorgegeven aan de samengestelde resource. Wanneer u klaar bent met het controleren van de nieuwe broncode, klikt u op **Volgende** of klikt u op de stap **Parameters.**
   ![Schermafbeelding van de broncodestap van de configuratiepagina samenstellen](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. In de stap **Parameters** wordt de parameter die elke samengestelde resource heeft, weergegeven, zodat deze kunnen worden verstrekt. Als een parameter een beschrijving heeft, wordt deze weergegeven naast het parameterveld. Als een veld een **parameter van het PSCredential-type** is, biedt de vervolgkeuzelijst voor het configureren van een lijst met **referentieobjecten** in het huidige automatiseringsaccount. A **+ Een referentieoptie toevoegen** is ook beschikbaar. Zodra alle vereiste parameters zijn opgegeven, klikt u op **Opslaan en compileren**.
   ![Schermafbeelding van de parametersstap van de configuratiepagina samenstellen](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

Zodra de nieuwe configuratie is opgeslagen, wordt deze ingediend voor compilatie. Status van de compilatietaak kan worden weergegeven zoals elke geïmporteerde configuratie. Zie [Een compilatietaak weergeven](automation-dsc-getting-started.md#viewing-a-compilation-job)voor meer informatie .

Wanneer de compilatie is voltooid, wordt de nieuwe configuratie weergegeven op het tabblad **Gecompileerde configuraties.** Zodra het zichtbaar is op dit tabblad, kan het worden toegewezen aan een beheerd knooppunt met behulp van de stappen in [Het toewijzen van een knooppunt aan een andere knooppuntconfiguratie](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Volgende stappen

- Zie [Aan de slag met Azure Automation State Configuration](automation-dsc-getting-started.md) om aan de slag te gaan.
- Zie [Onboarding-machines voor beheer door Azure Automation State Configuration](automation-dsc-onboarding.md) voor meer informatie over het inschepen van knooppunten.
- Zie [Configuraties compileren in Azure Automation State Configuration](automation-dsc-compile.md) voor meer informatie over het samenstellen van DSC-configuraties, zodat u ze toewijzen aan doelknooppunten.
- Zie [Cmdlets Azure Automation State Configuration cmdlets](/powershell/module/azurerm.automation/#automation) voor PowerShell-cmdlet
- Zie Azure [Automation State Configuration Pricing](https://azure.microsoft.com/pricing/details/automation/) voor prijsinformatie
- Zie [Continue implementatie met Azure Automation State Configuration en Chocolatey](automation-dsc-cd-chocolatey.md) voor een voorbeeld van het gebruik van Azure Automation State Configuration in a continuous deployment pipeline.
