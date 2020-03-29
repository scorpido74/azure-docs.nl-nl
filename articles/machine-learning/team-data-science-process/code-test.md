---
title: Data science code testen met Azure DevOps Services - Team Data Science Process
description: Data science code testen op Azure met de UCI adult income prediction dataset met het Team Data Science Process en Azure DevOps Services
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721967"
---
# <a name="data-science-code-testing-on-azure-with-the-team-data-science-process-and-azure-devops-services"></a>Data science code testen op Azure met het Team Data Science Process en Azure DevOps Services
Dit artikel geeft voorlopige richtlijnen voor het testen van code in een data science workflow. Dergelijke tests geven data scientists een systematische en efficiënte manier om de kwaliteit en verwachte uitkomst van hun code te controleren. We gebruiken een Team Data Science Process (TDSP) [project dat gebruik maakt van de UCI Adult Income dataset](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) die we eerder hebben gepubliceerd om te laten zien hoe codetesten kunnen worden uitgevoerd. 

## <a name="introduction-on-code-testing"></a>Inleiding over codetesten
"Unit testing" is een langdurige praktijk voor softwareontwikkeling. Maar voor data science is het vaak niet duidelijk wat "unit testing" betekent en hoe u code moet testen voor verschillende stadia van een levenscyclus van data science, zoals:

* Gegevensvoorbereiding
* Kwaliteitsonderzoek van gegevens
* Modelleren
* Modelimplementatie 

Dit artikel vervangt de term "unit testing" door "code testing." Het verwijst naar testen als de functies die helpen om te beoordelen of code voor een bepaalde stap van een data science levenscyclus is het produceren van resultaten "zoals verwacht." De persoon die de test schrijft, definieert wat 'zoals verwacht' is, afhankelijk van de uitkomst van de functie, bijvoorbeeld gegevenskwaliteitscontrole of modellering.

In dit artikel worden verwijzingen als nuttige bronnen verstrekt.

## <a name="azure-devops-for-the-testing-framework"></a>Azure DevOps voor het testframework
In dit artikel wordt beschreven hoe u testen uitvoeren en automatiseren met Azure DevOps. U besluiten om alternatieve gereedschappen te gebruiken. We laten ook zien hoe u een automatische build instellen met Azure DevOps en buildagents. Voor buildagents gebruiken we Azure Data Science Virtual Machines (DSVMs).

## <a name="flow-of-code-testing"></a>Stroom van codetesten
De algehele workflow van het testen van code in een data science project ziet er als volgt uit: 

![Stroomdiagram van codetests](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>Gedetailleerde stappen

Gebruik de volgende stappen voor het instellen en uitvoeren van codetesten en een geautomatiseerde build met behulp van een buildagent en Azure DevOps:

1. Een project maken in de desktoptoepassing Visual Studio:

    ![Scherm 'Nieuw project maken' in Visual Studio](./media/code-test/create_project.PNG)

   Nadat u uw project hebt gemaakt, vindt u het in Solution Explorer in het rechterdeelvenster:
    
    ![Stappen voor het maken van een project](./media/code-test/create_python_project_in_vs.PNG)

    ![Solution Explorer](./media/code-test/solution_explorer_in_vs.PNG)

1. Voer uw projectcode in de Azure DevOps-projectcode-opslagplaats: 

    ![Projectcoderepository](./media/code-test/create_repo.PNG)

1. Stel dat u wat werk voor de voorbereiding van gegevens hebt uitgevoerd, zoals gegevensopname, functieengineering en het maken van labelkolommen. U wilt er zeker van zijn dat uw code de resultaten genereert die u verwacht. Hier is een code die u gebruiken om te testen of de gegevensverwerkingscode goed werkt:

    * Controleer of kolomnamen juist zijn:
    
      ![Code voor overeenkomende kolomnamen](./media/code-test/check_column_names.PNG)

    * Controleer of de responsniveaus juist zijn:

      ![Code voor overeenkomende niveaus](./media/code-test/check_response_levels.PNG)

    * Controleer of het responspercentage redelijk is:

      ![Code voor responspercentage](./media/code-test/check_response_percentage.PNG)

    * Controleer de ontbrekende snelheid van elke kolom in de gegevens:
    
      ![Code voor ontbrekend tarief](./media/code-test/check_missing_rate.PNG)


1. Nadat u het werk voor gegevensverwerking en functieengineering hebt uitgevoerd en u een goed model hebt getraind, moet u ervoor zorgen dat het model dat u hebt getraind, nieuwe gegevenssets correct kan scoren. U de volgende twee tests gebruiken om de voorspellingsniveaus en verdeling van labelwaarden te controleren:

    * Controleer voorspellingsniveaus:
    
      ![Code voor het controleren van voorspellingsniveaus](./media/code-test/check_prediction_levels.PNG)

    * Controleer de verdeling van de voorspellingswaarden:

      ![Code voor het controleren van voorspellingswaarden](./media/code-test/check_prediction_values.PNG)

1. Zet alle testfuncties samen in een Python-script genaamd **test_funcs.py:**

    ![Python-script voor testfuncties](./media/code-test/create_file_test_func.PNG)


1. Nadat de testcodes zijn opgesteld, u de testomgeving instellen in Visual Studio.

   Maak een Python-bestand met de naam **test1.py**. Maak in dit bestand een klasse met alle tests die u wilt uitvoeren. Het volgende voorbeeld toont zes voorbereide tests:
    
    ![Python-bestand met een lijst met tests in een klasse](./media/code-test/create_file_test1_class.PNG)

1. Deze tests kunnen automatisch worden ontdekt als u **codetest.testCase** achter uw klassennaam plaatst. Open Test Explorer in het rechterdeelvenster en selecteer **Alles uitvoeren**. Alle tests zullen opeenvolgend worden uitgevoerd en zullen u vertellen of de test succesvol is of niet.

    ![De tests uitvoeren](./media/code-test/run_tests.PNG)

1. Check uw code in bij de projectrepository met Git-opdrachten. Uw meest recente werk wordt binnenkort weergegeven in Azure DevOps.

    ![Git-opdrachten voor het inchecken van code](./media/code-test/git_check_in.PNG)

    ![Meest recente werk in Azure DevOps](./media/code-test/git_check_in_most_recent_work.PNG)

1. Automatische build en test instellen in Azure DevOps:

    a. Selecteer in de projectrepository **Build en Release**en selecteer **+Nieuw** om een nieuw buildproces te maken.

    ![Selecties voor het starten van een nieuw bouwproces](./media/code-test/create_new_build.PNG)

    b. Volg de aanwijzingen om uw broncodelocatie, projectnaam, opslagplaats en branch-informatie te selecteren.
    
    ![Bron-, naam-, opslagplaats- en vertakkingsgegevens](./media/code-test/fill_in_build_info.PNG)

    c. Selecteer een sjabloon. Omdat er geen Python-projectsjabloon is, moet u eerst **Leeg proces**selecteren. 

    ![Lijst met sjablonen en knop 'Proces leegmaken'](./media/code-test/start_empty_process_template.PNG)

    d. Geef de build een naam en selecteer de agent. U hier de standaardinstelling kiezen als u een DSVM wilt gebruiken om het bouwproces te voltooien. Zie Agents bouwen en vrijgeven voor meer informatie over het instellen [van agents.](https://docs.microsoft.com/azure/devops/pipelines/agents/agents?view=vsts)
    
    ![Selecties voor builden en agenten](./media/code-test/select_agent.PNG)

    e. Selecteer **+** in het linkerdeelvenster om een taak toe te voegen voor deze buildfase. Omdat we het Python-script **test1.py** uitvoeren om alle controles te voltooien, gebruikt deze taak een PowerShell-opdracht om Python-code uit te voeren.
    
    ![Deelvenster Taken toevoegen met PowerShell geselecteerd](./media/code-test/add_task_powershell.PNG)

    f. Vul in de PowerShell-gegevens de vereiste informatie in, zoals de naam en versie van PowerShell. Kies **Inline Script** als type. 
    
    In het vak onder **Inline Script**u **python test1.py**typen. Controleer of de omgevingsvariabele correct is ingesteld voor Python. Als u een andere versie of kernel van Python nodig hebt, u het pad zoals weergegeven in de figuur expliciet opgeven: 
    
    ![PowerShell-gegevens](./media/code-test/powershell_scripts.PNG)

    g. Selecteer **& wachtrij opslaan** om het proces van de opbouwpijplijn te voltooien.

    ![Knop & wachtrij opslaan](./media/code-test/save_and_queue_build_definition.PNG)

Elke keer dat een nieuwe commit naar de code repository wordt geduwd, wordt het buildproces automatisch gestart. (Hier gebruiken we master als de repository, maar je elke branch definiëren.) Het proces voert het **test1.py** bestand in de agentmachine uit om ervoor te zorgen dat alles wat in de code is gedefinieerd, correct wordt uitgevoerd. 

Als waarschuwingen correct zijn ingesteld, wordt u per e-mail op de hoogte gesteld wanneer de build is voltooid. U ook de buildstatus controleren in Azure DevOps. Als het mislukt, u de details van de build controleren en erachter komen welk stuk is gebroken.

![E-mail melding van succes op te bouwen](./media/code-test/email_build_succeed.PNG)

![Azure DevOps melding van succes op te bouwen](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>Volgende stappen
* Zie de [UCI-bronvan inkomsten voor](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) concrete voorbeelden van unittests voor data science-scenario's.
* Volg de voorgaande contouren en voorbeelden uit het UCI-inkomstenvoorspellingsscenario in uw eigen data science-projecten.

## <a name="references"></a>Verwijzingen
* [Team Data Science Process](https://aka.ms/tdsp)
* [Testprogramma's voor Visual Studio](https://www.visualstudio.com/vs/features/testing-tools/)
* [Azure DevOps Testresources](https://www.visualstudio.com/team-services/)
* [Data Science Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)
