---
title: 'Quickstart: Knowledge Base testen met batchvragen'
description: Gebruik het batchtestprogramma QnA Maker om de Knowledge Base in uw QnA Maker-resource te testen op verwachte antwoorden, waarschijnlijkheidsscores en vragen met meerdere paden.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 05/26/2020
ms.openlocfilehash: ac406761abf922ff5f0d86eacd3e419c374ce096
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776762"
---
# <a name="quickstart-test-knowledge-base-with-batch-questions-and-expected-answers"></a>Quickstart: Knowledge Base testen met batchvragen en verwachte antwoorden

Gebruik het batchtestprogramma QnA Maker om de Knowledge Base in uw QnA Maker-resource te testen op verwachte antwoorden, waarschijnlijkheidsscores en vragen met meerdere paden.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/cognitive-services/)
* [Maak een QnA Maker-service](create-publish-knowledge-base.md) of gebruik een bestaande service die gebruikmaakt van de Engelse taal.
* Het `.docx`voorbeeldbestand [meerdere paden downloaden ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)
* Download het [batchtestprogramma](https://aka.ms/qnamakerbatchtestingtool), pak het uitvoerbaar bestand uit vanuit het `.zip`-bestand.

## <a name="sign-into-qna-maker-portal"></a>Aanmelden bij QnA Maker-portal

[Meld u aan](https://www.qnamaker.ai/) bij de QnA Maker-portal.

## <a name="create-a-new-knowledge-base-from-the-multi-turn-sampledocx-file"></a>Een Knowledge Base maken op basis van het .docx-voorbeeldbestand meerdere paden

1. Selecteer **Een Knowledge Base maken** in de menubalk.
1. Sla **Stap 1** over, want u moet al een QnA Maker-resource hebben, ga naar **Stap 2** om uw bestaande resourcegegevens te selecteren:
    * Azure Active Directory-ID
    * Azure-abonnementsnaam
    * Azure QnA Service-naam
    * Taal, de Engelse taal
1. Voer de naam `Multi-turn batch test quickstart` in als de naam van uw Knowledge Base.

1. Configureer in **Stap 4** de instellingen met de volgende tabel:

    |Instelling|Waarde|
    |--|--|
    |**Schakel uitpakken van meerdere paden in vanuit URL's, .pdf- of .docx-bestanden.**|Ingeschakeld|
    |**Standaardantwoordtekst**| `Batch test - default answer not found.`|
    |**+ Bestand toevoegen**|Selecteer de gedownloade `.docx`-bestandslijst in de vereisten.|
    |**Chit-chat**|**Professional** selecteren|

1. Selecteer in **stap 5**, **Uw KB maken**.

    Wanneer het proces is voltooid, geeft de portal de bewerkbare Knowledge Base weer.

## <a name="save-train-and-publish-knowledge-base"></a>De knowledge base opslaan, trainen en publiceren

1. Selecteer **Opslaan en trainen** in de werkbalk om de Knowledge Base op te slaan.
1. Selecteer **Publiceren** in de werkbalk en selecteer dan nogmaals **Publiceren** om de Knowledge Base te publiceren. Door de Knowledge Base te publiceren is deze beschikbaar voor query's vanuit een openbaar URL-eindpunt. Wanneer het publiceren is voltooid, slaat u de host-URL en eindpuntsleutelgegevens op die worden weergegeven op de pagina **Publiceren**.

    |Vereiste gegevens| Voorbeeld|
    |--|--|
    |Gepubliceerde host|`https://YOUR-RESOURCE-NAME.azurewebsites.net`|
    |Gepubliceerde sleutel|`XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX` (tekenreeks van 32 tekens weergegeven na `Endpoint`)|
    |App-id|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (tekenreeks van 36 tekens weergegeven als onderdeel van `POST`) |

## <a name="create-batch-test-file-with-question-ids"></a>Batchtestbestand maken met vraag-ID's

Maak een bestand met de naam `batch-test-data-1.tsv` met een teksteditor om het batchtestprogramma te gebruiken. Het bestand moet een UTF-8-indeling hebben en de volgende kolommen, gescheiden door een tab.

|TSV-invoerbestandsvelden|Opmerkingen|Voorbeeld|
|--|--|--|
|Knowledge Base-ID|Uw Knowledge Base-ID dat u kunt vinden op de pagina Publiceren. Test tegelijkertijd verschillende Knowledge Bases in dezelfde service in een enkel bestand door verschillende Knowledge Base-ID's te gebruiken in een enkel bestand.|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (tekenreeks van 36 tekens weergegeven als onderdeel van `POST`) |
|Vraag|De vraag die een gebruiker zou invoeren. Maximaal 1000 tekens.|`How do I sign out?`|
|Tags met metagegevens|optioneel|`topic:power` gebruikt de indeling _key:value_|
|Top-parameter|optioneel|`25`|
|ID van verwacht antwoord|optioneel|`13`|

Voeg voor deze Knowledge Base drie rijen toe aan het bestand met alleen de twee vereiste kolommen. De eerste kolom is het Knowledge Base-ID en de tweede kolom moet de volgende lijst vragen zijn:

|Kolom 2, vragen|
|--|
|`Use Windows Hello to sign in`|
|`Charge your Surface Pro 4`|
|`Get to know Windows 10`|

Deze vragen zijn de exacte tekst uit de Knowledge Base en moeten als waarde 100 retourneren voor waarschijnlijkheid.

Voeg vervolgens een aantal vragen toe op drie rijen, soortgelijk als deze vragen maar niet exact gelijk en gebruik dezelfde Knowledge Base-ID:

|Kolom 2, vragen|
|--|
|`What is Windows Hello?`|
|`How do I charge the laptop?`|
|`What features are in Windows 10?`|

> [!CAUTION]
> Zorg ervoor dat elke kolom wordt gescheiden door alleen een tab-scheidingsteken. Voorloop- of volgspaties worden aan de kolomgegevens toegevoegd en zorgen voor uitzonderingen in het programma als het type of de grootte onjuist is.

Als het batchtestbestand in Excel wordt geopend, ziet het eruit als de volgende afbeelding. De Knowledge Base-ID is vervangen door `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` voor de beveiliging. Zorg voor uw eigen batchtest dat de kolom uw Knowledge Base-ID weergeeft.

> [!div class="mx-imgBorder"]
> ![Invoer eerste versie van .tsv-bestand uit batchtest](../media/batch-test/batch-test-1-input.png)

## <a name="test-the-batch-file"></a>Batchbestand testen

Voer op de opdrachtregel het batchtestprogramma uit met de volgende CLI-indeling.

Vervang `YOUR-RESOURCE-NAME` en `ENDPOINT-KEY` met uw eigen waarden voor servicenaam en eindpuntsleutel. Deze waarden vindt u op de pagina **Instellingen** in de QnA Maker-portal.

```console
batchtesting.exe batch-test-data-1.tsv https://YOUR-RESOURCE-NAME.azurewebsites.net ENDPOINT-KEY out.tsv
```
De test wordt voltooid en genereert het `out.tsv`-bestand:

> [!div class="mx-imgBorder"]
> ![Uitvoer eerste versie van .tsv-bestand uit batchtest](../media/batch-test/batch-test-1-output.png)

De Knowledge Base-ID is vervangen door `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` voor de beveiliging. De kolom van uw eigen batchtest geeft uw Knowledge Base-ID weer.

De testuitvoer van de waarschijnlijkheidsscore in de vierde kolom, toont dat de drie eerste vragen, zoals verwacht, een score van 100 retourneerden, omdat elke vraag precies hetzelfde is als in de Knowledge Base. De laatste drie vragen met een nieuwe tekst retourneren geen 100 bij de waarschijnlijkheidsscore. Om de score voor de test en uw gebruikers te verhogen, moet u meer alternatieve vragen toevoegen aan de Knowledge Base.

## <a name="testing-with-the-optional-fields"></a>Testen met optionele velden

Wanneer u de indeling en het proces begrijpt, kunt u een testbestand genereren om met uw Knowledge Base uit te voeren op basis van een gegevensbron zoals chatlogboeken.

Omdat de gegevensbron en het proces zijn geautomatiseerd, kan het testbestand vaak worden uitgevoerd met verschillende instellingen om de juiste waarden te bepalen.

Als u bijvoorbeeld een chatlogboek hebt en u wilt vaststellen welk chattekst van toepassing is op welke metagegevensvelden, maakt u een testbestand en stelt u voor elke regel de metagegevensvelden in. Voer de test uit en beoordeel de rijen die overeenkomen met de metagegevens. Over het algemeen moeten de overeenkomsten positief zijn, maar u moet de resultaten beoordelen op fout-positieve resultaten. Een fout-positief is een rij die overeenkomt met de metagegevens, maar op basis van de tekst niet overeen zou moeten komen.

## <a name="using-optional-fields-in-the-input-batch-test-file"></a>Optionele velden gebruiken in het invoerbestand voor batchtesten

Gebruik het volgende diagram om de veldwaarden te vinden voor de optionele gegevens.

|Kolomnummer|Optionele kolom|Locatie van gegevens|
|--|--|--|
|3|metagegevens|Exporteer bestaande Knowledge Base voor bestaande _key:value_-paren.|
|4|top|De standaardwaarde `25` wordt aanbevolen.|
|5|Vraag-en-antwoord-set-ID|Exporteer bestaande Knowledge Base voor ID-waarden. U ziet ook dat de ID's in het uitvoerbestand zijn geretourneerd.|

## <a name="add-metadata-to-the-knowledge-base"></a>Metagegevens toevoegen aan de Knowledge Base

1. Voeg in de QnA-portal op de pagina **Bewerken** aan de volgende vragen metagegevens toe van `topic:power`:

    |Vragen|
    |--|
    |Uw Surface Pro 4 opladen|
    |Het niveau van de accu controleren|

    Twee QnA-paren hebben de metagegevensset.

    > [!TIP]
    > Om de metagegevens en QnA-ID's van elke set te zien, moet u de Knowledge Base exporteren. Selecteer de pagina **Instellingen** en selecteer vervolgens **Exporteren** als `.xls`-bestand. Zoek dit gedownloade bestand en open het met Excel om te controleren op metagegevens en ID.

1. Selecteer **Opslaan en trainen**, selecteer dan de pagina **Publiceren** en vervolgens de knop **Publiceren**. Door deze acties zijn de wijziging beschikbaar voor de batchtest. Download de Knowledge Base vanuit de pagina **Instellingen**.

    Het gedownloade bestand heeft de juiste indeling voor de metagegevens en de juiste vraag-en-antwoord-set-ID. Gebruik deze velden in het volgende gedeelte

    > [!div class="mx-imgBorder"]
    > ![Geëxporteerde Knowledge Base met metagegevens](../media/batch-test/exported-knowledge-base-with-metadata.png)

## <a name="create-a-second-batch-test"></a>Een tweede batchtest maken

Er zijn twee belangrijke scenario's voor batchtesten:
* **Chatlogboekbestanden verwerken**, bepaal het meestvoorkomende antwoord voor een nog niet bekeken vraag. De meestvoorkomende situatie is wanneer u een logboekbestand van query's moet verwerken, zoals gebruikersvragen van een chatbot. Maak een batchbestandtest met alleen de vereiste kolommen. De test retourneert het meestvoorkomende antwoord voor elke vraag. Dat betekent niet dat het meestvoorkomende antwoord ook het goede antwoord is. Wanneer deze test is voltooid, gaat u verder met de validatietest.
* **Validatietest**, valideer het verwachte antwoord. Voor deze test moeten alle vragen en bijbehorende verwachte antwoorden in de batchtest zijn gevalideerd. Hier is mogelijk wat handmatige verwerking nodig.

In de volgende procedure wordt ervan uitgegaan dat het scenario het verwerken is van chatlogboeken met

1. Maak een nieuw batchtestbestand om de optionele gegevens in op te nemen: `batch-test-data-2.tsv`. Voeg de zes rijen van het oorspronkelijke batchtestinvoerbestand toe, vervolgens voor elke rij de metagegevens, top en het QnA-paar-ID.

    Stel de metagegevens voor elke kolom in op dezelfde waarde: `topic:power`, om het geautomatiseerde proces te simuleren van het vergelijken van nieuwe tekst van chatlogboeken met de Knowledge Base.

    > [!div class="mx-imgBorder"]
    > ![Invoer tweede versie van .tsv-bestand uit batchtest](../media/batch-test/batch-test-2-input.png)

1. Voer de test opnieuw uit, wijzig de namen van het invoer- en uitvoerbestand om aan te geven dat het de tweede test is.

    > [!div class="mx-imgBorder"]
    > ![Uitvoer tweede versie van .tsv-bestand uit batchtest](../media/batch-test/batch-test-2-output.png)

## <a name="test-results-and-an-automated-test-system"></a>Testresultaten en een geautomatiseerd testsysteem

Dit testuitvoerbestand kan worden geparseerd als onderdeel van een geautomatiseerde doorlopende testpijplijn.

Deze specifieke testuitvoer moet als volgt worden gelezen: elke rij is gefilterd met metagegevens en omdat niet elke rij overeenkwam met de metagegevens in de Knowledge Base, is het standaardantwoord geretourneerd voor deze niet-overeenkomende rijen ('geen goede overeenkomst gevonden in KB'). Van de rijen die overeenkwamen, werden de QnA-ID en score geretourneerd.

Alle rijen hebben het label 'onjuist' geretourneerd, omdat geen enkele rij overeenkwam met het verwachte antwoord-ID.

U moet met deze resultaten kunnen zien dat u de tekst van een chatlogboek kunt gebruiken als de query voor elke rij. Zonder iets te weten over de gegevens vertellen de resultaten u veel over de gegevens dat u kunt gebruiken voor het vervolg:

* metagegevens
* QnA-ID
* Score

Was het filteren met metagegevens een goed idee voor de test? Ja en nee. Het testsysteem moet testbestanden maken voor elk metagegevenspaar en ook een test zonder metagegevensparen.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze Knowledge Base verder niet gaat testen, verwijder dan het batchbestandprogramma en de testbestanden.

Als u deze Knowledge Base verder niet gaat gebruiken, verwijder dan de Knowledge Base met de volgende stappen:

1. Selecteer in de QnA Maker-portal **Mijn Knowledge Bases** in het hoofdmenu.
1. Selecteer in de lijst met Knowledge Bases het pictogram **Verwijderen** op de rij van de Knowledge Base van deze quickstart.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naslaginformatie over REST-API voor QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
