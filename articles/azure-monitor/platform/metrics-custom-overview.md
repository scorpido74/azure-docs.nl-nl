---
title: Aangepaste metrische gegevens in Azure Monitor
description: Meer informatie over aangepaste metrische gegevens in Azure Monitor en hoe deze worden gemodelleerd.
author: ancav
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 744958fc44a8d10bbc8ca5d44af8c473548ae5ca
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73669167"
---
# <a name="custom-metrics-in-azure-monitor"></a>Aangepaste metrische gegevens in Azure Monitor

Wanneer u resources en toepassingen in azure implementeert, kunt u beginnen met het verzamelen van telemetrie om inzicht te krijgen in de prestaties en de status. In azure zijn enkele metrische gegevens voor u beschikbaar. Deze metrische gegevens worden Standard of platform genoemd. Ze zijn echter beperkt. Mogelijk wilt u een aantal aangepaste prestatie-indica toren of bedrijfsspecifieke metrische gegevens verzamelen om meer inzicht te krijgen.
Deze **aangepaste** metrische gegevens kunnen worden verzameld via de telemetrie van uw toepassing, een agent die wordt uitgevoerd op uw Azure-resources of zelfs een systeem voor het bewaken van een externe locatie en rechtstreeks aan Azure monitor is verzonden. Nadat u deze hebt gepubliceerd op Azure Monitor, kunt u bladeren, query's en waarschuwingen uitvoeren op aangepaste metrische gegevens voor uw Azure-resources en-toepassingen naast de standaard gegevens die door Azure worden verzonden.

## <a name="send-custom-metrics"></a>Aangepaste metrische gegevens verzenden
Aangepaste metrische gegevens kunnen via verschillende methoden naar Azure Monitor worden verzonden:
- Instrumenteer uw toepassing met behulp van de Azure-toepassing Insights-SDK en verzend aangepaste telemetrie naar Azure Monitor. 
- Installeer de Windows Azure Diagnostics (WAD)-extensie op [uw Azure-VM](collect-custom-metrics-guestos-resource-manager-vm.md), [schaalset voor virtuele machines](collect-custom-metrics-guestos-resource-manager-vmss.md), [klassieke virtuele](collect-custom-metrics-guestos-vm-classic.md)machine of [klassieke Cloud Services](collect-custom-metrics-guestos-vm-cloud-service-classic.md) en verzend prestatie meter items naar Azure monitor. 
- Installeer de [InfluxData-telegrafa-agent](collect-custom-metrics-linux-telegraf.md) op uw virtuele Azure Linux-machine en verzend metrische gegevens met behulp van de invoeg toepassing Azure monitor-uitvoer.
- Aangepaste metrische gegevens [rechtstreeks naar de Azure Monitor rest API](../../azure-monitor/platform/metrics-store-custom-rest-api.md), `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics`verzenden.

Wanneer u aangepaste metrische gegevens naar Azure Monitor stuurt, moet u het volgende informatie punt of elke waarde vermelden.

### <a name="authentication"></a>Authentication
Als u aangepaste metrische gegevens naar Azure Monitor wilt verzenden, moet de entiteit die de metrische gegevens indient, een geldig Azure Active Directory (Azure AD)-token in de **Bearer** -header van de aanvraag hebben. Er zijn een aantal ondersteunde manieren om een geldig Bearer-token te verkrijgen:
1. [Beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Geeft een identiteit aan een Azure-resource zelf, zoals een virtuele machine. Managed Service Identity (MSI) is ontworpen om resources machtigingen te geven om bepaalde bewerkingen uit te voeren. Een voor beeld is het toestaan van een resource over het verzenden van metrische gegevens. Aan een resource of het MSI-bestand kunnen **bewakings gegevens** voor de uitgever van machtigingen worden verleend voor een andere resource. Met deze machtiging kan het MSI-bestand ook metrische gegevens verzenden voor andere resources.
2. [Azure AD-Service-Principal](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals). In dit scenario kan een Azure AD-toepassing of-service worden toegewezen aan machtigingen voor het verzenden van metrische gegevens over een Azure-resource.
Azure Monitor valideert het toepassings token met behulp van open bare Azure AD-sleutels om de aanvraag te verifiëren. De rol van uitgever van de bestaande **bewakings metrieken** heeft deze machtiging al. Deze is beschikbaar in de Azure Portal. De Service-Principal, afhankelijk van de resources waarvoor de aangepaste metrische gegevens worden verstrekt, kan de rol van de **Uitgever van metrische gegevens voor bewaking** aan het vereiste bereik krijgen. Voor beelden zijn een abonnement, resource groep of specifieke resource.

> [!NOTE]  
> Als u een Azure AD-token aanvraagt om aangepaste metrische gegevens te verzenden, moet u ervoor zorgen dat de doel groep of de bron waarvoor het token wordt aangevraagd, is https://monitoring.azure.com/. Zorg ervoor dat u de opvolging '/' opgeeft.

### <a name="subject"></a>Onderwerp
Met deze eigenschap wordt vastgelegd voor welke Azure-Resource-ID de aangepaste metrische gegevens worden gerapporteerd. Deze informatie wordt gecodeerd in de URL van de API-aanroep die wordt gemaakt. Elke API kan alleen metrische waarden voor één Azure-resource indienen.

> [!NOTE]  
> U kunt geen aangepaste metrische gegevens verzenden op basis van de resource-ID van een resource groep of abonnement.
>
>

### <a name="region"></a>Regio
Met deze eigenschap wordt vastgelegd in welke Azure-regio de resource waarvoor u de metrische gegevens uitbrengt, wordt geïmplementeerd. Metrische gegevens moeten worden verzonden naar hetzelfde Azure Monitor regionale eind punt als de regio waarin de resource is geïmplementeerd. Aangepaste metrische gegevens voor een virtuele machine die is geïmplementeerd in West-Verenigde Staten, moeten bijvoorbeeld worden verzonden naar het regionale Azure Monitor eind punt Westus. De regio gegevens worden ook gecodeerd in de URL van de API-aanroep.

> [!NOTE]  
> Tijdens de open bare preview zijn aangepaste metrische gegevens alleen beschikbaar in een subset van Azure-regio's. Een lijst met ondersteunde regio's wordt beschreven in een latere sectie van dit artikel.
>
>

### <a name="timestamp"></a>Tijdstempel
Elk gegevens punt dat naar Azure Monitor wordt verzonden, moet worden gemarkeerd met een tijds tempel. Deze tijds tempel legt de datum/tijd vast waarop de metrische waarde wordt gemeten of verzameld. Azure Monitor worden metrische gegevens met tijds tempels geaccepteerd tot 20 minuten in het verleden en vijf minuten in de toekomst. De tijds tempel moet de ISO 8601-indeling hebben.

### <a name="namespace"></a>Naamruimte
Naam ruimten zijn een manier om Vergelijk bare metrische gegevens te categoriseren of groeperen. Met behulp van naam ruimten kunt u isolatie verkrijgen tussen groepen metrische gegevens die verschillende inzichten of prestatie-indica toren kunnen verzamelen. U kunt bijvoorbeeld een naam ruimte hebben met de naam **contosomemorymetrics** die geheugen gebruik houdt van metrische gegevens die uw app profiel. Een andere naam ruimte met de naam **contosoapptransaction** kan alle metrische gegevens over gebruikers transacties in uw toepassing bijhouden.

### <a name="name"></a>Naam
**Naam** is de naam van de metrische gegevens die worden gerapporteerd. Normaal gesp roken is de naam beschrijvend genoeg om te helpen bij het identificeren van wat wordt gemeten. Een voor beeld hiervan is een metrische waarde die het aantal door het geheugen gebruikte bytes op een bepaalde VM meet. Dit kan een metrische naam zijn, zoals het **geheugen bytes dat in gebruik**is.

### <a name="dimension-keys"></a>Dimensie sleutels
Een dimensie is een sleutel-of waardepaar dat helpt bij het beschrijven van aanvullende kenmerken over de metrische gegevens die worden verzameld. Door de aanvullende kenmerken te gebruiken, kunt u meer informatie verzamelen over de metriek, waardoor u meer inzicht kunt krijgen. Zo kan de metrische **geheugen bytes in gebruik** bijvoorbeeld een dimensie sleutel hebben met de naam **process** waarmee wordt vastgelegd hoeveel bytes van geheugen elk proces op een virtuele machine gebruikt. Met deze sleutel kunt u de metrische gegevens filteren om te zien hoeveel geheugen-specifieke processen gebruikmaken van of om de vijf meest voorkomende processen te identificeren op basis van geheugen gebruik.
Dimensies zijn optioneel, niet alle metrische gegevens kunnen dimensies hebben. Een aangepaste metriek kan Maxi maal 10 dimensies hebben.

### <a name="dimension-values"></a>Dimensie waarden
Bij het rapporteren van een metriek gegevens punt voor elke dimensie sleutel op de metrische waarde die wordt gerapporteerd, is er een overeenkomende dimensie waarde. Het is bijvoorbeeld mogelijk dat u het geheugen wilt rapporteren dat wordt gebruikt door de ContosoApp op uw virtuele machine:

* De naam van de metriek zou **geheugen bytes in gebruik**zijn.
* De dimensie sleutel wordt **verwerkt**.
* De dimensie waarde is **ContosoApp. exe**.

Wanneer u een metrische waarde publiceert, kunt u slechts één dimensie waarde per dimensie sleutel opgeven. Als u hetzelfde geheugen gebruik voor meerdere processen op de virtuele machine verzamelt, kunt u meerdere metrische waarden voor die tijds tempel rapporteren. Elke metrische waarde zou een andere dimensie waarde voor de **proces** dimensie sleutel opgeven.
Dimensies zijn optioneel, niet alle metrische gegevens kunnen dimensies hebben. Als voor een metrische post dimensie sleutels worden gedefinieerd, zijn overeenkomstige dimensie waarden verplicht.

### <a name="metric-values"></a>Metrische waarden
Azure Monitor worden alle metrische gegevens opgeslagen met granulatie intervallen van één minuut. We begrijpen dat er tijdens een bepaalde minuut een metriek mogelijk meerdere malen moet worden gesampled. Een voor beeld is het CPU-gebruik. Het is ook mogelijk dat er moet worden gemeten voor veel discrete gebeurtenissen. Een voor beeld is latentie van aanmeldings transacties. Als u het aantal onbewerkte waarden wilt beperken dat u wilt verzenden en betalen voor in Azure Monitor, kunt u de waarden lokaal vooraf combi neren en verzenden:

* **Min**: de minimale waargenomen waarde van alle voor beelden en metingen gedurende de minuut.
* **Max**: de Maxi maal waargenomen waarde van alle voor beelden en metingen gedurende de minuut.
* **Sum**: de som van alle waargenomen waarden van alle voor beelden en metingen gedurende de minuut.
* **Aantal**: het aantal voor beelden en metingen dat is gemaakt tijdens de minuut.

Als er gedurende een minuut 4 aanmeldings transacties voor uw app zijn, zijn de resulterende gemeten latenties voor beide bijvoorbeeld als volgt:

|Trans actie 1|Trans actie 2|Trans actie 3|Trans actie 4|
|---|---|---|---|
|7 MS|4 MS|13 MS|16 MS|
|

De resulterende metrische publicatie voor Azure Monitor zou er als volgt uitzien:
* Min: 4
* Max.: 16
* Sum: 40
* Aantal: 4

Als uw toepassing niet lokaal kan worden geaggregeerd en elke afzonderlijke steek proef of gebeurtenis direct bij de verzameling moet worden opgehaald, kunt u de waarden voor onbewerkte metingen verzenden. Elke keer dat er een aanmeldings transactie plaatsvindt in uw app, kunt u bijvoorbeeld een metriek publiceren om te Azure Monitor met slechts één meting. Voor een aanmeldings transactie die 12 MS duurde, zou de metrische publicatie er als volgt uitzien:
* Min: 12
* Maximum: 12
* Som: 12
* Aantal: 1

Met dit proces kunt u tijdens een bepaalde minuut meerdere waarden voor dezelfde metrische plus dimensie combinatie verzenden. Azure Monitor worden vervolgens alle onbewerkte waarden voor een bepaalde minuut verzonden en samengevoegd.

### <a name="sample-custom-metric-publication"></a>Voor beeld van een aangepaste metrische publicatie
In het volgende voor beeld maakt u een aangepaste metriek met de naam **geheugen bytes** die worden gebruikt onder het **geheugen profiel** metrische naam ruimte voor een virtuele machine. De metriek heeft één dimensie met de naam **process**. Voor de opgegeven tijds tempel verzenden we metrische waarden voor twee verschillende processen:

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"        ],
        "series": [
          {
            "dimValues": [
              "ContosoApp.exe"
            ],
            "min": 10,
            "max": 89,
            "sum": 190,
            "count": 4
          },
          {
            "dimValues": [
              "SalesApp.exe"
            ],
            "min": 10,
            "max": 23,
            "sum": 86,
            "count": 4
          }
        ]
      }
    }
  }
```
> [!NOTE]  
> Application Insights, de uitbrei ding van de diagnostische gegevens en de InfluxData-telegrafa-agent zijn al geconfigureerd voor het leveren van metrische waarden voor het juiste regionale eind punt en worden alle voor gaande eigenschappen in elke emissie overgedragen.
>
>

## <a name="custom-metric-definitions"></a>Aangepaste metrische definities
U hoeft geen aangepaste metriek vooraf te definiëren in Azure Monitor voordat deze wordt verzonden. Elk gegevens punt dat wordt gepubliceerd, bevat naam ruimte, naam en dimensie-informatie. De eerste keer dat een aangepaste metriek wordt verzonden naar Azure Monitor, wordt automatisch een metrische definitie gemaakt. Deze metrische definitie kan vervolgens worden gedetecteerd voor elke resource waarbij de metrische gegevens worden verzonden via de metrische definities.

> [!NOTE]  
> Azure Monitor biedt nog geen ondersteuning voor het definiëren van **eenheden** voor een aangepaste metriek.

## <a name="using-custom-metrics"></a>Aangepaste metrische gegevens gebruiken
Nadat aangepaste metrische gegevens zijn verzonden naar Azure Monitor, kunt u ze via de Azure Portal door bladeren en query's uitvoeren via de Azure Monitor REST-Api's. U kunt ook waarschuwingen maken om u te waarschuwen wanneer aan bepaalde voor waarden wordt voldaan.
### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Door uw aangepaste metrische gegevens bladeren via de Azure Portal
1.  Ga naar de [Azure Portal](https://portal.azure.com).
2.  Selecteer het deel venster **monitor** .
3.  Selecteer **Metrische gegevens**.
4.  Selecteer een resource waarvoor u aangepaste metrische gegevens hebt verzonden.
5.  Selecteer de metrische naam ruimte voor uw aangepaste metrische gegevens.
6.  Selecteer de aangepaste metriek.

## <a name="supported-regions"></a>Ondersteunde regio’s
Tijdens de open bare preview-periode is de mogelijkheid om aangepaste metrische gegevens te publiceren alleen beschikbaar in een subset van Azure-regio's. Deze beperking betekent dat metrische gegevens alleen voor resources in een van de ondersteunde regio's kunnen worden gepubliceerd. De volgende tabel bevat de set ondersteunde Azure-regio's voor aangepaste metrische gegevens. Ook worden de bijbehorende eind punten vermeld waarvoor metrische gegevens voor resources in deze regio's moeten worden gepubliceerd:

|Azure-regio |Voor voegsel regionale eind punt|
|---|---|
| **VS en Canada** | |
|VS - west-centraal | https:\/-westcentralus.monitoring.azure.com/ |
|VS - west 2       | https:\/-westus2.monitoring.azure.com/ |
|VS - noord-centraal | https:\/-northcentralus.monitoring.azure.com
|VS - zuid-centraal| https:\/-southcentralus.monitoring.azure.com/ |
|VS - centraal      | https:\/-centralus.monitoring.azure.com |
|Canada - midden | https:\//canadacentral.monitoring.Azure.comc
|VS - oost| https:\/-eastus.monitoring.azure.com/ |
| **Europa** | |
|Europa - noord    | https:\/-northeurope.monitoring.azure.com/ |
|Europa -west     | https:\/-westeurope.monitoring.azure.com/ |
|UK - zuid | https:\/-uksouth.monitoring.azure.com
|Frankrijk - centraal | https:\/-francecentral.monitoring.azure.com |
| **Africa** | |
|Zuid-Afrika - noord | https:\/-southafricanorth.monitoring.azure.com
| **Azië** | |
|India - centraal | https:\/-centralindia.monitoring.azure.com
|Australië - oost | https:\/-australiaeast.monitoring.azure.com
|Japan - oost | https:\/-japaneast.monitoring.azure.com
|Azië - zuidoost  | https:\/-southeastasia.monitoring.azure.com |
|Azië - oost | https:\/-eastasia.monitoring.azure.com
|Korea - centraal   | https:\/-koreacentral.monitoring.azure.com


## <a name="quotas-and-limits"></a>Quota en limieten
Azure Monitor de volgende gebruiks limieten opleggen voor aangepaste metrische gegevens:

|Category|Limiet|
|---|---|
|Actieve tijd reeks/abonnementen/regio|50,000|
|Dimensie sleutels per metriek|10|
|Teken reeks lengte voor metrische naam ruimten, metrische namen, dimensie sleutels en dimensie waarden|256 tekens|

Een actieve tijd reeks wordt gedefinieerd als een unieke combi natie van metrische gegevens, dimensie sleutels of dimensie waarden waarvan de metrische waarden in de afgelopen 12 uur zijn gepubliceerd.

## <a name="next-steps"></a>Volgende stappen
Aangepaste metrische gegevens van verschillende services gebruiken: 
 - [Virtuele machines](collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Schaalset voor virtuele machines](collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Azure Virtual Machines (klassiek)](collect-custom-metrics-guestos-vm-classic.md)
 - [Virtuele Linux-machine met behulp van de Telegraf-agent](collect-custom-metrics-linux-telegraf.md)
 - [REST API](../../azure-monitor/platform/metrics-store-custom-rest-api.md)
 - [Klassieke Cloud Services](collect-custom-metrics-guestos-vm-cloud-service-classic.md)
 
