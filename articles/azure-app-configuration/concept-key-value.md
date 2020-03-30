---
title: Informatie over azure app-sleutelwaardeopslag
description: Begrijpen hoe configuratiegegevens worden opgeslagen in Azure App-configuratie.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: 0b83a35d912c97ae25bc2d69d076e8eae8ca490f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523601"
---
# <a name="keys-and-values"></a>Toetsen en waarden

Azure App Configuration slaat configuratiegegevens op als sleutelwaardeparen. Sleutelparen zijn een eenvoudige en flexibele weergave van toepassingsinstellingen die door ontwikkelaars worden gebruikt.

## <a name="keys"></a>Sleutels

Sleutels dienen als id's voor sleutelwaardeparen en worden gebruikt om overeenkomstige waarden op te slaan en op te halen. Het is gebruikelijk om sleutels in een hiërarchische naamruimte te ordenen met behulp van een tekenscheidingsteken, zoals `/` of `:`. Gebruik een conventie die het meest geschikt is voor uw toepassing. Voor App Configuration zijn sleutels een op zichzelf staand object. Het onteert geen sleutels om erachter te komen hoe hun namen zijn gestructureerd of af te dwingen een regel op hen.

Hier volgen twee voorbeelden van belangrijke namen die in een hiërarchie zijn gestructureerd:

* Op basis van componentservices

        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint

* Op basis van implementatieregio's

        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint

Het gebruik van configuratiegegevens binnen toepassingskaders kan specifieke naamgevingsschema's voor belangrijke waarden dicteren. Het Spring Cloud-framework van `Environment` Java definieert bijvoorbeeld resources die instellingen leveren aan een Spring-toepassing.  Deze worden geparameteriseerd door variabelen die de naam en *het profiel*van *de toepassing* bevatten. Toetsen voor voorjaarscloudgerelateerde configuratiegegevens beginnen meestal met deze twee elementen die worden gescheiden door een scheidingsteken.

Sleutels die zijn opgeslagen in App Configuration zijn hoofdlettergevoelige tekenreeksen, op basis van Unicode. De toetsen *app1* en *App1* zijn verschillend in een App Configuration Store. Houd hier rekening mee wanneer u configuratie-instellingen in een toepassing gebruikt, omdat sommige frameworks configuratiesleutels ongevoelig behandelen. We raden het gebruik van de case af om sleutels te onderscheiden.

U elk unicode-teken in `*` `,`belangrijke `\`namen gebruiken, behalve voor, en.  Als u een van deze gereserveerde tekens `\{Reserved Character}`wilt opnemen, u eraan ontsnappen door. 

Er is een gecombineerde groottelimiet van 10 KB op een sleutelwaardepaar. Deze limiet omvat alle tekens in de sleutel, de waarde en alle bijbehorende optionele kenmerken. Binnen deze limiet kunt u veel niveaus voor sleutels gebruiken.

### <a name="design-key-namespaces"></a>Naamruimten voor sleuteltabellen ontwerpen

Er zijn twee algemene methoden voor naamgeving van sleutels die worden gebruikt voor configuratiegegevens: plat en hiërarchisch. Deze methoden zijn vergelijkbaar vanuit het oogpunt van toepassingsgebruik, maar hiërarchische naamgeving biedt een aantal voordelen:

* Eenvoudiger te lezen. Delimiters in een hiërarchische sleutelnaam functie als spaties in een zin. Ze bieden ook natuurlijke breuken tussen woorden.
* Eenvoudiger te beheren. Een hiërarchie voor sleutelnamen vertegenwoordigt logische groepen van configuratiegegevens.
* Eenvoudiger te gebruiken. Het is eenvoudiger om een query te schrijven die het patroon volgt van sleutels in een hiërarchische structuur, om zo slechts een gedeelte van de configuratiegegevens op te halen. Ook hebben veel nieuwere programmeerframeworks native ondersteuning voor hiërarchische configuratiegegevens, zodat uw toepassing gebruik kan maken van specifieke sets configuratie.

U kunt sleutels in App Configuration op allerlei manieren hiërarchisch indelen. Denk aan sleutels als [URI's.](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier) Elke hiërarchische sleutel is een *resourcepad* dat bestaat uit een of meer componenten die worden samengevoegd door scheidingstekens. Kies welk teken u als scheidingsteken wilt gebruiken op basis van wat uw toepassing, programmeertaal of framework nodig heeft. Gebruik meerdere scheidingstekens voor verschillende sleutels in app-configuratie.

### <a name="label-keys"></a>Labeltoetsen

Belangrijke waarden in app-configuratie kunnen optioneel een labelkenmerk hebben. Labels worden gebruikt om belangrijke waarden met dezelfde sleutel te onderscheiden. Een *sleutel-app1* met labels *A* en *B* vormt twee afzonderlijke sleutels in een app-configuratiearchief. Standaard heeft een sleutelwaarde geen label. Als u expliciet wilt verwijzen naar `\0` een sleutelwaarde `%00`zonder label, gebruikt u (URL gecodeerd als).

Label biedt een handige manier om varianten van een sleutel te maken. Een veelvoorkomend gebruik van labels is het opgeven van meerdere omgevingen voor dezelfde sleutel:

    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production

### <a name="version-key-values"></a>De belangrijkste waarden van de versie

App-configuratie geeft niet automatisch de belangrijkste waarden van de versie. Gebruik labels als een manier om meerdere versies van een sleutelwaarde te maken. U bijvoorbeeld een toepassingsversienummer of een Git commit-id invoeren in labels om belangrijke waarden te identificeren die zijn gekoppeld aan een bepaalde softwarebuild.

U elk unicode-teken `*`in `,`labels `\`gebruiken, behalve voor, en. Deze tekens zijn gereserveerd. Als u een gereserveerd teken wilt `\{Reserved Character}`opnemen, moet u eraan ontsnappen door .

### <a name="query-key-values"></a>Querysleutelwaarden

Elke sleutelwaarde wordt uniek geïdentificeerd door de sleutel `null`plus een label dat kan worden . U vraagt een app-configuratiearchief op naar belangrijke waarden door een patroon op te geven. De App Configuration Store retourneert alle belangrijke waarden die overeenkomen met het patroon en de bijbehorende waarden en kenmerken. Gebruik de volgende sleutelpatronen in REST API-aanroepen naar app-configuratie:

| Sleutel | |
|---|---|
| `key` wordt weggelaten of `key=*` | Komt overeen met alle sleutels |
| `key=abc` | Komt exact overeen met de sleutelnaam **abc** |
| `key=abc*` | Komt overeen met sleutelnamen die beginnen met **abc** |
| `key=abc,xyz` | Komt overeen met de belangrijkste namen **abc** of **xyz**. Beperkt tot vijf CvVs |

U ook de volgende labelpatronen opnemen:

| Label | |
|---|---|
| `label` wordt weggelaten of `label=*` | Komt overeen met elk label, waaronder`null` |
| `label=%00` | Label `null` Komt overeen |
| `label=1.0.0` | Komt exact overeen met label **1.0.0** |
| `label=1.0.*` | Komt overeen met labels die beginnen met **1.0.** |
| `label=%00,1.0.0` | Komt `null` overeen met labels of **1.0.0**, beperkt tot vijf CSV's |

## <a name="values"></a>Waarden

Waarden die zijn toegewezen aan sleutels zijn ook Unicode-tekenreeksen. U kunt alle Unicode-tekens gebruiken voor waarden. Er kan optioneel een door de gebruiker gedefinieerd inhoudstype aan elke waarde worden gekoppeld. Gebruik dit kenmerk om informatie op te slaan over een waarde die uw toepassing helpt deze correct te verwerken.

Configuratiegegevens die zijn opgeslagen in een app-configuratiearchief, worden in rust en onderweg versleuteld. De sleutels zijn niet versleuteld in rust. App-configuratie is geen vervangende oplossing voor Azure Key Vault. Bewaar er geen toepassingsgeheimen in.

## <a name="next-steps"></a>Volgende stappen

* [Point-in-time momentopname](./concept-point-time-snapshot.md)  
* [Functiebeheer](./concept-feature-management.md)  
