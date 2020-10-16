---
title: Begrippen begrijpen van de opslag plaats voor Apparaatbeheer | Microsoft Docs
description: Als oplossings ontwikkelaar of IT-Professional leert u meer over de basis concepten van de opslag plaats van het model van het apparaat.
author: rido-min
ms.author: rmpablos
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 95c9b6dee402bc0c2dd2cab8ef3200cfd9213d61
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92126821"
---
# <a name="device-model-repository"></a>Opslag plaats voor device model

Met de device model-opslag plaats (DMR) kunnen apparaats modellen IoT Plug en Play-apparaten beheren en delen. De modellen van het apparaat zijn JSON-bestanden die zijn gedefinieerd met de [Digital Apparaatdubbels Modeling Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

De DMR definieert een patroon voor het opslaan van DTDL-interfaces in een mappen structuur op basis van de dubbele model-id van het apparaat (DTMI). U kunt een interface vinden in de DMR door de DTMI te converteren naar een relatief pad. Bijvoorbeeld, de `dtmi:com:example:Thermostat;1` DTMI vertaalt naar `/dtmi/com/example/thermostat-1.json` .

## <a name="public-device-model-repository"></a>Model opslagplaats voor open bare apparaten

Micro soft host een open bare DMR met de volgende kenmerken:

- Gecurede modellen. Micro soft beoordeelt en goed keurt alle beschik bare interfaces met behulp van een open GitHub PR-validatie werk stroom.
- Onveranderbaarheid.  Nadat deze is gepubliceerd, kan een interface niet worden bijgewerkt.
- Hyper-scale. Micro soft biedt de vereiste infra structuur voor het maken van een veilig en zeer schaalbaar eind punt.

## <a name="custom-device-model-repository"></a>Aangepaste opslag plaats voor model apparaten

U kunt hetzelfde DMR-patroon gebruiken in elk opslag medium, zoals het lokale bestands systeem of aangepaste HTTP-webservers, om een aangepaste DMR te maken. U kunt modellen van de aangepaste DMR op dezelfde manier ophalen als uit de open bare DMR door de basis-URL voor toegang tot de DMR te wijzigen.

> [!NOTE]
> De hulpprogram ma's die worden gebruikt om de modellen in de open bare DMR te valideren, kunnen opnieuw worden gebruikt in aangepaste opslag plaatsen.

## <a name="public-models"></a>Open bare modellen

De open bare digitale twee modellen die zijn opgeslagen in de model opslagplaats, zijn beschikbaar voor iedereen om in hun toepassingen te gebruiken en te integreren. Open bare modellen bieden een open eco-systeem voor apparaten en oplossings ontwikkelaars om hun IoT Plug en Play-apparaten te delen en opnieuw te gebruiken.

Raadpleeg de sectie [een model publiceren](#publish-a-model) voor instructies over het publiceren van een model in de model opslagplaats om het openbaar te maken.

Gebruikers kunnen open bare interfaces zoeken, doorzoeken en weer geven vanuit de officiële [github-opslag plaats](https://github.com/Azure/iot-plugandplay-models).

Alle interfaces in de `dtmi` mappen zijn ook beschikbaar vanuit het open bare eind punt [https://devicemodels.azure.com](https://devicemodels.azure.com)

### <a name="resolve-models"></a>Modellen oplossen

Als u via een programma toegang wilt krijgen tot deze interfaces, moet u een dtmi converteren naar een relatief pad dat u kunt gebruiken voor het uitvoeren van een query op het open bare eind punt. In het volgende code voorbeeld ziet u hoe u dit kunt doen:

Als u een DTMI wilt omzetten in een absoluut pad, gebruiken we de `DtmiToPath` functie, met `IsValidDtmi` :

```cs
static string DtmiToPath(string dtmi)
{
    if (!IsValidDtmi(dtmi))
    {
        return null;
    }
    // dtmi:com:example:Thermostat;1 -> dtmi/com/example/thermostat-1.json
    return $"/{dtmi.ToLowerInvariant().Replace(":", "/").Replace(";", "-")}.json";
}

static bool IsValidDtmi(string dtmi)
{
    // Regex defined at https://github.com/Azure/digital-twin-model-identifier#validation-regular-expressions
    Regex rx = new Regex(@"^dtmi:[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?(?::[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?)*;[1-9][0-9]{0,8}$");
    return rx.IsMatch(dtmi);
}
```

Met het resulterende pad en de basis-URL voor de opslag plaats kunnen we de interface verkrijgen:

```cs
const string _repositoryEndpoint = "https://devicemodels.azure.com";

string dtmiPath = DtmiToPath(dtmi.ToString());
string fullyQualifiedPath = $"{_repositoryEndpoint}{dtmiPath}";
string modelContent = await _httpClient.GetStringAsync(fullyQualifiedPath);
```

## <a name="publish-a-model"></a>Een model publiceren

> [!Important]
> U moet een GitHub-account hebben om modellen te kunnen verzenden naar de open bare DMR.

1. Fork the Public GitHub opslag plaats: [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models) .
1. Kloon de gevorkte opslag plaats. Maak eventueel een nieuwe vertakking om uw wijzigingen geïsoleerd van de vertakking te laten blijven `main` .
1. Voeg de nieuwe interfaces toe aan de `dtmi` map met de Conventie map/bestands naam. Zie het hulp programma [add-model](#add-model) .
1. Valideer de modellen lokaal met behulp van de [scripts voor het valideren](#validate-files) van de wijzigingen sectie.
1. De wijzigingen lokaal door voeren en naar uw Fork pushen.
1. Maak vanuit uw Fork een PR die gericht is op de `main` vertakking. Zie [een uitgifte-of pull-aanvraag documenten maken](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request) .
1. Bekijk de [PR-vereisten](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md).

Met de PR wordt een reeks GitHub-acties geactiveerd waarmee de nieuwe toegezonden interfaces worden gevalideerd. Controleer of uw PR voldoet aan alle controles.

Micro soft reageert op een PR met alle controles in drie werk dagen.

### <a name="add-model"></a>add-model

De volgende stappen laten zien hoe het add-model.js script u helpt een nieuwe interface toe te voegen. Voor dit script moet Node.js worden uitgevoerd:

1. Ga vanaf een opdracht prompt naar het lokale Git-opslag plaats
1. `npm install` uitvoeren
1. `npm run add-model <path-to-file-to-add>` uitvoeren

Bekijk de console-uitvoer voor eventuele fout berichten.

### <a name="local-validation"></a>Lokale validatie

U kunt dezelfde validatie controles lokaal uitvoeren voordat u de PR verzendt om problemen vooraf te kunnen vaststellen.

#### <a name="validate-files"></a>validate-files

`npm run validate-files <file1.json> <file2.json>` Hiermee wordt gecontroleerd of het bestandspad overeenkomt met de verwachte map-en bestands namen.

#### <a name="validate-ids"></a>validate-id's

`npm run validate-ids <file1.json> <file2.json>` Hiermee wordt gecontroleerd of alle Id's die in het document zijn gedefinieerd, dezelfde root gebruiken als de hoofd-ID.

#### <a name="validate-deps"></a>validate-deps

`npm run validate-deps <file1.json> <file2.json>` Hiermee controleert u of alle afhankelijkheden beschikbaar zijn in de `dtmi` map.

#### <a name="validate-models"></a>valideren-modellen

U kunt het [DTDL-validatie voorbeeld](https://github.com/Azure-Samples/DTDL-Validator) uitvoeren om uw modellen lokaal te valideren.

## <a name="next-steps"></a>Volgende stappen

De voorgestelde volgende stap is het controleren van de [IoT Plug en Play-architectuur](concepts-architecture.md).
