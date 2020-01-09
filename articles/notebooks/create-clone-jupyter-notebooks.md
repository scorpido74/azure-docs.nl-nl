---
title: Jupyter-notebooks maken en klonen-Azure Notebooks preview
description: Azure Notebooks preview-projecten een verzameling van notitie blokken en gerelateerde bestanden beheren, die u kunt maken van een nieuwe of een kloon vanuit een andere bron.
ms.topic: how-to
ms.date: 02/25/2019
ms.openlocfilehash: b29ff336c09a3bbf05a57c8a3a503b1875b76e54
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646242"
---
# <a name="create-and-clone-projects-in-azure-notebooks-preview"></a>Projecten maken en klonen in Azure Notebooks preview

Azure-notitieblokken organiseert uw Jupyter-notebooks en de bijbehorende bestanden in logische groepen met de naam *projecten*. U een project eerst als een container maken en vervolgens maakt of een of meer notitieblokken in een map naast andere projectbestanden klonen. (Dit proces wordt geïllustreerd in de [zelfstudie](tutorial-create-run-jupyter-notebook.md).)

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Een project onderhoudt ook metagegevens en andere configuratie-instellingen die invloed hebben op de server op welke notitieblokken uitvoeren, met inbegrip van aangepaste installatiestappen uit en installatie van het pakket. Zie voor meer informatie, [beheren en configureren van projecten](configure-manage-azure-notebooks-projects.md).

## <a name="use-the-my-projects-dashboard"></a>Gebruik het dashboard Mijn projecten

Uw **Mijn projecten** dashboard op `https://notebooks.azure.com/<userID>/projects` is waar u weergeven, beheren en projecten maken:

[![dash board van mijn projecten in Azure Notebooks](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

Wat u op het dashboard kunt doen, is afhankelijk van of u bent aangemeld met het account dat eigenaar is van de gebruikers-ID:

| Opdracht | Beschikbaar voor | Beschrijving |
| --- | --- | --- |
| **Uitvoeren** | Eigenaar | Start de projectserver en de projectmap in Jupyter geopend. (Meer over het algemeen u eerst in een projectmap navigeren en start u een laptop van daaruit.) |
| **Downloaden** | Iedereen | Een kopie van het geselecteerde project gedownload als een ZIP-bestand. |
| **Delen** | Iedereen | Het delen pop-upvenster waarmee u kunt verkrijgen van een URL naar een geselecteerde project, delen op sociale media, stuurt u een e-mail met de URL en verkrijgen van HTML- of Markdown-code voor met een badge 'notebook starten' wordt weergegeven (Zie [verkrijgen van een badge starten](#obtain-a-launch-badge)) met de URL. |
| **Verwijderen** | Eigenaar | Hiermee verwijdert u het geselecteerde project. Deze bewerking kan niet ongedaan worden gemaakt. |
| **Terminal** | Eigenaar | Start de projectserver en vervolgens een nieuw browservenster geopend met de bash, terminal voor die server. |
| **+ Nieuw Project** | Eigenaar | Hiermee maakt u een nieuw project. Zie [een nieuw project maken](#create-a-new-project). |
| **GitHub opslag plaats uploaden** | Eigenaar | Een project importeert vanuit GitHub. [Een project importeren vanuit GitHub](#import-a-project-from-github). |
| **Kloon** | Iedereen | Een geselecteerde project worden gekopieerd naar uw eigen account. Vraagt u zich kunt aanmelden in als dit nog niet. Zie [klonen van een project](#clone-a-project). |

### <a name="obtain-a-launch-badge"></a>Verkrijgen van een badge starten

Wanneer u gebruikt de **Share** opdracht en selecteer de **insluiten** tabblad kunt u HTML-code of Markdown waarmee een badge 'notebook starten':

![Laptop-badge starten](https://notebooks.azure.com/launch.png)

Als u een Azure-notitieblokken-project hebt, kunt u een koppeling die wordt gekloond vanuit GitHub rechtstreeks met behulp van de volgende sjablonen, vervangen door de juiste gebruikersnaam en de namen van de opslagplaats:

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>Een nieuw project maken

Wanneer u gebruikt de **+ nieuw Project** opdracht, Azure-notitieblokken wordt weergegeven een **nieuw Project maken** pop-upvenster. Voer de volgende informatie in dit pop-upvenster en selecteer vervolgens **maken**:

| Veld | Beschrijving |
| --- | --- |
| Projectnaam | Een beschrijvende naam voor uw project die gebruikmaakt van Azure-notitieblokken weer te geven. Bijvoorbeeld ' mijn notitie blok-project '. |
| Project-id | Een aangepaste id die deel van de URL uitmaken die u gebruiken voor het delen van een project (het formulier is `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Deze ID mag alleen letters, cijfers en afbreek streepjes bevatten, is beperkt tot 30 tekens en kan geen [gereserveerde project-id](#reserved-project-ids)zijn. Als u niet zeker weet wat u wilt gebruiken, wordt een algemene conventie is het gebruik van een kleine versie van de projectnaam van uw waar spaties in afbreekstreepjes bevatten, zoals 'Mijn-notebook-project' (afgekapt indien nodig aanpassen aan de maximale lengte) zijn ingeschakeld. |
| Public | Als is ingesteld, kan iedereen met de koppeling voor toegang tot het project. Wanneer u een privé-project maakt, moet u deze optie uitschakelen. |
| Initialiseren van dit project met een Leesmij-bestand | Als instelt, maakt u een standaard *README.md* bestand in het project. Een *README.md* bestand is waar het bieden van documentatie voor uw project, indien gewenst. |

### <a name="reserved-project-ids"></a>Gereserveerde project-Id's

De volgende gereserveerde woorden kunnen niet worden gebruikt door zichzelf als project-Id's. Deze gereserveerde woorden kunnen echter worden gebruikt als onderdeel van langere project-Id's.

| | | | | | |
| --- | --- | --- | --- | --- | --- |
| Info | account | beheer | api | blog | leslokaal |
| content | dashboard | verkennen | faq | Help | html |
| start | importeren | tagbibliotheek | beheer | nieuw | notitieblok |
| notebooks | pdf | preview | koers | profile | zoeken |
| status | ondersteuning | test | | | |

Als u een van deze woorden als project-ID probeert te gebruiken, worden de pop-up **nieuwe project** -en **project instellingen** maken popups, ' bibliotheek-id is een gereserveerde id '.

Omdat een project-ID ook deel uitmaakt van de URL van een project, kan het gebruik van bepaalde tref woorden, zoals ' advertisements ', worden geblokkeerd door de software van ad blocker. In dergelijke gevallen gebruikt u een ander woord in de project-ID.

## <a name="import-a-project-from-github"></a>Een project importeren vanuit GitHub

U kunt eenvoudig een volledige openbare GitHub-opslagplaats importeren als een project met inbegrip van alle gegevens en *README.md* bestanden. Gebruik de **GitHub-opslagplaats uploaden** opdracht, geef de volgende informatie in het pop-upvenster op en selecteer vervolgens **importeren**:

| Veld | Beschrijving |
| --- | --- |
| GitHub-opslagplaats | De naam van de opslagplaats op github.com. Bijvoorbeeld, om te klonen van de Jupyter-notebooks voor Azure Cognitive Services op [ https://github.com/Microsoft/cognitive-services-notebooks ](https://github.com/Microsoft/cognitive-services-notebooks), voer ' Microsoft/cognitieve-services-notitieblokken'.  |
| Kloon-recursief | GitHub-opslagplaatsen kunnen meerdere onderliggende opslagplaatsen bevatten. Stel deze optie als u wilt klonen van de bovenliggende of hoofdopslagplaats en alle onderliggende items. Omdat het is mogelijk dat een opslagplaats te veel onderliggende elementen hebben, laat u deze optie wissen tenzij u zeker weet dat u ze nodig hebt. |
| Projectnaam | Een beschrijvende naam voor uw project die gebruikmaakt van Azure-notitieblokken weer te geven. |
| Project-id | Een aangepaste id die deel van de URL uitmaken die u gebruiken voor het delen van een project (het formulier is `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Deze ID mag alleen letters, cijfers en afbreek streepjes bevatten, is beperkt tot 30 tekens en kan geen [gereserveerde project-id](#reserved-project-ids)zijn. Als u niet zeker weet wat u wilt gebruiken, wordt een algemene conventie is het gebruik van een kleine versie van de projectnaam van uw waar spaties in afbreekstreepjes bevatten, zoals 'Mijn-notebook-project' (afgekapt indien nodig aanpassen aan de maximale lengte) zijn ingeschakeld. |
| Public | Als is ingesteld, kan iedereen met de koppeling voor toegang tot het project. Wanneer u een privé-project maakt, moet u deze optie uitschakelen. |

Importeren van een opslagplaats van GitHub importeert bijbehorende geschiedenis ook. U kunt standard Git-opdrachten uit vanaf de terminal naar de nieuwe wijzigingen doorvoeren, pull-wijzigingen vanuit GitHub, enzovoort.

## <a name="clone-a-project"></a>Een project klonen

Klonen, maakt een kopie van een bestaand project in uw eigen account, waar u kunt uitvoeren en wijzigen van uw laptop of een ander bestand in het project. U kunt klonen ook gebruiken om kopieën van uw eigen projecten waarin u hoeft u geen experimenten of voor andere werkzaamheden te verstoren het oorspronkelijke project te maken.

Voor het klonen van een project:

1. Op de **Mijn projecten** -dashboard met de rechtermuisknop op het gewenste project en selecteer **kloon** (sneltoets: c).

    ![Opdracht in het contextmenu project klonen](media/clone-command.png)

1. In de **kloon Project** pop-upvenster Voer een naam en de ID voor de kloon, en opgeven of de kloon openbaar is. Deze instellingen zijn hetzelfde als voor een [nieuw project](#create-a-new-project).

    ![Kloon Project pop-upvenster](media/clone-project.png)

1. Nadat u hebt geselecteerd de **kloon** knop,-laptops Azure gaat rechtstreeks naar de kopie.

## <a name="next-steps"></a>Volgende stappen

- [Voorbeelden van notebooks verkennen](azure-notebooks-samples.md)
- [Hoe: configureren en beheren van projecten](configure-manage-azure-notebooks-projects.md)
- [Hoe: installatie van pakketten van binnen een laptop](install-packages-jupyter-notebook.md)
- [Hoe: een diavoorstelling](present-jupyter-notebooks-slideshow.md)
- [Hoe: werken met bestanden](work-with-project-data-files.md)
- [Hoe: toegang tot gegevensbronnen](access-data-resources-jupyter-notebooks.md)
- [Procedure: Azure Machine Learning gebruiken](use-machine-learning-services-jupyter-notebooks.md)
