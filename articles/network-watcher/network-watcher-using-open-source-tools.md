---
title: Netwerk verkeers patronen visualiseren met open-source-hulpprogram ma's
titleSuffix: Azure Network Watcher
description: Op deze pagina wordt beschreven hoe u Network Watcher pakket Capture kunt gebruiken met Capanalysis om verkeers patronen van en naar uw virtuele machines te visualiseren.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 936d881b-49f9-4798-8e45-d7185ec9fe89
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 3baf73eed09e6880820952b8457526b6c21dd83b
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277766"
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>Netwerk verkeers patronen visualiseren van en naar uw Vm's met open source-hulpprogram ma's

Pakket opnames bevatten netwerk gegevens die u in staat stellen netwerk forensische en diepe pakket inspectie uit te voeren. Er zijn veel hulp middelen voor het openen van bronnen die u kunt gebruiken om pakket opnames te analyseren om inzicht te krijgen in uw netwerk. Een dergelijk hulp programma is CapAnalysis, een open-source programma voor het maken van een pakket opname. Het visualiseren van pakket opname gegevens is een waardevolle manier om snel inzichten te verkrijgen over patronen en afwijkingen in uw netwerk. Visualisaties bieden ook een manier om dergelijke inzichten te delen op een eenvoudig te gebruiken manier.

De Network Watcher van Azure biedt u de mogelijkheid om gegevens vast te leggen, zodat u pakket opnames kunt uitvoeren op uw netwerk. In dit artikel vindt u een overzicht van het visualiseren en verkrijgen van inzichten op basis van pakket opnamen met behulp van CapAnalysis met Network Watcher.

## <a name="scenario"></a>Scenario

U hebt een eenvoudige webtoepassing die is geïmplementeerd op een virtuele machine in Azure en open source-hulpprogram ma's wilt gebruiken om het netwerk verkeer te visualiseren om snel stroom patronen en mogelijke afwijkingen te identificeren. Met Network Watcher kunt u een pakket opname van uw netwerk omgeving verkrijgen en deze rechtstreeks opslaan in uw opslag account. CapAnalysis kan vervolgens de pakket opname rechtstreeks vanuit de opslag-BLOB opnemen en de inhoud ervan visualiseren.

![scenario][1]

## <a name="steps"></a>Stappen

### <a name="install-capanalysis"></a>CapAnalysis installeren

Als u CapAnalysis op een virtuele machine wilt installeren, raadpleegt u de volgende officiële instructies https://www.capanalysis.net/ca/how-to-install-capanalysis.
Om toegang tot CapAnalysis op afstand te krijgen, moet u poort 9877 op uw virtuele machine openen door een nieuwe regel voor binnenkomende beveiliging toe te voegen. Zie [regels maken in een bestaande NSG](../virtual-network/manage-network-security-group.md#create-a-security-rule)voor meer informatie over het maken van regels in netwerk beveiligings groepen. Zodra de regel is toegevoegd, kunt u toegang krijgen tot CapAnalysis van `http://<PublicIP>:9877`

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Azure Network Watcher gebruiken om een pakket opname sessie te starten

Met Network Watcher kunt u pakketten vastleggen om verkeer in en uit een virtuele machine bij te houden. Raadpleeg de instructies op [pakket opnames beheren met Network Watcher](network-watcher-packet-capture-manage-portal.md) om een pakket opname sessie te starten. Een pakket opname kan worden opgeslagen in een opslag-BLOB zodat deze kan worden geopend door CapAnalysis.

### <a name="upload-a-packet-capture-to-capanalysis"></a>Een pakket opname uploaden naar CapAnalysis
U kunt rechtstreeks een pakket opname van Network Watcher uploaden met behulp van het tabblad importeren vanuit URL en een koppeling naar de opslag-BLOB opgeven waar de pakket opname wordt opgeslagen.

Wanneer u een koppeling naar CapAnalysis, moet u een SAS-token toevoegen aan de URL van de opslag-blob.  Hiertoe gaat u naar de hand tekening voor gedeelde toegang vanuit het opslag account, wijst u de toegestane machtigingen aan en klikt u op de knop SAS genereren om een token te maken. U kunt vervolgens de SAS-token toevoegen aan de URL voor de opslag-blob van de pakket opname.

De resulterende URL ziet er ongeveer als volgt uit: http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere


### <a name="analyzing-packet-captures"></a>Pakket opnames analyseren

CapAnalysis biedt verschillende opties voor het visualiseren van de pakket opname, die elke analyse van een ander perspectief levert. Met deze visuele samen vatting kunt u inzicht krijgen in uw netwerk verkeer trends en de ongebruikelijke activiteiten snel herkennen. Enkele van deze functies worden weer gegeven in de volgende lijst:

1. Stroom tabellen

    Deze tabel bevat een lijst met stromen in de pakket gegevens, het tijds tempel dat is gekoppeld aan de stromen en de verschillende protocollen die aan de stroom zijn gekoppeld, evenals de bron-en doel-IP.

    ![pagina capanalysis-stroom][5]

1. Protocol overzicht

    In dit deel venster kunt u snel de distributie van netwerk verkeer via de verschillende protocollen en geographs bekijken.

    ![overzicht van capanalysis-Protocol][6]

1. Statistieken

    In dit deel venster kunt u de statistieken van het netwerk verkeer weer geven – verzonden en ontvangen bytes van bron-en doel-Ip's, stromen voor elk van de bron-en doel-Ip's, het protocol dat wordt gebruikt voor verschillende stromen en de duur van stromen.

    ![statistieken voor capanalysis][7]

1. geomap

    Dit deel venster bevat een kaart weergave van uw netwerk verkeer, waarbij kleuren worden geschaald naar het volume verkeer van elk land/regio. U kunt gemarkeerde landen/regio's selecteren om aanvullende stroom statistieken weer te geven, zoals de verhouding van de gegevens die worden verzonden en ontvangen van IP-adressen in dat land/regio.

    ![geomap][8]

1. Filters

    CapAnalysis biedt een set filters voor een snelle analyse van specifieke pakketten. U kunt er bijvoorbeeld voor kiezen om de gegevens te filteren op protocol om specifieke inzichten te verkrijgen over die subset van verkeer.

    ![filters][11]

    Ga naar [https://www.capanalysis.net/ca/#about](https://www.capanalysis.net/ca/#about) voor meer informatie over de mogelijkheden van CapAnalysis.

## <a name="conclusion"></a>Conclusie

Met de functie voor het vastleggen van pakketten van Network Watcher kunt u de gegevens vastleggen die nodig zijn om netwerk forensische uit te voeren en uw netwerk verkeer beter te begrijpen. In dit scenario laten we zien hoe pakket opnames van Network Watcher eenvoudig kunnen worden geïntegreerd met open-source visualisatie hulpprogramma's. Door open-source-hulpprogram ma's zoals CapAnalysis te gebruiken om pakketten te visualiseren, kunt u grondige pakket inspecties uitvoeren en snel trends binnen uw netwerk verkeer identificeren.

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over NSG-stroom logboeken naar [NSG flow-logboeken](network-watcher-nsg-flow-logging-overview.md)

Meer informatie over het visualiseren van uw NSG-stroom logboeken met Power BI door [visuals NSG flow-logboeken te bezoeken met Power bi](network-watcher-visualize-nsg-flow-logs-power-bi.md)
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
