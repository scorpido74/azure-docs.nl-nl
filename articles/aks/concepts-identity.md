---
title: 'Concepten: toegang en identiteit in azure Kubernetes Services (AKS)'
description: Meer informatie over toegang en identiteit in azure Kubernetes service (AKS), met inbegrip van Azure Active Directory integratie, Kubernetes op rollen gebaseerd toegangs beheer (RBAC) en rollen en bindingen.
services: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: e4945535417f7d8d33308121267ba97e1f835e13
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84690418"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Toegangs- en identiteitsopties voor Azure Kubernetes Service (AKS)

Er zijn verschillende manieren om te verifiëren met en beveiligde Kubernetes-clusters. Met op rollen gebaseerde toegangs beheer (RBAC) kunt u gebruikers of groepen alleen toegang geven tot de resources die ze nodig hebben. Met Azure Kubernetes service (AKS) kunt u de structuur van beveiliging en machtigingen verder verbeteren met behulp van Azure Active Directory. Deze benaderingen helpen u bij het beveiligen van de werk belastingen en klant gegevens van uw toepassing.

In dit artikel worden de belangrijkste concepten geïntroduceerd die u helpen bij het verifiëren en toewijzen van machtigingen in AKS:

- [Kubernetes-service accounts](#kubernetes-service-accounts)
- [Integratie van Azure Active Directory](#azure-active-directory-integration)
- [Op rollen gebaseerde toegangs beheer (RBAC)](#role-based-access-controls-rbac)
- [Rollen en ClusterRoles](#roles-and-clusterroles)
- [RoleBindings en ClusterRoleBindings](#rolebindings-and-clusterrolebindings)

## <a name="kubernetes-service-accounts"></a>Kubernetes-service accounts

Een van de primaire gebruikers typen in Kubernetes is een *Service account*. Er bestaat een service account in en wordt beheerd door de Kubernetes-API. De referenties voor service accounts worden opgeslagen als Kubernetes geheimen, waardoor ze kunnen worden gebruikt door geautoriseerde peul om te communiceren met de API-server. De meeste API-aanvragen bieden een verificatie token voor een service account of een normaal gebruikers account.

Normale gebruikers accounts staan meer traditionele toegang toe voor beheerders of ontwikkel aars van personen, niet alleen voor services en processen. Kubernetes zelf biedt geen oplossing voor identiteits beheer waar reguliere gebruikers accounts en wacht woorden worden opgeslagen. In plaats daarvan kunnen externe identiteits oplossingen worden geïntegreerd in Kubernetes. Voor AKS-clusters is deze geïntegreerde identiteits oplossing Azure Active Directory.

Zie [Kubernetes-verificatie][kubernetes-authentication]voor meer informatie over de identiteits opties in Kubernetes.

## <a name="azure-active-directory-integration"></a>Integratie van Azure Active Directory

De beveiliging van AKS-clusters kan worden uitgebreid met de integratie van Azure Active Directory (AD). Azure AD is gebouwd op tien tallen bedrijfs identiteits beheer en is een multi tenant-, Cloud-en identiteits beheer service die belang rijke Directory Services, Toegangs beheer voor toepassingen en identiteits beveiliging combineert. Met Azure AD kunt u on-premises identiteiten integreren in AKS-clusters om één bron te bieden voor account beheer en beveiliging.

![Integratie met AKS-clusters Azure Active Directory](media/concepts-identity/aad-integration.png)

Met Azure AD geïntegreerde AKS-clusters kunt u gebruikers of groepen toegang verlenen tot Kubernetes-resources binnen een naam ruimte of in het cluster. `kubectl`Een gebruiker kan een configuratie context verkrijgen door de opdracht [AZ AKS Get-credentials][az-aks-get-credentials] uit te voeren. Wanneer een gebruiker vervolgens met het AKS-cluster communiceert met `kubectl` , wordt ze gevraagd zich aan te melden met hun Azure AD-referenties. Deze benadering biedt één bron voor gebruikers account beheer en wachtwoord referenties. De gebruiker heeft alleen toegang tot de resources zoals gedefinieerd door de Cluster beheerder.

Azure AD-verificatie in AKS-clusters maakt gebruik van OpenID Connect Connect, een laag-id die boven op het OAuth 2,0-protocol is gebouwd. OAuth 2,0 definieert mechanismen voor het verkrijgen en gebruiken van toegangs tokens voor toegang tot beveiligde bronnen en OpenID Connect Connect implementeert verificatie als een uitbrei ding van het OAuth 2,0-autorisatie proces. Voor meer informatie over OpenID Connect Connect raadpleegt u de [Open-ID Connect-documentatie][openid-connect]. Voor het controleren van de verificatie tokens die zijn verkregen via Azure AD via OpenID Connect Connect, maken AKS-clusters gebruik van Kubernetes webhook-token verificatie. Zie de [documentatie voor webhook-token verificatie][webhook-token-docs]voor meer informatie.

## <a name="role-based-access-controls-rbac"></a>Op rollen gebaseerde toegangs beheer (RBAC)

Kubernetes maakt gebruik van op rollen gebaseerde toegangs beheer (RBAC) om nauw keurig filteren te bieden van de acties die gebruikers kunnen uitvoeren. Met dit besturings systeem kunt u gebruikers of groepen gebruikers toewijzen, machtigingen geven om resources te maken of te wijzigen, of logboeken van actieve werk belastingen van toepassingen bekijken. Deze machtigingen kunnen worden ingesteld op een enkele naam ruimte of worden toegestaan in het hele AKS-cluster. Met Kubernetes RBAC maakt u *rollen* om machtigingen te definiëren en wijst u deze rollen vervolgens toe aan gebruikers met *functie bindingen*.

Zie [using RBAC Authorization][kubernetes-rbac](Engelstalig) voor meer informatie.

### <a name="azure-role-based-access-controls-rbac"></a>Op rollen gebaseerd toegangs beheer (RBAC) van Azure
Een aanvullend mechanisme voor het beheren van de toegang tot resources is op rollen gebaseerd toegangs beheer (RBAC) van Azure. Kubernetes RBAC is ontworpen om te werken aan resources in uw AKS-cluster en Azure RBAC is ontworpen om te werken aan resources in uw Azure-abonnement. Met Azure RBAC maakt u een *roldefinitie* waarin de machtigingen worden beschreven die moeten worden toegepast. Vervolgens wordt aan een gebruiker of groep de functie definitie toegewezen voor een bepaald *bereik*. Dit kan een afzonderlijke resource, een resource groep of het hele abonnement zijn.

Zie [Wat is Azure RBAC?][azure-rbac] voor meer informatie.

## <a name="roles-and-clusterroles"></a>Rollen en ClusterRoles

Voordat u machtigingen toewijst aan gebruikers met Kubernetes RBAC, definieert u eerst die machtigingen als een *rol*. Kubernetes-rollen *verlenen* machtigingen. Er is geen principe voor het *weigeren* van machtigingen.

Rollen worden gebruikt voor het verlenen van machtigingen binnen een naam ruimte. Als u machtigingen wilt verlenen voor het hele cluster of als u cluster bronnen buiten een bepaalde naam ruimte wilt toewijzen, kunt u in plaats daarvan *ClusterRoles*gebruiken.

Een ClusterRole werkt op dezelfde manier om machtigingen te verlenen aan resources, maar kan worden toegepast op resources in het hele cluster, niet op een specifieke naam ruimte.

## <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings en ClusterRoleBindings

Zodra rollen zijn gedefinieerd om machtigingen te verlenen voor bronnen, wijst u deze Kubernetes RBAC-machtigingen toe aan een *RoleBinding*. Als uw AKS-cluster wordt geïntegreerd met Azure Active Directory, zijn bindingen de manier waarop de Azure AD-gebruikers machtigingen krijgen om acties in het cluster uit te voeren.

Roltoewijzingen worden gebruikt voor het toewijzen van rollen voor een bepaalde naam ruimte. Met deze benadering kunt u een enkel AKS-cluster logisch scheiden, met gebruikers die alleen toegang hebben tot de toepassings resources in hun toegewezen naam ruimte. Als u rollen moet binden in het hele cluster of als u cluster bronnen buiten een bepaalde naam ruimte wilt koppelen, kunt u in plaats daarvan *ClusterRoleBindings*gebruiken.

Een ClusterRoleBinding werkt op dezelfde manier om rollen aan gebruikers te binden, maar kan worden toegepast op resources in het hele cluster, niet op een specifieke naam ruimte. Met deze aanpak kunt u beheerders of ondersteunings technici toegang verlenen tot alle resources in het AKS-cluster.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Active Directory integreren met AKS][aks-aad]om aan de slag te gaan met Azure AD en Kubernetes RBAC.

Zie [Aanbevolen procedures voor verificatie en autorisatie in AKS][operator-best-practices-identity]voor gekoppelde aanbevolen procedures.

Raadpleeg de volgende artikelen voor meer informatie over de belangrijkste Kubernetes-en AKS-concepten:

- [Kubernetes/AKS-clusters en-workloads][aks-concepts-clusters-workloads]
- [Kubernetes/AKS-beveiliging][aks-concepts-security]
- [Kubernetes/AKS virtuele netwerken][aks-concepts-network]
- [Kubernetes/AKS-opslag][aks-concepts-storage]
- [Kubernetes/AKS-schaal][aks-concepts-scale]

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
