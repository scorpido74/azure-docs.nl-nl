---
title: Importeren en exporteren van gegevens met projecten met Azure-laptops
description: Hoe u gegevens overzetten naar een Azure-notitieblokken-project uit externe bronnen, en hoe u voor het exporteren van gegevens uit een project.
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: bd7ba27859e9d05c0d57c2f78b6449c2bc48ca33
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277387"
---
# <a name="work-with-data-files-in-azure-notebook-projects"></a>Werken met gegevensbestanden in Azure Notebook-projecten

Gegevens zijn de levensader van veel Jupyter notebooks, met name laptops die wordt gebruikt voor datatechnologie. Met Azure-Notebooks, kunt u eenvoudig importeren uit een groot aantal bronnen in een project en vervolgens met die gegevens van notitieblokken. U kunt ook laptops genereren van gegevens die zijn opgeslagen in het project, die u vervolgens voor gebruik elders downloaden kunt hebben.

De **gegevens** menu binnen een actieve notebook biedt ook **uploaden** en **downloaden** opdrachten die met bestanden in het project, evenals de tijdelijke bestanden voor de huidige werken laptop-sessie.

U kunt code in een notitieblok ook gebruiken rechtstreeks, toegang hebben tot een verscheidenheid aan gegevensbronnen met inbegrip van bestanden in een project. U kunt ook toegang tot willekeurige gegevens met behulp van opdrachten in een codecel. Omdat dergelijke gegevens worden opgeslagen in variabelen in de notebook-sessie, deze niet opgeslagen in het project, tenzij u code specifiek om projectbestanden te genereren.

Werken met code in de gegevens het is best binnen een actieve notebook zelf: voor dat doel, raadpleegt u de [aan met uw gegevens in Azure-notitieblokken voorbeeldnotitieblok](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb).

De rest van dit artikel biedt informatie over het niveau van het project bestandsbewerkingen.

## <a name="import-data"></a>Gegevens importeren

U kunt bestanden overbrengen naar een project van het projectdashboard of binnen een actieve laptop met behulp van de **gegevens** menu of een opdracht zoals `curl`.

### <a name="import-files-from-the-project-dashboard"></a>Bestanden importeren uit het projectdashboard

1. Navigeer naar de map waar u om de bestanden te importeren in het project.

1. Selecteer de **uploaden** opdracht en vervolgens een **van URL** of **van computer** en de benodigde informatie voor de gegevens die u wilt importeren:

   - **Via URL**: Voer de bron-adres in de **bestands-URL** veld en de bestandsnaam om toe te wijzen aan de notebook in uw project in de **bestandsnaam** veld. Selecteer vervolgens **+ toevoegen bestand** om toe te voegen van de URL aan de uploadlijst met. Het proces herhalen voor elke extra URL's, en selecteer vervolgens **gedaan**.

     ![Uploaden vanaf URL pop-upvenster](media/quickstarts/upload-from-url-popup.png)

   - **Vanaf computer**: slepen en neerzetten van bestanden in het pop-upvenster of selecteer **bestanden kiezen**, en blader vervolgens naar en selecteer de gegevensbestanden die u wilt importeren. U kunt verwijderen of een willekeurig aantal bestanden van elk type en de indeling kiezen omdat het is aan de code in het notitieblok opent u het bestand en parseren van de gegevens.

     ![Uploaden vanaf computer pop-upvenster](media/quickstarts/upload-from-computer-popup.png)

1. Zodra geïmporteerd, bestanden worden weergegeven op het projectdashboard en is toegankelijk vanuit notebook code met behulp van relatieve padnamen naar de map met.

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>Bestanden importeren vanuit het menu bestand in een notitie blok

1. Binnen een actieve laptop, selecteer de **bestand** > **uploaden** opdracht:

    ![Het bestand geüpload menuopdracht binnen een laptop](media/file-menu-upload.png)

1. In het dialoogvenster dat wordt geopend, gaat u naar en selecteer de bestanden die u wilt uploaden. U kunt een onbeperkt aantal bestanden van elk type selecteren. Selecteer **Open** wanneer u klaar bent.

1. In de **Uploadstatus** pop-upbericht verschijnt, selecteer een **doelmap** uit de vervolgkeuzelijst:

    - Sessie-map ( *~/* ): bestanden in de huidige sessie van de notebook wordt geüpload, maar maakt geen bestanden in het project. De map van de sessie is een peer in de projectmap, maar niet bewaard is gebleven na de sessie wordt beëindigd. Voorvoegsel voor toegang tot sessiebestanden in code, de bestandsnamen met het relatieve pad *... /* .

        Met behulp van de sessie-map, is het handig om te experimenten en voorkomt u dat het project met bestanden die u kan of mogelijk niet op lange termijn. U kunt bestanden ook uploaden naar de sessie-map die dezelfde naam hebben tot bestanden in het project zonder conflicten veroorzaken, en zonder dat om de bestandsnamen te wijzigen. Bijvoorbeeld: Stel dat u één versie van hebt *data.csv* in het project al, maar wilt om te experimenteren met een andere versie van *data.csv*. Door het bestand te uploaden naar de map van de sessie, kunt u de notebook met behulp van gegevens in het geüploade bestand uitvoeren (naar wordt verwezen in met behulp van code *... /Data.csv*) in plaats van de gegevens in het bestand van het project.

    - Projectmap ( */project*): worden bestanden geüpload in het project waar ze kunnen worden geopend met behulp van relatieve padnamen in de code. Een bestand uploadt naar deze map is hetzelfde als het uploaden van een bestand op het projectdashboard. Het bestand met het project wordt opgeslagen en is beschikbaar in latere sessies.

        Uploaden mislukt als u probeert een bestand met dezelfde naam als een bestaande resourcegroep in het project te uploaden. Als u wilt een bestand wilt overschrijven, upload het nieuwe bestand in het projectdashboard in plaats daarvan, waarmee u de optie om te overschrijven.

1. Selecteer **Start uploaden** om het proces te voltooien.

### <a name="create-or-import-files-using-commands"></a>Maken of importeren van bestanden met behulp van opdrachten

U kunt opdrachten in een terminal of in een cel Python-code gebruiken om bestanden in mappen van het project en de sessie te maken. Bijvoorbeeld, opdrachten, zoals `curl` en `wget` bestanden rechtstreeks downloaden vanaf Internet.

Voor het downloaden van bestanden in de terminal, selecteer de **Terminal** opdracht in het projectdashboard, voert u geschikte opdrachten:

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

Wanneer u een Python-codecel in een notitieblok, de opdrachten het voorvoegsel `!`.

De projectmap is de standaardmap, dus op te geven, zoals een naam van het doelbestand *oil_price.csv* wordt het bestand in het project. Voor het maken van een sessiebestand, de naam voorafgaan door *... /* als in *... /oil_price.csv*.

### <a name="create-files-in-code"></a>Bestanden in code maken

Bij het gebruik van code die wordt gemaakt van een bestand, zoals de pandas `write_csv` functie padnamen zijn altijd ten opzichte van de projectmap. Met behulp van *... /* maakt een sessiebestand dat wordt verwijderd wanneer de notebook wordt gestopt en gesloten.

## <a name="export-files"></a>Bestanden exporteren

U kunt gegevens exporteren van het projectdashboard of vanuit een notitieblok.

## <a name="export-files-from-the-project-dashboard"></a>Bestanden uit het projectdashboard exporteren

Op het projectdashboard met de rechtermuisknop op een bestand en selecteer **downloaden**:

![Downloaden van de opdracht in contextmenu project-item](media/download-command.png)

U kunt ook een bestand selecteren en gebruiken de **downloaden** opdracht (sneltoets: d) op het dashboard:

![Opdracht van de werkbalk op projectdashboard downloaden](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Bestanden in het menu van de gegevens in een notitieblok exporteren

1. Selecteer de **bestand** > **downloaden** -opdracht:

    ![Gegevens downloadopdracht binnen een laptop](media/file-menu-download.png)

1. Een pop-up wordt weergegeven waarin de mappen in de sessie. de *project* map bevat de project-bestanden:

    ![Gegevens downloaden opdracht pop-upvenster waarin u bestanden en mappen selecteren](media/file-menu-download-popup.png)

1. Schakel de selectievakjes links van de bestanden en mappen die u wilt downloaden, en selecteer vervolgens **downloaden geselecteerd**.

1. De notebook wordt voorbereid voor een enkel *.zip* -bestand met de geselecteerde bestanden, die u vervolgens opslaan als u normaal doet in uw browser. Hiermee maakt u de notebook een *.zip* bestand, zelfs wanneer u een enkel bestand downloaden.

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot cloud-gegevens in een notitieblok](access-data-resources-jupyter-notebooks.md)
