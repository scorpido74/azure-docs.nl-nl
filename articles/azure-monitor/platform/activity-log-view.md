---
title: Statusgebeurtenissen voor Azure Activity-logboeken weergeven in Azure Monitor
description: Bekijk het Azure Activity-logboek in Azure Monitor en haal deze op met PowerShell-, CLI- en REST-API.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: d2423d04ead9040cce53d847d24efe75be680d94
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397311"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Azure Activity-logboekgebeurtenissen weergeven en ophalen

Het [Azure-activiteitenlogboek](platform-logs-overview.md) biedt inzicht in gebeurtenissen op abonnementsniveau die zich in Azure hebben voorgedaan. In dit artikel vindt u informatie over verschillende methoden voor het bekijken en ophalen van gebeurtenissen in het activiteitenlogboek.

## <a name="azure-portal"></a>Azure Portal
Bekijk het activiteitenlogboek voor alle bronnen in het menu **Monitor** in de Azure-portal. Het activiteitenlogboek voor een bepaalde resource weergeven in het menu van het **activiteitenlogboek.**

![Activiteitenlogboek weergeven](./media/activity-logs-overview/view-activity-log.png)

U gebeurtenissen in het activiteitenlogboek filteren op de volgende velden:

* **Tijdspanne**: De begin- en eindtijd voor evenementen.
* **Categorie**: De gebeurteniscategorie zoals beschreven in [Rubrieken in het activiteitenlogboek](activity-log-view.md#categories-in-the-activity-log).
* **Abonnement**: een of meer Namen van Azure-abonnementen.
* **Resourcegroep**: een of meer resourcegroepen binnen de geselecteerde abonnementen.
* **Resource (naam)**: - De naam van een specifieke resource.
* **Resourcetype:** het type resource, bijvoorbeeld _Microsoft.Compute/virtualmachines_.
* **Bedrijfsnaam** - De naam van een Azure Resource Manager-bewerking, bijvoorbeeld _Microsoft.SQL/servers/Write_.
* **Ernst**: Het ernstniveau van de gebeurtenis. Beschikbare waarden zijn _Informatief_, _Waarschuwing_, _Fout_, _Kritiek_.
* **Gebeurtenis geïnitieerd door**: De gebruiker die de bewerking heeft uitgevoerd.
* **Zoek openen:** open het tekstzoekvak waarin wordt gezocht naar die tekenreeks in alle velden in alle gebeurtenissen.

## <a name="categories-in-the-activity-log"></a>Categorieën in het activiteitenlogboek
Elke gebeurtenis in het activiteitenlogboek heeft een bepaalde categorie die in de volgende tabel wordt beschreven. Zie [het gebeurtenisschema](activity-log-schema.md)van Azure Activity Log voor meer informatie over de schema's van deze categorieën . 

| Categorie | Beschrijving |
|:---|:---|
| Administratief | Bevat de record van alle bewerkingen voor maken, bijwerken, verwijderen en actie die via Resource Manager worden uitgevoerd. Voorbeelden van administratieve gebeurtenissen zijn het maken van _virtuele machine_ en het verwijderen van de _netwerkbeveiligingsgroep._<br><br>Elke actie die wordt uitgevoerd door een gebruiker of toepassing met Resource Manager, wordt gemodelleerd als een bewerking op een bepaald resourcetype. Als het bewerkingstype _Schrijven,_ _Verwijderen_of _Actie_is, worden de records van zowel het begin als het succes of het mislukken van die bewerking geregistreerd in de categorie Beheer. Administratieve gebeurtenissen bevatten ook wijzigingen in het op rollen gebaseerde toegangscontrolein een abonnement. |
| Service Health | Bevat de registratie van eventuele statusincidenten in Azure. Een voorbeeld van een Service _Health-gebeurtenis SQL Azure in Oost-VS ervaart downtime._ <br><br>Service Health-evenementen zijn er in zes varianten: _Actie vereist,_ _assisted recovery,_ _incident,_ _onderhoud_, _informatie_of _beveiliging_. Deze gebeurtenissen worden alleen gemaakt als u een resource in het abonnement hebt die door de gebeurtenis wordt beïnvloed.
| Status van resources | Bevat de record van alle gebeurtenissen in de resourcestatus die zijn opgetreden in uw Azure-bronnen. Een voorbeeld van een gebeurtenis Resourcestatus is _de status Virtuele machine die is gewijzigd in niet-beschikbaar_.<br><br>Gebeurtenissen in resourcestatus kunnen een van de vier statussen vertegenwoordigen: _Beschikbaar_, _Niet beschikbaar_, _gedegradeerd_en _onbekend_. Bovendien kunnen resourcestatusgebeurtenissen worden gecategoriseerd als _platform geïnitieerd_ of door de _gebruiker geïnitieerd_. |
| Waarschuwing | Bevat de record van activeringen voor Azure-waarschuwingen. Een voorbeeld van een waarschuwing gebeurtenis is _CPU% op myVM is meer dan 80 voor de afgelopen 5 minuten_.|
| Automatisch schalen | Bevat de registratie van alle gebeurtenissen die verband houden met de werking van de autoscale-engine op basis van de instellingen voor automatische schaal die u in uw abonnement hebt gedefinieerd. Een voorbeeld van een gebeurtenis Autoscale is _De opschaingsactie Autoscale is mislukt_. |
| Aanbeveling | Bevat aanbevelingsgebeurtenissen van Azure Advisor. |
| Beveiliging | Bevat de registratie van alle waarschuwingen die worden gegenereerd door Azure Security Center. Een voorbeeld van een beveiligingsgebeurtenis is _Verdacht dubbelextensiebestand uitgevoerd_. |
| Beleid | Bevat records van alle effectactiebewerkingen die worden uitgevoerd door Azure Policy. Voorbeelden van beleidsgebeurtenissen zijn _Audit_ en _Deny_. Elke actie die door Beleid wordt ondernomen, is gemodelleerd als een bewerking op een resource. |

## <a name="view-change-history"></a>Wijzigingsgeschiedenis weergeven

Wanneer u het activiteitenlogboek bekijkt, u zien welke wijzigingen er tijdens die gebeurtenistijd zijn gebeurd. U deze informatie bekijken met **De geschiedenis van wijzigen**. Selecteer een gebeurtenis in het activiteitenlogboek waar u dieper in wilt kijken. Selecteer het tabblad **Geschiedenis wijzigen (Voorbeeld)** om eventuele bijbehorende wijzigingen aan die gebeurtenis weer te geven.

![Geschiedenislijst voor een gebeurtenis wijzigen](media/activity-logs-overview/change-history-event.png)

Als er wijzigingen zijn verbonden aan de gebeurtenis, ziet u een lijst met wijzigingen die u selecteren. Hiermee wordt de pagina **Voorvertoning wijzigen** geopend. Op deze pagina ziet u de wijzigingen in de bron. Zoals u zien in het volgende voorbeeld, kunnen we niet alleen zien dat de VM van grootte is veranderd, maar ook wat de vorige VM-grootte was vóór de wijziging en wat er in is gewijzigd.

![Geschiedenispagina wijzigen met verschillen](media/activity-logs-overview/change-history-event-details.png)

Zie [Resourcewijzigingen opbrengen](../../governance/resource-graph/how-to/get-resource-changes.md)voor meer informatie over de geschiedenis van wijzigen .






## <a name="powershell"></a>PowerShell
Gebruik de [cmdlet Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) om het activiteitenlogboek op te halen bij PowerShell. Hieronder volgen enkele veelvoorkomende voorbeelden.

> [!NOTE]
> `Get-AzLog`biedt slechts 15 dagen geschiedenis. Gebruik de parameter **-MaxEvents** om de laatste N-gebeurtenissen na 15 dagen op te vragen. Als u toegang wilt krijgen tot gebeurtenissen ouder dan 15 dagen, gebruikt u de REST API of SDK. Als u **StartTime**niet opneemt, is de standaardwaarde **EndTime** min één uur. Als u **EndTime**niet opneemt, is de standaardwaarde de huidige tijd. Alle tijden zijn in UTC.


Logboekvermeldingen die na een bepaalde datumtijd zijn gemaakt:

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

Logboekvermeldingen tussen een datumtijdbereik opvragen:

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Logboekvermeldingen ophalen uit een specifieke resourcegroep:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Logboekvermeldingen van een specifieke resourceprovider ophalen tussen een datumtijdsbereik:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Logberichten met een specifieke beller opdoen:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Ontvang de laatste 1000 evenementen:

```powershell
Get-AzLog -MaxEvents 1000
```


## <a name="cli"></a>CLI
Gebruik [het activiteitenlogboek van AZ-monitor](cli-samples.md#view-activity-log-for-a-subscription) om het activiteitenlogboek uit CLI op te halen. Hieronder volgen enkele veelvoorkomende voorbeelden.


Bekijk alle beschikbare opties.

```azurecli
az monitor activity-log list -h
```

Logboekvermeldingen ophalen uit een specifieke resourcegroep:

```azurecli
az monitor activity-log list --resource-group <group name>
```

Logberichten met een specifieke beller opdoen:

```azurecli
az monitor activity-log list --caller myname@company.com
```

Logboeken opvragen op een resourcetype binnen een datumbereik:

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>REST-API
Gebruik de [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/) om het activiteitenlogboek op te halen uit een REST-client. Hieronder volgen enkele veelvoorkomende voorbeelden.

Activiteitslogboeken met filter opdoen:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Activiteitslogboeken met filter opdoen en selecteer:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Activiteitslogboeken opdoen met selecteren:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Activiteitslogboeken zonder filter of selecteer:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="next-steps"></a>Volgende stappen

* [Lees een overzicht van platformlogs](platform-logs-overview.md)
* [Diagnostische instelling maken om activiteitslogboeken naar andere bestemmingen te verzenden](diagnostic-settings.md)
