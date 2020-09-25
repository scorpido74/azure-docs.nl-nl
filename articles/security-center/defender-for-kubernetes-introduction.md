---
title: 'Azure Defender voor Kubernetes: de voor delen en functies'
description: Meer informatie over de voor delen en functies van Azure Defender voor Kubernetes.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: a7fb97f1036515f82fa3e61c18f95eadf0af704d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301700"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Inleiding tot Azure Defender voor Kubernetes

Azure Kubernetes service (AKS) is de beheerde service van micro soft voor het ontwikkelen, implementeren en beheren van toepassingen in containers.

Azure Security Center en AKS vormen de beste Cloud-native Kubernetes-beveiligings aanbieding en samen vormen deze een omgevings beveiliging, bescherming van werk belastingen en runtime-beveiliging zoals hieronder wordt beschreven.

Voor detectie van bedreigingen voor uw Kubernetes-clusters schakelt u **Azure Defender voor Kubernetes**in.

Detectie van bedreigingen op hostniveau voor uw Linux AKS-knoop punten is beschikbaar als u [Azure Defender voor servers](defender-for-servers-introduction.md)inschakelt.

## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Release status:|Algemeen beschikbaar (GA)|
|Koers|**Azure Defender voor Kubernetes** wordt gefactureerd, zoals wordt weer gegeven op [de pagina met prijzen](security-center-pricing.md)|
|Vereiste rollen en machtigingen:|**Beveiligings beheerder** kan waarschuwingen negeren.<br>**Beveiligings lezer** kan bevindingen weer geven.|
|Clouds|![Yes](./media/icons/yes-icon.png) Commerciële Clouds<br>![No](./media/icons/no-icon.png) National/soeverein (US Gov, China gov, andere gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Wat zijn de voor delen van Azure Defender voor Kubernetes?

### <a name="run-time-protection"></a>Run-time beveiliging

Dankzij doorlopende analyse van de volgende AKS-bronnen biedt Security Center realtime beveiliging tegen bedreigingen voor uw container omgevingen en worden er waarschuwingen gegenereerd voor bedreigingen en schadelijke activiteiten die zijn gedetecteerd op het niveau van de host *en* het AKS-cluster. U kunt deze informatie gebruiken om snel beveiligingsproblemen op te lossen en om de beveiliging van uw containers te verbeteren.

Security Center biedt bedreigings beveiliging op verschillende niveaus: 

- **Hostniveau (door Azure Defender voor servers)** : met behulp van dezelfde log Analytics-agent die Security Center gebruikt op andere vm's, bewaakt Azure Defender uw Linux AKS-knoop punten voor verdachte activiteiten, zoals detectie van webshells en verbinding met bekende verdachte IP-adressen. De agent bewaakt ook voor container-specifieke analyses, zoals het maken van geprivilegieerde containers, verdachte toegang tot API-servers en SSH-servers (Secure Shell) die binnen een docker-container worden uitgevoerd.

    Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-containerhost)voor een lijst met waarschuwingen op hostniveau op AKS.

    >[!IMPORTANT]
    > Als u ervoor kiest om de agents op uw hosts niet te installeren, ontvangt u alleen een subset van de voor delen van bedreigings beveiliging en beveiligings waarschuwingen. U ontvangt nog steeds waarschuwingen met betrekking tot netwerk analyse en communicatie met schadelijke servers.

- **AKS-cluster niveau (door Azure Defender voor Kubernetes)** -op het cluster niveau is beveiliging tegen bedreigingen gebaseerd op het analyseren van de audit logboeken van Kubernetes. Schakel Azure Defender in om deze bewaking zonder **agents** in te scha kelen. Als u waarschuwingen op dit niveau wilt genereren, controleert Security Center uw door AKS beheerde services met de logboeken die zijn opgehaald door AKS. Voor beelden van gebeurtenissen op dit niveau zijn onder andere beschik bare Kubernetes-Dash boards, het maken van rollen met hoge bevoegdheden en het maken van gevoelige koppels.

    Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-akscluster)voor een lijst met waarschuwingen op AKS-cluster niveau.

    >[!NOTE]
    > Security Center genereert beveiligings waarschuwingen voor Azure Kubernetes-service acties en implementaties die worden uitgevoerd nadat de optie Kubernetes is ingeschakeld op de abonnements instellingen. 

Het wereld wijde team van beveiligings onderzoekers bewaakt ook voortdurend de bedreigings landschap. Ze voegen container-specifieke waarschuwingen en beveiligings problemen toe wanneer ze worden gedetecteerd.

> [!TIP]
> U kunt container waarschuwingen simuleren door de instructies in [dit blog bericht](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)te volgen.



## <a name="how-does-security-centers-kubernetes-protection-work"></a>Hoe werkt de Kubernetes-beveiliging van Security Center?

Hieronder ziet u een diagram op hoog niveau van de interactie tussen Azure Security Center, de Azure Kubernetes-service en Azure Policy.

U kunt zien dat de items die door Security Center zijn ontvangen en geanalyseerd:

- controle logboeken van de API-server
- onbewerkte beveiligings gebeurtenissen van de Log Analytics agent
- informatie over de cluster configuratie van het AKS-cluster
- configuratie van de werk belasting van Azure Policy (via de **Azure Policy-invoeg toepassing voor Kubernetes**). [Meer informatie over de aanbevolen procedures voor workload Protection met behulp van Kubernetes Admission Control](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Architectuur op hoog niveau van de interactie tussen Azure Security Center, de Azure Kubernetes-service en Azure Policy" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::




## <a name="azure-defender-for-kubernetes---faq"></a>Veelgestelde vragen over Azure Defender voor Kubernetes

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Kan ik nog steeds AKS-beveiligingen krijgen zonder de Log Analytics-agent?

Zoals hierboven vermeld, biedt het optionele **Azure Defender for Kubernetes** -abonnement beveiliging op cluster niveau, de log Analytics-agent van **Azure Defender voor servers** beveiligt uw knoop punten. 

We raden u aan beide te implementeren voor de meest volledige beveiliging.

Als u ervoor kiest om de agent niet op uw hosts te installeren, ontvangt u alleen een subset van de voor delen van bedreigings beveiliging en beveiligings waarschuwingen. U ontvangt nog steeds waarschuwingen met betrekking tot netwerk analyse en communicatie met schadelijke servers.


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over de Kubernetes-beveiliging van Security Center, waaronder Azure Defender voor Kubernetes. 

Raadpleeg de volgende artikelen voor gerelateerde materialen: 

- [Azure Defender inschakelen](security-center-pricing.md)
- [Waarschuwingen exporteren naar een Azure Sentinel of een SIEM van derden](continuous-export.md)
- [Naslag tabel met waarschuwingen](alerts-reference.md)