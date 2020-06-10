---
title: 'Zelf studie: integreren met Power virtuele agents-QnA Maker'
description: In deze zelf studie kunt u de kwaliteit van uw kennis basis verbeteren met actief leren. Beoordeling, accepteren of afwijzen of toevoegen zonder bestaande vragen te verwijderen of te wijzigen.
ms.topic: tutorial
ms.date: 06/08/2020
ms.openlocfilehash: f1d51f6ad8892252161238eb71fbb02f463463fd
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84635342"
---
# <a name="tutorial-add-your-knowledge-base-to-power-virtual-agents"></a>Zelf studie: uw Knowledge Base toevoegen aan Power virtuele agents
Maak een bot met [Power Virtual agents](https://powervirtualagents.microsoft.com/) om antwoorden uit uw Knowledge Base te maken en uit te breiden.

In deze zelfstudie leert u het volgende:

<!-- green checkmark -->
> [!div class="checklist"]
> * Een bot voor Power Virtual agents maken
> * Een terugval onderwerp voor het systeem maken
> * QnA Maker als een actie toevoegen aan een onderwerp als een stroom voor automatische stroom uitvoering
> * Een oplossing voor het automatiseren van een stroom maken
> * Een stroom voor energie automatisering toevoegen aan uw oplossing
> * Power virtuele agents publiceren
> * Power virtuele agents testen en een antwoord ontvangen van uw QnA Maker Knowledge Base

## <a name="integrate-an-agent-with-a-knowledge-base"></a>Een agent met een Knowledge Base integreren

Met [Power Virtual agents](https://powervirtualagents.microsoft.com/) kunnen teams krachtig botsen maken met behulp van een begeleide, no-code grafische interface. U hebt geen gegevens wetenschappers of ontwikkel aars nodig.

In Power virtuele agents maakt u een agent met een reeks onderwerpen (onderwerpgebied), om gebruikers vragen te beantwoorden door acties uit te voeren. Als er geen antwoord wordt gevonden, kan een systeem-terugval een antwoord retour neren.

Configureer de agent om de vraag naar uw Knowledge Base te verzenden als onderdeel van de actie van een onderwerp of als onderdeel van het pad naar het terugval onderwerp van het *systeem* . Ze gebruiken beide een actie om verbinding te maken met uw Knowledge Base en retour neren een antwoord.

## <a name="power-automate-connects-to-generateanswer-action"></a>Energie automatisering maakt verbinding met `GenerateAnswer` actie

Als u uw agent wilt verbinden met uw Knowledge Base, gebruikt u automatische energie bewerking om de actie te maken. Energie automatisering biedt een proces stroom, waarmee verbinding wordt gemaakt met de API van QnA Maker `GenerateAnswer` .

Nadat u de stroom hebt ontworpen en opgeslagen, is deze beschikbaar via een geautomatiseerde oplossing voor het automatiseren van een stroom. Gebruik deze oplossing als een actie in uw agent.

## <a name="connect-an-agent-to-your-knowledge-base"></a>Een agent verbinden met uw kennis database

Hier volgt een overzicht van de stappen om een agent in Power Virtual agents te verbinden met een kennis database in QnA Maker.

* In de [QnA Maker](https://www.qnamaker.ai/) portal:
    * Uw Knowledge Base maken en publiceren.
    * Kopieer uw Knowledge Base-gegevens, met inbegrip van de ID, runtime-eindpunt sleutel en runtime-eindpunt host.
* In de Power door de portal voor [virtuele agents](https://powerva.microsoft.com/) :
    * Bouw een onderwerp voor de agent.
    * Een actie aanroepen (om stroom te automatiseren).
* In de portal voor [automatisch opstarten](https://us.flow.microsoft.com/) :
    * Zoeken naar _antwoord genereren met QnA Maker_ sjabloon
    * Gebruik de sjabloon om flow te configureren voor [het gebruik van de GenerateAnswer van QnA Maker](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/).
        * QnA Maker gepubliceerde Knowledge Base-informatie:
            * Knowledge Base-ID
            * Host voor QnA Maker van resource-eind punt
            * QnA Maker bron eindpunt sleutel
        * Invoer-gebruikers query
        * Resultaat van uitvoer-Knowledge Base
    * Maak een oplossing en voeg de stroom toe, of Voeg stroom toe aan een bestaande oplossing.
* Terug naar Power Virtual agents:
    * Selecteer de uitvoer van de oplossing als een bericht voor een onderwerp.

## <a name="create-and-publish-a-knowledge-base"></a>Een knowledge base maken

1. Volg de [Quick](../Quickstarts/create-publish-knowledge-base.md) start om een Knowledge Base te maken. Vul niet de laatste sectie in, over het maken van een bot. Gebruik in plaats daarvan deze zelf studie voor het maken van een bot met Power Virtual agents.

    > [!div class="mx-imgBorder"]
    > ![Scherm opname van gepubliceerde kennis basis instellingen](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    Voer uw gepubliceerde Knowledge Base-instellingen in op de pagina **instellingen** in de [QnA Maker](https://www.qnamaker.ai/) Portal. U hebt deze informatie nodig om de [stap](#create-a-power-automate-flow-to-connect-to-your-knowledge-base) voor het automatisch door lopen van uw QnA Maker-verbinding te configureren `GenerateAnswer` .

1. Zoek in de QnA Maker-Portal op de pagina **instellingen** de eindpunt sleutel, de eindpunt host en de Knowledge Base-id op.

## <a name="create-an-agent-in-power-virtual-agents"></a>Een agent maken in Power virtuele agents

1. [Meld u aan bij Power virtuele agents](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409). Gebruik uw school-of werk-e-mail account.

1. Als dit de eerste bot is, bevindt u zich op de **Start** pagina van de agent. Als dit niet uw eerste bot is, selecteert u de bot in de rechter bovenhoek van de pagina en selecteert u **+ nieuwe bot**.

    > [!div class="mx-imgBorder"]
    > ![Scherm opname van de start pagina van de virtuele Power-agents](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

## <a name="topics-provided-in-the-bot"></a>Onderwerpen in de bot

De agent gebruikt de verzameling onderwerp voor het beantwoorden van vragen in uw onderwerpgebied. In deze zelf studie heeft de agent veel onderwerpen die u hebt ontvangen, onderverdeeld in gebruikers onderwerpen en systeem onderwerpen.

Selecteer **onderwerpen** in het navigatie deel venster aan de linkerkant om de onderwerpen te bekijken die worden weer gegeven door de bot.

> [!div class="mx-imgBorder"]
> ![Scherm afbeelding van de onderwerpen die in de agent zijn opgenomen](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-the-system-fallback-topic"></a>Het terugval onderwerp van het systeem maken

Hoewel de agent vanuit elk onderwerp verbinding kan maken met uw Knowledge Base, maakt deze zelf studie gebruik van het *systeem terugval* onderwerp. Het onderwerp terugval wordt gebruikt wanneer de agent geen antwoord kan vinden. De agent geeft de tekst van de gebruiker door aan de API van QnA Maker `GenerateAnswer` , ontvangt het antwoord uit uw Knowledge Base en geeft deze weer als een bericht aan de gebruiker.

1. Selecteer in de portal [virtuele Power agents](https://powerva.microsoft.com/#/) in de rechter bovenhoek **instellingen** (het tandwiel pictogram). Selecteer vervolgens **systeem terugval**.

    > [!div class="mx-imgBorder"]
    > ![Scherm afbeelding van het menu-item Power virtuele agents voor systeem terugval](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. Selecteer **+ toevoegen** om een terugval onderwerp voor het systeem toe te voegen.

    > [!div class="mx-imgBorder"]
    > ![Scherm opname van het toevoegen van een terugval onderwerp.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. Nadat het onderwerp is toegevoegd, selecteert u **Ga naar terugval onderwerp** om het terugval onderwerp op het bewerkings canvas te schrijven.

    > [!TIP]
    > Als u terug wilt naar het terugval onderwerp, kunt u het vinden in de sectie **onderwerpen** als onderdeel van de **systeem** onderwerpen.

## <a name="use-the-authoring-canvas-to-add-an-action"></a>Het teken papier gebruiken om een actie toe te voegen

Gebruik de virtuele Power agents voor het ontwerpen van canvas om het terugval onderwerp te koppelen aan uw Knowledge Base. Het onderwerp begint met de niet-herkende gebruikers tekst. Een actie toevoegen waarmee deze tekst wordt door gegeven aan QnA Maker en vervolgens het antwoord als een bericht wordt weer gegeven. De laatste stap voor het weer geven van een antwoord wordt behandeld als een [afzonderlijke stap](#add-your-solutions-flow-to-power-virtual-agents), verderop in deze zelf studie.

Deze sectie maakt de conversatie stroom van het terugval onderwerp.

1. De nieuwe terugval actie heeft mogelijk al discussie stroom-elementen. Verwijder het item **escaleren** door het menu **Opties** te selecteren.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/delete-escalate-action-using-option-menu.png" alt-text="Gedeeltelijke scherm afbeelding van de conversatie stroom, met de optie verwijderen gemarkeerd.":::

1. Selecteer de **+** connector die naar het **bericht** venster stroomt en selecteer vervolgens **een actie aanroepen**.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png" alt-text="Gedeeltelijke scherm opname van een actie aanroepen.":::

1. Selecteer **Een stroom maken**. Het proces gaat u naar de Power portal voor automatisch uitvoeren.

    > [!div class="mx-imgBorder"]
    > ![Scherm opname van een stroom maken](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)


    Automatisch naar een nieuwe sjabloon wordt geautomatiseerd. U gebruikt deze nieuwe sjabloon niet.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-automate-flow-initial-template.png" alt-text="Gedeeltelijke scherm opname van het automatiseren van energie met een nieuwe stroom sjabloon.":::

## <a name="create-a-power-automate-flow-to-connect-to-your-knowledge-base"></a>Een stroom voor het automatiseren van stromen maken voor verbinding met uw Knowledge Base

Met de volgende procedure maakt u een stroom voor energie automatisering die:
* Neemt de binnenkomende gebruikers tekst en verzendt deze naar QnA Maker.
* Retourneert het bovenste antwoord terug naar uw agent.

1. Selecteer in **Automatische stroom automatisch**de optie **sjablonen** in de linkernavigatiebalk. Als u wordt gevraagd of u de browser pagina wilt verlaten, accepteert u verlof.

1. Zoek op de pagina sjablonen naar de sjabloon **antwoord genereren met QnA Maker** Selecteer vervolgens de sjabloon. Deze sjabloon bevat alle stappen voor het aanroepen van QnA Maker met de instellingen van uw Knowledge Base en het bovenste antwoord wordt geretourneerd.

1. Selecteer **door gaan**in het nieuwe scherm voor de QnA Maker stroom.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-automate-qna-flow-template-continue.png" alt-text="Gedeeltelijke scherm opname van QnA Maker sjabloon stroom met de knop door gaan gemarkeerd.":::

1. Selecteer het vak **antwoord actie genereren** en vul uw QnA Maker-instellingen in vanuit een vorige sectie met de titel [een Knowledge Base maken en publiceren](#create-and-publish-a-knowledge-base). Uw **servicehost in de** volgende afbeelding verwijst naar de host van uw Knowledge **Base en heeft** de indeling van `https://YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker` .


    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fill-in-generate-answer-settings.png" alt-text="Gedeeltelijke scherm opname van QnA Maker sjabloon stroom met de knop door gaan gemarkeerd.":::

1. Selecteer **Opslaan** om de stroom op te slaan.

## <a name="create-a-solution-and-add-the-flow"></a>Een oplossing maken en de stroom toevoegen

Om de stroom te kunnen vinden en er verbinding mee te maken, moet de stroom zijn opgenomen in een oplossing voor het automatiseren van de stroom.

1. Terwijl u nog steeds in de Power Portal automatiseert, selecteert u **oplossingen** in de navigatie aan de linkerkant.

1. Selecteer **+ Nieuwe oplossing**

1. Voer een weergavenaam in. De lijst met oplossingen omvat elke oplossing in uw organisatie of school. Kies een naamgevings Conventie waarmee u kunt filteren op alleen uw oplossingen. U kunt bijvoorbeeld een voor voegsel van uw e-mail bericht toevoegen aan de naam van de oplossing: `jondoe-power-virtual-agent-qnamaker-fallback` .

1. Selecteer uw uitgever in de lijst met opties.

1. Accepteer de standaard waarden voor de naam en versie.

1. Selecteer **maken** om het proces te volt ooien.

## <a name="add-your-flow-to-the-solution"></a>Uw stroom aan de oplossing toevoegen

1. Selecteer de oplossing die u zojuist hebt gemaakt in de lijst met oplossingen. Deze moet boven aan de lijst staan. Als dat niet het geval is, kunt u zoeken op uw e-mail naam, die deel uitmaakt van de naam van de oplossing.

1. Selecteer in de oplossing **+ bestaande toevoegen**en selecteer vervolgens **stroom** in de lijst.

1. Zoek in de lijst met **buiten oplossingen** uw stroom en selecteer **toevoegen** om het proces te volt ooien. Als er veel stromen zijn, bekijkt u de **gewijzigde** kolom om de meest recente stroom te vinden.

## <a name="add-your-solutions-flow-to-power-virtual-agents"></a>De stroom van uw oplossing toevoegen aan Power virtuele agents

1. Ga terug naar het browser tabblad met uw agent in Power Virtual agents. Het ontwerp teken is nog steeds geopend.

1. Als u een nieuwe stap in de stroom wilt invoegen, selecteert u de connector boven het vak **bericht** actie **+** . Selecteer vervolgens **een actie aanroepen**.

1. Selecteer in het pop-upvenster **stroom** de nieuwe stroom met de naam **antwoorden genereren met behulp van QnA Maker Knowledge Base...**. De nieuwe actie wordt weer gegeven in de stroom.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-flow-after-adding-action.png" alt-text="Gedeeltelijke scherm afbeelding van het teken papier van de stroom van de virtuele Power agent-onderwerp na het toevoegen van QnA Maker stroom.":::

1. Als u de invoer variabele correct wilt instellen op de QnA Maker actie, selecteert u **een variabele selecteren**en selecteert u **bot. UnrecognizedTriggerPhrase**.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-selection-action-input.png" alt-text="Gedeeltelijke scherm afbeelding van het teken papier voor het uitschakelen van de virtuele agent voor het onderwerp invoer variabele selecteren.":::


1. Als u de uitvoer variabele correct wilt instellen op de QnA Maker actie, selecteert u in de **bericht** actie **UnrecognizedTriggerPhrase**, selecteert u het pictogram om een variabele in te voegen en `{x}` selecteert u vervolgens **FinalAnswer**.

1. Selecteer in de context werkbalk de optie **Opslaan**om de details van het bewerkings canvas voor het onderwerp op te slaan.

Dit is het laatste teken van de agent.

> [!div class="mx-imgBorder"]
> ![Scherm opname van het canvas van de laatste agent](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-the-agent"></a>De agent testen

1. In het deel venster test kunt u **bijhouden tussen de onderwerpen**. Zo kunt u de voortgang tussen onderwerpen en binnen één onderwerp bekijken.

1. Test de agent door de gebruikers tekst in de volgende volg orde in te voeren. Het ontwerp doek rapporteert de geslaagde stappen met een groen vinkje.

    |Vraag volgorde|Test vragen|Functie|
    |--|--|--|
    |1|Hello|Gesprek starten|
    |2|Store-uren|Voorbeeld onderwerp. Dit is voor u geconfigureerd zonder extra werk aan uw kant.|
    |3|Ja|In antwoord op`Did that answer your question?`|
    |4|Uitstekend|In antwoord op`Please rate your experience.`|
    |5|Ja|In antwoord op`Can I help with anything else?`|
    |6|Hoe kan ik de prestaties van de door Voer voor query voorspellingen verbeteren?|Met deze vraag wordt de terugval actie geactiveerd, waarmee de tekst naar uw kennis basis wordt verzonden om te beantwoorden. Vervolgens wordt het antwoord weer gegeven. de groene controles voor de afzonderlijke acties geven aan dat er is geslaagd voor elke actie.|

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png" alt-text="Scherm opname van de chat-bot met het canvas dat het groene vinkje voor geslaagde acties aangeeft.":::

## <a name="publish-your-bot"></a>Uw bot publiceren

Als u de agent beschikbaar wilt maken voor alle leden van uw school of organisatie, moet u deze publiceren.

1. Selecteer in de navigatie aan de linkerkant **publiceren**. Selecteer vervolgens **publiceren** op de pagina.

1. Probeer uw bot uit op de demo website (zoek naar de koppeling onder **publiceren**).

    Er wordt een nieuwe webpagina geopend met uw bot. Vraag de bot op dezelfde test vraag:`How can I improve the throughput performance for query predictions?`

    > [!div class="mx-imgBorder"]
    > ![Scherm opname van het canvas van de laatste agent](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>Uw bot delen

Als u de demo website wilt delen, configureert u deze als een kanaal.

1. Selecteer in de navigatie aan de linkerkant de **Manage**optie  >  **kanalen**beheren.

1. Selecteer **demo website** in de lijst met kanalen.

1. Kopieer de koppeling en selecteer **Opslaan**. Plak de koppeling naar uw demo website in een e-mail naar uw school of organisatie leden.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met de Knowledge Base, verwijdert u de QnA Maker resources in de Azure Portal.

## <a name="next-step"></a>Volgende stap

[Analytische gegevens verkrijgen voor uw knowledge base](../How-To/get-analytics-knowledge-base.md)

Meer informatie over:
* [Power Virtual Agents](https://docs.microsoft.com/power-virtual-agents/)
* [Power Automate](https://docs.microsoft.com/power-automate/)
* [QnA Maker connector](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/) en de [instellingen voor de connector](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)