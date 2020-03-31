---
title: Betrouwbare gebeurtenisverwerking van Azure Functions
description: Ontbrekende Event Hub-berichten in Azure-functies voorkomen
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: cshoe
ms.openlocfilehash: e4f35495d8a01146068cffb9159c29c46c3c0d29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75561864"
---
# <a name="azure-functions-reliable-event-processing"></a>Betrouwbare gebeurtenisverwerking van Azure Functions

Gebeurtenisverwerking is een van de meest voorkomende scenario's die zijn gekoppeld aan serverloze architectuur. In dit artikel wordt beschreven hoe u een betrouwbare berichtenprocessor met Azure-functies maakt om te voorkomen dat u berichten verliest.

## <a name="challenges-of-event-streams-in-distributed-systems"></a>Uitdagingen van gebeurtenisstromen in gedistribueerde systemen

Overweeg een systeem dat gebeurtenissen verzendt met een constante snelheid van 100 gebeurtenissen per seconde. Met deze snelheid kunnen meerdere parallelle functies-exemplaren de binnenkomende 100 gebeurtenissen per seconde verbruiken.

Echter, een van de volgende minder optimale omstandigheden zijn mogelijk:

- Wat gebeurt er als de uitgever van de gebeurtenis een corrupte gebeurtenis verzendt?
- Wat gebeurt er als de instantie Functions onverwerkte uitzonderingen tegenkomt?
- Wat gebeurt er als een downstream-systeem offline gaat?

Hoe ga je met deze situaties om met behoud van de doorvoer van uw toepassing?

Met wachtrijen komt betrouwbare berichten vanzelf. In combinatie met een trigger functies maakt de functie een vergrendeling voor het wachtrijbericht. Als de verwerking mislukt, wordt het slot vrijgegeven om een andere instantie toe te staan de verwerking opnieuw te proberen. De verwerking gaat vervolgens door totdat het bericht met succes wordt geëvalueerd of wordt toegevoegd aan een gifwachtrij.

Hoewel een bericht in één wachtrij in een cyclus voor nieuwe poging kan blijven staan, blijven andere parallelle uitvoeringen de resterende berichten in de wachtrij plaatsen. Het resultaat is dat de totale doorvoer grotendeels onaangetast blijft door één slechte boodschap. Opslagwachtrijen garanderen echter geen bestelling en zijn niet geoptimaliseerd voor de hoge doorvoervereisten die door gebeurtenishubs worden vereist.

Azure Event Hubs bevatten daarentegen geen vergrendelingsconcept. Om functies zoals high-throughput, meerdere consumentengroepen en replay-ability mogelijk te maken, gedragen Event Hubs-gebeurtenissen zich meer als een videospeler. Gebeurtenissen worden gelezen vanaf één punt in de stream per partitie. Vanuit de aanwijzer u vanaf die locatie vooruit of achteruit lezen, maar u moet ervoor kiezen om de aanwijzer voor gebeurtenissen te laten verwerken.

Wanneer er fouten optreden in een stream, als u besluit de aanwijzer op dezelfde plek te houden, wordt de gebeurtenisverwerking geblokkeerd totdat de aanwijzer is gevorderd. Met andere woorden, als de aanwijzer wordt gestopt om problemen met de verwerking van een enkele gebeurtenis te behandelen, beginnen de onverwerkte gebeurtenissen zich op te stapelen.

Azure Functions voorkomt impasses door de aanwijzer van de stream te bevorderen, ongeacht het succes of de fout. Aangezien de aanwijzer blijft vooruitgaan, moeten uw functies op de juiste manier met storingen omgaan.

## <a name="how-azure-functions-consumes-event-hubs-events"></a>Hoe Azure Functions gebeurtenishubs-gebeurtenissen verbruikt

Azure Functions gebruikt Event Hub-gebeurtenissen tijdens het fietsen door de volgende stappen:

1. Er wordt een aanwijzer gemaakt en blijft bestaan in Azure Storage voor elke partitie van de gebeurtenishub.
2. Wanneer er standaard nieuwe berichten worden ontvangen (standaard in een batch), probeert de host de functie te activeren met de batch berichten.
3. Als de functie de uitvoering voltooit (met of zonder uitzondering), gaat de aanwijzer vooruit en wordt een controlepunt opgeslagen in het opslagaccount.
4. Als de functie-uitvoering niet kan worden voltooid, slaagt de host er niet in de aanwijzer te voltooien. Als de aanwijzer niet geavanceerd is, worden later controles uitgevoerd op het verwerken van dezelfde berichten.
5. Stap 2–4 herhalen

Dit gedrag onthult een paar belangrijke punten:

- *Onverwerkte uitzonderingen kunnen ertoe leiden dat u berichten verliest.* Uitvoeringen die resulteren in een uitzondering, blijven de aanwijzer verder ontwikkelen.
- *Functies garandeert ten minste eenmaal levering.* Uw code en afhankelijke systemen moeten mogelijk [rekening houden met het feit dat hetzelfde bericht twee keer kan worden ontvangen.](./functions-idempotent.md)

## <a name="handling-exceptions"></a>Afhandeling van uitzonderingen

In het algemeen moet elke functie een [try/catch-blok](./functions-bindings-error-pages.md) op het hoogste niveau van code bevatten. In het bijzonder moeten alle functies `catch` die gebeurtenishubs-gebeurtenissen gebruiken, een blok hebben. Op die manier, wanneer een uitzondering wordt verhoogd, de vangst blok verwerkt de fout voordat de aanwijzer vordert.

### <a name="retry-mechanisms-and-policies"></a>Mechanismen en beleid opnieuw proberen

Sommige uitzonderingen zijn van voorbijgaande aard en worden niet opnieuw weergegeven wanneer een bewerking even later opnieuw wordt geprobeerd. Daarom is de eerste stap altijd om de bewerking opnieuw te proberen. Je zou kunnen schrijven retry processing regels zelf, maar ze zijn zo gemeengoed dat een aantal tools beschikbaar zijn. Met deze bibliotheken u robuuste beleid voor het opnieuw proberen definiëren, waarmee u ook de verwerkingsvolgorde behouden.

Als u foutafhandelingsbibliotheken in uw functies introduceert, u zowel basis- als geavanceerd beleid voor nieuwe technieken definiëren. U bijvoorbeeld een beleid implementeren dat een werkstroom volgt die wordt geïllustreerd door de volgende regels:

- Probeer een bericht drie keer in te voegen (mogelijk met een vertraging tussen pogingen).
- Als de uiteindelijke uitkomst van alle pogingen mislukt, voegt u een bericht toe aan een wachtrij, zodat de verwerking op de stream kan worden voortgezet.
- Corrupte of onverwerkte berichten worden later verwerkt.

> [!NOTE]
> [Polly](https://github.com/App-vNext/Polly) is een voorbeeld van een bibliotheek voor veerkracht en tijdelijke foutafhandeling voor C#-toepassingen.

Bij het werken met vooraf nageleefde C#-klassebibliotheken u [met uitzonderingsfilters](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-catch) code uitvoeren wanneer er een niet-behandelde uitzondering optreedt.

Voorbeelden die aantonen hoe u uitzonderingsfilters gebruikt, zijn beschikbaar in de [Azure WebJobs SDK-repo.](https://github.com/Azure/azure-webjobs-sdk/wiki)

## <a name="non-exception-errors"></a>Fouten zonder uitzondering

Sommige problemen doen zich zelfs voor wanneer er geen fout aanwezig is. Overweeg bijvoorbeeld een fout die optreedt in het midden van een uitvoering. Als een functie in dit geval de uitvoering niet voltooit, wordt de verschuivingsaanwijzer nooit gevorderd. Als de aanwijzer niet vooruitgaat, blijft elke instantie die wordt uitgevoerd na een mislukte uitvoering dezelfde berichten lezen. Deze situatie biedt een "ten minste eenmaal" garantie.

De zekerheid dat elk bericht ten minste één keer wordt verwerkt, houdt in dat sommige berichten meer dan één keer kunnen worden verwerkt. Uw functie-apps moeten zich bewust zijn van deze mogelijkheid en moeten worden gebouwd rond de [principes van idempotentie.](./functions-idempotent.md)

## <a name="stop-and-restart-execution"></a>Uitvoering stoppen en opnieuw starten

Hoewel een paar fouten aanvaardbaar kunnen zijn, wat als uw app aanzienlijke fouten ondervindt? U stoppen met activeren op gebeurtenissen totdat het systeem een gezonde toestand bereikt. Het hebben van de mogelijkheid om de verwerking te pauzeren wordt vaak bereikt met een circuit breaker patroon. Met het stroomonderbrekerpatroon kan uw app het circuit van het gebeurtenisproces "doorbreken" en op een later tijdstip worden hervat.

Er zijn twee stukken nodig om een stroomonderbreker in een gebeurtenisproces te implementeren:

- Gedeelde status in alle instanties om de status van het circuit bij te houden en te controleren
- Hoofdproces dat de circuitstatus kan beheren (open of gesloten)

Implementatiedetails kunnen variëren, maar om de status tussen instanties te delen, hebt u een opslagmechanisme nodig. U ervoor kiezen om status op te slaan in Azure Storage, een Redis-cache of een ander account dat toegankelijk is voor een verzameling functies.

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) of [duurzame entiteiten](./durable/durable-functions-overview.md) zijn een natuurlijke pasvorm om de werkstroom- en circuitstatus te beheren. Andere services kunnen net zo goed werken, maar logische apps worden gebruikt voor dit voorbeeld. Met behulp van logische apps u de uitvoering van een functie onderbreken en opnieuw starten, zodat u de controle hebt die nodig is om het stroomonderbrekerpatroon te implementeren.

### <a name="define-a-failure-threshold-across-instances"></a>Een foutdrempel definiëren voor instanties

Om rekening te houden met meerdere instanties die gebeurtenissen tegelijk verwerken, is de aanhoudende gedeelde externe status nodig om de status van het circuit te controleren.

Een regel die u implementeren, kan dit afdwingen:

- Als er meer dan 100 eventuele fouten binnen 30 seconden in alle instanties, dan breken het circuit en stoppen met triggeren op nieuwe berichten.

De implementatiedetails variëren afhankelijk van uw behoeften, maar in het algemeen u een systeem maken dat:

1. Fouten bij een opslagaccount registreren (Azure Storage, Redis, enz.)
1. Wanneer een nieuwe fout wordt geregistreerd, controleert u het aantal rollen om te zien of de drempelwaarde is bereikt (bijvoorbeeld meer dan 100 in de laatste 30 seconden).
1. Als de drempelwaarde is bereikt, zendt u een gebeurtenis uit naar Azure Event Grid waarin het systeem wordt verteld dat het circuit moet worden afgebroken.

### <a name="managing-circuit-state-with-azure-logic-apps"></a>Circuitstatus beheren met Azure Logic Apps

In de volgende beschrijving wordt op een manier een Azure Logic App gemaakt om een functie-app niet te verwerken.

Azure Logic Apps wordt geleverd met ingebouwde connectors voor verschillende services, beschikt over stateful orchestrations en is een logische keuze om de circuitstatus te beheren. Nadat het circuit is gedetecteerd, u een logische app bouwen om de volgende werkstroom te implementeren:

1. Een werkstroom voor gebeurtenisrasteractiveren en de Azure-functie stoppen (met de Azure Resource-connector)
1. Een meldingse-mail verzenden met een optie om de werkstroom opnieuw te starten

De e-mailontvanger kan de status van het circuit onderzoeken en, indien van toepassing, het circuit opnieuw starten via een link in de meldingsmail. Als de werkstroom de functie opnieuw start, worden berichten verwerkt vanuit het laatste controlepunt van de gebeurtenishub.

Met deze aanpak gaan er geen berichten verloren, worden alle berichten in volgorde verwerkt en u het circuit zo lang breken als nodig is.

## <a name="resources"></a>Resources

- [Betrouwbare gebeurtenisverwerkingmonsters](https://github.com/jeffhollan/functions-csharp-eventhub-ordered-processing)
- [Azure Durable Functions Circuit Breaker](https://github.com/jeffhollan/functions-durable-actor-circuitbreaker)

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie:

- [Foutafhandeling azure-functies](./functions-bindings-error-pages.md)
- [Formaat van geüploade afbeeldingen automatisch wijzigen met Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json&tabs=dotnet)
- [Een functie maken die kan worden geïntegreerd met Azure Logic Apps](./functions-twitter-email.md)
