---
title: Informatie over appstatus in Azure Spring Cloud
description: Meer informatie over de app-status Categorieën in azure lente Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: brendm
ms.openlocfilehash: 70a9e6392e21422d7513197fbf7a1a75e1f6ab8f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82569002"
---
# <a name="understanding-app-status-in-azure-spring-cloud"></a>Informatie over appstatus in Azure Spring Cloud

De Azure veer Cloud-gebruikers interface levert informatie over de status van actieve toepassingen.  Er is een optie voor **apps** voor elke resource groep in een abonnement dat algemene status van toepassings typen weergeeft.  Voor elk toepassings type worden **toepassings exemplaren**weer gegeven.

## <a name="apps-status"></a>Status van apps
Als u de algemene status van een toepassings type wilt weer geven, selecteert u **apps** in het navigatie deel venster links van een resource groep. Met het resultaat wordt de status van de geïmplementeerde app weer gegeven:

* Bij de **inrichtings status** wordt de inrichtings status van de implementatie weer gegeven
* **Met het actieve exemplaar** wordt weer gegeven hoeveel app-exemplaren worden uitgevoerd/hoeveel app-exemplaren gewenst zijn. Als de app moet worden gestopt, wordt de kolom *gestopt*weer gegeven.
* In het **geregistreerde exemplaar** wordt weer gegeven hoeveel app-exemplaren zijn geregistreerd bij Eureka/hoeveel app-exemplaar gewenst zijn. Als de app moet worden gestopt, wordt de kolom *gestopt*weer gegeven.


 ![Status van apps](media/spring-cloud-concept-app-status/apps-ui-status.png)

**De implementatie status wordt gerapporteerd als een van de volgende waarden:**

| Enum | Definitie |
|:--:|:----------------:|
| In uitvoering | De implementatie moet worden uitgevoerd. |
| Gestopt | De implementatie moet worden gestopt. |

**De inrichtings status is alleen toegankelijk vanuit de CLI.  De waarde wordt gerapporteerd als een van de volgende waarden:**

| Enum | Definitie |
|:--:|:----------------:|
| Maken | De resource wordt gemaakt. |
| Bijwerken | De resource wordt bijgewerkt. |
| Geslaagd | Er zijn resources opgegeven en het binaire bestand wordt geïmplementeerd. |
| Mislukt | Het doel van het *succes* is niet gerealiseerd. |
| Verwijderen | De resource wordt verwijderd. Dit voor komt dat de bewerking wordt voor komen en de resource is niet beschikbaar in deze status. |

## <a name="app-instances-status"></a>Status app-exemplaren

Als u de status van een specifiek exemplaar van een geïmplementeerde app wilt weer geven, klikt u op de **naam** van de app in de gebruikers interface van **apps** . De resultaten worden weer gegeven:
* **Status**: of de instantie wordt uitgevoerd of de status ervan
* **DiscoveryStatus**: de geregistreerde status van het app-exemplaar in de Eureka-server

 ![Status app-exemplaren](media/spring-cloud-concept-app-status/apps-ui-instance-status.png)

**De status van het exemplaar wordt gerapporteerd als een van de volgende waarden:**

| Enum | Definitie |
|:--:|:----------------:|
| Starten | Het binaire bestand is geïmplementeerd voor het opgegeven exemplaar. Het starten van het exemplaar van het jar-bestand kan mislukken omdat jar niet goed kan worden uitgevoerd. |
| In uitvoering | Het exemplaar werkt. |
| Mislukt | Het app-exemplaar kan het binaire bestand van de gebruiker niet starten na verschillende pogingen. |
| Afsluit | Het app-exemplaar wordt afgesloten. |

**De detectie status van het exemplaar wordt gerapporteerd als een van de volgende waarden:**

| Enum | Definitie |
|:--:|:----------------:|
| GEVOUWEN | Het app-exemplaar is geregistreerd voor Eureka en klaar om verkeer te ontvangen |
| OUT_OF_SERVICE | Het app-exemplaar is geregistreerd voor Eureka en kan verkeer ontvangen. maar wordt voor het bewuste verkeer afgesloten. |
| OPGESOMD | Het app-exemplaar is niet geregistreerd bij Eureka of is geregistreerd, maar kan geen verkeer ontvangen. |


## <a name="see-also"></a>Zie ook
* [Een Java Spring-toepassing voorbereiden voor implementatie in Azure Spring Cloud](spring-cloud-tutorial-prepare-app-deployment.md)