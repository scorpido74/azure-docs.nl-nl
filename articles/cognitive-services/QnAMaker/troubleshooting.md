---
title: Probleemoplossing - QnA Maker
description: De samengestelde lijst met de meest gestelde vragen over de QnA Maker-service zal u helpen de service sneller en met betere resultaten over te nemen.
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: diberry
ms.openlocfilehash: e002efe74bf7bcd3d944b01b0a25a731a2db3f66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284259"
---
# <a name="troubleshooting-for-qna-maker"></a>Probleemoplossing voor QnA Maker

De samengestelde lijst met de meest gestelde vragen over de QnA Maker-service zal u helpen de service sneller en met betere resultaten over te nemen.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="manage-predictions"></a>Voorspellingen beheren

<details>
<summary><b>Hoe kan ik de doorvoerprestaties voor queryvoorspellingen verbeteren?</b></summary>

**Antwoord:** Problemen met de doorvoerprestatie geven aan dat u moet opschalen voor zowel uw App-service als uw cognitieve zoekopdracht. Overweeg een replica toe te voegen aan uw cognitief zoeken om de prestaties te verbeteren.

Meer informatie over [prijsniveaus](Concepts/azure-resources.md).
</details>

<details>
<summary><b>Hoe krijg je het QnAMaker-serviceeindpunt</b></summary>

**Antwoord:** QnAMaker service eindpunt is handig voor het opsporen van doeleinden wanneer u contact opnemen met QnAMaker Support of UserVoice. Het eindpunt is een URL https://your-resource-name.azurewebsites.netin dit formulier: .

1. Ga naar uw QnAMaker-service (resourcegroep) in de [Azure-portal](https://portal.azure.com)

    ![QnAMaker Azure-brongroep in Azure-portal](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selecteer de App-service die is gekoppeld aan de QnA Maker-bron. Meestal zijn de namen hetzelfde.

     ![QnAMaker-appservice selecteren](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. De URL van het eindpunt is beschikbaar in de sectie Overzicht

    ![QnAMaker-eindpunt](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

## <a name="manage-the-knowledge-base"></a>Beheer de kennisbank

<details>
<summary><b>Ik heb per ongeluk een deel van mijn QnA Maker verwijderd, wat moet ik doen?</b></summary>

**Antwoord:** Verwijder geen van de Azure-services die zijn gemaakt samen met de QnA Maker-bron, zoals Zoeken of Web App. Deze zijn nodig voor QnA Maker om te werken, als u er een verwijdert, zal QnA Maker niet meer correct werken.

Alle verwijderingen zijn permanent, inclusief vraag- en antwoordparen, bestanden, URL's, aangepaste vragen en antwoorden, kennisbases of Azure-bronnen. Zorg ervoor dat u uw kennisbestand **exporteert** vanaf de pagina Instellingen voordat u een deel van uw kennisbank verwijderd.

</details>

<details>
<summary><b>Waarom worden mijn URL(s)/bestand(en) geen vraagbeantwoordingsparen geëxtraheerd?</b></summary>

**Antwoord:** Het is mogelijk dat QnA Maker sommige vraag-en-antwoord (QnA)-inhoud niet automatisch kan extraheren uit geldige FAQ-URL's. In dergelijke gevallen u de QnA-inhoud in een .txt-bestand plakken en zien of het gereedschap het kan innemen. Afwisselend u op redactionele wijze inhoud toevoegen aan uw kennisbank via de [QnA Maker-portal.](https://qnamaker.ai)

</details>

<details>
<summary><b>Hoe groot kan ik een kennisbank maken?</b></summary>

**Antwoord:** De grootte van de kennisbank is afhankelijk van de SKU van Azure-zoekopdrachten die u kiest bij het maken van de QnA Maker-service. Lees [hier](./Tutorials/choosing-capacity-qnamaker-deployment.md) meer informatie.

</details>

<details>
<summary><b>Waarom zie ik niets in de vervolgkeuzelijst als ik een nieuwe kennisbank probeer te creëren?</b></summary>

**Antwoord:** U hebt nog geen QnA Maker-services in Azure gemaakt. Lees [hier](./How-To/set-up-qnamaker-service-azure.md) hoe je dat doet.

</details>

<details>
<summary><b>Hoe deel ik een kennisbank met anderen?</b></summary>

**Antwoord**: Het delen van werken op het niveau van een QnA Maker-service, dat wil zeggen dat alle kennisbanken in de service worden gedeeld. Lees [hier](./How-To/collaborate-knowledge-base.md) hoe je samenwerken aan een kennisbank.

</details>

<details>
<summary><b>u een kennisbank delen met een medewerker die niet in dezelfde AAD-huurder zit, om een kennisbank aan te passen?</b></summary>

**Antwoord:** Delen is gebaseerd op Azure-role-based access control (RBAC). Als u _een_ resource in Azure delen met een andere gebruiker, u ook QnA Maker delen.

</details>

<details>
<summary><b>Als u een App Service Plan met 5 QnAMaker kennisbanken. u lees-/schrijfrechten toewijzen aan 5 verschillende gebruikers, zodat elk van hen toegang heeft tot slechts 1 Kennisbank QnAMaker?</b></summary>

**Antwoord**: U een volledige QnAMaker-service delen, niet individuele kennisbanken.

</details>

<details>
<summary><b>Hoe kan ik het standaardbericht wijzigen als er geen goede overeenkomst wordt gevonden?</b></summary>

**Antwoord:** Het standaardbericht maakt deel uit van de instellingen in uw App-service.
- Ga naar uw App-servicebron in de Azure-portal

![qnamaker appservice](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Klik op de optie **Instellingen**

![qnamaker appservice-instellingen](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- De waarde van de **instelling Standaardantwoord** wijzigen
- Uw App-service opnieuw starten

![qnamaker appservice opnieuw opgestart](./media/qnamaker-faq/qnamaker-appservice-restart.png)


</details>

<details>
<summary><b>Waarom wordt mijn SharePoint-koppeling niet geëxtraheerd?</b></summary>

**Antwoord:** Zie [Gegevensbronlocaties](./Concepts/knowledge-base.md#data-source-locations) voor meer informatie.

</details>

<details>
<summary><b>De updates die ik gemaakt om mijn kennis bank zijn niet terug te vinden op publiceren. Waarom niet?</b></summary>

**Antwoord:** Elke bewerkingsbewerking, of het nu in een tabelupdate, test of instelling is, moet worden opgeslagen voordat deze kan worden gepubliceerd. Klik na elke bewerking **op Opslaan en trainen.**

</details>

<details>
<summary><b>Ondersteunt de kennisbank rijke data of multimedia?</b></summary>

**Antwoord**:

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Multimedia-automatische extractie voor bestanden en URL's

* URLS - beperkte HTML-tot-Markdown conversiemogelijkheid.
* Bestanden - niet ondersteund

#### <a name="answer-text-in-markdown"></a>Tekst beantwoorden in afwaardering
Zodra QnA-sets zich in de kennisbasis bevinden, u de markeringstekst van een antwoord bewerken om koppelingen naar media op te nemen die beschikbaar zijn via openbare URL's.


</details>

<details>
<summary><b>Ondersteunt QnA Maker niet-Engelse talen?</b></summary>

**Antwoord**: Zie meer details over [ondersteunde talen](./Overview/languages-supported.md).

Als u inhoud uit meerdere talen hebt, moet u voor elke taal een aparte service maken.

</details>

## <a name="manage-service"></a>Services beheren

<details>
<summary><b>Wanneer moet ik mijn app-service opnieuw starten?</b></summary>

**Antwoord:** Vernieuw uw app-service wanneer het waarschuwingspictogram naast de versiewaarde voor de kennisbank in de tabel **Eindpunttoetsen** op de pagina **Gebruikersinstellingen** [staat.](https://www.qnamaker.ai/UserSettings)

</details>

<details>
<summary><b>Ik heb mijn bestaande zoekservice verwijderd. Hoe kan ik dit oplossen?</b></summary>

**Antwoord:** Als u een Azure Cognitive Search-index verwijdert, is de bewerking definitief en kan de index niet worden hersteld.

</details>

<details>
<summary><b>Ik heb `testkb` mijn index verwijderd in mijn zoekservice. Hoe kan ik dit oplossen?</b></summary>

**Antwoord:** Uw oude gegevens kunnen niet worden hersteld. Maak een nieuwe QnA Maker-bron en maak opnieuw uw kennisbank.

</details>

<details>
<summary><b>Wanneer moet ik mijn eindpunttoetsen vernieuwen?</b></summary>

**Antwoord:** Vernieuw uw eindpunttoetsen als u vermoedt dat ze zijn gecompromitteerd.

</details>

<details>
<summary><b>Kan ik dezelfde Azure Cognitive Search-bron gebruiken voor kennisbases met meerdere talen?</b></summary>

**Antwoord:** Om meerdere talen en meerdere kennisbanken te gebruiken, moet de gebruiker voor elke taal een QnA Maker-bron maken. Hierdoor wordt per taal een afzonderlijke Azure-zoekservice gemaakt. Het mengen van verschillende taalkennisbases in één Azure-zoekservice leidt tot een verminderde relevantie van resultaten.

</details>

<details>
<summary><b>Hoe kan ik de naam wijzigen van de Azure Cognitive Search-bron die wordt gebruikt door QnA Maker?</b></summary>

**Antwoord:** De naam van de Azure Cognitive Search-bron is de bronnaam QnA Maker met een aantal willekeurige letters die aan het einde zijn toegevoegd. Dit maakt het moeilijk om onderscheid te maken tussen meerdere zoekbronnen voor QnA Maker. Maak een aparte zoekservice (noem deze zoals u dat wilt) en verbind deze met uw QnA-service. De stappen zijn vergelijkbaar met de stappen die u moet uitvoeren om [een Azure-zoekopdracht](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)te upgraden.

</details>

<details>
<summary><b>Wanneer QnA `Runtime core is not initialized,` Maker terugkeert, hoe los ik het op?</b></summary>

**Antwoord:** De schijfruimte voor uw app-service is mogelijk vol. Stappen om uw schijfruimte op te lossen:

1. Selecteer in de [Azure-portal](https://portal.azure.com)de app-service van Uw QnA Maker en stop de service.
1. Terwijl u nog steeds op de App-service zit, selecteert u **Ontwikkelhulpprogramma's,** vervolgens **Geavanceerde hulpprogramma's**en **vervolgens Gaan**. Hiermee wordt een nieuw browservenster geopend.
1. Selecteer **Foutopsporingsconsole**en vervolgens **CMD** om een opdrachtregelgereedschap te openen.
1. Navigeer naar de _site/wwwroot/Data/QnAMaker/directory._
1. Verwijder alle mappen waarvan de `rd`naam begint met .

    Verwijder het volgende **niet:**

    * KbIdToRankerMappings.txt-bestand
    * Bestand EndpointSettings.json
    * Map Eindpuntsleutels

1. Start de app-service.
1. Krijg toegang tot je kennisbank om te controleren of het nu werkt.

</details>

## <a name="integrate-with-other-services-including-bots"></a>Integreren met andere services, waaronder Bots

<details>
<summary><b>Moet ik Bot Framework gebruiken om QnA Maker te gebruiken?</b></summary>

**Antwoord**: Nee, u hoeft het [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) niet te gebruiken met QnA Maker. QnA Maker wordt echter aangeboden als een van de verschillende sjablonen in [Azure Bot Service.](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) Bot Service maakt snelle intelligente botontwikkeling mogelijk via Microsoft Bot Framework en wordt uitgevoerd in een serverloze omgeving.

</details>

<details>
<summary><b>Hoe kan ik een nieuwe bot maken met QnA Maker?</b></summary>

**Antwoord:** Volg de instructies in [deze](./Quickstarts/create-publish-knowledge-base.md) documentatie om uw Bot te maken met Azure Bot Service.

</details>

<details>
<summary><b>Hoe gebruik ik een andere kennisbank met een bestaande Azure-botservice?</b></summary>

**Antwoord**: Je moet de volgende informatie over je kennisbank hebben:

* Kennisbank-ID.
* De gepubliceerde subdomeinnaamnaam van Knowledge Base, `host`die na publicatie op de pagina **Instellingen** wordt gevonden.
* De gepubliceerde eindpuntsleutel van Knowledge Base - gevonden op de pagina **Instellingen** nadat u hebt gepubliceerd.

Ga met deze informatie naar de app-service van uw bot in de Azure-portal. Wijzig deze waarden onder **Instellingen ->->->-toepassingsinstellingen.**

De eindpuntsleutel van de kennisbank `QnAAuthkey` is gelabeld in de ABS-service.

</details>

<details>
<summary><b>Kunnen twee of meer klanttoepassingen een kennisbank delen?</b></summary>

**Antwoord**: Ja, de kennisbank kan worden opgevraagd bij een willekeurig aantal klanten. Als het antwoord van de kennisbank traag of een time-out lijkt te zijn, u overwegen de servicelaag voor de app-service die is gekoppeld aan de kennisbank te upgraden.

</details>

<details>
<summary><b>Hoe sluit ik de QnA Maker-service in op mijn website?</b></summary>

**Antwoord:** Volg deze stappen om de QnA Maker-service in te sluiten als een webchatbesturingselement op uw website:

1. Maak uw FAQ-bot door de instructies [hier te volgen.](./Quickstarts/create-publish-knowledge-base.md)
2. De webchat inschakelen door de [stappen hier te volgen](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)

</details>

## <a name="data-storage"></a>Gegevensopslag

<details>
<summary><b>Welke gegevens worden opgeslagen en waar worden ze opgeslagen?</b></summary>

**Antwoord**:

Wanneer u uw QnA Maker-service maakt, hebt u een Azure-regio geselecteerd. Uw kennisbanken en logbestanden worden opgeslagen in deze regio.

</details>