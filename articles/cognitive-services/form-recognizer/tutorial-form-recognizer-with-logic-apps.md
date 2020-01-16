---
title: 'Zelf studie: formulier herkenner gebruiken met Azure Logic Apps voor het analyseren van facturen-formulier herkenner'
titleSuffix: Azure Cognitive Services
description: In deze zelf studie gebruikt u de formulier Recognizer met Azure Logic Apps om een werk stroom te maken waarmee het proces van het trainen van een model wordt geautomatiseerd en getest met voorbeeld gegevens.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 10/27/2019
ms.author: nitinme
ms.openlocfilehash: 14affb2c2aa53fc7a2b1a5946e81ad124800f678
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981264"
---
# <a name="tutorial-use-form-recognizer-with-azure-logic-apps-to-analyze-invoices"></a>Zelf studie: formulier herkenner gebruiken met Azure Logic Apps voor het analyseren van facturen

In deze zelf studie maakt u een werk stroom in Azure Logic Apps die gebruikmaakt van een formulier herkenner, een service die deel uitmaakt van Azure Cognitive Services suite, om gegevens uit facturen te extra heren. U gebruikt de formulier Recognizer om eerst een model te trainen met behulp van een set met voorbeeld gegevens en het model vervolgens te testen met een andere gegevensset. De voorbeeld gegevens die in deze zelf studie worden gebruikt, worden opgeslagen in Azure Storage BLOB-containers.

Deze zelf studie bevat de volgende onderwerpen:

> [!div class="checklist"]
> * Toegang aanvragen voor de formulier herkenning
> * Een Azure Storage BLOB-container maken
> * Voorbeeld gegevens uploaden naar de Azure Blob-container
> * Een Azure Logic-app maken
> * De logische app configureren voor het gebruik van een formulier Recognizer-resource
> * De werk stroom testen door de logische app uit te voeren

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak een gratis versie](https://azure.microsoft.com/free/).

## <a name="request-access-for-form-recognizer"></a>Toegang aanvragen voor de formulier herkenning

Formulier herkenner is beschikbaar in een preview-versie van beperkte toegang. Als u toegang wilt krijgen tot de preview, vult u het formulier voor de [toegangs aanvraag voor de formulier herkenning](https://aka.ms/FormRecognizerRequestAccess) in en verzendt u dit. Zodra uw aanvraag is goedgekeurd door het team van Azure Cognitive Services, ontvangt u een e-mail met instructies voor het openen van de service.

## <a name="understand-the-invoice-to-be-analyzed"></a>De te analyseren factuur begrijpen

De set met voorbeeld gegevens die we gebruiken om het model te trainen en het model te testen, is beschikbaar als zip-bestand van [github](https://go.microsoft.com/fwlink/?linkid=2090451). Down load en pak het zip-bestand uit en open een factuur PDF-bestand in de map **/Train** . U ziet dat er een tabel is met het factuur nummer, de factuur datum, enzovoort. 

> [!div class="mx-imgBorder"]
> ![voor beeld van factuur](media/tutorial-form-recognizer-with-logic-apps/sample-receipt.png)

In deze zelf studie leert u hoe u de informatie uit deze tabellen uitpakt in een JSON-indeling met een werk stroom die is gemaakt met Azure Logic Apps en formulier herkenning.

## <a name="create-an-azure-storage-blob-container"></a>Een Azure Storage BLOB-container maken

U gebruikt deze container om voorbeeld gegevens te uploaden die nodig zijn om het model te trainen.

1. Volg de instructies in [Create a Azure Storage account](../../storage/common/storage-account-create.md) om een opslag account te maken. Gebruik **formrecostorage** als de naam van het opslag account.
1. Volg de instructies in [een Azure Blob-container maken](../../storage/blobs/storage-quickstart-blobs-portal.md) om een container te maken binnen het Azure Storage-account. Gebruik **formrecocontainer** als container naam. Zorg ervoor dat u het niveau voor open bare toegang instelt op **container (anonieme lees toegang voor containers en blobs)** .

    > [!div class="mx-imgBorder"]
    > ![een BLOB-container maken](media/tutorial-form-recognizer-with-logic-apps/create-blob-container.png)

## <a name="upload-sample-data-to-the-azure-blob-container"></a>Voorbeeld gegevens uploaden naar de Azure Blob-container

Down load de voorbeeld gegevens die beschikbaar zijn op [github](https://go.microsoft.com/fwlink/?linkid=2090451). Extraheer de gegevens naar een lokale map en upload de inhoud van de map **/Train** naar de **formrecocontainer** die u eerder hebt gemaakt. Volg de instructies bij het [uploaden van een blok-BLOB](../../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob) voor het uploaden van gegevens naar een container.

Kopieer de URL van de container. U hebt dit later in deze zelf studie nodig. Als u het opslag account en de container met dezelfde namen hebt gemaakt als vermeld in deze zelf studie, is de URL *https:\//formrecostorage.blob.core.Windows.net/formrecocontainer/* .

## <a name="create-a-form-recognizer-resource"></a>Een resource voor een formulier herkenning maken

[!INCLUDE [create resource](./includes/create-resource.md)]

## <a name="create-your-logic-app"></a>Uw logische app maken

U kunt Azure Logic Apps gebruiken om taken en werk stromen te automatiseren en te organiseren. In deze zelf studie maakt u een logische app die wordt geactiveerd door een factuur te ontvangen die u wilt analyseren als e-mail bijlage. In deze werk stroom voert u de volgende taken uit:
* Configureer de logische app zodanig dat deze automatisch wordt geactiveerd wanneer u een e-mail ontvangt waaraan een factuur is gekoppeld.
* Configureer de logische app voor het gebruik van een vorm van een **Train model** -bewerking voor het herkennen van een model voor het trainen van de voorbeeld gegevens die u hebt geüpload naar de Azure Blob-opslag.
* Configureer de logische app voor het gebruik van een form-bewerking voor het **analyseren** van formulieren voor het model dat u al hebt getraind. Met dit onderdeel wordt de factuur die u aan deze logische app verstrekt, geanalyseerd op basis van het model dat eerder is getraind.

Laten we beginnen. Volg deze stappen om uw werk stroom in te stellen.

1. Selecteer in het hoofd menu van Azure **een resource maken** > **integratie** > **logische app**.

1. Onder **Logische app maken** geeft u informatie op over uw logische app zoals hier wordt weergegeven. Wanneer u klaar bent, selecteert u **maken**.

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Naam** | <*logic-app-name*> | De naam van de logische app, die alleen letters, cijfers, afbreek streepjes (`-`), onderstrepings tekens (`_`), haakjes (`(`, `)`) en punten (`.`) kan bevatten. In dit voor beeld wordt ' mijn-eerste logica-app ' gebruikt. |
   | **Abonnement** | <*Azure-subscription-name*> | De naam van uw Azure-abonnement |
   | **Resourcegroep** | <*Azure-resource-group-name*> | De naam voor de [Azure-resource groep](./../../azure-resource-manager/management/overview.md) die wordt gebruikt om verwante resources te organiseren. In dit voor beeld wordt ' My-First-LA-RG ' gebruikt. |
   | **Locatie** | <*Azure-regio*> | De regio waar u de gegevens van uw logische app opslaat. In dit voor beeld wordt ' West US ' gebruikt. |
   | **Log Analytics** | Uit | Behoud de instelling **Uit** voor het vastleggen van diagnostische gegevens. |
   ||||

1. Nadat Azure uw app heeft geïmplementeerd, selecteert u op de Azure-werk balk **meldingen** > **gaat u naar de resource** voor uw geïmplementeerde logische app. U kunt ook uw logische app zoeken en selecteren door de naam in het zoekvak te typen.

   De Logic Apps-ontwerpfunctie wordt geopend en u ziet een pagina met een inleidende video en veelgebruikte triggers. Kies onder **Sjablonen** de optie **Lege logische app**.

   > [!div class="mx-imgBorder"]
   > ![lege sjabloon voor logische app selecteren](./../../logic-apps/media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

### <a name="configure-the-logic-app-to-trigger-the-workflow-when-an-email-arrives"></a>De logische app configureren om de werk stroom te activeren wanneer er een e-mail bericht binnenkomt

In deze zelf studie wordt de werk stroom geactiveerd wanneer er een e-mail met een bijgevoegde factuur wordt ontvangen. Voor deze zelf studie kiezen we Office 365 als e-mail service, maar u kunt elke andere e-mail provider gebruiken die u wilt gebruiken.

1. Selecteer op de tabbladen alle, selecteer **Office 365 Outlook**en selecteer onder **Triggers** **de optie wanneer een nieuwe e-mail binnenkomt**.

    ![Logische app activeren via een inkomend e-mail bericht](media/tutorial-form-recognizer-with-logic-apps/logic-app-email-trigger.png)

1. Klik in het vak **Office 365 Outlook** op **Aanmelden**en voer de details in om u aan te melden bij een Office 365-account.

1. Voer de volgende stappen uit in het volgende dialoog venster.
    1. Selecteer de map die moet worden bewaakt voor een nieuw e-mail bericht.
    1. Voor **heeft bijlagen** , selecteert u **Ja**. Dit zorgt ervoor dat alleen de e-mail berichten met bijlagen de werk stroom activeren.
    1. Selecteer **Ja**als u **bijlagen wilt toevoegen** . Dit zorgt ervoor dat de inhoud van de bijlage wordt gebruikt in downstream-verwerking.

        > [!div class="mx-imgBorder"]
        > e-mail trigger voor logische apps ![configureren](media/tutorial-form-recognizer-with-logic-apps/logic-app-specify-email-folder.png)

1. Klik in de werk balk bovenaan op **Opslaan** .

### <a name="configure-the-logic-app-to-use-form-recognizer-train-model-operation"></a>De logische app configureren voor het gebruik van de vorm van een Train model-bewerking voor formulier herkenning

Voordat u de Form Recognizer-service kunt gebruiken voor het analyseren van facturen, moet u een model trainen door een aantal voor beelden van facturen te verstrekken die het model kan analyseren en leren van.

1. Selecteer **nieuwe stap**en zoek onder **Kies een actie**naar **formulier herkenner**. Selecteer in de resultaten die worden weer gegeven, de optie **formulier Recognizer**en selecteer vervolgens onder de acties die beschikbaar zijn voor de formulier herkenner, **Train model**.

    > [!div class="mx-imgBorder"]
    > ![een model voor het herleiden van een formulier maken](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-train-model.png)

1. Geef in het dialoog venster formulier herkenning een naam op voor de verbinding en voer de eind punt-URL in en de sleutel die u hebt opgehaald voor de formulier Recognizer-resource.

    > [!div class="mx-imgBorder"]
    > de naam van de ![-verbinding voor de herkenning van het formulier](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-create-connection.png)

    Klik op **Maken**.

1. Voer in het dialoog venster **Train model** , voor **bron**, de URL in voor de container waarnaar u de voorbeeld gegevens hebt geüpload.

    > [!div class="mx-imgBorder"]
    > Opslag container ![voor voorbeeld facturen](media/tutorial-form-recognizer-with-logic-apps/source-for-train-model.png)

1. Klik in de werk balk bovenaan op **Opslaan** .

### <a name="configure-the-logic-app-to-use-the-form-recognizer-analyze-form-operation"></a>De logische app configureren voor het gebruik van de bewerking formulier Recognizer analyseren formulier

In deze sectie voegt u de bewerking **formulier analyseren** toe aan de werk stroom. Met deze bewerking wordt het al getrainde model gebruikt voor het analyseren van een nieuwe factuur die aan de logische app wordt gegeven.

1. Selecteer **nieuwe stap**en zoek onder **Kies een actie**naar **formulier herkenner**. Selecteer in de resultaten die worden weer gegeven, de optie **formulier Recognizer**en selecteer vervolgens onder de acties die beschikbaar zijn voor de formulier herkenner, de optie **formulier analyseren**.

    > [!div class="mx-imgBorder"]
    > ![een model voor het herkenner van formulieren analyseren](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-analyze-model.png)

1. Ga als volgt te werk in het dialoog venster **formulier analyseren** :

    1. Klik op het tekstvak **model-id** en selecteer in het dialoog venster dat wordt geopend, op het tabblad **dynamische inhoud** de optie **modelId**. Als u dit doet, geeft u de stroom toepassing op met de model-ID van het model dat u in de laatste sectie hebt getraind.

        > [!div class="mx-imgBorder"]
        > de ModelID voor de formulier herkenner ![gebruiken](media/tutorial-form-recognizer-with-logic-apps/analyze-form-model-id.png)

    2. Klik op het tekstvak **document** en selecteer in het dialoog venster dat wordt geopend op het tabblad **dynamische inhoud** de optie **bijlagen inhoud**. Als u dit doet, configureert u de stroom om het voorbeeld factuur bestand te gebruiken dat is gekoppeld aan het e-mail bericht dat wordt verzonden om de werk stroom te activeren.

        > [!div class="mx-imgBorder"]
        > ![e-mail bijlage gebruiken om facturen te analyseren](media/tutorial-form-recognizer-with-logic-apps/analyze-form-input-data.png)

1. Klik in de werk balk bovenaan op **Opslaan** .

### <a name="extract-the-table-information-from-the-invoice"></a>De tabel informatie uit de factuur extra heren

In deze sectie configureren we de logische app voor het extra heren van de gegevens uit de tabel in de facturen.

1. Selecteer **een actie toevoegen**en selecteer onder **Kies een actie**zoeken naar **opstellen** en klik onder de beschik bare acties op opnieuw **samen stellen** .
    ![tabel gegevens uit de factuur ophalen](media/tutorial-form-recognizer-with-logic-apps/extract-table.png)

1. Klik in het dialoog venster **opstellen** op het tekstvak **invoer** en selecteer **tabellen**in het dialoog venster dat verschijnt.

    > [!div class="mx-imgBorder"]
    > ![tabel gegevens uit de factuur ophalen](media/tutorial-form-recognizer-with-logic-apps/select-tables.png)

1. Klik op **Opslaan**.

## <a name="test-your-logic-app"></a>Uw logische app testen

Als u de logische app wilt testen, gebruikt u de voor beelden van facturen in de map **/test** van de voor beeld-gegevensset die u hebt gedownload van [github](https://go.microsoft.com/fwlink/?linkid=2090451). Voer de volgende stappen uit:

1. Selecteer in de Azure Logic Apps Designer voor uw app **uitvoeren** in de werk balk bovenaan. De werk stroom is nu actief en wacht op het ontvangen van een e-mail waaraan de factuur is gekoppeld.
1. Een e-mail verzenden met een voorbeeld factuur die is gekoppeld aan het e-mail adres dat u hebt ingevoerd tijdens het maken van de logische app. Zorg ervoor dat het e-mail bericht wordt bezorgd in de map die u hebt ingevoerd tijdens het configureren van de logische app.
1. Zodra het e-mail bericht in de map wordt bezorgd, wordt in de Logic Apps Designer een scherm weer gegeven met de voortgang van elke fase. In de onderstaande scherm afbeelding ziet u dat er een e-mail bericht met een bijlage wordt ontvangen en dat de werk stroom wordt uitgevoerd.

    > [!div class="mx-imgBorder"]
    > de werk stroom ![starten door een e-mail](media/tutorial-form-recognizer-with-logic-apps/logic-apps-email-arrived-progress.png) te verzenden

1. Wanneer de uitvoering van alle fasen van de werk stroom is voltooid, wordt in de Logic Apps Designer een groen selectie vakje weer gegeven voor elke fase. Selecteer in het ontwerp venster **voor elke 2**en selecteer vervolgens **opstellen**.

    > [!div class="mx-imgBorder"]
    > ![werk stroom voltooid](media/tutorial-form-recognizer-with-logic-apps/logic-apps-verify-output.png)

    Kopieer de uitvoer in het vak **uitvoer** en plak deze in een tekst editor.

1. Vergelijk de JSON-uitvoer met de voorbeeld factuur die u als bijlage in het e-mail bericht hebt verzonden. Controleer of de JSON-gegevens overeenkomen met de gegevens in de tabel in de factuur.

    ```json
    [
      {
        "id": "table_0",
        "columns": [
          {
            "header": [
              {
                "text": "Invoice Number",
                "boundingBox": [
                  38.5,
                  585.2,
                  113.4,
                  585.2,
                  113.4,
                  575.8,
                  38.5,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "7689302",
                  "boundingBox": [
                    38.5,
                    549.8,
                    77.3,
                    549.8,
                    77.3,
                    536.2,
                    38.5,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Invoice Date",
                "boundingBox": [
                  139.7,
                  585.2,
                  198.5,
                  585.2,
                  198.5,
                  575.8,
                  139.7,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "3/09/2015",
                  "boundingBox": [
                    139.7,
                    548.1,
                    184,
                    548.1,
                    184,
                    536.2,
                    139.7,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Invoice Due Date",
                "boundingBox": [
                  240.5,
                  585.2,
                  321,
                  585.2,
                  321,
                  575.8,
                  240.5,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "6/29/2016",
                  "boundingBox": [
                    240.5,
                    549,
                    284.8,
                    549,
                    284.8,
                    536.2,
                    240.5,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Charges",
                "boundingBox": [
                  341.3,
                  585.2,
                  381.2,
                  585.2,
                  381.2,
                  575.8,
                  341.3,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "$22,123.24",
                  "boundingBox": [
                    380.6,
                    548.5,
                    430.5,
                    548.5,
                    430.5,
                    536.2,
                    380.6,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "VAT ID",
                "boundingBox": [
                  442.1,
                  590,
                  474.8,
                  590,
                  474.8,
                  575.8,
                  442.1,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "QR",
                  "boundingBox": [
                    447.7,
                    549.8,
                    462.6,
                    549.8,
                    462.6,
                    536.2,
                    447.7,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          }
        ]
      }
    ]
    ```
    U hebt deze zelf studie voltooid.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie stelt u een Azure Logic Apps werk stroom in voor het gebruik van de formulier herkenner om een model te trainen en de inhoud van een factuur uit te pakken. Vervolgens leert u hoe u een set trainings gegevens bouwt, zodat u een vergelijkbaar scenario met uw eigen formulieren kunt maken.

> [!div class="nextstepaction"]
> [Een set trainings gegevens bouwen](build-training-data-set.md)