---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 06/30/2020
ms.topic: include
ms.openlocfilehash: 1b650fa5a0e9ba2f7019e6e67690d9d1fd65e72a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90894888"
---
Security Center biedt realtime bescherming van bedreigingen voor uw container omgevingen en genereert waarschuwingen voor verdachte activiteiten. U kunt deze informatie gebruiken om snel beveiligingsproblemen op te lossen en om de beveiliging van uw containers te verbeteren.

Security Center biedt bedreigings beveiliging op verschillende niveaus: 

* **Hostniveau (door Azure Defender voor servers)** : met behulp van dezelfde log Analytics-agent die Security Center gebruikt op andere vm's, bewaakt Azure Defender uw Linux AKS-knoop punten voor verdachte activiteiten, zoals detectie van webshells en verbinding met bekende verdachte IP-adressen. De agent bewaakt ook voor container-specifieke analyses, zoals het maken van geprivilegieerde containers, verdachte toegang tot API-servers en SSH-servers (Secure Shell) die binnen een docker-container worden uitgevoerd.

    >[!IMPORTANT]
    > Als u ervoor kiest om de agents op uw hosts niet te installeren, ontvangt u alleen een subset van de voor delen van bedreigings beveiliging en beveiligings waarschuwingen. U ontvangt nog steeds waarschuwingen met betrekking tot netwerk analyse en communicatie met schadelijke servers.

    Zie de [naslag tabel met waarschuwingen](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost)voor een lijst met waarschuwingen op hostniveau op AKS.


* **AKS-cluster niveau (door Azure Defender voor Kubernetes)** -op het cluster niveau is beveiliging tegen bedreigingen gebaseerd op het analyseren van de audit logboeken van Kubernetes. Schakel Azure Defender in om deze bewaking zonder **agents** in te scha kelen. Als u waarschuwingen op dit niveau wilt genereren, controleert Security Center uw door AKS beheerde services met de logboeken die zijn opgehaald door AKS. Voor beelden van gebeurtenissen op dit niveau zijn onder andere beschik bare Kubernetes-Dash boards, het maken van rollen met hoge bevoegdheden en het maken van gevoelige koppels.

    >[!NOTE]
    > Security Center genereert beveiligings waarschuwingen voor Azure Kubernetes-service acties en implementaties die worden uitgevoerd nadat de optie Kubernetes is ingeschakeld op de abonnements instellingen. 

    Zie de [naslag tabel met waarschuwingen](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)voor een lijst met waarschuwingen op AKS-cluster niveau.

Het wereld wijde team van beveiligings onderzoekers bewaakt ook voortdurend de bedreigings landschap. Ze voegen container-specifieke waarschuwingen en beveiligings problemen toe wanneer ze worden gedetecteerd.

> [!TIP]
> U kunt container waarschuwingen simuleren door de instructies in [dit blog bericht](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)te volgen.