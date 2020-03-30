---
title: Jupyter-notitieblokken maken en klonen - Proefversie van Azure Notebooks
description: Azure Notebooks Preview-projecten beheren een verzameling notitieblokken en gerelateerde bestanden, die u maken nieuw of kloon uit een andere bron.
ms.topic: how-to
ms.date: 02/25/2019
ms.openlocfilehash: b29ff336c09a3bbf05a57c8a3a503b1875b76e54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280571"
---
# <a name="create-and-clone-projects-in-azure-notebooks-preview"></a>Projecten maken en klonen in Azure Notebooks Preview

Azure Notebooks organiseert uw Jupyter-notitieblokken en gerelateerde bestanden in logische groepen die *projecten*worden genoemd. U maakt een project eerst als container en maakt of kloont vervolgens een of meer notitieblokken in een map naast andere projectbestanden. (Dit proces wordt gedemonstreerd in de [zelfstudie](tutorial-create-run-jupyter-notebook.md).)

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Een project onderhoudt ook metagegevens en andere configuratie-instellingen die van invloed zijn op de server waarop notitieblokken worden uitgevoerd, inclusief aangepaste installatiestappen en pakketinstallatie. Zie [Projecten beheren en configureren](configure-manage-azure-notebooks-projects.md)voor meer informatie.

## <a name="use-the-my-projects-dashboard"></a>Het dashboard Mijn projecten gebruiken

Op het dashboard `https://notebooks.azure.com/<userID>/projects` Mijn **projecten** u projecten bekijken, beheren en maken:

[![Dashboard Mijn projecten in Azure-notitieblokken](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

Wat u op het dashboard doen, is afhankelijk van of u bent aangemeld met het account dat eigenaar is van de gebruikersnaam:

| Opdracht | Beschikbaar voor | Beschrijving |
| --- | --- | --- |
| **Uitvoeren** | Eigenaar | Hiermee start u de projectserver en opent u de projectmap in Jupyter. (Meer in het algemeen navigeert u eerst naar een projectmap en start u vervolgens vanaf daar een notitieblok.) |
| **Downloaden** | Iedereen | Downloadt een kopie van het geselecteerde project als zip-bestand. |
| **Delen** | Iedereen | Hiermee wordt de pop-up voor delen weergegeven waarmee u een URL verkrijgen voor een geselecteerd project, delen met sociale media, een e-mail met de URL verzenden en zowel HTML- als Markdown-code verkrijgen met een badge voor het starten van notitiebloken (zie [een startbadge verkrijgen](#obtain-a-launch-badge)) met de URL. |
| **Verwijderen** | Eigenaar | Hiermee verwijdert u het geselecteerde project. Deze bewerking kan niet ongedaan worden gemaakt. |
| **Terminal** | Eigenaar | Hiermee start u de projectserver en opent u een nieuw browservenster met de bash-terminal voor die server. |
| **+ Nieuw project** | Eigenaar | Hiermee maakt u een nieuw project. Zie [Een nieuw project maken](#create-a-new-project). |
| **GitHub Repo uploaden** | Eigenaar | Importeert een project van GitHub. [Een project importeren vanuit GitHub](#import-a-project-from-github). |
| **Klonen** | Iedereen | Kopieert een geselecteerd project naar uw eigen account. Vraagt u om u aan te melden als u dit nog niet doet. Zie [Een project klonen](#clone-a-project). |

### <a name="obtain-a-launch-badge"></a>Een launch badge verkrijgen

Wanneer u de opdracht **Delen** gebruikt en het tabblad **Insluiten** selecteert, u HTML-code of Markdown kopiëren waarmee een badge met 'startnotitieblok' wordt gemaakt:

![Notitieblokbadge starten](https://notebooks.azure.com/launch.png)

Als u geen Azure Notebooks-project hebt, u een koppeling maken die rechtstreeks vanuit GitHub wordt gekloond met de volgende sjablonen, waarbij u de juiste gebruikersnaam- en archiefnamen vervangt:

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>Een nieuw project maken

Wanneer u de opdracht **+ Nieuw project** gebruikt, worden in Azure Notebooks een pop-up Nieuw project **maken** weergegeven. Voer in deze pop-up de volgende gegevens in en selecteer **Vervolgens Maken:**

| Veld | Beschrijving |
| --- | --- |
| Projectnaam | Een vriendelijke naam voor uw project die Azure Notebooks gebruikt voor weergavedoeleinden. Bijvoorbeeld 'Mijn notitieblokproject'. |
| Project-id | Een aangepaste id die onderdeel wordt van de URL die `https://notebooks.azure.com/<user_id>/projects/<project_id>`u gebruikt om een project te delen (het formulier is). Deze id kan alleen letters, cijfers en koppeltekens gebruiken, is beperkt tot 30 tekens en kan geen [gereserveerde project-ID](#reserved-project-ids)zijn. Als u niet zeker weet wat u moet gebruiken, moet een gemeenschappelijke conventie een kleine versie van uw projectnaam gebruiken waarbij spaties worden omgezet in koppeltekens, zoals 'mijn-notebook-project' (afgekapt indien nodig om aan de lengtelimiet te voldoen). |
| Public | Als deze optie is ingesteld, kan iedereen met de koppeling toegang krijgen tot het project. Wanneer u een privéproject maakt, moet u deze optie wissen. |
| Initialiseiseer dit project met een README | Als u deze optie instelt, maakt u een *standaardbestand README.md* in het project. Een *README.md* bestand is desgewenst documentatie voor uw project. |

### <a name="reserved-project-ids"></a>Gereserveerde project-i-d's

De volgende gereserveerde woorden kunnen niet door zichzelf als project-id's worden gebruikt. Deze gereserveerde woorden kunnen echter worden gebruikt als onderdeel van langere project-id's.

| | | | | | |
| --- | --- | --- | --- | --- | --- |
| Info | account | beheer | api | blog | leslokaal |
| content | dashboard | verkennen | FAQ | Help | html |
| startpagina | importeren | Bibliotheek | beheer | nieuw | Notebook |
| notebooks | pdf | trailer | Prijzen | profiel | zoeken |
| status | ondersteuning | test | | | |

Als u een van deze woorden als project-id probeert te gebruiken, geven de pop-ups **Nieuw project** en **Projectinstellingen** maken aan: 'Bibliotheek-id is een gereserveerde id'.

Omdat een project-id ook deel uitmaakt van de URL van een project, kan ad blocker-software het gebruik van bepaalde zoekwoorden blokkeren, zoals 'advertentie'. Gebruik in dergelijke gevallen een ander woord in de project-ID.

## <a name="import-a-project-from-github"></a>Een project importeren uit GitHub

U eenvoudig een volledige openbare GitHub repo importeren als een project met alle gegevens en *README.md* bestanden. Gebruik de opdracht **GitHub Repo uploaden,** geef de volgende details op in de pop-up en selecteer **Importeren:**

| Veld | Beschrijving |
| --- | --- |
| GitHub-opslagplaats | De naam van de bronrepository op github.com. Als u bijvoorbeeld de Jupyter-notitieblokken [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)voor Azure Cognitive Services wilt klonen bij , voert u 'Microsoft/cognitive-services-notebooks' in.  |
| Recursief klonen | GitHub-repositories kunnen meerdere onderliggende opslagplaatsen bevatten. Stel deze optie in als u de bovenliggende opslagplaats en al zijn kinderen wilt klonen. Omdat het mogelijk is voor een repository om veel kinderen te hebben, laat deze optie duidelijk, tenzij je weet dat je het nodig hebt. |
| Projectnaam | Een vriendelijke naam voor uw project die Azure Notebooks gebruikt voor weergavedoeleinden. |
| Project-id | Een aangepaste id die onderdeel wordt van de URL die `https://notebooks.azure.com/<user_id>/projects/<project_id>`u gebruikt om een project te delen (het formulier is). Deze id kan alleen letters, cijfers en koppeltekens gebruiken, is beperkt tot 30 tekens en kan geen [gereserveerde project-ID](#reserved-project-ids)zijn. Als u niet zeker weet wat u moet gebruiken, moet een gemeenschappelijke conventie een kleine versie van uw projectnaam gebruiken waarbij spaties worden omgezet in koppeltekens, zoals 'mijn-notebook-project' (afgekapt indien nodig om aan de lengtelimiet te voldoen). |
| Public | Als deze optie is ingesteld, kan iedereen met de koppeling toegang krijgen tot het project. Wanneer u een privéproject maakt, moet u deze optie wissen. |

Het importeren van een opslagplaats van GitHub importeert ook zijn geschiedenis. U standaard Git-opdrachten van de terminal gebruiken om nieuwe wijzigingen door te voeren, wijzigingen van GitHub te halen, enzovoort.

## <a name="clone-a-project"></a>Een project klonen

Klonen maakt een kopie van een bestaand project in uw eigen account, waar u vervolgens een notitieblok of ander bestand in het project uitvoeren en wijzigen. U ook klonen gebruiken om kopieën te maken van uw eigen projecten waarin u experimenten of ander werk doet zonder het oorspronkelijke project te verstoren.

Een project klonen:

1. Klik op het dashboard **Mijn projecten** met de rechtermuisknop op het gewenste project en selecteer **Clone** (sneltoets: c).

    ![Opdracht Klonen in het menu projectcontext](media/clone-command.png)

1. Voer in de pop-up **Clone Project** een naam en id voor de kloon in en geef op of de kloon openbaar is. Deze instellingen zijn hetzelfde als voor een [nieuw project.](#create-a-new-project)

    ![Pop-up van Kloonproject](media/clone-project.png)

1. Nadat u de **knop Klonen** hebt geselecteerd, navigeert Azure Notebooks rechtstreeks naar de kopie.

## <a name="next-steps"></a>Volgende stappen

- [Voorbeelden van notebooks verkennen](azure-notebooks-samples.md)
- [Hoe: Projecten configureren en beheren](configure-manage-azure-notebooks-projects.md)
- [Hoe: pakketten installeren vanuit een notitieblok](install-packages-jupyter-notebook.md)
- [Hoe: Een diavoorstelling presenteren](present-jupyter-notebooks-slideshow.md)
- [Hoe: Werken met gegevensbestanden](work-with-project-data-files.md)
- [Hoe: Toegang tot gegevensbronnen](access-data-resources-jupyter-notebooks.md)
- [How to: Azure Machine Learning gebruiken](use-machine-learning-services-jupyter-notebooks.md)
