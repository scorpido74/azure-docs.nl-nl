---
title: Gegevens importeren en exporteren met projecten met Azure Notebooks Preview
description: Meer informatie over het weergeven van gegevens in een Azure Notebooks Preview-project uit externe bronnen en hoe u gegevens uit een project exporteert.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: e1d4a52ab7f4ad2ca3438af4bc87bec0b79f34d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646973"
---
# <a name="work-with-data-files-in-azure-notebooks-preview-projects"></a>Werken met gegevensbestanden in Azure Notebooks Preview-projecten

Data is de levensader van veel Jupyter notebooks, vooral notebooks die worden gebruikt voor data science. Met Azure Notebooks u eenvoudig importeren uit verschillende bronnen in een project en die gegevens uit notitieblokken gebruiken. U notitieblokken ook gegevens laten genereren die in het project zijn opgeslagen, die u vervolgens elders downloaden.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Het menu **Gegevens** in een lopend notitieblok biedt ook **opdrachten uploaden** en **downloaden,** die werken met bestanden in het project en tijdelijke bestanden voor de huidige notitiebloksessie.

U ook code in een notitieblok gebruiken om rechtstreeks toegang te krijgen tot verschillende gegevensbronnen, waaronder bestanden binnen een project. U ook toegang krijgen tot willekeurige gegevens door opdrachten in een codecel te gebruiken. Omdat dergelijke gegevens worden opgeslagen in variabelen binnen de notitiebloksessie, worden deze niet opgeslagen in het project, tenzij u code gebruikt om specifiek projectbestanden te genereren.

Werken met code in gegevens wordt het beste ervaren in een lopend notitieblok zelf: raadpleeg daarvoor het [voorbeeldnotitieblok van Uw gegevens in Azure Notebooks.](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb)

De rest van dit artikel bevat details over bestandsbewerkingen op projectniveau.

## <a name="import-data"></a>Gegevens importeren

U bestanden naar een project brengen vanuit het projectdashboard **Data** of in een `curl`lopend notitieblok met behulp van het menu Gegevens of een opdracht zoals.

### <a name="import-files-from-the-project-dashboard"></a>Bestanden importeren uit het projectdashboard

1. Navigeer in het project naar de map waar u de bestanden wilt importeren.

1. Selecteer de opdracht **Uploaden** en vervolgens **van URL** of **van computer** en projecteer de benodigde gegevens voor de gegevens die u wilt importeren:

   - **Van URL:** voer het bronadres in het **veld URL van bestand** in en de bestandsnaam die u wilt toewijzen aan het notitieblok in uw project in het veld **Bestandsnaam.** Selecteer vervolgens **+ Bestand toevoegen** om de URL toe te voegen aan de uploadlijst. Herhaal het proces voor eventuele extra URL's en selecteer **Gereed**.

     ![Uploaden vanuit URL pop-up](media/quickstarts/upload-from-url-popup.png)

   - **Vanaf de computer**: Sleep en drop bestanden naar de pop-up of selecteer **Bestanden kiezen**, blader naar en selecteer de gegevensbestanden die u wilt importeren. U een willekeurig aantal bestanden, van elk type en formaat, laten vallen of kiezen, omdat het aan de code in het notitieblok is om het bestand te openen en de gegevens ervan te ontzeggen.

     ![Uploaden vanaf computerpop-up](media/quickstarts/upload-from-computer-popup.png)

1. Eenmaal geïmporteerd, worden bestanden weergegeven in het projectdashboard en kunnen ze worden geopend in de notebookcode met behulp van relatieve pathnamen naar de map met inhoud.

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>Bestanden importeren uit het menu Bestand in een notitieblok

1. Selecteer in een lopend notitieblok de opdracht **Bestand** > **uploaden:**

    ![Menu opdracht Voor het uploaden van bestanden in een notitieblok](media/file-menu-upload.png)

1. Navigeer in het dialoogvenster dat wordt geopend naar en selecteer de bestanden die u wilt uploaden. U een willekeurig aantal bestanden van elk type selecteren. Selecteer **Openen** wanneer u klaar bent.

1. Selecteer in de pop-up **Status uploaden** die wordt weergegeven een **doelmap** in de vervolgkeuzelijst:

    - Sessiemap*~/* : Hiermee worden bestanden geüpload naar de huidige notitiebloksessie, maar worden er geen bestanden in het project gemaakt. De sessiemap is een peer naar de projectmap, maar blijft niet bestaan nadat de sessie is afgelopen. Als u sessiebestanden in code wilt openen, moet u de bestandsnamen vooraf fixeren met het relatieve pad *.. /*.

        Het gebruik van de sessiemap is handig voor experimenten en voorkomt dat het project wordt verrommeld met bestanden die u al dan niet op lange termijn nodig hebt. U ook bestanden uploaden naar de sessiemap met identieke namen als bestanden in het project zonder conflicten te veroorzaken en zonder de naam van de bestanden te hoeven wijzigen. Stel dat u al één versie van *data.csv* in het project hebt, maar dat u wilt experimenteren met een andere versie van *data.csv*. Door het bestand in de sessiemap te uploaden, u het notitieblok uitvoeren met behulp van gegevens in het geüploade bestand (verwijzend naar het bestand in code met behulp van *.. /data.csv)* in plaats van de gegevens in het bestand van het project.

    - Projectmap (*/project*): uploadt bestanden naar het project waar ze toegankelijk zijn met behulp van relatieve pathnames in code. Het uploaden van een bestand naar deze map is hetzelfde als het uploaden van een bestand op het projectdashboard. Het bestand wordt opgeslagen bij het project en is beschikbaar in latere sessies.

        Uploaden mislukt als u probeert een bestand met dezelfde naam te uploaden als een bestand dat al in het project bestaat. Als u een bestand wilt overschrijven, uploadt u het nieuwe bestand in plaats daarvan vanuit het projectdashboard, waardoor u de optie hebt om te overschrijven.

1. Selecteer **Upload starten** om het proces te voltooien.

### <a name="create-or-import-files-using-commands"></a>Bestanden maken of importeren met opdrachten

U opdrachten binnen een terminal of binnen een Python-codecel gebruiken om bestanden te maken in zowel de project- als sessiemappen. Bijvoorbeeld opdrachten zoals `curl` en `wget` download bestanden rechtstreeks van het internet.

Als u bestanden in de terminal wilt downloaden, selecteert u de opdracht **Terminal** op het projectdashboard en voert u geschikte opdrachten in:

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

Wanneer u een Python-codecel in een notitieblok `!`gebruikt, u de opdrachten vooraf fixeren met.

De projectmap is de standaardmap, dus door een doelbestandsnaam op te geven zoals *oil_price.csv* maakt het bestand in het project. Als u een sessiebestand wilt maken, moet u de naam vooraf fixeren met *.. /* zoals in *.. /oil_price.csv*.

### <a name="create-files-in-code"></a>Bestanden maken in code

Bij het gebruik van code die een `write_csv` bestand maakt, zoals de functie panda's, zijn padnamen altijd relatief ten opzichte van de projectmap. Met behulp van *.. /* maakt een sessiebestand dat wordt verwijderd wanneer het notitieblok wordt gestopt en gesloten.

## <a name="export-files"></a>Bestanden exporteren

U gegevens exporteren vanuit het projectdashboard of vanuit een notitieblok.

## <a name="export-files-from-the-project-dashboard"></a>Bestanden exporteren vanuit het projectdashboard

Klik op het projectdashboard met de rechtermuisknop op een bestand en selecteer **Downloaden:**

![Opdracht downloaden in het contextmenu van projectitem](media/download-command.png)

U ook een bestand selecteren en de opdracht **Downloaden** (sneltoets: d) op het dashboard gebruiken:

![Opdracht Werkbalk downloaden op projectdashboard](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Bestanden exporteren vanuit het menu Gegevens in een notitieblok

1. Selecteer de menuopdracht **Bestand** > **downloaden:**

    ![Menu opdracht Gegevens downloaden in een notitieblok](media/file-menu-download.png)

1. Er verschijnt een pop-up met de mappen in de sessie. de *projectmap* bevat de projectbestanden:

    ![Pop-up van de opdracht Gegevens downloaden waarin u bestanden en mappen selecteert](media/file-menu-download-popup.png)

1. Selecteer de vakken links van de bestanden en mappen die u wilt downloaden en selecteer **Selecteer Geselecteerd downloaden**.

1. Het notitieblok bereidt één *.zip-bestand* voor met de geselecteerde bestanden, die u vervolgens opslaat zoals u normaal doet vanuit uw browser. Het notitieblok maakt een *.zip-bestand,* zelfs wanneer u één bestand downloadt.

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot cloudgegevens in een notebook](access-data-resources-jupyter-notebooks.md)
