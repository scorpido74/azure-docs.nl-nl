---
title: Testen van data science-code met Services van Azure DevOps - Team Data Science Process
description: Data science code testen op Azure met de gegevensset UCI volwassenen inkomsten voorspelling met het Team Data Science Process en Azure DevOps-Services
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=weig, previous-ms.author=weig
ms.openlocfilehash: 9612114bb368898ccf31b2c8692869b84544b652
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721967"
---
# <a name="data-science-code-testing-on-azure-with-the-team-data-science-process-and-azure-devops-services"></a>Data science code testen op Azure met het Team Data Science Process en Azure DevOps-Services
Dit artikel bevat voorlopige richtlijnen voor het testen van code in een werkstroom voor datatechnologie. Dergelijke test biedt gegevenswetenschappers een systematische en efficiënte manier om te controleren of de kwaliteit en de verwachte resultaten van hun code. We gebruiken een Team Data Science Process (TDSP) [project die gebruikmaakt van de gegevensset UCI volwassenen inkomsten](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) die we eerder gepubliceerd om weer te geven hoe het testen van code kan worden gedaan. 

## <a name="introduction-on-code-testing"></a>Inleiding over het testen van code
'Eenheidstesten' is een zich al geruime tijd procedure voor het ontwikkelen van software. Maar voor een gegevens wetenschap is het vaak niet duidelijk wat ' eenheids testen ' betekent en hoe u code moet testen voor verschillende stadia van een levens cyclus van data wetenschappen, zoals:

* Gegevensvoorbereiding
* Data quality onderzoek
* Modelleren
* Modelimplementatie 

In dit artikel vervangt de term 'eenheid testen"met"code testen." Deze gegevensset verwijst naar testen als de functies die u helpen te beoordelen als code voor een bepaalde stap van een volledige wetenschappelijke levensduur levert resultaten 'zoals verwacht." De persoon die dat de test definieert wat schrijft 'zoals verwacht,"is afhankelijk van het resultaat van de functie--bijvoorbeeld kwaliteitscontrole van de gegevens of modellen.

Dit artikel bevat verwijzingen naar als handige resources.

## <a name="azure-devops-for-the-testing-framework"></a>Azure DevOps voor het testen framework
In dit artikel wordt beschreven hoe u uitvoeren en testen met behulp van Azure DevOps te automatiseren. U kunt andere hulpprogramma's gebruiken. We laten ook zien hoe u een automatische build instellen met behulp van Azure DevOps en build-agents. Voor de build-agents gebruiken we Azure Data Science Virtual Machines (Dsvm).

## <a name="flow-of-code-testing"></a>Stroom van het testen van code
De algemene werkstroom testen in een data science-project de code ziet er als volgt: 

![Stroomdiagram van het testen van code](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>Gedetailleerde stappen

Gebruik de volgende stappen uit om te stellen en het testen van code en een geautomatiseerde build uitvoeren met behulp van een build-agent en de Azure DevOps:

1. Een project maken in de Visual Studio-bureaubladtoepassing:

    ![Scherm 'Nieuw project maken' in Visual Studio](./media/code-test/create_project.PNG)

   Nadat u uw project maakt, moet u deze vinden in Solution Explorer in het rechterdeelvenster:
    
    ![Stappen voor het maken van een project](./media/code-test/create_python_project_in_vs.PNG)

    ![Solution Explorer](./media/code-test/solution_explorer_in_vs.PNG)

1. De projectcode van uw in de opslagplaats met Azure DevOps-project code feed: 

    ![Codeopslagplaats project](./media/code-test/create_repo.PNG)

1. Stel dat u sommige gegevensvoorbereidingswerkzaamheden, zoals gegevensopname, feature-engineering en het maken van label kolommen hebben gedaan. U wilt controleren of dat uw code genereert de resultaten die u verwacht. Dit is wat code die u gebruiken kunt om te testen of de code van de gegevensverwerking correct werkt:

    * Controleer of de namen van kolommen rechts zijn:
    
      ![Code voor het afstemmen van de namen van kolommen](./media/code-test/check_column_names.PNG)

    * Controleer of responsniveaus rechts zijn:

      ![Code voor die overeenkomen met niveaus](./media/code-test/check_response_levels.PNG)

    * Controleer of de antwoord-percentage redelijke is:

      ![Code voor het percentage van de reactie voor](./media/code-test/check_response_percentage.PNG)

    * Controleer de ontbrekende snelheid van elke kolom in de gegevens:
    
      ![Code voor een ontbrekende snelheid](./media/code-test/check_missing_rate.PNG)


1. Nadat u hebt de gegevensverwerking en functie-engineering werk gedaan en u een goed model hebt getraind, zorg ervoor dat het model dat u getraind correct nieuwe gegevenssets kunt beoordelen. U kunt de volgende twee tests uit om te controleren of de voorspelling niveaus en distributie van de labelwaarden:

    * Controleer de voorspelling niveaus:
    
      ![Code voor het controleren van voorspelling niveaus](./media/code-test/check_prediction_levels.PNG)

    * Controleer de distributie van voorspelling waarden:

      ![Code voor het controleren van de waarden voor voorspelling](./media/code-test/check_prediction_values.PNG)

1. Alle functies samen in een pythonscript met de naam test put **test_funcs.py**:

    ![Python-script voor test-functies](./media/code-test/create_file_test_func.PNG)


1. Nadat de test-codes zijn voorbereid, kunt u de testomgeving in Visual Studio instellen.

   Maak een Python-bestand met de naam **test1.py**. In dit bestand, maakt u een klasse met alle tests die u wilt doen. Het volgende voorbeeld ziet u zes tests voorbereid:
    
    ![Python-bestand met een lijst met tests in een klasse](./media/code-test/create_file_test1_class.PNG)

1. Deze tests kunnen automatisch worden gedetecteerd als u **codetest.testCase** achter de klassenaam. Test Explorer openen in het rechterdeelvenster en selecteer **alles uitvoeren**. Alle tests achter elkaar wordt uitgevoerd en zien als de test geslaagd of niet is.

    ![De tests worden uitgevoerd](./media/code-test/run_tests.PNG)

1. Controleer in uw code in de projectopslagplaats met behulp van Git-opdrachten. Uw meest recente werk worden, weergegeven over enkele ogenblikken in Azure DevOps.

    ![GIT-opdrachten voor het controleren van in de code](./media/code-test/git_check_in.PNG)

    ![Meest recente werk in Azure DevOps](./media/code-test/git_check_in_most_recent_work.PNG)

1. Instellen van automatische bouwen en testen in Azure DevOps:

    a. Selecteer in de projectopslagplaats **Build and Release**, en selecteer vervolgens **+ nieuw** om een nieuwe buildproces te maken.

    ![Selecties voor het starten van een nieuwe bouw proces](./media/code-test/create_new_build.PNG)

    b. Volg de aanwijzingen om uw code bronlocatie, naam van het project, opslagplaats en vertakking informatie te selecteren.
    
    ![Bron-, naam-, opslag plaats-en vertakkings gegevens](./media/code-test/fill_in_build_info.PNG)

    c. Selecteer een sjabloon. Omdat er geen Python projectsjabloon, maken, starten door het selecteren van **leeg proces**. 

    ![Lijst met sjablonen en de knop ' leeg proces '](./media/code-test/start_empty_process_template.PNG)

    d. De naam van de build en selecteer de agent. U kunt hier de standaard optie kiezen als u een DSVM wilt gebruiken om het bouw proces te volt ooien. Zie voor meer informatie over de instelling agents [bouwen en uitbrengen agents](https://docs.microsoft.com/azure/devops/pipelines/agents/agents?view=vsts).
    
    ![Selecties voor Build en agent](./media/code-test/select_agent.PNG)

    e. Selecteer **+** in het linkerdeelvenster, een taak wordt toegevoegd voor deze build-fase. Omdat we het python-script **test1.py** uitvoeren om alle controles te volt ooien, gebruikt deze taak een Power shell-opdracht voor het uitvoeren van python-code.
    
    ![Deel venster taken toevoegen met Power shell geselecteerd](./media/code-test/add_task_powershell.PNG)

    f. In de details van PowerShell, vult u de vereiste gegevens, zoals de naam en versie van PowerShell. Kies **Inline Script** als het type. 
    
    In het vak onder **inline-script**kunt u **python test1.py**typen. Zorg ervoor dat de omgevings variabele juist is ingesteld voor python. Als u een andere versie of een andere kernel van python nodig hebt, kunt u het pad expliciet opgeven, zoals wordt weer gegeven in de afbeelding: 
    
    ![Power shell-Details](./media/code-test/powershell_scripts.PNG)

    g. Selecteer **& wachtrij opslaan** om het build pipeline-proces te volt ooien.

    ![Knop voor opslaan van & wachtrij](./media/code-test/save_and_queue_build_definition.PNG)

Nu telkens wanneer een nieuwe doorvoer wordt doorgestuurd naar de opslagplaats, wordt het bouwproces automatisch gestart. (Hier gebruiken we de Master als opslag plaats, maar u kunt een vertakking definiëren.) Het proces voert het **test1.py** -bestand in de agent machine uit om ervoor te zorgen dat alles wat in de code is gedefinieerd, correct wordt uitgevoerd. 

Als meldingen correct zijn ingesteld, u krijgt een bericht in e-mailbericht wanneer de build is voltooid. U kunt ook de status van de build in Azure DevOps controleren. Als dit mislukt, kunt u de details van de build en weten welk stuk verbroken is.

![E-mailmelding van de build-geslaagd](./media/code-test/email_build_succeed.PNG)

![Melding van slagen van de build van Azure DevOps](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>Volgende stappen
* Zie de [UCI inkomsten voorspelling opslagplaats](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) voor concrete voorbeelden van eenheidstests voor data science-scenario's.
* Volg de voorgaande overzicht en voorbeelden van het scenario UCI inkomsten voorspelling in uw eigen data science-projecten.

## <a name="references"></a>Naslaginformatie
* [Team Data Science Process](https://aka.ms/tdsp)
* [Hulpprogramma's voor Visual Studio testen](https://www.visualstudio.com/vs/features/testing-tools/)
* [Resources voor Azure DevOps testen](https://www.visualstudio.com/team-services/)
* [Virtuele Machines voor Datatechnologie](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)
