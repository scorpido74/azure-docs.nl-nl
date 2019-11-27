---
title: Maken en klonen Jupyter-notebooks in Azure
description: Azure notitieblokken projecten beheren van een verzameling van notitieblokken en -gerelateerde bestanden, die u kunt nieuwe maken of klonen vanaf een andere bron.
ms.topic: article
ms.date: 02/25/2019
ms.openlocfilehash: fc5425312637710f0b9f94493b8cfb4a48582236
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277580"
---
# <a name="create-and-clone-projects"></a>Projecten maken en klonen

Azure Notebooks worden uw Jupyter-notebooks en gerelateerde bestanden ingedeeld in logische groepen die *projecten*worden genoemd. U een project eerst als een container maken en vervolgens maakt of een of meer notitieblokken in een map naast andere projectbestanden klonen. (Dit proces wordt in de [zelf studie](tutorial-create-run-jupyter-notebook.md)gedemonstreerd.)

Een project onderhoudt ook metagegevens en andere configuratie-instellingen die invloed hebben op de server op welke notitieblokken uitvoeren, met inbegrip van aangepaste installatiestappen uit en installatie van het pakket. Zie [projecten beheren en configureren](configure-manage-azure-notebooks-projects.md)voor meer informatie.

## <a name="use-the-my-projects-dashboard"></a>Gebruik het dashboard Mijn projecten

In het dash board **Mijn projecten** op `https://notebooks.azure.com/<userID>/projects` ziet u hoe u projecten bekijkt, beheert en maakt:

[![dash board van mijn projecten in Azure Notebooks](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

Wat u op het dashboard kunt doen, is afhankelijk van of u bent aangemeld met het account dat eigenaar is van de gebruikers-ID:

| Opdracht | Beschikbaar voor | Beschrijving |
| --- | --- | --- |
| **Uitvoeringsrun** | Eigenaar | Start de projectserver en de projectmap in Jupyter geopend. (Meer over het algemeen u eerst in een projectmap navigeren en start u een laptop van daaruit.) |
| **Downloaden** | Iedereen | Een kopie van het geselecteerde project gedownload als een ZIP-bestand. |
| **Delen** | Iedereen | Hier wordt de pop-up weer gegeven waarmee u een URL kunt verkrijgen voor een geselecteerd project, delen naar sociale media, een e-mail verzendt met de URL en de code voor HTML of prijs verlaging kunt verkrijgen voor met de badge ' Launch Notebook ' (Zie [een Launch badge verkrijgen](#obtain-a-launch-badge)) met de URL. |
| **Verwijderen** | Eigenaar | Hiermee verwijdert u het geselecteerde project. Deze bewerking kan niet ongedaan worden gemaakt. |
| **Terminal** | Eigenaar | Start de projectserver en vervolgens een nieuw browservenster geopend met de bash, terminal voor die server. |
| **+ Nieuw project** | Eigenaar | Hiermee maakt u een nieuw project. Zie [een nieuw project maken](#create-a-new-project). |
| **GitHub opslag plaats uploaden** | Eigenaar | Een project importeert vanuit GitHub. [Importeer een project uit github](#import-a-project-from-github). |
| **Kopiëren** | Iedereen | Een geselecteerde project worden gekopieerd naar uw eigen account. Vraagt u zich kunt aanmelden in als dit nog niet. Zie [een project klonen](#clone-a-project). |

### <a name="obtain-a-launch-badge"></a>Verkrijgen van een badge starten

Wanneer u de opdracht **delen** gebruikt en het tabblad **insluiten** selecteert, kunt u HTML-code of-prijs opwaarderen die een ' notitie blok starten ' badge maakt:

![Laptop-badge starten](https://notebooks.azure.com/launch.png)

Als u een Azure-notitieblokken-project hebt, kunt u een koppeling die wordt gekloond vanuit GitHub rechtstreeks met behulp van de volgende sjablonen, vervangen door de juiste gebruikersnaam en de namen van de opslagplaats:

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>Een nieuw project maken

Wanneer u de **+ New Project** -opdracht gebruikt, wordt er een pop-upvenster **nieuw project maken** Azure notebooks weer gegeven. Voer in dit pop-upvenster de volgende gegevens in en selecteer vervolgens **maken**:

| Veld | Beschrijving |
| --- | --- |
| Projectnaam | Een beschrijvende naam voor uw project die gebruikmaakt van Azure-notitieblokken weer te geven. Bijvoorbeeld ' mijn notitie blok-project '. |
| Project-id | Een aangepaste id die onderdeel wordt van de URL die u gebruikt om een project te delen (het formulier is `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Deze ID mag alleen letters, cijfers en afbreek streepjes bevatten, is beperkt tot 30 tekens en kan geen [gereserveerde project-id](#reserved-project-ids)zijn. Als u niet zeker weet wat u wilt gebruiken, wordt een algemene conventie is het gebruik van een kleine versie van de projectnaam van uw waar spaties in afbreekstreepjes bevatten, zoals 'Mijn-notebook-project' (afgekapt indien nodig aanpassen aan de maximale lengte) zijn ingeschakeld. |
| Public | Als is ingesteld, kan iedereen met de koppeling voor toegang tot het project. Wanneer u een privé-project maakt, moet u deze optie uitschakelen. |
| Initialiseren van dit project met een Leesmij-bestand | Als deze instelling is ingesteld, wordt in het project een standaard *README.MD* -bestand gemaakt. In een *README.MD* -bestand kunt u indien gewenst documentatie voor uw project opgeven. |

### <a name="reserved-project-ids"></a>Gereserveerde project-Id's

De volgende gereserveerde woorden kunnen niet worden gebruikt door zichzelf als project-Id's. Deze gereserveerde woorden kunnen echter worden gebruikt als onderdeel van langere project-Id's.

| | | | | | |
| --- | --- | --- | --- | --- | --- |
| wilt | account | beheer | api | blog | school |
| content | dashboard | Ontdek | faq | Help | html |
| mijn | wederinvoer | tagbibliotheek | beheer | nieuw | notitieblok |
| notebooks | pdf | preview | koers | profile | Opdracht |
| status | Voor | test | | | |

Als u een van deze woorden als project-ID probeert te gebruiken, worden de pop-up **nieuwe project** -en **project instellingen** maken popups, ' bibliotheek-id is een gereserveerde id '.

Omdat een project-ID ook deel uitmaakt van de URL van een project, kan het gebruik van bepaalde tref woorden, zoals ' advertisements ', worden geblokkeerd door de software van ad blocker. In dergelijke gevallen gebruikt u een ander woord in de project-ID.

## <a name="import-a-project-from-github"></a>Een project importeren vanuit GitHub

U kunt eenvoudig een volledige open bare GitHub-opslag plaats importeren als een project met inbegrip van alle gegevens-en *README.MD* -bestanden. Gebruik de **Upload github opslag plaats** opdracht, geef de volgende details op in het pop-upvenster en selecteer vervolgens **importeren**:

| Veld | Beschrijving |
| --- | --- |
| GitHub-opslagplaats | De naam van de opslagplaats op github.com. Als u bijvoorbeeld de Jupyter-notebooks voor Azure Cognitive Services op [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)wilt klonen, voert u ' micro soft/cognitieve-Services-Notebook ' in.  |
| Kloon-recursief | GitHub-opslagplaatsen kunnen meerdere onderliggende opslagplaatsen bevatten. Stel deze optie als u wilt klonen van de bovenliggende of hoofdopslagplaats en alle onderliggende items. Omdat het is mogelijk dat een opslagplaats te veel onderliggende elementen hebben, laat u deze optie wissen tenzij u zeker weet dat u ze nodig hebt. |
| Projectnaam | Een beschrijvende naam voor uw project die gebruikmaakt van Azure-notitieblokken weer te geven. |
| Project-id | Een aangepaste id die onderdeel wordt van de URL die u gebruikt om een project te delen (het formulier is `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Deze ID mag alleen letters, cijfers en afbreek streepjes bevatten, is beperkt tot 30 tekens en kan geen [gereserveerde project-id](#reserved-project-ids)zijn. Als u niet zeker weet wat u wilt gebruiken, wordt een algemene conventie is het gebruik van een kleine versie van de projectnaam van uw waar spaties in afbreekstreepjes bevatten, zoals 'Mijn-notebook-project' (afgekapt indien nodig aanpassen aan de maximale lengte) zijn ingeschakeld. |
| Public | Als is ingesteld, kan iedereen met de koppeling voor toegang tot het project. Wanneer u een privé-project maakt, moet u deze optie uitschakelen. |

Importeren van een opslagplaats van GitHub importeert bijbehorende geschiedenis ook. U kunt standard Git-opdrachten uit vanaf de terminal naar de nieuwe wijzigingen doorvoeren, pull-wijzigingen vanuit GitHub, enzovoort.

## <a name="clone-a-project"></a>Een project klonen

Klonen, maakt een kopie van een bestaand project in uw eigen account, waar u kunt uitvoeren en wijzigen van uw laptop of een ander bestand in het project. U kunt klonen ook gebruiken om kopieën van uw eigen projecten waarin u hoeft u geen experimenten of voor andere werkzaamheden te verstoren het oorspronkelijke project te maken.

Voor het klonen van een project:

1. Klik in het dash board **Mijn projecten** met de rechter muisknop op het gewenste project en selecteer **kloon** (sneltoets: c).

    ![Opdracht in het contextmenu project klonen](media/clone-command.png)

1. Voer in het menu **kloon project** de naam en id in voor de kloon en geef op of de kloon openbaar is. Deze instellingen zijn hetzelfde als voor een [Nieuw project](#create-a-new-project).

    ![Kloon Project pop-upvenster](media/clone-project.png)

1. Nadat u de knop **klonen** hebt geselecteerd, gaat Azure notebooks rechtstreeks naar de kopie.

## <a name="next-steps"></a>Volgende stappen

- [Voorbeelden van notebooks verkennen](azure-notebooks-samples.md)
- [Procedure: projecten configureren en beheren](configure-manage-azure-notebooks-projects.md)
- [Procedure: pakketten installeren vanuit een notitie blok](install-packages-jupyter-notebook.md)
- [Procedure: een diavoorstelling presen teren](present-jupyter-notebooks-slideshow.md)
- [Procedure: werken met gegevens bestanden](work-with-project-data-files.md)
- [Informatie over toegang tot gegevens bronnen](access-data-resources-jupyter-notebooks.md)
- [Procedure: Azure Machine Learning gebruiken](use-machine-learning-services-jupyter-notebooks.md)
