---
title: 'Zelf studie: een query uitvoeren vanuit Power apps'
titleSuffix: Azure Cognitive Search
description: Stapsgewijze richt lijnen voor het bouwen van een Power-app die verbinding maakt met een Azure Cognitive Search-index, query's verzendt en resultaten weergeeft.
author: luiscabrer
manager: eladz
ms.author: luisca
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: tutorial
ms.date: 04/25/2020
ms.openlocfilehash: 2a2e292390b2f060bf31d739605d7506203a5619
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901405"
---
# <a name="tutorial-query-a-cognitive-search-index-from-power-apps"></a>Zelf studie: een Cognitive Search-index uit Power-apps opvragen

Maak gebruik van de Rapid Application Development omgeving van Power apps voor het maken van een aangepaste app voor uw Doorzoek bare inhoud in azure Cognitive Search.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Verbinding maken met Azure Cognitive Search
> * Een query aanvraag instellen
> * Resultaten in een canvas-app visualiseren

Als u geen Azure-abonnement hebt, opent u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* [Account voor Power-apps](http://make.powerapps.com)

* [Hotels-voor beeld-index](search-get-started-portal.md)

* [Query-API-sleutel](search-security-api-keys.md#find-existing-keys)

## <a name="1---create-a-custom-connector"></a>1-een aangepaste connector maken

Een connector in Power apps is een gegevens bron verbinding. In deze stap maakt u een aangepaste connector om verbinding te maken met een zoek index in de Cloud.

1. [Meld](http://make.powerapps.com) u aan bij Power apps.

1. Klik aan de linkerkant op **gegevens** > **aangepaste connectors**.
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="Menu aangepaste connector" border="true":::

1. Selecteer **+ nieuwe aangepaste connector**en selecteer vervolgens **leeg item maken**.

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="Maken uit leeg menu" border="true":::

1. Geef uw aangepaste connector een naam (bijvoorbeeld *AzureSearchQuery*) en klik vervolgens op **door gaan**.

1. Voer informatie in op de pagina Algemeen:

   * Achtergrond kleur van het pictogram (#007ee5)
   * Beschrijving (bijvoorbeeld ' een connector naar Azure Cognitive Search ')
   * Op de host moet u de URL van uw zoek service invoeren (bijvoorbeeld `<yourservicename>.search.windows.net`)
   * Voer voor basis-URL gewoon in "/"

    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="Dialoog venster Algemene informatie" border="true":::

1. Stel op de pagina Beveiliging de *API-sleutel* in als het **verificatie type**en stel zowel het parameter label als de parameter naam in op de *API-sleutel*. Selecteer voor **parameter locatie** *koptekst* zoals hieronder wordt weer gegeven.

    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="Optie voor verificatie type" border="true":::

1. Selecteer op de pagina definities **+ nieuwe actie** om een actie te maken waarmee een query wordt uitgevoerd op de index. Voer de waarde ' query ' in voor de samen vatting en de naam van de bewerkings-ID. Voer een beschrijving in, zoals *' query's uitvoeren op de zoek index '*.

    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="Opties voor nieuwe acties" border="true":::

1. Schuif omlaag. Selecteer in aanvragen **+ importeren van de voorbeeld** knop om een query aanvraag naar uw zoek service te configureren:

   * De bewerking selecteren`GET`

   * Voer voor de URL een voorbeeld query in voor uw zoek index`search=*` (retourneert alle documenten `$select=` , kunt u velden kiezen). De API-versie is vereist. Volledig opgegeven, een URL kan er als volgt uitzien:`https://mydemo.search.windows.net/indexes/hotels-sample-index/docs?search=*&$select=HotelName,Description,Address/City&api-version=2019-05-06`

   * Typ `Content-Type`voor kopteksten. 

     In **Power apps** wordt de syntaxis gebruikt om para meters uit de query te halen. U ziet dat het zoek veld expliciet is gedefinieerd. 

       :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="Importeren vanuit voorbeeld" border="true":::

1. Klik op **importeren** om de aanvraag automatisch in te vullen. Voltooi de instelling van de meta gegevens van de para meter door te klikken op het symbool **..** . naast elk van de para meters. Klik op **terug** om terug te keren naar de pagina aanvraag na elke para meter-update.

   :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="Importeren uit voorbeeld dialoogvenster" border="true":::

1. Voor *zoeken*: Stel `*` in op de **standaard waarde**, stel **vereist** in op *Onwaar* en stel **zicht baarheid** in op *geen*. 

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="Meta gegevens van zoek parameter" border="true":::

1. Voor *Select*: instellen `HotelName,Description,Address/City` als **standaard waarde**, stel **vereist** in op *False*en stel **zicht baarheid** in op *geen*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-4-parameter-metadata-select.png" alt-text="Meta gegevens van de versie parameter" border="true":::

1. Voor *API-Version*: Stel `2019-05-06` in als de **standaard waarde**, stel **vereist** in op *True*en stel **zicht baarheid** in als *intern*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="Meta gegevens van de versie parameter" border="true":::

1. Voor *type inhoud*: Stel in op `application/json`.

1. Nadat u deze wijzigingen hebt aangebracht, schakelt u over naar de weer gave **Swagger-editor** . In het gedeelte para meters ziet u de volgende configuratie:

    ```JSON
    parameters:
      - {name: search, in: query, required: false, type: string, default: '*'}
      - {name: $select, in: query, required: false, type: string, default: 'HotelName,Description,Address/City'}
      - {name: api-version, in: query, required: true, type: string, default: '2019-05-06',
        x-ms-visibility: internal}
      - {name: Content-Type, in: header, required: false, type: string}
    ```

1. Keer terug naar de **3. Aanvraag** stap en schuif omlaag naar de sectie antwoord. Klik op **standaard reactie toevoegen**. Dit is essentieel omdat de Power-apps het schema van het antwoord beter begrijpen. 

1. Plak een voorbeeld reactie. Een eenvoudige manier om een voor beeld van een antwoord vast te leggen is via Search Explorer in de Azure Portal. Voer in Search Explorer dezelfde query in als voor de aanvraag, maar Voeg **$Top = 2** toe om de resultaten te beperken tot slechts twee documenten:: `search=*&$select=HotelName,Description,Address/City&$top=2`. 

   Power apps heeft slechts enkele resultaten nodig om het schema te detecteren.

    ```JSON
    {
        "@odata.context": "https://mydemo.search.windows.net/indexes('hotels-sample-index')/$metadata#docs(*)",
        "value": [
            {
                "@search.score": 1,
                "HotelName": "Arcadia Resort & Restaurant",
                "Description": "The largest year-round resort in the area offering more of everything for your vacation – at the best value!  What can you enjoy while at the resort, aside from the mile-long sandy beaches of the lake? Check out our activities sure to excite both young and young-at-heart guests. We have it all, including being named “Property of the Year” and a “Top Ten Resort” by top publications.",
                "Address": {
                    "City": "Seattle"
                }
            },
            {
                "@search.score": 1,
                "HotelName": "Travel Resort",
                "Description": "The Best Gaming Resort in the area.  With elegant rooms & suites, pool, cabanas, spa, brewery & world-class gaming.  This is the best place to play, stay & dine.",
                "Address": {
                    "City": "Albuquerque"
                }
            }
        ]
    }
    ```

    > [!TIP] 
    > Er is een teken limiet voor het JSON-antwoord dat u kunt invoeren, dus wilt u de JSON vereenvoudigen voordat u deze plakt. Het schema en de indeling van het antwoord zijn belang rijker dan de waarden zelf. Het veld Beschrijving kan bijvoorbeeld worden vereenvoudigd tot alleen de eerste zin.

1. Klik in de rechter bovenhoek op **connector maken** .

## <a name="2---test-the-connection"></a>2-de verbinding testen

Wanneer de connector voor het eerst wordt gemaakt, moet u deze opnieuw openen in de lijst met aangepaste connectors om deze te testen. Als u later extra updates wilt uitvoeren, kunt u in de wizard testen.

U hebt een [query-API-sleutel](search-security-api-keys.md#find-existing-keys) voor deze taak nodig. Telkens wanneer een verbinding wordt gemaakt, of voor een test uitvoering of-opname in een app, moet de connector de query-API-sleutel hebben die wordt gebruikt om verbinding te maken met Azure Cognitive Search.

1. Klik helemaal links op **aangepaste connectors**.

1. Zoek naar de connector op naam (in deze zelf studie is ' AzureSearchQuery ').

1. Selecteer de connector, vouw de lijst acties uit en selecteer **Eigenschappen weer geven**.

    :::image type="content" source="./media/search-howto-powerapps/1-11-1-test-connector.png" alt-text="Eigenschappen weergeven" border="true":::

1. Selecteer **bewerken** in de rechter bovenhoek.

1. Selecteer **4. Test** om de pagina test te openen.

1. Klik in test bewerking op **+ nieuwe verbinding**.

1. Voer een query-API-sleutel in. Dit is een Azure Cognitive Search-query voor alleen-lezen toegang tot een index. U kunt [de sleutel vinden](search-security-api-keys.md#find-existing-keys) in de Azure Portal. 

1. Klik in bewerkingen op de knop **test bewerking** . Als u bent geslaagd, ziet u de status 200 en in de hoofd tekst van het antwoord ziet u JSON waarin de zoek resultaten worden beschreven.

    :::image type="content" source="./media/search-howto-powerapps/1-11-2-test-connector.png" alt-text="JSON-antwoord" border="true":::

## <a name="3---visualize-results"></a>3-resultaten visualiseren

In deze stap maakt u een Power-app met een zoekvak, een zoek knop en een weergave gebied voor de resultaten. De Power-app maakt verbinding met de onlangs gemaakte aangepaste connector om de gegevens op te halen uit Azure Search.

1. Vouw aan de linkerkant **apps** > **+ nieuwe app** > -**canvas**uit.

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="Een canvas-app maken" border="true":::

1. Selecteer het type toepassing. Voor deze zelf studie maakt u een **lege app** met de **telefoon indeling**. De **Power apps Studio** wordt weer gegeven.

1. In de Studio selecteert u het tabblad **gegevens bronnen** en klikt u op de nieuwe connector die u zojuist hebt gemaakt. In ons geval wordt dit *AzureSearchQuery*genoemd. Klik op **een verbinding toevoegen**.

   Voer de query-API-sleutel in.

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="connector verbinden" border="true":::

    Nu *AzureSearchQuery* is een gegevens bron die beschikbaar is voor gebruik vanuit uw toepassing.

1. Voeg op het **tabblad invoegen**een paar besturings elementen toe aan het canvas.

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="Besturings elementen invoegen" border="true":::

1. Voeg de volgende elementen in:

   * Een tekst label met de waarde ' query: '
   * Een INPUT-element voor tekst (aanroep het *txtQuery*, standaard waarde: ' * ')
   * Een knop met de tekst ' zoeken ' 
   * Een verticale galerie met de naam (noem deze *galleryResults*)

    Het canvas moet er ongeveer als volgt uitzien:

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="Indeling van besturings elementen" border="true":::

1. Als u wilt dat de **knop Zoeken** een query geeft, plakt u de volgende actie in **onselect**:

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Query({search: txtQuery.Text}).value))
    ```

   De volgende scherm afbeelding toont de formule balk voor de actie **onselect** .

    :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="Knop onselect" border="true":::

   Deze actie zorgt ervoor dat de knop een nieuwe verzameling met de naam *azResult* bijwerkt met het resultaat van de zoek opdracht, met behulp van de tekst in het tekstvak *txtQuery* als query term.

   > [!NOTE]
   > Probeer het opnieuw als u een syntaxis fout van de formule krijgt: de functie ClearCollect heeft enkele ongeldige functies.
   > 
   > * Controleer eerst of de verwijzing naar de connector juist is. Wis de naam van de connector en begin met het typen van de naam van de connector. IntelliSense moet de juiste connector en term Voorst Ellen.
   > 
   > * Als de fout zich blijft voordoen, verwijdert u de connector en maakt u deze opnieuw. Als er meerdere exemplaren van een connector zijn, is het mogelijk dat de app de verkeerde versie gebruikt.
   > 

1. Koppel het besturings element verticaal galerie aan de *azResult* -verzameling die is gemaakt toen u de vorige stap voltooide. 

   Selecteer het besturings element galerie en voer de volgende acties uit in het deel venster Eigenschappen.

   * Stel **Data Source** in op *azResult*.
   * Selecteer een **indeling** die geschikt is voor u op basis van het type gegevens in de index. In dit geval hebben we de indeling *titel, subtitel en hoofd tekst* gebruikt.
   * **Bewerk velden**en selecteer de velden die u wilt visualiseren.

    Omdat we een voorbeeld resultaat hebben opgegeven toen we de connector hebben gedefinieerd, is de app op de hoogte van de beschik bare velden in uw index.
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="Galerievelden" border="true":::   
 
1. Druk op **F5** om de app te bekijken.  

    :::image type="content" source="./media/search-howto-powerapps/2-8-3-final.png" alt-text="Definitieve app" border="true":::    

<!--     Remember that the fields can be set to calculated values.

    For the example, setting using the *"Image, Title and Subtitle"* layout and specifying the *Image* function as the concatenation of the root path for the data and the file name (for instance, `"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`) will produce the result below.

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="Final app" border="true":::         -->

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de resources die u hebt gemaakt, nog nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal met behulp van de koppeling **alle resources** of **resource groepen** in het navigatie deel venster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, Indexeer functies en gegevens bronnen. U kunt afzonderlijke items in de Portal verwijderen om de limiet te blijven.

## <a name="next-steps"></a>Volgende stappen

Met Power apps kunnen aangepaste apps worden Rapid Application Development. Nu u weet hoe u verbinding kunt maken met een zoek index, leest u meer over het maken van een geavanceerde visualiseren ervaring in een aangepaste Power-app.

> [!div class="nextstepaction"]
> [Power apps-leer catalogus](https://docs.microsoft.com/powerapps/learning-catalog/get-started)

