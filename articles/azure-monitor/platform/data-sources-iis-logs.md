---
title: IIS-logboeken in Azure-monitor | Microsoft Documenten
description: IIS (Internet Information Services) slaat gebruikersactiviteit op in logboekbestanden die kunnen worden verzameld door Azure Monitor.  In dit artikel wordt beschreven hoe u verzameling iIS-logboeken en details van de records die ze maken in Azure Monitor configureert.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: 1b3ae6295a639c3d59643b106b920cb606572e0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670573"
---
# <a name="collect-iis-logs-in-azure-monitor"></a>IIS-logboeken verzamelen in Azure-monitor
Internet Information Services (IIS) slaat gebruikersactiviteit op in logboekbestanden die door Azure Monitor kunnen worden verzameld en als [logboekgegevens](data-platform.md)kunnen worden opgeslagen.

![IIS-logboeken](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>IIS-logboeken configureren
Azure Monitor verzamelt items uit logboekbestanden die door IIS zijn gemaakt, dus u moet [IIS configureren voor logboekregistratie.](https://technet.microsoft.com/library/hh831775.aspx)

Azure Monitor ondersteunt alleen IIS-logboekbestanden die zijn opgeslagen in W3C-indeling en biedt geen ondersteuning voor aangepaste velden of IIS Advanced Logging. Het verzamelt geen logboeken in NCSA- of IIS-native formaat.

IIS-logboeken configureren in Azure Monitor in het [menu Geavanceerde instellingen](agent-data-sources.md#configuring-data-sources).  Er is geen andere configuratie vereist dan **Het selecteren van IIS-logboekbestanden met W3C-indeling**verzamelen.


## <a name="data-collection"></a>Gegevensverzameling
Azure Monitor verzamelt IIS-logboekvermeldingen van elke agent telkens wanneer de logboektijdstempel wordt gewijzigd. Het logboek wordt elke **5 minuten**gelezen. Als IIS om welke reden dan ook de tijdstempel niet bijwerkt vóór de rollovertijd wanneer een nieuw bestand wordt gemaakt, worden items verzameld na het maken van het nieuwe bestand. De frequentie van het maken van nieuwe bestanden wordt bepaald door de instelling **Rollover Schedule log** voor de IIS-site, die standaard één keer per dag is. Als de instelling **elk uur**is , verzamelt Azure Monitor het logboek elk uur. Als de instelling **Dagelijks**is, verzamelt Azure Monitor het logboek elke 24 uur.


## <a name="iis-log-record-properties"></a>Eigenschappen van IIS-logboekrecord
IIS-logboekrecords hebben een type **W3CIISLog** en hebben de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving |
|:--- |:--- |
| Computer |Naam van de computer waarvan de gebeurtenis is verzameld. |
| Cip |IP-adres van de client. |
| csMethode |Methode van het verzoek, zoals GET of POST. |
| csReferer |Site die de gebruiker volgde een link van naar de huidige site. |
| csUserAgent |Browsertype van de client. |
| csUserName |Naam van de geverifieerde gebruiker die toegang heeft tot de server. Anonieme gebruikers worden aangeduid met een koppelteken. |
| csUriStem (csUriStem) |Doel van de aanvraag, zoals een webpagina. |
| csUriQuery (csUriQuery) |Query, indien aanwezig, dat de client probeerde uit te voeren. |
| ManagementGroupName |Naam van de managementgroep voor Operations Manager agents.  Voor andere agents is dit\<AOI-werkplek-ID\> |
| RemoteIPCountry |Land/regio van het IP-adres van de klant. |
| RemoteIPLatitude |Latitude van het IP-adres van de client. |
| RemoteIPLongitude |Longitude van het IP-adres van de client. |
| scStatus scStatus scStatus |HTTP-statuscode. |
| scSubStatus |Substatusfoutcode. |
| scWin32Status |Windows-statuscode. |
| Sip |IP-adres van de webserver. |
| SourceSystem |OpsMgr OpsMgr |
| Sport |Poort op de server met de client die is verbonden. |
| sSiteName |Naam van de IIS-site. |
| TimeGenerated |Datum en tijd waarop de vermelding is geregistreerd. |
| Tijdgenomen |Tijdsduur om de aanvraag in milliseconden te verwerken. |

## <a name="log-queries-with-iis-logs"></a>Query's registreren met IIS-logboeken
In de volgende tabel vindt u verschillende voorbeelden van logboekquery's waarmee IIS-logboekrecords worden opgehaald.

| Query’s uitvoeren | Beschrijving |
|:--- |:--- |
| W3CIISLog |Alle IIS-logboekrecords. |
| W3CIISLog &#124; waar scStatus==500 |Alle IIS-logboekrecords met een retourstatus van 500. |
| W3CIISLog &#124; count() samenvatten op cIP |Aantal IIS-logboekvermeldingen per IP-adres van de client. |
| W3CIISLog &#124; waar csHost=="www\.contoso.com" &#124; count() samenvatten door csUriStem |Aantal IIS-logboekvermeldingen per URL voor\.de host www contoso.com. |
| W3CIISLog &#124; samenvatten som (csBytes) per computer &#124; nemen 500000 |Totaal aantal ontvangen bytes per IIS-computer. |

## <a name="next-steps"></a>Volgende stappen
* Configureer Azure Monitor om andere [gegevensbronnen](agent-data-sources.md) te verzamelen voor analyse.
* Meer informatie over [logboekquery's](../log-query/log-query-overview.md) om de gegevens te analyseren die zijn verzameld uit gegevensbronnen en -oplossingen.
