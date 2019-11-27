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

Het menu **Data** van een actief notitie blok biedt ook **Upload** -en **Download** opdrachten, die met bestanden in het project werken, evenals tijdelijke bestanden voor de huidige notitieblok sessie.

U kunt code in een notitieblok ook gebruiken rechtstreeks, toegang hebben tot een verscheidenheid aan gegevensbronnen met inbegrip van bestanden in een project. U kunt ook toegang tot willekeurige gegevens met behulp van opdrachten in een codecel. Omdat dergelijke gegevens worden opgeslagen in variabelen in de notebook-sessie, deze niet opgeslagen in het project, tenzij u code specifiek om projectbestanden te genereren.

Werken met code in gegevens is het meest geschikt in een actief notitie blok zelf: Raadpleeg voor dat doel de [informatie over het verkrijgen van uw gegevens in azure notebooks voorbeeld notitieblok](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb).

De rest van dit artikel biedt informatie over het niveau van het project bestandsbewerkingen.

## <a name="import-data"></a>Gegevens importeren

U kunt bestanden vanuit het project-dash board of in een actief notitie blok naar een project brengen met behulp van het menu **Data** of een opdracht zoals `curl`.

### <a name="import-files-from-the-project-dashboard"></a>Bestanden importeren uit het projectdashboard

1. Navigeer naar de map waar u om de bestanden te importeren in het project.

1. Selecteer de **Upload** opdracht, vervolgens **op basis van URL** of **van computer** en projecteer de benodigde gegevens voor de gegevens die u wilt importeren:

   - **Van URL**: Voer het bron adres in het veld **bestands-URL** in en geef de bestands naam op die aan het notitie blok in het project moet worden toegewezen in het veld **Bestands naam** . Selecteer vervolgens **+ bestand toevoegen** om de URL toe te voegen aan de upload lijst. Herhaal dit proces voor eventuele extra Url's en selecteer vervolgens **gereed**.

     ![Uploaden vanaf URL pop-upvenster](media/quickstarts/upload-from-url-popup.png)

   - **Van computer**: Sleep en zet bestanden neer in de pop-up, of selecteer **bestanden kiezen**, blader naar en selecteer de gegevens bestanden die u wilt importeren. U kunt verwijderen of een willekeurig aantal bestanden van elk type en de indeling kiezen omdat het is aan de code in het notitieblok opent u het bestand en parseren van de gegevens.

     ![Uploaden vanaf computer pop-upvenster](media/quickstarts/upload-from-computer-popup.png)

1. Zodra geïmporteerd, bestanden worden weergegeven op het projectdashboard en is toegankelijk vanuit notebook code met behulp van relatieve padnamen naar de map met.

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>Bestanden importeren vanuit het menu bestand in een notitie blok

1. Selecteer in een actief notitie blok de opdracht **bestand** > **uploaden** :

    ![Het bestand geüpload menuopdracht binnen een laptop](media/file-menu-upload.png)

1. In het dialoogvenster dat wordt geopend, gaat u naar en selecteer de bestanden die u wilt uploaden. U kunt een onbeperkt aantal bestanden van elk type selecteren. Selecteer **openen** wanneer u klaar bent.

1. Selecteer in het pop-upvenster **Upload status** dat wordt weer gegeven een **doelmap** in de vervolg keuzelijst:

    - Sessiemap ( *~/* ): uploadt bestanden naar de huidige notebook sessie, maar maakt geen bestanden in het project. De map van de sessie is een peer in de projectmap, maar niet bewaard is gebleven na de sessie wordt beëindigd. Als u toegang wilt krijgen tot sessie bestanden in code, moet u de bestands namen voor het relatieve pad opgeven *. /* .

        Met behulp van de sessie-map, is het handig om te experimenten en voorkomt u dat het project met bestanden die u kan of mogelijk niet op lange termijn. U kunt bestanden ook uploaden naar de sessie-map die dezelfde naam hebben tot bestanden in het project zonder conflicten veroorzaken, en zonder dat om de bestandsnamen te wijzigen. Stel bijvoorbeeld dat er al één versie van *Data. CSV* in het project is, maar u wilt experimenteren met een andere versie van *Data. CSV*. Als u het bestand in de map Session uploadt, kunt u het notitie blok uitvoeren met behulp van gegevens in het geüploade bestand (in code verwijzen met behulp van *.. /data.CSV*) in plaats van de gegevens in het bestand van het project.

    - Projectmap ( */project*): uploadt bestanden naar het project waar ze toegankelijk zijn met relatieve padnamen in de code. Een bestand uploadt naar deze map is hetzelfde als het uploaden van een bestand op het projectdashboard. Het bestand met het project wordt opgeslagen en is beschikbaar in latere sessies.

        Uploaden mislukt als u probeert een bestand met dezelfde naam als een bestaande resourcegroep in het project te uploaden. Als u wilt een bestand wilt overschrijven, upload het nieuwe bestand in het projectdashboard in plaats daarvan, waarmee u de optie om te overschrijven.

1. Selecteer **uploaden starten** om het proces te volt ooien.

### <a name="create-or-import-files-using-commands"></a>Maken of importeren van bestanden met behulp van opdrachten

U kunt opdrachten in een terminal of in een cel Python-code gebruiken om bestanden in mappen van het project en de sessie te maken. Bijvoorbeeld: opdrachten als `curl` en `wget` rechtstreeks bestanden downloaden van het internet.

Als u bestanden in de Terminal wilt downloaden, selecteert u de **Terminal** opdracht in het project dashboard en voert u de volgende opdrachten in:

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

Wanneer u een python-code-cel in een notitie blok gebruikt, moet u de opdrachten voor het `!`.

De projectmap is de standaardmap. u kunt dus een doel bestandsnaam opgeven, zoals *oil_price. CSV* maakt het bestand in het project. Als u een sessie bestand wilt maken, maakt u een voor voegsel van de naam met *.. /* als in *.. /oil_price. CSV*.

### <a name="create-files-in-code"></a>Bestanden in code maken

Wanneer u code gebruikt waarmee een bestand wordt gemaakt, zoals de functie Pandas `write_csv`, zijn padnamen altijd relatief ten opzichte van de projectmap. Gebruiken *.. /* maakt een sessie bestand dat wordt genegeerd wanneer het notitie blok wordt gestopt en gesloten.

## <a name="export-files"></a>Bestanden exporteren

U kunt gegevens exporteren van het projectdashboard of vanuit een notitieblok.

## <a name="export-files-from-the-project-dashboard"></a>Bestanden uit het projectdashboard exporteren

Klik in het project dashboard met de rechter muisknop op een bestand en selecteer **downloaden**:

![Downloaden van de opdracht in contextmenu project-item](media/download-command.png)

U kunt ook een bestand selecteren en de **down load** opdracht (sneltoets: d) op het dash board gebruiken:

![Opdracht van de werkbalk op projectdashboard downloaden](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Bestanden in het menu van de gegevens in een notitieblok exporteren

1. Selecteer de menu opdracht **bestand** > **downloaden** :

    ![Gegevens downloadopdracht binnen een laptop](media/file-menu-download.png)

1. Er wordt een pop-upvenster weer gegeven waarin de mappen in de sessie worden weer gegeven. de *projectmap bevat de project bestanden* :

    ![Gegevens downloaden opdracht pop-upvenster waarin u bestanden en mappen selecteren](media/file-menu-download-popup.png)

1. Selecteer de vakken Links van de bestanden en mappen die u wilt downloaden en selecteer vervolgens **geselecteerd downloaden**.

1. Het notitie blok bereidt één *zip* -bestand voor met de geselecteerde bestanden, die u vervolgens opslaat zoals u normaal gesp roken kunt doen vanuit uw browser. Het notitie blok maakt een *zip* -bestand, zelfs wanneer u een enkel bestand downloadt.

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot Cloud gegevens in een notitie blok](access-data-resources-jupyter-notebooks.md)
