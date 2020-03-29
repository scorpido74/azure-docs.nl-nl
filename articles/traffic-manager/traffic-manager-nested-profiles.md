---
title: Geneste trafficmanagerprofielen in Azure
titleSuffix: Azure Traffic Manager
description: In dit artikel wordt de functie 'Geneste profielen' van Azure Traffic Manager uitgelegd
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2018
ms.author: rohink
ms.openlocfilehash: 282099cb274c1ea872a0df9c2753a939ef31421f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938578"
---
# <a name="nested-traffic-manager-profiles"></a>Geneste Traffic Manager-profielen

Traffic Manager bevat een reeks verkeersrouteringsmethoden waarmee u bepalen hoe Traffic Manager kiest welk eindpunt verkeer van elke eindgebruiker moet ontvangen. Zie [Verkeersbeheermethoden voor](traffic-manager-routing-methods.md)meer informatie .

Elk Traffic Manager-profiel geeft één verkeersrouteringsmethode op. Er zijn echter scenario's die een geavanceerdere verkeersroutering vereisen dan de routebepaling die wordt geboden door één Traffic Manager-profiel. U Traffic Manager-profielen nesten om de voordelen van meer dan één verkeersrouteringsmethode te combineren. Met geneste profielen u het standaard gedrag van Traffic Manager overschrijven om grotere en complexere toepassingsimplementaties te ondersteunen.

In de volgende voorbeelden ziet u hoe u geneste Traffic Manager-profielen in verschillende scenario's gebruiken.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Voorbeeld 1: 'Prestaties' en 'Gewogen' verkeersroutering combineren

Stel dat u een toepassing hebt geïmplementeerd in de volgende Azure-regio's: West-VS, West-Europa en Oost-Azië. U gebruikt de verkeersrouteringsmethode 'Prestaties' van Traffic Manager om verkeer te distribueren naar de regio die het dichtst bij de gebruiker staat.

![Profiel van één verkeersmanager][4]

Stel dat u een update van uw service wilt testen voordat u deze op grotere schaal uitrolt. U wilt de 'gewogen' verkeersrouteringsmethode gebruiken om een klein percentage verkeer naar uw testimplementatie te leiden. U stelt de testimplementatie in naast de bestaande productie-implementatie in West-Europa.

U zowel 'Gewogen' als 'Prestatieverkeersroutering' niet combineren in één profiel. Om dit scenario te ondersteunen, maakt u een Traffic Manager-profiel met behulp van de twee West-Europese eindpunten en de 'Gewogen' verkeersrouteringsmethode. Vervolgens voegt u dit 'onderliggende' profiel toe als eindpunt aan het 'bovenliggende' profiel. Het bovenliggende profiel gebruikt nog steeds de methode Voor het genereren van verkeer uitvoeren en bevat de andere globale implementaties als eindpunten.

In het volgende diagram wordt het volgende voorbeeld geïllustreerd:

![Geneste Traffic Manager-profielen][2]

In deze configuratie verdeelt verkeer dat via het bovenliggende profiel wordt geleid, het verkeer normaal over regio's. Binnen West-Europa verdeelt het geneste profiel het verkeer naar de productie- en testeindpunten volgens de toegewezen gewichten.

Wanneer het bovenliggende profiel de verkeersrouteringsmethode 'Prestaties' gebruikt, moet elk eindpunt een locatie toegewezen krijgen. De locatie wordt toegewezen wanneer u het eindpunt configureert. Kies het Azure-gebied dat het dichtst bij uw implementatie ligt. De Azure-regio's zijn de locatiewaarden die worden ondersteund door de InternetLatency Table. Zie [Traffic Manager 'Performance'-routeringsmethode voor](traffic-manager-routing-methods.md#performance)meer informatie .

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Voorbeeld 2: Eindpuntbewaking in geneste profielen

Traffic Manager bewaakt actief de status van elk serviceeindpunt. Als een eindpunt niet in orde is, leidt Traffic Manager gebruikers naar alternatieve eindpunten om de beschikbaarheid van uw service te behouden. Dit gedrag voor endpointbewaking en failover is van toepassing op alle methoden voor het routeren van verkeer. Zie [Eindpuntbewaking van verkeersbeheer voor](traffic-manager-monitoring.md)meer informatie . Endpoint monitoring werkt anders voor geneste profielen. Met geneste profielen voert het bovenliggende profiel niet rechtstreeks gezondheidscontroles uit op het kind. In plaats daarvan wordt de status van de eindpunten van het kindprofiel gebruikt om de algehele status van het onderliggende profiel te berekenen. Deze statusgegevens worden gepropageerd in de geneste profielhiërarchie. Het bovenliggende profiel gebruikt deze geaggregeerde status om te bepalen of verkeer naar het onderliggende profiel moet worden doorgestroomd. Zie de [veelgestelde vragen](traffic-manager-FAQs.md#traffic-manager-nested-profiles) over de gezondheid van geneste profielen.

Terug naar het vorige voorbeeld, stel dat de productie-inzet in West-Europa mislukt. Standaard leidt het 'onderliggende' profiel al het verkeer naar de testimplementatie. Als de testimplementatie ook mislukt, bepaalt het bovenliggende profiel dat het onderliggende profiel geen verkeer mag ontvangen omdat alle onderliggende eindpunten niet in orde zijn. Vervolgens verdeelt het bovenliggende profiel verkeer naar de andere regio's.

![Mislukte geneste profielfailover (standaardgedrag)][3]

Je zou blij kunnen zijn met deze regeling. Of u zou bezorgd kunnen zijn dat al verkeer voor West-Europa nu naar de testimplementatie in plaats van een beperkt subsetverkeer gaat. Ongeacht de gezondheid van de testimplementatie, wilt u niet naar de andere regio's wanneer de productie-implementatie in West-Europa mislukt. Als u deze failover wilt inschakelen, u de parameter 'MinChildEndpoints' opgeven bij het configureren van het onderliggende profiel als eindpunt in het bovenliggende profiel. De parameter bepaalt het minimumaantal beschikbare eindpunten in het onderliggende profiel. De standaardwaarde is '1'. Voor dit scenario stelt u de waarde MinChildEndpoints in op 2. Onder deze drempel wordt het bovenliggende profiel als beschikbaar beschouwt en wordt het verkeer naar de andere eindpunten doorgestroomd.

De volgende figuur illustreert deze configuratie:

![Geneste profielfailover met 'MinChildEndpoints' = 2][4]

> [!NOTE]
> De routeringsmethode 'Prioriteit' verdeelt al het verkeer naar één eindpunt. Er is dus weinig zin in een MinChildEndpoints-instelling anders dan '1' voor een kindprofiel.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Voorbeeld 3: Prioriteit geven aan failoverregio's in 'Prestatie'-verkeersroutering

Het standaardgedrag voor de verkeersrouteringsmethode 'Prestaties' is wanneer u eindpunten op verschillende geografische locaties hebt, worden de eindgebruikers naar het 'dichtstbijzijnde' eindpunt geleid in termen van de laagste netwerklatentie.

Stel dat u de voorkeur geeft aan de failover van West-Europa naar West-VS en alleen het verkeer naar andere regio's leidt wanneer beide eindpunten niet beschikbaar zijn. U deze oplossing maken met behulp van een onderliggend profiel met de verkeersrouteringsmethode 'Prioriteit'.

!['Performance' verkeersroutering met preferentiële failover][6]

Aangezien het Eindpunt van West-Europa een hogere prioriteit heeft dan het West-Amerikaanse eindpunt, wordt al het verkeer naar het eindpunt van West-Europa gestuurd wanneer beide eindpunten online zijn. Als West-Europa faalt, wordt het verkeer naar West-VS geleid. Met het geneste profiel wordt het verkeer alleen naar Oost-Azië geleid wanneer zowel West-Europa als West-VS falen.

U dit patroon voor alle regio's herhalen. Vervang alle drie eindpunten in het bovenliggende profiel door drie onderliggende profielen, die elk een geprioritteerde failover-reeks bieden.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Voorbeeld 4: Verkeersroutering 'Prestaties' tussen meerdere eindpunten in dezelfde regio beheren

Stel dat de verkeersrouteringsmethode 'Prestaties' wordt gebruikt in een profiel dat meer dan één eindpunt in een bepaalde regio heeft. Het verkeer dat naar die regio wordt geleid, wordt standaard gelijkmatig verdeeld over alle beschikbare eindpunten in die regio.

!['Performance' verkeersroutering in de regio verkeersverdeling (standaardgedrag)][7]

In plaats van meerdere eindpunten toe te voegen in West-Europa, zijn deze eindpunten ingesloten in een apart onderliggend profiel. Het onderliggende profiel wordt aan de ouder toegevoegd als het enige eindpunt in West-Europa. De instellingen op het onderliggende profiel kunnen de verkeersverdeling met West-Europa regelen door prioriteitsafhankelijke of gewogen verkeersroutering binnen die regio mogelijk te maken.

!['Performance' verkeersroutering met aangepaste verkeersverdeling in de regio][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Voorbeeld 5: Instellingen voor bewaking per eindpunt

Stel dat u Traffic Manager gebruikt om verkeer soepel te migreren van een verouderde on-premises website naar een nieuwe cloudversie die wordt gehost in Azure. Voor de verouderde site wilt u de startpagina URI gebruiken om de status van de site te controleren. Maar voor de nieuwe cloudversie implementeert u een aangepaste bewakingspagina (pad '/monitor.aspx') met extra controles.

![Eindpuntbewaking van Traffic Manager (standaardgedrag)][9]

De bewakingsinstellingen in een Traffic Manager-profiel zijn van toepassing op alle eindpunten binnen één profiel. Met geneste profielen gebruikt u een ander onderliggend profiel per site om verschillende bewakingsinstellingen te definiëren.

![Eindpuntbewaking van Traffic Manager met per-endpoint-instellingen][10]

## <a name="faqs"></a>Veelgestelde vragen

* [Hoe configureer ik geneste profielen?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#traffic-manager-endpoint-monitoring)

* [Hoeveel nestlagen ondersteunt Traffic Manger?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-layers-of-nesting-does-traffic-manger-support)

* [Kan ik andere eindpunttypen mengen met geneste onderliggende profielen in hetzelfde Traffic Manager-profiel?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile)

* [Hoe is het factureringsmodel van toepassing op geneste profielen?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-the-billing-model-apply-for-nested-profiles)

* [Is er een prestatie-impact voor geneste profielen?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-there-a-performance-impact-for-nested-profiles)

* [Hoe berekent Traffic Manager de status van een genest eindpunt in een bovenliggend profiel?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Traffic Manager-profielen](traffic-manager-overview.md)

Meer informatie over het [maken van een Traffic Manager-profiel](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png
