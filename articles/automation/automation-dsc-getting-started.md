---
title: Aan de slag met Azure Automation State Configuration
description: Uitleg en voorbeelden van de meest voorkomende taken in Azure Automation State Configuration (DSC)
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2c33fbed6c547897847c79cc1260d2bdc9ec6b17
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010321"
---
# <a name="get-started-with-azure-automation-state-configuration"></a>Aan de slag met Azure Automation State Configuration

In dit artikel wordt uitgelegd hoe u de meest voorkomende taken uitvoeren met Azure Automation State Configuration, zoals het maken, importeren en compileren van configuraties, onboarding-machines voor het beheren en weergeven van rapporten. Zie [Azure Automation State Configuration Overview](automation-dsc-overview.md)voor een overzicht van wat Azure Automation State Configuration is. Zie Windows [PowerShell Desired State Configuration Overview](/powershell/scripting/dsc/overview/overview)voor de documentatie van de gewenste status (Desired State Configuration) voor de gewenste statusconfiguratie .

In dit artikel vindt u een stapsgewijze handleiding voor het gebruik van Azure Automation State Configuration. Als u een voorbeeldomgeving wilt die al is ingesteld zonder de stappen te volgen die in dit artikel zijn beschreven, u de volgende sjabloon Resourcebeheer gebruiken: [sjabloon Beheerd knooppunt azure automation](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration). Met deze sjabloon wordt een voltooide Azure Automation State Configuration-omgeving ingesteld, inclusief een Azure VM die wordt beheerd door Azure Automation State Configuration.

## <a name="prerequisites"></a>Vereisten

Om de voorbeelden in dit artikel te voltooien, zijn de volgende opties vereist:

- Een Azure Automation-account. Zie [Azure Uitvoeren-als-account](automation-sec-configure-azure-runas-account.md) voor instructies over het maken van een Azure Automation Uitvoeren-als-account.
- Een Azure Resource Manager VM (niet Classic) waarop een [ondersteund besturingssysteem wordt uitgevoerd.](automation-dsc-overview.md#operating-system-requirements) Zie [Uw eerste virtuele Windows-machine maken met behulp van Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md) voor instructies voor het maken van een VM

## <a name="creating-a-dsc-configuration"></a>Een DSC-configuratie maken

U maakt een eenvoudige [DSC-configuratie](/powershell/scripting/dsc/configurations/configurations) die zorgt voor de aanwezigheid of afwezigheid van de **Web-Server Windows-functie** (IIS), afhankelijk van hoe u knooppunten toewijst.

1. [VSCode](https://code.visualstudio.com/docs) (of een teksteditor) starten.
1. Typ de volgende tekst:

    ```powershell
    configuration TestConfig
    {
        Node IsWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true
            }
        }

        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'
            }
        }
    }
    ```
1. Sla het bestand op als `TestConfig.ps1`.

Deze configuratie roept één bron aan in elk knooppuntblok, de [WindowsFeature-bron,](/powershell/scripting/dsc/reference/resources/windows/windowsfeatureresource)die de aanwezigheid of afwezigheid van de **webserverfunctie** garandeert.

## <a name="importing-a-configuration-into-azure-automation"></a>Een configuratie importeren in Azure Automation

Vervolgens importeert u de configuratie in het automatiseringsaccount.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Klik aan de linkerkant op **Alle bronnen** en vervolgens op de naam van uw automatiseringsaccount.
1. Selecteer op de pagina **Automatiseringsaccount** de optie **Statusconfiguratie (DSC)** onder **Configuratiebeheer**.
1. Klik op de pagina **Statusconfiguratie (DSC)** op het tabblad **Configuraties** en klik vervolgens op **+ Toevoegen**.
1. Blader op de pagina Configuratie `TestConfig.ps1` **importeren** naar het bestand op uw computer.

   ![Schermafbeelding van het blad **ImportConfiguration**](./media/automation-dsc-getting-started/AddConfig.png)

1. Klik op **OK**.

## <a name="viewing-a-configuration-in-azure-automation"></a>Een configuratie weergeven in Azure Automation

Nadat u een configuratie hebt geïmporteerd, u deze bekijken in de Azure-portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Klik aan de linkerkant op **Alle bronnen** en vervolgens op de naam van uw automatiseringsaccount.
1. Selecteer op de pagina **Automatiseringsaccount** de optie **Statusconfiguratie (DSC)** onder **Configuratiebeheer**.
1. Klik op de pagina **Statusconfiguratie (DSC)** op het tabblad **Configuraties** en klik vervolgens op **TestConfig** (dit is de naam van de configuratie die u in de vorige procedure hebt geïmporteerd).
1. Klik op de pagina **TestConfig Configuration** op **Configuratiebron weergeven**.

   ![Schermafbeelding van het configuratieblad TestConfig](./media/automation-dsc-getting-started/ViewConfigSource.png)

   Er wordt een **bronpagina voor testconfig-configuratie** geopend, waarop de PowerShell-code voor de configuratie wordt weergegeven.

## <a name="compiling-a-configuration-in-azure-automation"></a>Een configuratie samenstellen in Azure Automation

Voordat u een gewenste status op een knooppunt toepassen, moet een DSC-configuratie die die status definieert, worden gecompileerd in een of meer knooppuntconfiguraties (MOF-document) en op de DSC Pull-server voor automatisering worden geplaatst. Zie [Configuraties compileren in Azure Automation State Configuration](automation-dsc-compile.md)voor een meer gedetailleerde beschrijving van het compileren van configuraties in Azure Automation State Configuration.
Zie [DSC-configuraties](/powershell/scripting/dsc/configurations/configurations)voor meer informatie over het samenstellen van configuraties.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Klik aan de linkerkant op **Alle bronnen** en vervolgens op de naam van uw automatiseringsaccount.
1. Klik op de pagina **Automatiseringsaccount** op **Configuratie (DSC)** onder **Configuratiebeheer**.
1. Klik op de pagina **Statusconfiguratie (DSC)** op het tabblad **Configuraties** en klik vervolgens op **TestConfig** (de naam van de eerder geïmporteerde configuratie).
1. Klik op de pagina **TestConfig Configuration** op **Compileren**en klik vervolgens op **Ja**. Hiermee begint een compilatietaak.

   ![Schermafbeelding van de knop TestConfig-configuratiepagina met compilering](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Wanneer u een configuratie in Azure Automation compileert, worden alle gemaakte knooppuntconfiguratie-MOF's automatisch geïmplementeerd naar de pull-server.

## <a name="viewing-a-compilation-job"></a>Een compilatietaak weergeven

Nadat u een compilatie hebt gestart, u deze weergeven in de tegel **Compilatietaken** op de pagina **Configuratie.** De tegel **Compilatietaken** toont momenteel lopende, voltooide en mislukte taken. Wanneer u een taakpagina voor compilaties opent, wordt informatie over die taak weergegeven, inclusief eventuele fouten of waarschuwingen, invoerparameters die in de configuratie worden gebruikt en compilatielogboeken.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Klik aan de linkerkant op **Alle bronnen** en vervolgens op de naam van uw automatiseringsaccount.
1. Klik op de pagina **Automatiseringsaccount** op **Configuratie (DSC)** onder **Configuratiebeheer**.
1. Klik op de pagina **Statusconfiguratie (DSC)** op het tabblad **Configuraties** en klik vervolgens op **TestConfig** (de naam van de eerder geïmporteerde configuratie).
1. Selecteer **onder Compilatietaken**de compilatietaak die u wilt weergeven. Er wordt een **pagina Compilatietaak** geopend met de datum waarop de compilatietaak is gestart.

   ![Schermafbeelding van de pagina Compilatietaak](./media/automation-dsc-getting-started/CompilationJob.png)

1. Klik op een tegel op de pagina **Compilatietaak** voor meer informatie over de taak.

## <a name="viewing-node-configurations"></a>Knooppuntconfiguraties weergeven

Als een compilatietaak succesvol is voltooid, worden een of meer nieuwe knooppuntconfiguraties gemaakt. Een knooppuntconfiguratie is een MOF-document dat wordt geïmplementeerd op de pull-server en klaar is om te worden getrokken en toegepast door een of meer knooppunten. U de knooppuntconfiguraties in uw Automatiseringsaccount bekijken op de pagina **Statusconfiguratie (DSC).** Een knooppuntconfiguratie heeft een naam met het formulier *ConfigurationName*. *NodeName*.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Klik aan de linkerkant op **Alle bronnen** en vervolgens op de naam van uw automatiseringsaccount.
1. Klik op het beheer van het **automatiseringsaccount** op **Configuratie (DSC)** onder **Configuratiebeheer**.
1. Klik op de pagina **Statusconfiguratie (DSC)** op het tabblad **Gecompileerde configuraties.**

   ![Schermafbeelding van het tabblad Gecompileerde configuraties](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration"></a>Onboarding van een Azure VM voor beheer met Azure Automation State Configuration

U Azure Automation State Configuration gebruiken om Azure VM's (zowel Classic als Resource Manager), on-premises VM's, Linux-machines, AWS VM's en on-premises fysieke machines te beheren. In dit artikel leert u hoe u alleen in Azure Resource Manager VM's worden gebruikt. Zie [Onboarding-machines voor beheer door Azure Automation State Configuration voor](automation-dsc-onboarding.md)informatie over het inwerken van andere typen machines.

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-state-configuration"></a>Een Azure Resource Manager VM voor beheer door Azure Automation State Configuration aan boord nemen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Klik aan de linkerkant op **Alle bronnen** en vervolgens op de naam van uw automatiseringsaccount.
1. Klik op het beheer van het **automatiseringsaccount** op **Configuratie (DSC)** onder **Configuratiebeheer**.
1. Klik op de pagina **Statusconfiguratie (DSC)** terwijl u op het tabblad **Knooppunten** klikt op **+ Toevoegen**.

   ![Schermafbeelding van de pagina DSC-knooppunten met de knop Azure VM toevoegen](./media/automation-dsc-getting-started/OnboardVM.png)

1. Selecteer **op** de pagina Virtuele machines uw VM.
1. Klik op de detailpagina **Virtuele machine** op **+ Verbinden**.

   > [!IMPORTANT]
   > Dit moet een Azure Resource Manager VM zijn met een [ondersteund besturingssysteem](automation-dsc-overview.md#operating-system-requirements).

2. Selecteer **op** de pagina Registratie de naam van de knooppuntconfiguratie die u op de vm wilt toepassen in het vak **Knooppuntconfiguratie.** Het opgeven van een naam op dit punt is optioneel. U de toegewezen knooppuntconfiguratie wijzigen nadat u het knooppunt hebt ingetnen.
   Schakel **indien nodig knooppunt opnieuw opstarten in**en klik op **OK**.

   ![Schermafbeelding van het registratieblad](./media/automation-dsc-getting-started/RegisterVM.png)

   De door u opgegeven knooppuntconfiguratie wordt toegepast op de VM met intervallen die zijn opgegeven door de frequentie van de **configuratiemodus**en de VM controleert op updates van de knooppuntconfiguratie met intervallen die zijn opgegeven door de **verversingsfrequentie**. Zie [Lokaal configuratiebeheer configureren](/powershell/scripting/dsc/managing-nodes/metaConfig)voor meer informatie over hoe deze waarden worden gebruikt.

Azure start het proces voor het onboardingen van de VM. Wanneer deze is voltooid, wordt de vm weergegeven op het tabblad **Knooppunten** van de pagina **Statusconfiguratie (DSC)** in het automatiseringsaccount.

## <a name="viewing-the-list-of-managed-nodes"></a>De lijst met beheerde knooppunten weergeven

U de lijst met alle machines die zijn ingeschakeld voor beheer bekijken in uw Automatiseringsaccount op het tabblad Knooppunten van de pagina DSC.You can view the list of all machines that have been onboarded for management in your Automation account in the **Nodes** tab of the **State configuration (DSC)** page.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Klik aan de linkerkant op **Alle bronnen** en vervolgens op de naam van uw automatiseringsaccount.
1. Klik op het beheer van het **automatiseringsaccount** op **Configuratie (DSC)** onder **Configuratiebeheer**.
1. Klik op de pagina **Statusconfiguratie (DSC)** op het tabblad **Knooppunten.**

## <a name="viewing-reports-for-managed-nodes"></a>Rapporten weergeven voor beheerde knooppunten

Elke keer dat azure automation state configuration een consistentiecontrole uitvoert op een beheerd knooppunt, stuurt het knooppunt een statusrapport terug naar de pull-server. U deze rapporten bekijken op de pagina voor dat knooppunt.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Klik aan de linkerkant op **Alle bronnen** en vervolgens op de naam van uw automatiseringsaccount.
1. Klik op het beheer van het **automatiseringsaccount** op **Configuratie (DSC)** onder **Configuratiebeheer**.
1. Klik op de pagina **Statusconfiguratie (DSC)** op het tabblad **Knooppunten.** Hier ziet u het overzicht van de configuratiestatus en de details voor elk knooppunt.

   ![Schermafbeelding van knooppuntpagina](./media/automation-dsc-getting-started/NodesTab.png)

1. Klik op het tabblad **Knooppunten** op de knooppuntrecord om de rapportage te openen. Klik op het rapport dat u aanvullende rapportagegegevens wilt weergeven.

   ![Schermafbeelding van het rapportblad](./media/automation-dsc-getting-started/NodeReport.png)

Op het blad voor een afzonderlijk rapport ziet u de volgende statusgegevens voor de bijbehorende consistentiecontrole:

- De rapportstatus : of het knooppunt nu 'Compatibel' is, de configuratie 'Mislukt' of het knooppunt is 'Niet compatibel' (wanneer het knooppunt in de **ApplyandMonitor-modus** staat en de machine zich niet in de gewenste staat bevindt).
- De begintijd voor de consistentiecontrole.
- De totale runtime voor de consistentiecontrole.
- Het type consistentiecontrole.
- Eventuele fouten, waaronder de foutcode en foutmelding.
- Alle DSC-resources die in de configuratie worden gebruikt en de status van elke resource (of het knooppunt zich in de gewenste status voor die resource bevindt) - u op elke resource klikken om meer gedetailleerde informatie voor die resource te krijgen.
- De naam, het IP-adres en de configuratiemodus van het knooppunt.

U ook op **Onbewerkt rapport weergeven** klikken om de werkelijke gegevens te zien die het knooppunt naar de server verzendt.
Zie [Een DSC-rapportserver gebruiken](/powershell/scripting/dsc/pull-server/reportserver)voor meer informatie over het gebruik van die gegevens.

Het kan enige tijd duren nadat een knooppunt is aanboord voordat het eerste rapport beschikbaar is. Mogelijk moet u tot 30 minuten wachten op het eerste rapport nadat u aan boord van een knooppunt bent.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Een knooppunt toewijzen aan een andere knooppuntconfiguratie

U een knooppunt toewijzen om een andere knooppuntconfiguratie te gebruiken dan de configuratie die u in eerste instantie hebt toegewezen.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Klik aan de linkerkant op **Alle bronnen** en vervolgens op de naam van uw automatiseringsaccount.
1. Klik op het beheer van het **automatiseringsaccount** op **Configuratie (DSC)** onder **Configuratiebeheer**.
1. Klik op de pagina **Statusconfiguratie (DSC)** op het tabblad **Knooppunten.**
1. Klik op het tabblad **Knooppunten** op de naam van het knooppunt dat u opnieuw wilt toewijzen.
1. Klik op de pagina voor dat knooppunt op **Knooppuntconfiguratie toewijzen**.

    ![Schermafbeelding van de pagina Knooppuntdetails met de knop Knooppuntconfiguratie toewijzen](./media/automation-dsc-getting-started/AssignNode.png)

1. Selecteer op de pagina **Knooppuntconfiguratie toewijzen** de knooppuntconfiguratie waaraan u het knooppunt wilt toewijzen en klik op **OK**.

    ![Schermafbeelding van de pagina Knooppuntconfiguratie toewijzen](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Het registreren van een knooppunt uitschrijven

Als u niet langer wilt dat een knooppunt wordt beheerd door Azure Automation DSC, u het registreren uitschrijven.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Klik aan de linkerkant op **Alle bronnen** en vervolgens op de naam van uw automatiseringsaccount.
1. Klik op het beheer van het **automatiseringsaccount** op **Configuratie (DSC)** onder **Configuratiebeheer**.
1. Klik op de pagina **Statusconfiguratie (DSC)** op het tabblad **Knooppunten.**
1. Klik op het tabblad **Knooppunten** op de naam van het knooppunt dat u wilt uitschrijven.
1. Klik op de pagina voor dat knooppunt op **Uitschrijven**.

    ![Schermafbeelding van de pagina Knooppuntdetails met de knop Uitschrijven markeren](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Verwante artikelen

- [Overzicht van configuratie van Azure Automation State](automation-dsc-overview.md)
- [Onboarding-machines voor beheer door Azure Automation State Configuration](automation-dsc-onboarding.md)
- [Overzicht van de gewenste configuratie van Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [CmdLets azure automation state configuration](/powershell/module/azurerm.automation/#automation)
- [Azure Automation State Configuration Pricing Azure Automation State Configuration Pricing](https://azure.microsoft.com/pricing/details/automation/)
