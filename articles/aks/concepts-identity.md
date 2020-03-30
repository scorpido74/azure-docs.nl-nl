---
title: Concepten - Toegang en identiteit in Azure Kubernetes Services (AKS)
description: Meer informatie over toegang en identiteit in Azure Kubernetes Service (AKS), inclusief Azure Active Directory-integratie, RBAC (Kubernetes role-based access control) en rollen en bindingen.
services: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: e4945535417f7d8d33308121267ba97e1f835e13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259602"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Toegangs- en identiteitsopties voor Azure Kubernetes Service (AKS)

Er zijn verschillende manieren om Kubernetes-clusters te verifiëren en te beveiligen. Met behulp van op rollen gebaseerde toegangscontroles (RBAC) u gebruikers of groepen toegang verlenen tot alleen de resources die ze nodig hebben. Met Azure Kubernetes Service (AKS) u de beveiligings- en machtigingenstructuur verder verbeteren met Azure Active Directory. Deze benaderingen helpen u bij het beveiligen van uw toepassingsworkloads en klantgegevens.

In dit artikel worden de kernconcepten geïntroduceerd waarmee u machtigingen verifiëren en toewijzen in AKS:

- [Kubernetes-serviceaccounts](#kubernetes-service-accounts)
- [Integratie van Azure Active Directory](#azure-active-directory-integration)
- [Op rollen gebaseerde toegangscontroles (RBAC)](#role-based-access-controls-rbac)
- [Rollen en clusterrollen](#roles-and-clusterroles)
- [Rolbindingen en clusterrolebindingen](#rolebindings-and-clusterrolebindings)

## <a name="kubernetes-service-accounts"></a>Kubernetes-serviceaccounts

Een van de belangrijkste gebruikerstypen in Kubernetes is een *serviceaccount.* Er bestaat een serviceaccount in en wordt beheerd door de Kubernetes API. De referenties voor serviceaccounts worden opgeslagen als Kubernetes-geheimen, waardoor ze kunnen worden gebruikt door geautoriseerde pods om te communiceren met de API-server. De meeste API-aanvragen bieden een verificatietoken voor een serviceaccount of een normaal gebruikersaccount.

Normale gebruikersaccounts bieden meer traditionele toegang voor menselijke beheerders of ontwikkelaars, niet alleen services en processen. Kubernetes zelf biedt geen oplossing voor identiteitsbeheer waarbij gewone gebruikersaccounts en wachtwoorden worden opgeslagen. In plaats daarvan kunnen externe identiteitsoplossingen worden geïntegreerd in Kubernetes. Voor AKS-clusters is deze geïntegreerde identiteitsoplossing Azure Active Directory.

Zie [Kubernetes-verificatie][kubernetes-authentication]voor meer informatie over de identiteitsopties in Kubernetes.

## <a name="azure-active-directory-integration"></a>Integratie van Azure Active Directory

De beveiliging van AKS-clusters kan worden verbeterd met de integratie van Azure Active Directory (AD). Azure AD is gebouwd op tientallen jaren bedrijfsidentiteitsbeheer en is een multi-tenant- en cloudmap- en identiteitsbeheerservice die kerndirectoryservices, toepassingstoegangsbeheer en identiteitsbescherming combineert. Met Azure AD u on-premises identiteiten integreren in AKS-clusters om één bron te bieden voor accountbeheer en -beveiliging.

![Azure Active Directory-integratie met AKS-clusters](media/concepts-identity/aad-integration.png)

Met aks-clusters die in Azure AD zijn geïntegreerd, u gebruikers of groepen toegang verlenen tot Kubernetes-bronnen binnen een naamruimte of in het cluster. Om een `kubectl` configuratiecontext te verkrijgen, kan een gebruiker de opdracht [az aks get-credentials][az-aks-get-credentials] uitvoeren. Wanneer een gebruiker vervolgens communiceert met `kubectl`het AKS-cluster met , wordt deze gebruiker gevraagd zich aan te melden met hun Azure AD-referenties. Deze aanpak biedt één bron voor gebruikersaccountbeheer en wachtwoordreferenties. De gebruiker heeft alleen toegang tot de bronnen zoals gedefinieerd door de clusterbeheerder.

Azure AD-verificatie in AKS-clusters maakt gebruik van OpenID Connect, een identiteitslaag die is gebouwd bovenop het OAuth 2.0-protocol. OAuth 2.0 definieert mechanismen voor het verkrijgen en gebruiken van toegangstokens om toegang te krijgen tot beveiligde bronnen, en OpenID Connect implementeert verificatie als een uitbreiding op het OAuth 2.0-autorisatieproces. Zie de Documentatie Open ID Connect voor meer informatie over OpenID [Connect.][openid-connect] Om de verificatietokens te verifiëren die via OpenID Connect van Azure AD zijn verkregen, gebruiken AKS-clusters Kubernetes Webhook Token Authentication. Zie de documentatie [webhook tokenverificatie][webhook-token-docs]voor meer informatie .

## <a name="role-based-access-controls-rbac"></a>Op rollen gebaseerde toegangscontroles (RBAC)

Om gedetailleerde filtering te bieden van de acties die gebruikers kunnen uitvoeren, maakt Kubernetes gebruik van op rollen gebaseerde toegangscontroles (RBAC). Met dit besturingselementmechanisme u gebruikers of groepen gebruikers toestemming geven om bijvoorbeeld resources te maken of te wijzigen of logboeken weer te geven van het uitvoeren van toepassingsworkloads. Deze machtigingen kunnen worden beperkt tot één naamruimte of worden verleend in het hele AKS-cluster. Met Kubernetes RBAC maakt u *rollen* om machtigingen te definiëren en wijst u deze rollen vervolgens toe aan gebruikers met *rolbindingen.*

Zie [RBAC-autorisatie gebruiken][kubernetes-rbac]voor meer informatie .

### <a name="azure-role-based-access-controls-rbac"></a>Op Azure-role gebaseerde toegangscontroles (RBAC)
Een extra mechanisme voor het beheren van de toegang tot resources is Azure role-based access controls (RBAC). Kubernetes RBAC is ontworpen om te werken aan bronnen binnen uw AKS-cluster en Azure RBAC is ontworpen om te werken aan bronnen binnen uw Azure-abonnement. Met Azure RBAC maakt u een *roldefinitie* die de toe te passen machtigingen schetst. Een gebruiker of groep krijgt deze roldefinitie vervolgens toegewezen voor een bepaald *bereik*, dat een afzonderlijke resource, een resourcegroep of voor het hele abonnement kan zijn.

Zie [Wat is Azure RBAC voor][azure-rbac] meer informatie?

## <a name="roles-and-clusterroles"></a>Rollen en clusterrollen

Voordat u machtigingen toewijst aan gebruikers met Kubernetes RBAC, definieert u deze machtigingen eerst als een *rol.* Kubernetes-rollen *verlenen* machtigingen. Er is geen concept van een *weigering* toestemming.

Rollen worden gebruikt om machtigingen te verlenen binnen een naamruimte. Als u machtigingen voor het hele cluster moet verlenen of bronnen buiten een bepaalde naamruimte moet *clusteren,* u in plaats daarvan Clusterrollen gebruiken.

Een clusterrol werkt op dezelfde manier om machtigingen toe te kennen aan resources, maar kan worden toegepast op bronnen in het hele cluster, niet op een specifieke naamruimte.

## <a name="rolebindings-and-clusterrolebindings"></a>Rolbindingen en clusterrolebindingen

Zodra rollen zijn gedefinieerd om machtigingen toe te kennen aan resources, wijst u deze Kubernetes RBAC-machtigingen toe met een *RoleBinding*. Als uw AKS-cluster integreert met Azure Active Directory, worden deze Azure AD-gebruikers machtigingen gegeven om acties binnen het cluster uit te voeren.

Rolbindingen worden gebruikt om rollen toe te wijzen voor een bepaalde naamruimte. Met deze benadering u logisch erwijs één AKS-cluster scheiden, waarbij gebruikers alleen toegang hebben tot de toepassingsbronnen in hun toegewezen naamruimte. Als u rollen in het hele cluster moet binden of resources buiten een bepaalde naamruimte moet clusteren, u in plaats daarvan *ClusterroleBindings*gebruiken.

Een clusterrolbinding werkt op dezelfde manier om rollen aan gebruikers te binden, maar kan worden toegepast op resources in het hele cluster, niet op een specifieke naamruimte. Met deze aanpak u beheerders of ondersteuningstechnici toegang verlenen tot alle bronnen in het AKS-cluster.

## <a name="next-steps"></a>Volgende stappen

Zie Azure Active Directory integreren met [AKS][aks-aad]om aan de slag te gaan met Azure AD en Kubernetes RBAC.

Zie [Aanbevolen procedures voor verificatie en autorisatie in AKS voor][operator-best-practices-identity]bijbehorende aanbevolen procedures.

Zie de volgende artikelen voor meer informatie over de belangrijkste Kubernetes- en AKS-concepten:

- [Kubernetes / AKS-clusters en workloads][aks-concepts-clusters-workloads]
- [Kubernetes / AKS-beveiliging][aks-concepts-security]
- [Kubernetes / AKS virtuele netwerken][aks-concepts-network]
- [Kubernetes / AKS-opslag][aks-concepts-storage]
- [Kubernetes / AKS-schaal][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: azure-ad-integration-cli.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
