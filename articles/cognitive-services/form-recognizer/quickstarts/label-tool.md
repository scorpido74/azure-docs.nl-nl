---
title: 'Snelstart: formulierlabelen, een model trainen en een formulier analyseren met behulp van het voorbeeldlabelgereedschap - Formulierherkenning'
titleSuffix: Azure Cognitive Services
description: In deze snelstart gebruikt u het voorbeeldlabelingsgereedschap Formulierherkenning om formulierdocumenten handmatig te labelen. Vervolgens traint u een aangepast model met de gelabelde documenten en gebruikt u het model om sleutel-/waardeparen te extraheren.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: pafarley
ms.openlocfilehash: 0cfe58ab0d161019d5f53d9135c65db7beff2bb4
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397995"
---
# <a name="train-a-form-recognizer-model-with-labels-using-the-sample-labeling-tool"></a>Een formulierherkenningsmodel trainen met labels met het voorbeeldlabelgereedschap

In deze quickstart gebruikt u de FORM Recognizer REST API met het voorbeeldlabelingsgereedschap om een aangepast model te trainen met handmatig gelabelde gegevens. Bekijk het gedeelte [Trainen met labels](../overview.md#train-with-labels) in het overzicht voor meer informatie over deze functie.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om deze snelle start te voltooien, moet u het:

- Een set van ten minste zes vormen van hetzelfde type. U gebruikt deze gegevens om het model te trainen en een formulier te testen. U een [voorbeeldgegevensset](https://go.microsoft.com/fwlink/?linkid=2090451) gebruiken voor deze quickstart. Upload de trainingsbestanden naar de hoofdmap van een blobopslagcontainer in een Azure Storage-account.

## <a name="create-a-form-recognizer-resource"></a>Een bron voor formulierherkenning maken

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-the-sample-labeling-tool"></a>Het voorbeeldlabelingsgereedschap instellen

U gebruikt de Docker-engine om het voorbeeldlabelingsgereedschap uit te voeren. Volg deze stappen om de Docker-container in te stellen. Zie het [Docker-overzicht](https://docs.docker.com/engine/docker-overview/) voor een inleiding tot de basisprincipes van Docker en containers.

> [!TIP]
> De OCR Form Labeling Tool is ook beschikbaar als een open source project op GitHub. De tool is een webapplicatie die is gebouwd met React + Redux en is geschreven in TypeScript. Zie [OCR Form Labeling Tool](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md#run-as-web-application)voor meer informatie of bijdragen.

1. Installeer docker eerst op een hostcomputer. Deze handleiding laat je zien hoe je lokale computer als host gebruiken. Als u een Docker-hostingservice in Azure wilt gebruiken, raadpleegt u de handleiding [voor het voorbeeldlabelen](../deploy-label-tool.md) implementeren. 

   De hostcomputer moet voldoen aan de volgende hardwarevereisten:

    | Container | Minimum | Aanbevolen|
    |:--|:--|:--|
    |Voorbeeldlabeling, gereedschap|2-core, 4 GB geheugen|4-core geheugen met 8 GB|

   Installeer Docker op uw machine door de juiste instructies voor uw besturingssysteem te volgen: 
   * [Windows](https://docs.docker.com/docker-for-windows/)
   * [Macos](https://docs.docker.com/docker-for-mac/)
   * [Linux](https://docs.docker.com/install/)

1. Download de voorbeeldlabelende `docker pull` gereedschapscontainer met de opdracht.
    ```
    docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool
    ```
1. Nu bent u klaar om `docker run`de container te draaien met .
    ```
    docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool eula=accept
    ```

   Met deze opdracht is het voorbeeldlabelingsprogramma beschikbaar via een webbrowser. Ga [http://localhost:3000](http://localhost:3000)naar.

> [!NOTE]
> U documenten en treinmodellen ook labelen met de FORM Recognizer REST API. Zie Trainen met labels met [de REST API en Python](./python-labeled-data.md)om te trainen en te analyseren met de REST API.

## <a name="set-up-input-data"></a>Invoergegevens instellen

Zorg er eerst voor dat alle trainingsdocumenten van hetzelfde formaat zijn. Als u formulieren in meerdere indelingen hebt, ordeert u deze in submappen op basis van een algemene indeling. Wanneer u traint, moet u de API naar een submap leiden.

### <a name="configure-cross-domain-resource-sharing-cors"></a>Cross-domain resource sharing (CORS) configureren

CorS inschakelen op uw opslagaccount. Selecteer uw opslagaccount in de Azure-portal en klik op het tabblad **CORS** in het linkerdeelvenster. Vul op de onderste regel de volgende waarden in. Klik vervolgens bovenaan op **Opslaan.**

* Toegestane oorsprong = * 
* Toegestane methoden = \[selecteer alle\]
* Toegestane kopteksten = *
* Blootgestelde kopteksten = * 
* Maximumleeftijd = 200

> [!div class="mx-imgBorder"]
> ![CORS-installatie in de Azure-portal](../media/label-tool/cors-setup.png)

## <a name="connect-to-the-sample-labeling-tool"></a>Verbinding maken met het voorbeeldlabelingsgereedschap

Het voorbeeldlabelingsgereedschap maakt verbinding met een bron (waar uw oorspronkelijke formulieren zich bevinden) en een doel (waarbij de gemaakte labels en uitvoergegevens worden geëxporteerd).

Verbindingen kunnen worden ingesteld en gedeeld tussen projecten. Ze maken gebruik van een uitbreidbaar providermodel, zodat u eenvoudig nieuwe bron-/doelproviders toevoegen.

Als u een nieuwe verbinding wilt maken, klikt u op het pictogram **Nieuwe verbindingen** (stekker) in de linkernavigatiebalk.

Vul de velden in met de volgende waarden:

* **Weergavenaam** - De naam van het verbindingsscherm.
* **Beschrijving** - Uw projectbeschrijving.
* **SAS URL** - De URL van de gedeelde toegangshandtekening (SAS) van uw Azure Blob Storage-container. Als u de SAS-URL wilt ophalen, opent u de Microsoft Azure Storage Explorer, klikt u met de rechtermuisknop op uw container en selecteert **u Handtekening voor gedeelde toegang ophalen**. Stel de vervaldatum in op enige tijd nadat u de service hebt gebruikt. Controleer of de machtigingen **Lezen,** **Schrijven,** **Verwijderen**en **Lijst** zijn ingeschakeld en klik op **Maken**. Kopieer vervolgens de waarde in de **sectie URL.** Het zou de `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`vorm moeten hebben: .

![Verbindingsinstellingen van voorbeeldlabeling](../media/label-tool/connections.png)

## <a name="create-a-new-project"></a>Een nieuw project maken

In het voorbeeldlabelingshulpmiddel slaan projecten uw configuraties en instellingen op. Maak een nieuw project en vul de velden in met de volgende waarden:

* **Weergavenaam** - de naam van de projectweergave
* **Beveiligingstoken** - Sommige projectinstellingen kunnen gevoelige waarden bevatten, zoals API-sleutels of andere gedeelde geheimen. Elk project genereert een beveiligingstoken dat kan worden gebruikt om gevoelige projectinstellingen te versleutelen/decoderen. U beveiligingstokens vinden in de toepassingsinstellingen door op het tandwielpictogram in de onderste hoek van de linkernavigatiebalk te klikken.
* **Bronverbinding** - De Azure Blob Storage-verbinding die u hebt gemaakt in de vorige stap die u voor dit project wilt gebruiken.
* **Mappad** - Optioneel - Als uw bronformulieren zich in een map in de blobcontainer bevinden, geeft u hier de mapnaam op
* **Url van formulierherkenningsservice Uri** - URL van uw formulierherkenningspunt.
* **API-sleutel** - Uw abonnementssleutel voor formulierherkenning.
* **Beschrijving** - Optioneel - Projectbeschrijving

![Nieuwe projectpagina op voorbeeldlabelingstool](../media/label-tool/new-project.png)

## <a name="label-your-forms"></a>Uw formulieren labelen

Wanneer u een project maakt of opent, wordt het venster van de hoofdmarkeringseditor geopend. De tag editor bestaat uit drie delen:

* Een resizable voorbeeldvenster met een schuifbare lijst met formulieren van de bronverbinding.
* Het hoofdeditorvenster waarmee u tags toepassen.
* Het editorvenster voor tags waarmee gebruikers tags kunnen wijzigen, vergrendelen, opnieuw ordenen en verwijderen. 

### <a name="identify-text-elements"></a>Tekstelementen identificeren

Klik **op OCR uitvoeren op alle bestanden** in het linkerdeelvenster om de informatie over de tekstindeling voor elk document op te halen. Met het labelgereedschap worden omsluitende vakken rond elk tekstelement getekend.

### <a name="apply-labels-to-text"></a>Labels toepassen op tekst

Vervolgens maakt u tags (labels) en past u deze toe op de tekstelementen die het model moet herkennen.

1. Gebruik eerst het editorvenster van tags om de tags te maken die u wilt identificeren.
  1. Klik **+** hier om een nieuwe tag te maken.
  1. Voer de tagnaam in.
  1. Druk op Enter om de tag op te slaan.
1. Klik en sleep in de hoofdeditor om een of meerdere woorden uit de gemarkeerde tekstelementen te selecteren.
1. Klik op de tag die u wilt toepassen of druk op de bijbehorende toetsenbordtoets. De nummertoetsen worden toegewezen als sneltoetsen voor de eerste 10 tags. U de tags opnieuw ordenen met de pictogrammen voor de pijl-omhoog en pijl-omlaag in het deelvenster-tageditor.
    > [!Tip]
    > Houd rekening met de volgende tips wanneer u uw formulieren labelt.
    > * U slechts één tag toepassen op elk geselecteerd tekstelement.
    > * Elke tag kan slechts één keer per pagina worden toegepast. Als een waarde meerdere keren op hetzelfde formulier wordt weergegeven, maakt u verschillende tags voor elke instantie. Bijvoorbeeld: "factuur# 1", "factuur# 2" enzovoort.
    > * Tags kunnen niet over verschillende pagina's heen lopen.
    > * Labelwaarden zoals deze op het formulier worden weergegeven; probeer niet om een waarde in twee delen met twee verschillende markeringen op te splitsen. Een adresveld moet bijvoorbeeld worden gelabeld met één tag, zelfs als het meerdere regels omvat.
    > * Neem geen sleutels op in&mdash;uw gelabelde velden alleen de waarden.
    > * Tabelgegevens moeten automatisch worden gedetecteerd en beschikbaar zijn in het JSON-bestand voor uiteindelijke uitvoer. Als het model echter niet al uw tabelgegevens detecteert, u deze velden ook handmatig taggen. Tag elke cel in de tabel met een ander label. Als uw formulieren tabellen met verschillende aantallen rijen hebben, moet u ervoor zorgen dat u ten minste één formulier tagt met de grootst mogelijke tabel.


Volg de bovenstaande stappen om vijf van uw formulieren te labelen en ga vervolgens verder met de volgende stap.

![Hoofdeditorvenster van voorbeeldlabelingsgereedschap](../media/label-tool/main-editor.png)


## <a name="train-a-custom-model"></a>Aangepast model trainen

Klik op het pictogram Trein (de treinauto) in het linkerdeelvenster om de pagina Training te openen. Klik vervolgens op de knop **Trainen** om het model te trainen. Zodra het trainingsproces is voltooid, ziet u de volgende informatie:

* **Model-ID** - De ID van het model dat is gemaakt en getraind. Elk trainingsgesprek creëert een nieuw model met een eigen ID. Kopieer deze tekenreeks naar een veilige locatie; je hebt het nodig als je voorspellingsoproepen wilt doen via de REST API.
* **Gemiddelde nauwkeurigheid** - De gemiddelde nauwkeurigheid van het model. U de nauwkeurigheid van het model verbeteren door aanvullende formulieren en training opnieuw te labelen om een nieuw model te maken. We raden u aan om te beginnen met het labelen van vijf formulieren en het toevoegen van meer formulieren als dat nodig is.
* De lijst met tags en de geschatte nauwkeurigheid per tag.

![trainingsweergave](../media/label-tool/train-screen.png)

Na afloop van de training, onderzoekt u de **waarde gemiddelde nauwkeurigheid.** Als deze laag is, moet u meer invoerdocumenten toevoegen en de bovenstaande stappen herhalen. De documenten die u al hebt gelabeld, blijven in de projectindex staan.

> [!TIP]
> U het trainingsproces ook uitvoeren met een REST API-aanroep. Zie Trainen met labels [met Python](./python-labeled-data.md)voor meer informatie over hoe u dit doen.

## <a name="analyze-a-form"></a>Een formulier analyseren

Klik op het pictogram Voorspellen (rechthoeken) aan de linkerkant om het model te testen. Upload een formulierdocument dat u niet hebt gebruikt in het trainingsproces. Klik vervolgens op de knop **Voorspellen** aan de rechterkant om belangrijke/waardevoorspellingen voor het formulier te krijgen. De tool past tags toe in selectievakken en rapporteert het vertrouwen van elke tag.

> [!TIP]
> U de API analyseren ook uitvoeren met een REST-aanroep. Zie Trainen met labels [met Python](./python-labeled-data.md)voor meer informatie over hoe u dit doen.

## <a name="improve-results"></a>Resultaten verbeteren

Afhankelijk van de gerapporteerde nauwkeurigheid, u verdere training doen om het model te verbeteren. Nadat u een voorspelling hebt gedaan, onderzoekt u de betrouwbaarheidswaarden voor elk van de toegepaste tags. Als de gemiddelde nauwkeurigheidstrainingswaarde hoog was, maar de betrouwbaarheidsscores laag zijn (of de resultaten onjuist zijn), moet u het bestand dat wordt gebruikt voor voorspelling toevoegen aan de trainingsset, deze labelen en opnieuw trainen.

De gerapporteerde gemiddelde nauwkeurigheid, betrouwbaarheidsscores en werkelijke nauwkeurigheid kunnen inconsistent zijn wanneer de geanalyseerde documenten verschillen van de documenten die worden gebruikt in de training. Houd er rekening mee dat sommige documenten er hetzelfde uitzien wanneer ze door mensen worden bekeken, maar er anders uitzien dan het AI-model. U bijvoorbeeld trainen met een formuliertype met twee varianten, waarbij de trainingsset bestaat uit 20% variatie A en 80% variatie B. Tijdens de voorspelling zullen de betrouwbaarheidsscores voor documenten van variatie A waarschijnlijk lager zijn.

## <a name="save-a-project-and-resume-later"></a>Een project opslaan en later hervatten

Als u uw project op een ander tijdstip of in een andere browser wilt hervatten, moet u het beveiligingstoken van uw project opslaan en het later opnieuw invoeren. 

### <a name="get-project-credentials"></a>Projectreferenties ophalen
Ga naar de pagina projectinstellingen (schuifpictogram) en noteer de naam van het beveiligingstoken. Ga vervolgens naar uw toepassingsinstellingen (tandwielpictogram), die alle beveiligingstokens in uw huidige browserexemplaar weergeven. Vind het beveiligingstoken van uw project en kopieer de naam en de sleutelwaarde naar een beveiligde locatie.

### <a name="restore-project-credentials"></a>Projectreferenties herstellen
Wanneer u het project wilt hervatten, moet u eerst een verbinding maken met dezelfde blobopslagcontainer. Herhaal de bovenstaande stappen om dit te doen. Ga vervolgens naar de pagina met toepassingsinstellingen (tandwielpictogram) en kijk of het beveiligingstoken van uw project aanwezig is. Als dit niet het is, voegt u een nieuw beveiligingstoken toe en kopieert u uw tokennaam en sleutel uit de vorige stap. Klik vervolgens op Instellingen opslaan. 

### <a name="resume-a-project"></a>Een project hervatten

Ga tot slot naar de hoofdpagina (huispictogram) en klik op Cloudproject openen. Selecteer vervolgens de blob-opslagverbinding en selecteer het *vott-bestand van* uw project. De toepassing laadt alle instellingen van het project omdat het beveiligingstoken is.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u het voorbeeldlabelingsgereedschap Formulierherkenning gebruiken om een model te trainen met handmatig gelabelde gegevens. Als u de labeltool wilt integreren in uw eigen toepassing, gebruikt u de REST API's die betrekking hebben op gelabelde gegevenstraining.

> [!div class="nextstepaction"]
> [Trainen met labels met Python](./python-labeled-data.md)
