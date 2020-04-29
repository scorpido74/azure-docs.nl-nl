---
title: IIS-logboeken in Azure Monitor | Microsoft Docs
description: Internet Information Services (IIS) slaat gebruikers activiteiten op in logboek bestanden die door Azure Monitor kunnen worden verzameld.  In dit artikel wordt beschreven hoe u een verzameling IIS-logboeken en-Details configureert van de records die ze in Azure Monitor maken.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: 1b3ae6295a639c3d59643b106b920cb606572e0a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77670573"
---
# <a name="collect-iis-logs-in-azure-monitor"></a>IIS-logboeken in Azure Monitor verzamelen
Internet Information Services (IIS) slaat gebruikers activiteiten op in logboek bestanden die kunnen worden verzameld door Azure Monitor en worden opgeslagen als [logboek gegevens](data-platform.md).

![IIS-logboeken](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>IIS-logboeken configureren
Azure Monitor verzamelt vermeldingen uit logboek bestanden die zijn gemaakt door IIS, dus moet u [IIS configureren voor logboek registratie](https://technet.microsoft.com/library/hh831775.aspx).

Azure Monitor ondersteunt alleen IIS-logboek bestanden die zijn opgeslagen in de W3C-indeling en ondersteunt geen aangepaste velden of geavanceerde logboek registratie van IIS. Er worden geen logboeken verzameld in NCSA of de native IIS-indeling.

Configureer IIS-logboeken in Azure Monitor vanuit het [menu Geavanceerde instellingen](agent-data-sources.md#configuring-data-sources).  Er is geen configuratie vereist, anders dan het selecteren van **IIS-logboek bestanden voor W3C-indeling verzamelen**.


## <a name="data-collection"></a>Gegevens verzamelen
Azure Monitor worden IIS-logboek vermeldingen van elke agent verzameld telkens wanneer de tijds tempel van het logboek wordt gewijzigd. Het logboek wordt elke **vijf minuten**gelezen. Als IIS de tijds tempel voor de rollover tijd niet bijwerkt wanneer een nieuw bestand wordt gemaakt, worden de gegevens verzameld na het maken van het nieuwe bestand. De frequentie van het maken van nieuwe bestanden wordt bepaald door de instelling voor het schema voor de **rollover van logboek bestanden** voor de IIS-site, die standaard één keer per dag wordt uitgevoerd. Als de instelling elk **uur**is, verzamelt Azure monitor elk uur het logboek. Als de instelling **dagelijks**is, verzamelt Azure monitor het logboek elke 24 uur.


## <a name="iis-log-record-properties"></a>Eigenschappen van IIS-logboek record
IIS-logboek records hebben een type **W3CIISLog** en hebben de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving |
|:--- |:--- |
| Computer |De naam van de computer waarop de gebeurtenis is verzameld. |
| cIP |IP-adres van de client. |
| csMethod |De methode van de aanvraag, zoals GET of POST. |
| csReferer |Site waar de gebruiker een koppeling heeft gevolgd van naar de huidige site. |
| csUserAgent |Browser type van de client. |
| csUserName |Naam van de geverifieerde gebruiker die toegang heeft tot de server. Anonieme gebruikers worden aangeduid met een koppelteken. |
| csUriStem |Doel van de aanvraag, zoals een webpagina. |
| csUriQuery |Indien van toepassing, query die de client probeerde uit te voeren. |
| ManagementGroupName |De naam van de beheer groep voor Operations Manager agents.  Voor andere agents is dit AOI-\<werk ruimte-id\> |
| RemoteIPCountry |Het land/de regio van het IP-adres van de client. |
| RemoteIPLatitude |De breedte graad van het client-IP-adres. |
| RemoteIPLongitude |De lengte graad van het client-IP-adres. |
| scStatus |HTTP-status code. |
| scSubStatus |Fout code van substatus. |
| scWin32Status |Windows-status code. |
| sIP |Het IP-adres van de webserver. |
| SourceSystem |OpsMgr |
| Manifestatie |Poort op de server waarmee de client is verbonden. |
| sSiteName |De naam van de IIS-site. |
| TimeGenerated |De datum en tijd waarop de vermelding is geregistreerd. |
| TimeTaken |De tijds duur voor het verwerken van de aanvraag in milliseconden. |

## <a name="log-queries-with-iis-logs"></a>Query's vastleggen in Logboeken met IIS-logboeken
De volgende tabel bevat verschillende voor beelden van logboek query's waarmee IIS-logboek records worden opgehaald.

| Query’s uitvoeren | Beschrijving |
|:--- |:--- |
| W3CIISLog |Alle IIS-logboek records. |
| W3CIISLog &#124; waarbij scStatus = = 500 |Alle IIS-logboek records met de retour status 500. |
| Aantal W3CIISLog &#124;-overzicht () door overschrijving |Aantal IIS-logboek vermeldingen op client-IP-adres. |
| W3CIISLog &#124; waarbij csHost = = "www\.contoso.com" &#124; totaal aantal () door csUriStem |Aantal IIS-logboek vermeldingen op URL voor de www\.-contoso.com van de host. |
| W3CIISLog &#124; samenvat Sum (csBytes) door computer &#124; 500000 |Het totale aantal bytes dat door elke IIS-computer is ontvangen. |

## <a name="next-steps"></a>Volgende stappen
* Configureer Azure Monitor voor het verzamelen van andere [gegevens bronnen](agent-data-sources.md) voor analyse.
* Meer informatie over [logboek query's](../log-query/log-query-overview.md) voor het analyseren van de gegevens die zijn verzameld uit gegevens bronnen en oplossingen.
