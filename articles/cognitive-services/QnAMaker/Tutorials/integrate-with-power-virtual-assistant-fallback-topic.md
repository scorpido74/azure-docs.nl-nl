---
title: 'Zelfstudie: Integreren met Power Virtual Agents - QnA Maker'
titleSuffix: Azure Cognitive Services
description: In deze tutorial, het verbeteren van de kwaliteit van uw kennisbank met actief leren. Bestaande vragen controleren, accepteren of weigeren of toevoegen zonder bestaande vragen te verwijderen of te wijzigen.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 03/11/2020
ms.author: diberry
ms.openlocfilehash: 4557dee995c8a01067f7e6ad0e79bb7115b6ecdb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402816"
---
# <a name="tutorial-add-your-knowledge-base-to-power-virtual-agents"></a>Zelfstudie: Voeg uw kennisbasis toe aan Power Virtual Agents
Maak en breid een [Power Virtual Agents-bot](https://powervirtualagents.microsoft.com/) uit om antwoorden te geven vanuit je kennisbank.

In deze zelfstudie leert u het volgende:

<!-- green checkmark -->
> [!div class="checklist"]
> * Een Power Virtual Agents-bot maken
> * Een terugvalonderwerp van het systeem maken
> * QnA Maker toevoegen als een actie aan een onderwerp als een stroom voor het automatiseren van stroom
> * Een Oplossing voor Het automatiseren van energie maken
> * Een power automate flow toevoegen aan uw oplossing
> * Power Virtual Agents publiceren
> * Test Power Virtual Agents en ontvang een antwoord van uw QnA Maker-kennisbank

## <a name="integrate-an-agent-with-a-knowledge-base"></a>Integreer een agent met een kennisbank

[Power Virtual Agents](https://powervirtualagents.microsoft.com/) stelt teams in staat om krachtige bots te maken met behulp van een begeleide, no-code grafische interface. U hebt geen gegevenswetenschappers of ontwikkelaars nodig.

In Power Virtual Agents maakt u een agent met een reeks onderwerpen (onderwerpgebieden), om gebruikersvragen te beantwoorden door acties uit te voeren. Als een antwoord niet kan worden gevonden, kan een terugval van het systeem een antwoord retourneren.

Configureer de agent om de vraag naar uw kennisbank te sturen als onderdeel van de actie van een onderwerp of als onderdeel van het *onderwerppad Van de Valback* van het systeem. Beiden gebruiken een actie om verbinding te maken met uw kennisbank en een antwoord terug te geven.

## <a name="power-automate-connects-to-generateanswer-action"></a>Power Automate `GenerateAnswer` maakt verbinding met actie

Als u uw agent wilt verbinden met uw kennisbank, gebruikt u Power Automate om de actie te maken. Power Automate biedt een processtroom, die verbinding `GenerateAnswer` maakt met de API van QnA Maker.

Nadat u de stroom hebt ontworpen en opgeslagen, is deze beschikbaar via een Power Automate-oplossing. Gebruik die oplossing als een actie in uw agent.

## <a name="connect-an-agent-to-your-knowledge-base"></a>Verbind een agent met uw kennisbank

Hier volgt een overzicht van de stappen om een agent in Power Virtual Agents te verbinden met een kennisbank in QnA Maker.

* In de [QnA Maker](https://www.qnamaker.ai/) portal:
    * Bouw en publiceer je kennisbank.
    * Kopieer uw kennisbasisgegevens, waaronder de ID, runtime endpoint-toets en runtime endpointhost.
* In de [Power Virtual Agents](https://powerva.microsoft.com/) portal:
    * Bouw een agent onderwerp.
    * Een actie aanroepen (naar Power Automate flow).
* In de [Power Automate](https://us.flow.microsoft.com/) portal:
    * Bouw een flow met een connector naar [QnA Maker's GenerateAnswer.](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)
        * QnA Maker publiceerde kennisbankinformatie:
            * Knowledge base ID
            * QnA Maker resource endpoint host
            * Eindpuntsvan QnA Maker-resource
        * Invoer - gebruikersquery
        * Output - knowledge base antwoord
    * Maak een oplossing en voeg de stroom toe.
* Terug naar Power Virtual Agents:
    * Selecteer de uitvoer van de oplossing als een bericht voor een onderwerp.

## <a name="create-and-publish-a-knowledge-base"></a>Een knowledge base maken

1. Volg de [quickstart](../Quickstarts/create-publish-knowledge-base.md) om een kennisbank te creëren. Voltooi de laatste sectie niet over het maken van een bot. Gebruik deze zelfstudie in plaats daarvan om een bot te maken met Power Virtual Agents.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van gepubliceerde instellingen voor kennisbasis](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    Voer uw gepubliceerde kennisbasisinstellingen in op de pagina **Instellingen** in de [QnA](https://www.qnamaker.ai/) Maker-portal. U hebt deze informatie nodig voor de stap Power `GenerateAnswer` [Automate](#create-a-power-automate-flow-to-connect-to-your-knowledge-base) om uw QnA Maker-verbinding te configureren.

1. Zoek in de QnA Maker-portal op de pagina **Instellingen** de eindpuntsleutel, eindpunthost en knowledge base-id.

## <a name="create-an-agent-in-power-virtual-agents"></a>Een agent maken in Power Virtual Agents

1. [Meld u aan bij Power Virtual Agents](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409). Gebruik je school- of werk-e-mailaccount.
1. Als dit je eerste bot is, sta je op de **startpagina** van de agent. Als dit niet de eerste bot is, selecteert u de bot in het rechterbovengedeelte van de pagina en selecteert **u + Nieuwe bot**.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van de startpagina van Power Virtual Agents](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

1. Voer uw gepubliceerde kennisbasisinstellingen in, te vinden op de pagina **Instellingen** in de [QnA](https://www.qnamaker.ai/) Maker-portal.

## <a name="topics-provided-in-the-bot"></a>Onderwerpen in de bot

De agent gebruikt de onderwerpverzameling om vragen in uw vakgebied te beantwoorden. In deze zelfstudie heeft de agent veel onderwerpen voor u beschikbaar, verdeeld in gebruikersonderwerpen en systeemonderwerpen.

> [!div class="mx-imgBorder"]
> ![Schermafbeelding van onderwerpen in de agent](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-the-system-fallback-topic"></a>Het terugvalonderwerp van het systeem maken

Hoewel de agent kan verbinding maken met uw kennisbank van elk onderwerp, deze tutorial maakt gebruik van het *systeem fallback* onderwerp. Het terugvalonderwerp wordt gebruikt wanneer de agent geen antwoord kan vinden. De agent geeft de tekst van de gebruiker `GenerateAnswer` door aan de API van QnA Maker, ontvangt het antwoord van uw kennisbank en geeft deze als bericht aan de gebruiker weer.

1. Selecteer in de [power virtual agents-portal](https://powerva.microsoft.com/#/) in de rechterbovenhoek de optie **Instellingen** (het tandwielpictogram). Selecteer vervolgens **System Fallback**.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van het menu-item Power Virtual Agents voor System Fallback](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. Selecteer **+ Toevoegen** om een terugvalonderwerp van het systeem toe te voegen.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van het toevoegen van een terugvalonderwerp.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. Nadat het onderwerp is toegevoegd, selecteert u **Ga naar Fallback-onderwerp** om het terugvalonderwerp op het ontwerpcanvas te schrijven.

    > [!TIP]
    > Als u terug wilt naar het terugvalonderwerp, is het beschikbaar in de sectie **Onderwerpen,** als onderdeel van de **systeemonderwerpen.**

## <a name="use-the-authoring-canvas-to-add-an-action"></a>Het ontwerpcanvas gebruiken om een actie toe te voegen

Gebruik het canvas voor het ontwerpen van Power Virtual Agents om het terugvalonderwerp aan te sluiten op uw kennisbank. Het onderwerp begint met de niet-herkende gebruikerstekst. Voeg een actie toe die die tekst doorgeeft aan QnA Maker en het antwoord vervolgens als een bericht weergeeft. De laatste stap van het weergeven van een antwoord wordt behandeld als een [aparte stap,](#add-your-solutions-flow-to-power-virtual-agents)later in deze tutorial.

In deze sectie wordt de gespreksstroom van het terugvalonderwerp geopperd.

1. De nieuwe terugvalactie heeft mogelijk al gespreksstroomelementen. Verwijder het item **Escaleren** door het menu **Opties te** selecteren.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van het terugvalonderwerp van Power Virtual Agents](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fallback-topic-delete-escalate.png)

1. Selecteer **+** de connector die uit het vak **Bericht** stroomt en selecteer **Een actie aanroepen**.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van Actie aanroepen](../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png)

1. Selecteer **Een stroom maken**. Het proces brengt u naar de Power Automate portal.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van Een stroom maken](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)

## <a name="create-a-power-automate-flow-to-connect-to-your-knowledge-base"></a>Creëer een Power Automate flow om verbinding te maken met je kennisbank

Met de volgende procedure wordt een power automate-stroom gezoen die:
* Neemt de binnenkomende gebruikerstekst en stuurt deze naar QnA Maker.
* Wijst het topantwoord van QnA Maker toe aan een variabele en stuurt de variabele (bovenaan antwoord) als antwoord terug naar uw agent.

1. In **Power Automate**wordt de **stroomsjabloon** voor u gestart. Selecteer **bewerken** om de invoervariabele van de agent naar uw kennisbank te configureren in het **stroomitem Power Virtual Agents.** De op tekst gebaseerde invoervariabele is de door de gebruiker ingediende tekstvraag van uw agent.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van de optie Power Automate om de invoervariabele als teksttekenreeks te configureren](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable.png)

1. Voeg een tekstinvoer toe `InputText`en geef de `IncomingUserQuestion`variabele een naam met een beschrijving van . Met deze naamgeving u de invoertekst onderscheiden van de uitvoertekst die u later maakt.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van de optie Power Automate om de naam en beschrijving van de invoervariabele te configureren](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable-name-and-description.png)

1. Selecteer **+** de connector die uit het vak **Virtuele energieagents** stroomt om een nieuwe stap in de stroom in te voegen (vóór de **waarde retourneren naar De virtuele agent van Power).** Selecteer vervolgens **Een actie toevoegen**.

1. Zoek `Qna` naar de **qnA maker-acties** en selecteer **Antwoord genereren**.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van Antwoord genereren](../media/how-to-integrate-power-virtual-agent/generate-answer-action-selected.png)

    De vereiste verbindingsinstellingen voor QnA Maker worden weergegeven in de actie en de vraaginstellingen van de agent.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van de vereiste verbindingsinstellingen](../media/how-to-integrate-power-virtual-agent/generate-answer-knowledge-base-settings.png)

1. Configureer de actie met uw knowledge base-id, eindpunthost en eindpunttoets. Deze zijn te vinden op de **instellingen** pagina van uw kennisbank, in de QnA Maker portal.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van gepubliceerde instellingen voor kennisbasis](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

1. Als u de **vraag wilt**configureren, selecteert `InputText` u het tekstvak en selecteert u het in de lijst.

1. Als u een nieuwe stap in **+** de stroom wilt invoegen, selecteert u de connector die uit het actievak **Antwoord genereren** stroomt. Selecteer vervolgens **Een actie toevoegen**.

1. Als u een variabele wilt toevoegen `GenerateAnswer`om de antwoordtekst vast te leggen die is geretourneerd, zoekt en selecteert u de `Initialize variable` actie.

    Stel de naam van `OutgoingQnAAnswer`de variabele in op , en selecteer het type als **Tekenreeks**. Stel de **waarde**niet in.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van het initialiseren van de uitvoervariabele](../media/how-to-integrate-power-virtual-agent/initialize-output-variable-for-qna-answer.png)

1. Als u een nieuwe stap in **+** de stroom wilt invoegen, selecteert u de connector die uit het vak **Initialiseren van variabele** actie stroomt. Selecteer vervolgens **Een actie toevoegen**.

1. Als u de volledige knowledge base JSON-respons op`Apply to each` de variabele wilt instellen, zoekt en selecteert u de actie. Selecteer `GenerateAnswer` `answers`de .

1. Als u alleen het bovenste antwoord wilt retourneren, selecteert u in hetzelfde **vak Toepassen op elk** vak de optie Een actie **toevoegen**. Zoeken naar en selecteer **Variabele instellen**.

    Selecteer **in** het vak Variabele instellen het tekstvak voor **Naam**en selecteer **OutgoingQnAAnswer** in de lijst.

    Selecteer het tekstvak voor **Waarde**en selecteer **Antwoord antwoord** in de lijst.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van het instellen van de naam en waarde voor de variabele](../media/how-to-integrate-power-virtual-agent/power-automate-flow-apply-to-each-set-variable.png)

1. Als u de variabele (en de waarde ervan) wilt retourneren, selecteert u de **waarde(en) retourneren naar het stroomitem van De Virtuele Agent van Power.** **Selecteer** > vervolgens**Een uitvoer bewerken**. Selecteer een **tekstuitvoertype** en voer `FinalAnswer`de titel **van** . Selecteer het tekstvak **Value**voor de waarde `OutgoingQnAAnswer` en selecteer de variabele.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van het instellen van de retourwaarde](../media/how-to-integrate-power-virtual-agent/power-automate-flow-return-value.png)

1. Selecteer **Opslaan** om de stroom op te slaan.

## <a name="create-a-solution-and-add-the-flow"></a>Een oplossing maken en de stroom toevoegen

Om de agent te kunnen vinden en verbinding te maken met de stroom, moet de stroom worden opgenomen in een Power Automate-oplossing.

1. Terwijl u nog steeds in de Power Automate-portal zit, selecteert u **Oplossingen** in de navigatie aan de linkerkant.

1. Selecteer **+ Nieuwe oplossing**

1. Voer een weergavenaam in. De lijst met oplossingen bevat elke oplossing in uw organisatie of school. Kies een naamgevingsconventie die u helpt te filteren op alleen uw oplossingen. U uw e-mail bijvoorbeeld vooraf `jondoe-power-virtual-agent-qnamaker-fallback`fixeren op de naam van uw oplossing:.

1. Selecteer uw uitgever in de lijst met keuzes.

1. Accepteer de standaardwaarden voor de naam en versie.

1. Selecteer **Maken** om het proces te voltooien.

## <a name="add-your-flow-to-the-solution"></a>Voeg uw stroom toe aan de oplossing

1. Selecteer in de lijst met oplossingen de oplossing die u zojuist hebt gemaakt. Het zou bovenaan de lijst moeten staan. Als dit niet het is, zoekt u op uw e-mailnaam, die deel uitmaakt van de naam van de oplossing.

1. Selecteer in de oplossing **+ Bestaande toevoegen**en selecteer Vervolgens **Vloeiend** in de lijst.

1. Zoek uw stroom en selecteer **Toevoegen** om het proces te voltooien. Als er veel stromen zijn, kijkt u naar de **kolom Gewijzigd** om de meest recente stroom te vinden.

## <a name="add-your-solutions-flow-to-power-virtual-agents"></a>De stroom van uw oplossing toevoegen aan Power Virtual Agents

1. Ga terug naar het tabblad browser met uw agent in Power Virtual Agents. Het ontwerpcanvas moet nog steeds geopend zijn.

1. Als u een nieuwe stap in de stroom wilt **+** invoegen, selecteert u onder het **actievak Bericht** de connector. Selecteer vervolgens **Een actie aanroepen**.

1. Selecteer in de nieuwe actie de invoerwaarde van **Niet-herkende TriggerPhrase**. Hiermee wordt de tekst van de agent doorgegeven aan de stroom.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van de optie Virtuele energiegemachtigden om niet-herkende triggerzin te selecteren](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-select-unrecognized-trigger-phrase.png)

1. Als u een nieuwe stap in **Action** de stroom **+** wilt invoegen, selecteert u onder het vak Actie de connector. Selecteer vervolgens **Een bericht weergeven**.

1. Voer de berichttekst in. `Your answer is:` Selecteer `FinalAnswer` als contextvariabele met behulp van de functie van de werkbalk ter plaatse.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van de optie Virtuele energiegemachtigden om de berichttekst in te voeren](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-show-message-final-answer.png)

1. Selecteer op de contextwerkbalk **Opslaan**om de details van het ontwerpcanvas voor het onderwerp op te slaan.

Zo ziet het laatste agentdoek eruit.

> [!div class="mx-imgBorder"]
> ![Schermafbeelding van het canvas van de uiteindelijke agent](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-the-agent"></a>Test de agent

1. Schakel in het testvenster **Track tussen onderwerpen**in. Hiermee u de voortgang tussen onderwerpen bekijken, evenals binnen één onderwerp.

1. Test de agent door de gebruikerstekst in de volgende volgorde in te voeren. Het ontwerpcanvas rapporteert de geslaagde stappen met een groen vinkje.

    |Vraagvolgorde|Testvragen|Doel|
    |--|--|--|
    |1|Hello|Gesprek beginnen|
    |2|Winkeluren|Voorbeeldonderwerp. Dit is voor u geconfigureerd zonder extra werk van uw kant.|
    |3|Ja|In antwoord op`Did that answer your question?`|
    |4|Uitstekend|In antwoord op`Please rate your experience.`|
    |5|Ja|In antwoord op`Can I help with anything else?`|
    |6|Wat is een kennisbank?|Deze vraag activeert de terugvalactie, die de tekst naar uw kennisbank stuurt om te beantwoorden. Dan wordt het antwoord getoond. |

> [!div class="mx-imgBorder"]
> ![Schermafbeelding van het canvas van de uiteindelijke agent](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png)

## <a name="publish-your-bot"></a>Uw bot publiceren

Als u de agent beschikbaar wilt maken voor alle leden van uw school of organisatie, moet u deze publiceren.

1. Selecteer **Publiceren**in de navigatie aan de linkerkant . Selecteer vervolgens **Publiceren** op de pagina.

1. Probeer je bot op de demo website (kijk voor de link onder **Publiceren).**

    Er wordt een nieuwe webpagina geopend met uw bot. Stel de bot dezelfde testvraag:`What is a knowledge base?`

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van het canvas van de uiteindelijke agent](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>Deel je bot

Als u de demowebsite wilt delen, configureert u deze als een kanaal.

1. Selecteer**Kanalen** **beheren** > in de navigatie aan de linkerkant .

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