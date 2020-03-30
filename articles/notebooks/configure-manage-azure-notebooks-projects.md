---
title: Azure-notitieblokvoorbeeld configureren en beheren
description: Meer informatie over het beheren van projectmetagegevens, projectbestanden, de omgeving van het project en installatiestappen via zowel de gebruikersinterface van Azure Notebooks als directe terminaltoegang.
ms.topic: how-to
ms.date: 02/28/2020
ms.openlocfilehash: 1674effda2cb9bda45f49c91ca618225b0a75f0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280597"
---
# <a name="manage-and-configure-projects-in-azure-notebooks-preview"></a><a id="manage-and-configure-projects" />Projecten beheren en configureren in De Proefversie van Azure Notebooks

Een project in Azure Notebooks Preview is in wezen een configuratie van de onderliggende Linux virtuele machine waarin Jupyter notebooks draaien, samen met een bestandsmap en beschrijvende metadata. 

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Met het projectdashboard in Azure Notebooks u bestanden beheren en op een andere manier de kenmerken van het project configureren:

- De rekenlaag waarop het project wordt uitgevoerd, wat de gratis laag of een virtuele Azure-machine kan zijn.
- Projectmetagegevens, waaronder een naam, beschrijving, een id die wordt gebruikt bij het delen van het project en of het project openbaar of privé is.
- Het notitieblok, de gegevens en andere bestanden van het project, die u net als elk ander bestandssysteem beheert.
- De omgeving van een project, die u beheert via opstartscripts of rechtstreeks via de terminal.
- Logs, die u toegang via de terminal.

> [!Note]
> De hier beschreven beheer- en configuratiefuncties zijn alleen beschikbaar voor de projecteigenaar die het project in eerste instantie heeft gemaakt. U het project echter in uw eigen account klonen, in welk geval u eigenaar wordt en het project naar wens configureren.

Azure Notebooks start de onderliggende virtuele machine wanneer u een notitieblok of ander bestand uitvoert. De server slaat bestanden automatisch op en wordt afgesloten na 60 minuten inactiviteit. U de server ook op elk gewenst moment stoppen met de opdracht **Afsluiten** (sneltoets: h).

## <a name="compute-tier"></a>Rekenlaag

Standaard worden projecten uitgevoerd op de **Free Compute-laag,** die beperkt is tot 4 GB geheugen en 1 GB aan gegevens om misbruik te voorkomen. U deze beperkingen omzeilen en de rekenkracht verhogen door een andere virtuele machine te gebruiken die u hebt ingericht in een Azure-abonnement. Zie [Virtuele machines voor Data Science gebruiken](use-data-science-virtual-machine.md)voor meer informatie.

## <a name="edit-project-metadata"></a>Projectmetagegevens bewerken

Selecteer **projectinstellingen**in het projectdashboard en selecteer vervolgens het tabblad **Informatie,** dat de metagegevens van het project bevat zoals beschreven in de volgende tabel. U projectmetagegevens op elk gewenst moment wijzigen.

| Instelling | Beschrijving |
| --- | --- |
| Projectnaam | Een vriendelijke naam voor uw project die Azure Notebooks gebruikt voor weergavedoeleinden. Bijvoorbeeld "Hello World in Python". |
| Project-id | Een aangepaste id die onderdeel wordt van de URL die u gebruikt om een project te delen. Deze id kan alleen letters, cijfers en koppeltekens gebruiken, is beperkt tot 30 tekens en kan geen [gereserveerde project-ID](create-clone-jupyter-notebooks.md#reserved-project-ids)zijn. Als u niet zeker weet wat u moet gebruiken, moet een gemeenschappelijke conventie een kleine versie van uw projectnaam gebruiken waarbij spaties worden omgezet in koppeltekens, zoals 'mijn-notebook-project' (afgekapt indien nodig om aan de lengtelimiet te voldoen). |
| Openbaar project | Als deze optie is ingesteld, kan iedereen met de koppeling toegang krijgen tot het project. Wanneer u een privéproject maakt, moet u deze optie wissen. |
| Klonen verbergen | Als deze is ingesteld, kunnen andere gebruikers geen lijst met klonen zien die voor dit project zijn gemaakt. Klonen verbergen is handig voor projecten die worden gedeeld met veel mensen die geen deel uitmaken van dezelfde organisatie, zoals bij het gebruik van een notitieblok voor het onderwijzen van een klas. |

> [!Important]
>
> Als u de project-id wijzigt, worden alle koppelingen naar het project die u eerder hebt gedeeld, ongeldig gemaakt.

## <a name="manage-project-files"></a>Projectbestanden beheren

Het projectdashboard toont de inhoud van het mapsysteem van het project. U verschillende opdrachten gebruiken om deze bestanden te beheren.

### <a name="create-new-files-and-folders"></a>Nieuwe bestanden en mappen maken

Met de opdracht **+ Nieuw** (sneltoets: n) worden nieuwe bestanden of mappen gemaakt. Wanneer u de opdracht gebruikt, selecteert u eerst het type item dat u wilt maken:

| Objecttype | Beschrijving | Opdrachtgedrag |
| --- | --- | --- |
| **Notebook** | Een Jupyter-laptop | Hiermee wordt een pop-up weergegeven waarin u de bestandsnaam en taal van het notitieblok opgeeft. |
| **Map** | Een submap | Hiermee maakt u een bewerkingsveld in de bestandslijst van het project waarin u de mapnaam invoert. |
| **Leeg bestand** | Een bestand waarin u alle inhoud zoals tekst, gegevens, enz. | Hiermee maakt u een bewerkingsveld in de bestandslijst van het project waarin u de bestandsnaam invoert. |
| **Markdown** | Een Markdown-bestand. | Hiermee maakt u een bewerkingsveld in de bestandslijst van het project waarin u de bestandsnaam invoert. |

### <a name="upload-files"></a>Bestanden uploaden

De opdracht **Uploaden** biedt twee opties om gegevens van andere locaties te importeren: **van URL** en **van computer.** Zie [Werken met gegevensbestanden in Azure Notebook-projecten](work-with-project-data-files.md)voor meer informatie.

### <a name="select-file-specific-commands"></a>Bestandsspecifieke opdrachten selecteren

Elk item in de bestandslijst van het project biedt opdrachten via een contextmenu met de rechtermuisknop:

![Opdrachten in een bestandscontextmenu](media/project-file-commands.png)

| Opdracht | Sneltoets | Actie |
| --- | --- | --- |
| Voer | r (of klik op) | Met een notitieblokbestand wordt een notitieblokbestand uitgevoerd. Andere bestandstypen worden geopend om te bekijken.  |
| Koppeling kopiëren | y | Hiermee kopieert u een koppeling naar het bestand naar het klembord. |
| Run in Jupyter Lab | J | Loopt een notebook in JupyterLab, dat is een meer ontwikkelaar-georiënteerde interface dan Jupyter normaal biedt. |
| Preview | p | Hiermee opent u een HTML-voorbeeld van het bestand; voor notitieblokken is de preview een alleen-lezen weergave van het notitieblok. Zie de sectie [Voorbeeld voor](#preview) meer informatie. |
| Bestand bewerken | i | Hiermee opent u het bestand om te bewerken. |
| Download | d | Downloadt een zip-bestand met het bestand of de inhoud van een map. |
| Naam wijzigen | a | Vraagt om een nieuwe naam voor het bestand of de map. |
| Verwijderen | x | Vraagt om bevestiging en verwijdert het bestand vervolgens permanent uit het project. Verwijderingen kunnen niet ongedaan worden gemaakt. |
| Verplaatsen | m | Hiermee verplaatst u een bestand naar een andere map in hetzelfde project. |

#### <a name="preview"></a>Preview

Een voorbeeld van een bestand of notitieblok is een alleen-lezen weergave van de inhoud; het uitvoeren van notitieblokcellen is uitgeschakeld. Er wordt een voorbeeld weergegeven aan iedereen die een koppeling heeft naar het bestand of notitieblok, maar zich niet heeft aangemeld bij Azure Notebooks. Eenmaal aangemeld, kan een gebruiker het notitieblok naar zijn eigen account klonen of het notitieblok downloaden naar zijn lokale computer.

De voorbeeldpagina ondersteunt verschillende werkbalkopdrachten met sneltoetsen:

| Opdracht | Sneltoets | Actie |
| --- | --- | --- |
| Delen | s | Hiermee wordt de pop-up voor delen weergegeven waaruit u een koppeling verkrijgen, delen met sociale media, HTML verkrijgen voor inbedding en een e-mail verzenden. |
| Klonen | c  | Kloon het notitieblok naar uw account. |
| Voer | r | Draait het notitieblok als je dit mag doen. |
| Download | d | Downloadt een kopie van het notitieblok. |

## <a name="configure-the-project-environment"></a>De projectomgeving configureren

Er zijn drie manieren om de omgeving van de onderliggende virtuele machine te configureren waarin uw notitieblokken worden uitgevoerd:

- Een eenmalig initialisatiescript opnemen
- De omgevingsinstellingen van het project gebruiken om installatiestappen op te geven
- Toegang tot de virtuele machine via een terminal.

Alle vormen van projectconfiguratie worden toegepast wanneer de virtuele machine wordt gestart en heeft dus invloed op alle notitieblokken binnen het project.

### <a name="one-time-initialization-script"></a>Eenmalige initialisatiescript

De eerste keer Azure Notebooks maakt een server voor het project, het zoekt naar een bestand in het project genaamd *aznbsetup.sh*. Als dit bestand aanwezig is, worden het in Azure Notebooks uitgevoerd. De uitvoer van het script wordt opgeslagen in uw projectmap als *.aznbsetup.log*.

### <a name="environment-setup-steps"></a>Stappen voor het instellen van omgevingen

U de omgevingsinstellingen van het project gebruiken om afzonderlijke stappen te maken die de omgeving configureren.

Selecteer **projectinstellingen**in het projectdashboard en selecteer vervolgens het tabblad **Omgeving** waarin u de installatiestappen voor het project toevoegt, verwijdert en wijzigt:

![Pop-up projectinstellingen met tabblad Omgeving geselecteerd](media/project-settings-environment-steps.png)

Als u een stap wilt toevoegen, selecteert u **eerst + Toevoegen**en selecteert u vervolgens een staptype in de vervolgkeuzelijst **Bewerking:**

![Bewerkingskiezer voor een nieuwe stap voor het instellen van omgeving](media/project-settings-environment-details.png)

De informatie die u vervolgens projecteert, is afhankelijk van het type bewerking dat u hebt gekozen:

- **Requirements.txt**: Selecteer in de tweede vervolgkeuzelijst een *requirements.txt-bestand* dat al in het project zit. Selecteer vervolgens een Python-versie in de derde vervolgkeuzelijst die wordt weergegeven. Met behulp van een *requirements.txt-bestand* worden Azure Notebooks uitgevoerd `pip install -r` met het bestand *requirements.txt* bij het starten van een notitieblokserver. U hoeft pakketten niet expliciet te installeren vanuit het notitieblok zelf.

- **Shell-script:** Selecteer in de tweede vervolgkeuzelijst een script voor bashshell in het project (meestal een bestand met de *.sh-extensie)* dat alle opdrachten bevat die u wilt uitvoeren om de omgeving te initialiseren.

- **Environment.yml**: Selecteer in de tweede vervolgkeuzelijst een *bestand 'environments.yml* voor Python-projecten met behulp van een conda-omgeving.

   > [!WARNING]
   > Aangezien dit een voorbeeldservice in ontwikkeling is, is `Environment.yml` er momenteel een bekend probleem waarbij de instelling niet wordt toegepast op uw project zoals verwacht. Het project en de Jupyter-notitieblokken binnen laden momenteel het opgegeven omgevingsbestand niet.

Wanneer u klaar bent met het toevoegen van stappen, selecteert u **Opslaan**.

### <a name="use-the-terminal"></a>Gebruik de terminal

Op het projectdashboard opent de **opdracht Terminal** een Linux-terminal die u direct toegang geeft tot de server. Binnen de terminal u gegevens downloaden, bestanden bewerken of beheren, processen inspecteren en zelfs tools zoals vi en nano gebruiken.

> [!Note]
> Als u opstartscripts in de omgeving van uw project hebt, kan het openen van de terminal een bericht weergeven dat de installatie nog in volle gang is.

U alle standaard Linux commando's in de terminal. U `ls` in de thuismap ook de verschillende omgevingen op de virtuele machine bekijken, zoals *anaconda2_501,* *anaconda3_420,* *anaconda3_501,* *IfSharp*en *R,* samen met een *projectmap* die het project bevat:

![Projectterminal in Azure-notitieblokken](media/project-terminal.png)

Als u een specifieke omgeving wilt beïnvloeden, wijzigt u eerst mappen in die omgevingsmap.

Voor de Python-omgevingen `pip` `conda` kunt u de map van elke omgeving vinden en in *de opslaglocatiemap.* U ook ingebouwde aliassen gebruiken voor de omgevingen:

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

Wijzigingen die in de server zijn aangebracht, zijn alleen van toepassing op de huidige sessie, behalve op bestanden en mappen die u in de *projectmap* zelf maakt. Het bewerken van een bestand in de projectmap blijft bijvoorbeeld `pip install` bestaan tussen sessies, maar pakketten met dat zijn niet.

> [!Note]
> Als u `python` `python3`de door het systeem geïnstalleerde versies van Python gebruikt of aanroept, worden deze niet gebruikt voor notitieblokken. U hebt ook geen machtigingen `pip install` voor bewerkingen zoals bewerkingen, dus zorg ervoor dat u de versiespecifieke aliassen gebruikt.

## <a name="access-notebook-logs"></a>Notitiebloklogboeken openen

Als u problemen ondervindt bij het uitvoeren van een notitieblok, wordt de uitvoer van Jupyter opgeslagen in een map met de naam *.nb.log*. U deze logboeken openen via de opdracht **Terminal** of het projectdashboard.

Meestal wanneer u Jupyter lokaal draait, u het vanuit een terminalvenster zijn begonnen. Het terminalvenster toont uitvoer zoals kernelstatus.

Als u logboeken wilt weergeven, gebruikt u de volgende opdracht in de terminal:

```bash
cat .nb.log
```

U de opdracht ook gebruiken vanuit een codecel in een Python-notitieblok:

```bash
!cat .nb.log
```

## <a name="next-steps"></a>Volgende stappen

- [Hoe: Werken met projectgegevensbestanden](work-with-project-data-files.md)
- [Toegang tot cloudgegevens in een notebook](access-data-resources-jupyter-notebooks.md)
