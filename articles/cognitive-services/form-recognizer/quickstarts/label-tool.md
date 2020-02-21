---
title: 'Snelstartgids: formulieren labelen, model leren en een formulier analyseren met behulp van de voor beeld-formulier herkenning'
titleSuffix: Azure Cognitive Services
description: In deze Quick Start gebruikt u het hulp programma voor het labelen van het voor beeld van de formulier herkenning om formulier documenten hand matig te labelen. Vervolgens traint u een aangepast model met de gelabelde documenten en gebruikt u het model om sleutel-waardeparen te extra heren.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: pafarley
ms.openlocfilehash: 812680e587ac5c5c8b3d949199a615fcd85fa610
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485349"
---
# <a name="train-a-form-recognizer-model-with-labels-using-the-sample-labeling-tool"></a>Een model voor het herkennen van een formulier met labels trainen met behulp van het voor beeld-label programma

In deze Quick Start gebruikt u de REST API formulier Recognizer met het hulp programma voor het labelen van het voor beeld om een aangepast model met hand matig gelabelde gegevens te trainen. Zie de sectie [met labels trainen](../overview.md#train-with-labels) in het overzicht voor meer informatie over deze functie.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze Snelstartgids hebt u het volgende nodig:

- Een set van ten minste zes soorten van hetzelfde type. U gebruikt deze gegevens om het model te trainen en een formulier te testen. U kunt een voor [beeld](https://go.microsoft.com/fwlink/?linkid=2090451) van een gegevensset voor deze Quick Start gebruiken. Upload de trainings bestanden naar de hoofdmap van een BLOB storage-container in een Azure Storage-account.

## <a name="create-a-form-recognizer-resource"></a>Een resource voor een formulier herkenning maken

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-the-sample-labeling-tool"></a>Het hulp programma voor het labelen van het voor beeld instellen

U gebruikt de docker-engine voor het uitvoeren van het hulp programma voor het labelen van voor beelden. Volg deze stappen om de docker-container in te stellen. Zie het [docker-overzicht](https://docs.docker.com/engine/docker-overview/)voor een primer op basis van docker en container.
1. Installeer eerst docker op een hostcomputer. De hostcomputer kan uw lokale computer zijn ([Windows](https://docs.docker.com/docker-for-windows/), [macOS](https://docs.docker.com/docker-for-mac/)of [Linux](https://docs.docker.com/install/)). U kunt ook een docker-hosting service gebruiken in azure, zoals de [Azure Kubernetes-service](https://docs.microsoft.com/azure/aks/index), [Azure container instances](https://docs.microsoft.com/azure/container-instances/index)of een Kubernetes-cluster [dat is geïmplementeerd op een Azure stack](https://docs.microsoft.com/azure-stack/user/azure-stack-solution-template-kubernetes-deploy?view=azs-1910). De hostcomputer moet voldoen aan de volgende hardwarevereisten:

    | Container | Minimum | Aanbevolen|
    |:--|:--|:--|
    |Voor beeld van een label programma|2 Core, 4 GB geheugen|4 kern geheugen van 8 GB|
    
1. Haal de voor beeld-container met de `docker pull` opdracht.
    ```
    docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool
    ```
1. U bent nu klaar om de container met `docker run`uit te voeren.
    ```
    docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool eula=accept
    ```

   Met deze opdracht wordt het hulp programma voor het labelen van het voor beeld beschikbaar gemaakt via een webbrowser. Ga naar [http://localhost:3000](http://localhost:3000).

> [!NOTE]
> U kunt ook documenten labelen en model leren met behulp van de REST API voor formulier herkenning. Als u wilt trainen en analyseren met de REST API, raadpleegt u [Train with labels met behulp van de rest API en python](./python-labeled-data.md).

## <a name="set-up-input-data"></a>Invoer gegevens instellen

Zorg er eerst voor dat alle trainings documenten dezelfde indeling hebben. Als u formulieren in meerdere indelingen hebt, kunt u deze indelen in submappen op basis van de algemene indeling. Wanneer u traint, moet u de API naar een submap sturen.

### <a name="configure-cross-domain-resource-sharing-cors"></a>Het delen van resources (CORS) voor meerdere domeinen configureren

Schakel CORS in voor uw opslag account. Selecteer uw opslag account in de Azure Portal en klik op het tabblad **CORS** in het linkerdeel venster. Vul op de onderste regel de volgende waarden in. Klik vervolgens bovenaan op **Opslaan** .

* Toegestane oorsprongen = * 
* Toegestane methoden = \[alle\] selecteren
* Toegestane headers = *
* Weer gegeven headers = * 
* Max. leeftijd = 200

> [!div class="mx-imgBorder"]
> ![CORS-instellingen in de Azure Portal](../media/label-tool/cors-setup.png)

## <a name="connect-to-the-sample-labeling-tool"></a>Verbinding maken met het hulp programma voor het labelen van het voor beeld

Het hulp programma voor het labelen van labels maakt verbinding met een bron (waarbij uw oorspronkelijke formulieren zijn) en een doel (waar de gemaakte labels en uitvoer gegevens worden geëxporteerd).

Verbindingen kunnen worden ingesteld en gedeeld in projecten. Ze gebruiken een uitbreidbaar provider model, zodat u eenvoudig nieuwe bron/doel providers kunt toevoegen.

Als u een nieuwe verbinding wilt maken, klikt u op het pictogram **nieuwe verbindingen** (plug) in de linkernavigatiebalk.

Vul de velden in met de volgende waarden:

* **Weergave naam** : de weergave naam van de verbinding.
* **Beschrijving** : de beschrijving van uw project.
* **SAS-URL** : de URL voor Shared Access Signature (SAS) van uw Azure Blob Storage-container. Als u de SAS-URL wilt ophalen, opent u de Microsoft Azure Storage Explorer, klikt u met de rechter muisknop op uw container en selecteert u **gedeelde toegangs handtekening ophalen**. Stel de verloop tijd in op enige tijd nadat u de service hebt gebruikt. Zorg ervoor dat de machtigingen **lezen**, **schrijven**, **verwijderen**en **lijst** zijn ingeschakeld en klik op **maken**. Kopieer vervolgens de waarde in de sectie **URL** . Het moet de volgende indeling hebben: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

![Verbindings instellingen van het hulp programma voor het labelen van voor beelden](../media/label-tool/connections.png)

## <a name="create-a-new-project"></a>Een nieuw project maken

In het hulp programma labelen worden uw configuraties en instellingen opgeslagen in projecten. Maak een nieuw project en vul de velden in met de volgende waarden:

* **Weergave naam** : de weergave naam van het project
* **Beveiligings token** : sommige project instellingen kunnen gevoelige waarden bevatten, zoals API-sleutels of andere gedeelde geheimen. Elk project genereert een beveiligings token dat kan worden gebruikt voor het versleutelen/ontsleutelen van gevoelige project instellingen. U kunt beveiligings tokens vinden in de toepassings instellingen door te klikken op het tandwiel pictogram in de linkerbenedenhoek van de linkernavigatiebalk.
* **Bron verbinding** : de Azure Blob Storage-verbinding die u hebt gemaakt in de vorige stap die u voor dit project wilt gebruiken.
* **Mappad-optioneel** : als uw bron formulieren zich in een map in de BLOB-container bevinden, geeft u de mapnaam hier op
* **URI van de formulier Recognizer-service** : de eind punt-URL van de formulier herkenning.
* **API-sleutel** : de abonnements sleutel voor uw formulier herkenning.
* **Beschrijving** -optioneel-project beschrijving

![Nieuwe project pagina voor het hulp programma labelen](../media/label-tool/new-project.png)

## <a name="label-your-forms"></a>Uw formulieren een label

Wanneer u een project maakt of opent, wordt het hoofd venster van de tageditor geopend. De code-editor bestaat uit drie delen:

* Een voorbeeld venster met een verstelbaar formaat dat een lijst met formulieren van de bron verbinding bevat.
* Het deel venster hoofd editor waarmee u labels kunt Toep assen.
* Het deel venster Tags editor waarmee gebruikers labels kunnen wijzigen, vergren delen, opnieuw ordenen en verwijderen. 

### <a name="identify-text-elements"></a>Tekst elementen identificeren

Klik op **OCR uitvoeren op alle bestanden** in het linkerdeel venster om informatie over de tekst indeling voor elk document op te halen. Met het hulp programma labelen worden omsluitende vakken rond elk tekst element getekend.

### <a name="apply-labels-to-text"></a>Labels Toep assen op tekst

Vervolgens maakt u labels en past u deze toe op de tekst elementen die u door het model wilt laten herkennen.

1. Gebruik eerst het deel venster Tags editor om de labels (labels) te maken die u wilt identificeren.
1. Klik in de hoofd editor op en sleep om een of meer woorden van de gemarkeerde tekst elementen te selecteren.

    > [!NOTE]
    > U kunt momenteel geen tekst selecteren die over meerdere pagina's heen loopt.
1. Klik op het label dat u wilt Toep assen of druk op de bijbehorende toetsenbord toets. U kunt slechts één tag Toep assen op elk geselecteerd tekst element en elke tag kan slechts eenmaal per pagina worden toegepast.

    > [!TIP]
    > De nummer sleutels worden toegewezen als sneltoetsen voor de eerste tien Tags. U kunt de volg orde van de labels wijzigen met behulp van de pictogrammen pijl omhoog en omlaag in het deel venster Code-editor.

Volg de bovenstaande stappen voor het labelen van vijf van uw formulieren en ga vervolgens verder met de volgende stap.

![Venster van de hoofd editor van het hulp programma voor het labelen van voor beelden](../media/label-tool/main-editor.png)


## <a name="train-a-custom-model"></a>Aangepast model trainen

Klik op het trein pictogram (de trein wagen) in het linkerdeel venster om de pagina training te openen. Klik vervolgens op de **trein** knop om het model te trainen. Zodra het trainings proces is voltooid, ziet u de volgende informatie:

* **Model-id** : de id van het model dat is gemaakt en getraind. Elk trainings gesprek maakt een nieuw model met een eigen ID. Deze teken reeks kopiëren naar een veilige locatie; u hebt deze nodig als u Voorspellings aanroepen wilt uitvoeren via de REST API.
* **Gemiddelde nauw keurigheid** : de gemiddelde nauw keurigheid van het model. U kunt de nauw keurigheid van het model verbeteren door extra formulieren en training opnieuw te labelen om een nieuw model te maken. We raden u aan om te beginnen met het labelen van vijf formulieren en om indien nodig meer formulieren toe te voegen.
* De lijst met tags en de geschatte nauw keurigheid per label.

![trainings weergave](../media/label-tool/train-screen.png)

Nadat de training is voltooid, controleert u de **gemiddelde nauwkeurigheids** waarde. Als het laag is, moet u meer invoer documenten toevoegen en de bovenstaande stappen herhalen. De documenten die u al hebt gelabeld, blijven in de project index.

> [!TIP]
> U kunt het trainings proces ook uitvoeren met een REST API-oproep. Zie voor meer informatie over hoe u dit doet, [Train with labels using python](./python-labeled-data.md).

## <a name="analyze-a-form"></a>Een formulier analyseren

Klik op het pictogram voors pellen (rechthoeken) aan de linkerkant om het model te testen. Upload een formulier document dat u niet in het trainings proces hebt gebruikt. Klik vervolgens op de knop voors **pellen** aan de rechter kant om de voor spellingen van sleutel/waarde voor het formulier op te halen. In het hulp programma worden tags toegepast in selectie kaders en wordt het vertrouwen van elke tag gerapporteerd.

> [!TIP]
> U kunt ook de analyse-API met een REST-aanroep uitvoeren. Zie voor meer informatie over hoe u dit doet, [Train with labels using python](./python-labeled-data.md).

## <a name="improve-results"></a>Resultaten verbeteren

Afhankelijk van de gerapporteerde nauw keurigheid kunt u verdere trainingen doen om het model te verbeteren. Nadat u een voor spelling hebt uitgevoerd, kunt u de betrouwbaarheids waarden voor elk van de toegepaste labels bekijken. Als de gemiddelde nauw keurigheid van de training hoog was, maar de betrouwbaarheids scores laag zijn (of de resultaten onjuist zijn), moet u het bestand dat wordt gebruikt voor de voor spelling, toevoegen aan de Trainingsset, het label en het opnieuw trainen.

De gerapporteerde gemiddelde nauw keurigheid, betrouwbaarheids scores en werkelijke nauw keurigheid kunnen inconsistent zijn wanneer de geanalyseerde documenten verschillen van de voor waarden die worden gebruikt in de training. Houd er bij dat sommige documenten er ongeveer zo uitzien als ze worden weer gegeven door personen, maar kunnen er onderscheid uitzien van het AI-model. U kunt bijvoorbeeld trainen met een formulier type dat twee variaties heeft, waarbij de set training bestaat uit 20% Variation A en 80% variant B. Tijdens de voor spelling zijn de betrouwbaarheids scores voor documenten van variatie A waarschijnlijk lager.

## <a name="save-a-project-and-resume-later"></a>Een project opslaan en later hervatten

Als u het project op een ander tijdstip of in een andere browser wilt hervatten, moet u het beveiligings token van het project opslaan en het later opnieuw invoeren. 

### <a name="get-project-credentials"></a>Project referenties ophalen
Ga naar de pagina met Project instellingen (schuif regelaar) en noteer de naam van het beveiligings token. Ga vervolgens naar uw toepassings instellingen (tandwiel pictogram), waarin alle beveiligings tokens in uw huidige browser exemplaar worden weer gegeven. Zoek het beveiligings token van uw project en kopieer de naam en sleutel waarde naar een veilige locatie.

### <a name="restore-project-credentials"></a>Project referenties herstellen
Als u uw project wilt hervatten, moet u eerst een verbinding maken met dezelfde Blob Storage-container. Herhaal de bovenstaande stappen om dit te doen. Ga vervolgens naar de pagina met toepassings instellingen (tandwiel pictogram) en kijk of het beveiligings token van uw project daar is. Als dat niet het geval is, voegt u een nieuw beveiligings token toe en kopieert u de token naam en-sleutel uit de vorige stap. Klik vervolgens op instellingen opslaan. 

### <a name="resume-a-project"></a>Een project hervatten
Ga ten slotte naar de hoofd pagina (huis pictogram) en klik op Cloud project openen. Selecteer vervolgens de verbinding voor Blob-opslag en selecteer het *. vott* -bestand van het project. De toepassing laadt alle instellingen van het project, omdat deze het beveiligings token heeft.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u het hulp programma voor het labelen van het voorbeeld formulier Recognizer kunt gebruiken om een model met hand matig gelabelde gegevens te trainen. Als u het hulp programma voor labels wilt integreren in uw eigen toepassing, gebruikt u de REST-Api's die omgaan met gelabelde gegevens training.

> [!div class="nextstepaction"]
> [Trainen met labels met behulp van python](./python-labeled-data.md)
