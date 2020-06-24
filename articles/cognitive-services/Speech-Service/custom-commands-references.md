---
title: Aangepaste opdrachten concepten en definities-spraak service
titleSuffix: Azure Cognitive Services
description: In dit artikel vindt u informatie over de concepten en definities voor toepassingen voor aangepaste opdrachten.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 5a5e3590e0e184633760f080dfd3402ed75bd48e
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307517"
---
# <a name="custom-commands-concepts-and-definitions"></a>Concepten en definities voor aangepaste opdrachten

In dit artikel vindt u informatie over concepten en definities voor toepassingen voor aangepaste opdrachten.

## <a name="commands-configuration"></a>Configuratie van opdrachten
Opdrachten zijn de basis bouwstenen van een toepassing met aangepaste opdrachten. Een opdracht is een set configuraties die vereist is voor het volt ooien van een specifieke taak gedefinieerd door een gebruiker.

### <a name="example-sentences"></a>Voorbeeld zinnen
Voor beeld uitingen zijn de set-voor beelden die de gebruiker kan zeggen dat er een bepaalde opdracht wordt geactiveerd. Houd er rekening mee dat u alleen een voor beeld van uitingen moet opgeven en geen volledige lijst. 

### <a name="parameters"></a>Parameters
Para meters zijn de vereiste gegevens voor de opdrachten voor het volt ooien van een taak. In complexe scenario's kunnen para meters ook worden gebruikt om voor waarden te definiëren waarmee aangepaste acties worden geactiveerd.

### <a name="completion-rules"></a>Voltooiings regels
Reeks regels die moeten worden uitgevoerd wanneer de opdracht gereed is om te worden vervuld, d.w.z. Wanneer aan alle voor waarden van de regels wordt voldaan.

### <a name="interaction-rules"></a>Interactie regels
Aanvullende regels voor het afhandelen van specifieke of complexe situaties. U kunt aanvullende validaties toevoegen of geavanceerde functies configureren, zoals bevestigingen of een correctie in één stap. U kunt ook uw eigen aangepaste interactie regels maken.

## <a name="parameters-configuration"></a>Configuratie van para meters

Para meters zijn gegevens die worden vereist door opdrachten voor het volt ooien van een taak. In complexe scenario's kunnen para meters ook worden gebruikt om voor waarden te definiëren waarmee aangepaste acties worden geactiveerd.

### <a name="name"></a>Name
Een para meter wordt geïdentificeerd door de eigenschap name. U moet altijd een beschrijvende naam opgeven voor een para meter. Een para meter kan worden aangeduid in verschillende secties, zoals-tijdens het maken van voor waarden, spraak reacties of andere acties.
 
### <a name="isglobal"></a>IsGlobal
Selectie vakje dat aangeeft of het bereik van deze para meter wordt gedeeld door alle opdrachten in de toepassing. Als een para meter globaal is, kan de waarde ervan in elk opdracht bereik worden gegeven en zodra een waarde is toegewezen. deze kan vanuit elk van de opdrachten worden aangeduid. 

### <a name="required"></a>Vereist
Selectie vakje om aan te geven of een waarde voor deze para meter is vereist voor het volt ooien van de opdracht. U moet antwoorden configureren om de gebruiker te vragen om een waarde op te geven als een para meter is gemarkeerd als vereist.

### <a name="type"></a>Type
Aangepaste opdrachten bieden ondersteuning voor de volgende typen para meters:


* DateTime
* Geografie
* Getal
* Tekenreeks

Al deze parameter typen ondersteunen configuratie van de standaard waarde, die u vanuit de portal kunt configureren.

### <a name="configuration"></a>Configuratie
Configuratie is een parameter eigenschap die alleen is gedefinieerd voor type: String. Hieronder vindt u de ondersteunde waarden
* Geen
* Volledige invoer accepteren: als u deze optie inschakelt, accepteert de para meter alle invoer utterance. Dit is handig wanneer de gebruiker een para meter met de volledige utterance nodig heeft. bijvoorbeeld post adressen.
* Vooraf gedefinieerde invoer waarden accepteren uit externe catalogus: wordt gebruikt voor het configureren van para meters die een grote verscheidenheid aan waarden kunnen aannemen. bijvoorbeeld verkoop catalogus. In dit geval wordt de catalogus gehost op een extern webeindpunt en kan deze onafhankelijk worden geconfigureerd.
* Vooraf gedefinieerde invoer waarden accepteren uit interne catalogus: wordt gebruikt voor het configureren van para meters die een paar waarden kunnen aannemen. In dit geval moeten waarden worden geconfigureerd in de speech Studio.


### <a name="validation"></a>Validatie
Validaties zijn constructies die van toepassing zijn op bepaalde parameter typen, waarmee u beperkingen kunt configureren voor de waarde van de para meter. Op dit moment worden door aangepaste opdrachten validaties ondersteund voor de volgende typen para meters:
* DateTime
* Getal

## <a name="rules-configuration"></a>Configuratie van regels
Een regel in aangepaste opdrachten wordt gedefinieerd door een set **voor waarden**, die wanneer wordt voldaan, een reeks **acties**uitvoert. Met regel kunt u ook de status van de **post-uitvoering** en de **verwachtingen** voor de volgende beurt configureren.

### <a name="types"></a>Typen
Aangepaste opdrachten ondersteunen de volgende regel Categorieën:
* Voltooiings regels

    Lijst met regels die moeten worden uitgevoerd bij het uitvoeren van de opdracht. Alle regels die in deze sectie zijn geconfigureerd waarvoor de voor waarden waar zijn, worden uitgevoerd.
    
* Interactie regels

    Interactie regels kunnen worden gebruikt voor het configureren van aanvullende aangepaste validaties, bevestigingen, correctie in één stap of voor het uitvoeren van andere logica voor aangepaste dialoog vensters. Deze regels worden geëvalueerd op elke beurt en kunnen worden gebruikt voor het activeren van de voltooiings regels.

De verschillende acties die zijn geconfigureerd als onderdeel van een regel, worden uitgevoerd in de volg orde waarin ze worden weer gegeven in de portal voor ontwerpen.
    
### <a name="conditions"></a>Voorwaarden
Set voor waarden waaraan moet worden voldaan om een regel uit te voeren. De voor waarde voor regels kan van de volgende typen zijn:
* Parameter waarde is gelijk aan: de waarde van de geconfigureerde para meter is gelijk aan een specifieke waarde.
* Geen parameter waarde: geconfigureerde para meters mogen geen waarde hebben.
* Vereiste para meters: de geconfigureerde para meter heeft een waarde.
* Alle vereiste para meters: alle para meters die zijn gemarkeerd als vereist, hebben een waarde.
* Bijgewerkte para meters: een of meer parameter waarden zijn bijgewerkt als gevolg van het verwerken van de huidige invoer (utterance/activity).
* De bevestiging is voltooid: de invoer voor utterance/activity is geslaagd (Ja).
* Bevestiging geweigerd: invoer utterance/-activiteit is geslaagd (Nee).
* De vorige opdracht moet worden bijgewerkt: dit probleem wordt gebruikt in gevallen waarin u een negatie bevestiging wilt opvangen en een update. Achter de schermen wordt dit geconfigureerd voor wanneer de dialoog engine een negatieve bevestiging detecteert waarbij de intentie hetzelfde is als de vorige beurt en de gebruiker heeft gereageerd op een update.

### <a name="actions"></a>Acties
* Spraak antwoord verzenden: een antwoord verzenden naar de client.
* Waarde van para meter bijwerken: update waarde van een opdracht parameter naar een opgegeven waarde.
* Parameter waarde wissen waarde van opdracht parameter gewist.
* Web-eind punt aanroepen: een aanroep naar een webeindpunt maken.
* Activiteit verzenden naar client: een aangepaste activiteit verzenden naar de client.

### <a name="expectations"></a>Uiteen
Verwachtingen worden gebruikt voor het configureren van hints voor de verwerking van de volgende gebruikers invoer. De volgende typen worden ondersteund:
* De verwachte bevestiging van de gebruiker: geeft aan dat de toepassing een bevestiging verwacht (Ja/Nee) voor de volgende invoer van de gebruiker.
* De invoer van para meter (s) van de gebruiker wordt verwacht: Geef een of meer opdracht parameters op die de toepassing verwacht van de gebruikers invoer.

### <a name="post-execution-state"></a>Status na uitvoering
Status van het dialoog venster na het verwerken van de huidige invoer (utterance/activity). Het gaat om de volgende typen:
* Opdracht voltooid: Voltooi de opdracht en er worden geen aanvullende regels van de opdracht verwerkt.
* Voltooiings regels uitvoeren: Voer alle geldige voltooiings regels uit.
* Wachten op invoer van de gebruiker: wacht op de volgende invoer van de gebruiker.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Bekijk de voor beelden op GitHub](https://aka.ms/speech/cc-samples)
