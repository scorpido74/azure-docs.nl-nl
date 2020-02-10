---
title: 'Snelstartgids: Knowledge Base testen met batch vragen'
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
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2020
ms.locfileid: "77108994"
---
# <a name="quickstart-test-knowledge-base-with-batch-questions-and-expected-answers"></a>Snelstartgids: Knowledge Base testen met batch vragen en verwachte antwoorden

Gebruik het hulp programma voor het testen van de QnA Maker om de kennis bases in uw QnA Maker resource te testen op de verwachte antwoorden, betrouwbaarheids scores en vragen over meerdere schakelingen.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Maak een QnA Maker-service](create-publish-knowledge-base.md) of gebruik een bestaande service, die gebruikmaakt van de Engelse taal.
* Down load het [multi-`.docx`-voorbeeld bestand](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)
* Down load het [hulp programma voor batch tests](https://aka.ms/qnamakerbatchtestingtool), pak het uitvoer bare bestand uit het `.zip`-bestand uit.

## <a name="sign-into-qna-maker-portal"></a>Aanmelden bij QnA Maker Portal

[Meld](https://www.qnamaker.ai/) u aan bij de QnA Maker Portal.

## <a name="create-a-new-knowledge-base-from-the-multi-turn-sampledocx-file"></a>Een nieuwe Knowledge Base maken op basis van het bestand Sample. docx voor meerdere zetten

1. Selecteer **een Knowledge Base maken** in de werk balk.
1. Sla **stap 1** over, omdat u al een QnA Maker resource hebt, waarbij u verdergaat met **stap 2** om uw bestaande resource gegevens te selecteren:
    * Azure Active Directory-ID
    * Naam van het Azure-abonnement
    * Naam van de Azure QnA-service
    * Taal: de Engelse taal
1. Voer de naam `Multi-turn batch test quickstart` in als de naam van uw Knowledge Base.

1. Configureer in **stap 4**de instellingen met de volgende tabel:

    |Instelling|Waarde|
    |--|--|
    |**Schakel het ophalen van meerdere schakelingen uit vanuit Url's, PDF-of DOCX-bestanden.**|Geselecteerd|
    |**Standaard antwoord tekst**| `Batch test - default answer not found.`|
    |**+ Bestand toevoegen**|Selecteer de gedownloade `.docx` bestands vermelding in de vereisten.|
    |**Chit-Chat**|**Professionele** selecteren|

1. Selecteer in **stap 5** **de optie uw KB maken**.

    Wanneer het maken van het proces is voltooid, wordt in de Portal de Bewerk bare Knowledge Base weer gegeven.

## <a name="save-train-and-publish-knowledge-base"></a>Kennis basis opslaan, trainen en publiceren

1. Selecteer **opslaan en trainen** in de werk balk om de Knowledge Base op te slaan.
1. Selecteer **publiceren** op de werk balk en selecteer vervolgens **publiceren** opnieuw om de Knowledge Base te publiceren. Met publiceren wordt de Knowledge Base beschikbaar voor query's van een open bare URL-eind punt. Wanneer het publiceren is voltooid, slaat u de host-URL en de informatie over de eindpunt sleutel op die op de pagina **publiceren** worden weer gegeven.

    |Vereiste gegevens| Voorbeeld|
    |--|--|
    |Gepubliceerde host|`https://YOUR-RESOURCE-NAME.azurewebsites.net`|
    |Gepubliceerde sleutel|`XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX` teken reeks van 32 weer gegeven na `Endpoint`)|
    |App-ID|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` teken reeks (36) die als onderdeel van `POST`wordt weer gegeven |

## <a name="create-batch-test-file-with-question-ids"></a>Batch test bestand maken met vraag-Id's

Als u het hulp programma voor batch testen wilt gebruiken, maakt u een bestand met de naam `batch-test-data-1.tsv` met een tekst editor. Het bestand moet de volgende kolommen bevatten, gescheiden door een tabblad.

|Velden van het TSV-invoer bestand|Opmerkingen|Voorbeeld|
|--|--|--|
|Knowledge Base-ID|Uw Knowledge Base-ID vindt u op de pagina publiceren. Test verschillende kennis grondslagen in dezelfde service in één keer in één bestand met behulp van verschillende Knowledge Base-Id's in één bestand.|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` teken reeks (36) die als onderdeel van `POST`wordt weer gegeven |
|Vraag|De vraag tekst die een gebruiker invoert. Maxi maal 1.000 tekens.|`How do I sign out?`|
|Tags met metagegevens|Beschrijving|`topic:power` maakt gebruik van de indeling _Key: waarde_|
|Bovenste para meter|Beschrijving|`25`|
|Verwachte antwoord-ID|Beschrijving|`13`|

Voor deze Knowledge Base voegt u drie rijen van alleen de 2 vereiste kolommen toe aan het bestand. De eerste kolom is uw Knowledge Base-ID en de tweede kolom moet de volgende lijst met vragen zijn:

|Kolom 2: vragen|
|--|
|`Use Windows Hello to sign in`|
|`Charge your Surface Pro 4`|
|`Get to know Windows 10`|

Deze vragen zijn de exacte tekst uit de Knowledge Base en retour neren 100 als betrouw bare Score.

Voeg vervolgens enkele vragen toe die vergelijkbaar zijn met deze vragen, maar niet precies hetzelfde op drie rijen met dezelfde Knowledge Base-ID:

|Kolom 2: vragen|
|--|
|`What is Windows Hello?`|
|`How do I charge the laptop?`|
|`What features are in Windows 10?`|

> [!CAUTION]
> Zorg ervoor dat elke kolom wordt gescheiden door een tab-scheidings teken. Voor loop-of volg spaties worden toegevoegd aan de kolom gegevens en zorgt ervoor dat het programma uitzonde ringen genereert wanneer het type of de grootte onjuist is.

Het batch test bestand, wanneer dit wordt geopend in Excel, ziet eruit als in de volgende afbeelding. De Knowledge Base-ID is vervangen door `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` voor beveiliging. Zorg ervoor dat voor uw eigen batch-test de kolom uw Knowledge Base-ID bevat.

> [!div class="mx-imgBorder"]
> ![invoer van de eerste versie van het. TSV-bestand van de batch test](../media/batch-test/batch-test-1-input.png)

## <a name="test-the-batch-file"></a>Het batch-bestand testen

Voer het programma voor batch tests uit met de volgende CLI-indeling op de opdracht regel.

Vervang `YOUR-RESOURCE-NAME` en `ENDPOINT-KEY` door uw eigen waarden voor de service naam en de sleutel van het eind punt. Deze waarden zijn te vinden op de pagina **instellingen** in de QnA Maker Portal.

```console
batchtesting.exe batch-test-data-1.tsv https://YOUR-RESOURCE-NAME.azurewebsites.net ENDPOINT-KEY out.tsv
```
De test is voltooid en het `out.tsv` bestand wordt gegenereerd:

> [!div class="mx-imgBorder"]
> ![uitvoer van de eerste versie van het. TSV-bestand van de batch test](../media/batch-test/batch-test-1-output.png)

De Knowledge Base-ID is vervangen door `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` voor beveiliging. Voor uw eigen batch test wordt in de kolom de Knowledge Base-ID weer gegeven.

De test uitvoer van een betrouwbaarheids Score, in de vierde kolom, toont de eerste 3 vragen die een Score van 100 zoals verwacht, omdat elke vraag precies hetzelfde is als in de Knowledge Base. De laatste 3 vragen, met een nieuwe woord vorm van de vraag, retour neren 100 niet als de betrouwbaarheids Score. Als u de score voor de test en uw gebruikers wilt verhogen, moet u meer alternatieve vragen toevoegen aan de Knowledge Base.

## <a name="testing-with-the-optional-fields"></a>Testen met de optionele velden

Wanneer u de indeling en het proces begrijpt, kunt u een test bestand genereren om uit te voeren op basis van uw Knowledge Base uit een gegevens bron, zoals uit chat Logboeken.

Omdat de gegevens bron en het proces geautomatiseerd zijn, kan het test bestand vaak met verschillende instellingen worden uitgevoerd om de juiste waarden te bepalen.

Als u bijvoorbeeld een chat logboek hebt en u wilt bepalen welke chat tekst van toepassing is op welke meta gegevens velden, moet u een test bestand maken en de meta gegevens velden voor elke rij instellen. Voer de test uit en controleer de rijen die overeenkomen met de meta gegevens. Over het algemeen moeten de overeenkomsten positief zijn, maar u moet de resultaten voor valse positieven bekijken. Een fout-positief is een rij die overeenkomt met de meta gegevens, maar op basis van de tekst. de waarde mag niet overeenkomen.

## <a name="using-optional-fields-in-the-input-batch-test-file"></a>Optionele velden in het test bestand van de invoer batch gebruiken

Gebruik de volgende tabel om te begrijpen hoe u de veld waarden voor optionele gegevens kunt vinden.

|Kolom nummer|Optionele kolom|Gegevenslocatie|
|--|--|--|
|3|metagegevens|Bestaande Knowledge Base exporteren voor bestaande _sleutel:_ waardeparen.|
|4|Boven|De standaard waarde van `25` wordt aanbevolen.|
|5|ID van de vraag en de set met antwoorden|Bestaande Knowledge Base exporteren voor ID-waarden. U ziet ook dat de Id's zijn geretourneerd in het uitvoer bestand.|

## <a name="add-metadata-to-the-knowledge-base"></a>Meta gegevens toevoegen aan de Knowledge Base

1. Voeg in de QnA-Portal op de pagina **bewerken** meta gegevens van `topic:power` toe aan de volgende vragen:

    |Beantwoorden|
    |--|
    |Uw Surface Pro 4 in rekening brengen|
    |Het accu niveau controleren|

    Voor twee QnA sets zijn de meta gegevens ingesteld.

    > [!TIP]
    > Als u de meta gegevens en QnA-Id's van elke set wilt zien, exporteert u de Knowledge Base. Selecteer de pagina **instellingen** en selecteer vervolgens **exporteren** als een `.xls` bestand. Zoek dit gedownloade bestand en open het met Excel controleren op meta gegevens en de ID.

1. Selecteer **opslaan en trainen**, selecteer de pagina **publiceren** en selecteer vervolgens de knop **publiceren** . Deze acties maken de wijziging beschikbaar voor de batch-test. Down load de Knowledge Base via de pagina **instellingen** .

    Het gedownloade bestand heeft de juiste indeling voor de meta gegevens en de juiste set-ID voor de vraag en het antwoord. Deze velden gebruiken in de volgende sectie

    > [!div class="mx-imgBorder"]
    > ![geëxporteerde kennis basis met meta gegevens](../media/batch-test/exported-knowledge-base-with-metadata.png)

## <a name="create-a-second-batch-test"></a>Een tweede batch test maken

Er zijn twee belang rijke scenario's voor batch tests:
* **Chat-logboek bestanden verwerken** : het meest voorkomende antwoord op een eerder getwijfelde vraag bepalen: de meestvoorkomende situatie is dat u het logboek bestand van query's moet verwerken, zoals de gebruikers vragen van een chat-bot. Een test voor een batch-bestand maken met alleen de vereiste kolommen. De test retourneert het eerste antwoord voor elke vraag. Dat betekent niet dat het beste antwoord het juiste antwoord is. Wanneer u deze test hebt voltooid, gaat u verder met de validatie test.
* **Validatie test** : Valideer het verwachte antwoord. Deze test vereist dat alle vragen en overeenkomende verwachte antwoorden in de batch test zijn gevalideerd. Dit kan hand matig proces vereisen.

In de volgende procedure wordt ervan uitgegaan dat het scenario is voor het verwerken van chat-logboeken met

1. Maak een nieuw batch-test bestand voor het toevoegen van optionele gegevens `batch-test-data-2.tsv`. Voeg de 6 rijen van het oorspronkelijke invoer bestand voor batch tests toe en voeg vervolgens de set-ID voor meta gegevens, top en QnA toe voor elke rij.

    Als u het geautomatiseerde proces voor het controleren van nieuwe tekst in chat logboeken wilt simuleren in de Knowledge Base, stelt u de meta gegevens voor elke kolom in op dezelfde waarde: `topic:power`.

    > [!div class="mx-imgBorder"]
    > ![invoer van de tweede versie van het. TSV-bestand vanuit de batch test](../media/batch-test/batch-test-2-input.png)

1. Voer de test opnieuw uit en wijzig de namen van de invoer-en uitvoer bestanden om aan te geven dat dit de tweede test is.

    > [!div class="mx-imgBorder"]
    > ![uitvoer tweede versie van. TSV-bestand van batch test](../media/batch-test/batch-test-2-output.png)

## <a name="test-results-and-an-automated-test-system"></a>Test resultaten en een geautomatiseerd test systeem

Dit test uitvoer bestand kan worden geparseerd als onderdeel van een geautomatiseerde continue test pijplijn.

Deze specifieke test uitvoer moet worden gelezen als: elke rij is gefilterd met meta gegevens, en omdat elke rij niet overeenkomt met de meta gegevens in de Knowledge Base, wordt het standaard antwoord voor die niet-overeenkomende rijen geretourneerd ("geen goede overeenkomst gevonden in KB"). Van de rijen die overeenkomen, zijn de QnA-ID en Score geretourneerd.

Alle rijen hebben het label onjuist geretourneerd, omdat er geen rijen worden gevonden die overeenkomen met de verwachte antwoord-ID.

U kunt met deze resultaten zien dat u een chat-logboek wilt maken en de tekst als de query van elke rij wilt gebruiken. Zonder dat u iets hoeft te weten over de gegevens, geven de resultaten u veel informatie over de gegevens die u vervolgens kunt gebruiken om vooruit te gaan:

* meta gegevens
* QnA-ID
* score

Is het filteren met meta gegevens een goed idee voor de test? Ja en Nee. Het test systeem moet test bestanden maken voor elk meta gegevens paar en een test zonder meta gegevens paren.

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet wilt door gaan met het testen van de Knowledge Base, verwijdert u het hulp programma voor batch bestanden en de test bestanden.

Als u deze Knowledge Base niet wilt blijven gebruiken, verwijdert u de Knowledge Base met de volgende stappen:

1. Selecteer in de QnA Maker Portal **mijn Knowledge bases** in het bovenste menu.
1. Selecteer in de lijst met kennis grondslagen het pictogram **verwijderen** in de rij van de Knowledge Base van deze Quick Start.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naslaginformatie over REST API voor QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
