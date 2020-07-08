---
title: Informatie over Azure-app configuratie sleutel-waarde Store
description: Begrijpen hoe configuratie gegevens worden opgeslagen in Azure-app configuratie.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: b8f8bda52be63a4176411855dd9ff9919e9e31f5
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85856679"
---
# <a name="keys-and-values"></a>Sleutels en waarden

Azure-app-configuratie slaat configuratie gegevens op als sleutel-waardeparen. Sleutel-waardeparen zijn een eenvoudige en flexibele representatie van toepassings instellingen die worden gebruikt door ontwikkel aars.

## <a name="keys"></a>Sleutels

Sleutels fungeren als id's voor sleutel-waardeparen en worden gebruikt voor het opslaan en ophalen van de bijbehorende waarden. Het is een veelvoorkomende procedure om sleutels te organiseren in een hiërarchische naam ruimte met behulp van een teken scheidings tekens, zoals `/` of `:` . Gebruik een Conventie die het meest geschikt is voor uw toepassing. Voor App Configuration zijn sleutels een op zichzelf staand object. Sleutels worden niet geparseerd om erachter te komen hoe hun namen worden gestructureerd of dat er regels worden afgedwongen.

Hier volgen twee voor beelden van sleutel namen die zijn gestructureerd in een hiërarchie:

* Op basis van componentservices

```aspx
        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint
```

* Op basis van implementatieregio's

```aspx
        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint
```

Het gebruik van configuratie gegevens in Application Frameworks kan specifieke naamgevings schema's voor sleutel waarden dicteren. In het voor beeld van de lente van een Java-Framework definieert `Environment` u bijvoorbeeld bronnen voor het leveren van instellingen aan een lente-toepassing.  Deze zijn para meters van variabelen die *toepassings naam* en- *profiel*bevatten. Sleutels voor lente-Cloud configuratie gegevens beginnen meestal met deze twee elementen gescheiden door een scheidings teken.

Sleutels die zijn opgeslagen in App Configuration zijn hoofdlettergevoelige tekenreeksen, op basis van Unicode. De sleutels *app1* en *app1* zijn uniek in een app-configuratie opslag. Houd dit in gedachten wanneer u configuratie-instellingen in een toepassing gebruikt omdat sommige Frameworks configuratie sleutels hoofdletter gevoelig verwerken. Het is niet raadzaam om cases te gebruiken om sleutels te onderscheiden.

U kunt elk Unicode-teken in sleutel namen gebruiken, behalve voor `*` , `,` , en `\` .  Als u een van deze gereserveerde tekens wilt gebruiken, sluit u deze met `\{Reserved Character}` . 

Er is een gecombineerde grootte limiet van 10 KB voor een sleutel/waarde-paar. Deze limiet omvat alle tekens in de sleutel, de bijbehorende waarde en alle bijbehorende optionele kenmerken. Binnen deze limiet kunt u veel niveaus voor sleutels gebruiken.

### <a name="design-key-namespaces"></a>Naam ruimten van ontwerp sleutel

Er zijn twee algemene methoden voor naamgeving van sleutels die worden gebruikt voor configuratiegegevens: plat en hiërarchisch. Deze methoden zijn vergelijkbaar met het standpunt van een toepassings gebruik, maar hiërarchische naamgeving biedt een aantal voor delen:

* Eenvoudiger te lezen. Scheidings tekens in een naam van een hiërarchische sleutel functioneren als spaties in een zin. Ze bieden ook natuurlijke onderbrekingen tussen woorden.
* Eenvoudiger te beheren. Een hiërarchie voor sleutelnamen vertegenwoordigt logische groepen van configuratiegegevens.
* Eenvoudiger te gebruiken. Het is eenvoudiger om een query te schrijven die het patroon volgt van sleutels in een hiërarchische structuur, om zo slechts een gedeelte van de configuratiegegevens op te halen. Daarnaast hebben veel nieuwere programmeer raamwerken systeem eigen ondersteuning voor hiërarchische configuratie gegevens, zodat uw toepassing gebruik kan maken van specifieke configuratie sets.

U kunt sleutels in App Configuration op allerlei manieren hiërarchisch indelen. U beschouwt dergelijke sleutels als [uri's](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Elke hiërarchische sleutel is een *bronpad* dat bestaat uit een of meer onderdelen die samen worden samengevoegd door scheidings tekens. Kies welk teken moet worden gebruikt als een scheidings punt op basis van de behoeften van uw toepassing, programmeer taal of Framework. Gebruik meerdere scheidings tekens voor verschillende sleutels in de app-configuratie.

### <a name="label-keys"></a>Label sleutels

Sleutel waarden in app-configuratie kunnen optioneel een label kenmerk hebben. Labels worden gebruikt om sleutel waarden te onderscheiden met dezelfde sleutel. Een Key *app1* met labels *A* en *B* vormt twee afzonderlijke sleutels in een app-configuratie opslag. Standaard heeft een sleutel waarde geen label. Als u expliciet wilt verwijzen naar een sleutel waarde zonder label, gebruikt u `\0` (URL-gecodeerd als `%00` ).

Label biedt een handige manier om varianten van een sleutel te maken. Het gebruik van labels is gebruikelijk om meerdere omgevingen op te geven voor dezelfde sleutel:

```aspx
    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production
```

### <a name="version-key-values"></a>Versie sleutel waarden

App-configuratie bevat niet automatisch sleutel waarden. Gebruik labels als een manier om meerdere versies van een sleutel waarde te maken. U kunt bijvoorbeeld een versie nummer van een toepassing of een Git-doorvoer-ID invoeren in labels om sleutel waarden te identificeren die zijn gekoppeld aan een bepaalde software-build.

U kunt elk Unicode-teken gebruiken in labels, behalve voor `*` , `,` , en `\` . Deze tekens zijn gereserveerd. Als u een gereserveerd teken wilt toevoegen, moet u dit met behulp van gebruiken `\{Reserved Character}` .

### <a name="query-key-values"></a>Sleutel waarden opvragen

Elke sleutel waarde wordt uniek aangeduid door de sleutel plus een label dat kan zijn `null` . U kunt een app-configuratie Archief voor sleutel waarden opvragen door een patroon op te geven. In het app-configuratie archief worden alle sleutel waarden geretourneerd die overeenkomen met het patroon en de bijbehorende waarden en kenmerken. Gebruik de volgende sleutel patronen in REST API-aanroepen naar app-configuratie:

| Sleutel | |
|---|---|
| `key` wordt weggelaten of `key=*` | Komt overeen met alle sleutels |
| `key=abc` | Komt exact overeen met sleutel naam **ABC** |
| `key=abc*` | Komt overeen met sleutelnamen die beginnen met **abc** |
| `key=abc,xyz` | Komt overeen met de sleutel namen **ABC** of **xyz**. Beperkt tot vijf Csv's |

U kunt ook de volgende label patronen toevoegen:

| Label | |
|---|---|
| `label` wordt weggelaten of `label=*` | Komt overeen met een label, inclusief`null` |
| `label=%00` | Komt overeen met `null` label |
| `label=1.0.0` | Komt exact overeen met label **1.0.0** |
| `label=1.0.*` | Komt overeen met labels die beginnen met **1.0.** |
| `label=%00,1.0.0` | Komt overeen met labels `null` of **1.0.0**, beperkt tot vijf csv's |

## <a name="values"></a>Waarden

Waarden die zijn toegewezen aan sleutels zijn ook Unicode-tekenreeksen. U kunt alle Unicode-tekens gebruiken voor waarden. Er kan optioneel een door de gebruiker gedefinieerd inhoudstype aan elke waarde worden gekoppeld. Gebruik dit kenmerk om informatie op te slaan over een waarde die uw toepassing helpt om deze correct te verwerken.

Configuratie gegevens die zijn opgeslagen in een app-configuratie archief, worden versleuteld in rust en onderweg. De sleutels zijn niet versleuteld op rest. App-configuratie is geen vervangings oplossing voor Azure Key Vault. Sla de toepassings geheimen hierin niet op.

## <a name="next-steps"></a>Volgende stappen

* [Point-in-time-snapshot](./concept-point-time-snapshot.md)  
* [Functiebeheer](./concept-feature-management.md)  
