---
title: Netwerkverkeerspatronen visualiseren met open source-tools
titleSuffix: Azure Network Watcher
description: Op deze pagina wordt beschreven hoe u Network Watcher-pakketopname gebruiken met Capanalysis om verkeerspatronen van en naar uw VM's te visualiseren.
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
ms.openlocfilehash: f36db28b58cd57b6407019b378a82632aa6c6228
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840652"
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>Netwerkverkeerspatronen van en naar uw VM's visualiseren met behulp van open-sourcetools

Packet captures bevatten netwerkgegevens waarmee u forensisch onderzoek en deep packet inspection uitvoeren. Er zijn veel open source-tools die u gebruiken om pakketopnames te analyseren om inzichten over uw netwerk te verkrijgen. Een van deze tool is CapAnalysis, een open-source packet capture visualisatie tool. Het visualiseren van packet capture data is een waardevolle manier om snel inzicht te krijgen in patronen en afwijkingen binnen uw netwerk. Visualisaties bieden ook een manier om dergelijke inzichten op een gemakkelijk verbruikbare manier te delen.

Azure's Network Watcher biedt u de mogelijkheid om gegevens vast te leggen door dat u pakketopnames op uw netwerk uitvoeren. Dit artikel biedt een wandeling door hoe te visualiseren en inzichten te krijgen van pakket opnames met behulp van CapAnalysis met Network Watcher.

## <a name="scenario"></a>Scenario

U hebt een eenvoudige webtoepassing geïmplementeerd op een VM in Azure en wilt open-sourcetools gebruiken om het netwerkverkeer te visualiseren om snel stroompatronen en mogelijke afwijkingen te identificeren. Met Network Watcher u een packet capture van uw netwerkomgeving verkrijgen en deze rechtstreeks opslaan op uw opslagaccount. CapAnalysis kan vervolgens de pakketopname rechtstreeks van de opslagblob innemen en de inhoud ervan visualiseren.

![scenario][1]

## <a name="steps"></a>Stappen

### <a name="install-capanalysis"></a>CapAnalysis installeren

Om CapAnalysis op een virtuele machine te installeren, https://www.capanalysis.net/ca/how-to-install-capanalysisu hier de officiële instructies bekijken.
Als u CapAnalysis op afstand wilt openen met ordertoegang, moet u poort 9877 op uw vm openen door een nieuwe binnenkomende beveiligingsregel toe te voegen. Voor meer informatie over het maken van regels in netwerkbeveiligingsgroepen raadpleegt u [Regels maken in een bestaande NSG.](../virtual-network/manage-network-security-group.md#create-a-security-rule) Zodra de regel is toegevoegd, moet u capanalysis kunnen openen vanuit`http://<PublicIP>:9877`

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Azure Network Watcher gebruiken om een sessie voor het vastleggen van pakketten te starten

Met Network Watcher u pakketten vastleggen om het verkeer in en uit een virtuele machine te volgen. U verwijzen naar de instructies bij [Pakketopnames beheren met Network Watcher](network-watcher-packet-capture-manage-portal.md) om een pakketopnamesessie te starten. Een pakketopname kan worden opgeslagen in een opslagblob die toegankelijk is voor CapAnalysis.

### <a name="upload-a-packet-capture-to-capanalysis"></a>Een pakketopname uploaden naar CapAnalysis
U direct een pakketopname uploaden die door de netwerkwatcher is gemaakt via het tabblad Importeren van URL en een koppeling geven naar de opslagblob waar de pakketopname is opgeslagen.

Wanneer u een koppeling naar CapAnalysis geeft, moet u een SAS-token toevoegen aan de URL van de opslagblob.  Ga hiervoor naar Gedeelde toegangshandtekening vanuit het opslagaccount, wijst de toegestane machtigingen aan en druk op de knop SAS genereren om een token te maken. Vervolgens u het SAS-token toevoegen aan de URL van de packet capture-opslagblob.

De resulterende URL ziet er ongeveer als de volgende URL:http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere


### <a name="analyzing-packet-captures"></a>Pakketopnames analyseren

CapAnalysis biedt verschillende opties om uw pakketopname te visualiseren, elk met analyse vanuit een ander perspectief. Met deze visuele samenvattingen u uw netwerkverkeerstrends begrijpen en snel ongebruikelijke activiteiten herkennen. Een paar van deze functies worden weergegeven in de volgende lijst:

1. Stroomtabellen

    In deze tabel vindt u de lijst met stromen in de pakketgegevens, de tijdstempel die is gekoppeld aan de stromen en de verschillende protocollen die aan de stroom zijn gekoppeld, evenals bron- en bestemmings-IP.

    ![capanalysis-stroompagina][5]

1. Overzicht van protocol

    Met dit deelvenster u snel de verdeling van het netwerkverkeer over de verschillende protocollen en regio's zien.

    ![overzicht van capanalysis-protocol][6]

1. statistieken

    Met dit deelvenster u netwerkverkeersstatistieken bekijken - bytes die zijn verzonden en ontvangen van ip-artikelen van de bron en bestemming, stromen voor elk van de bron- en doel-IP's, protocol dat wordt gebruikt voor verschillende stromen en de duur van stromen.

    ![capanalysis statistieken][7]

1. Geomap (Geomap)

    In dit deelvenster ziet u een kaartweergave van uw netwerkverkeer, waarbij kleuren worden geschaald naar het volume van het verkeer van elk land/regio. U gemarkeerde landen/regio's selecteren om aanvullende stroomstatistieken te bekijken, zoals het percentage gegevens dat is verzonden en ontvangen van IP's in dat land/regio.

    ![geomap][8]

1. Filters

    CapAnalysis biedt een set filters voor een snelle analyse van specifieke pakketten. U er bijvoorbeeld voor kiezen om de gegevens te filteren op protocol om specifieke inzichten te krijgen over die subset van verkeer.

    ![filters][11]

    Bezoek [https://www.capanalysis.net/ca/#about](https://www.capanalysis.net/ca/#about) voor meer informatie over alle mogelijkheden van CapAnalysis.

## <a name="conclusion"></a>Conclusie

Met de packet capture-functie van Network Watcher u de gegevens vastleggen die nodig zijn om forensisch onderzoek in het netwerk uit te voeren en uw netwerkverkeer beter te begrijpen. In dit scenario hebben we laten zien hoe pakketopnames van Network Watcher eenvoudig kunnen worden geïntegreerd met open-source visualisatietools. Door open-source tools zoals CapAnalysis te gebruiken om pakketten te visualiseren, u deep packet inspection uitvoeren en snel trends identificeren binnen uw netwerkverkeer.

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over NSG-stroomlogboeken naar [NSG Flow-logboeken](network-watcher-nsg-flow-logging-overview.md)

Meer informatie over het visualiseren van uw NSG-stroomlogboeken met Power BI door te gaan naar [Visualize NSG-stromenlogboeken met Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
<!--Image references-->

[1]: ./media/network-watcher-using-open-source-tools/figure1.png
[2]: ./media/network-watcher-using-open-source-tools/figure2.png
[3]: ./media/network-watcher-using-open-source-tools/figure3.png
[4]: ./media/network-watcher-using-open-source-tools/figure4.png
[5]: ./media/network-watcher-using-open-source-tools/figure5.png
[6]: ./media/network-watcher-using-open-source-tools/figure6.png
[7]: ./media/network-watcher-using-open-source-tools/figure7.png
[8]: ./media/network-watcher-using-open-source-tools/figure8.png
[9]: ./media/network-watcher-using-open-source-tools/figure9.png
[10]: ./media/network-watcher-using-open-source-tools/figure10.png
[11]: ./media/network-watcher-using-open-source-tools/figure11.png
