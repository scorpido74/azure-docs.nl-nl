---
title: Samenwerken met Git - Team Data Science Process
description: Hoe deelcode ontwikkeling te doen voor data science projecten met behulp van Git met agile planning.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 0708e395eff90ff5b889c05f0fd5e7a98205c5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721894"
---
# <a name="collaborative-coding-with-git"></a>Samenwerken aan code met Git

In dit artikel wordt beschreven hoe git kan worden gebruikt als het samenwerkingscodeontwikkelingskader voor data science-projecten. In het artikel wordt inbehandeld hoe u code in Azure Repos koppelt aan [werkitems voor flexibele ontwikkeling](agile-development.md) in Azure-borden, hoe u codebeoordelingen maken en samenvoegen en hoe u pull-aanvragen voor wijzigingen maken en samenvoegen.

## <a name="link-a-work-item-to-an-azure-repos-branch"></a><a name='Linkaworkitemwithagitbranch-1'></a>Een werkitem koppelen aan een Azure Repos-vertakking 

Azure DevOps biedt een handige manier om een Azure Boards User Story of Taakwerkitem te verbinden met een Azure Repos Git-repository-branch. U uw gebruikersartikel of taak rechtstreeks koppelen aan de code die eraan is gekoppeld. 

Als u een werkitem wilt verbinden met een nieuwe vertakking, selecteert u de **acties** -ovaal (**...**) naast het werkitem en bladert u in het contextmenu naar en selecteert **u Nieuwe vertakking**.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

Geef in het dialoogvenster **Een vertakking maken** de nieuwe branchnaam en de basis Azure Repos Git-opslagplaats en vertakking op. De basisopslagplaats moet zich in hetzelfde Azure DevOps-project bevinden als het werkitem. De basistak kan de hoofdvertakking of een andere bestaande vertakking zijn. Selecteer **Vertakking maken**. 

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

U ook een nieuwe branch maken met de volgende Git bash-opdracht in Windows of Linux:

```bash
git checkout -b <new branch name> <base branch name>

```
Als u geen basisnaam \<> opgeeft, is de `master`nieuwe vertakking gebaseerd op . 

Voer de volgende opdracht uit om over te schakelen naar uw werkkantoor: 

```bash
git checkout <working branch name>
```

Nadat u bent overgestapt naar de werkbranche, u beginnen met het ontwikkelen van code- of documentatieartefacten om het werkitem te voltooien. Hardlopen `git checkout master` schakelt je `master` terug naar de tak.

Het is een goede gewoonte om een Git-filiaal te maken voor elk werkitem voor gebruikersverhalen. Vervolgens u voor elk taakwerkitem een vertakking maken op basis van de vertakking van het gebruikersartikel. Organiseer de branches in een hiërarchie die overeenkomt met de relatie User Story-Task wanneer u meerdere mensen hebt die aan verschillende gebruikersverhalen werken voor hetzelfde project of aan verschillende taken voor hetzelfde gebruikersartikel. U conflicten minimaliseren door elk teamlid te laten werken op een andere branch of aan verschillende code of andere artefacten wanneer u een branch deelt. 

In het volgende diagram ziet u de aanbevolen vertakkingsstrategie voor TDSP. U hebt mogelijk niet zoveel branches nodig als hier wordt weergegeven, vooral wanneer slechts één of twee mensen aan een project werken, of slechts één persoon aan alle taken van een gebruikersartikel werkt. Maar het scheiden van de ontwikkelingstak van de master branch is altijd een goede praktijk, en kan helpen voorkomen dat de release branch wordt onderbroken door ontwikkelingsactiviteiten. Zie [Een succesvol Git Branching Model](https://nvie.com/posts/a-successful-git-branching-model/)voor een volledige beschrijving van het Git-vertakkingsmodel.

![3](./media/collaborative-coding-with-git/3-git-branches.png)

U een werkitem ook koppelen aan een bestaande vestiging. Selecteer **koppeling toevoegen**op de pagina **Detail** van een werkitem . Selecteer vervolgens een bestaande vertakking waaraan het werkitem moet worden gekoppeld en selecteer **OK**. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

## <a name="work-on-the-branch-and-commit-changes"></a><a name='WorkonaBranchandCommittheChanges-2'></a>Werk aan de branche en bega veranderingen 

Nadat u een wijziging hebt aangebracht voor uw werkitem, zoals het `script` toevoegen van een R-scriptbestand aan de branch van uw lokale machine, u de wijziging van uw lokale filiaal naar de upstream-werktak vastleggen met behulp van de volgende Git-bash-opdrachten:

```bash
git status
git add .
git commit -m "added an R script file"
git push origin script
```

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## <a name="create-a-pull-request"></a><a name='CreateapullrequestonVSTS-3'></a>Een pull-aanvraag maken

Nadat een of meer commits en pushes, wanneer u klaar bent om uw huidige werktak samen te voegen in de basisbranch, u een *pull-aanvraag* maken en indienen in Azure Repos. 

Wijs vanaf de hoofdpagina van uw Azure DevOps-project **Repos** > **Pull-aanvragen** aan in de linkernavigatie. Selecteer vervolgens een van de knoppen **Nieuw uittrekverzoek** of de koppeling **Een pullrequest maken.**

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

Navigeer indien nodig in het scherm **Nieuw pull-verzoek** naar de Git-opslagplaats en de branch waar u uw wijzigingen in wilt samenvoegen. Voeg andere gewenste informatie toe of wijzig deze. Voeg **onder Revisoren**de namen van de revisoren toe en selecteer **Vervolgens Maken**. 

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## <a name="review-and-merge"></a><a name='ReviewandMerge-4'></a>Controleren en samenvoegen

Zodra u het pull-verzoek hebt gemaakt, ontvangen uw revisoren een e-mailmelding om het pull-verzoek te bekijken. De revisoren testen of de wijzigingen werken en controleren de wijzigingen indien mogelijk met de aanvrager. De revisoren kunnen opmerkingen maken, wijzigingen aanvragen en de pull-aanvraag goedkeuren of weigeren op basis van hun beoordeling. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

Nadat de revisoren de wijzigingen hebben goedgekeurd, u of iemand anders met samenvoegmachtigingen de werktak samenvoegen met de basisbranch. Selecteer **Voltooien**en selecteer Vervolgens **Samenvoegen voltooien** in het dialoogvenster **Uittrekaanvraag** voltooien. U ervoor kiezen om het werkkantoor te verwijderen nadat deze is samengevoegd. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Controleer of de aanvraag is gemarkeerd als **VOLTOOID**. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Wanneer u teruggaat naar **Repos** in de linkernavigatie, u zien dat `script` u bent overgeschakeld naar de hoofdvertakking sinds de branch is verwijderd.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

U ook de volgende Git-bashopdrachten gebruiken om de `script` werkbranch samen te voegen naar de basisbranch en de werktak te verwijderen na het samenvoegen:

```bash
git checkout master
git merge script
git branch -d script
```

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)

## <a name="next-steps"></a>Volgende stappen

[Het uitvoeren van gegevenswetenschapstaken](execute-data-science-tasks.md) laat zien hoe u hulpprogramma's gebruiken om verschillende veelvoorkomende gegevenswetenschappelijke taken uit te voeren, zoals interactieve gegevensverkenning, gegevensanalyse, rapportage en het maken van modellen.

[Voorbeeld walkthroughs](walkthroughs.md) lijsten walkthroughs van specifieke scenario's, met links en miniatuur beschrijvingen. De gekoppelde scenario's illustreren hoe cloud- en on-premises tools en services kunnen worden gecombineerd in workflows of pijplijnen om intelligente toepassingen te maken. 

