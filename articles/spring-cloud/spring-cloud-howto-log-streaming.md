---
title: Azure Spring Cloud-applogboeken in realtime streamen
description: Logboekstreaming gebruiken om toepassingslogboeken direct weer te geven
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.openlocfilehash: fc208a3542528fb4554a365a02e13c2da3055cf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192197"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>Azure Spring Cloud-applogboeken in realtime streamen
Azure Spring Cloud stelt logboekstreaming in Azure CLI in staat om realtime logboeken van toepassingsconsoles te krijgen voor het oplossen van problemen. U ook [logboeken en statistieken analyseren met diagnostische instellingen.](./diagnostic-services.md)

## <a name="prerequisites"></a>Vereisten

* Installeer [Azure CLI-extensie](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#install-the-azure-cli-extension) voor Spring Cloud, minimaal versie 0.2.0 .
* Een exemplaar van **Azure Spring Cloud** met een lopende toepassing, bijvoorbeeld De [Cloud-app voor de lente](./spring-cloud-quickstart-launch-app-cli.md).

> [!NOTE]
>  De ASC CLI-extensie wordt bijgewerkt van versie 0.2.0 naar 0.2.1. Deze wijziging is van invloed op de `az spring-cloud app log tail`syntaxis van `az spring-cloud app logs`de opdracht voor het streamen van logboeken: , die wordt vervangen door: . De opdracht: `az spring-cloud app log tail` zal worden afgeschaft in een toekomstige release. Als u versie 0.2.0 hebt gebruikt, u upgraden naar 0.2.1. Verwijder eerst de oude versie `az extension remove -n spring-cloud`met de opdracht: .  Installeer vervolgens 0.2.1 op `az extension add -n spring-cloud`de opdracht: .

## <a name="use-cli-to-tail-logs"></a>CLI gebruiken om logboeken te volgen

Als u wilt voorkomen dat herhaaldelijk de naam van uw resourcegroep en service-instantie wordt opgegeven, stelt u de naam van de standaardbrongroep en de naam van het cluster in.
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
In volgende voorbeelden worden de resourcegroep en de servicenaam weggelaten in de opdrachten.

### <a name="tail-log-for-app-with-single-instance"></a>Staartlogboek voor app met één exemplaar
Als een app met de naam auth-service slechts één exemplaar heeft, u het logboek van de app-instantie met de volgende opdracht bekijken:
```
az spring-cloud app logs -n auth-service
```
Hiermee worden logboeken retourneren:
```
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>Staartlogboek voor app met meerdere exemplaren
Als er meerdere exemplaren `auth-service`zijn voor de benoemde app, `-i/--instance` u het instantielogboek bekijken met behulp van de optie. 

Eerst u de namen van de app-instantie met volgende opdracht krijgen.

```
az spring-cloud app show -n auth-service --query properties.activeDeployment.properties.instances -o table
```
Met resultaten:

```
Name                                         Status    DiscoveryStatus
-------------------------------------------  --------  -----------------
auth-service-default-12-75cc4577fc-pw7hb  Running   UP
auth-service-default-12-75cc4577fc-8nt4m  Running   UP
auth-service-default-12-75cc4577fc-n25mh  Running   UP
``` 
Vervolgens u logboeken van een `-i/--instance` app-exemplaar streamen met de optie:

```
az spring-cloud app logs -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

U ook details van app-exemplaren krijgen van de Azure-portal.  Nadat **u Apps** hebt geselecteerd in het linkernavigatiedeelvenster van uw Azure Spring Cloud-service, selecteert u **App-exemplaren**.

### <a name="continuously-stream-new-logs"></a>Voortdurend nieuwe logboeken streamen
`az spring-cloud ap log tail` Hiermee worden standaard alleen bestaande logboeken afgedrukt die naar de app-console worden gestreamd en worden ze vervolgens afgesloten. Als u nieuwe logboeken wilt streamen, voegt u -f (--follow) toe:  

```
az spring-cloud app logs -n auth-service -f
``` 
Ga als u alle ondersteunde logboekregistratieopties controleren:
``` 
az spring-cloud app logs -h 
```

## <a name="next-steps"></a>Volgende stappen

* [Logboeken en statistieken analyseren met diagnostische instellingen](./diagnostic-services.md)

 





