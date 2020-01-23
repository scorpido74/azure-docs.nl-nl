---
title: Aanbevolen procedures Developer - Pod-beveiliging in Azure Kubernetes Services (AKS)
description: Informatie over de ontwikkelaar van aanbevolen procedures voor het beveiligen van pods in Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: zarhoads
ms.openlocfilehash: 17f281aeb2ef3f1f32f3e13fe66fe8b74b1d9116
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547673"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Best practices voor beveiliging van de schil in Azure Kubernetes Service (AKS)

Als u toepassingen ontwikkelen en in Azure Kubernetes Service (AKS uitvoeren), is de beveiliging van uw schillen belangrijkste overweging. Uw toepassingen moeten worden ontworpen voor het principe van het minst vereiste aantal bevoegdheden. Uw persoonlijke gegevens veilig te houden is topprioriteit hebben voor klanten. U wilt niet dat referenties zoals database verbindings reeksen, sleutels of geheimen en certificaten die worden blootgesteld aan de buiten wereld waar een aanvaller kan profiteren van deze geheimen voor schadelijke doel einden. Geen toe te voegen aan uw code of insluit in uw containerinstallatiekopieën. Deze methode wilt maken van een risico voor blootstelling en beperken de mogelijkheid om te roteren deze referenties als de containerinstallatiekopieën moet opnieuw worden opgebouwd.

Dit artikel Best practices is gericht op het beveiligen van AKS. Procedures voor:

> [!div class="checklist"]
> * Beveiligingscontext pod gebruiken om te beperken van toegang tot processen en services of uitbreiding van bevoegdheden
> * Verifiëren met andere Azure-resources met gebruikmaking van identiteiten pod beheerd
> * Aanvragen en referenties worden opgehaald uit een digitale kluis, zoals Azure Key Vault

U kunt ook de aanbevolen procedures voor [cluster beveiliging][best-practices-cluster-security] lezen en voor [container installatie kopie beheer][best-practices-container-image-management].

## <a name="secure-pod-access-to-resources"></a>Beveiligde pod toegang tot resources

**Aanbevolen procedurerichtlijn** : als u wilt uitvoeren als een andere gebruiker of groep en de limiet voor toegang tot het onderliggende knooppunt processen en services, het definiëren van de schil context beveiligingsinstellingen. Toewijzen van de minste aantal bevoegdheden die zijn vereist.

Voor uw toepassingen goed te laten werken, schillen mag worden uitgevoerd als een opgegeven gebruiker of groep en niet als *hoofdmap*. De `securityContext` voor een pod of container kunt u definiëren van instellingen zoals *uitvoerenals* of *fsGroup* aan wordt ervan uitgegaan dat de juiste machtigingen. Alleen de gebruiker of groepsmachtigingen toewijzen, en de beveiligingscontext niet gebruiken als een manier om u te wordt ervan uitgegaan dat aanvullende machtigingen. De instellingen voor *runAsUser*, bevoegdheids escalatie en andere Linux-capaciteiten zijn alleen beschikbaar op Linux-knoop punten en peulen.

Wanneer u als een niet-hoofdgebruiker uitvoert, containers kunnen geen binding met de bevoorrechte poorten onder 1024. In dit scenario kan vervalsen van het feit dat een app wordt uitgevoerd op een bepaalde poort Kubernetes-Services worden gebruikt.

Een beveiligingscontext schil kunt ook aanvullende mogelijkheden of machtigingen voor toegang tot processen en services definiëren. De volgende definities voor algemene beveiliging context kunnen worden ingesteld:

* **allowPrivilegeEscalation** Hiermee definieert u of de schil kunt ervan uitgaan dat *hoofdmap* bevoegdheden. Ontwerp van uw toepassingen, zodat deze instelling is altijd ingesteld op *false*.
* **Linux-mogelijkheden** kunt u de schil toegang tot de onderliggende processen van het knooppunt. Wees voorzichtig met het toewijzen van deze mogelijkheden. Toewijzen van de minste aantal bevoegdheden die nodig zijn. Zie [Linux-mogelijkheden][linux-capabilities]voor meer informatie.
* **SELinux labels** is een Linux-kernel security-module waarmee u toegangsbeleid voor toegang tot services, processen en bestandssysteem definiëren. Opnieuw toewijzen van de minste aantal bevoegdheden die nodig zijn. Zie [selinux-opties in Kubernetes][selinux-labels] voor meer informatie.

Het volgende voorbeeld pod YAML-manifest stelt beveiliging context instellingen op te geven:

* Er wordt een Pod uitgevoerd als gebruikers-ID *1000* en een deel van de groeps-ID *2000*
* Kan geen bevoegdheden voor het gebruik van escaleren `root`
* Linux-mogelijkheden voor toegang tot netwerkinterfaces en klok van de realtime (hardware) van de host kunt

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

Met uw cluster operator om te bepalen welke beveiliging context-instellingen die u moet werken. Probeer te ontwerpen van uw toepassingen te minimaliseren aanvullende machtigingen en toegang tot die de schil is vereist. Er zijn aanvullende beveiligingsfuncties beschikbaar om te beperken van toegang met behulp van AppArmor en seccomp (beveiligde computers) die kan worden geïmplementeerd met clusteroperators. Zie [Secure container Access to resources][apparmor-seccomp](Engelstalig) voor meer informatie.

## <a name="limit-credential-exposure"></a>Referentieblootstelling limiet

**Aanbevolen procedurerichtlijn** -referenties niet definiëren in de code van uw toepassing. Gebruik beheerde identiteiten voor Azure-resources om te laten uw schil aanvraag voor toegang tot andere resources. Een digitale kluis, zoals Azure Key Vault, moet ook worden gebruikt voor het opslaan en ophalen van digitale sleutels en referenties. Beheerde identiteiten van pod zijn alleen bedoeld voor gebruik met Linux-en container installatie kopieën.

Als u wilt beperken het risico van referenties die worden weergegeven in de code van uw toepassing, te voorkomen dat het gebruik van vaste of gedeelde referenties. Referenties of sleutels mag niet rechtstreeks in uw code worden opgenomen. Als deze referenties worden weergegeven, wordt de toepassing moet worden bijgewerkt en opnieuw geïmplementeerd. Er is een betere benadering schillen geven hun eigen identiteit en de manier om te verifiëren zelf of referenties automatisch worden opgehaald uit een digitale-kluis.

Met de volgende [gekoppelde open-source projecten van AKS][aks-associated-projects] kunt u automatisch peul verifiëren of referenties en sleutels van een digitale kluis aanvragen:

* Identiteiten voor een Azure-resources, beheerde en
* Azure Key Vault FlexVol-stuurprogramma

Gerelateerde AKS open-source projecten worden niet ondersteund door de technische ondersteuning van Azure. Ze zijn bedoeld om feedback en bugs van onze community te verzamelen. Deze projecten worden niet aanbevolen voor productie gebruik.

### <a name="use-pod-managed-identities"></a>Gebruik pod beheerde identiteiten

Met een beheerde identiteit voor Azure-resources kan een pod zichzelf verifiëren voor Azure-Services die dit ondersteunen, zoals opslag of SQL. De schil krijgt een Azure-identiteit waarmee ze worden geverifieerd bij Azure Active Directory en een digitale token kunnen ontvangen. Deze digitale token kan worden weergegeven aan andere Azure-services te controleren of de schil is gemachtigd voor toegang tot de service en de vereiste acties uitvoeren. Deze methode betekent dat er geen geheimen vereist voor tekenreeksen voor databaseverbindingen, bijvoorbeeld zijn. De vereenvoudigde werkstroom voor pod beheerde identiteit wordt weergegeven in het volgende diagram:

![Vereenvoudigde werkstroom voor pod beheerde identiteit in Azure](media/developer-best-practices-pod-security/basic-pod-identity.png)

Met een beheerde identiteit hoeft de code van uw toepassing niet te zijn referenties voor toegang tot een service, zoals Azure Storage. Omdat elke pod wordt geverifieerd met een eigen identiteit, dus u kunt controleren en toegang beoordelen. Als uw toepassing verbinding met andere Azure-services maakt, gebruik van beheerde identiteiten limiet referentie hergebruik en beveiligingsrisico wordt beperkt.

Zie [Configure a AKS cluster to use pod Managed Identities and with your Applications][aad-pod-identity] (Engelstalig) voor meer informatie over pod-identiteiten.

### <a name="use-azure-key-vault-with-flexvol"></a>Azure Key Vault gebruiken met FlexVol

Beheerde pod identiteiten werkt goed voor verificatie op basis van ondersteunende Azure-services. Voor uw eigen services of toepassingen zonder beheerde identiteiten voor Azure-resources verifiëren u nog steeds met behulp van referenties of sleutels. Een digitale kluis kan worden gebruikt voor het opslaan van deze referenties.

Wanneer toepassingen moeten een referentie ze communiceren met de digitale kluis, de meest recente referenties ophalen en maak verbinding met de vereiste service. Azure Key Vault kan deze digitale kluis zijn. De vereenvoudigde werkstroom voor het ophalen van een referentie uit Azure Key Vault met gebruikmaking van identiteiten pod beheerd wordt weergegeven in het volgende diagram:

![Vereenvoudigde werkstroom voor het ophalen van een referentie uit Key Vault met behulp van een schil beheerde identiteit](media/developer-best-practices-pod-security/basic-key-vault-flexvol.png)

Met Key Vault opslaan en regelmatig draaien geheimen zoals referenties, opslagaccountsleutels of certificaten. U kunt Azure Key Vault integreren met een AKS-cluster met behulp van een FlexVolume. Het stuurprogramma FlexVolume kunt het AKS-cluster systeemeigen referenties ophalen uit Key Vault en bieden ze veilig alleen aan de aanvragende schil. Werken met uw cluster-operator op die de Key Vault FlexVol-stuurprogramma op de AKS-knooppunten implementeren. Toegang aanvragen tot Key Vault en ophalen van de referenties die u nodig hebt door het stuurprogramma FlexVolume kunt u de identiteit van een pod beheerd.

Azure Key Vault met FlexVol is bedoeld voor gebruik met toepassingen en services die worden uitgevoerd op Linux, peulen en knoop punten.

## <a name="next-steps"></a>Volgende stappen

In dit artikel is gericht op over het beveiligen van uw schillen. Voor het implementeren van sommige van deze gebieden, Zie de volgende artikelen:

* [Beheerde identiteiten gebruiken voor Azure-resources met AKS][aad-pod-identity]
* [Azure Key Vault integreren met AKS][aks-keyvault-flexvol]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo-pod
[aks-keyvault-flexvol]: https://github.com/Azure/kubernetes-keyvault-flexvol
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.12/#selinuxoptions-v1-core
[aks-associated-projects]: https://github.com/Azure/AKS/blob/master/previews.md#associated-projects

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources
