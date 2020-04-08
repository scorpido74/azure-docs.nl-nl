---
title: Aanbevolen procedures voor podbeveiliging
titleSuffix: Azure Kubernetes Service
description: Meer informatie over de aanbevolen procedures voor ontwikkelaars voor het beveiligen van pods in Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: zarhoads
ms.openlocfilehash: 1f093b5276ee7ab334043e57f97a108267c32c87
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804381"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Aanbevolen procedures voor podbeveiliging in Azure Kubernetes Service (AKS)

Bij het ontwikkelen en uitvoeren van toepassingen in Azure Kubernetes Service (AKS) is de beveiliging van uw pods een belangrijke overweging. Uw toepassingen moeten worden ontworpen voor het principe van het minste aantal vereiste bevoegdheden. Het beveiligen van privégegevens is top of mind voor klanten. U wilt niet dat referenties zoals databaseverbindingen, sleutels of geheimen en certificaten worden blootgesteld aan de buitenwereld, waar een aanvaller misbruik kan maken van deze geheimen voor kwaadaardige doeleinden. Voeg ze niet toe aan uw code of sluit ze niet in in uw containerafbeeldingen. Deze aanpak zou leiden tot een risico voor blootstelling en beperken de mogelijkheid om deze referenties te draaien als de container beelden moeten worden herbouwd.

Dit best practices artikel richt zich op hoe pods in AKS te beveiligen. Procedures voor:

> [!div class="checklist"]
> * De beveiligingscontext van de pod gebruiken om de toegang tot processen en services of escalatie van bevoegdheden te beperken
> * Verifiëren met andere Azure-resources met podbeheerde identiteiten
> * Referenties aanvragen en ophalen uit een digitale kluis, zoals Azure Key Vault

U ook de aanbevolen procedures voor [clusterbeveiliging][best-practices-cluster-security] en voor [containerimagebeheer][best-practices-container-image-management]lezen.

## <a name="secure-pod-access-to-resources"></a>Beveiligde podtoegang tot bronnen

**Richtlijnen voor aanbevolen procedures** - Als u als een andere gebruiker of groep wilt worden uitgevoerd en de toegang tot de onderliggende knooppuntprocessen en -services wilt beperken, definieert u de contextinstellingen voor podbeveiliging. Wijs het minste aantal vereiste bevoegdheden toe.

Als u uw toepassingen correct wilt uitvoeren, moeten pods worden uitgevoerd als een gedefinieerde gebruiker of groep en niet als *root.* Met `securityContext` de voor een pod of container u instellingen definiëren, zoals *runAsUser* of *fsGroup* om de juiste machtigingen aan te nemen. Wijs alleen de vereiste gebruikers- of groepsmachtigingen toe en gebruik de beveiligingscontext niet als een middel om extra machtigingen aan te nemen. De *runAsUser,* privilege escalation en andere Linux-instellingen zijn alleen beschikbaar op Linux-knooppunten en -pods.

Wanneer u als niet-hoofdgebruiker wordt uitgevoerd, kunnen containers niet worden gekoppeld aan de bevoorrechte poorten onder 1024. In dit scenario kan Kubernetes Services worden gebruikt om te verhullen dat een app op een bepaalde poort wordt uitgevoerd.

Een podbeveiligingscontext kan ook aanvullende mogelijkheden of machtigingen definiëren voor toegang tot processen en services. De volgende definities van de gemeenschappelijke beveiligingscontext kunnen worden ingesteld:

* **allowPrivilegeEscalation** definieert of de pod *rootrechten* kan aannemen. Ontwerp uw toepassingen zodat deze instelling altijd is ingesteld op *false.*
* **Linux-mogelijkheden** laten de pod toegang tot onderliggende knooppuntprocessen. Zorg ervoor dat u deze mogelijkheden toegeeft. Wijs het minste aantal benodigde bevoegdheden toe. Zie [Linux-mogelijkheden][linux-capabilities]voor meer informatie.
* **SELinux-labels** is een Linux-kernelbeveiligingsmodule waarmee u toegangsbeleid voor services, processen en bestandssysteemtoegang definiëren. Wijs nogmaals het minste aantal benodigde bevoegdheden toe. Zie [SELinux-opties voor][selinux-labels] meer informatie in Kubernetes

In het volgende voorbeeldpod YAML-manifest worden de instellingen voor beveiligingscontextingesteld om te definiëren:

* Pod wordt uitgevoerd als gebruikersnaam *1000* en onderdeel van groeps-ID *2000*
* Kan de bevoegdheden voor het gebruik niet escaleren`root`
* Met Linux-mogelijkheden toegang tot netwerkinterfaces en de real-time (hardware)klok van de host

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  containers:
    - name: security-context-demo
      image: nginx:1.15.5
    securityContext:
      runAsUser: 1000
      fsGroup: 2000
      allowPrivilegeEscalation: false
      capabilities:
        add: ["NET_ADMIN", "SYS_TIME"]
```

Werk samen met uw clusteroperator om te bepalen welke beveiligingscontextinstellingen u nodig hebt. Probeer uw toepassingen te ontwerpen om extra machtigingen te minimaliseren en toegang te krijgen tot de pod. Er zijn extra beveiligingsfuncties om de toegang te beperken met behulp van AppArmor en seccomp (secure computing) die kunnen worden geïmplementeerd door clusteroperators. Zie [Toegang tot containers beveiligen tot resources voor][apparmor-seccomp]meer informatie.

## <a name="limit-credential-exposure"></a>Blootstelling aan referenties beperken

**Richtlijnen voor aanbevolen procedures** - definieer geen referenties in uw toepassingscode. Gebruik beheerde identiteiten voor Azure-bronnen om uw pod toegang te geven tot andere bronnen. Een digitale kluis, zoals Azure Key Vault, moet ook worden gebruikt om digitale sleutels en referenties op te slaan en op te halen. Pod beheerde identiteiten is alleen bedoeld voor gebruik met Linux-pods en containerafbeeldingen.

Als u het risico wilt beperken dat referenties in uw toepassingscode worden weergegeven, vermijdt u het gebruik van vaste of gedeelde referenties. Referenties of sleutels mogen niet direct in uw code worden opgenomen. Als deze referenties worden weergegeven, moet de toepassing worden bijgewerkt en opnieuw worden geïmplementeerd. Een betere aanpak is om pods hun eigen identiteit en manier te geven om zichzelf te verifiëren, of om automatisch referenties uit een digitale kluis op te halen.

Met de volgende [bijbehorende AKS-opensourceprojecten][aks-associated-projects] u pods automatisch verifiëren of referenties en sleutels aanvragen uit een digitale kluis:

* Beheerde identiteiten voor Azure-resources en
* Azure Key Vault FlexVol-stuurprogramma

Gekoppelde AKS-opensourceprojecten worden niet ondersteund door technische ondersteuning van Azure. Ze worden verstrekt om feedback en bugs van onze community te verzamelen. Deze projecten worden niet aanbevolen voor productiegebruik.

### <a name="use-pod-managed-identities"></a>Podbeheerde identiteiten gebruiken

Met een beheerde identiteit voor Azure-resources kan een pod zichzelf verifiëren tegen Azure-services die deze ondersteunen, zoals Opslag of SQL. Aan de pod is een Azure-identiteit toegewezen waarmee ze kunnen verifiëren in Azure Active Directory en een digitaal token kunnen ontvangen. Dit digitale token kan worden gepresenteerd aan andere Azure-services die controleren of de pod is geautoriseerd om toegang te krijgen tot de service en de vereiste acties uit te voeren. Deze aanpak betekent dat er bijvoorbeeld geen geheimen nodig zijn voor databaseverbindingen. De vereenvoudigde werkstroom voor door een pod beheerde identiteit wordt weergegeven in het volgende diagram:

![Vereenvoudigde werkstroom voor podbeheerde identiteit in Azure](media/developer-best-practices-pod-security/basic-pod-identity.png)

Met een beheerde identiteit hoeft uw toepassingscode geen referenties op te nemen om toegang te krijgen tot een service, zoals Azure Storage. Zoals elke pod zich verifieert met een eigen identiteit, zodat u de toegang controleren en controleren. Als uw toepassing verbinding maakt met andere Azure-services, gebruikt u beheerde identiteiten om hergebruik van referenties en risico op blootstelling te beperken.

Zie [Een AKS-cluster configureren om door een pod beheerde identiteiten en met uw toepassingen te gebruiken voor][aad-pod-identity] meer informatie over podidentiteiten.

### <a name="use-azure-key-vault-with-flexvol"></a>Azure Key Vault gebruiken met FlexVol

Beheerde podidentiteiten werken geweldig om te verifiëren tegen ondersteuning van Azure-services. Voor uw eigen services of toepassingen zonder beheerde identiteiten voor Azure-bronnen, verifieert u nog steeds met referenties of sleutels. Een digitale kluis kan worden gebruikt om deze referenties op te slaan.

Wanneer toepassingen een referentie nodig hebben, communiceren ze met de digitale kluis, halen ze de nieuwste referenties op en maken ze verbinding met de vereiste service. Azure Key Vault kan deze digitale kluis zijn. De vereenvoudigde werkstroom voor het ophalen van een referentie uit Azure Key Vault met podbeheerde identiteiten wordt weergegeven in het volgende diagram:

![Vereenvoudigde werkstroom voor het ophalen van een referentie uit Key Vault met behulp van een door een pod beheerde identiteit](media/developer-best-practices-pod-security/basic-key-vault-flexvol.png)

Met Key Vault slaat u geheimen op, zoals referenties, opslagaccountsleutels of certificaten. U Azure Key Vault integreren met een AKS-cluster met een FlexVolume. Met het FlexVolume-stuurprogramma kan het AKS-cluster referenties native ophalen uit Key Vault en deze veilig alleen aan de aanvragende pod verstrekken. Werk samen met uw clusteroperator om het Key Vault FlexVol-stuurprogramma te implementeren op de AKS-knooppunten. U een door een pod beheerde identiteit gebruiken om toegang tot Key Vault aan te vragen en de referenties op te halen die u nodig hebt via het FlexVolume-stuurprogramma.

Azure Key Vault met FlexVol is bedoeld voor gebruik met toepassingen en services die worden uitgevoerd op Linux-pods en -knooppunten.

## <a name="next-steps"></a>Volgende stappen

Dit artikel richtte zich op het beveiligen van uw pods. Zie de volgende artikelen om een aantal van deze gebieden te implementeren:

* [Beheerde identiteiten gebruiken voor Azure-resources met AKS][aad-pod-identity]
* [Azure Key Vault integreren met AKS][aks-keyvault-flexvol]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo
[aks-keyvault-flexvol]: https://github.com/Azure/kubernetes-keyvault-flexvol
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.12/#selinuxoptions-v1-core
[aks-associated-projects]: https://github.com/Azure/AKS/blob/master/previews.md#associated-projects

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources
