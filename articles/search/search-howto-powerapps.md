---
title: Azure Cognitive Search opvragen vanuit Power Apps
titleSuffix: Azure Cognitive Search
description: Stapsgewijze richtlijnen voor het maken van aangepaste connector voor cognitief zoeken en hoe u deze visualiseren vanuit een Power App
author: luiscabrer
manager: eladz
ms.author: luisca
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: c246f8652227a5ad2c0798880e530d6039cdeea8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385109"
---
# <a name="how-to-query-a-cognitive-search-index-from-power-apps"></a>Een cognitive search-index opvragen vanuit Power Apps

In dit document ziet u hoe u een aangepaste Power Apps-connector maakt, zodat u zoekresultaten uit een zoekindex ophalen. Het toont ook hoe u een zoekopdracht uitte en de resultaten van een Power App visualiseert. 

## <a name="prerequisites"></a>Vereisten
*    Toegang tot Power Apps-account met de mogelijkheid om aangepaste connectors te maken.
*    We gaan ervan uit dat u al een Azure Search Index hebt gemaakt.

## <a name="create-a-custom-connector-to-query-azure-search"></a>Een aangepaste connector maken voor query Azure Search

Er zijn twee belangrijke stappen voor het hebben van een PowerApp met Azure Cognitive Search-resultaten. Laten we eerst een connector maken die de zoekindex kan opvragen. In het [volgende gedeelte](#visualize-results-from-the-custom-connector) werken we uw Power Apps-toepassing bij om de resultaten van de connector te visualiseren.

1. Ga naar [make.powerapps.com](http://make.powerapps.com) en **Meld je aan.**

1. Zoeken naar**aangepaste gegevensconnectoren** **Data** > 
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="Menu Aangepaste connector" border="true":::

1. Klik **op + Nieuwe aangepaste connector** en selecteer Maken van **leeg**.

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="Maken vanuit leeg menu" border="true":::

1. Geef uw aangepaste connector een naam. (dat wil zeggen *AzureSearchQuery)* en klik vervolgens op **Doorgaan**. Hiermee wordt een wizard weergegeven om uw nieuwe connector te maken.

1. Voer gegevens in op de algemene pagina.

    - Achtergrondkleur pictogram (bijvoorbeeld #007ee5)
    - Beschrijving (bijvoorbeeld 'Een connector voor Azure Cognitive Search')
    - In de host moet u de URL van uw `<yourservicename>.search.windows.net`zoekservice invoeren (bijvoorbeeld )
    - Voer voor basis-URL gewoon "/" in
    
    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="Algemene informatiedialoog" border="true":::

1. Stel op de beveiligingspagina *API-sleutel* in als **verificatietype**, stel het parameterlabel en parameternaamvelden in als *api-toets*. Selecteer *Koptekst* zoals hieronder wordt weergegeven voor **parameterlocatie.**
 
    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="Optie Verificatietype" border="true":::

1. Selecteer op de pagina Definities de optie **+ Nieuwe actie** om een actie te maken die de index opvraagt. Voer de waarde 'Query' in voor het overzicht en de naam van de bewerkings-id. Voer een beschrijving in zoals *'Query's de zoekindex'.*
 
    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="Nieuwe actieopties" border="true":::


1. Druk op de knop **+ Importeren uit voorbeeld** om de parameters en kopteksten te definiëren. Vervolgens definieert u de queryaanvraag.  

    * Het werkwoord selecteren`GET`
    * Voer voor de URL bijvoorbeeld een voorbeeldquery in voor uw zoekindex:
       
    >https://yoursearchservicename.search.windows.net/indexes/yourindexname/docs?search=*&api-versie=2019-05-06-Preview
    

    **Power Apps** gebruikt de syntaxis om parameters uit de query te extraheren. Let op, we hebben het zoekveld expliciet gedefinieerd. 

    :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="Importeren vanuit voorbeeld" border="false":::

1.  Klik **op Importeren** om het dialoogvenster Aanvraag automatisch vooraf in te vullen.

    :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="Importeren uit voorbeelddialoog" border="false":::


1. Voltooi het instellen van de parameter metadata door te klikken op de **...** symbool naast elk van de parameters.

    - Voor *zoeken:* Stel in `*` als de **standaardwaarde**, stel **de vereiste** in als *onwaar* en stel de **zichtbaarheid** in op *geen*. 

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="Metagegevens van de parameter zoeken" border="true":::

    - Voor *api-versie* `2019-05-06-Preview` : Stel in als **standaardwaarde,** stel de **zichtbaarheid** in als intern en stel **de ingestelde in die vereist is** op *True*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="Metagegevens van versieparameter" border="true":::

    - Evenzo, voor *api-key*, stel het in indien **nodig**, met *interne* **zichtbaarheid**. Voer de API-sleutel van uw zoekservice in als **de standaardwaarde**.
    
    Nadat u deze wijzigingen hebt aangebracht, schakelt u in op de weergave **Swagger Editor.** In de sectie parameters ziet u de volgende configuratie:    

    ```
          parameters:
          - {name: search, in: query, required: false, type: string, default: '*'}
          - {name: api-version, in: query, required: true, type: string, default: 2019-05-06-Preview,
            x-ms-visibility: internal}
          - {name: api-key, in: header, required: true, type: string, default: YOURKEYGOESHERE,
            x-ms-visibility: internal}
    ```

1. Klik in de sectie Antwoord op **'Standaardantwoord toevoegen'.** Dit is van cruciaal belang omdat Power **Apps** hiermee inzicht krijgt in het schema van de reactie. Een voorbeeldreactie plakken.

    > [!TIP] 
    > Er is een tekenlimiet voor de JSON-respons die u invoeren, dus u de JSON vereenvoudigen zodat deze wordt geplakt voordat u deze plakt. Het belangrijke aspect schema/formaat van de respons. De werkelijke waarden in de voorbeeldrespons zijn minder belangrijk en kunnen worden vereenvoudigd om het aantal tekens te verminderen.
    

1.    Klik rechtsboven in het scherm op de knop **Connector maken** voordat u het testen.

1.  Klik op de testpagina op de **+ nieuwe verbinding**en voer de querysleutel van uw zoekservice in als waarde voor *api-toets.*

    Met deze stap u de wizard uit en naar de pagina Verbindingen brengen. Misschien wilt u teruggaan naar de editor Aangepaste verbindingen om de verbinding daadwerkelijk te testen. Ga naar **Aangepaste connector** > Selecteer de nieuw gemaakte connector > *...* > **Eigenschappen weergeven** > **Bewerken** > **4. Test** om terug te gaan naar de testpagina.

1.    Klik nu op **Testbewerking** om ervoor te zorgen dat u resultaten van uw index krijgt. Als u succesvol was, ziet u een status van 200 en in de hoofdtekst van de reactie ziet u JSON die uw zoekresultaten beschrijft.




## <a name="visualize-results-from-the-custom-connector"></a>Resultaten van de aangepaste connector visualiseren
Het doel van deze zelfstudie is niet om u te laten zien hoe u fancy gebruikerservaringen maken met power-apps, zodat de ui-lay-out minimalistisch zal zijn. Laten we een PowerApp maken met een zoekvak, een zoekknop en de resultaten weergeven in een galeriebesturingselement.  De PowerApp maakt verbinding met onze onlangs gemaakte aangepaste connector om de gegevens van Azure Search te halen.

1. Maak een nieuwe Power App. Ga naar de sectie **Apps,** klik op **+ Nieuwe app**en selecteer **Canvas**.

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="Canvas-app maken" border="true":::

1. Selecteer het type toepassing dat u wilt. Maak voor deze zelfstudie een **lege app** met de **telefoonindeling**. De **Power Apps Studio** verschijnt.

1. Eenmaal in de studio selecteert u het tabblad **Gegevensbronnen** en klikt u op de nieuwe connector die u zojuist hebt gemaakt. In ons geval heet het *AzureSearchQuery*. Klik **op Een verbinding toevoegen**.

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="verbindingsconnector" border="true":::

    *AzureSearchQuery* is nu een gegevensbron die beschikbaar is om vanuit uw toepassing te worden gebruikt.
    
1. Navigeer naar het **tabblad Invoegen,** zodat we een paar besturingselementen aan ons formulier kunnen toevoegen.

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="Besturingselementen invoegen" border="true":::

1.  De volgende elementen invoegen:
    -   Een tekstlabel met de waarde 'Query:'
    -   Een element Tekstinvoer (noem het *txtQuery*, standaardwaarde: "*")
    -   Een knop met de tekst "Zoeken" 
    -   Een verticale galerie genaamd (noem het *galleryResults)*
    
    Uw formulier moet er ongeveer zo uitzien:

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="Indeling voor besturingselementen" border="true":::

1. Als u wilt dat de **knop Zoeken** een query uitgeeft, selecteert u de knop en plakt u de volgende actie die u op **OnSelect**wilt uitvoeren:

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Get({search: txtQuery.Text}).value))
    ```

    :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="Knop OnSelect" border="true":::
 
    Met deze actie wordt de knop ertoe geleid dat een nieuwe verzameling met de naam *azResult* wordt bijgewerkt met het resultaat van de zoekopdracht, waarbij de tekst in het tekstvak *txtQuery* als queryterm wordt gebruikt.
    
1.  Als volgende stap koppelen we de verticale galerij die we hebben gemaakt aan de *azResult-collectie.* Selecteer het galeriebesturingselement en voer de volgende acties uit in het deelvenster Eigenschappen.

    -  **DataSource** instellen op *azResult*.
    
    -  Selecteer een **indeling** die voor u werkt op basis van het type gegevens in uw index. In dit geval gebruikten we de *titel, ondertitel en body* layout.
    
    -  **Velden bewerken**en de velden selecteren die u wilt visualiseren.

    Aangezien we een voorbeeldresultaat hebben gegeven toen we de connector hebben gedefinieerd, is de app op de hoogte van de velden die beschikbaar zijn in uw index.
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="Galerievelden" border="true":::   
 
1.  Druk op **F5** om een voorbeeld van de app te bekijken.  

    Houd er rekening mee dat de velden kunnen worden ingesteld op berekende waarden.      
    Als u bijvoorbeeld de indeling *'Afbeelding, titel en ondertitel'* instelt en de *functie Afbeelding* opgeeft als samenvoeging `"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`van het hoofdpad voor de gegevens en de bestandsnaam (bijvoorbeeld) wordt het resultaat hieronder weergegeven.

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="Definitieve app" border="true":::        

## <a name="next-steps"></a>Volgende stappen

Zie Power Apps Learning [Catalog](https://docs.microsoft.com/powerapps/learning-catalog/get-started)voor meer informatie en online training.

