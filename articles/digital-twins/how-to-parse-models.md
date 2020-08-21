---
title: Modellen parseren en valideren
titleSuffix: Azure Digital Twins
description: Meer informatie over het gebruik van de parser-bibliotheek voor het parseren van DTDL-modellen.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 69b52be3a3eca2ab48ed09f6401780ea033f223c
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723976"
---
# <a name="parse-and-validate-models-with-the-dtdl-parser-library"></a>Modellen parseren en valideren met de DTDL parser-bibliotheek

[Modellen](concepts-models.md) in azure Digital apparaatdubbels worden gedefinieerd met behulp van de op JSON-LD gebaseerde Digital apparaatdubbels Definition Language (DTDL). **Het is raadzaam om uw modellen offline te valideren voordat u ze uploadt naar uw Azure Digital Apparaatdubbels-exemplaar.**

U kunt dit doen door een .NET-bibliotheek voor DTDL-parsering aan de client zijde te maken op NuGet: [**micro soft. Azure. DigitalTwins. parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). 

U kunt de parser-bibliotheek rechtstreeks in uw C#-code gebruiken of het voorbeeld project language-neutraal code gebruiken dat is gebaseerd op de parser-bibliotheek: [**DTDL validator**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator)-voor beeld.

## <a name="use-the-dtdl-validator-sample"></a>Het voor beeld van de DTDL-validator gebruiken

De [**validatie functie DTDL**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator) is een voorbeeld project dat model documenten kan valideren om er zeker van te zijn dat de DTDL geldig is. Het is gebaseerd op de .NET parser-bibliotheek en is neutraal. U kunt het downloaden met behulp van de knop voor het laden van een *zip* -voorbeeld koppeling.

De bron code bevat voor beelden voor het gebruik van de parser-bibliotheek. U kunt het voor beeld validator als een opdracht regel programma gebruiken om een mapstructuur van DTDL-bestanden te valideren. Het biedt ook een interactieve modus.

In de map voor het voor beeld van de DTDL-validator raadpleegt u het *README.MD* -bestand voor instructies over hoe u het voor beeld kunt inpakken in een zelfstandig uitvoerbaar bestand.

Nadat u een zelfstandig pakket hebt gemaakt en het uitvoer bare bestand aan uw pad hebt toegevoegd, kunt u de validatie functie met deze opdracht uitvoeren in een-console op uw computer:

```cmd/sh
DTDLValidator
```

Met de standaard opties zoekt het voor beeld naar `*.json` bestanden in de huidige map en alle submappen. U kunt ook de volgende optie toevoegen om het voor beeld te zoeken in de aangegeven map en alle submappen voor bestanden met de extensie *. dtdl*:

```cmd/sh
DTDLValidator -d C:\Work\DTDL -e dtdl 
```

U kunt de `-i` optie voor het voor beeld toevoegen om de interactieve modus in te voeren:

```cmd/sh
DTDLValidator -i
```

Zie voor meer informatie over dit voor beeld de bron code of de uitvoering `DTDLValidator --help` .

## <a name="use-the-net-parser-library"></a>De .NET parser-bibliotheek gebruiken 

De bibliotheek [**micro soft. Azure. DigitalTwins. parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/) biedt model toegang tot de DTDL definities, die in wezen optreden als het equivalent van C# reflectie voor DTDL. Deze tape wisselaar kan onafhankelijk van een [Azure Digital APPARAATDUBBELS SDK](how-to-use-apis-sdks.md)worden gebruikt, met name voor DTDL-validatie in een visuele of tekst editor. Het is handig om te controleren of uw model definitie bestanden geldig zijn voordat u deze uploadt naar de service.

Als u de parser-bibliotheek wilt gebruiken, geeft u deze een set DTDL-documenten. Normaal gesp roken haalt u deze model documenten op uit de-service, maar u kunt ze ook lokaal beschikbaar stellen als uw client verantwoordelijk is voor het uploaden van ze naar de service in de eerste plaats. 

Hier volgt de algemene werk stroom voor het gebruik van de parser:
1. Haal sommige of alle DTDL-documenten op uit de service.
2. Geef de geretourneerde, in-Memory DTDL-documenten door aan de parser.
3. De parser valideert de set van documenten die hieraan worden door gegeven en retourneert gedetailleerde fout gegevens. Deze mogelijkheid is handig in scenario's met de editor.
4. Gebruik de parser-Api's om door te gaan met het analyseren van de modellen die zijn opgenomen in de documentenset. 

De mogelijkheden van de parser zijn onder andere:
* Alle geïmplementeerde model interfaces ophalen (de inhoud van de sectie van de interface `extends` ).
* Alle eigenschappen, telemetrie, opdrachten, onderdelen en relaties die in het model zijn gedeclareerd, ophalen. Met deze opdracht worden ook alle meta gegevens in deze definities opgehaald en worden de overname ( `extends` secties) in rekening gebracht.
* Alle complexe model definities ophalen.
* Bepaal of een model kan worden toegewezen vanuit een ander model.

> [!NOTE]
> [IoT Plug en Play-apparaten (PnP)](../iot-pnp/overview-iot-plug-and-play.md) gebruiken een kleine syntaxis variant om hun functionaliteit te beschrijven. Deze variant van de syntaxis is een semantisch compatibele subset van de DTDL die wordt gebruikt in azure Digital Apparaatdubbels. Wanneer u de parser-bibliotheek gebruikt, hoeft u niet te weten welke syntaxis variant is gebruikt voor het maken van de DTDL voor uw digitale twee. De parser retourneert altijd standaard hetzelfde model voor zowel de syntaxis van PnP-als Azure Digital Apparaatdubbels.

### <a name="code-with-the-parser-library"></a>Code met de parser-bibliotheek

U kunt de parser-bibliotheek rechtstreeks gebruiken voor zaken als het valideren van modellen in uw eigen toepassing of voor het genereren van dynamische, op modellen gebaseerde gebruikers interface, Dash boards en rapporten.

Voor de ondersteuning van het volgende voor beeld van de parsers code moet u rekening houden met verschillende modellen die zijn gedefinieerd in een Azure Digital Apparaatdubbels-exemplaar:

> [!TIP] 
> Het `dtmi:com:contoso:coffeeMaker` model gebruikt de syntaxis van het *mogelijkheidsprofiel* , wat impliceert dat het is geïnstalleerd in de service door een PnP-apparaat te verbinden dat dat model weergeeft.

```json
{
  "@id": " dtmi:com:contoso:coffeeMaker",
  "@type": "CapabilityModel",
  "implements": [
        { "name": "coffeeMaker", "schema": " dtmi:com:contoso:coffeeMakerInterface" }
  ]    
}
{
  "@id": " dtmi:com:contoso:coffeeMakerInterface",
  "@type": "Interface",
  "contents": [
      { "@type": "Property", "name": "waterTemp", "schema": "double" }  
  ]
}
{
  "@id": " dtmi:com:contoso:coffeeBar",
  "@type": "Interface",
  "contents": [
        { "@type": "relationship", "contains": " dtmi:com:contoso:coffeeMaker" },
        { "@type": "property", "name": "capacity", "schema": "integer" }
  ]    
}
```

De volgende code toont een voor beeld van het gebruik van de parser-bibliotheek om deze definities in C# weer te geven:

```csharp
async void ParseDemo(DigitalTwinsClient client)
{
    try
    {
        AsyncPageable<ModelData> mdata = client.GetModelsAsync(null, true);
        List<string> models = new List<string>();
        await foreach (ModelData md in mdata)
            models.Add(md.Model);
        ModelParser parser = new ModelParser();
        IReadOnlyDictionary<Dtmi, DTEntityInfo> dtdlOM = await parser.ParseAsync(models);

        List<DTInterfaceInfo> interfaces = new List<DTInterfaceInfo>();
        IEnumerable<DTInterfaceInfo> ifenum = 
            from entity in dtdlOM.Values
            where entity.EntityKind == DTEntityKind.Interface
            select entity as DTInterfaceInfo;
        interfaces.AddRange(ifenum);
        foreach (DTInterfaceInfo dtif in interfaces)
        {
            PrintInterfaceContent(dtif, dtdlOM);
        }

    } catch (RequestFailedException rex)
    {

    }
}

void PrintInterfaceContent(DTInterfaceInfo dtif, IReadOnlyDictionary<Dtmi, DTEntityInfo> dtdlOM, int indent=0)
{
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < indent; i++) sb.Append("  ");
    Console.WriteLine($"{sb}Interface: {dtif.Id} | {dtif.DisplayName}");
    SortedDictionary<string, DTContentInfo> contents = dtif.Contents;
    foreach (DTContentInfo item in contents.Values)
    {
        switch (item.EntityKind)
        {
            case DTEntityKind.Property:
                DTPropertyInfo pi = item as DTPropertyInfo;
                Console.WriteLine($"{sb}--Property: {pi.Name} with schema {pi.Schema}");
                break;
            case DTEntityKind.Relationship:
                DTRelationshipInfo ri = item as DTRelationshipInfo;
                Console.WriteLine($"{sb}--Relationship: {ri.Name} with target {ri.Target}");
                break;
            case DTEntityKind.Telemetry:
                DTTelemetryInfo ti = item as DTTelemetryInfo;
                Console.WriteLine($"{sb}--Telemetry: {ti.Name} with schema {ti.Schema}");
                break;
            case DTEntityKind.Component:
                DTComponentInfo ci = item as DTComponentInfo;
                Console.WriteLine($"{sb}--Component: {ci.Id} | {ci.Name}");
                dtdlOM.TryGetValue(ci.Id, out DTEntityInfo value);
                DTInterfaceInfo component = value as DTInterfaceInfo;
                PrintInterfaceContent(component, dtdlOM, indent + 1);
                break;
            default:
                break;
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen

Wanneer u klaar bent met het schrijven van uw modellen, raadpleegt u de DigitalTwinsModels-Api's om ze te uploaden (en andere beheer bewerkingen uit te voeren):
* [*Instructies: Aangepaste modellen beheren*](how-to-manage-model.md)