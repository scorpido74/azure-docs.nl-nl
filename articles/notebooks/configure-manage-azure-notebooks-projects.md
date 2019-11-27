---
title: Configureren en beheren van Azure Notebook-projecten
description: Over het beheren van de metagegevens van het project, project-bestanden, van het project-omgeving en installatiestappen via de gebruikersinterface van Azure-notitieblokken en de directe terminal toegang.
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: 56c265122894412e79b3d5a7b256964c49ab81a6
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277648"
---
# <a name="manage-and-configure-projects"></a>Projecten beheren en configureren

Een project in notitieblokken van Azure is in feite een configuratie van de onderliggende virtuele Linux-machine waarin Jupyter-notebooks uitgevoerd, samen met een map en de beschrijvende metagegevens. Het projectdashboard in notitieblokken van Azure kunt u bestanden beheren en configureren van het project kenmerken:

- De compute-laag waarop het project wordt uitgevoerd. Dit kan de gratis laag of een virtuele machine van Azure zijn.
- Meta gegevens van het project, met een naam, beschrijving, een id die wordt gebruikt bij het delen van het project en of het project openbaar of privé is.
- De notebook-, gegevens-en andere bestanden van het project, die u als elk ander bestands systeem beheert.
- De omgeving van een project, die u via opstart scripts of rechtstreeks via de Terminal beheert.
- Logboeken, die u via de Terminal kunt openen.

> [!Note]
> De functies voor beheer en configuratie die hier worden beschreven, zijn alleen beschikbaar voor de project eigenaar die in eerste instantie het project heeft gemaakt. U kunt het project echter klonen in uw eigen account, in welk geval u de eigenaar wordt en het project naar wens kunt configureren.

Azure-notitieblokken wordt de onderliggende virtuele machine gestart wanneer u een laptop of een ander bestand uitvoeren. De server automatisch bestanden worden opgeslagen en worden afgesloten na 60 minuten van inactiviteit. U kunt de server ook op elk gewenst moment stoppen met de opdracht **shutdown** (sneltoets: h).

## <a name="compute-tier"></a>Compute-laag

Standaard worden projecten uitgevoerd op de **gratis Compute** -laag. Dit is beperkt tot 4 GB geheugen en 1 GB aan gegevens om misbruik te voor komen. U kunt deze beperkingen overs Laan en de reken kracht verhogen met behulp van een andere virtuele machine die u in een Azure-abonnement hebt ingericht. Zie [How to use data Science virtual machines](use-data-science-virtual-machine.md)voor meer informatie.

## <a name="edit-project-metadata"></a>Project metagegevens bewerken

Selecteer in het project dashboard **project instellingen**en selecteer vervolgens het tabblad **informatie** , dat de meta gegevens van het project bevat, zoals beschreven in de volgende tabel. U kunt de metagegevens van het project op elk gewenst moment wijzigen.

| Instelling | Beschrijving |
| --- | --- |
| Projectnaam | Een beschrijvende naam voor uw project die gebruikmaakt van Azure-notitieblokken weer te geven. Bijvoorbeeld: 'Hello World in Python'. |
| Project-id | Een aangepaste id die deel van de URL die u gebruikt uitmaken voor het delen van een project. Deze ID mag alleen letters, cijfers en afbreek streepjes bevatten, is beperkt tot 30 tekens en kan geen [gereserveerde project-id](create-clone-jupyter-notebooks.md#reserved-project-ids)zijn. Als u niet zeker weet wat u wilt gebruiken, wordt een algemene conventie is het gebruik van een kleine versie van de projectnaam van uw waar spaties in afbreekstreepjes bevatten, zoals 'Mijn-notebook-project' (afgekapt indien nodig aanpassen aan de maximale lengte) zijn ingeschakeld. |
| Openbare project | Als is ingesteld, kan iedereen met de koppeling voor toegang tot het project. Wanneer u een privé-project maakt, moet u deze optie uitschakelen. |
| Klonen verbergen | Als is ingesteld, andere gebruikers kunnen een lijst met klonen die zijn gemaakt voor dit project niet zien. Klonen verbergen is handig voor projecten die worden gedeeld met veel mensen die geen deel uitmaken van dezelfde organisatie, zoals bij het gebruik van een notitieblok voor het geven van een klasse. |

> [!Important]
>
> Wijzigen van de project-ID ongeldig maken een koppeling maakt naar het project dat u hebt mogelijk eerder hebt gedeeld.

## <a name="manage-project-files"></a>Project-bestanden beheren

Het projectdashboard ziet u de inhoud van het project tot mappensysteem. U kunt verschillende opdrachten gebruiken voor het beheren van deze bestanden.

### <a name="create-new-files-and-folders"></a>Nieuwe bestanden en mappen maken

De **+ nieuwe** opdracht (sneltoets: n) maakt nieuwe bestanden of mappen. Wanneer u de opdracht, selecteert u eerst het type item te maken:

| Itemtype | Beschrijving | Opdrachtgedrag |
| --- | --- | --- |
| **Notitieblok** | Een Jupyter-notebook | Geeft een pop-upvenster waarin u de bestandsnaam en de taal van het notitieblok opgeeft. |
| **Map** | Een submap | Hiermee maakt u een bewerkingsveld met het in de lijst met bestanden van het project waarin u de naam van de map. |
| **Leeg bestand** | Een bestand waarin u alle inhoud, zoals tekst, gegevens, enzovoort kunt opslaan. | Maakt een bewerkingsveld in de lijst met bestanden van het project waarin u de bestandsnaam invoeren. |
| **Markdown** | Een Markdown-bestand. | Maakt een bewerkingsveld in de lijst met bestanden van het project waarin u de bestandsnaam invoeren. |

### <a name="upload-files"></a>Bestanden uploaden

De **Upload** opdracht biedt twee opties voor het importeren van gegevens van andere locaties: **van URL** en **van computer**. Zie [werken met gegevens bestanden in azure notebook-projecten](work-with-project-data-files.md)voor meer informatie.

### <a name="select-file-specific-commands"></a>Selecteer bestand-specifieke opdrachten

Elk item in de lijst met bestanden van het project bevat opdrachten via een snelmenu met de rechtermuisknop op:

![Opdrachten in het contextmenu van een bestand](media/project-file-commands.png)

| Opdracht | Sneltoets | Actie |
| --- | --- | --- |
| Voer | r (of klik op) | Voert een laptop-bestand. Andere bestandstypen zijn geopend voor weergave.  |
| Koppeling kopiëren | Y | Een koppeling naar het bestand naar het Klembord gekopieerd. |
| Uitvoeren in Jupyter-Lab | "J" | Voert een notitieblok in Jjupyterlab, dit is een meer op ontwikkelaars gerichte interface dan Jupyter normaal biedt. |
| Preview | p | Hiermee opent u een HTML-Preview-versie van het bestand. voor laptops is de Preview-versie een alleen-lezen weergave van de notebook. Zie de sectie [Preview](#preview) voor meer informatie. |
| Bestand bewerken | Ik | Hiermee opent het bestand voor het bewerken van. |
| Download | d | Downloadt een zip-bestand dat het bestand of de inhoud van een map bevat. |
| Naam wijzigen | a | Wordt u gevraagd om een nieuwe naam voor het bestand of map. |
| Verwijderen | x | Wordt om bevestiging wordt gevraagd, en vervolgens wordt het bestand permanent verwijderd uit het project. Verwijderingen kunnen niet ongedaan worden gemaakt. |
| Verplaatsen | m | Een bestand is verplaatst naar een andere map in hetzelfde project. |

#### <a name="preview"></a>Preview

Een voorbeeld van een bestand of een laptop is een alleen-lezen weergave van de inhoud. uitvoeren van de notebook cellen is uitgeschakeld. Een Preview-versie wordt voor iedereen die een koppeling naar het bestand of de notebook heeft, maar is niet aangemeld bij Azure-notitieblokken weergegeven. Nadat u bent aangemeld, een gebruiker de notebook bij hun account kunt klonen of ze kunnen de notebook downloaden naar de lokale computer.

De voorbeeldpagina ondersteunt verschillende opdrachten in de werkbalk met sneltoetsen:

| Opdracht | Sneltoets | Actie |
| --- | --- | --- |
| Delen | s | Geeft het delen pop-upvenster van waaruit u kunt een koppeling verkrijgen, delen op sociale media, HTML-code verkrijgen voor het insluiten van en een e-mail verzenden. |
| Klonen | c  | Kloon de notebook voor uw account. |
| Voer | R | De notebook uitgevoerd als u mag doen. |
| Download | d | Een kopie van de notebook downloadt. |

## <a name="configure-the-project-environment"></a>De project-omgeving configureren

Er zijn drie manieren om te configureren van de omgeving van de onderliggende virtuele machine waarin uw laptops worden uitgevoerd:

- Een eenmalige Initialisatiescript voor lidddatabase van opnemen
- Instellingen van de omgeving van het project gebruiken om op te geven van de installatiestappen uit
- Toegang tot de virtuele machine via een terminal.

Alle vormen van projectconfiguratie worden toegepast wanneer de virtuele machine wordt gestart, en dus is van invloed op alle laptops binnen het project.

### <a name="one-time-initialization-script"></a>Eenmalige Initialisatiescript voor lidddatabase van

De eerste keer dat Azure Notebooks een server voor het project maakt, wordt gezocht naar een bestand in het project met de naam *aznbsetup.sh*. Als dit bestand aanwezig is, wordt het door Azure Notebooks uitgevoerd. De uitvoer van het script wordt opgeslagen in de projectmap als *. aznbsetup. log*.

### <a name="environment-setup-steps"></a>Instellingsstappen omgeving

Instellingen van de omgeving van het project kunt u afzonderlijke stappen die u configureert u de omgeving maken.

Selecteer in het project dashboard **project instellingen**en selecteer vervolgens het tabblad **omgeving** waarin u de installatie stappen voor het project toevoegt, verwijdert en wijzigt:

![Project instellingen pop-upvenster met tabblad omgeving geselecteerd](media/project-settings-environment-steps.png)

Als u een stap wilt toevoegen, selecteert u eerst **+ toevoegen**en selecteert u vervolgens een stap type in de vervolg keuzelijst **bewerking** :

![Bewerking de verkeersselector voor op een nieuwe omgeving setup stap](media/project-settings-environment-details.png)

De informatie die u vervolgens projecteren, is afhankelijk van het type bewerking dat u hebt gekozen:

- **Requirements. txt**: Selecteer in de tweede vervolg keuzelijst het bestand *Requirements. txt* dat zich al in het project bevermeldt. Selecteer vervolgens een Python-versie van de derde vervolgkeuzelijst die wordt weergegeven. Met het bestand *Requirements. txt* Azure Notebooks wordt `pip install -r` uitgevoerd met het bestand *Requirements. txt* bij het starten van een notebook server. U hoeft niet te pakketten uit binnen de notebook zelf expliciet te installeren.

- **Shell script**: Selecteer in de tweede vervolg keuzelijst een bash-shell script in het project (meestal een bestand met de extensie *. sh* ) dat opdrachten bevat die u wilt uitvoeren om de omgeving te initialiseren.

- **Environment. yml**: Selecteer in de tweede vervolg keuzelijst een *omgeving. yml* -bestand voor python-projecten met een Conda-omgeving.

Wanneer u klaar bent met het toevoegen van stappen, selecteert u **Opslaan**.

### <a name="use-the-terminal"></a>Gebruik de terminal

In het project dashboard wordt met de opdracht **Terminal** een Linux-terminal geopend waarmee u rechtstreeks toegang krijgt tot de server. In de terminal kunt u downloaden van gegevens, bewerken of beheren van bestanden, processen controleren en zelfs gebruik hulpprogramma's zoals vi en nano.

> [!Note]
> Hebt u opstartscripts in de omgeving van uw project, is het mogelijk dat weergegeven een bericht weergegeven dat aangeeft dat de installatie nog steeds bezig de terminal te openen.

U kunt een standaard Linux-opdrachten in de terminal op te geven. U kunt `ls` ook gebruiken in de basismap om de verschillende omgevingen te zien die zich op de virtuele machine bevinden, zoals *anaconda2_501*, *anaconda3_420*, *anaconda3_501*, *IfSharp*en *R*, samen met een *projectmap* die het project bevat:

![Project-terminal in Azure-notitieblokken](media/project-terminal.png)

Als u wilt invloed hebben op een specifieke omgeving, wijzig de mappen eerst naar die map omgeving.

Voor de python-omgevingen kunt u `pip` en `conda` vinden in de map *bin* van elke omgeving. U kunt ook ingebouwde aliassen gebruiken voor de omgevingen:

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

Wijzigingen op de server zijn alleen van toepassing op de huidige sessie, behalve voor bestanden en mappen die u in de *projectmap* zelf maakt. Het bewerken van een bestand in de projectmap wordt bijvoorbeeld persistent gemaakt tussen sessies, maar pakketten met `pip install` niet.

> [!Note]
> Als u `python` of `python3`gebruikt, roept u de door het systeem geïnstalleerde versies van python aan, die niet worden gebruikt voor notebooks. U hebt geen machtigingen voor bewerkingen als `pip install` van. Zorg er dus voor dat u de versie-specifieke aliassen gebruikt.

## <a name="access-notebook-logs"></a>Logboeken van de notebook openen

Als u problemen ondervindt bij het uitvoeren van een notebook, wordt de uitvoer van Jupyter opgeslagen in een map met de naam *. nb. log*. U kunt deze logboeken openen via de opdracht **Terminal** of het project dashboard.

Doorgaans wanneer u Jupyter lokaal uitvoert u mogelijk hebt gestart vanuit een terminal-venster. In het terminalvenster ziet u uitvoer zoals de status van de kernel.

Als u zich aanmeldt, gebruik de volgende opdracht in de terminal:

```bash
cat .nb.log
```

U kunt ook de opdracht gebruiken vanaf een lege codecel in een Python-notebook:

```bash
!cat .nb.log
```

## <a name="next-steps"></a>Volgende stappen

- [Procedure: werken met Project-gegevens bestanden](work-with-project-data-files.md)
- [Toegang tot Cloud gegevens in een notitie blok](access-data-resources-jupyter-notebooks.md)
