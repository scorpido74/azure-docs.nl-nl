---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 06/30/2020
ms.topic: include
ms.openlocfilehash: ba695337a1da1e440895f6b9f6fe2eb34f5041e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800598"
---
Security Center biedt realtime bescherming van bedreigingen voor uw container omgevingen en genereert waarschuwingen voor verdachte activiteiten. U kunt deze informatie gebruiken om snel beveiligingsproblemen op te lossen en om de beveiliging van uw containers te verbeteren.

Security Center biedt bedreigings beveiliging op verschillende niveaus: 

* **Hostniveau** : de log Analytics-agent bewaakt Linux voor verdachte activiteiten. De agent activeert waarschuwingen voor verdachte activiteiten die afkomstig zijn van het knoop punt of een container die erop wordt uitgevoerd. Voor beelden van dergelijke activiteiten zijn Webshell detectie en verbinding met bekende verdachte IP-adressen.

    Voor een diep gaande inzicht in de beveiliging van uw container omgeving bewaakt de agent de container-specifieke analyses. Er worden waarschuwingen geactiveerd voor gebeurtenissen, zoals het maken van geprivilegieerde containers, verdachte toegang tot API-servers en SSH-servers (Secure Shell) die binnen een docker-container worden uitgevoerd.

    >[!IMPORTANT]
    > Als u ervoor kiest om de agents op uw hosts niet te installeren, ontvangt u alleen een subset van de voor delen van bedreigings beveiliging en beveiligings waarschuwingen. U ontvangt nog steeds waarschuwingen met betrekking tot netwerk analyse en communicatie met schadelijke servers.

    Zie de [naslag tabel met waarschuwingen](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost)voor een lijst met waarschuwingen op hostniveau op AKS.


* Op het **AKS-cluster niveau**is beveiliging tegen bedreigingen gebaseerd op het analyseren van controle logboeken van Kubernetes. Als u deze bewaking zonder **agents** wilt inschakelen, voegt u de optie Kubernetes toe aan uw abonnement op de pagina met **prijs & instellingen** (Zie [prijzen](https://docs.microsoft.com/azure/security-center/security-center-pricing)). Als u waarschuwingen op dit niveau wilt genereren, controleert Security Center uw door AKS beheerde services met de logboeken die zijn opgehaald door AKS. Voor beelden van gebeurtenissen op dit niveau zijn onder andere beschik bare Kubernetes-Dash boards, het maken van rollen met hoge bevoegdheden en het maken van gevoelige koppels.

    >[!NOTE]
    > Security Center genereert beveiligings waarschuwingen voor Azure Kubernetes-service acties en implementaties die worden uitgevoerd nadat de optie Kubernetes is ingeschakeld op de abonnements instellingen. 

    Zie de [naslag tabel met waarschuwingen](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)voor een lijst met waarschuwingen op AKS-cluster niveau.

Het wereld wijde team van beveiligings onderzoekers bewaakt ook voortdurend de bedreigings landschap. Ze voegen container-specifieke waarschuwingen en beveiligings problemen toe wanneer ze worden gedetecteerd.

> [!TIP]
> U kunt container waarschuwingen simuleren door de instructies in [dit blog bericht](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)te volgen.