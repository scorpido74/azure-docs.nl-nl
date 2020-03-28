---
title: 'Quickstart: Test kennisbank met batchvragen'
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: e16166c741b99c1af5b36f2c7ccd25b01f7544ba
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77108994"
---
# <a name="quickstart-test-knowledge-base-with-batch-questions-and-expected-answers"></a>Quickstart: Test kennisbank met batchvragen en verwachte antwoorden

Gebruik de QnA Maker batchtesttool om de kennisbases in uw QnA Maker-bron te testen op verwachte antwoorden, betrouwbaarheidsscores en multi-turnprompts.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Maak [een QnA Maker-service](create-publish-knowledge-base.md) of gebruik een bestaande service, die de Engelse taal gebruikt.
* Het [ `.docx` voorbeeldbestand met meerdere beurten downloaden](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)
* Download het [batchtestgereedschap,](https://aka.ms/qnamakerbatchtestingtool)haal het `.zip` uitvoerbare bestand uit het bestand.

## <a name="sign-into-qna-maker-portal"></a>Meld u aan bij qnA Maker-portal

[Meld u](https://www.qnamaker.ai/) aan bij de QnA Maker portal.

## <a name="create-a-new-knowledge-base-from-the-multi-turn-sampledocx-file"></a>Maak een nieuwe kennisbank vanuit het multi-turn sample.docx-bestand

1. Selecteer **Een kennisbank maken** op de gereedschapsbalk.
1. Stap **1** overslaan omdat u al een QnA Maker-bron moet hebben, ga naar **stap 2** om uw bestaande brongegevens te selecteren:
    * Azure Active Directory-id
    * Naam Azure-abonnement
    * Azure QnA-servicenaam
    * Taal - de Engelse taal
1. Voer de `Multi-turn batch test quickstart` naam in als de naam van uw kennisbank.

1. Configureer in **stap 4**de instellingen met de volgende tabel:

    |Instelling|Waarde|
    |--|--|
    |**Multi-turn extractie uit URL's, .pdf- of .docx-bestanden inschakelen.**|Geselecteerd|
    |**Standaardantwoordtekst**| `Batch test - default answer not found.`|
    |**+ Bestand toevoegen**|Selecteer de `.docx` gedownloade bestandsvermelding in de vereisten.|
    |**Chit-chat**|Selecteer **Professional**|

1. Selecteer in **stap 5**De optie Uw **KB maken.**

    Wanneer het creatieproces is voltooid, geeft de portal de bewerkbare kennisbank weer.

## <a name="save-train-and-publish-knowledge-base"></a>Kennisbank opslaan, trainen en publiceren

1. Selecteer **Opslaan en trainen** op de werkbalk om de kennisbasis op te slaan.
1. Selecteer **Publiceren op** de werkbalk en selecteer Opnieuw **publiceren** om de kennisbank te publiceren. Publishing maakt de kennisbank beschikbaar voor query's vanaf een openbaar URL-eindpunt. Wanneer publicatie is voltooid, slaat u de URL en eindpuntsleutelgegevens op die op de **pagina Publiceren worden** weergegeven.

    |Vereiste gegevens| Voorbeeld|
    |--|--|
    |Gepubliceerde host|`https://YOUR-RESOURCE-NAME.azurewebsites.net`|
    |Gepubliceerde sleutel|`XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX`(32 tekentekenreeks `Endpoint` weergegeven na )|
    |App-id|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`(36 tekentekenreeks weergegeven `POST`als onderdeel van ) |

## <a name="create-batch-test-file-with-question-ids"></a>Batchtestbestand maken met vraag--geïdentificeerde gegevens

Als u het batchtestgereedschap wilt gebruiken, maakt u een bestand met de naam `batch-test-data-1.tsv` met een teksteditor. Het bestand moet de volgende kolommen gescheiden hebben door een tabblad.

|TSV-invoerbestandsvelden|Opmerkingen|Voorbeeld|
|--|--|--|
|Knowledge base ID|Uw kennisbank-ID op de publicatiepagina. Test verschillende kennisbases in dezelfde service in één keer in één bestand met behulp van verschillende knowledge base-id's in één bestand.|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`(36 tekentekenreeks weergegeven `POST`als onderdeel van ) |
|Vraag|De vraagtekst die een gebruiker zou invoeren. Maximaal 1000 tekens.|`How do I sign out?`|
|Tags met metagegevens|optioneel|`topic:power`gebruikt de _sleutel:waardenotatie_|
|Bovenste parameter|optioneel|`25`|
|Verwachte antwoord-ID|optioneel|`13`|

Voeg voor deze kennisbank 3 rijen van slechts de 2 vereiste kolommen toe aan het bestand. De eerste kolom is uw knowledge base ID en de tweede kolom moet de volgende lijst met vragen zijn:

|Kolom 2 - vragen|
|--|
|`Use Windows Hello to sign in`|
|`Charge your Surface Pro 4`|
|`Get to know Windows 10`|

Deze vragen zijn de exacte formulering van de kennisbank en moeten 100 als de betrouwbaarheidsscore retourneren.

Voeg vervolgens een paar vragen toe, vergelijkbaar met deze vragen, maar niet precies hetzelfde op 3 rijen, met dezelfde knowledge base ID:

|Kolom 2 - vragen|
|--|
|`What is Windows Hello?`|
|`How do I charge the laptop?`|
|`What features are in Windows 10?`|

> [!CAUTION]
> Zorg ervoor dat elke kolom alleen wordt gescheiden door een tabscheidingsteken. Voor- of achterliggende spaties worden toegevoegd aan de kolomgegevens en zorgen ervoor dat het programma uitzonderingen maakt wanneer het type of de grootte onjuist is.

Het batchtestbestand, dat in Excel wordt geopend, lijkt op de volgende afbeelding. De knowledge base ID `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` is vervangen door voor de veiligheid. Zorg ervoor dat de kolom uw knowledge base-id weergeeft voor uw eigen batchtest.

> [!div class="mx-imgBorder"]
> ![Eerste versie van .tsv-bestand invoeren vanuit batchtest](../media/batch-test/batch-test-1-input.png)

## <a name="test-the-batch-file"></a>Het batchbestand testen

Voer het batchtestprogramma uit met de volgende CLI-indeling op de opdrachtregel.

Vervang `YOUR-RESOURCE-NAME` `ENDPOINT-KEY` en met uw eigen waarden voor servicenaam en eindpuntsleutel. Deze waarden zijn te vinden op de pagina **Instellingen** in de QnA Maker-portal.

```console
batchtesting.exe batch-test-data-1.tsv https://YOUR-RESOURCE-NAME.azurewebsites.net ENDPOINT-KEY out.tsv
```
De test voltooit `out.tsv` en genereert het bestand:

> [!div class="mx-imgBorder"]
> ![Eerste versie van .tsv-bestand uit batchtest uitvoeren](../media/batch-test/batch-test-1-output.png)

De knowledge base ID `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` is vervangen door voor de veiligheid. Voor uw eigen batchtest geeft de kolom uw knowledge base-ID weer.

De test output van vertrouwen score, in de 4e kolom, toont de top 3 vragen terug een score van 100 zoals verwacht, omdat elke vraag is precies hetzelfde als het verschijnt in de kennisbank. De laatste 3 vragen, met nieuwe formulering van de vraag, niet terug te keren 100 als het vertrouwen score. Om de score zowel voor de test als voor uw gebruikers te verhogen, moet u meer alternatieve vragen toevoegen aan de kennisbank.

## <a name="testing-with-the-optional-fields"></a>Testen met de optionele velden

Zodra u de indeling en het proces begrijpt, u een testbestand genereren, om tegen uw kennisbestand uit te voeren vanuit een bron van gegevens, zoals uit chatslogboeken.

Omdat de gegevensbron en het proces geautomatiseerd zijn, kan het testbestand vele malen worden uitgevoerd met verschillende instellingen om de juiste waarden te bepalen.

Als u bijvoorbeeld een chatlogboek hebt en u wilt bepalen welke chatlogboektekst van toepassing is op welke metagegevensvelden, maakt u een testbestand en stelt u de metagegevensvelden in voor elke rij. Voer de test uit en bekijk vervolgens de rijen die overeenkomen met de metagegevens. Over het algemeen moeten de wedstrijden positief zijn, maar u moet de resultaten bekijken op false positives. Een false positive is een rij die overeenkomt met de metagegevens, maar op basis van de tekst moet deze niet overeenkomen.

## <a name="using-optional-fields-in-the-input-batch-test-file"></a>Optionele velden gebruiken in het testbestand voor invoerbatch

Gebruik de volgende grafiek om te begrijpen hoe u de veldwaarden voor optionele gegevens vinden.

|Kolomnummer|Optionele kolom|Gegevenslocatie|
|--|--|--|
|3|metagegevens|Bestaande kennisbasis exporteren voor bestaande _sleutelparen:waardeparen._|
|4|top|Standaardwaarde `25` van wordt aanbevolen.|
|5|Id van de stelvraag en antwoord|Bestaande kennisbank exporteren voor ID-waarden. Merk ook op dat de id's zijn geretourneerd in het uitvoerbestand.|

## <a name="add-metadata-to-the-knowledge-base"></a>Metagegevens toevoegen aan de kennisbank

1. Voeg in de QnA-portal op de `topic:power` pagina **Bewerken** metagegevens toe aan de volgende vragen:

    |Vragen|
    |--|
    |Laad je Surface Pro 4 op|
    |Controleer het batterijniveau|

    Twee QnA-sets hebben de metadataset.

    > [!TIP]
    > Als u de metagegevens en QnA-id's van elke set wilt bekijken, exporteert u de kennisbank. Selecteer de pagina **Instellingen** en `.xls` selecteer **Exporteren** als bestand. Zoek dit gedownloade bestand en open met Excel-controle op metagegevens en id.Find this downloaded file and open with Excel reviewing for metadata and ID.

1. Selecteer **Opslaan en trainen**en selecteer vervolgens de pagina **Publiceren** en selecteer vervolgens de knop **Publiceren.** Met deze acties is de wijziging beschikbaar voor de batchtest. Download de kennisbank van de pagina **Instellingen.**

    Het gedownloade bestand heeft de juiste indeling voor de metagegevens en de juiste vraag- en antwoordset-id. Deze velden gebruiken in de volgende sectie

    > [!div class="mx-imgBorder"]
    > ![Geëxporteerde kennisbank met metadata](../media/batch-test/exported-knowledge-base-with-metadata.png)

## <a name="create-a-second-batch-test"></a>Een tweede batchtest maken

Er zijn twee belangrijke scenario's voor batchtesten:
* **Chatlogbestanden verwerken** - Bepaal het bovenste antwoord op een nog niet eerder vertoonde vraag - de meest voorkomende situatie is wanneer u moet verwerken zijn logboekbestand van query's, zoals de gebruikersvragen van een chatbot. Maak een batchbestandstest, met alleen de vereiste kolommen. De test geeft het hoogste antwoord voor elke vraag. Dat betekent niet dat het belangrijkste antwoord is het juiste antwoord. Zodra u deze test hebt voltooid, gaat u verder met de validatietest.
* **Validatietest** - Valideer het verwachte antwoord. Deze test vereist dat alle vragen en overeenkomende verwachte antwoorden in de batchtest zijn gevalideerd. Dit kan een handmatig proces vereisen.

De volgende procedure gaat ervan uit dat het scenario is om chatlogs te verwerken met

1. Maak een nieuw batchtestbestand met `batch-test-data-2.tsv`optionele gegevens. Voeg de 6 rijen uit het oorspronkelijke batchtestinvoerbestand toe en voeg vervolgens de metagegevens, de bovenste en QnA-set-id voor elke rij toe.

    Als u het geautomatiseerde proces wilt simuleren om nieuwe tekst uit chatlogboeken te `topic:power`controleren op de kennisbank, stelt u de metagegevens voor elke kolom in op dezelfde waarde: .

    > [!div class="mx-imgBorder"]
    > ![Tweede versie van .tsv-bestand invoeren vanuit batchtest](../media/batch-test/batch-test-2-input.png)

1. Voer de test opnieuw uit en wijzig de namen van invoer- en uitvoerbestanden om aan te geven dat het de tweede test is.

    > [!div class="mx-imgBorder"]
    > ![Tweede versie van .tsv-bestand uit batchtest uitvoeren](../media/batch-test/batch-test-2-output.png)

## <a name="test-results-and-an-automated-test-system"></a>Testresultaten en een geautomatiseerd testsysteem

Dit testuitvoerbestand kan worden ontleed als onderdeel van een geautomatiseerde continue testpijplijn.

Deze specifieke testuitvoer moet worden gelezen als: elke rij werd gefilterd met metagegevens en omdat elke rij niet overeenkwam met de metagegevens in de kennisbank, is het standaardantwoord voor die niet-overeenkomende rijen geretourneerd ("geen goede overeenkomst gevonden in kb"). Van de rijen die wel overeenkomen, werden de QnA ID en score geretourneerd.

Alle rijen hebben het label van onjuist geretourneerd omdat er geen rij overeenkwam met de verwachte antwoord-id.

U moet met deze resultaten kunnen zien dat u een chatlogboek maken en de tekst gebruiken als query van elke rij. Zonder iets te weten over de gegevens, de resultaten vertellen u veel over de gegevens die u vervolgens gebruiken vooruit:

* meta-gegevens
* QnA-id
* Score

Was filteren met meta-data een goed idee voor de test? Ja en nee. Het testsysteem moet testbestanden maken voor elk meta-datapaar en een test zonder meta-dataparen.

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet doorgaat met het testen van de kennisbank, verwijdert u het batchbestandhulpprogramma en de testbestanden.

Als u deze kennisbank niet meer wilt gebruiken, verwijdert u de kennisbank met de volgende stappen:

1. Selecteer in de QnA Maker-portal **Mijn kennisbases** in het bovenste menu.
1. Selecteer in de lijst met kennisbanken het pictogram **Verwijderen** op de rij van de kennisbank van deze quickstart.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naslaginformatie over REST-API voor QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
