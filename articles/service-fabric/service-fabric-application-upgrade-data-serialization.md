---
title: 'Toepassingsupgrade: dataserialisatie'
description: Aanbevolen procedures voor het serialiseren van gegevens en hoe dit van invloed is op het uitvoeren van toepassingsupgrades.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: 7dc60c28b56982f82c1ac90db55ac752977ea2d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457496"
---
# <a name="how-data-serialization-affects-an-application-upgrade"></a>Hoe gegevensserialisatie van invloed is op een toepassingsupgrade
In een [upgrade met rollende toepassingen](service-fabric-application-upgrade.md)wordt de upgrade toegepast op een subset van knooppunten, één upgradedomein tegelijk. Tijdens dit proces staan sommige upgradedomeinen op de nieuwere versie van uw toepassing en sommige upgradedomeinen bevinden zich op de oudere versie van uw toepassing. Tijdens de implementatie moet de nieuwe versie van uw toepassing de oude versie van uw gegevens kunnen lezen en de oude versie van uw toepassing moet de nieuwe versie van uw gegevens kunnen lezen. Als de gegevensindeling niet vooruit- en achterwaarts compatibel is, kan de upgrade mislukken of erger nog, gegevens kunnen verloren gaan of beschadigd raken. In dit artikel wordt besproken wat uw gegevensindeling is en worden best practices aanbevolen om ervoor te zorgen dat uw gegevens voorwaarts en achterwaarts compatibel zijn.

## <a name="what-makes-up-your-data-format"></a>Wat maakt uw gegevensformaat?
In Azure Service Fabric zijn de gegevens die worden gehandhaafd en gerepliceerd afkomstig uit uw C#-klassen. Voor toepassingen die [betrouwbare verzamelingen](service-fabric-reliable-services-reliable-collections.md)gebruiken, zijn die gegevens de objecten in de betrouwbare woordenboeken en wachtrijen. Voor toepassingen die gebruik maken [van Betrouwbare Actoren](service-fabric-reliable-actors-introduction.md), dat is de steunstatus voor de acteur. Deze C#-klassen moeten serializable zijn om te worden gehandhaafd en gerepliceerd. Daarom wordt de gegevensindeling gedefinieerd door de velden en eigenschappen die zijn geserialiseerd, evenals hoe ze worden geserialiseerd. In een `IReliableDictionary<int, MyClass>` van de gegevens zijn `int` de gegevens `MyClass`bijvoorbeeld geserialiseerd en geserialiseerd .

### <a name="code-changes-that-result-in-a-data-format-change"></a>Codewijzigingen die resulteren in een wijziging van de gegevensindeling
Aangezien de gegevensindeling wordt bepaald door C#-klassen, kunnen wijzigingen in de klassen een wijziging van de gegevensindeling veroorzaken. Er moet voor worden gezorgd dat een rolling upgrade de wijziging van de gegevensindeling kan verwerken. Voorbeelden die wijzigingen in de gegevensindeling kunnen veroorzaken:

* Velden of eigenschappen toevoegen of verwijderen
* De naam van velden of eigenschappen wijzigen
* De typen velden of eigenschappen wijzigen
* De klassenaam of naamruimte wijzigen

### <a name="data-contract-as-the-default-serializer"></a>Gegevenscontract als standaardserialisator
De serialisator is over het algemeen verantwoordelijk voor het lezen van de gegevens en deserialiseren van de gegevens in de huidige versie, zelfs als de gegevens in een oudere of *nieuwere* versie. De standaard serializer is de [Data Contract serializer](https://msdn.microsoft.com/library/ms733127.aspx), die goed gedefinieerde versieregels heeft. Met betrouwbare collecties kan de serialisator worden overschreven, maar betrouwbare actoren momenteel niet. De data serializer speelt een belangrijke rol bij het mogelijk maken van rolling upgrades. De Data Contract serializer is de serializer die we aanbevelen voor Service Fabric-toepassingen.

## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Hoe de gegevensindeling van invloed is op een rolling upgrade
Tijdens een rolling upgrade zijn er twee belangrijke scenario's waarin de serialisator een oudere of *nieuwere* versie van uw gegevens kan tegenkomen:

1. Nadat een knooppunt is bijgewerkt en een back-up wordt gestart, laadt de nieuwe serialisator de gegevens die door de oude versie op de schijf zijn blijven staan.
2. Tijdens de rolling upgrade bevat het cluster een mix van de oude en nieuwe versies van uw code. Aangezien replica's in verschillende upgradedomeinen kunnen worden geplaatst en replica's gegevens naar elkaar verzenden, kan de nieuwe en/of oude versie van uw gegevens worden aangetroffen door de nieuwe en/of oude versie van uw serialisator.

> [!NOTE]
> De "nieuwe versie" en "oude versie" verwijzen hier naar de versie van uw code die wordt uitgevoerd. De "nieuwe serializer" verwijst naar de serializer code die wordt uitgevoerd in de nieuwe versie van uw toepassing. De "nieuwe gegevens" verwijst naar de geserialiseerde C# klasse van de nieuwe versie van uw toepassing.
> 
> 

De twee versies van code en gegevensformaat moeten zowel voorwaarts als achterwaarts compatibel zijn. Als ze niet compatibel zijn, kan de rolling upgrade mislukken of gegevens kunnen verloren gaan. De rolling upgrade kan mislukken omdat de code of serializer uitzonderingen of een fout kan gooien wanneer deze de andere versie tegenkomt. Gegevens kunnen verloren gaan als er bijvoorbeeld een nieuwe eigenschap is toegevoegd, maar de oude serialisator deze tijdens deserialisatie verwijdert.

Data Contract is de aanbevolen oplossing om ervoor te zorgen dat uw gegevens compatibel zijn. Er zijn goed gedefinieerde versieregels voor het toevoegen, verwijderen en wijzigen van velden. Het heeft ook ondersteuning voor het omgaan met onbekende velden, aansluiten in de serialisatie en deserialisatie proces, en omgaan met klasse erfenis. Zie [Gegevenscontract gebruiken](https://msdn.microsoft.com/library/ms733127.aspx)voor meer informatie .

## <a name="next-steps"></a>Volgende stappen
[Upgraden van uw toepassing Met Visual Studio](service-fabric-application-upgrade-tutorial.md) u een applicatie-upgrade doorlopen met Visual Studio.

[Het upgraden van uw toepassing met Powershell](service-fabric-application-upgrade-tutorial-powershell.md) leidt u door een applicatie-upgrade met PowerShell.

Bepaal hoe uw toepassing wordt bijgewerkt met [upgradeparameters.](service-fabric-application-upgrade-parameters.md)

Meer informatie over het gebruik van geavanceerde functionaliteit tijdens het upgraden van uw toepassing door te verwijzen naar [geavanceerde onderwerpen.](service-fabric-application-upgrade-advanced.md)

Los veelvoorkomende problemen op in toepassingsupgrades door te verwijzen naar de stappen in [het oplossen van toepassingsupgrades.](service-fabric-application-upgrade-troubleshooting.md)

