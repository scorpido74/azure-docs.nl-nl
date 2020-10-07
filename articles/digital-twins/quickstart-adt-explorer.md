---
title: Snelstart - Een voorbeeldscenario verkennen
titleSuffix: Azure Digital Twins
description: Snelstart - Gebruik het ADT Explorer-voorbeeld om een vooraf ontwikkeld scenario te visualiseren en te verkennen.
author: baanders
ms.author: baanders
ms.date: 9/24/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: dbe37e8a5cba18254cff1dc5d0fff4d5b9bc783d
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372609"
---
# <a name="quickstart---explore-a-sample-azure-digital-twins-scenario-using-adt-explorer"></a>Snelstart - Een voorbeeld van een Azure Digital Twins-scenario verkennen met behulp van ADT Explorer

Met Azure Digital Twins kunt u live modellen van uw echte omgevingen maken en gebruiken. Dit wordt gedaan door afzonderlijke elementen te modelleren als **digitale tweelingen** en deze vervolgens te verbinden tot een kennis**graaf** die kan reageren op live gebeurtenissen en waaruit gegevens kunnen worden opgevraagd.

In deze quickstart verkent u een vooraf gebouwde Azure Digital Twins-graaf, met behulp van een voorbeeldtoepassing genaamd [**Azure Digital Twins (ADT) Explorer**](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Met ADT Explorer kunt u een scenario uploaden, visuele weergaven van uw tweelingen en graaf bekijken, en andere beheeractiviteiten uitvoeren via een visuele ervaring op basis van een browser.

De quickstart bevat de volgende hoofdstappen:

1. Een Azure Digital Twins-exemplaar en ADT Explorer instellen
1. Vooraf gemaakte modellen en graafgegevens uploaden om het voorbeeldscenario samen te stellen
1. De gemaakte scenariograaf verkennen
1. Wijzigingen aanbrengen in de graaf

De voorbeeldgraaf waarmee u gaat werken, vertegenwoordigt een gebouw met twee verdiepingen en twee kamers. De graaf ziet er als volgt uit:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Weergave van een graaf met vier ronde knooppunten die zijn verbonden via pijlen. Een cirkel met het label 'Floor1' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room1'; een cirkel met het label 'Floor0' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room0'. 'Floor1' en 'Floor0' zijn niet verbonden.":::

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-abonnement nodig om deze quickstart te voltooien. Als u er nog geen hebt, kunt u er **[nu gratis een maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** .

U moet ook **Node.js** op uw computer hebben. U kunt de nieuwste versie verkrijgen via deze link: [Node.js](https://nodejs.org/).

Tenslotte moet u ook twee voorbeelden downloaden om te gebruiken tijdens de snelstart:
* De voorbeeldtoepassing **ADT Explorer**. Dit voorbeeld bevat de hoofd-app die u in de quickstart gebruikt om een Azure Digital Twins-scenario te laden en te verkennen. Ga hierheen om de app te downloaden: [Azure Digital Twins (ADT) Explorer](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Klik op de knop *ZIP downloaden* om een *.zip*-bestand met deze voorbeeldcode naar uw machine te downloaden als _**Azure_Digital_Twins__ADT__explorer.zip**_. Pak de map uit en extraheer de bestanden.
* Het **Azure Digital Twins-voorbeeldscenario**. Dit bevat een vooraf gebouwde Azure Digital Twins-graaf die u in ADT Explorer laadt om mee te werken. Ga hierheen om het scenario te downloaden: [Azure Digital Twins-voorbeelden](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples). Klik op de knop *ZIP downloaden* om een *.zip*-bestand met deze voorbeeldcode naar uw machine te downloaden. Hiermee wordt een .zip-folder gedownload naar uw machine als **Azure_Digital_Twins_samples.zip**. Pak de map uit en extraheer de bestanden.

## <a name="set-up-azure-digital-twins-and-adt-explorer"></a>Azure Digital Twins en ADT Explorer instellen

De eerste stap bij het werken met Azure Digital Twins is het instellen van een **Azure Digital Twins-exemplaar**. Nadat u een exemplaar van de service hebt gemaakt, kunt u deze later in de quickstart vullen met de voorbeeldgegevens.

U stelt ook machtigingen in zodat ADT Explorer kan worden uitgevoerd op uw computer en toegang heeft tot uw exemplaar van Azure Digital Twins. Zo kunt u de voorbeeld-app gebruiken om uw exemplaar en de bijbehorende gegevens te verkennen.

### <a name="set-up-azure-digital-twins-instance"></a>Een Azure Digital Twins-exemplaar instellen

Stel eerst een Azure Digital Twins-instantie in en de vereiste verificatie, zodat u ermee kunt werken. Volg hiervoor de instructies in [*Instructies: een exemplaar en verificatie instellen*](how-to-set-up-instance-portal.md). Afhankelijk van uw favoriete ervaring wordt het configuratie-artikel aangeboden voor de [Azure Portal](how-to-set-up-instance-portal.md), [CLI](how-to-set-up-instance-cli.md)of [geautomatiseerd voorbeeld van een Cloud Shell-implementatiescript](how-to-set-up-instance-scripted.md). Alle versies van de instructies bevatten ook stappen om te controleren of u elke stap hebt voltooid en gereed bent om door te gaan met het nieuwe exemplaar.

In deze quickstart hebt u de volgende waarden nodig van toen u het exemplaar hebt ingesteld. Als u deze waarden opnieuw wilt opzoeken, gebruikt u de koppelingen naar de bijbehorende secties in het artikel over installeren om ze te vinden in de [Azure-portal](https://portal.azure.com).
* Azure Digital Twins-exemplaar **_hostnaam_** ([beschikbaar in de portal](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* Azure AD-app-registratie **_Toepassings-id (client)_** ([beschikbaar in de portal](how-to-set-up-instance-portal.md#collect-important-values))
* Azure AD-app-registratie **_Map-id (tenant)_** ([beschikbaar in de portal](how-to-set-up-instance-portal.md#collect-important-values))

### <a name="set-adt-explorer-permissions"></a>Machtigingen voor ADT Explorer instellen

Vervolgens bereidt u het exemplaar van Azure Digital Twins dat u hebt gemaakt voor om te werken met ADT Explorer, dat een lokaal gehoste webtoepassing is. Ga naar de pagina [App-registraties](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) in Azure Portal en selecteer de naam van uw app-registratie in de lijst.

Selecteer *Verificatie* in het menu van de registratie en klik op *+ Een platform toevoegen*.

:::image type="content" source="media/quickstart-adt-explorer/authentication-pre.png" alt-text="Weergave van een graaf met vier ronde knooppunten die zijn verbonden via pijlen. Een cirkel met het label 'Floor1' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room1'; een cirkel met het label 'Floor0' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room0'. 'Floor1' en 'Floor0' zijn niet verbonden." lightbox="media/quickstart-adt-explorer/authentication-pre.png":::

Selecteer *Web* op de pagina *Platforms configureren* die volgt.
Vul de configuratiegegevens als volgt in:
* **Omleidings-URI's**: Voeg de omleidings-URI *http://localhost:3000* toe.
* **Impliciete toekenning**: Schakel het selectievakje voor *Toegangstokens* in.

Klik op *Configureren* om te voltooien.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/authentication-configure-web.png" alt-text="Weergave van een graaf met vier ronde knooppunten die zijn verbonden via pijlen. Een cirkel met het label 'Floor1' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room1'; een cirkel met het label 'Floor0' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room0'. 'Floor1' en 'Floor0' zijn niet verbonden.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

U hebt nu een webconfiguratie geconfigureerd die door ADT Explorer zal worden gebruikt. Het tabblad Verificatie in Azure Portal moet dit weerspiegelen. Nadat u de onderstaande secties hebt gecontroleerd, klikt u op *Opslaan*.

:::image type="content" source="media/quickstart-adt-explorer/authentication-post.png" alt-text="Weergave van een graaf met vier ronde knooppunten die zijn verbonden via pijlen. Een cirkel met het label 'Floor1' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room1'; een cirkel met het label 'Floor0' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room0'. 'Floor1' en 'Floor0' zijn niet verbonden.":::

### <a name="run-and-configure-adt-explorer"></a>ADT Explorer uitvoeren en configureren

Voer vervolgens de toepassing ADT Explorer uit en configureer deze voor uw Azure Digital Twins-exemplaar.

Ga naar de gedownloade en uitgepakte map _**Azure_Digital_Twins__ADT__explorer**_. Open een opdrachtprompt op de maplocatie *Azure_Digital_Twins__ADT__explorer/client/src*.

Voer `npm install` uit om alle vereiste afhankelijkheden te downloaden.

Start vervolgens de app met de opdracht `npm run start`.

Na een paar seconden wordt een browservenster geopend en wordt de app weergegeven in de browser.

:::image type="content" source="media/quickstart-adt-explorer/explorer-blank.png" alt-text="Weergave van een graaf met vier ronde knooppunten die zijn verbonden via pijlen. Een cirkel met het label 'Floor1' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room1'; een cirkel met het label 'Floor0' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room0'. 'Floor1' en 'Floor0' zijn niet verbonden." lightbox="media/quickstart-adt-explorer/explorer-blank.png":::

Klik bovenaan het venster (te zien in onderstaande afbeelding) op de knop *Aanmelden* om ADT Explorer zo te configureren dat het werkt met het exemplaar dat u hebt ingesteld. 

:::image type="content" source="media/quickstart-adt-explorer/sign-in.png" alt-text="Weergave van een graaf met vier ronde knooppunten die zijn verbonden via pijlen. Een cirkel met het label 'Floor1' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room1'; een cirkel met het label 'Floor0' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room0'. 'Floor1' en 'Floor0' zijn niet verbonden." lightbox="media/quickstart-adt-explorer/sign-in.png":::

Voer de belangrijke informatie in die u eerder hebt verzameld in de sectie [Prerequisites](#prerequisites) (Vereisten):
* (Client-)id van de app
* (Tenant-)id van de map
* De URL van het Azure Digital Twins-exemplaar, in de indeling *https://{instance host name}*

>[!NOTE]
> U kunt deze informatie op elk gewenst moment opnieuw bekijken/bewerken door hetzelfde pictogram te selecteren om het aanmeldingsvenster weer te openen. De waarden die u hebt doorgegeven, blijven bewaard.

> [!TIP]
> Als er een foutbericht `SignalRService.subscribe` wordt weergegeven wanneer u verbinding maakt, moet u ervoor zorgen dat de URL van uw Azure Digital Twins begint met *https://* .

Als u het pop-upvenster *Aangevraagde machtigingen* van Microsoft ziet, geeft u deze toepassing toestemming en accepteert u dit om door te gaan.

## <a name="add-the-sample-data"></a>De voorbeeldgegevens toevoegen

Vervolgens importeert u het voorbeeldscenario en de graaf in ADT Explorer.

Het voorbeeldscenario bevindt zich in de gedownloade en uitgepakte map _**Azure_Digital_Twins_samples**_. Ga nu daar die map.

### <a name="models"></a>Modellen

De eerste stap bij het maken van een Azure Digital Twins-oplossing is het definiëren van de woordenlijst voor uw omgeving. Dit doet u door aangepaste [**modellen**](concepts-models.md) te maken waarin de typen entiteiten worden beschreven die in uw omgeving aanwezig zijn. 

Elk model is geschreven in een JSON-LD-achtige taal, **Digital Twins Definition Language (DTDL)** genaamd, en beschrijft de *eigenschappen*, *telemetrie*, *relaties* en *componenten* van één type entiteit. Later gaat u deze modellen gebruiken als de basis voor digitale tweelingen die specifieke instanties van deze typen vertegenwoordigen.

Normaal gesproken voert u drie stappen uit om een model te maken:
1. De modeldefinitie schrijven (in de quickstart is dit al gedaan als onderdeel van de voorbeeldoplossing)
2. Het model valideren om te controleren of de syntaxis klopt (in de quickstart is dit al gedaan als onderdeel van de voorbeeldoplossing)
3. Het model uploaden naar uw exemplaar van Azure Digital Twins
 
In deze quickstart zijn de modelbestanden al voor u geschreven en gevalideerd, en zijn ze opgenomen bij de oplossing die u hebt gedownload. In deze sectie gaat u twee vooraf geschreven modellen uploaden naar uw exemplaar om deze onderdelen van een gebouwomgeving te definiëren:
* Floor
* Room

#### <a name="upload-models"></a>Modellen uploaden

Klik in het vak *MODEL VIEW* (MODELWEERGAVE) op het pictogram *Upload a Model* (Een model uploaden).

:::image type="content" source="media/quickstart-adt-explorer/upload-model.png" alt-text="Weergave van een graaf met vier ronde knooppunten die zijn verbonden via pijlen. Een cirkel met het label 'Floor1' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room1'; een cirkel met het label 'Floor0' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room0'. 'Floor1' en 'Floor0' zijn niet verbonden." lightbox="media/quickstart-adt-explorer/upload-model.png":::
 
1. Ga in het weergegeven bestandsselectievak naar de map *Azure_Digital_Twins_samples/AdtSampleApp/SampleClientApp/models* in de gedownloade opslagplaats.
2. Selecteer *Room.json* en *Floor.json*en klik op OK. (Als u wilt kunt u de andere modellen ook uploaden, maar die worden niet gebruikt in deze quickstart.)
3. Volg het pop-upvenster waarin u wordt gevraagd u aan te melden bij uw Azure-account.

>[!NOTE]
>Als u het volgende foutbericht ziet: :::image type="content" source="media/quickstart-adt-explorer/error-models-popup.png" alt-text="Weergave van een graaf met vier ronde knooppunten die zijn verbonden via pijlen. Een cirkel met het label 'Floor1' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room1'; een cirkel met het label 'Floor0' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room0'. 'Floor1' en 'Floor0' zijn niet verbonden." border="false"::: 
> Probeer dan de pop-upblokkering uit te schakelen of gebruik een andere browser.

ADT Explorer uploadt nu deze modelbestanden naar uw Azure Digital Twins-exemplaar. Deze zouden moeten worden weergegeven in het vak *MODEL VIEW* (MODELWEERGAVE) met hun beschrijvende naam en volledige model-id's. U kunt op de informatiebellen *View Model* (Model bekijken) klikken om de achterliggende DTDL-code te zien.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/model-info.png" alt-text="Weergave van een graaf met vier ronde knooppunten die zijn verbonden via pijlen. Een cirkel met het label 'Floor1' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room1'; een cirkel met het label 'Floor0' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room0'. 'Floor1' en 'Floor0' zijn niet verbonden." lightbox="media/quickstart-adt-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>Twins en de tweelinggraaf

Nu er een paar modellen zijn geüpload naar uw exemplaar van Azure Digital Twins, kunt u [**digitale tweelingen**](concepts-twins-graph.md) toevoegen volgens de modeldefinities. 

Digitale tweelingen vertegenwoordigen de werkelijke entiteiten in uw bedrijfsomgeving: dingen als sensoren op een boerderij, lampen in een auto of, zoals in deze quickstart, ruimten in een verdieping van een gebouw. U kunt veel tweelingen maken van elk modeltype (zoals meerdere kamers die allemaal gebruikmaken van het *Room*-model) en deze met relaties verbinden tot een **tweelinggraaf** die de volledige omgeving vertegenwoordigt.

In deze sectie uploadt u vooraf gemaakte tweelingen die zijn verbonden met een vooraf gemaakte graaf. De graaf bevat twee verdiepingen en twee kamers, verbonden met de volgende indeling:
* *Floor0*
    - bevat *Room0*
* *Floor1*
    - bevat *Room1*

#### <a name="import-the-graph"></a>De graaf importeren

Klik in het vak *GRAPH VIEW* (GRAAFWEERGAVE) op het pictogram *Import Graph* (Graaf importeren).

:::image type="content" source="media/quickstart-adt-explorer/import-graph.png" alt-text="Weergave van een graaf met vier ronde knooppunten die zijn verbonden via pijlen. Een cirkel met het label 'Floor1' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room1'; een cirkel met het label 'Floor0' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room0'. 'Floor1' en 'Floor0' zijn niet verbonden." lightbox="media/quickstart-adt-explorer/import-graph.png":::

Ga in het bestandsselectievak naar de map *Azure_Digital_Twins_samples/AdtSampleApp/SampleClientApp* en kies het spreadsheetbestand _**buildingScenario.xlsx**_. Dit bestand bevat een beschrijving van de voorbeeldgraaf. Klik op OK.

Na een paar seconden opent ADT Explorer een *Import*-weergave met een preview van de graaf die wordt geladen.

Als u het uploaden van de graaf wilt bevestigen, klikt u op het pictogram *Save* (Opslaan) in de rechterbovenhoek van de *GRAPH VIEW* (GRAAFWEERGAVE):

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/graph-preview-save.png" alt-text="Weergave van een graaf met vier ronde knooppunten die zijn verbonden via pijlen. Een cirkel met het label 'Floor1' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room1'; een cirkel met het label 'Floor0' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room0'. 'Floor1' en 'Floor0' zijn niet verbonden." lightbox="media/quickstart-adt-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

ADT Explorer gebruikt nu het geüploade bestand om de gevraagde tweelingen en relaties ertussen te maken. Er wordt een dialoogvenster weergegeven om aan te geven dat het is voltooid. Klik op *Close* (Sluiten).

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/import-success.png" alt-text="Weergave van een graaf met vier ronde knooppunten die zijn verbonden via pijlen. Een cirkel met het label 'Floor1' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room1'; een cirkel met het label 'Floor0' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room0'. 'Floor1' en 'Floor0' zijn niet verbonden." lightbox="media/quickstart-adt-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

De graaf is nu geüpload naar ADT Explorer. Om de graaf te zien, klikt u op de knop *Run Query* (Query uitvoeren) in het vak *GRAPH EXPLORER* (GRAAFVERKENNER) aan de bovenkant van het ADT Explorer-venster. 

:::image type="content" source="media/quickstart-adt-explorer/run-query.png" alt-text="Weergave van een graaf met vier ronde knooppunten die zijn verbonden via pijlen. Een cirkel met het label 'Floor1' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room1'; een cirkel met het label 'Floor0' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room0'. 'Floor1' en 'Floor0' zijn niet verbonden." lightbox="media/quickstart-adt-explorer/run-query.png":::

Hiermee wordt de standaardquery uitgevoerd waarmee alle digitale tweelingen worden geselecteerd en weergegeven. ADT Explorer haalt alle tweelingen en relaties op uit de service en tekent de daardoor gedefinieerde graaf in het vak *GRAPH VIEW* (GRAAFWEERGAVE).

## <a name="explore-the-graph"></a>De graaf verkennen

Nu ziet u de geüploade graaf van het voorbeeldscenario:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Weergave van een graaf met vier ronde knooppunten die zijn verbonden via pijlen. Een cirkel met het label 'Floor1' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room1'; een cirkel met het label 'Floor0' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room0'. 'Floor1' en 'Floor0' zijn niet verbonden.":::

De cirkels (graafknooppunten) vertegenwoordigen digitale tweelingen, en de lijnen vertegenwoordigen relaties. U ziet dat de tweeling *Floor0* *Room0* bevat, en *Floor1* bevat *Room1*.

Als u een muis gebruikt, kunt u op delen van de grafiek klikken en deze verslepen om ze te verplaatsen.

### <a name="view-twin-properties"></a>Tweelingeigenschappen weergeven 

U kunt een tweeling selecteren om een lijst met eigenschappen en hun waarden weer te geven in het vak *PROPERTY EXPLORER* (EIGENSCHAPVERKENNER). 

Dit zijn de eigenschappen van *Room0*:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room0.png" alt-text="Weergave van een graaf met vier ronde knooppunten die zijn verbonden via pijlen. Een cirkel met het label 'Floor1' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room1'; een cirkel met het label 'Floor0' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room0'. 'Floor1' en 'Floor0' zijn niet verbonden." lightbox="media/quickstart-adt-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

U ziet dat *Room0* een temperatuur van **70** °F heeft.

Dit zijn de eigenschappen van *Room1*:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room1.png" alt-text="Weergave van een graaf met vier ronde knooppunten die zijn verbonden via pijlen. Een cirkel met het label 'Floor1' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room1'; een cirkel met het label 'Floor0' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room0'. 'Floor1' en 'Floor0' zijn niet verbonden." lightbox="media/quickstart-adt-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

U ziet dat *Room1* een temperatuur van **80** °F heeft.

### <a name="query-the-graph"></a>Query uitvoeren op de graaf

Een hoofdfunctie van Azure Digital Twins is de mogelijkheid om gemakkelijk en efficiënt [query's](concepts-query-language.md) uit te voeren op uw tweelinggrafiek om vragen over uw omgeving te beantwoorden. 

Een manier om de tweelingen in uw graaf op te vragen, is op basis van hun *eigenschappen*. Door query's uit te voeren op basis van eigenschappen kunt u allerlei vragen beantwoorden, en uitbijters in uw omgeving vinden die mogelijk aandacht vereisen.

In deze sectie voert u een query uit om de volgende vraag te beantwoorden: _**Welke tweelingen in mijn omgeving hebben een temperatuur van meer dan 75 °F?**_

Als u het antwoord wilt zien, voert u de volgende query uit in het vak *QUERY EXPLORER* (QUERYVERKENNER):

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Eerder hebben we in de tweelingeigenschappen gezien dat *Room0* de temperatuur **70** °F heeft, en *Room1* de temperatuur **80** °F. Daarom wordt alleen _**Room1**_ in de resultaten weergegeven.
    
:::image type="content" source="media/quickstart-adt-explorer/result-query-property-before.png" alt-text="Weergave van een graaf met vier ronde knooppunten die zijn verbonden via pijlen. Een cirkel met het label 'Floor1' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room1'; een cirkel met het label 'Floor0' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room0'. 'Floor1' en 'Floor0' zijn niet verbonden." lightbox="media/quickstart-adt-explorer/result-query-property-before.png":::

>[!TIP]
> Andere vergelijkingsoperators ( *<* , *>* , *=* of *!=* ) worden ook ondersteund in bovenstaande query. U kunt proberen deze, andere waarden of andere tweelingen eigenschappen in te vullen in de query om te proberen uw eigen vragen te beantwoorden.

## <a name="edit-data-in-the-graph"></a>Gegevens bewerken in de graaf

U kunt ADT Explorer gebruiken om de eigenschappen van de in uw graaf weergegeven tweelingen te bewerken. In deze sectie gaan we **_de temperatuur_ van Room0 verhogen naar 76**.

Selecteer *Room0*, waardoor de lijst met eigenschappen wordt weergegeven in het vak *PROPERTY EXPLORER* (EIGENSCHAPVERKENNER).

De eigenschappen in deze lijst zijn bewerkbaar. Selecteer de temperatuurwaarde **70** om een nieuwe waarde te kunnen invoeren. Voer **76**in en klik op het pictogram *Save* (Opslaan) om de temperatuur bij te werken naar **76**.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/new-properties-room0.png" alt-text="Weergave van een graaf met vier ronde knooppunten die zijn verbonden via pijlen. Een cirkel met het label 'Floor1' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room1'; een cirkel met het label 'Floor0' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room0'. 'Floor1' en 'Floor0' zijn niet verbonden." lightbox="media/quickstart-adt-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Wanneer het opslaan is gelukt, ziet u het venster *Patch Information* (Patchinformatie) met de patchcode die achter de schermen met de Azure Digital Twins-[API's](how-to-use-apis-sdks.md) is gebruikt om de update te doen. Klik op *Close* (Sluiten).

### <a name="query-to-see-the-result"></a>Query om het resultaat weer te geven

Verifieer dat de graaf uw update van de temperatuur van *Room0* inderdaad heeft geregistreerd door de eerdere query nogmaals uit te voeren om **alle tweelingen in de omgeving met een temperatuur van meer dan 75 op te halen**:

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Nu de temperatuur van *Room0* is gewijzigd van **70** in **76**, moeten beide tweelingen in het resultaat worden weergegeven.

:::image type="content" source="media/quickstart-adt-explorer/result-query-property-after.png" alt-text="Weergave van een graaf met vier ronde knooppunten die zijn verbonden via pijlen. Een cirkel met het label 'Floor1' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room1'; een cirkel met het label 'Floor0' is door een pijl met het label 'contains' (bevat) verbonden met een cirkel met het label 'Room0'. 'Floor1' en 'Floor0' zijn niet verbonden." lightbox="media/quickstart-adt-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>Samenvatting en context

In deze quickstart hebt u een exemplaar van Azure Digital Twins gemaakt, gekoppeld aan ADT Explorer en gevuld met een voorbeeldscenario. 

Vervolgens hebt u de graaf verkend door...
1. Een query te gebruiken om een vraag over het scenario te beantwoorden.
2. Een eigenschap van een digitale tweeling te bewerken.
    * De query opnieuw uit te voeren om te zien hoe het antwoord is veranderd als gevolg van uw update.

De intentie van deze oefening is te demonstreren hoe u de Azure Digital Twins-graaf kunt gebruiken om vragen over uw omgeving te beantwoorden terwijl de omgeving blijft veranderen. 

Hoewel u in deze quickstart de temperatuur met de hand hebt bijgewerkt, koppelt u normaal gesproken digitale tweelingen in Azure Digital Twins aan echte IoT-apparaten, zodat ze automatisch updates krijgen op basis van telemetriegegevens. Zo kunt u een live graaf maken die altijd de werkelijke toestand van uw omgeving weerspiegelt, en query's gebruiken om in real time informatie te krijgen over wat er in uw omgeving gebeurt.

## <a name="clean-up-resources"></a>Resources opschonen

Als u het werk voor deze quickstart wilt afronden, moet u eerst de actieve console-app afsluiten. Hierdoor wordt de verbinding met de ADT Explorer-app in de browser afgesloten en kunt u geen live gegevens meer weergeven in de browser. U kunt het browsertabblad sluiten.

Als u van plan bent om door te gaan met de zelf studies voor Azure Digital Twins, kan het exemplaar dat in deze quickstart wordt gebruikt, opnieuw worden gebruikt voor die artikelen en hoeft u dit niet te verwijderen.
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Verwijder tot slot de voorbeeldmappen van het project die u hebt gedownload naar uw lokale machine (_**Azure_Digital_Twins__ADT__explorer**_ en _**Azure_Digital_Twins_samples**_). Mogelijk moet u zowel de gezipte als de uitgepakte versie verwijderen.

## <a name="next-steps"></a>Volgende stappen 

Ga vervolgens verder met de zelfstudies over Azure Digital Twins om uw eigen Azure Digital Twins-scenario en-interactiehulpprogramma's samen te stellen.

> [!div class="nextstepaction"]
> [*Zelfstudie: Een client-app coderen*](tutorial-code.md)
