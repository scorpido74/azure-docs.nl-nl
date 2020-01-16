---
title: Detectie van bedreigingen voor systeem eigen Cloud Computing in Azure Security Center | Microsoft Docs
description: In dit artikel vindt u de eigen Cloud Compute-waarschuwingen die beschikbaar zijn in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: a85ec565077ac229cd24bb7b9cf753015aa56ebf
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76024855"
---
# <a name="threat-detection-for-cloud-native-computing-in-azure-security-center"></a>Detectie van bedreigingen voor systeem eigen Cloud Computing in Azure Security Center

Als systeem eigen oplossing heeft Azure Security Center unieke zicht baarheid in interne logboeken voor het identificeren van een aanvals methodologie op meerdere doelen. Dit artikel bevat de waarschuwingen die beschikbaar zijn voor de volgende Azure-Services:

* [Azure App Service](#app-services)
* [Azure-containers](#azure-containers) 

> [!NOTE]
> Deze services zijn momenteel niet beschikbaar in azure Government en soevereine Cloud regio's.

## Azure App Service<a name="app-services"></a>

Security Center gebruikt de schaal van de cloud om aanvallen te identificeren die gericht zijn op toepassingen die meer dan App Service. Aanvallers kunnen webtoepassingen testen om zwakke plekken te vinden en misbruik te maken. Aanvragen voor toepassingen die in Azure worden uitgevoerd, worden voordat ze naar specifieke omgevingen worden doorgestuurd via verschillende gateways, waar ze worden geïnspecteerd en geregistreerd. Deze gegevens worden vervolgens gebruikt om aanvallen en aanvallers te identificeren en om nieuwe patronen te leren die later zullen worden gebruikt.

Door de zicht baarheid van Azure als een Cloud provider te gebruiken, Security Center analyseert App Service interne Logboeken om een aanvals methodologie op meerdere doelen te identificeren. Zo omvat de methodologie uitgebreide scans en gedistribueerde aanvallen. Dit type aanval is doorgaans afkomstig uit een kleine subset van IP-adressen en toont patronen van het verkennen naar vergelijk bare eind punten op meerdere hosts. De aanvallen zoeken naar een kwets bare pagina of invoeg toepassing en kunnen niet worden geïdentificeerd op basis van het standpunt van één host.

Als u een App Service-abonnement op basis van Windows uitvoert, heeft Security Center ook toegang tot de onderliggende sandboxes en virtuele machines. Samen met de hierboven vermelde logboek gegevens kan de infra structuur het verhaal vertellen van een nieuwe aanval die in het wild wordt circuleren om inbreuk te kunnen vormen op de klant machines. Daarom, zelfs als Security Center wordt geïmplementeerd nadat een web-app is misbruikt, kan het mogelijk worden doorlopende aanvallen te detecteren.

Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-azureappserv)voor een overzicht van de waarschuwingen voor Azure app service.

## Azure-containers<a name="azure-containers"></a>

Security Center voorziet in realtime detectie van bedreigingen voor uw container omgevingen en genereert waarschuwingen voor verdachte activiteiten. U kunt deze informatie gebruiken om snel beveiligingsproblemen op te lossen en om de beveiliging van uw containers te verbeteren.

We detecteren bedreigingen op verschillende niveaus: 

* **Host level** -Security Center Agent (Zie de [prijs](security-center-pricing.md) informatie voor de Standard-laag) wordt Linux gecontroleerd op verdachte activiteiten. De agent activeert waarschuwingen voor verdachte activiteiten die afkomstig zijn van het knoop punt of een container die erop wordt uitgevoerd. Voor beelden van dergelijke activiteiten zijn Webshell detectie en verbinding met bekende verdachte IP-adressen.

    Voor een diep gaande inzicht in de beveiliging van uw container omgeving bewaakt de agent de container-specifieke analyses. Er worden waarschuwingen geactiveerd voor gebeurtenissen, zoals het maken van geprivilegieerde containers, verdachte toegang tot API-servers en SSH-servers (Secure Shell) die binnen een docker-container worden uitgevoerd.

    >[!NOTE]
    > Als u ervoor kiest om de agents op uw hosts niet te installeren, ontvangt u alleen een subset van de voor delen en waarschuwingen voor bedreigingen detectie. U ontvangt nog steeds waarschuwingen met betrekking tot netwerk analyse en communicatie met schadelijke servers.

    Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-containerhost)voor een lijst met waarschuwingen op hostniveau.


* Voor **AKS-cluster niveau**is er sprake van detectie van bedreigingen op basis van de analyse van Kubernetes-controle Logboeken. Als u deze bewaking zonder **agents** wilt inschakelen, voegt u de optie Kubernetes toe aan uw abonnement op de pagina met **prijs & instellingen** (Zie [prijzen](security-center-pricing.md)). Als u waarschuwingen op dit niveau wilt genereren, controleert Security Center uw door AKS beheerde services met de logboeken die zijn opgehaald door AKS. Voor beelden van gebeurtenissen op dit niveau zijn onder andere weer gegeven Kubernetes-Dash boards, het maken van rollen met hoge bevoegdheden en het maken van gevoelige koppels.

    >[!NOTE]
    > Security Center genereert detectie waarschuwingen voor Azure Kubernetes-service acties en implementaties die worden uitgevoerd nadat de optie Kubernetes is ingeschakeld op de abonnements instellingen. 

    Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-akscluster)voor een lijst met waarschuwingen op AKS-cluster niveau.

Het wereld wijde team van beveiligings onderzoekers bewaakt ook voortdurend de bedreigings landschap. Ze voegen container-specifieke waarschuwingen en beveiligings problemen toe wanneer ze worden gedetecteerd.

## <a name="next-steps"></a>Volgende stappen

* Zie [app service-plannen](https://azure.microsoft.com/pricing/details/app-service/plans/)voor meer informatie over het plannen van app service.