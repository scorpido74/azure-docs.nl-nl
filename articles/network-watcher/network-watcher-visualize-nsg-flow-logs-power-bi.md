---
title: Azure NSG-stroomlogboeken visualiseren - Power BI
titleSuffix: Azure Network Watcher
description: Op deze pagina wordt beschreven hoe u NSG-stroomlogboeken visualiseren met Power BI.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 955e13b88037aa42b59707698549b1c980720990
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840601"
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>Stroomlogboeken van netwerkbeveiligingsgroepen visualiseren met Power BI

Met stroomlogboeken voor netwerkbeveiliginggroeps u informatie weergeven over binnendringenen en het binnendringen van IP-verkeer op netwerkbeveiligingsgroepen. Deze stroomlogboeken tonen uitgaande en binnenkomende stromen per regel, de NIC waarop de stroom van toepassing is, 5-tuple-informatie over de stroom (Bron/Bestemming-IP, Bron/Bestemmingspoort, Protocol) en als het verkeer is toegestaan of geweigerd.

Het kan moeilijk zijn om inzicht te krijgen in stroomregistratiegegevens door handmatig in de logbestanden te zoeken. In dit artikel bieden we een oplossing om uw meest recente stroomlogboeken te visualiseren en meer te weten te komen over het verkeer op uw netwerk.

> [!Warning]  
> De volgende stappen werken met stroomlogboeken versie 1. Zie Inleiding [tot stroomlogboekregistratie voor netwerkbeveiligingsgroepen voor](network-watcher-nsg-flow-logging-overview.md)meer informatie. De volgende instructies werken niet met versie 2 van de logbestanden, zonder wijziging.

## <a name="scenario"></a>Scenario

In het volgende scenario verbinden we Power BI-bureaublad met het opslagaccount dat we hebben geconfigureerd als de gootsteen voor onze NSG Flow Logging-gegevens. Nadat we verbinding hebben gemaakt met ons opslagaccount, downloadt en ontdoet Power BI de logboeken om een visuele weergave te geven van het verkeer dat wordt geregistreerd door netwerkbeveiligingsgroepen.

Met behulp van de visuals in de sjabloon u onderzoeken:

* Top Praters
* Time Series Flow Data op richting en regel besluit
* Flows by Network Interface MAC-adres
* Stromen per NSG en regel
* Stromen per bestemmingspoort

De meegeleverde sjabloon is bewerkbaar, zodat u deze wijzigen om nieuwe gegevens, visuals of query's toe te voegen die aan uw behoeften voldoen.

## <a name="setup"></a>Instellen

Voordat u begint, moet u Network Security Group Flow Logging hebben ingeschakeld op een of meer netwerkbeveiligingsgroepen in uw account. Raadpleeg het volgende artikel voor instructies voor het inschakelen van netwerkbeveiligingsstroomlogboeken: [Inleiding tot stroomlogboekregistratie voor netwerkbeveiligingsgroepen.](network-watcher-nsg-flow-logging-overview.md)

U moet ook de Power BI Desktop-client op uw machine hebben geïnstalleerd en voldoende vrije ruimte op uw machine om de logboekgegevens die in uw opslagaccount bestaan te downloaden en te laden.

![Visio-diagram][1]

### <a name="steps"></a>Stappen

1. Download en open de volgende Power BI-sjabloon in de sjabloon Power BI Desktop Application [Network Watcher PowerBI-stroomlogboeken](https://aka.ms/networkwatcherpowerbiflowlogstemplate)
1. De vereiste queryparameters invoeren
   1. **StorageAccountName** – Geeft de naam op van het opslagaccount met de NSG-stroomlogboeken die u wilt laden en visualiseren.
   1. **NumberOfLogFiles** – Geeft het aantal logbestanden op dat u wilt downloaden en visualiseren in Power BI. Als er bijvoorbeeld 50 is opgegeven, de 50 laatste logboekbestanden. Als we 2 NSG's hebben ingeschakeld en geconfigureerd om NSG-stroomlogboeken naar dit account te verzenden, kunnen de afgelopen 25 uur logboeken worden bekeken.

      ![power BI-hoofd][2]

1. Voer de toegangssleutel voor uw opslagaccount in. U geldige toegangssleutels vinden door naar uw opslagaccount te navigeren in de Azure-portal en **Toegangssleutels** te selecteren in het menu Instellingen. Klik **op Verbinden** en pas wijzigingen toe.

    ![toegangssleutels][3]

    ![toegangssleutel 2][4]

4. Uw logboeken worden gedownload en ontleed en u nu gebruik maken van de vooraf gemaakte visuals.

## <a name="understanding-the-visuals"></a>Inzicht in de visuals

In de sjabloon zijn een set visuals die helpen om de GEGEVENS van de NSG Flow Log te begrijpen. De volgende afbeeldingen tonen een voorbeeld van hoe het dashboard eruit ziet wanneer het wordt gevuld met gegevens. Hieronder onderzoeken we elk beeld in meer detail 

![powerbi][5]
 
In het visual Top Talkers worden de IP's weergegeven die de meeste verbindingen hebben geïnitieerd gedurende de opgegeven periode. De grootte van de vakken komt overeen met het relatieve aantal verbindingen. 

![toptalkers][6]

De volgende grafieken van de tijdreeksen tonen het aantal stromen over de periode. De bovenste grafiek wordt gesegmenteerd door de stroomrichting en de onderste wordt gesegmenteerd door de beslissing die is genomen (toestaan of weigeren). Met deze visual u uw verkeerstrends in de loop van de tijd onderzoeken en abnormale pieken of dalingen in verkeer of verkeerssegmentatie herkennen.

![stromenoverperiod][7]

De volgende grafieken tonen de stromen per netwerkinterface, waarbij de bovenste gesegmenteerd door stroomrichting en de lagere gesegmenteerde door genomen beslissing. Met deze informatie u inzicht krijgen in welke van uw VM's het meest verwant zijn aan anderen en of verkeer naar een specifieke VM wordt toegestaan of geweigerd.

![flowspernic][8]

De volgende donutwieldiagram toont een uitsplitsing van Flows per bestemmingspoort. Met deze informatie u de meest gebruikte bestemmingspoorten bekijken die binnen de opgegeven periode worden gebruikt.

![donut][9]

In het volgende staafdiagram ziet u de stroom door NSG en regel. Met deze informatie u zien dat de NSG's verantwoordelijk zijn voor het meeste verkeer en de verdeling van het verkeer op een NSG per regel.

![staafdiagram][10]
 
In de volgende informatiegrafieken worden informatie weergegeven over de NSG's die aanwezig zijn in de logboeken, het aantal stromen dat gedurende de periode is vastgelegd en de datum van het vroegste logboek dat is vastgelegd. Deze informatie geeft u een idee van wat NSGs worden geregistreerd en de datum bereik van stromen.

![infodiagram1][11]

![infochart2][12]

Deze sjabloon bevat de volgende slicers waarmee u alleen de gegevens bekijken waarin u het meest geïnteresseerd bent. U filteren op uw resourcegroepen, NSGs en regels. U ook filteren op 5-tuple informatie, beslissing, en het moment dat het logboek is geschreven.

![slicers][13]

## <a name="conclusion"></a>Conclusie

We hebben in dit scenario laten zien dat we met behulp van Network Security Group Flow-logboeken van Network Watcher en Power BI in staat zijn om het verkeer te visualiseren en te begrijpen. Met behulp van de meegeleverde sjabloon downloadt Power BI de logboeken rechtstreeks uit de opslag en verwerkt deze lokaal. De tijd die nodig is om de sjabloon te laden, is afhankelijk van het aantal aangevraagde bestanden en de totale grootte van de gedownloade bestanden.

Voel je vrij om deze sjabloon aan te passen aan uw behoeften. Er zijn tal van manieren waarop u Power BI gebruiken met Netwerkbeveiligingsgroepstroomlogboeken. 

## <a name="notes"></a>Opmerkingen

* Logboeken worden standaard opgeslagen in`https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/`

    * Als er andere gegevens in een andere map zijn, moeten de query's worden gewijzigd die de gegevens moeten opvragen en verwerken.

* De meegeleverde sjabloon wordt niet aanbevolen voor gebruik met meer dan 1 GB aan logboeken.

* Als u een grote hoeveelheid logboeken hebt, raden we u aan een oplossing te onderzoeken met behulp van een ander gegevensarchief zoals Data Lake of SQL-server.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het visualiseren van uw NSG-stroomlogboeken met de Elastic Stack door te gaan naar [Visualize Azure Network Watcher NSG-stroomlogboeken met behulp van open source-hulpprogramma's](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

[1]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure7.png
[8]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure8.png
[9]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure9.png
[10]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure10.png
[11]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure11.png
[12]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure12.png
[13]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure13.png
