---
title: Informatie extraheren in Excel met text analytics en Power Automate
titleSuffix: Azure Cognitive Services
description: Leer hoe u Excel-tekst extraheren zonder code te hoeven schrijven, met Text Analytics en Power Automate.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/27/2019
ms.author: aahi
ms.openlocfilehash: fd70fe14d3765fb7c21b92f62b4d73564176baa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78201186"
---
# <a name="extract-information-in-excel-using-text-analytics-and-power-automate"></a>Informatie extraheren in Excel met text analytics en Power Automate 

In deze zelfstudie maakt u een stroom voor het automatiseren van stroom voor het extraheren van tekst in een Excel-spreadsheet zonder code te hoeven schrijven. 

Deze stroom zal een spreadsheet van problemen gemeld over een appartementencomplex, en classificeren ze in twee categorieën: sanitair en andere. Het zal ook de namen en telefoonnummers van de huurders die ze verzonden. Ten slotte wordt deze informatie in de stroom toegevoegd aan het Excel-blad. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Power Automate gebruiken om een stroom te maken
> * Excel-gegevens uploaden vanuit OneDrive voor Bedrijven
> * Tekst uit Excel extraheren en naar de API voor Text Analytics verzenden 
> * Gebruik de gegevens uit de API om een Excel-blad bij te werken.

## <a name="prerequisites"></a>Vereisten

- Een Microsoft Azure-account. [Start een gratis proefversie](https://azure.microsoft.com/free/) of [meld u aan](https://portal.azure.com/).
- Een Text Analytics-bron. Als u er geen hebt, u [er een maken in de Azure-portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) en de gratis laag gebruiken om deze zelfstudie te voltooien.
- Het [sleutel- en eindpunt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) dat voor u is gegenereerd tijdens het aanmelden.
- Een spreadsheet met tenantproblemen. Voorbeeldgegevens worden verstrekt op GitHub
- Office 365, met OneDrive voor Bedrijven.

## <a name="add-the-excel-file-to-onedrive-for-business"></a>Het Excel-bestand toevoegen aan OneDrive voor Bedrijven

Download het voorbeeld Excel-bestand van [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx). Dit bestand moet worden opgeslagen in uw OneDrive voor Bedrijven-account.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Voorbeelden uit het Excel-bestand.":::

De problemen worden gemeld in ruwe tekst. We gebruiken de naamentiteitsherkenning van de Text Analytics API om de naam en het telefoonnummer van de persoon te extraheren. Dan is de stroom zal kijken voor het woord "sanitair" in de beschrijving om de problemen te categoriseren. 

## <a name="create-a-new-power-automate-workflow"></a>Een nieuwe power automate-workflow maken

Ga naar de [Power Automate-site](https://preview.flow.microsoft.com/)en log in. Klik vervolgens op **Maken** en **Geplande stroom**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="Het scherm voor het maken van stromen.":::


Initialiseer op de pagina **Een geplande stroom** maken uw stroom in met de volgende velden:

|Veld |Waarde  |
|---------|---------|
|**Stroomnaam**     | **Geplande beoordeling** of een andere naam.         |
|**Beginnen**     |  Voer de huidige datum en tijd in.       |
|**Herhaal elke**     | **1 uur**        |

## <a name="add-variables-to-the-flow"></a>Variabelen toevoegen aan de stroom

> [!NOTE]
> Als u een afbeelding van de voltooide stroom wilt zien, u deze downloaden van [GitHub.](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/TextAnalytics/flow-diagrams) 

Maak variabelen die de informatie vertegenwoordigen die aan het Excel-bestand wordt toegevoegd. Klik op **Nieuwe stap** en zoek naar **Initialiserenvariabele**. Doe dit vier keer, om vier variabelen te maken.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="Variabelen initialiseren.":::

Voeg de volgende informatie toe aan de variabelen die u hebt gemaakt. Ze vertegenwoordigen de kolommen van het Excel-bestand. Als er variabelen zijn samengevouwen, u erop klikken om ze uit te vouwen.

| Actie |Name   | Type | Waarde |
|---------|---------|---|---|
| Variabele initialiseren | var_person | Tekenreeks | Person |
| Variabele 2 initialiseren | var_phone | Tekenreeks | Phone_Number |
| Variabele 3 initialiseren | var_plumbing | Tekenreeks | Loodgieters |
| Variabele 4 initialiseren | var_other | Tekenreeks | andere | 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="informatie in de stroomvariabelen":::

## <a name="read-the-excel-file"></a>Het excelbestand lezen

Klik **op Nieuwe stap** en typ **Excel**en selecteer **Vervolgens Lijstrijen die in een tabel aanwezig zijn** in de lijst met acties.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="Excel-rijen toevoegen.":::

Voeg het Excel-bestand toe aan de stroom door de velden in deze actie in te vullen. Voor deze zelfstudie moet het bestand zijn geüpload naar OneDrive voor Bedrijven.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="Excel-rijen toevoegen.":::

Klik op **Nieuwe stap** en voeg een toepassing toe **aan elke** actie.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="voeg een opdracht toepassen toe.":::

Klik op **Een uitvoer uit vorige stap selecteren**. Selecteer **waarde**in het vak Dynamische inhoud dat wordt weergegeven .

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="Selecteer uitvoer uit het Excel-bestand.":::

## <a name="send-a-request-to-the-text-analytics-api"></a>Een aanvraag verzenden naar de Text Analytics API

Als u dit nog niet hebt gedaan, moet u een [Text Analytics-bron](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) maken in de Azure-portal.

### <a name="create-a-text-analytics-connection"></a>Een Text Analytics-verbinding maken

Klik **in**de toepassing op elke actie op **Een actie toevoegen**. Ga naar de **sleutel- en eindpuntpagina** van uw Text Analytics-bron in de Azure-portal en ontvang het sleutel- en eindpunt voor uw Text Analytics-bron.

Voer in uw stroom de volgende gegevens in om een nieuwe Text Analytics-verbinding te maken.

> [!NOTE]
> Als u al een Text Analytics-verbinding hebt gemaakt en uw verbindingsgegevens wilt wijzigen, klikt u op de ellips in de rechterbovenhoek en klikt u op **+ Nieuwe verbinding toevoegen**.

| Veld           | Waarde                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| Verbindingsnaam | Een naam voor de verbinding met uw Text Analytics-bron. Bijvoorbeeld `TAforPowerAutomate`. |
| Accountsleutel     | De sleutel voor uw Text Analytics-bron.                                                                                   |
| Site-URL        | Het eindpunt voor uw Text Analytics-bron.                                                       |

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="Voeg text analytics-referenties toe aan uw stroom.":::

## <a name="extract-the-excel-content"></a>De excel-inhoud extraheren 

Nadat de verbinding is gemaakt, zoekt u naar **Text Analytics** en selecteert u **Entiteiten**. Hiermee wordt informatie uit de beschrijvingskolom van het probleem geëxtraheerd.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="Voeg text analytics-referenties toe aan uw stroom.":::

Klik in het veld **Tekst** en selecteer **Beschrijving** in de vensters dynamische inhoud die worden weergegeven. Voer `en` voor taal in. (Klik op Geavanceerde opties weergeven als u Taal niet ziet)

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="Voeg text analytics-referenties toe aan uw stroom.":::


## <a name="extract-the-person-name"></a>De naam van de persoon extraheren

Vervolgens vinden we het type persoon entiteit in de tekstanalyse-uitvoer. Klik in **de actie Toepassen op elke**actie op Een actie **toevoegen**en maak een andere toepassing **op elke** actie. Klik in het tekstvak en selecteer **Entiteiten** in het venster Dynamische inhoud dat wordt weergegeven.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="Voeg text analytics-referenties toe aan uw stroom.":::

Klik in de nieuw gemaakte **actie Toepassen op elke actie op** Een actie **toevoegen**en voeg een **besturingselement Voorwaarde** toe.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="Voeg text analytics-referenties toe aan uw stroom.":::

Klik in het venster Voorwaarde op het eerste tekstvak. Zoek in het venster Dynamische inhoud naar **Entiteiten Type** en selecteer deze.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="Voeg text analytics-referenties toe aan uw stroom.":::

Zorg ervoor dat het tweede vak is ingesteld **op .** Selecteer vervolgens het derde vak `var_person` en zoek naar in het venster Dynamische inhoud. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="Voeg text analytics-referenties toe aan uw stroom.":::

Typ in de voorwaarde **Als ja** in Excel en selecteer Een **rij bijwerken**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="Voeg text analytics-referenties toe aan uw stroom.":::

Voer de Excel-gegevens in en werk de velden **Sleutelkolom,** **Sleutelwaarde** en **Persoonsnaam** bij. Hiermee wordt de naam die door de API wordt gedetecteerd, toegevoegd aan het Excel-blad. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="Voeg text analytics-referenties toe aan uw stroom.":::

## <a name="get-the-phone-number"></a>Het telefoonnummer ophalen

Minimaliseer de **actie Toepassen op elke actie van 2** door op de naam te klikken. Voeg vervolgens een andere **toepassing toe op elke** actie, zoals voorheen. het zal worden genoemd **Van toepassing op elke 3**. Selecteer het tekstvak en voeg **entiteiten** toe als uitvoer voor deze actie. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-3.png" alt-text="Voeg text analytics-referenties toe aan uw stroom.":::

Voeg **binnen Toepassen op elke 3**een **conditiebesturingselement** toe. Het zal de naam **Voorwaarde 2**. Zoek in het eerste tekstvak naar en voeg **Entiteiten toe Tekst** vanuit het venster Dynamische inhoud. Zorg ervoor dat het middelste vak is ingesteld **op is gelijk aan**. Voer vervolgens in het juiste `var_phone`tekstvak . 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="Voeg text analytics-referenties toe aan uw stroom.":::

Voeg in de voorwaarde **Als ja** een **rijactie bijwerken** toe. Voer vervolgens de informatie in zoals we hierboven hebben gedaan, voor de kolom telefoonnummers van het Excel-blad. Hiermee wordt het telefoonnummer dat door de API wordt gedetecteerd, toegevoegd aan het Excel-blad. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="Voeg text analytics-referenties toe aan uw stroom.":::


## <a name="get-the-plumbing-issues"></a>Krijg de sanitair problemen

Minimaliseer **Van toepassing op elke 3** door op de naam te klikken. Maak vervolgens een andere **toepassing op elk** in de bovenliggende actie. Selecteer het tekstvak en voeg **Entiteiten** toe als uitvoer voor deze actie in het venster Dynamische inhoud. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-4.png" alt-text="Voeg text analytics-referenties toe aan uw stroom.":::


Vervolgens controleert de stroom of de probleembeschrijving in de excel-tabelrij het woord "sanitair" bevat. Zo ja, dan wordt er 'sanitair' toegevoegd in de kolom IssueType. Zo niet, dan gaan we 'andere' invoeren.

Voeg in de actie **Toepassen op elke actie 4** een **conditiebesturingselement** toe. Het zal de naam **Voorwaarde 3**. Zoek in het eerste tekstvak naar en voeg **Beschrijving** toe uit het Excel-bestand met het venster Dynamische inhoud. Zorg ervoor dat het middelste vak **bevat**. Zoek en selecteer `var_plumbing`vervolgens in het juiste tekstvak . 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="Voeg text analytics-referenties toe aan uw stroom.":::


Klik in de voorwaarde **Als ja** op Een **actie toevoegen**en selecteer Een **rij bijwerken**. Voer vervolgens de informatie in zoals voorheen. Selecteer in de kolom `var_plumbing`IssueType de optie . Dit zal een "sanitair" label toe te passen op de rij.

Klik **in** de voorwaarde Als er geen voorwaarde is, klik **op Een actie toevoegen**en selecteer **Een rij bijwerken**. Voer vervolgens de informatie in zoals voorheen. Selecteer in de kolom `var_other`IssueType de optie . Hiermee wordt een "ander" label op de rij aangebracht.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="Voeg text analytics-referenties toe aan uw stroom.":::

## <a name="test-the-workflow"></a>De werkstroom testen

Klik in de rechterbovenhoek van het scherm op **Opslaan**en **test .** Selecteer **Ik voer de triggeractie uit**. Klik **op & test**, run **flow**, vervolgens **Gereed**.

Het Excel-bestand wordt bijgewerkt in uw OneDrive-account. Het zal eruit zien als de onderstaande.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="De bijgewerkte excel-spreadsheet.":::

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Ontdek meer oplossingen](../text-analytics-user-scenarios.md)
