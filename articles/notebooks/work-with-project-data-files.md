---
title: Gegevens importeren en exporteren met projecten met Azure Notebooks preview
description: Meer informatie over het inbrengen van gegevens in een Azure Notebooks preview-project uit externe bronnen en het exporteren van gegevens uit een project.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: b3669128582d3bdd6a3c4506a040856ab7b07e9a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85834111"
---
# <a name="work-with-data-files-in-azure-notebooks-preview-projects"></a>Werken met gegevens bestanden in Azure Notebooks preview-projecten

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Gegevens zijn de lifeblood van veel Jupyter-notebooks, met name notebooks die worden gebruikt voor gegevens wetenschap. Met Azure Notebooks kunt u eenvoudig importeren uit verschillende bronnen in een project en vervolgens die gegevens van notebooks gebruiken. U kunt notitie blokken ook gegevens laten genereren die zijn opgeslagen in het project, die u vervolgens kunt downloaden voor gebruik op een andere locatie.

Het menu **Data** van een actief notitie blok biedt ook **Upload** -en **Download** opdrachten, die met bestanden in het project werken, evenals tijdelijke bestanden voor de huidige notitieblok sessie.

U kunt ook code binnen een notebook gebruiken om rechtstreeks toegang te krijgen tot een verscheidenheid aan gegevens bronnen, met inbegrip van bestanden in een project. U kunt ook toegang tot wille keurige gegevens krijgen met behulp van opdrachten in een code-cel. Omdat dergelijke gegevens worden opgeslagen in variabelen binnen de notitieblok sessie, worden deze niet opgeslagen in het project, tenzij u code gebruikt om specifiek project bestanden te genereren.

Werken met code in gegevens is het meest geschikt in een actief notitie blok zelf: Raadpleeg voor dat doel de [informatie over het verkrijgen van uw gegevens in azure notebooks voorbeeld notitieblok](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb).

In de rest van dit artikel vindt u meer informatie over bestands bewerkingen op project niveau.

## <a name="import-data"></a>Gegevens importeren

U kunt bestanden vanuit het project-dash board of in een actief notitie blok naar een project brengen met behulp van het menu **Data** of een opdracht, zoals `curl` .

### <a name="import-files-from-the-project-dashboard"></a>Bestanden importeren uit het project dashboard

1. Navigeer in het project naar de map waarnaar u de bestanden wilt importeren.

1. Selecteer de **Upload** opdracht, vervolgens **op basis van URL** of **van computer** en projecteer de benodigde gegevens voor de gegevens die u wilt importeren:

   - **Van URL**: Voer het bron adres in het veld **bestands-URL** in en geef de bestands naam op die aan het notitie blok in het project moet worden toegewezen in het veld **Bestands naam** . Selecteer vervolgens **+ bestand toevoegen** om de URL toe te voegen aan de upload lijst. Herhaal dit proces voor eventuele extra Url's en selecteer vervolgens **gereed**.

     ![Uploaden via URL-pop-up](media/quickstarts/upload-from-url-popup.png)

   - **Van computer**: Sleep en zet bestanden neer in de pop-up, of selecteer **bestanden kiezen**, blader naar en selecteer de gegevens bestanden die u wilt importeren. U kunt een wille keurig aantal bestanden neerzetten of kiezen, van elk type en elke indeling, omdat het tot de code in het notitie blok gaat om het bestand te openen en de gegevens te parseren.

     ![Van de computer uploaden](media/quickstarts/upload-from-computer-popup.png)

1. Nadat de bestanden zijn geïmporteerd, worden deze weer gegeven op het project dashboard en kunnen ze in de notitieblok code worden geopend met relatieve padnamen naar de bovenliggende map.

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>Bestanden importeren vanuit het menu bestand in een notitie blok

1. Selecteer in een actief notitie blok de opdracht voor het uploaden van **bestanden**  >  **Upload** :

    ![Menu opdracht bestand uploaden binnen een notitie blok](media/file-menu-upload.png)

1. In het dialoog venster dat wordt geopend, gaat u naar en selecteert u de bestanden die u wilt uploaden. U kunt een wille keurig aantal bestanden van elk type selecteren. Selecteer **openen** wanneer u klaar bent.

1. Selecteer in het pop-upvenster **Upload status** dat wordt weer gegeven een **doelmap** in de vervolg keuzelijst:

    - Sessiemap ( *~/* ): uploadt bestanden naar de huidige notebook sessie, maar maakt geen bestanden in het project. De sessiemap is een peer met de projectmap, maar blijft niet behouden nadat de sessie is beëindigd. Als u toegang wilt krijgen tot sessie bestanden in code, moet u de bestands namen voor het relatieve pad opgeven *. /*.

        Het gebruik van de sessiemap is handig voor experimenten en voor komt dat u het project overzichtelijk maakt met bestanden die u mogelijk op lange termijn nodig hebt. U kunt ook bestanden uploaden naar de sessiemap met identieke namen naar bestanden in het project zonder dat er conflicten ontstaan en zonder dat u de bestanden hoeft te wijzigen. Stel bijvoorbeeld dat er al een versie van *data.csv* in het project is, maar u wilt experimenteren met een andere versie van *data.csv*. Als u het bestand in de map Session uploadt, kunt u het notitie blok uitvoeren met behulp van gegevens in het geüploade bestand (in code verwijzen met behulp van *.. /data.csv*) in plaats van de gegevens in het bestand van het project.

    - Projectmap (*/project*): uploadt bestanden naar het project waar ze toegankelijk zijn met relatieve padnamen in de code. Het uploaden van een bestand naar deze map is hetzelfde als het uploaden van een bestand in het project dashboard. Het bestand wordt opgeslagen met het project en is beschikbaar in latere sessies.

        Uploaden mislukt als u een bestand met dezelfde naam probeert te uploaden dat al in het project bestaat. Als u een bestand wilt overschrijven, uploadt u het nieuwe bestand vanuit het project dashboard in plaats daarvan, waarmee u de optie voor overschrijven kunt gebruiken.

1. Selecteer **uploaden starten** om het proces te volt ooien.

### <a name="create-or-import-files-using-commands"></a>Bestanden maken of importeren met behulp van opdrachten

U kunt opdrachten binnen een Terminal of binnen een python-code-cel gebruiken om bestanden te maken binnen de project-en sessie mappen. Bijvoorbeeld opdrachten, zoals `curl` en `wget` downloaden van bestanden vanaf internet rechtstreeks.

Als u bestanden in de Terminal wilt downloaden, selecteert u de **Terminal** opdracht in het project dashboard en voert u de volgende opdrachten in:

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

Wanneer u een python-code-cel in een notitie blok gebruikt, moet u de opdrachten voorzien van een voor voegsel `!` .

De projectmap is de standaardmap, dus geef een naam op voor het doel, zoals *oil_price.csv* het bestand in het project maakt. Als u een sessie bestand wilt maken, maakt u een voor voegsel van de naam met *.. /* als in *.. /oil_price.csv*.

### <a name="create-files-in-code"></a>Bestanden in code maken

Wanneer u code gebruikt waarmee een bestand wordt gemaakt, zoals de functie Pandas `write_csv` , zijn padnamen altijd relatief ten opzichte van de projectmap. Gebruiken *.. /* maakt een sessie bestand dat wordt genegeerd wanneer het notitie blok wordt gestopt en gesloten.

## <a name="export-files"></a>Bestanden exporteren

U kunt gegevens exporteren vanuit het project dashboard of vanuit een notitie blok.

## <a name="export-files-from-the-project-dashboard"></a>Bestanden vanuit het project dashboard exporteren

Klik in het project dashboard met de rechter muisknop op een bestand en selecteer **downloaden**:

![Opdracht downloaden in het context menu van het project item](media/download-command.png)

U kunt ook een bestand selecteren en de **down load** opdracht (sneltoets: d) op het dash board gebruiken:

![Opdracht werk balk downloaden in Project dashboard](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Bestanden exporteren vanuit het menu Data in een notitie blok

1. Selecteer de **File**  >  menu opdracht bestand**downloaden** :

    ![Menu opdracht voor het downloaden van gegevens binnen een notitie blok](media/file-menu-download.png)

1. Er wordt een pop-upvenster weer gegeven waarin de mappen in de sessie worden weer gegeven. de *projectmap bevat de project bestanden* :

    ![Pop-up van de opdracht gegevens downloaden, waarin u bestanden en mappen selecteert](media/file-menu-download-popup.png)

1. Selecteer de vakken Links van de bestanden en mappen die u wilt downloaden en selecteer vervolgens **geselecteerd downloaden**.

1. Het notitie blok bereidt één *zip* -bestand voor met de geselecteerde bestanden, die u vervolgens opslaat zoals u normaal gesp roken kunt doen vanuit uw browser. Het notitie blok maakt een *zip* -bestand, zelfs wanneer u een enkel bestand downloadt.

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot cloudgegevens in een notebook](access-data-resources-jupyter-notebooks.md)
