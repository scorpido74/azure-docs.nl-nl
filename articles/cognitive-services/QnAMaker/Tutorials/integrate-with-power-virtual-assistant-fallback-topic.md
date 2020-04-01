---
title: 'Zelfstudie: Integreren met Power Virtual Agent - QnA Maker'
description: In deze tutorial, het verbeteren van de kwaliteit van uw kennisbank met actief leren. Bestaande vragen controleren, accepteren of weigeren, toevoegen zonder bestaande vragen te verwijderen of te wijzigen.
ms.topic: tutorial
ms.date: 03/11/2020
ms.openlocfilehash: 283667c587e395a1d712f82f3385582b4c5c3227
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398875"
---
# <a name="tutorial-add-knowledge-base-to-power-virtual-agent"></a>Zelfstudie: Knowledge Base toevoegen aan Power Virtual Agent
Maak en breid een [Power Virtual Agent-bot](https://powervirtualagents.microsoft.com/) uit om antwoorden te geven vanuit je kennisbank.

**In deze zelfstudie leert u het volgende:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Power Virtual Agent maken
> * Onderwerp van het terugdraaien van het systeem maken
> * QnA Maker toevoegen als actie aan onderwerp als Power Automate flow
> * Power Automate-oplossing maken
> * Power Automate flow toevoegen aan oplossing
> * Power Virtual Agent publiceren
> * Test Power Virtual Agent, ontvang antwoord van QnA Maker knowledge base

## <a name="integrate-a-power-virtual-agent-with-a-knowledge-base"></a>Integreer een Power Virtual Agent met een kennisbank

[Power Virtual Agents](https://powervirtualagents.microsoft.com/) stelt teams in staat om eenvoudig krachtige bots te maken met behulp van een begeleide, no-code grafische interface zonder dat gegevenswetenschappers of ontwikkelaars nodig zijn.

Een Power Virtual Agent wordt gemaakt met een reeks onderwerpen (onderwerpgebieden), om vragen van gebruikers te beantwoorden door acties uit te voeren. Als een antwoord niet kan worden gevonden, kan een terugval van het systeem een antwoord retourneren.

Configureer de agent om de vraag naar uw kennisbank te sturen als onderdeel van de actie van een onderwerp of als onderdeel van het **onderwerppad Van de Valback** van het systeem. Ze gebruiken allebei hetzelfde mechanisme van een actie om verbinding te maken met je kennisbank en een antwoord terug te geven.

## <a name="power-automate-connects-to-generateanswer-action"></a>Power Automate maakt verbinding met generateanswer-actie

Als u uw agent wilt verbinden met uw kennisbank, gebruikt u Power Automate om de actie te maken. Power Automate biedt een **processtroom,** die verbinding maakt met de GenerateAnswer API van QnA Maker.

Zodra de **stroom** is ontworpen en opgeslagen, is deze beschikbaar via een Power **Automate-oplossing.**  Zodra de GenerateAnswer-stroom aan een oplossing is toegevoegd, gebruikt u die oplossing als een actie in uw agent.

## <a name="process-steps-to-connect-an-agent-to-your-knowledge-base"></a>Processtappen om een agent te verbinden met uw kennisbank

De volgende stappen worden gepresenteerd als een overzicht om u te helpen begrijpen hoe de stappen zich verhouden tot het doel van het aansluiten van een Power Virtual Agent op een QnA Maker-kennisbank.

Stappen om een Power Virtual-agent te gebruiken met QnA Maker:
* In [QnA Maker](https://www.qnamaker.ai/) portal
    * Kennisbank bouwen en publiceren
    * Kopieer kennisbasisdetails, waaronder knowledge base-id, runtime endpoint-toets en runtime endpointhost.
* In [Power Virtual Agent-portal](https://powerva.microsoft.com/)
    * Agentonderwerp bouwen
    * Een actie aanroepen (naar Power Automate Flow)
* In [Power Automate](https://us.flow.microsoft.com/) portal
    * Bouw een stroom met connector naar [QnA Maker's GenerateAnswer](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)
        * QnA Maker publiceert kennisbankinformatie
            * Knowledge base ID
            * QnA Maker resource endpoint host
            * Eindpuntsvan QnA Maker-resource
        * Invoer - gebruikersquery
        * Output - knowledge base antwoord
    * Oplossing maken en flow toevoegen
* Terug naar Power Virtual Agent
    * De uitvoer van de oplossing selecteren als bericht voor onderwerp

## <a name="create-and-publish-a-knowledge-base"></a>Een knowledge base maken

1. Volg de [quickstart](../Quickstarts/create-publish-knowledge-base.md) om een kennisbank te creëren. Voltooi de laatste sectie niet om een bot te maken. Deze zelfstudie is een vervanging voor het laatste deel van de quickstart, omdat deze zelfstudie de Power Virtual Agent gebruikt om een bot te maken, in plaats van de Bot Framework-bot in de quickstart.

    > [!div class="mx-imgBorder"]
    > ![Voer uw gepubliceerde knowledge base-instellingen in op de pagina **Instellingen** in dehttps://www.qnamaker.ai/) portal [QnA Maker].](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    U hebt deze informatie nodig voor de [stap Power Automate](#create-power-automate-flow-to-connect-to-your-knowledge-base) om uw QnA Maker GenerateAnswer-verbinding te configureren.

1. Zoek de eindpuntsleutel, eindpunthost en knowledge base-id op de pagina **Instellingen** in de QnA Maker-portal.

## <a name="create-power-virtual-agent"></a>Power Virtual Agent maken

1. [Meld u aan bij](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409) de Power Virtual Agent met uw school of werk e-mailaccount.
1. Als dit uw eerste bot is, moet u op de **startpagina** van de agent staan. Als dit niet uw eerste Power Virtual Agent is, selecteert u de bot in de navigatie rechtsboven en selecteert **u + Nieuwe bot**.

    > [!div class="mx-imgBorder"]
    > ![Voer uw gepubliceerde knowledge base-instellingen in op de pagina **Instellingen** in dehttps://www.qnamaker.ai/) portal [QnA Maker].](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

## <a name="several-topics-are-provided-in-the-bot"></a>Verschillende onderwerpen zijn opgenomen in de bot

De agent gebruikt de onderwerpverzameling om vragen in uw vakgebied te beantwoorden. In deze tutorial, de agent heeft veel onderwerpen voor u, verdeeld in **user topics** en **systeem onderwerpen**.

> [!div class="mx-imgBorder"]
> ![De agent gebruikt de onderwerpverzameling om vragen in uw vakgebied te beantwoorden. In deze zelfstudie heeft de agent veel onderwerpen voorzien in **Gebruikersonderwerpen** en **Systeemonderwerpen**.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-power-virtual-agents-system-fallback-topic"></a>Het terugvalonderwerp van Power Virtual Agent's System maken

Terwijl de agent kan verbinding maken met uw kennisbank van elk onderwerp, deze tutorial maakt gebruik van het System **Fallback** onderwerp. Het terugvalonderwerp wordt gebruikt wanneer de agent geen antwoord kan vinden. De agent geeft de tekst van de gebruiker door aan de GenerateAnswer API van QnA Maker, ontvangt het antwoord van uw kennisbank en geeft deze weer als bericht weer aan de gebruiker.

1. Selecteer in de portal [Power Virtual Agents](https://powerva.microsoft.com/#/) in de rechterbovenhoek van de navigatie de pagina **Instellingen.** Het pictogram voor deze pagina is de versnelling. Selecteer **Systeemterugval**.

    > [!div class="mx-imgBorder"]
    > ![Power Virtual-agentmenu-item voor System Fallback](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. Selecteer in het **Settings** venster Pop-upinstellingen de optie **+ Toevoegen** om een Systeemterugvalonderwerp toe te voegen.

    > [!div class="mx-imgBorder"]
    > ![Voeg in het venster Instellingen het terugvalonderwerp toe.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. Nadat het onderwerp is toegevoegd, selecteert u **Ga naar Fallback-onderwerp** om het Fallback-onderwerp op het ontwerpcanvas te schrijven.

    > [!TIP]
    > Als u wilt terugkeren naar het Fallback-onderwerp, is het beschikbaar in de sectie **Onderwerpen,** als onderdeel van de **systeemonderwerpen.**

## <a name="use-authoring-canvas-to-add-an-action"></a>Ontwerpcanvas gebruiken om een actie toe te voegen

Gebruik het canvas voor het ontwerpen van Power Virtual Agents om het Fallback-onderwerp aan te sluiten op uw kennisbank. Het onderwerp begint met de **niet-herkende gebruikerstekst.** Voeg een actie toe die die tekst doorgeeft aan QnA Maker en het antwoord vervolgens als een bericht weergeeft. De laatste stap van het weergeven van een antwoord wordt behandeld als een [aparte stap](#add-solutions-flow-to-power-virtual-agent) later in deze tutorial.

In deze sectie wordt de gespreksstroom van het terugvalonderwerp geopperd.

1. De nieuwe Fallback-actie kan al gespreksstroomelementen bevatten. Verwijder het item **Escaleren** door het menu Opties te selecteren.

    > [!div class="mx-imgBorder"]
    > ![Fallback-actie starten met triggerzinnen](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fallback-topic-delete-escalate.png)

1. Selecteer **+** de connector die uit het vak **Bericht** stroomt en selecteer **Een actie aanroepen**.

    > [!div class="mx-imgBorder"]
    > ![Een actie aanroepen](../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png)

1. Selecteer **Een stroom maken**. Het proces brengt u naar **Power Automate,** een andere browser-gebaseerde portal.

    > [!div class="mx-imgBorder"]
    > ![Een actie aanroepen](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)

## <a name="create-power-automate-flow-to-connect-to-your-knowledge-base"></a>Maak Power Automate Flow om verbinding te maken met uw kennisbank

Met de volgende procedure wordt een **power automate-stroom gezoen** die:
* neemt de binnenkomende gebruikerstekst
* stuurt het naar QnA Maker
* wijst het topantwoord van QnA Maker toe op een variabele
* stuurt de variabele (bovenste antwoord) als het antwoord terug naar uw agent

1. In **Power Automate**wordt de **stroomsjabloon** voor u gestart. Selecteer **bewerken** om de invoervariabele van de agent naar uw kennisbank te configureren in het **stroomitem Power Virtual Agents.** De op tekst gebaseerde invoervariabele is de door de gebruiker ingediende tekstvraag van uw agent.

    > [!div class="mx-imgBorder"]
    > ![De invoervariabele configureren als teksttekenreeks](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable.png)

1. Voeg een tekstinvoer toe `InputText` en geef `IncomingUserQuestion`de variabele een naam met een beschrijving van . Met deze naamgeving u de invoertekst onderscheiden van de uitvoertekst die u later maakt.

    > [!div class="mx-imgBorder"]
    > ![Voeg een tekstinvoer toe en geef de variabele 'InputText' een naam met een beschrijving van 'UserQuestion'](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable-name-and-description.png)

1. Selecteer **+** de connector die uit het vak **Virtuele energiegemachtigden** stroomt om een nieuwe stap in de stroom in te voegen (vóór de **waarde retourneren naar Power Virtual Agent)** en selecteer **Vervolgens Een actie toevoegen**.

1. Zoek `Qna` naar de **qnA maker-acties** en selecteer **Antwoord genereren**.

    > [!div class="mx-imgBorder"]
    > ![Zoek naar 'Qna' om de **QnA Maker** acties te vinden en selecteer vervolgens **Antwoord genereren**](../media/how-to-integrate-power-virtual-agent/generate-answer-action-selected.png)

    De drie (3) vereiste verbindingsinstellingen voor QnA Maker worden weergegeven in de actie en de vraaginstellingen van de Power Virtual Agent.

    > [!div class="mx-imgBorder"]
    > ![De verbindingsinstellingen voor QnA Maker worden in de actie weergegeven.](../media/how-to-integrate-power-virtual-agent/generate-answer-knowledge-base-settings.png)

1. Configureer de actie met uw knowledge base-id, eindpunthost en eindpunttoets. Deze zijn te vinden op de **instellingen** pagina van uw kennisbank, in de QnA Maker portal.

    > [!div class="mx-imgBorder"]
    > ![Voer uw gepubliceerde knowledge base-instellingen in op de pagina **Instellingen** in dehttps://www.qnamaker.ai/) portal [QnA Maker].](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

1. Als u de **vraag wilt**configureren, `InputText` selecteert u het tekstvak en selecteert u het in de lijst.

1. Als u een nieuwe stap in **+** de stroom wilt invoegen, selecteert u de connector die uit het actievak **Antwoord genereren** stroomt en selecteert u **Een actie toevoegen**.

1. Als u een variabele wilt toevoegen om de antwoordtekst `Initialize variable` vast te leggen die is geretourneerd vanuit GenerateAnswer, zoekt en selecteert u de actie.

    Stel de naam van `OutgoingQnAAnswer`de variabele in op , en selecteer het type als **Tekenreeks**. Stel de **waarde**niet in.

    > [!div class="mx-imgBorder"]
    > ![Stel de naam van de variabele in op 'QnAAnswer' en selecteer het type als **Tekenreeks**](../media/how-to-integrate-power-virtual-agent/initialize-output-variable-for-qna-answer.png)

1. Als u een nieuwe stap in **+** de stroom wilt invoegen, selecteert u de connector die uit het vak Variabele actie **Initialize** stroomt en selecteert u **Een actie toevoegen**.

1. Als u de volledige knowledge base JSON-respons op`Apply to each` de variabele wilt instellen, zoekt en selecteert u de actie. Selecteer het `answers`antwoord genereren .

1. Als u alleen het bovenste antwoord wilt retourneren, selecteert u in hetzelfde **vak Toepassen op elk** vak de optie Een actie **toevoegen**. Zoeken naar en selecteer **Variabele instellen**.

    Selecteer **in** het vak Variabele instellen het tekstvak voor **Naam**en selecteer **OutgoingQnAAnswer** in de lijst.

    Selecteer het tekstvak voor **Waarde**en selecteer **Antwoord antwoord** in de lijst.

    > [!div class="mx-imgBorder"]
    > ![De naam en waarde voor de variabele instellen](../media/how-to-integrate-power-virtual-agent/power-automate-flow-apply-to-each-set-variable.png)

1. Als u de variabele (en de waarde ervan) wilt retourneren, selecteert u de **waarde retourneren naar het stroomitem van De Virtuele Agent** van Power en selecteert u **Bewerken**en voegt u vervolgens een **uitvoer toe**. Selecteer een **tekstuitvoertype** en `FinalAnswer`voer de titel **van** . Selecteer het tekstvak **Value**voor de `OutgoingQnAAnswer` waarde en selecteer vervolgens de variabele.

    > [!div class="mx-imgBorder"]
    > ![De retourwaarde instellen](../media/how-to-integrate-power-virtual-agent/power-automate-flow-return-value.png)

1. Selecteer **Opslaan** om de stroom op te slaan.

## <a name="create-solution-and-add-flow"></a>Oplossing maken en flow toevoegen

Om de Power Virtual Agent te laten vinden en verbinding te maken met de stroom, moet de stroom worden opgenomen in een Power Automate-oplossing.

1. Terwijl u nog steeds in de Power Automate-portal zit, selecteert u **Oplossingen** in de navigatie aan de linkerkant.

1. Selecteer **+ Nieuwe oplossing**

1. Voer een weergavenaam in. De lijst met oplossingen bevat elke oplossing in uw organisatie of school. Kies een naamgevingsconventie die u helpt te filteren op alleen uw `jondoe-power-virtual-agent-qnamaker-fallback`oplossingen, zoals het vooraf herstellen van uw e-mail naar uw oplossingsnaam: .

1. Selecteer uw uitgever in de lijst met keuzes.

1. Accepteer de standaardwaarden voor de naam en versie.

1. Selecteer **Maken** om het proces te voltooien.

## <a name="add-flow-to-solution"></a>Stroom toevoegen aan oplossing

1. Selecteer in de lijst met oplossingen de oplossing die u zojuist hebt gemaakt. Het zou bovenaan de lijst moeten staan. Als dit niet het is, zoekt u op uw e-mailnaam, die deel uitmaakt van de naam van de oplossing.

1. Selecteer in de oplossing **+ Bestaande toevoegen**en selecteer **Stroom** in de lijst.

1. Zoek uw stroom en selecteer **Toevoegen** om het proces te voltooien. Als er veel stromen zijn, kijkt u naar de **kolom Gewijzigd** om de meest recente stroom te vinden.

## <a name="add-solutions-flow-to-power-virtual-agent"></a>De stroom van de oplossing toevoegen aan Power Virtual Agent

1. Ga terug naar het tabblad browser met uw Power Virtual Agent. Het ontwerpcanvas moet nog steeds geopend zijn.

1. Selecteer **+** de connector onder het **actievak Bericht** om een nieuwe stap in de stroom in te voegen en selecteer **Een actie aanroepen**.

1. Selecteer in de nieuwe actie de invoerwaarde van **Niet-herkende TriggerPhrase**. Hiermee wordt de tekst van de agent doorgegeven aan de stroom.

    > [!div class="mx-imgBorder"]
    > ![Selecteer in de nieuwe actie de invoerwaarde van **Niet-herkende TriggerPhrase**.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-select-unrecognized-trigger-phrase.png)

1. Selecteer **+** de connector onder het vak **Actie** om een nieuwe stap in de stroom in te voegen en selecteer **Een bericht weergeven**.

1. Voer de berichttekst `Your answer is:`in `FinalAnswer` en selecteer als contextvariabele met behulp van de functie van de werkbalk ter plaatse.

    > [!div class="mx-imgBorder"]
    > ![Voer de berichttekst en het 'FinalAnswer' van de Power Automate Flow in.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-show-message-final-answer.png)

1. Selecteer **Opslaan op** de contextwerkbalk om de details van het ontwerpcanvas voor het onderwerp op te slaan.

Het laatste doek wordt hieronder weergegeven.

> [!div class="mx-imgBorder"]
> ![Laatste agentcanvas](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-power-virtual-agent"></a>Test Power Virtual Agent

1. Schakel in het testvenster **Track tussen onderwerpen**in. Hiermee u de voortgang tussen onderwerpen en in één onderwerp bekijken.

1. Test de agent door de gebruikerstekst in de onderstaande volgorde in te voeren. Het ontwerpcanvas rapporteert de geslaagde stappen met een groen vinkje.

|Vraagvolgorde|Testvragen|Doel|
|--|--|--|
|1|Hello|Gesprek beginnen|
|2|Winkeluren|Voorbeeldonderwerp - geconfigureerd voor u zonder extra werk van uw kant.|
|3|Ja|In antwoord op`Did that answer your question?`|
|4|Uitstekend|In antwoord op`Please rate your experience.`|
|5|Ja|In antwoord op`Can I help with anything else?`|
|6|Wat is een kennisbank?|Deze vraag activeert de terugvalactie, die de tekst naar uw kennisbank stuurt om te beantwoorden, waarna het antwoord wordt weergegeven. |

> [!div class="mx-imgBorder"]
> ![Laatste agentcanvas](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png)

## <a name="publish-your-bot"></a>Uw bot publiceren

Om de agent beschikbaar te maken voor alle leden van uw school of organisatie, moet u deze publiceren.

1. Selecteer **Publiceren** in de linkernavigatie en selecteer **Publiceren** op de pagina.

1. Probeer je bot op de demo website, die als een link onder de **knop Publiceren** .

    Er wordt een nieuwe webpagina geopend met uw bot. Stel de bot dezelfde testvraag:`What is a knowledge base?`

    > [!div class="mx-imgBorder"]
    > ![Laatste agentcanvas](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>Deel je bot

Om de demowebsite te delen, configureer je deze als een kanaal.

1. Selecteer Vervolgens **Kanalen** **beheren** in de linkernavigatie.

1. Selecteer **Demo-website** in de kanalenlijst.

1. Kopieer de koppeling en selecteer **Opslaan**. Plak de link naar uw demowebsite in een e-mail naar uw school- of organisatieleden.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met de kennisbank, verwijdert u de QnA Maker-resources in de Azure-portal.

## <a name="next-step"></a>Volgende stap

[Analytische gegevens verkrijgen voor uw knowledge base](../How-To/get-analytics-knowledge-base.md)

Meer informatie over:
* [Power Virtual Agents](https://docs.microsoft.com/power-virtual-agents/)
* [Power Automate](https://docs.microsoft.com/power-automate/)
* [QnA Maker-connector](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/) en de [instellingen voor de connector](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)