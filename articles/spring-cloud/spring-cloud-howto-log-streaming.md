---
title: Azure lente-logboeken voor Cloud-apps in realtime streamen
description: Logboek streaming gebruiken om toepassings logboeken onmiddellijk weer te geven
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.openlocfilehash: fa2e7af51ff681da0bfdac928cc08bf75126a3b8
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156417"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>Azure lente-logboeken voor Cloud-apps in realtime streamen
Met Azure lente-Cloud kunt u logboek streaming in azure CLI inschakelen voor het verkrijgen van real-time toepassings console logboeken voor het oplossen van problemen. U kunt ook [Logboeken en metrische gegevens analyseren met Diagnostische instellingen](./diagnostic-services.md).

## <a name="prerequisites"></a>Vereisten

* Installeer de [Azure cli-extensie](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#install-the-azure-cli-extension) voor de lente-Cloud, mini maal versie 0.2.0.
* Een exemplaar van **Azure lente Cloud** met een actieve toepassing, bijvoorbeeld [lente-Cloud-app](./spring-cloud-quickstart-launch-app-cli.md).

## <a name="use-cli-to-tail-logs"></a>CLI gebruiken om logboeken af te staart

Als u de naam van de resource groep en het service-exemplaar herhaaldelijk wilt opgeven, stelt u de standaard naam van de resource groep en de naam van het cluster in.
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
In de volgende voor beelden wordt de resource groep en de service naam wegge laten in de opdrachten.

### <a name="tail-log-for-app-with-single-instance"></a>Staart logboek voor app met één exemplaar
Als een app met de naam auth-service slechts één exemplaar heeft, kunt u het logboek van het app-exemplaar weer geven met de volgende opdracht:
```
az spring-cloud app log tail -n auth-service
```
Hiermee worden logboeken geretourneerd:
```
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>Staart logboek voor app met meerdere exemplaren
Als er meerdere exemplaren bestaan voor de app met de naam `auth-service`, kunt u het exemplaar logboek weer geven met behulp van de `-i/--instance` optie. U kunt bijvoorbeeld het logboek van een exemplaar van een app streamen door de naam van de app en de naam van het exemplaar op te geven:

```
az spring-cloud app log tail -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```
U kunt ook de app-exemplaren van de Azure Portal ophalen. 
1. Navigeer naar de resource groep en selecteer uw Azure lente Cloud-exemplaar.
1. Selecteer in het overzicht van de Azure veer Cloud-instantie de optie **apps** in het navigatie deel venster aan de linkerkant.
1. Selecteer uw app en klik vervolgens op **app-exemplaren** in het navigatie deel venster links. 
1. App-exemplaren worden weer gegeven.

### <a name="continuously-stream-new-logs"></a>Doorlopend streamen nieuwe logboeken
`az spring-cloud ap log tail` worden standaard alleen bestaande logboeken afgedrukt die naar de app-console worden gestreamd en vervolgens afgesloten. Als u nieuwe logboeken wilt streamen, voegt u-f (--follow):  

```
az spring-cloud app log tail -n auth-service -f
``` 
Controleren of alle opties voor logboek registratie worden ondersteund:
``` 
az spring-cloud app log tail -h 
```

## <a name="next-steps"></a>Volgende stappen

* [Logboeken en metrische gegevens analyseren met Diagnostische instellingen](./diagnostic-services.md)

 





