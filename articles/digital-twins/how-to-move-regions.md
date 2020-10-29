---
title: Instantie verplaatsen naar een andere Azure-regio
titleSuffix: Azure Digital Twins
description: Zie een Azure Digital Apparaatdubbels-exemplaar verplaatsen van de ene Azure-regio naar een andere.
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: e586e9acc9510dc1aaae511fa51e5a0c3255bd8f
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2020
ms.locfileid: "93026493"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Een Azure Digital Apparaatdubbels-exemplaar verplaatsen naar een andere Azure-regio

Als u uw Azure Digital Apparaatdubbels-exemplaar wilt verplaatsen van de ene regio naar een andere, is het huidige proces om **uw resources opnieuw te maken in de nieuwe regio** en vervolgens de oorspronkelijke resources te verwijderen. Aan het einde van dit proces werkt u met een nieuw exemplaar van Azure Digital Apparaatdubbels dat identiek is aan de eerste, behalve voor de bijgewerkte locatie.

Dit artikel bevat richt lijnen voor het uitvoeren van een volledige verhuizing, kopiëren over alles wat u moet doen om ervoor te zorgen dat het nieuwe exemplaar overeenkomt met het origineel.

Dit proces bestaat uit de volgende stappen:
1. Voorbereiden: down load uw oorspronkelijke modellen, apparaatdubbels en Graph.
2. Verplaatsen: Maak een nieuwe Azure Digital Apparaatdubbels-instantie in een nieuwe regio.
3. Verplaatsen: Vul het nieuwe Azure Digital Apparaatdubbels-exemplaar opnieuw in.
    - Upload originele modellen, apparaatdubbels en Graph.
    - Maak eind punten en routes opnieuw.
    - Gekoppelde resources opnieuw koppelen.
4. Bron resources opschonen: oorspronkelijke instantie verwijderen.

## <a name="prerequisites"></a>Vereisten

Voordat u probeert uw Azure Digital Apparaatdubbels-exemplaar opnieuw te maken, is het een goed idee om de onderdelen van uw oorspronkelijke exemplaar te passeren en een duidelijk beeld te krijgen van alle onderdelen die opnieuw moeten worden gemaakt.

Hier volgen enkele vragen die u kunt overwegen:
* Wat zijn de **modellen** die zijn geüpload naar mijn exemplaar? Hoeveel er zijn er?
* Wat zijn de **apparaatdubbels** in mijn exemplaar? Hoeveel er zijn er?
* Wat is de algemene vorm van de **grafiek** in mijn exemplaar? Hoeveel relaties zijn er?
* Welke **eind punten** heb ik in mijn exemplaar?
* Welke **routes** heb ik in mijn exemplaar? Hebben ze filters?
* Waar maakt mijn exemplaar **verbinding met andere Azure-Services** ? Enkele algemene integratie punten zijn...
    - Event Grid, Event hub of Service Bus
    - Azure Functions
    - Logic Apps
    - Time Series Insights
    - Azure Maps
    - Device Provisioning Service (DPS)
* Wat andere **persoonlijke apps of bedrijfs toepassingen** heb ik die verbinding maken met mijn exemplaar?

U kunt deze informatie verzamelen met behulp van de [Azure Portal](https://portal.azure.com), [Azure Digital Apparaatdubbels api's en Sdk's](how-to-use-apis-sdks.md), [Azure Digital apparaatdubbels cli-opdrachten](how-to-use-cli.md)of het [Azure Digital apparaatdubbels (ADT) Explorer-](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) voor beeld.

## <a name="prepare"></a>Voorbereiden

In deze sectie gaat u voorbereiden om uw exemplaar opnieuw te maken door **uw oorspronkelijke modellen, apparaatdubbels en grafiek te downloaden** van uw oorspronkelijke exemplaar. In dit artikel wordt het voor beeld van de [Azure Digital apparaatdubbels (ADT) Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) gebruikt.

>[!NOTE]
>Mogelijk hebt u al bestanden met de modellen en/of de grafiek in uw exemplaar. Als dat het geval is, hoeft u niet opnieuw alles te downloaden: alleen de onderdelen die u mist of de items die mogelijk zijn gewijzigd sinds u deze bestanden oorspronkelijk hebt geüpload (zoals apparaatdubbels die mogelijk zijn bijgewerkt met nieuwe gegevens).

### <a name="limitations-of-adt-explorer"></a>Beperkingen van ADT Explorer

Het voor beeld van de [Azure Digital apparaatdubbels (ADT) Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) is een client-app-voor beeld dat een visuele weer gave van uw grafiek ondersteunt en die een visuele interactie met uw exemplaar biedt. In dit artikel wordt uitgelegd hoe u dit kunt gebruiken om uw modellen, apparaatdubbels en grafieken te downloaden en later opnieuw te uploaden.

Let echter op dat dit een voor **beeld** is en niet een volledig hulp programma. Het is niet zwaar getest en is niet gebouwd om grafieken van een grote grootte te verwerken. Houd daarom de volgende beperkingen met betrekking tot het gebruik van voor beelden in acht:
* Het voor beeld is momenteel alleen getest op de grootte van grafieken tot 1000 knoop punten en 2000 relaties
* Het voor beeld biedt geen ondersteuning voor nieuwe pogingen in het geval van onregelmatige storingen
* In het voor beeld wordt de gebruiker niet noodzakelijkerwijs gewaarschuwd als de geüploade gegevens onvolledig zijn
* In het voor beeld worden geen fouten verwerkt die voortkomen uit zeer grote grafieken, met meer beschik bare bronnen, zoals geheugen

Als het voor beeld de grootte van uw grafiek niet kan verwerken, kunt u de grafiek exporteren en importeren met behulp van andere Azure Digital Apparaatdubbels-hulpprogram ma's voor ontwikkel aars:
* [Azure Digital Apparaatdubbels CLI-opdrachten](how-to-use-cli.md)
* [Azure Digital Apparaatdubbels Api's en Sdk's](how-to-use-apis-sdks.md)

### <a name="set-up-adt-explorer-application"></a>ADT Explorer-toepassing instellen

Als u wilt door gaan met ADT Explorer, downloadt u eerst de code van de voorbeeld toepassing en stelt u deze in op de computer. 

Ga hier naar het voor beeld: [Azure Digital apparaatdubbels (ADT) Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Klik op de knop *ZIP downloaden* om een *.zip* -bestand met deze voorbeeldcode naar uw machine te downloaden als _**Azure_Digital_Twins__ADT__explorer.zip**_ . Pak het bestand uit.

Stel vervolgens de machtigingen voor ADT Explorer in en configureer deze. Volg hiervoor de instructies in de sectie [*Azure Digital apparaatdubbels en ADT Explorer instellen*](quickstart-adt-explorer.md#set-up-azure-digital-twins-and-adt-explorer) van de Snelstartgids voor Azure Digital apparaatdubbels. In deze sectie wordt u begeleid bij de volgende stappen:
1. Een Azure Digital Apparaatdubbels-exemplaar instellen (u kunt dit onderdeel overs Laan omdat u al een exemplaar hebt)
2. Een **Azure AD-App-registratie** instellen om toegang te bieden tot uw exemplaar
3. Machtigingen instellen om ADT Explorer uit te voeren op uw computer
4. Voer ADT Explorer uit en configureer deze om verbinding te maken met uw exemplaar. U gebruikt de **hostnaam** van uw oorspronkelijke Azure Digital apparaatdubbels-exemplaar dat u verplaatst, en de **client-ID** en **Tenant-id** van de app-registratie.

U moet nu de voorbeeld toepassing ADT Explorer in een browser op uw computer laten worden uitgevoerd. Het voor beeld moet worden verbonden met uw oorspronkelijke Azure Digital Apparaatdubbels-exemplaar.

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="Browservenster met een app die wordt uitgevoerd op localhost:3000. De app heet ADT Explorer en bevat de vakken Query Explorer, Model View, Graph View en Property Explorer. Er zijn nog geen gegevens op het scherm." lightbox="media/how-to-move-regions/explorer-blank.png":::

Als u de verbinding wilt controleren, kunt u op de knop *query uitvoeren* klikken om de standaard query uit te voeren waarin alle apparaatdubbels en relaties worden weer gegeven in de grafiek in het vak *Explorer Verkenner* .

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Browservenster met een app die wordt uitgevoerd op localhost:3000. De app heet ADT Explorer en bevat de vakken Query Explorer, Model View, Graph View en Property Explorer. Er zijn nog geen gegevens op het scherm." lightbox="media/how-to-move-regions/run-query.png":::

U kunt de uitvoering van ADT Explorer verlaten, omdat u deze later in dit artikel opnieuw gaat gebruiken om deze items opnieuw te uploaden naar uw nieuwe exemplaar in de doel regio.

### <a name="download-models-twins-and-graph"></a>Modellen, apparaatdubbels en grafieken downloaden

Down load vervolgens de modellen, apparaatdubbels en Graph in uw oplossing naar uw computer.

Als u al deze tegelijk wilt downloaden, moet u eerst controleren of de volledige grafiek wordt weer gegeven in het vak *grafiek weergave* (u kunt dit doen door de standaard query van `SELECT * FROM digitaltwins` in het vak *query Verkenner* opnieuw uit te voeren).
 
Klik vervolgens op het pictogram *grafiek exporteren* in het vak *grafiek weergave* .

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="Browservenster met een app die wordt uitgevoerd op localhost:3000. De app heet ADT Explorer en bevat de vakken Query Explorer, Model View, Graph View en Property Explorer. Er zijn nog geen gegevens op het scherm." lightbox="media/how-to-move-regions/export-graph.png":::

Hiermee wordt een *Download* koppeling in de *weer gave grafiek* ingeschakeld. Selecteer deze optie om een op JSON gebaseerde weer gave van het query resultaat te downloaden, inclusief uw modellen, apparaatdubbels en relaties. Hiermee wordt een *JSON* -bestand gedownload naar uw computer.

>[!NOTE]
>Als het gedownloade bestand een andere bestands extensie heeft, probeert u de extensie rechtstreeks te bewerken en te wijzigen in *. json* .

## <a name="move"></a>Verplaatsen

Daarna voltooit u het ' verplaatsen ' van uw exemplaar door een nieuw exemplaar te maken in de doel regio en dit te vullen met de gegevens en onderdelen van uw oorspronkelijke exemplaar.

### <a name="create-a-new-instance"></a>Een nieuw exemplaar maken

Maak eerst **een nieuw exemplaar van Azure Digital apparaatdubbels in de doel regio** . Volg hiervoor de stappen in [*How-to: een instantie en verificatie instellen*](how-to-set-up-instance-portal.md), waarbij u de volgende aanwijzers in acht houdt:
* U kunt de naam **van het nieuwe exemplaar in** een andere resource groep blijven gebruiken. Als u dezelfde resource groep wilt gebruiken als de oorspronkelijke instantie, heeft het nieuwe exemplaar een eigen naam nodig.
* Voer de nieuwe regio voor het doel in als u wordt gevraagd om een locatie.

Zodra dit is voltooid, hebt u de **hostnaam** van het nieuwe exemplaar nodig om de installatie van de gegevens te kunnen voortzetten. Als u tijdens de installatie geen notitie hebt gemaakt, kunt u [deze instructies](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) volgen om de app nu te downloaden van de Azure Portal.

### <a name="repopulate-old-instance"></a>Oude instantie opnieuw vullen

Vervolgens stelt u het nieuwe exemplaar in, zodat het een kopie is van het origineel.

#### <a name="upload-original-models-twins-and-graph-using-adt-explorer"></a>Originele modellen, apparaatdubbels en grafieken uploaden met behulp van ADT Explorer

In deze sectie kunt u uw modellen, apparaatdubbels en grafiek opnieuw uploaden naar het nieuwe exemplaar. Als u geen modellen, apparaatdubbels of grafieken in uw oorspronkelijke instantie hebt of als u deze niet wilt verplaatsen naar het nieuwe exemplaar, kunt u door gaan naar de [volgende sectie](#recreate-endpoints-and-routes).

Als u wilt door gaan, gaat u terug naar het browser venster waarop **ADT Explorer** wordt uitgevoerd en volgt u de onderstaande stappen.

##### <a name="connect-to-the-new-instance"></a>Verbinding maken met het nieuwe exemplaar

Op dit moment is ADT Explorer verbonden met uw oorspronkelijke Azure Digital Apparaatdubbels-exemplaar. Als u de verbinding wilt verbreken, gaat u naar het nieuwe exemplaar door te klikken op de knop *Aanmelden* boven aan het venster. 

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="Browservenster met een app die wordt uitgevoerd op localhost:3000. De app heet ADT Explorer en bevat de vakken Query Explorer, Model View, Graph View en Property Explorer. Er zijn nog geen gegevens op het scherm." lightbox="media/how-to-move-regions/sign-in.png":::

U kunt dezelfde app-registratie opnieuw gebruiken, dus u hoeft alleen de *ADT-URL* te vervangen om uw nieuwe instantie weer te geven. Wijzig deze waarde in dat de *https://{New Instance hostname}* wordt gelezen.

Druk op *verbinding maken* . U wordt mogelijk gevraagd om u opnieuw aan te melden met uw Azure-referenties en/of deze toepassing toestemming te geven voor uw exemplaar.

##### <a name="upload-models-twins-and-graph"></a>Modellen, apparaatdubbels en grafieken uploaden

Upload vervolgens de oplossings onderdelen die u eerder hebt gedownload naar uw nieuwe exemplaar.

Als u uw **modellen, apparaatdubbels en grafiek** wilt uploaden, klikt u op het pictogram voor het *importeren* van de grafiek in het vak *diagram* . Met deze optie worden alle drie deze onderdelen tegelijk geüpload (zelfs modellen die momenteel niet worden gebruikt in de grafiek).

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="Browservenster met een app die wordt uitgevoerd op localhost:3000. De app heet ADT Explorer en bevat de vakken Query Explorer, Model View, Graph View en Property Explorer. Er zijn nog geen gegevens op het scherm." lightbox="media/how-to-move-regions/import-graph.png":::

In het vak bestands kiezer gaat u naar de gedownloade grafiek. Selecteer het bestand Graph *. json* en klik op *Open* .

Na een paar seconden opent ADT Explorer een *Import* -weergave met een preview van de graaf die wordt geladen.

Als u het uploaden van de graaf wilt bevestigen, klikt u op het pictogram *Save* (Opslaan) in de rechterbovenhoek van de *GRAPH VIEW* (GRAAFWEERGAVE):

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="Browservenster met een app die wordt uitgevoerd op localhost:3000. De app heet ADT Explorer en bevat de vakken Query Explorer, Model View, Graph View en Property Explorer. Er zijn nog geen gegevens op het scherm." lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

ADT Explorer uploadt nu uw modellen en grafiek (inclusief de apparaatdubbels en relaties) naar uw nieuwe Azure Digital Apparaatdubbels-exemplaar. Er wordt een bericht weer gegeven met de melding dat het aantal modellen, apparaatdubbels en relaties is geüpload:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="Browservenster met een app die wordt uitgevoerd op localhost:3000. De app heet ADT Explorer en bevat de vakken Query Explorer, Model View, Graph View en Property Explorer. Er zijn nog geen gegevens op het scherm." lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Als u wilt controleren of alles is geüpload, klikt u op de knop *query uitvoeren* in het vak *Graph Explorer* om de standaard query uit te voeren waarin alle apparaatdubbels en relaties in de grafiek worden weer gegeven. Hiermee wordt ook de lijst met modellen in de *model weergave* vernieuwd.

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Browservenster met een app die wordt uitgevoerd op localhost:3000. De app heet ADT Explorer en bevat de vakken Query Explorer, Model View, Graph View en Property Explorer. Er zijn nog geen gegevens op het scherm." lightbox="media/how-to-move-regions/run-query.png":::

U ziet uw grafiek met alle apparaatdubbels en relaties die worden weer gegeven in het vak *EXPLORER Verkenner* . U ziet ook de modellen die worden vermeld in het vak *model weergave* .

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="Browservenster met een app die wordt uitgevoerd op localhost:3000. De app heet ADT Explorer en bevat de vakken Query Explorer, Model View, Graph View en Property Explorer. Er zijn nog geen gegevens op het scherm." lightbox="media/how-to-move-regions/post-upload.png":::

Hiermee wordt bevestigd dat uw modellen, apparaatdubbels en grafiek opnieuw zijn geüpload naar het nieuwe exemplaar in de doel regio.

#### <a name="recreate-endpoints-and-routes"></a>Eind punten en routes opnieuw maken

Als u **eind punten en/of routes** in uw oorspronkelijke exemplaar hebt, moet u ze opnieuw maken in uw nieuwe exemplaar. Als u geen eind punten of routes in uw oorspronkelijke exemplaar hebt of als u deze niet naar het nieuwe exemplaar wilt verplaatsen, kunt u door gaan naar de [volgende sectie](#re-link-connected-resources).

Als dat niet het geval is, volgt u de stappen in [*How-to: Manage eind punten en routes*](how-to-manage-routes-portal.md) met het nieuwe exemplaar en houdt u de volgende punten in acht: 
* U hoeft de Event Grid, de Event hub of de Service Bus resource die u voor het eind punt gebruikt, **niet** opnieuw te maken (de *vereiste* secties in de instructies voor het eind punt). U hoeft het eind punt alleen opnieuw te maken op het Azure Digital Apparaatdubbels-exemplaar.
* U kunt de **namen** van eind punten en routes hergebruiken, omdat deze zijn afgestemd op een ander exemplaar.
* Vergeet niet om de vereiste **filters** toe te voegen aan de routes die u maakt.

#### <a name="re-link-connected-resources"></a>Verbonden resources opnieuw koppelen

Als u andere apps of Azure-resources hebt die zijn verbonden met uw oorspronkelijke Azure Digital Apparaatdubbels-exemplaar, moet u de verbinding bewerken zodat deze in plaats daarvan het nieuwe exemplaar bereiken. Hiertoe behoren mogelijk andere Azure-Services of persoonlijke of bedrijfs-apps die u hebt ingesteld voor gebruik met Azure Digital Apparaatdubbels.

Als u geen andere resources hebt die zijn verbonden met uw oorspronkelijke exemplaar of als u deze niet wilt verplaatsen naar het nieuwe exemplaar, kunt u door gaan naar de [volgende sectie](#verify).

Als u wilt door gaan, moet u rekening houden met de verbonden resources in uw scenario. U hoeft geen verbonden resources te verwijderen en opnieuw te maken. in plaats daarvan hoeft u alleen de punten te bewerken waar ze verbinding maken met een Azure Digital Apparaatdubbels-exemplaar via de **hostnaam** , en dit bijwerken om de hostnaam van het nieuwe exemplaar te gebruiken in plaats van het origineel.

De exacte resources die u nodig hebt om te bewerken, zijn afhankelijk van uw scenario, maar hier volgen enkele algemene integratie punten:
* Azure Functions. Als u een Azure-functie hebt waarvan de code de hostnaam van het oorspronkelijke exemplaar bevat, moet u deze waarde bijwerken naar de hostnaam van het nieuwe exemplaar en de functie opnieuw publiceren.
* Event Grid, Event Hubs of Service Bus
* Logic Apps
* Time Series Insights
* Azure Maps
* Device Provisioning Service (DPS)
* Persoonlijke of bedrijfs-apps buiten Azure, zoals de **client-app** die in de zelf studie is gemaakt [*: Codeer een client-app*](tutorial-code.md)die verbinding maakt met het exemplaar en Azure Digital apparaatdubbels-api's aanroept

Nadat deze stap is voltooid, moet het nieuwe exemplaar in de doel regio een kopie van het oorspronkelijke exemplaar zijn.

## <a name="verify"></a>Verifiëren

U kunt de volgende hulpprogram ma's gebruiken om te controleren of het nieuwe exemplaar correct is ingesteld:
* De [**Azure Portal**](https://portal.azure.com) (goed om te controleren of het nieuwe exemplaar bestaat en zich in de juiste doel regio bevindt, is ook geschikt voor het controleren van eind punten en routes en verbindingen met andere Azure-Services)
* De [Azure Digital Apparaatdubbels **cli-opdrachten**](how-to-use-cli.md) (goed om te controleren of het nieuwe exemplaar bestaat en zich in de juiste doel regio bevindt, kan ook worden gebruikt voor het controleren van exemplaar gegevens)
* [**ADT Explorer**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) (geschikt voor het controleren van exemplaar gegevens, zoals modellen, apparaatdubbels en Graph)
* De [Azure Digital Apparaatdubbels api's en sdk's](how-to-use-apis-sdks.md) (geschikt voor het controleren van exemplaar gegevens, zoals modellen, apparaatdubbels en Graph, zijn ook geschikt voor het controleren van eind punten en routes)

U kunt ook proberen aangepaste apps of end-to-end-stromen uit te voeren die u met uw oorspronkelijke exemplaar hebt uitgevoerd, zodat u kunt controleren of ze met het nieuwe exemplaar goed werken.

## <a name="clean-up-source-resources"></a>Bron resources opschonen

Nu het nieuwe exemplaar is ingesteld in de doel regio met een kopie van de gegevens en verbindingen van het oorspronkelijke exemplaar, kunt u **het oorspronkelijke exemplaar verwijderen** .

U kunt dit doen in de [Azure Portal](https://portal.azure.com), met de [cli](how-to-use-cli.md)of met behulp van de [Control vlak-api's](how-to-use-apis-sdks.md#overview-control-plane-apis).

Als u het exemplaar met behulp van de Azure Portal wilt verwijderen, [opent u de portal](https://portal.azure.com) in een browser venster en navigeert u naar uw oorspronkelijke Azure Digital apparaatdubbels-exemplaar door de naam te zoeken in de zoek balk van de portal.

Klik op de knop *verwijderen* en volg de aanwijzingen om het verwijderen te volt ooien.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Browservenster met een app die wordt uitgevoerd op localhost:3000. De app heet ADT Explorer en bevat de vakken Query Explorer, Model View, Graph View en Property Explorer. Er zijn nog geen gegevens op het scherm.":::