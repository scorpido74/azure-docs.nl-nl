---
title: Resource logboeken verzamelen & analyseren
description: Registreer en analyseer bron logboek gebeurtenissen voor Azure Container Registry zoals verificatie, installatie kopie push en installatie kopie ophalen.
ms.topic: article
ms.date: 01/03/2020
ms.openlocfilehash: 00f9468721126bd166051df47cec1596356e9b54
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79409640"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>Azure Container Registry logboeken voor diagnostische evaluatie en controle

In dit artikel wordt uitgelegd hoe u logboek gegevens kunt verzamelen voor een Azure container Registry met behulp van de functies van [Azure monitor](../azure-monitor/overview.md). Azure Monitor verzamelt [bron logboeken](../azure-monitor/platform/platform-logs-overview.md) (voorheen *Diagnostische logboeken*genoemd) voor door gebruikers gestuurde gebeurtenissen in het REGI ster. Verzamelen en gebruiken van deze gegevens om te voldoen aan de behoeften, zoals:

* Register verificatie gebeurtenissen controleren om te zorgen voor beveiliging en naleving 

* Geef een compleet activiteiten spoor op register artefacten, zoals pull-en pull-gebeurtenissen, zodat u operationele problemen met uw REGI ster kunt vaststellen 

Het verzamelen van bron logboek gegevens met behulp van Azure Monitor kan extra kosten in rekening worden gebracht. Zie [Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/). 

## <a name="repository-events"></a>Opslagplaats gebeurtenissen

De volgende gebeurtenissen op opslagplaats niveau voor installatie kopieën en andere artefacten worden momenteel geregistreerd:

* **Push gebeurtenissen**
* **Pull-gebeurtenissen**
* **Tag-gebeurtenissen**
* **Gebeurtenissen verwijderen** (waaronder gebeurtenissen voor het verwijderen van de opslag plaats)

Gebeurtenissen op opslagplaats niveau die momenteel niet zijn geregistreerd: gebeurtenissen opschonen.

## <a name="registry-resource-logs"></a>Register bron logboeken

Bron logboeken bevatten gegevens die worden verzonden door Azure-resources die hun interne bewerking beschrijven. Voor een Azure container Registry bevatten de logboeken verificatie en gebeurtenissen op opslagplaats niveau die in de volgende tabellen zijn opgeslagen. 

* **ContainerRegistryLoginEvents** -register verificatie gebeurtenissen en status, met inbegrip van de binnenkomende identiteit en het IP-adres
* **ContainerRegistryRepositoryEvents** : bewerkingen zoals pushen en pullen voor installatie kopieën en andere artefacten in register opslagplaatsen
* **Metrische AzureMetrics** - in[container register](../azure-monitor/platform/metrics-supported.md#microsoftcontainerregistryregistries) , zoals geaggregeerde push-en pull-aantallen.

Voor bewerkingen zijn logboek gegevens inclusief:
  * Status geslaagd of mislukt
  * Begin-en eind tijd tempels

Naast resource Logboeken biedt Azure een [activiteiten logboek](../azure-monitor/platform/platform-logs-overview.md), een record met Azure-beheer gebeurtenissen op abonnements niveau, zoals het maken of verwijderen van een container register.

## <a name="enable-collection-of-resource-logs"></a>Verzamelen van resource logboeken inschakelen

Het verzamelen van bron logboeken voor een container register is standaard niet ingeschakeld. Schakel de diagnostische instellingen expliciet in voor elk REGI ster dat u wilt bewaken. Zie [Diagnostische instelling maken voor het verzamelen van platform logboeken en metrische gegevens in azure](../azure-monitor/platform/diagnostic-settings.md)voor opties voor het inschakelen van diagnostische instellingen.

Als u bijvoorbeeld Logboeken en metrische gegevens voor een container register in bijna realtime in Azure Monitor wilt weer geven, verzamelt u de bron Logboeken in een Log Analytics-werk ruimte. Als u deze diagnostische instelling wilt inschakelen met behulp van de Azure Portal:

1. Als u nog geen werk ruimte hebt, maakt u een werk ruimte met behulp van de [Azure Portal](../azure-monitor/learn/quick-create-workspace.md). Om latentie bij het verzamelen van gegevens te minimaliseren, moet u ervoor zorgen dat de werk ruimte zich in **dezelfde regio** bevindt als uw container register.
1. Selecteer in de portal het REGI ster en selecteer **bewaking > Diagnostische instellingen > diagnostische instelling toevoegen**.
1. Voer een naam in voor de instelling en selecteer **verzenden naar log Analytics**.
1. Selecteer de werk ruimte voor de diagnostische logboeken van het REGI ster.
1. Selecteer de logboek gegevens die u wilt verzamelen en klik op **Opslaan**.

De volgende afbeelding toont het maken van een diagnostische instelling voor een REGI ster met behulp van de portal.

![Diagnostische instellingen inschakelen](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> Verzamel alleen de gegevens die u nodig hebt, berekenings kosten en uw bewakings behoeften. Als u bijvoorbeeld alleen verificatie gebeurtenissen wilt controleren, selecteert u alleen het **ContainerRegistryLoginEvents** -logboek. 

## <a name="view-data-in-azure-monitor"></a>Gegevens in Azure Monitor weer geven

Nadat u het verzamelen van Diagnostische logboeken in Log Analytics hebt ingeschakeld, kan het enkele minuten duren voordat gegevens worden weer gegeven in Azure Monitor. Als u de gegevens in de portal wilt weer geven, selecteert u het REGI ster en selecteert u **> logboeken controleren**. Selecteer een van de tabellen die gegevens voor het REGI ster bevatten. 

Query's uitvoeren om de gegevens weer te geven. Er worden verschillende voorbeeld query's gegeven of u kunt uw eigen query uitvoeren. Met de volgende query worden bijvoorbeeld de meest recente 24 uur aan gegevens opgehaald uit de **ContainerRegistryRepositoryEvents** -tabel:

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

De volgende afbeelding toont voorbeeld uitvoer:

![Query uitvoeren op logboekgegevens](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

Zie [aan de slag met Azure Monitor Log Analytics](../azure-monitor/log-query/get-started-portal.md)of probeer de log Analytics- [demo omgeving](https://portal.loganalytics.io/demo)voor een zelf studie over het gebruik van log Analytics in het Azure Portal. 

Zie [overzicht van logboek query's in azure monitor](../azure-monitor/log-query/log-query-overview.md)voor meer informatie over logboek query's.

### <a name="additional-query-examples"></a>Aanvullende query voorbeelden

#### <a name="100-most-recent-registry-events"></a>100 meest recente register gebeurtenissen

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer , OperationName , Identity , Repository , DurationMs , Region , ResultType
```

## <a name="additional-log-destinations"></a>Aanvullende logboek doelen

Naast het verzenden van de logboeken naar Log Analytics, of als alternatief, is het een gemeen schappelijke scenario om een Azure Storage account te selecteren als een logboek bestemming. Als u Logboeken in Azure Storage wilt archiveren, maakt u een opslag account voordat u archiveren via de diagnostische instellingen inschakelt.

U kunt ook diagnostische logboek gebeurtenissen streamen naar een [Azure Event hub](../event-hubs/event-hubs-what-is-event-hubs.md). Event Hubs kan miljoenen gebeurtenissen per seconde opnemen, die u vervolgens kunt transformeren en opslaan met behulp van een real-time analyse provider. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het gebruik van [log Analytics](../azure-monitor/log-query/get-started-portal.md) en het maken van [logboek query's](../azure-monitor/log-query/get-started-queries.md).
* Zie [overzicht van Azure platform-logboeken](../azure-monitor/platform/platform-logs-overview.md) voor meer informatie over platform logboeken die beschikbaar zijn op verschillende lagen van Azure.

