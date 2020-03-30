---
title: Upgraden naar Azure Cognitive Search .NET SDK versie 10
titleSuffix: Azure Cognitive Search
description: Migreer code naar de Azure Cognitive Search .NET SDK versie 10 van oudere versies. Meer informatie over wat nieuw is en welke codewijzigingen vereist zijn.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ad912eb0b26354d40a654a1c8782dfcb960235e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847524"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-10"></a>Upgraden naar Azure Cognitive Search .NET SDK versie 10

Als u versie 9.0 of ouder van de [Azure Search .NET SDK](https://aka.ms/search-sdk)gebruikt, helpt dit artikel u uw toepassing te upgraden naar versie 10.

Azure Search wordt omgedoopt tot Azure Cognitive Search in versie 10, maar naamruimten en pakketnamen blijven ongewijzigd. Eerdere versies van de SDK (9.0 en eerder) blijven de oude naam gebruiken. Zie [Azure Cognitive Search gebruiken vanuit een .NET-toepassing](search-howto-dotnet-sdk.md)voor meer informatie over het gebruik van de SDK, inclusief voorbeelden.

Versie 10 voegt verschillende functies en bugfixes toe, waardoor deze op hetzelfde `2019-05-06`functionele niveau komt als de meest recente versie van de REST API-versie. In gevallen waarin een wijziging de bestaande code breekt, doorlopen we de [stappen die nodig zijn om het probleem op te lossen.](#UpgradeSteps)

> [!NOTE]
> Als u versie 8.0-preview of ouder gebruikt, moet u eerst upgraden naar versie 9 en vervolgens upgraden naar versie 10. Zie [Upgraden naar de Azure Search .NET SDK-versie 9](search-dotnet-sdk-migration-version-9.md) voor instructies.
>
> Uw zoekservice-instantie ondersteunt verschillende REST API-versies, waaronder de nieuwste. U een versie blijven gebruiken wanneer deze niet langer de laatste is, maar we raden u aan uw code te migreren om de nieuwste versie te gebruiken. Wanneer u de REST API gebruikt, moet u de API-versie in elke aanvraag opgeven via de parameter api-versie. Bij gebruik van de .NET SDK bepaalt de versie van de SDK die u gebruikt de overeenkomstige versie van de REST API. Als u een oudere SDK gebruikt, u die code zonder wijzigingen blijven uitvoeren, zelfs als de service is geüpgraded om een nieuwere API-versie te ondersteunen.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-10"></a>Nieuw in versie 10
Versie 10 van de Azure Cognitive Search .NET SDK richt zich`2019-05-06`op de nieuwste algemeen beschikbare versie van de REST API ( ) met deze updates:

* Introductie van twee nieuwe vaardigheden - [Voorwaardelijke vaardigheid](cognitive-search-skill-conditional.md) en [tekstvertaling vaardigheid](cognitive-search-skill-text-translation.md).
* [Shaper-vaardigheidsingangen](cognitive-search-skill-shaper.md) zijn geherstructureerd om consolidatie uit geneste contexten mogelijk te maken. Zie voor meer informatie dit [voorbeeld JSON-definitie](https://docs.microsoft.com/azure/search/cognitive-search-skill-shaper#scenario-3-input-consolidation-from-nested-contexts).
* Toevoeging van twee nieuwe [field mapping functies:](search-indexer-field-mappings.md)
    - [urlEncode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urlencode-function)
    - [urlDecode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urldecode-function)
* In bepaalde gevallen kunnen fouten en waarschuwingen die worden weergegeven in [de status van de uitvoering van indexeren](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) aanvullende details bevatten die helpen bij het debuggen. `IndexerExecutionResult`is bijgewerkt om dit gedrag weer te geven.
* Individuele vaardigheden die binnen een [skillset zijn gedefinieerd,](cognitive-search-defining-skillset.md) `name` kunnen optioneel worden geïdentificeerd door een eigenschap op te geven.
* `ServiceLimits`toont limieten voor `IndexerExecutionInfo` complexe [typen](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) en toont relevante indexerlimieten/quota.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Stappen om te upgraden

1. Werk uw NuGet-referentie bij voor `Microsoft.Azure.Search` het gebruik van de NuGet Package Manager-console of door met de rechtermuisknop op uw projectreferenties te klikken en "NuGet-pakketten beheren..." te selecteren in Visual Studio.

2. Zodra NuGet de nieuwe pakketten en hun afhankelijkheden heeft gedownload, bouwt u uw project opnieuw op. 

3. Als uw build mislukt, moet u elke buildfout oplossen. Zie [Wijzigingen in versie 10 verbreken](#ListOfChanges) voor meer informatie over het oplossen van elke potentiële buildfout.

4. Zodra u buildfouten of waarschuwingen hebt opgelost, u wijzigingen aanbrengen in uw toepassing om te profiteren van nieuwe functionaliteit als u dat wilt. Nieuwe functies in de SDK worden beschreven in [Wat is er nieuw in versie 10](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-10"></a>Wijzigingen in versie 10 verbreken

Er zijn verschillende wijzigingen in versie 10 waarvoor mogelijk ook codewijzigingen nodig zijn naast het opnieuw opbouwen van uw toepassing.

> [!NOTE]
> De onderstaande lijst met wijzigingen is niet volledig. Sommige wijzigingen zullen waarschijnlijk niet resulteren in bouwfouten, maar zijn technisch breken omdat ze binaire compatibiliteit met assemblages die afhankelijk zijn van eerdere versies van de Azure Cognitive Search .NET SDK-assemblages breken. Belangrijke wijzigingen die onder deze categorie vallen, worden ook vermeld, samen met aanbevelingen. Herbouw uw toepassing bij het upgraden naar versie 10 om binaire compatibiliteitsproblemen te voorkomen.

### <a name="custom-web-api-skill-definition"></a>Aangepaste web-API-vaardigheiddefinitie

De definitie van de [Custom Web API-vaardigheid](cognitive-search-custom-skill-web-api.md) is onjuist opgegeven in versie 9 en ouder. 

Het model `WebApiSkill` `HttpHeaders` voor opgegeven als een objecteigenschap die een woordenboek _bevat._ Het maken van `WebApiSkill` een skillset met een constructie op deze manier zou resulteren in een uitzondering, omdat de REST API zou overwegen de aanvraag slecht gevormd. Dit probleem is verholpen door `HttpHeaders` **een woordenboekeigenschap** op `WebApiSkill` het hoogste niveau op het model zelf te maken - wat wordt beschouwd als een geldig verzoek van de REST API.

Als u bijvoorbeeld eerder hebt geprobeerd een `WebApiSkill` als volgt te instantiëren:

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new WebApiHttpHeaders()
    {
        Headers = new Dictionary<string, string>()
        {
            ["header"] = "value"
        }
    }
};

```

wijzig deze in het volgende om de validatiefout van de REST API te voorkomen:

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new Dictionary<string, string>()
    {
        ["header"] = "value"
    }
};

```

## <a name="shaper-skill-allows-nested-context-consolidation"></a>Shaper-vaardigheid maakt geneste contextconsolidatie mogelijk

Shaper-vaardigheid kan nu invoerconsolidatie mogelijk maken vanuit geneste contexten. Om deze wijziging mogelijk `InputFieldMappingEntry` te maken, hebben we deze aangepast `Source` zodat deze kan `SourceContext` `Inputs` worden geinstantieerd door alleen een eigenschap of zowel de eigenschappen als de eigenschappen op te geven.

U hoeft waarschijnlijk geen codewijzigingen aan te brengen; houd er echter rekening mee dat slechts één van deze twee combinaties is toegestaan. Dit betekent:

- Het `InputFieldMappingEntry` maken `Source` van een plaats waar alleen geïnitialiseerd is geldig.
- Het `InputFieldMappingEntry` maken `SourceContext` van `Inputs` een waar alleen en worden geïnitialiseerd is geldig.
- Alle andere combinaties met deze drie eigenschappen zijn ongeldig.

Als u besluit gebruik te gaan maken van deze nieuwe mogelijkheid, moet u ervoor zorgen dat al uw clients worden bijgewerkt om versie 10 eerst te gebruiken, voordat u die wijziging uitrolt. Anders bestaat de mogelijkheid dat een update door een client (met behulp van een oudere versie van de SDK) naar de Shaper-vaardigheid kan leiden tot validatiefouten.

> [!NOTE]
> Hoewel het `InputFieldMappingEntry` onderliggende model is gewijzigd om consolidatie uit geneste contexten mogelijk te maken, is het gebruik alleen geldig binnen de definitie van een Shaper-vaardigheid. Als u deze mogelijkheid in andere vaardigheden gebruikt, terwijl deze geldig is tijdens het compileren, resulteert dit in een validatiefout bij runtime.

## <a name="skills-can-be-identified-by-a-name"></a>Vaardigheden kunnen worden geïdentificeerd door een naam

Elke vaardigheid binnen een skillset `Name`heeft nu een nieuwe eigenschap, die kan worden geïnitialiseerd in uw code om te helpen bij het identificeren van de vaardigheid. Dit is optioneel - wanneer niet-gespecificeerd (dat is de standaard, als er geen expliciete code wijziging is aangebracht), is het toegewezen een standaard naam met behulp van de 1-gebaseerde index van de vaardigheid in de skillset, vooraf vastgesteld met de '#' karakter. Bijvoorbeeld in de volgende skillset-definitie (de meeste initialisaties die zijn overgeslagen voor beknoptheid):

```csharp
var skillset = new Skillset()
{
    Skills = new List<Skill>()
    {
        new SentimentSkill(),
        new WebApiSkill(),
        new ShaperSkill(),
        ...
    }
}
```

`SentimentSkill`is toegewezen `#1`een `WebApiSkill` naam `#2` `ShaperSkill` , `#3` wordt toegewezen , wordt toegewezen en ga zo maar door.

Als u ervoor kiest om vaardigheden te identificeren aan de andere maat, moet u eerst alle exemplaren van uw clients bijwerken naar versie 10 van de SDK. Anders bestaat de mogelijkheid dat een client die een `null` oudere `Name` versie van de SDK gebruikt, de eigenschap van een vaardigheid kan uitvoeren, waardoor de client terugvalt op het standaardnaamgevingsschema.

## <a name="details-about-errors-and-warnings"></a>Details over fouten en waarschuwingen

`ItemError`en `ItemWarning` modellen die details inkapselen van fouten en waarschuwingen (respectievelijk) die optreden tijdens een indexeruitvoering zijn gewijzigd om drie nieuwe eigenschappen op te nemen met als doel om te helpen bij het debuggen van de indexeerder. Deze eigenschappen zijn:

- `Name`: De naam van de bron waar de fout vandaan is gekomen. Het kan bijvoorbeeld verwijzen naar een bepaalde vaardigheid in de bijgevoegde vaardigheden.
- `Details`: Aanvullende details over de fout of waarschuwing.
- `DocumentationLink`: Een koppeling naar een handleiding voor het oplossen van problemen voor de specifieke fout of waarschuwing.

> [!NOTE]
> We zijn begonnen met het structureren van onze fouten en waarschuwingen om deze nuttige details waar mogelijk op te nemen. We werken eraan om ervoor te zorgen dat voor alle fouten en waarschuwingen deze details aanwezig zijn, maar het is een work in progress en deze aanvullende details kunnen niet altijd worden ingevuld.

## <a name="next-steps"></a>Volgende stappen

- Wijzigingen in de Shaper-vaardigheid hebben de meeste potentiële impact op nieuwe of bestaande code. Als volgende stap moet u dit voorbeeld opnieuw bekijken ter illustratie van de invoerstructuur: [voorbeeld van shaper-vaardigheid JSON-definitie](cognitive-search-skill-shaper.md)
- Ga door het [AI-verrijkingsoverzicht](cognitive-search-concept-intro.md).
- Wij zijn blij met uw feedback op de SDK. Als u problemen ondervindt, u ons gerust om hulp vragen op [Stack Overflow.](https://stackoverflow.com/questions/tagged/azure-search) Als u een bug vindt, u een probleem indienen in de [Azure .NET SDK GitHub-repository.](https://github.com/Azure/azure-sdk-for-net/issues) Zorg ervoor dat u de uitgiftetitel vooraf opmaakt met "[Azure Cognitive Search]".

