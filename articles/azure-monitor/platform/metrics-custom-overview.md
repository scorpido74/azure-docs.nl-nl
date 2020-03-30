---
title: Aangepaste statistieken in Azure Monitor
description: Meer informatie over aangepaste statistieken in Azure Monitor en hoe ze worden gemodelleerd.
author: ancav
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: e104877ef641a87eac4ba19bb3342c6e029bf80c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294584"
---
# <a name="custom-metrics-in-azure-monitor"></a>Aangepaste statistieken in Azure Monitor

Terwijl u resources en toepassingen implementeert in Azure, wilt u telemetrie verzamelen om inzicht te krijgen in hun prestaties en status. Azure maakt sommige statistieken uit de doos voor u beschikbaar. Deze statistieken worden standaard of platform genoemd. Echter, ze zijn beperkt in de natuur. Misschien wilt u een aantal aangepaste prestatie-indicatoren of bedrijfsspecifieke statistieken verzamelen om diepere inzichten te bieden.
Deze **aangepaste** statistieken kunnen worden verzameld via de telemetrie van uw toepassing, een agent die wordt uitgevoerd op uw Azure-bronnen of zelfs een externe bewakingssysteem en rechtstreeks wordt verzonden naar Azure Monitor. Nadat ze zijn gepubliceerd in Azure Monitor, u bladeren, query's en waarschuwingen over aangepaste statistieken voor uw Azure-bronnen en -toepassingen naast de standaardstatistieken die door Azure worden uitgestraald.

## <a name="send-custom-metrics"></a>Aangepaste statistieken verzenden
Aangepaste statistieken kunnen via verschillende methoden naar Azure Monitor worden verzonden:
- Instrumenter uw toepassing met behulp van de Azure Application Insights SDK en stuur aangepaste telemetrie naar Azure Monitor. 
- Installeer de WINDOWS Azure Diagnostics (WAD)-extensie op uw [Azure VM,](collect-custom-metrics-guestos-resource-manager-vm.md) [virtuele machineschaalset,](collect-custom-metrics-guestos-resource-manager-vmss.md) [klassieke VM](collect-custom-metrics-guestos-vm-classic.md)of klassieke [Cloud Services](collect-custom-metrics-guestos-vm-cloud-service-classic.md) en stuur prestatiemeteritems naar Azure Monitor. 
- Installeer de [InfluxData Telegraf-agent](collect-custom-metrics-linux-telegraf.md) op uw Azure Linux VM en verzend statistieken met behulp van de Azure Monitor-uitvoerplug-in.
- Stuur aangepaste statistieken [rechtstreeks naar de Azure Monitor REST API](../../azure-monitor/platform/metrics-store-custom-rest-api.md), `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics`.

Wanneer u aangepaste statistieken naar Azure Monitor verzendt, moet elk gegevenspunt of elke gerapporteerde waarde de volgende gegevens bevatten.

### <a name="authentication"></a>Authentication
Als u aangepaste statistieken wilt verzenden naar Azure Monitor, heeft de entiteit die de statistiek verzendt, een geldig Azure Active Directory-token (Azure AD) nodig in de kop van de aanvraag **drager.** Er zijn een paar ondersteunde manieren om een geldig token aan toonder te verkrijgen:
1. [Beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Geeft een identiteit aan een Azure-bron zelf, zoals een VM. Managed Service Identity (MSI) is ontworpen om resources machtigingen te geven voor het uitvoeren van bepaalde bewerkingen. Een voorbeeld is dat een resource statistieken over zichzelf kan uitzenden. Een resource of msi kan machtigingen voor **Monitoring Metrics Publisher** op een andere bron krijgen. Met deze toestemming kan de MSI ook statistieken voor andere bronnen uitzenden.
2. [Azure AD-serviceprincipal](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals). In dit scenario kan een Azure AD-toepassing of -service machtigingen krijgen om statistieken over een Azure-bron uit te zenden.
Als u de aanvraag wilt verifiëren, valideert Azure Monitor het toepassingstoken met behulp van openbare Azure AD-sleutels. De bestaande functie **Publisher-statistieken** voor monitoringstatistieken heeft deze machtiging al. Het is beschikbaar in de Azure-portal. De serviceprincipal kan, afhankelijk van de resources waarvoor het aangepaste statistieken uitzendt, de rol **Publisher-statistieken controleren** op het vereiste bereik. Voorbeelden hiervan zijn een abonnement, resourcegroep of specifieke resource.

> [!NOTE]  
> Wanneer u een Azure AD-token aanvraagt om aangepaste statistieken uit te `https://monitoring.azure.com/`zenden, moet u ervoor zorgen dat de doelgroep of bron waarvoor het token wordt aangevraagd. Zorg ervoor dat u de trailing '/' opneemt.

### <a name="subject"></a>Onderwerp
Met deze eigenschap wordt vastgelegd voor welke Azure-resource-id de aangepaste statistiek wordt gerapporteerd. Deze informatie wordt gecodeerd in de URL van de API-aanroep die wordt uitgevoerd. Elke API kan alleen metrische waarden indienen voor één Azure-bron.

> [!NOTE]  
> U geen aangepaste statistieken uitzenden tegen de resource-id van een resourcegroep of -abonnement.
>
>

### <a name="region"></a>Regio
Deze eigenschap legt vast in welke Azure-regio de resource waarvoor u statistieken uitzendt, wordt geïmplementeerd. Metrische gegevens moeten worden uitgezonden naar hetzelfde regionale eindpunt azure monitor als de regio waarin de bron is geïmplementeerd. Aangepaste statistieken voor een VM die in West-US is geïmplementeerd, moeten bijvoorbeeld worden verzonden naar het westus-regionale Azure Monitor-eindpunt. De regio-informatie is ook gecodeerd in de URL van de API-aanroep.

> [!NOTE]  
> Tijdens de openbare preview zijn aangepaste statistieken alleen beschikbaar in een subset van Azure-regio's. Een lijst van ondersteunde regio's wordt gedocumenteerd in een later deel van dit artikel.
>
>

### <a name="timestamp"></a>Tijdstempel
Elk gegevenspunt dat naar Azure Monitor wordt verzonden, moet worden gemarkeerd met een tijdstempel. Deze tijdstempel legt de Datumtijd vast waarop de metrische waarde wordt gemeten of verzameld. Azure Monitor accepteert metrische gegevens met tijdstempels tot 20 minuten in het verleden en 5 minuten in de toekomst. De tijdstempel moet in ISO 8601-indeling zijn.

### <a name="namespace"></a>Naamruimte
Naamruimten zijn een manier om vergelijkbare statistieken samen te categoriseren of te groeperen. Door naamruimten te gebruiken, u isolatie bereiken tussen groepen statistieken die verschillende inzichten of prestatie-indicatoren kunnen verzamelen. U hebt bijvoorbeeld een naamruimte met de **naamcontosomemorymetrics** die statistieken voor het gebruik van het geheugen bijhoudt welk profiel uw app heeft. Een andere naamruimte genaamd **contosoapptransaction** kan alle statistieken over gebruikerstransacties in uw toepassing bijhouden.

### <a name="name"></a>Name
**Naam** is de naam van de statistiek die wordt gerapporteerd. Meestal is de naam beschrijvend genoeg om te helpen identificeren wat er wordt gemeten. Een voorbeeld is een statistiek die het aantal geheugenbytes meet dat op een bepaalde vm wordt gebruikt. Het kan een metrische naam hebben, zoals **Memory Bytes In Use**.

### <a name="dimension-keys"></a>Dimensietoetsen
Een dimensie is een sleutel- of waardepaar dat helpt bij het beschrijven van aanvullende kenmerken over de statistiek die wordt verzameld. Door de extra kenmerken te gebruiken, u meer informatie verzamelen over de statistiek, wat diepere inzichten mogelijk maakt. De statistiek **Geheugenbytes in gebruik** kan bijvoorbeeld een dimensiesleutel hebben met de naam **Process** die vastlegt hoeveel bytes geheugen elk proces op een virtuele machine verbruikt. Met deze sleutel u de statistiek filteren om te zien hoeveel geheugenspecifieke processen worden gebruikt of om de top vijf processen te identificeren op basis van geheugengebruik.
Afmetingen zijn optioneel, niet alle metrische gegevens kunnen afmetingen hebben. Een aangepaste statistiek kan maximaal 10 dimensies hebben.

### <a name="dimension-values"></a>Dimensiewaarden
Bij het melden van een metriek gegevenspunt is er voor elke dimensiesleutel op de statistiek die wordt gerapporteerd een overeenkomstige dimensiewaarde. U bijvoorbeeld het geheugen dat door de ContosoApp wordt gebruikt, rapporteren op uw VM:

* De metrische naam zou **Memory Bytes in Gebruik**zijn.
* De dimensiesleutel zou **Process**zijn.
* De dimensiewaarde zou **ContosoApp.exe**zijn.

Wanneer u een metrische waarde publiceert, u slechts één dimensiewaarde per dimensiesleutel opgeven. Als u hetzelfde geheugengebruik verzamelt voor meerdere processen op de VM, u meerdere metrische waarden voor die tijdstempel rapporteren. Elke metrische waarde geeft een andere dimensiewaarde op voor de **dimensiesleutel Proces.**
Afmetingen zijn optioneel, niet alle metrische gegevens kunnen afmetingen hebben. Als een metriek bericht dimensietoetsen definieert, zijn overeenkomstige dimensiewaarden verplicht.

### <a name="metric-values"></a>Metrische waarden
Azure Monitor slaat alle metrische gegevens op met granulaire intervallen van één minuut. We begrijpen dat gedurende een bepaalde minuut een statistiek mogelijk meerdere keren moet worden bemonsterd. Een voorbeeld is CPU-gebruik. Of het kan nodig zijn om te worden gemeten voor vele discrete gebeurtenissen. Een voorbeeld hiervan zijn aanmeldingstransactielatencies. Als u het aantal ruwe waarden wilt beperken dat u moet uitstoten en betalen in Azure Monitor, u de waarden lokaal vooraf samenvoegen en de waarden uitzenden:

* **Min**: De minimale waargenomen waarde van alle monsters en metingen gedurende de minuut.
* **Max**: De maximale waargenomen waarde van alle monsters en metingen gedurende de minuut.
* **Som**: De optelling van alle waargenomen waarden uit alle monsters en metingen gedurende de minuut.
* **Aantal**: Het aantal monsters en metingen dat gedurende de minuut is genomen.

Als er bijvoorbeeld gedurende een minuut 4 aanmeldingstransacties voor uw app zijn geweest, kunnen de resulterende gemeten latencies voor elk van deze minuten als volgt zijn:

|Transactie 1|Transactie 2|Transactie 3|Transactie 4|
|---|---|---|---|
|7 ms|4 ms|13 ms|16 ms|
|

Vervolgens is de resulterende metrische publicatie naar Azure Monitor als volgt:
* Min: 4
* Max: 16
* Som: 40
* Tel: 4

Als uw toepassing niet in staat is om lokaal vooraf te worden samengevoegd en elk afzonderlijk monster of gebeurtenis onmiddellijk na het verzamelen moet uitzenden, u de ruwe meetwaarden uitzenden. Elke keer dat een aanmeldingstransactie in uw app plaatsvindt, publiceert u bijvoorbeeld een statistiek naar Azure Monitor met slechts één meting. Dus voor een aanmeldingstransactie die 12 ms duurde, zou de metrische publicatie als volgt zijn:
* Min: 12
* Max: 12
* Som: 12
* Tel: 1

Met dit proces u meerdere waarden uitzenden voor dezelfde combinatie plus dimensie gedurende een bepaalde minuut. Azure Monitor neemt vervolgens alle ruwe waarden die voor een bepaalde minuut worden uitgestoten en verzamelt deze samen.

### <a name="sample-custom-metric-publication"></a>Voorbeeld van aangepaste metrische publicatie
In het volgende voorbeeld maakt u een aangepaste statistiek genaamd **Geheugenbytes in Gebruik** onder het metrische naamruimtegeheugenprofiel voor een virtuele machine. **Memory Profile** De statistiek heeft één dimensie genaamd **Proces**. Voor de gegeven tijdstempel zenden we metrische waarden uit voor twee verschillende processen:

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
> Application Insights, de diagnostische extensie en de InfluxData Telegraf-agent zijn al geconfigureerd om metrische waarden uit te zenden ten opzichte van het juiste regionale eindpunt en alle voorgaande eigenschappen in elke emissie te dragen.
>
>

## <a name="custom-metric-definitions"></a>Aangepaste metrische definities
Het is niet nodig om een aangepaste statistiek vooraf te definiëren in Azure Monitor voordat deze wordt uitgezonden. Elk gepubliceerd metriek gegevenspunt bevat naamruimte- en naam- en dimensiegegevens. Dus de eerste keer dat een aangepaste statistiek wordt uitgezonden naar Azure Monitor, wordt automatisch een metrische definitie gemaakt. Deze metrische definitie is vervolgens vindbaar op elke resource waartegen de statistiek wordt uitgezonden via de metrische definities.

> [!NOTE]  
> Azure Monitor biedt nog geen ondersteuning voor het definiëren van **eenheden** voor een aangepaste statistiek.

## <a name="using-custom-metrics"></a>Aangepaste statistieken gebruiken
Nadat aangepaste statistieken zijn verzonden naar Azure Monitor, u ze bekijken via de Azure-portal en deze opvragen via de AZURE Monitor REST API's. U er ook waarschuwingen over maken om u op de hoogte te stellen wanneer aan bepaalde voorwaarden is voldaan.

> [!NOTE]
> U moet een lezer of bijdrager zijn om aangepaste statistieken te bekijken.

### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Blader door uw aangepaste statistieken via de Azure-portal
1.    Ga naar de [Azure-portal.](https://portal.azure.com)
2.    Selecteer het **deelvenster Monitor.**
3.    Selecteer **Metrische gegevens**.
4.    Selecteer een resource waartegen aangepaste statistieken zijn uitgestraald.
5.    Selecteer de naamruimte voor metrische gegevens voor uw aangepaste statistiek.
6.    Selecteer de aangepaste statistiek.

## <a name="supported-regions"></a>Ondersteunde regio’s
Tijdens de openbare preview is de mogelijkheid om aangepaste statistieken te publiceren alleen beschikbaar in een subset van Azure-regio's. Deze beperking betekent dat statistieken alleen kunnen worden gepubliceerd voor resources in een van de ondersteunde regio's. In de volgende tabel vindt u de set ondersteunde Azure-regio's voor aangepaste statistieken. Het bevat ook de bijbehorende eindpunten die statistieken voor resources in die regio's moeten worden gepubliceerd om:

|Azure-regio |Regionaal eindpuntvoorvoegsel|
|---|---|
| **VS en Canada** | |
|VS - west-centraal | https:\//westcentralus.monitoring.azure.com/ |
|VS - west 2       | https:\//westus2.monitoring.azure.com/ |
|VS - noord-centraal | https:\//northcentralus.monitoring.azure.com
|VS - zuid-centraal| https:\//southcentralus.monitoring.azure.com/ |
|VS - centraal      | https:\//centralus.monitoring.azure.com |
|Canada - midden | https:\//canadacentral.monitoring.azure.comc
|VS - oost| https:\//eastus.monitoring.azure.com/ |
| **Europa** | |
|Europa - noord    | https:\//northeurope.monitoring.azure.com/ |
|Europa -west     | https:\//westeurope.monitoring.azure.com/ |
|Verenigd Koninkrijk Zuid | https:\//uksouth.monitoring.azure.com
|Frankrijk - centraal | https:\//francecentral.monitoring.azure.com |
| **Afrika** | |
|Zuid-Afrika Noord | https:\//southafricanorth.monitoring.azure.com
| **Azië** | |
|India - centraal | https:\//centralindia.monitoring.azure.com
|Australië - oost | https:\//australiaeast.monitoring.azure.com
|Japan - oost | https:\//japaneast.monitoring.azure.com
|Azië - zuidoost  | https:\//southeastasia.monitoring.azure.com |
|Azië - oost | https:\//eastasia.monitoring.azure.com
|Korea - centraal   | https:\//koreacentral.monitoring.azure.com


## <a name="quotas-and-limits"></a>Quota en limieten
Azure Monitor legt de volgende gebruikslimieten op aan aangepaste statistieken:

|Categorie|Limiet|
|---|---|
|Actieve tijdreeksen/abonnementen/regio|50,000|
|Dimensietoetsen per statistiek|10|
|Tekenreekslengte voor metrische naamruimten, metrische namen, dimensiesleutels en dimensiewaarden|256 tekens|

Een actieve tijdreeks wordt gedefinieerd als elke unieke combinatie van metrische, dimensiesleutel of dimensiewaarde waarbij metrische waarden in de afgelopen 12 uur zijn gepubliceerd.

## <a name="next-steps"></a>Volgende stappen
Gebruik aangepaste statistieken van verschillende services: 
 - [Virtuele machines](collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Schaalset voor virtuele machines](collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Azure Virtual Machines (klassiek)](collect-custom-metrics-guestos-vm-classic.md)
 - [Linux Virtual Machine met behulp van de Telegraf-agent](collect-custom-metrics-linux-telegraf.md)
 - [REST-API](../../azure-monitor/platform/metrics-store-custom-rest-api.md)
 - [Klassieke cloudservices](collect-custom-metrics-guestos-vm-cloud-service-classic.md)
 
