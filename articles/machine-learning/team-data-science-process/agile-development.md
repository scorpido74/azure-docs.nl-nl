---
title: Agile ontwikkeling van data science projecten - Team Data Science Process
description: Voer een data science project uit op een systematische, versiegestuurde en collaboratieve manier binnen een projectteam met behulp van het Team Data Science Process.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c097c14406349d973e905fadb806cc159d9b16d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722098"
---
# <a name="agile-development-of-data-science-projects"></a>Agile ontwikkeling van data science projecten

Dit document beschrijft hoe ontwikkelaars een data science-project op een systematische, versiebeheerde en collaboratieve manier binnen een projectteam kunnen uitvoeren met behulp van het [Team Data Science Process](overview.md) (TDSP). De TDSP is een framework ontwikkeld door Microsoft dat een gestructureerde reeks activiteiten biedt om cloudgebaseerde, voorspellende analyseoplossingen efficiënt uit te voeren. Zie [Team Data Science Process rollen en taken](roles-tasks.md)voor een overzicht van de rollen en taken die worden uitgevoerd door een data science-team dat standaard is op het TDSP. 

Dit artikel bevat instructies over hoe: 

- Doe *sprint planning* voor werk items die betrokken zijn bij een project.
- *Werkitems* toevoegen aan sprints.
- Maak en gebruik een *agile-afgeleide werkitemsjabloon* die specifiek aansluit bij de levenscyclusfasen van TDSP.

In de volgende instructies worden de stappen beschreven die nodig zijn om een TDSP-teamomgeving in te stellen met Azure Boards en Azure Repos in Azure DevOps. De instructies maken gebruik van Azure DevOps, omdat u TDSP bij Microsoft zo implementeert. Als uw groep een ander codehostingplatform gebruikt, worden de taken van de teamlead over het algemeen niet gewijzigd, maar de manier om de taken te voltooien is anders. Het koppelen van een werkitem aan een Git-filiaal is bijvoorbeeld mogelijk niet hetzelfde met GitHub als met Azure Repos.

De volgende figuur illustreert een typische sprintplanning-, coderings- en broncontroleworkflow voor een data science-project:

![Team Data Science Process](./media/agile-development/1-project-execute.png)

##  <a name="work-item-types"></a><a name='Terminology-1'></a>Werkitemtypen

In het TDSP-sprintplanningskader zijn er vier veelgebruikte *werkitemtypen:* *Functies*, *Gebruikersverhalen,* *Taken*en *Bugs*. De achterstand voor alle werkitems is op projectniveau, niet op het Git-repository-niveau. 

Dit zijn de definities voor de typen werkitems:

- **Functie:** een functie komt overeen met een projectbetrokkenheid. Verschillende interacties met een klant zijn verschillende functies en het is het beste om verschillende fasen van een project als verschillende functies te beschouwen. Als u een schema kiest zoals * \<ClientName>-\<EngagementName>* om uw functies een naam te geven, u eenvoudig de context van het project en de betrokkenheid van de namen zelf herkennen.
  
- **Gebruikersverhaal**: Gebruikersverhalen zijn werkitems die nodig zijn om een functie end-to-end te voltooien. Voorbeelden van gebruikersverhalen zijn:
  - Gegevens ophalen 
  - Gegevens verkennen 
  - Functies genereren
  - Modellen bouwen
  - Modellen operationeel maken 
  - Modellen opnieuw trainen
  
- **Taak:** Taken zijn toewijsbare werkitems die moeten worden uitgevoerd om een specifiek gebruikersartikel te voltooien. Taken in de gegevens van *gebruikersverhaalget* kunnen bijvoorbeeld zijn:
  - SQL Server-referenties ophalen
  - Gegevens uploaden naar SQL Data Warehouse
  
- **Bug:** Bugs zijn problemen in bestaande code of documenten die moeten worden opgelost om een taak te voltooien. Als bugs worden veroorzaakt door ontbrekende werkitems, kunnen ze escaleren tot gebruikersverhalen of taken. 

Gegevenswetenschappers kunnen zich comfortabeler voelen met behulp van een flexibele sjabloon die functies, gebruikersverhalen en taken vervangt door TDSP-levenscyclusfasen en substadia. Zie [Een flexibele TDSP-werksjabloon gebruiken](#set-up-agile-dsp-6)als u een sjabloon voor agile afgeleide wilt maken die specifiek aansluit bij de levenscyclusfasen van TDSP.

> [!NOTE]
> TDSP leent de concepten functies, gebruikersverhalen, taken en bugs van softwarecodebeheer (SCM). De TDSP-concepten kunnen enigszins afwijken van hun conventionele SCM-definities.

## <a name="plan-sprints"></a><a name='SprintPlanning-2'></a>Sprints plannen

Veel data scientists zijn bezig met meerdere projecten, die maanden kunnen duren om te voltooien en in verschillende tempo's te gaan. Sprintplanning is handig voor projectprioritering en resourceplanning en -toewijzing. In Azure Boards u eenvoudig werkitems voor uw projecten maken, beheren en bijhouden en sprintplanning uitvoeren om ervoor te zorgen dat projecten verder gaan zoals verwacht.

Zie [Scrum sprints](https://en.wikipedia.org/wiki/Scrum_(software_development)#Sprint)voor meer informatie over sprintplanning. 

Zie [Backlog-items toewijzen aan een sprint voor](/azure/devops/boards/sprints/assign-work-sprint)meer informatie over sprintplanning in Azure-borden. 

## <a name="add-a-feature-to-the-backlog"></a><a name='AddFeature-3'></a>Een functie toevoegen aan de achterstand 

Nadat uw project- en projectcoderepository is gemaakt, u een functie toevoegen aan de achterstand om het werk voor uw project weer te geven.

1. Selecteer op uw projectpagina De optie **Backlogs** > **in** de linkernavigatie. 
   
1. Als het werkitemtype in de bovenste balk op het tabblad **Achterstand** **wordt weergegeven,** wordt de vervolgkeuzelijst en selecteert u **Functies**. Selecteer vervolgens **Nieuw werkitem.**
   
   ![Nieuw werkitem selecteren](./media/agile-development/2-sprint-team-overview.png)
   
1. Voer een titel in voor de functie, meestal uw projectnaam, en selecteer **Toevoegen aan boven**. 
   
   ![Voer een titel in en selecteer Toevoegen aan boven](./media/agile-development/3-sprint-team-add-work.png)
   
1. Selecteer en open de nieuwe functie in de lijst **Backlog.** Vul de beschrijving in, wijs een teamlid toe en stel planningsparameters in. 
   
   U de functie ook koppelen aan de Azure Repos-codeopslagplaats van het project door **koppeling toevoegen** te selecteren onder de sectie **Ontwikkeling.** 
   
   Nadat u de functie hebt bewerkt, selecteert u **& sluiten opslaan**.
   
   ![Functie bewerken en & sluiten opslaan selecteren](./media/agile-development/3a-add-link-repo.png)

## <a name="add-a-user-story-to-the-feature"></a><a name='AddStoryunderfeature-4'></a>Een gebruikersartikel toevoegen aan de functie 

Onder de functie u gebruikersverhalen toevoegen om de belangrijkste stappen te beschrijven die nodig zijn om het project te voltooien. 

Een nieuw gebruikersverhaal toevoegen aan een functie:

1. Selecteer **op** het tabblad **+** Achterstand de linkerkant van de functie. 
   
   ![Een nieuw gebruikersartikel toevoegen onder de functie](./media/agile-development/4-sprint-add-story.png)
   
1. Geef het gebruikersartikel een titel en bewerk details zoals toewijzing, status, beschrijving, opmerkingen, planning en prioriteit. 
   
   U het gebruikersartikel ook koppelen aan een filiaal van de Azure Repos-codeopslagplaats van het project door **koppeling toevoegen** te selecteren onder de sectie **Ontwikkeling.** Selecteer de opslagplaats en vertakking waaraan u het werkitem wilt koppelen en selecteer **OK**.
   
   ![Koppeling toevoegen](./media/agile-development/5-sprint-edit-story.png)
   
1. Wanneer u klaar bent met het bewerken van het gebruikersartikel, selecteert u **& Sluiten opslaan**. 

## <a name="add-a-task-to-a-user-story"></a><a name='AddTaskunderstory-5'></a>Een taak toevoegen aan een gebruikersartikel 

Taken zijn specifieke gedetailleerde stappen die nodig zijn om elk gebruikersartikel te voltooien. Nadat alle taken van een gebruikersartikel zijn voltooid, moet ook het gebruikersverhaal worden voltooid. 

Als u een taak aan een **+** gebruikersartikel wilt toevoegen, selecteert u het volgende naast het item Gebruikersverhaal en selecteert u **Taak**. Vul de titel en andere informatie in de taak in.

![Een taak toevoegen aan een gebruikersartikel](./media/agile-development/7-sprint-add-task.png)

Nadat u Functies, Gebruikersverhalen en taken hebt gemaakt, u ze bekijken in de weergaven **Backlogs** of **Boards** om hun status bij te houden.

![Weergave Achterstanden](./media/agile-development/8-sprint-backlog-view.png)

![Weergave Borden](./media/agile-development/8a-sprint-board-view.png)

## <a name="use-an-agile-tdsp-work-template"></a><a name='set-up-agile-dsp-6'></a>Een flexibele TDSP-werksjabloon gebruiken

Gegevenswetenschappers kunnen zich comfortabeler voelen met behulp van een flexibele sjabloon die functies, gebruikersverhalen en taken vervangt door TDSP-levenscyclusfasen en substadia. In Azure Boards u een door Agile afgeleide sjabloon maken waarmee tdsp-levenscyclusfasen worden gebruikt om werkitems te maken en bij te houden. De volgende stappen doorlopen het instellen van een data science-specifieke agile processjabloon en het maken van data science-werkitems op basis van de sjabloon.

### <a name="set-up-an-agile-data-science-process-template"></a>Een sjabloon voor Agile Data Science Process instellen

1. Selecteer op de hoofdpagina van uw Azure DevOps-organisatie **de optie Organisatie-instellingen** op de linkernavigatie. 
   
1. Selecteer In de **navigatie met de organisatie-instellingen** onder **Borden**de optie **Proces**. 
   
1. Selecteer in het deelvenster **Alle processen** de **...** naast **Agile**en selecteer **Vervolgens Overgenomen proces maken**.
   
   ![Overgenomen proces maken vanuit Agile](./media/agile-development/10-settings.png) 
   
1. Voer in het **dialoogvenster Overgenomen proces maken van Agile** de naam *AgileDataScienceProcess*in en selecteer **Proces maken**.
   
   ![AgileDataScienceProcess-proces maken](./media/agile-development/11-agileds.png)
   
1. Selecteer in **Alle processen**het nieuwe **AgileDataScienceProcess.** 
   
1. Schakel op het tabblad **Werkitemtypen** **Epic**, **Feature**, **User Story**en **Task** uit door de **...** naast elk item te selecteren en vervolgens Uitschakelen **te selecteren.** 
   
   ![Typen werkitemtypen uitschakelen](./media/agile-development/12-disable.png)
   
1. Selecteer **in Alle processen**het tabblad **Achterstandsniveaus.** Selecteer **onder Portfoliobacklogs**de **...** naast **Epic (uitgeschakeld)** en selecteer vervolgens **Bewerken/Hernoemen**. 
   
1. Ga als een dialoogvenster naar **het niveau van de achterstand bewerken:**
   1. Onder **Naam**, vervangen **Epic** met *TDSP Projecten*. 
   1. Selecteer **Onder Werkitemtypen op dit achterstandsniveau**de optie **Nieuw werkitemtype**, Voer *TDSP-project*in en selecteer **Toevoegen**. 
   1. Laat **het type Standaardwerkitem**vallen en selecteer **TDSP-project**. 
   1. Selecteer **Opslaan**.
   
   ![Portefeuilleachterstandsniveau instellen](./media/agile-development/13-rename.png)  
   
1. Volg dezelfde stappen om de naam **van functies** te wijzigen in *TDSP-fasen*en voeg de volgende nieuwe werkitemtypen toe:
   
   - *Bedrijfsinzicht*
   - *Gegevensverwerving*
   - *Modelleren*
   - *Implementatie*
   
1. Wijzig **onder Vereiste backlog**de naam van **Verhalen** in *TDSP-substadia*, voeg het nieuwe werkitemtype *TDSP-subfase*toe en stel het standaardwerkitemtype in op **TDSP-subfase**.
   
1. Voeg **onder Iteratiebacklog**een nieuw werkitemtype *TDSP-taak*toe en stel deze in als het standaardwerkitemtype. 
   
Nadat u de stappen hebt voltooid, moeten de achterstandsniveaus er als volgt uitzien:
   
 ![TDSP-sjabloonachterstandsniveaus](./media/agile-development/14-template.png)  

### <a name="create-agile-data-science-process-work-items"></a>Werkitems van Agile Data Science Process maken

U de sjabloon voor het gegevenswetenschapsproces gebruiken om TDSP-projecten te maken en werkitems bij te houden die overeenkomen met de levenscyclusfasen van TDSP.

1. Selecteer op de hoofdpagina van uw Azure DevOps-organisatie de optie **Nieuw project**. 
   
1. Geef uw project in het dialoogvenster **Nieuw project maken** een naam en selecteer **Geavanceerd**. 
   
1. Val onder **Werkitemproces**de val op en selecteer **AgileDataScienceProcess**en selecteer **Vervolgens Maken**.
   
   ![Een TDSP-project maken](./media/agile-development/15-newproject.png)
   
1. Selecteer in het nieuw gemaakte project De optie **Backlogs** > **Backlogs** in de linkernavigatie.
   
1. Als u TDSP-projecten zichtbaar wilt maken, selecteert u het pictogram **Teaminstellingen configureren.** Schakel **in** het scherm Instellingen het selectievakje **TDSP-projecten** in en selecteer **Opslaan en sluiten**.
   
   ![Het selectievakje TDSP-projecten inschakelen](./media/agile-development/16-enabledsprojects1.png)
   
1. Als u een tdsp-project voor gegevenswetenschap wilt maken, selecteert u **TDSP-projecten** in de bovenste balk en selecteert u **Vervolgens Nieuw werkitem**. 
   
1. Geef in de pop-up het werkitem TDSP Project een naam en selecteer **Toevoegen aan boven**.
   
   ![Werkitem data science-project maken](./media/agile-development/17-dsworkitems0.png)
   
1. Als u een werkitem wilt toevoegen onder **+** het TDSP-project, selecteert u het volgende naast het project en selecteert u het type werkitem dat u wilt maken. 
   
   ![Itemtype gegevenswetenschap selecteren](./media/agile-development/17-dsworkitems1.png)
   
1. Vul de details in het nieuwe werkitem in en selecteer **& sluiten opslaan**.
   
1. Blijf de **+** symbolen naast werkitems selecteren om nieuwe TDSP-fasen, substadia en taken toe te voegen. 
   
Hier is een voorbeeld van hoe de gegevenswetenschapsprojectitems moeten worden weergegeven in de weergave **Achterstanden:**

![18](./media/agile-development/18-workitems1.png)


## <a name="next-steps"></a>Volgende stappen

[Collaboratie codering met Git](collaborative-coding-with-git.md) beschrijft hoe samenwerkingscode ontwikkeling te doen voor data science projecten met behulp van Git als de gedeelde code development framework, en hoe deze codering activiteiten te koppelen aan het werk gepland met het agile proces.

[Voorbeeld walkthroughs](walkthroughs.md) lijsten walkthroughs van specifieke scenario's, met links en miniatuur beschrijvingen. De gekoppelde scenario's illustreren hoe cloud- en on-premises tools en services kunnen worden gecombineerd in workflows of pijplijnen om intelligente toepassingen te maken.
  
Aanvullende bronnen voor agile processen:

- [Agile-proces](/azure/devops/boards/work-items/guidance/agile-process)
  
- [Agile proceswerkitemtypen en -werkstroom](/azure/devops/boards/work-items/guidance/agile-process-workflow)

