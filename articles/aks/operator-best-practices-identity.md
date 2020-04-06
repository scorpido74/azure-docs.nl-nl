---
title: Aanbevolen procedures voor het beheren van identiteit
titleSuffix: Azure Kubernetes Service
description: Lees de aanbevolen procedures voor de clusteroperator voor het beheren van verificatie en autorisatie voor clusters in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 0e3569be769fcf70a65cbfee62a3b80a5abdc3b5
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668317"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Aanbevolen procedures voor verificatie en autorisatie in Azure Kubernetes Service (AKS)

Terwijl u clusters implementeert en onderhoudt in Azure Kubernetes Service (AKS), moet u manieren implementeren om de toegang tot bronnen en services te beheren. Zonder deze besturingselementen kunnen accounts toegang hebben tot bronnen en services die ze niet nodig hebben. Het kan ook moeilijk zijn om bij te houden welke set referenties werden gebruikt om wijzigingen aan te brengen.

Dit best practices-artikel richt zich op hoe een clusteroperator toegang en identiteit voor AKS-clusters kan beheren. In dit artikel leert u het volgende:

> [!div class="checklist"]
> * AKS-clustergebruikers verifiëren met Azure Active Directory
> * Toegang tot bronnen beheren met op rollen gebaseerde toegangscontroles (RBAC)
> * Een beheerde identiteit gebruiken om zichzelf te authenticeren met andere services

## <a name="use-azure-active-directory"></a>Azure Active Directory gebruiken

**Richtlijnen voor aanbevolen procedures** - Implementeren van AKS-clusters met Azure AD-integratie. Het gebruik van Azure AD centraliseert de component identiteitsbeheer. Elke wijziging in het gebruikersaccount of de groepsstatus wordt automatisch bijgewerkt in de toegang tot het AKS-cluster. Gebruik Rollen of clusterrollen en bindingen, zoals besproken in de volgende sectie, om gebruikers of groepen zo min mogelijk te beheren als er minimaal een aantal benodigde machtigingen nodig is.

De ontwikkelaars en toepassingseigenaren van uw Kubernetes-cluster hebben toegang nodig tot verschillende bronnen. Kubernetes biedt geen oplossing voor identiteitsbeheer om te bepalen welke gebruikers kunnen communiceren met welke resources. In plaats daarvan integreert u uw cluster meestal met een bestaande identiteitsoplossing. Azure Active Directory (AD) biedt een oplossing voor bedrijfsbeheer en kan worden geïntegreerd met AKS-clusters.

Met azure AD-geïntegreerde clusters in AKS maakt u *rollen* of *clusterrollen* die toegangsmachtigingen voor resources definiëren. Vervolgens *bindt u* de rollen aan gebruikers of groepen vanuit Azure AD. Deze Kubernetes role-based access controls (RBAC) worden besproken in de volgende sectie. De integratie van Azure AD en de manier waarop u de toegang tot bronnen beheert, zijn te zien in het volgende diagram:

![Verificatie op clusterniveau voor Azure Active Directory-integratie met AKS](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. Ontwikkelaar verifieert met Azure AD.
1. Het eindpunt voor azure AD-tokenuitgifte geeft het toegangstoken uit.
1. De ontwikkelaar voert een actie uit met het Azure AD-token, zoals`kubectl create pod`
1. Kubernetes valideert het token met Azure Active Directory en haalt groepslidmaatschappen van de ontwikkelaar op.
1. RBAC (Kubernetes role-based access control) en clusterbeleid worden toegepast.
1. Het verzoek van de ontwikkelaar is al dan niet mislukt op basis van eerdere validatie van Azure AD-groepslidmaatschap en Kubernetes RBAC en -beleid.

Zie Azure Active Directory integreren met [AKS][aks-aad]als u een AKS-cluster wilt maken dat Azure AD gebruikt.

## <a name="use-role-based-access-controls-rbac"></a>Op rollen gebaseerde toegangscontroles (RBAC) gebruiken

**Richtlijnen voor aanbevolen procedures** - Gebruik Kubernetes RBAC om de machtigingen te definiëren die gebruikers of groepen hebben om resources in het cluster te gebruiken. Maak rollen en bindingen die het minste aantal vereiste machtigingen toewijzen. Integreer met Azure AD, zodat elke wijziging in de gebruikersstatus of het groepslidmaatschap automatisch wordt bijgewerkt en de toegang tot clusterbronnen actueel is.

In Kubernetes u gedetailleerde controle bieden over de toegang tot bronnen in het cluster. Machtigingen kunnen worden gedefinieerd op clusterniveau of op specifieke naamruimten. U definiëren welke resources kunnen worden beheerd en met welke machtigingen. Deze rollen worden vervolgens toegepast op gebruikers of groepen met een binding. Zie [Toegangs- en identiteitsopties voor Azure Kubernetes Service (AKS) voor][aks-concepts-identity]meer informatie over *rollen,* *clusterrollen*en *bindingen.*

Als voorbeeld u een rol maken die volledige toegang verleent tot bronnen in de naamruimte met de naam *finance-app,* zoals weergegeven in het volgende voorbeeld YAML-manifest:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

Vervolgens wordt een RoleBinding gemaakt die de Azure *AD-gebruikersontwikkelaar1\@contoso.com* aan de RoleBinding bindt, zoals wordt weergegeven in het volgende YAML-manifest:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

Wanneer *ontwikkelaar1\@contoso.com* is geverifieerd ten opzichte van het AKS-cluster, hebben ze volledige machtigingen voor resources in de naamruimte voor de financiële *app.* Op deze manier scheidt en beheert u logischerwijs de toegang tot bronnen. Kubernetes RBAC moet worden gebruikt in combinatie met Azure AD-integratie, zoals besproken in de vorige sectie.

Zie Toegang tot clusterbronnen beheren met behulp van op [rollen gebaseerde toegangsbesturingselementen en Azure Active Directory-identiteiten in AKS][azure-ad-rbac]als u wilt zien hoe u Azure AD-groepen gebruiken om de toegang tot Kubernetes-bronnen te beheren met RBAC.

## <a name="use-pod-identities"></a>Podidentiteiten gebruiken

**Richtlijnen voor beste praktijken** - Gebruik geen vaste referenties in pods of containerafbeeldingen, omdat ze het risico lopen op blootstelling of misbruik. Gebruik in plaats daarvan podidentiteiten om automatisch toegang aan te vragen met behulp van een centrale Azure AD-identiteitsoplossing. Pod-identiteiten zijn alleen bedoeld voor gebruik met Linux-pods en containerafbeeldingen.

Wanneer pods toegang nodig hebben tot andere Azure-services, zoals Cosmos DB, Key Vault of Blob Storage, heeft de pod toegangsreferenties nodig. Deze toegangsreferenties kunnen worden gedefinieerd met de containerafbeelding of worden geïnjecteerd als een Kubernetes-geheim, maar moeten handmatig worden gemaakt en toegewezen. Vaak worden de referenties opnieuw gebruikt tussen pods en worden ze niet regelmatig gedraaid.

Beheerde identiteiten voor Azure-resources (momenteel geïmplementeerd als een gekoppeld AKS-opensourceproject) stellen u in staat om automatisch toegang te vragen tot services via Azure AD. U definieert niet handmatig referenties voor pods, maar ze vragen in realtime een toegangstoken aan en kunnen het gebruiken om alleen toegang te krijgen tot hun toegewezen services. In AKS worden twee componenten geïmplementeerd door de clusteroperator om pods beheerde identiteiten te laten gebruiken:

* **De NOde Management Identity (NMI)-server** is een pod die wordt uitgevoerd als een DaemonSet op elk knooppunt in het AKS-cluster. De NMI-server luistert naar podaanvragen naar Azure-services.
* **De Managed Identity Controller (MIC)** is een centrale pod met machtigingen voor het opvragen van de Kubernetes API-server en controleert op een Azure-identiteitstoewijzing die overeenkomt met een pod.

Wanneer pods toegang tot een Azure-service vragen, leiden netwerkregels het verkeer om naar de NMI-server (Node Management Identity). De NMI-server identificeert pods die toegang tot Azure-services aanvragen op basis van hun afstandsbediening en stelt de Managed Identity Controller (MIC) op. De MIC controleert op Azure-identiteitstoewijzingen in het AKS-cluster en de NMI-server vraagt vervolgens een toegangstoken aan van Azure Active Directory (AD) op basis van de identiteitstoewijzing van de pod. Azure AD biedt toegang tot de NMI-server, die wordt geretourneerd naar de pod. Dit toegangstoken kan door de pod worden gebruikt om vervolgens toegang tot services in Azure aan te vragen.

In het volgende voorbeeld maakt een ontwikkelaar een pod die een beheerde identiteit gebruikt om toegang tot een Azure SQL Server-exemplaar aan te vragen:

![Met pod-identiteiten kan een pod automatisch toegang tot andere services aanvragen](media/operator-best-practices-identity/pod-identities.png)

1. Clusteroperator maakt eerst een serviceaccount dat kan worden gebruikt om identiteiten in kaart te brengen wanneer pods toegang tot services vragen.
1. De NMI-server en MIC worden geïmplementeerd om podaanvragen voor toegangstokens door te sturen naar Azure AD.
1. Een ontwikkelaar implementeert een pod met een beheerde identiteit die een toegangstoken aanvraagt via de NMI-server.
1. Het token wordt teruggestuurd naar de pod en wordt gebruikt om toegang te krijgen tot een Azure SQL Server-exemplaar.

> [!NOTE]
> Beheerde podidentiteiten is een open source-project en wordt niet ondersteund door technische ondersteuning van Azure.

Zie [Azure Active Directory-identiteiten voor Kubernetes-toepassingen][aad-pod-identity]als u podidentiteiten wilt gebruiken.

## <a name="next-steps"></a>Volgende stappen

Dit artikel over aanbevolen procedures is gericht op verificatie en autorisatie voor uw cluster en resources. Zie de volgende artikelen om een aantal van deze aanbevolen procedures te implementeren:

* [Azure Active Directory integreren met AKS][aks-aad]
* [Beheerde identiteiten gebruiken voor Azure-resources met AKS][aad-pod-identity]

Zie de volgende aanbevolen procedures voor meer informatie over clusterbewerkingen in AKS:

* [Multitenancy en clusterisolatie][aks-best-practices-cluster-isolation]
* [Basisfuncties kubernetes scheduler][aks-best-practices-scheduler]
* [Geavanceerde functies voor Kubernetes scheduler][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: azure-ad-integration-cli.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[azure-ad-rbac]: azure-ad-rbac.md
