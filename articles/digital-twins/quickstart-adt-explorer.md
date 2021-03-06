---
title: Snelstart - Een voorbeeldscenario verkennen
titleSuffix: Azure Digital Twins
description: Quickstart - Gebruik het ADT Explorer-voorbeeld om een vooraf ontwikkeld scenario te visualiseren en te verkennen.
author: baanders
ms.author: baanders
ms.date: 9/24/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: d203cb5ccef90fd09659ba64b7bcbc8b9be9e47a
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358072"
---
# <a name="quickstart---explore-a-sample-azure-digital-twins-scenario-using-adt-explorer"></a>Snelstart - Een voorbeeld van een Azure Digital Twins-scenario verkennen met behulp van ADT Explorer

Met Azure Digital Twins kunt u live modellen van uw echte omgevingen maken en gebruiken. Eerst modelleert u afzonderlijke elementen als *digitale dubbels*. Vervolgens koppelt u deze aan een *kennisgraaf* die kan reageren op livegebeurtenissen en waarop query's kunnen worden uitgevoerd om informatie op te halen.

In deze quickstart verkent u een vooraf gebouwde Azure Digital Twins-graaf, met behulp van een voorbeeldtoepassing genaamd [Azure Digital Twins (ADT) Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). U gebruikt ADT Explorer om het volgende te doen:

- Een digitale weergave van een omgeving uploaden.
- Visuele afbeeldingen bekijken van de dubbels en de graaf die zijn gemaakt als representatie van de omgeving in Azure Digital Twins.
- Andere beheeractiviteiten uitvoeren via visuele besturingselementen op basis van een browser.

De quickstart bevat de volgende hoofdstappen:

1. Een Azure Digital Twins-exemplaar en ADT Explorer instellen.
1. Vooraf gemaakte modellen en graafgegevens uploaden om het voorbeeldscenario samen te stellen.
1. De gemaakte scenariograaf verkennen.
1. Wijzigingen aanbrengen in de graaf.

De voorbeeldgraaf waarmee u gaat werken, vertegenwoordigt een gebouw met twee verdiepingen en twee kamers. De graaf ziet eruit als deze afbeelding:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Weergave van een graaf met vier ronde knooppunten die zijn verbonden via pijlen. Een cirkel met de naam Floor1 (Verdieping1) is via een pijl met het label 'contains' verbonden met een cirkel met het label Room1 (Kamer1). Een cirkel met de naam Floor0 (Verdieping0) is via een pijl met het label 'contains' verbonden met een cirkel met het label Room0 (Kamer0). Floor1 en Floor0 zijn niet verbonden.":::

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-abonnement nodig om deze quickstart te voltooien. Als u er nog geen hebt, kunt u er [nu gratis een maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

U moet ook **Node.js** op uw computer hebben. Zie [Node.js](https://nodejs.org/) als u de nieuwste versie wilt downloaden.

Tenslotte moet u ook het voorbeeld downloaden om te gebruiken tijdens de quickstart. De voorbeeldtoepassing is **ADT Explorer**. Dit voorbeeld bevat de app die u in de quickstart gebruikt om een Azure Digital Twins-scenario te laden en te verkennen. Het bevat ook de voorbeeldscenariobestanden. Als u het voorbeeld wilt downloaden, gaat u naar [Azure Digital Twins (ADT) Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Selecteer de knop **Download ZIP** om een zipbestand met deze voorbeeldcode naar uw machine te downloaden. Pak de map **Azure_Digital_Twins__ADT__explorer.zip** uit en extraheer de bestanden.

## <a name="set-up-azure-digital-twins-and-adt-explorer"></a>Azure Digital Twins en ADT Explorer instellen

De eerste stap bij het werken met Azure Digital Twins is het instellen van een Azure Digital Twins-instantie. Nadat u een instantie van de service hebt gemaakt en uw referenties hebt ingesteld om u te verifiëren met ADT Explorer, kunt u verbinding maken met de instantie in ADT Explorer en deze vervolgens vullen met de voorbeeldgegevens die verderop in quickstart worden vermeld.

In de rest van deze sectie krijgt u stapsgewijze instructies.

### <a name="set-up-an-azure-digital-twins-instance"></a>Een Azure Digital Twins-instantie instellen

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="set-up-local-azure-credentials"></a>Lokale Azure-referenties instellen

In de ADT Explorer-toepassing wordt gebruikgemaakt van [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (onderdeel van de `Azure.Identity`-bibliotheek) om gebruikers te verifiëren bij de Azure Digital Twins-instantie wanneer u deze uitvoert op uw lokale machine. Voor meer informatie over verschillende manieren waarop een client-app kan verifiëren met Azure Digital Twins, raadpleegt u [Verificatiecode voor app schrijven](how-to-authenticate-client.md).

Met dit verificatietype zoekt ADT Explorer naar referenties in uw lokale omgeving, zoals een Azure-aanmelding in een lokale [Azure-CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) of in Visual Studio of Visual Studio Code. Daarom moet u zich *lokaal aanmelden bij Azure* via een van deze mechanismen om referenties in te stellen voor de ADT Explorer-app.

Als u al op een van deze manieren bent aangemeld bij Azure, kunt u doorgaan naar de [volgende sectie](#run-and-configure-adt-explorer).

Als dat niet het geval is, kunt u de lokale Azure CLI installeren met de volgende stappen:

1. Volg het proces op [deze installatiekoppeling](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) om de installatie te voltooien die past bij uw besturingssysteem.
1. Open een consolevenster op uw computer.
1. Voer `az login` uit en volg de verificatieprompts om u aan te melden bij uw Azure-account.

Nadat u zich hebt aangemeld, zouden uw Azure-referenties automatisch door ADT Explorer moeten worden opgehaald wanneer u deze in de volgende sectie gaat uitvoeren.

U kunt het verificatieconsolevenster desgewenst sluiten. U kunt het venster ook geopend laten om het te gebruiken in de volgende stap.

### <a name="run-and-configure-adt-explorer"></a>ADT Explorer uitvoeren en configureren

Voer vervolgens de toepassing ADT Explorer uit en configureer deze voor uw Azure Digital Twins-exemplaar.

1. Ga naar de gedownloade en uitgepakte map **Azure_Digital_Twins__ADT__explorer**.
Open een consolevenster op de maplocatie **Azure_Digital_Twins__ADT__explorer/client/src**.

1. Voer `npm install` uit om alle vereiste afhankelijkheden te downloaden.

1. Start de app met de opdracht `npm run start`.

   Na een paar seconden wordt een browservenster geopend en wordt de app weergegeven in de browser.

   :::image type="content" source="media/quickstart-adt-explorer/explorer-blank.png" alt-text="Browservenster met een app die wordt uitgevoerd op localhost:3000. De app heet ADT Explorer en bevat de vakken Query Explorer, Model View, Graph View en Property Explorer. Er zijn nog geen gegevens op het scherm." lightbox="media/quickstart-adt-explorer/explorer-blank.png":::

1. Klik rechts bovenaan het venster (te zien in de volgende afbeelding) op de knop **Aanmelden** om ADT Explorer zo te configureren dat het werkt met het exemplaar dat u hebt ingesteld.

   :::image type="content" source="media/quickstart-adt-explorer/sign-in.png" alt-text="ADT Explorer met het pictogram Sign In (Aanmelden) gemarkeerd bovenaan het venster. Het pictogram toont een eenvoudige omtrek van een persoon met de omtrek van een sleutel erop." lightbox="media/quickstart-adt-explorer/sign-in.png":::

1. Voer de URL van de Azure Digital Twins-instantie in die u eerder hebt opgehaald uit de sectie [Vereiste](#prerequisites) in de indeling *https://{hostnaam instantie}* .

>[!NOTE]
> U kunt deze informatie op elk gewenst moment opnieuw bekijken/bewerken door hetzelfde pictogram te selecteren om het vak **Sign in** (Aanmelden) weer te openen. De waarden die u hebt doorgegeven, blijven bewaard.

> [!TIP]
> Als het foutbericht `SignalRService.subscribe` wordt weergegeven wanneer u verbinding maakt, moet u ervoor zorgen dat de URL van uw Azure Digital Twins begint met *https://* .

Als u het pop-upvenster **Aangevraagde machtigingen** van Microsoft ziet, geeft u deze toepassing toestemming en accepteert u dit om door te gaan.

## <a name="add-the-sample-data"></a>De voorbeeldgegevens toevoegen

Vervolgens importeert u het voorbeeldscenario en de graaf in ADT Explorer. Het voorbeeldscenario bevindt zich ook in de map **Azure_Digital_Twins__ADT__explorer** die u eerder hebt gedownload.

### <a name="models"></a>Modellen

De eerste stap bij het maken van een Azure Digital Twins-oplossing is het definiëren van de woordenlijst voor uw omgeving. Hiertoe maakt u aangepaste [modellen](concepts-models.md) waarin de typen entiteiten worden beschreven die in uw omgeving aanwezig zijn.

Elk model wordt geschreven in een taal, zoals JSON-LD. Deze taal noemen we de DTDL (Digital Twin Definition Language). In elk model wordt één type entiteit beschreven, in termen van de *eigenschappen*, *telemetriegegevens*, *relaties* en *onderdelen*. Later gaat u deze modellen gebruiken als de basis voor digitale tweelingen die specifieke instanties van deze typen vertegenwoordigen.

Normaal gesproken maakt u een model in drie stappen:

1. De modeldefinitie schrijven. In de quickstart is dit al gedaan als onderdeel van de voorbeeldoplossing.
1. Het model valideren om te controleren of de syntaxis klopt. In de quickstart is dit al gedaan als onderdeel van de voorbeeldoplossing.
1. Het model uploaden naar uw exemplaar van Azure Digital Twins.
 
In deze quickstart zijn de modelbestanden al voor u geschreven en gevalideerd. Ze zijn opgenomen bij de oplossing die u hebt gedownload. In deze sectie gaat u twee vooraf geschreven modellen uploaden naar uw exemplaar om deze onderdelen van een gebouwomgeving te definiëren:

* Floor
* Room

#### <a name="upload-models"></a>Modellen uploaden

Volg deze stappen om modellen te uploaden.

1. Selecteer in het vak **MODEL VIEW** (MODELWEERGAVE) het pictogram **Upload a Model** (Een model uploaden).

   :::image type="content" source="media/quickstart-adt-explorer/upload-model.png" alt-text="In het vak Model View (Modelweergave) is het middelste pictogram gemarkeerd. Het laat een pijl zien die naar een wolk (de cloud) wijst." lightbox="media/quickstart-adt-explorer/upload-model.png":::
 
1. Ga in het weergegeven bestandsselectievak naar de map **Azure_Digital_Twins__ADT__explorer/client/examples** in de gedownloade opslagplaats.
1. Selecteer **Room.json** en **Floor.json** en selecteer **OK**. Als u wilt kunt u ook extra modellen uploaden, maar die worden niet gebruikt in deze quickstart.
1. Volg het pop-updialoogvenster waarin u wordt gevraagd om u aan te melden bij uw Azure-account.

>[!NOTE]
>Als u het volgende foutbericht ziet: :::image type="content" source="media/quickstart-adt-explorer/error-models-popup.png" alt-text="Een pop-upvenster met de melding 'Fout: Fout bij het ophalen van modellen: ClientAuthError: Fout bij openen van pop-upvenster. Dit kan gebeuren als u Internet Explorer gebruikt, of als pop-upvensters in de browser zijn geblokkeerd.' met de knop Sluiten onderaan." border="false"::: 
> Probeer dan de pop-upblokkering uit te schakelen of gebruik een andere browser.

ADT Explorer uploadt nu deze modelbestanden naar uw Azure Digital Twins-exemplaar. Deze zouden moeten worden weergegeven in het vak **MODEL VIEW** (MODELWEERGAVE) met de beschrijvende namen en volledige model-id's. U kunt de informatiepictogrammen **View Model** (Model bekijken) selecteren om de achterliggende DTDL-code te zien.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/model-info.png" alt-text="Een weergave van het vak Model View (Modelweergave) met twee modeldefinities: Floor (dtmi:example:Floor;1) en Room (dtmi:example:Room;1). Het informatiepictogram 'View Model' (Model bekijken) met een letter 'i' in een cirkel is voor elk model gemarkeerd." lightbox="media/quickstart-adt-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>Twins en de tweelinggraaf

Nu er een paar modellen zijn geüpload naar uw exemplaar van Azure Digital Twins, kunt u [digitale tweelingen](concepts-twins-graph.md) toevoegen volgens de modeldefinities.

Digitale dubbels vertegenwoordigen de werkelijke entiteiten in uw bedrijfsomgeving. Dit kunnen sensoren in een farm, lichten in een auto of, zoals in deze quickstart, kamers op een verdieping zijn. U kunt veel dubbels maken van elk modeltype, zoals meerdere kamers die allemaal gebruikmaken van het model *Room* (Kamer). U verbindt deze met relaties in een *dubbelgraaf* die de volledige omgeving vertegenwoordigt.

In deze sectie uploadt u vooraf gemaakte dubbels die zijn verbonden met een vooraf gemaakte graaf. De graaf bevat twee verdiepingen en twee kamers, verbonden met de volgende indeling:

* Floor0
    - Contains Room0
* Floor1
    - Contains Room1

#### <a name="import-the-graph"></a>De graaf importeren

Voer deze stappen uit om de graaf te importeren.

1. Selecteer in het vak **GRAPH VIEW** (GRAAFWEERGAVE) het pictogram **Import Graph** (Graaf importeren).

   :::image type="content" source="media/quickstart-adt-explorer/import-graph.png" alt-text="In het vak Graph View (Grafiekweergave) is een pictogram gemarkeerd. Het laat een pijl zien die naar een wolk (de cloud) wijst." lightbox="media/quickstart-adt-explorer/import-graph.png":::

2. Ga in het bestandskeuzevak naar de map **Azure_Digital_Twins__ADT__explorer/client/examples** en selecteer het spreadsheetbestand **buildingScenario.xlsx**. Dit bestand bevat een beschrijving van de voorbeeldgraaf. Selecteer **OK**.

   Na een paar seconden wordt in ADT Explorer een **Import**-weergave geopend met een preview van de graaf die moet worden geladen.

3. Als u het uploaden van de graaf wilt bevestigen, selecteert u het pictogram **Save** (Opslaan) in de rechterbovenhoek van het vak **GRAPH VIEW** (GRAAFWEERGAVE).

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/graph-preview-save.png" alt-text="Het pictogram Save (Opslaan) gemarkeerd in het deelvenster Graph Preview (Graafvoorbeeld)." lightbox="media/quickstart-adt-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

4. ADT Explorer gebruikt nu het geüploade bestand om de gevraagde dubbels en relaties ertussen te maken. Als dat is voltooid, wordt er een dialoogvenster weergegeven. Selecteer **Close** (Sluiten).

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/import-success.png" alt-text="Een dialoogvenster waarin wordt aangegeven dat het importeren van de graaf is voltooid. Er staat 'Import successful. 4 twins imported. 2 relationships imported.'" lightbox="media/quickstart-adt-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

5. De graaf is nu geüpload naar ADT Explorer. Om de graaf te zien, selecteert u de knop **Run Query** (Query uitvoeren) in het vak **GRAPH EXPLORER** (GRAAFVERKENNER) aan de bovenkant van het ADT Explorer-venster.

   :::image type="content" source="media/quickstart-adt-explorer/run-query.png" alt-text="De knop Run Query (Query uitvoeren) rechtsboven in het venster is gemarkeerd." lightbox="media/quickstart-adt-explorer/run-query.png":::

Met deze actie wordt de standaardquery uitgevoerd waarmee alle digitale dubbels worden geselecteerd en weergegeven. ADT Explorer haalt alle dubbels en relaties van de service op. Hiermee wordt de graaf getekend op basis van de gedefinieerde dubbels en relaties in het vak **GRAPH VIEW** (GRAAFWEERGAVE).

## <a name="explore-the-graph"></a>De graaf verkennen

Nu ziet u de geüploade graaf van het voorbeeldscenario.

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Weergave van het vak Graph View (Graafweergave) met een dubbelgraaf. Een cirkel met de naam Floor1 (Verdieping1) is via een pijl met het label Contains (Bevat) verbonden met een cirkel met het label Room1 (Kamer1). Een cirkel met de naam Floor0 (Verdieping0) is via een pijl met het label Contains (Bevat) verbonden met een cirkel met het label Room0 (Kamer0).":::

De cirkels (graafknooppunten) vertegenwoordigen digitale dubbels. De lijnen vertegenwoordigen relaties. U ziet dat de dubbel **Floor0** **Room0** bevat, en dat de dubbel **Floor1** **Room1** bevat.

Als u een muis gebruikt, kunt u delen van de graaf verslepen om ze te verplaatsen.

### <a name="view-twin-properties"></a>Tweelingeigenschappen weergeven

U kunt een tweeling selecteren om een lijst met eigenschappen en hun waarden weer te geven in het vak **PROPERTY EXPLORER** (EIGENSCHAPVERKENNER).

Dit zijn de eigenschappen van Room0:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room0.png" alt-text="Markering rond het vak Property Explorer (Eigenschapverkenner) waarin de eigenschappen voor Room0 worden weergegeven, zoals (onder andere) het veld $dtId met de waarde Room0, het veld Temperature (Temperatuur) met de waarde 70 en het veld Humidity (Vochtigheidsgraad) met de waarde 30." lightbox="media/quickstart-adt-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room0 heeft een temperatuur van 70 °F.

Dit zijn de eigenschappen van Room1:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room1.png" alt-text="Markering rond het vak Property Explorer (Eigenschapverkenner) waarin de eigenschappen voor Room1 worden weergegeven, zoals (onder andere) het veld $dtId met de waarde Room1, het veld Temperature (Temperatuur) met de waarde 80 en het veld Humidity (Vochtigheidsgraad) met de waarde 60." lightbox="media/quickstart-adt-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room1 heeft een temperatuur van 80 °F.

### <a name="query-the-graph"></a>Query uitvoeren op de graaf

Een hoofdfunctie van Azure Digital Twins is de mogelijkheid om gemakkelijk en efficiënt [query's](concepts-query-language.md) uit te voeren op uw tweelinggrafiek om vragen over uw omgeving te beantwoorden.

Een manier om de tweelingen in uw graaf op te vragen, is op basis van hun *eigenschappen*. Met query's op basis van eigenschappen kunnen vaak allerlei vragen worden beantwoord. U kunt bijvoorbeeld uitbijters in uw omgeving zoeken die mogelijk aandacht vereisen.

In deze sectie voert u een query uit om erachter te komen hoeveel dubbels in uw omgeving een temperatuur van meer dan 75 °F hebben.

Als u het antwoord wilt zien, voert u de volgende query uit in het vak **QUERY EXPLORER** (QUERYVERKENNER).

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Eerder hebben we in de tweelingeigenschappen gezien dat Room0 de temperatuur 70 °F heeft, en Room1 de temperatuur 80 °F. Daarom wordt alleen Room1 in de resultaten weergegeven.
    
:::image type="content" source="media/quickstart-adt-explorer/result-query-property-before.png" alt-text="Resultaten van eigenschapquery, alleen Room1 weergegeven." lightbox="media/quickstart-adt-explorer/result-query-property-before.png":::

>[!TIP]
> Andere vergelijkingsoperators (<, >, = of !=) worden ook ondersteund in bovenstaande query. U kunt proberen deze operators, andere waarden of andere eigenschappen van dubbels in te vullen in de query om te proberen uw eigen vragen te beantwoorden.

## <a name="edit-data-in-the-graph"></a>Gegevens bewerken in de graaf

U kunt ADT Explorer gebruiken om de eigenschappen van de in uw graaf weergegeven tweelingen te bewerken. In deze sectie gaan we de temperatuur van Room0 verhogen naar 76 °F.

Selecteer om te beginnen **Room0**, zodat de lijst met eigenschappen wordt weergegeven in het vak **PROPERTY EXPLORER** (EIGENSCHAPVERKENNER).

De eigenschappen in deze lijst zijn bewerkbaar. Selecteer de temperatuurwaarde **70** om een nieuwe waarde te kunnen invoeren. Voer **76** in en selecteer het pictogram **Save** (Opslaan) om de temperatuur bij te werken naar **76**.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/new-properties-room0.png" alt-text="Het vak Property Explorer (Eigenschapverkenner) met de eigenschappen van Room0. De temperatuurwaarde is een bewerkbaar vak met de waarde 76, en het pictogram Save (Opslaan) is gemarkeerd." lightbox="media/quickstart-adt-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Nu ziet u het venster **Patch Information** (Patchinformatie) met de patchcode die achter de schermen met de Azure Digital Twins-[API's](how-to-use-apis-sdks.md) is gebruikt om de update te doen. Selecteer **Close** (Sluiten).

### <a name="query-to-see-the-result"></a>Query om het resultaat weer te geven

Verifieer dat de graaf uw update van de temperatuur voor Room0 inderdaad heeft geregistreerd door de eerdere query nogmaals uit te voeren om alle dubbels in de omgeving met een temperatuur van meer dan 75 op te halen.

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Nu de temperatuur van Room0 is gewijzigd van 70 in 76, moeten beide tweelingen in het resultaat worden weergegeven.

:::image type="content" source="media/quickstart-adt-explorer/result-query-property-after.png" alt-text="Resultaten van eigenschapquery, zowel Room0 als Room1 weergegeven." lightbox="media/quickstart-adt-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>Samenvatting en context

In deze quickstart hebt u een exemplaar van Azure Digital Twins gemaakt, gekoppeld aan ADT Explorer en gevuld met een voorbeeldscenario.

Vervolgens hebt u de graaf verkend door:

* Een query te gebruiken om een vraag over het scenario te beantwoorden.
* Een eigenschap van een digitale tweeling te bewerken.
* De query opnieuw uit te voeren om te zien hoe het antwoord is veranderd als gevolg van uw update.

De intentie van deze oefening is te demonstreren hoe u de Azure Digital Twins-graaf kunt gebruiken om vragen over uw omgeving te beantwoorden terwijl de omgeving blijft veranderen.

In deze quickstart hebt u de temperatuur handmatig bijgewerkt. Normaal gesproken koppelt u digitale dubbels in Azure Digital Twins aan echte IoT-apparaten, zodat ze automatisch updates krijgen op basis van telemetriegegevens. Op deze manier kunt u een livegraaf maken die altijd de werkelijke status van uw omgeving weerspiegelt. U kunt query's gebruiken om informatie te krijgen over wat er in realtime in uw omgeving gebeurt.

## <a name="clean-up-resources"></a>Resources opschonen

Als u het werk voor deze quickstart wilt afronden, moet u eerst de actieve console-app afsluiten. Met deze actie wordt de verbinding met de ADT Explorer-app in de browser afgesloten. U kunt geen livegegevens meer weergeven in de browser. U kunt het browsertabblad sluiten.

Als u van plan bent om door te gaan met de zelfstudies voor Azure Digital Twins, kunt u het exemplaar dat in deze quickstart wordt gebruikt, opnieuw gebruiken voor die artikelen en hoeft u dit niet te verwijderen.
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Verwijder tot slot de voorbeeldmappen van het project die u hebt gedownload naar uw lokale machine (**Azure_Digital_Twins__ADT__explorer**). Mogelijk moet u zowel de gezipte als de uitgepakte versie verwijderen.

## <a name="next-steps"></a>Volgende stappen

Ga vervolgens verder met de zelfstudies over Azure Digital Twins om uw eigen Azure Digital Twins-scenario en-interactiehulpprogramma's samen te stellen.

> [!div class="nextstepaction"]
> [Zelfstudie: Een client-app coderen](tutorial-code.md)