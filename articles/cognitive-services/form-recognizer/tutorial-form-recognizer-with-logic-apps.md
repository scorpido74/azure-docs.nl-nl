---
title: 'Zelfstudie: Formulierherkenning gebruiken met Azure Logic Apps om facturen te analyseren - Formulierherkenning'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie gebruikt u Form Recognizer met Azure Logic Apps om een werkstroom te maken die het proces van het trainen van een model automatiseert en test met behulp van voorbeeldgegevens.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/27/2020
ms.author: nitinme
ms.openlocfilehash: d71d9c7e6570e562fe4c692ede1d07b70c923cb6
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77118264"
---
# <a name="tutorial-use-form-recognizer-with-azure-logic-apps-to-analyze-invoices"></a>Zelfstudie: Formulierherkenning gebruiken met Azure Logic Apps om facturen te analyseren

In deze zelfstudie maakt u een werkstroom in Azure Logic Apps die Form Recognizer gebruikt, een service die deel uitmaakt van de Azure Cognitive Services-suite, om gegevens uit facturen te extraheren. Eerst trainje je een Form Recognizer-model met een voorbeeldgegevensset en dan test je het model op een andere gegevensset.

Hier is wat deze tutorial heeft betrekking op:

> [!div class="checklist"]
> * Toegang aanvragen voor formulierherkenning
> * Een blobcontainer voor Azure Storage maken
> * Voorbeeldgegevens uploaden naar de Azure blob-container
> * Een Azure Logic-app maken
> * De logische app configureren om een bron voor formulierherkenning te gebruiken
> * De werkstroom testen door de logische app uit te voeren

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een.](https://azure.microsoft.com/free/)

## <a name="understand-the-invoice-to-be-analyzed"></a>De te analyseren factuur begrijpen

De voorbeeldgegevensset die u gebruikt om het model te trainen en te testen, is beschikbaar als een .zip-bestand van [GitHub.](https://go.microsoft.com/fwlink/?linkid=2090451) Download en haal het .zip-bestand en open een PDF-factuurbestand onder de map **/Train.** Let op: het heeft een tabel met het factuurnummer, factuurdatum, enzovoort. 

> [!div class="mx-imgBorder"]
> ![Voorbeeldfactuur](media/tutorial-form-recognizer-with-logic-apps/sample-receipt.png)

In deze zelfstudie leert u hoe u een Azure Logic Apps-werkstroom gebruiken om de informatie uit tabellen als deze in de JSON-indeling te extraheren.

## <a name="create-an-azure-storage-blob-container"></a>Een blobcontainer voor Azure Storage maken

U gebruikt deze container om voorbeeldgegevens te uploaden die nodig zijn om het model te trainen.

1. Volg de instructies in [Een Azure Storage-account maken](../../storage/common/storage-account-create.md) om een opslagaccount te maken. Gebruik **formrecostorage** als naam van het opslagaccount.
1. Volg de instructies in [Een Azure blobcontainer maken](../../storage/blobs/storage-quickstart-blobs-portal.md) om een container te maken in het Azure Storage-account. Gebruik **formrecocontainer** als containernaam. Zorg ervoor dat u het openbare toegangsniveau instelt op **Container (anonieme leestoegang voor containers en blobs).**

    > [!div class="mx-imgBorder"]
    > ![Blob-container maken](media/tutorial-form-recognizer-with-logic-apps/create-blob-container.png)

## <a name="upload-sample-data-to-the-azure-blob-container"></a>Voorbeeldgegevens uploaden naar de Azure blob-container

Download de voorbeeldgegevens die beschikbaar zijn op [GitHub.](https://go.microsoft.com/fwlink/?linkid=2090451) Haal de gegevens naar een lokale map en upload de inhoud van de map **/Train** naar de **formrecocontainer** die u eerder hebt gemaakt. Volg de instructies bij [Een blokblob uploaden](../../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob) om gegevens naar een container te uploaden.

Kopieer de URL van de container. Deze URL heb je later in de zelfstudie nodig. Als u het opslagaccount en de container hebt gemaakt met dezelfde namen als in deze zelfstudie, is de URL *\/https: /formrecostorage.blob.core.windows.net/formrecocontainer/*.

## <a name="create-a-form-recognizer-resource"></a>Een bron voor formulierherkenning maken

[!INCLUDE [create resource](./includes/create-resource.md)]

## <a name="create-your-logic-app"></a>Uw logische app maken

U Azure Logic Apps gebruiken om taken en werkstromen te automatiseren en te orkestreren. In deze zelfstudie maakt u een logische app die wordt geactiveerd door een factuur te ontvangen die u als e-mailbijlage wilt analyseren. In deze werkstroom voert u de volgende taken uit:
* Configureer de logische app om automatisch te activeren wanneer u een e-mail ontvangt met een factuur eraan gekoppeld.
* Configureer de logische app om een bewerking **Formulierherkenningstreinmodel** te gebruiken om een model te trainen met behulp van de voorbeeldgegevens die u hebt geüpload naar de Azure blob-opslag.
* Configureer de logische app om een **formulierherkenningsformulierbewerking** te gebruiken om het model te gebruiken dat u al hebt getraind. Dit onderdeel analyseert de factuur die u aan deze logische app verstrekt op basis van het model dat het eerder heeft getraind.

Volg deze stappen om uw werkstroom in te stellen.

1. Selecteer in het hoofdmenu van Azure de optie Een > **logica-app**voor > **bronintegratie** **maken**.

1. Onder **Logische app maken** geeft u informatie op over uw logische app zoals hier wordt weergegeven. Nadat u klaar bent, selecteert u **Maken**.

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Naam** | <*logische-app-naam*> | De naam van uw logische app, die alleen`-`letters, cijfers, koppeltekens`(`( `)`), onderstreept`.`(`_`), haakjes ( , en perioden ( ). In dit voorbeeld wordt gebruik gemaakt van "My-First-Logic-App". |
   | **Abonnement** | <*Azure-abonnementsnaam*> | De naam van uw Azure-abonnement |
   | **Resourcegroep** | <*Naam azure-resource-groep*> | De naam voor de [Azure-brongroep](./../../azure-resource-manager/management/overview.md) die wordt gebruikt om gerelateerde resources te ordenen. In dit voorbeeld wordt "My-First-LA-RG" gebruikt. |
   | **Locatie** | <*Azure-regio*> | De regio waar u uw logica-app-informatie opslaan. In dit voorbeeld wordt "West US" gebruikt. |
   | **Logboekanalyse** | Uit | Behoud de instelling **Uit** voor het vastleggen van diagnostische gegevens. |
   ||||

1. Nadat Azure uw app heeft geïmplementeerd, selecteert u op de werkbalk Azure de optie **Meldingen** > **Ga naar bron** voor uw geïmplementeerde logica-app. U ook uw logische app zoeken en selecteren door de naam in het zoekvak te typen.

   De Logic Apps-ontwerpfunctie wordt geopend en u ziet een pagina met een inleidende video en veelgebruikte triggers. Kies onder **Sjablonen** de optie **Lege logische app**.

   > [!div class="mx-imgBorder"]
   > ![Lege sjabloon selecteren voor logische app](./../../logic-apps/media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

### <a name="configure-the-logic-app-to-trigger-the-workflow-when-an-email-arrives"></a>De logische app configureren om de werkstroom te activeren wanneer een e-mail binnenkomt

In deze zelfstudie activeert u de werkstroom wanneer een e-mail wordt ontvangen met een bijgevoegde factuur. Deze zelfstudie gebruikt Office 365 als e-mailservice, maar u elke andere e-mailprovider gebruiken die u wilt gebruiken.

1. Selecteer in de tabbladen Alles, selecteer **Office 365 Outlook**en selecteer vervolgens onder **Triggers**de optie Wanneer een **nieuwe e-mail wordt weergegeven**.

    ![Logische app activeren via een binnenkomende e-mail](media/tutorial-form-recognizer-with-logic-apps/logic-app-email-trigger.png)

1. Klik in het vak **Office 365 Outlook** op **Aanmelden**en voer de gegevens in om u aan te melden bij een Office 365-account.

1. Voer in het volgende dialoogvenster de volgende stappen uit.
    1. Selecteer de map die moet worden gecontroleerd voor nieuwe e-mail.
    1. Selecteer **Ja**voor **bijlagen bij Heeft**. Dit zorgt ervoor dat alleen de e-mails met bijlagen de werkstroom activeren.
    1. Selecteer **Ja** **voor Bijlagen opnemen**. Dit zorgt ervoor dat de inhoud van de bijlage wordt gebruikt in downstream verwerking.

        > [!div class="mx-imgBorder"]
        > ![E-mailtrigger voor logische apps configureren](media/tutorial-form-recognizer-with-logic-apps/logic-app-specify-email-folder.png)

1. Klik **op Opslaan** vanaf de werkbalk bovenaan.

### <a name="configure-the-logic-app-to-use-form-recognizer-train-model-operation"></a>De logische app configureren om de bewerking Van formulierherkenningstreinmodel te gebruiken

Voordat u de form recognizer-service gebruiken om facturen te analyseren, moet u een model trainen door het een aantal voorbeeldfacturengegevens te verstrekken waarvan het model kan analyseren en van het model kan leren.

1. Selecteer **Nieuwe stap**en zoek onder Een **actie**kiezen naar **Formulierherkenning**. Selecteer **formulierherkenning**in de resultaten die worden weergegeven en selecteer vervolgens onder de acties die beschikbaar zijn voor Formulierherkenning de optie **Treinmodel**.

    > [!div class="mx-imgBorder"]
    > ![Een formulierherkenningsmodel trainen](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-train-model.png)

1. Geef in het dialoogvenster Formulierherkenning een naam voor de verbinding op en voer de URL van het eindpunt en de sleutel in die u hebt opgehaald voor de bron Formulierherkenning.

    > [!div class="mx-imgBorder"]
    > ![Verbindingsnaam voor formulierherkenning](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-create-connection.png)

    Klik **op Maken**.

1. Voer in het dialoogvenster **Treinmodel** voor **Bron**de URL in voor de container waar u de voorbeeldgegevens hebt geüpload.

    > [!div class="mx-imgBorder"]
    > ![Opslagcontainer voor voorbeeldfacturen](media/tutorial-form-recognizer-with-logic-apps/source-for-train-model.png)

1. Klik **op Opslaan** vanaf de werkbalk bovenaan.

### <a name="configure-the-logic-app-to-use-the-form-recognizer-analyze-form-operation"></a>De logische app configureren om de bewerking Formulierherkenning sanalyseerformulier te gebruiken

In deze sectie voegt u de bewerking **Formulier analyseren** toe aan de werkstroom. Deze bewerking maakt gebruik van het reeds getrainde model om een nieuwe factuur te analyseren die wordt geleverd aan de logische app.

1. Selecteer **Nieuwe stap**en zoek onder Een **actie**kiezen naar **Formulierherkenning**. Selecteer **formulierherkenning**en selecteer vervolgens onder de acties die beschikbaar zijn voor Formulierherkenning in de resultaten die worden weergegeven **formulier .**

    > [!div class="mx-imgBorder"]
    > ![Een formulierherkenningsmodel analyseren](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-analyze-model.png)

1. Ga in het dialoogvenster **Formulier analyseren** de volgende stappen uit:

    1. Klik op het tekstvak **Model-id** en selecteer in het dialoogvenster dat wordt geopend onder tabblad **Dynamische inhoud** **modelId**. Zo voorzie je de flowapplicatie van de model-ID van het model dat je in de laatste sectie hebt getraind.

        > [!div class="mx-imgBorder"]
        > ![De ModelID gebruiken voor formulierherkenning](media/tutorial-form-recognizer-with-logic-apps/analyze-form-model-id.png)

    2. Klik **op** het tekstvak Document en selecteer in het dialoogvenster dat wordt geopend onder tabblad **Dynamische inhoud** de optie **Inhoud bijlagen**. Hiermee configureert u de stroom om het voorbeeldfactuurbestand te gebruiken dat is gekoppeld aan de e-mail die de werkstroom activeert.

        > [!div class="mx-imgBorder"]
        > ![E-mailbijlage gebruiken om facturen te analyseren](media/tutorial-form-recognizer-with-logic-apps/analyze-form-input-data.png)

1. Klik **op Opslaan** vanaf de werkbalk bovenaan.

### <a name="extract-the-table-information-from-the-invoice"></a>De tabelgegevens uit de factuur extraheren

In deze sectie configureert u de logische app om de informatie uit de tabel binnen de facturen te extraheren.

1. Selecteer **Een actie toevoegen**en selecteer onder Een **actie**kiezen , Zoeken naar **Componeren** en selecteer onder de beschikbare acties **opnieuw componeren.**
    ![Tabelgegevens uit de factuur extraheren](media/tutorial-form-recognizer-with-logic-apps/extract-table.png)

1. Klik in het dialoogvenster **Samenmaken** op het **tekstvak Invoer** en selecteer in het dialoogvenster dat wordt weergegeven **tabellen**.

    > [!div class="mx-imgBorder"]
    > ![Tabelgegevens uit de factuur extraheren](media/tutorial-form-recognizer-with-logic-apps/select-tables.png)

1. Klik op **Opslaan**.

## <a name="test-your-logic-app"></a>Uw logische app testen

Als u de logische app wilt testen, gebruikt u de voorbeeldfacturen in de **map /Test** van de voorbeeldgegevensset die u hebt gedownload van [GitHub.](https://go.microsoft.com/fwlink/?linkid=2090451) Volg deze stappen:

1. Selecteer **Uitvoeren** op de werkbalk bovenaan in de ontwerper van Azure Logic Apps voor uw app. De werkstroom is nu actief en wacht op het ontvangen van een e-mail met de factuur bijgevoegd.
1. Stuur een e-mail met een voorbeeldfactuur die is gekoppeld aan het e-mailadres dat u hebt opgegeven tijdens het maken van de logische app. Zorg ervoor dat de e-mail wordt bezorgd in de map die u hebt opgegeven tijdens het configureren van de logische app.
1. Zodra de e-mail in de map wordt bezorgd, toont de Logic Apps Designer een scherm met de voortgang van elke fase. In de onderstaande schermafbeelding ziet u dat een e-mail met bijlage is ontvangen en dat de werkstroom aan de gang is.

    > [!div class="mx-imgBorder"]
    > ![De werkstroom starten door een e-mail te verzenden](media/tutorial-form-recognizer-with-logic-apps/logic-apps-email-arrived-progress.png)

1. Nadat alle fasen van de werkstroom zijn voltooid, toont de Logic Apps Designer een groen selectievakje voor elke fase. Selecteer voor elke **2 voor elke 2**en selecteer Vervolgens **Componeren**.

    > [!div class="mx-imgBorder"]
    > ![Werkstroom voltooid](media/tutorial-form-recognizer-with-logic-apps/logic-apps-verify-output.png)

    Kopieer de uitvoer in het vak **UITVOER** en plak deze naar een teksteditor.

1. Vergelijk de JSON-uitvoer met de voorbeeldfactuur die u als bijlage in de e-mail hebt verzonden. Controleer of de JSON-gegevens overeenkomen met de gegevens in de tabel in de factuur.

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
    Je hebt deze tutorial met succes voltooid!

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie stelt u een Azure Logic Apps-werkstroom in om Formulierherkenning te gebruiken om een model te trainen en de inhoud van een factuur te extraheren. Leer vervolgens hoe u een trainingsgegevensset maken, zodat u een vergelijkbaar scenario maken met uw eigen formulieren.

> [!div class="nextstepaction"]
> [Een set met trainingsgegevens samenstellen](build-training-data-set.md)