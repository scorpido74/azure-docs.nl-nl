---
title: Azure-app configuratie sleutel-waarde Store
description: Een overzicht van hoe configuratie gegevens worden opgeslagen in Azure-app configuratie.
author: yegu-ms
ms.author: yegu
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 11e019e276944e0c3f50d604420046979b5f3fbb
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/26/2019
ms.locfileid: "75495282"
---
# <a name="keys-and-values"></a>Sleutels en waarden

Azure-app-configuratie slaat configuratie gegevens op als sleutel-waardeparen. Sleutel-waardeparen zijn een eenvoudige, nog flexibele manier om verschillende soorten toepassings instellingen te vertegenwoordigen waarmee ontwikkel aars bekend zijn.

## <a name="keys"></a>Sleutels

Sleutels fungeren als de naam voor sleutel-waardeparen en worden gebruikt voor het opslaan en ophalen van de bijbehorende waarden. Het is een veelvoorkomende procedure om sleutels te organiseren in een hiërarchische naam ruimte met behulp van een teken scheidings tekens, zoals `/` of `:`. Gebruik een Conventie die het meest geschikt is voor uw toepassing. Voor App Configuration zijn sleutels een op zichzelf staand object. Sleutels worden niet geparseerd om erachter te komen hoe hun namen worden gestructureerd of dat er regels worden afgedwongen.

Het gebruik van configuratie gegevens in Application Frameworks kan specifieke naamgevings schema's voor sleutel waarden dicteren. Een voor beeld: het lente Cloud-Framework van Java definieert `Environment` resources die instellingen leveren aan een lente toepassing die door variabelen met de naam en het *profiel*van de *toepassing* wordt geleverd. Sleutels voor lente-Cloud configuratie gegevens beginnen meestal met deze twee elementen gescheiden door een scheidings teken.

Sleutels die zijn opgeslagen in App Configuration zijn hoofdlettergevoelige tekenreeksen, op basis van Unicode. De sleutels *app1* en *app1* zijn uniek in een app-configuratie opslag. Houd dit in gedachten wanneer u configuratie-instellingen in een toepassing gebruikt omdat sommige Frameworks configuratie sleutels hoofdletter gevoelig verwerken. In het ASP.NET Core-configuratiesysteem worden sleutels bijvoorbeeld als niet-hoofdlettergevoelige tekenreeksen behandeld. Om onvoorspelbaar gedrag te voor komen bij het uitvoeren van een query op de app-configuratie binnen een ASP.NET Core-toepassing, moet u geen sleutels gebruiken die alleen verschillen per behuizing.

U kunt elk Unicode-teken gebruiken in sleutel namen die zijn ingevoerd in de app-configuratie, met uitzonde ring van `*`, `,`en `\`. Deze tekens zijn gereserveerd. Als u een gereserveerd teken moet toevoegen, moet u dit met behulp van `\{Reserved Character}`verescapeen. Er is een gecombineerde grootte limiet van 10.000 tekens voor een sleutel/waarde-paar. Deze limiet omvat alle tekens in de sleutel, de bijbehorende waarde en alle bijbehorende optionele kenmerken. Binnen deze limiet kunt u veel niveaus voor sleutels gebruiken.

### <a name="design-key-namespaces"></a>Naam ruimten van ontwerp sleutel

Er zijn twee algemene methoden voor naamgeving van sleutels die worden gebruikt voor configuratiegegevens: plat en hiërarchisch. Deze methoden zijn vergelijkbaar met het standpunt van een toepassings gebruik, maar hiërarchische naamgeving biedt een aantal voor delen:

* Eenvoudiger te lezen. In plaats van een lange teken reeks worden scheidings tekens in een naam van een hiërarchische sleutel als spaties in een zin gebruikt. Ze bieden ook natuurlijke onderbrekingen tussen woorden.
* Eenvoudiger te beheren. Een hiërarchie voor sleutelnamen vertegenwoordigt logische groepen van configuratiegegevens.
* Eenvoudiger te gebruiken. Het is eenvoudiger om een query te schrijven die het patroon volgt van sleutels in een hiërarchische structuur, om zo slechts een gedeelte van de configuratiegegevens op te halen. Daarnaast hebben veel nieuwere programmeer raamwerken systeem eigen ondersteuning voor hiërarchische configuratie gegevens, zodat uw toepassing gebruik kan maken van specifieke configuratie sets.

U kunt sleutels in App Configuration op allerlei manieren hiërarchisch indelen. U beschouwt dergelijke sleutels als [uri's](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Elke hiërarchische sleutel is een *bronpad* dat bestaat uit een of meer onderdelen die samen worden samengevoegd door scheidings tekens. Kies welk teken moet worden gebruikt als een scheidings punt op basis van de behoeften van uw toepassing, programmeer taal of Framework. Gebruik meerdere scheidings tekens voor verschillende sleutels in de app-configuratie.

Hier volgen enkele voorbeelden van hoe u sleutelnamen in een hiërarchie kunt indelen:

* Op basis van componentservices

        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint

* Op basis van implementatieregio's

        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint

### <a name="label-keys"></a>Label sleutels

Sleutel waarden in app-configuratie kunnen optioneel een label kenmerk hebben. Labels worden gebruikt om sleutel waarden te onderscheiden met dezelfde sleutel. Een Key *app1* met labels *A* en *B* vormt twee afzonderlijke sleutels in een app-configuratie opslag. Het label voor een sleutel waarde is standaard leeg of `null`.

Label biedt een handige manier om varianten van een sleutel te maken. Het gebruik van labels is gebruikelijk om meerdere omgevingen op te geven voor dezelfde sleutel:

    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production

### <a name="version-key-values"></a>Versie sleutel waarden

App-configuratie bevat niet automatisch sleutel waarden zoals ze worden gewijzigd. Gebruik labels als een manier om meerdere versies van een sleutel waarde te maken. U kunt bijvoorbeeld een versie nummer van een toepassing of een Git-doorvoer-ID invoeren in labels om sleutel waarden te identificeren die zijn gekoppeld aan een bepaalde software-build.

U kunt elk Unicode-teken in labels gebruiken, met uitzonde ring van `*`, `,`en `\`. Deze tekens zijn gereserveerd. Als u een gereserveerd teken wilt gebruiken, moet u dit met behulp van `\{Reserved Character}`verescapeen.

### <a name="query-key-values"></a>Sleutel waarden opvragen

Elke sleutel waarde wordt uniek aangeduid door de sleutel plus een label dat kan worden `null`. U kunt een app-configuratie Archief voor sleutel waarden opvragen door een patroon op te geven. In het app-configuratie archief worden alle sleutel waarden geretourneerd die overeenkomen met het patroon en de bijbehorende waarden en kenmerken. Gebruik de volgende sleutel patronen in REST API-aanroepen naar app-configuratie:

| Sleutel | |
|---|---|
| `key` wordt weggelaten of `key=*` | Komt overeen met alle sleutels |
| `key=abc` | Komt exact overeen met sleutel naam **ABC** |
| `key=abc*` | Komt overeen met sleutelnamen die beginnen met **abc** |
| `key=*abc` | Komt overeen met sleutelnamen die beginnen met **abc** |
| `key=*abc*` | Komt overeen met sleutelnamen die **abc** bevatten |
| `key=abc,xyz` | Komt overeen met de sleutel namen **ABC** of **xyz**, beperkt tot vijf csv's |

U kunt ook de volgende label patronen toevoegen:

| Label | |
|---|---|
| `label` wordt weggelaten of `label=*` | Komt overeen met een label, dat `null` bevat |
| `label=%00` | Komt overeen met `null` label |
| `label=1.0.0` | Komt exact overeen met label **1.0.0** |
| `label=1.0.*` | Komt overeen met labels die beginnen met **1.0.** |
| `label=*.0.0` | Komt overeen met labels die eindigen met **.0.0** |
| `label=*.0.*` | Komt overeen met labels die **.0.** bevatten |
| `label=%00,1.0.0` | Komt overeen met labels `null` of **1.0.0**, beperkt tot vijf csv's |

## <a name="values"></a>Waarden

Waarden die zijn toegewezen aan sleutels zijn ook Unicode-tekenreeksen. U kunt alle Unicode-tekens gebruiken voor waarden. Er is een optioneel, door de gebruiker gedefinieerd type inhoud gekoppeld aan elke waarde. Gebruik dit kenmerk om informatie, bijvoorbeeld een coderings schema, op te slaan over een waarde die uw toepassing helpt om deze correct te verwerken.

Configuratie gegevens die zijn opgeslagen in een app-configuratie archief, inclusief alle sleutels en waarden, worden versleuteld in rust en onderweg. App-configuratie is geen vervangings oplossing voor Azure Key Vault. Sla de toepassings geheimen hierin niet op.

## <a name="next-steps"></a>Volgende stappen

* [Moment opname van een bepaald tijdstip](./concept-point-time-snapshot.md)  
* [Functie beheer](./concept-feature-management.md)  
