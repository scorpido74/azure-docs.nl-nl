---
title: Veelgestelde vragen over Azure dev Spaces
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Antwoorden vinden op enkele veelgestelde vragen over Azure dev Spaces
keywords: 'Docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, helm, service-net, service mesh routing, kubectl, K8S '
ms.openlocfilehash: 526e8a247241bd3ea0e61b6cd3b025cd473b29aa
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942512"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Veelgestelde vragen over Azure dev Spaces

Dit is een oplossing voor veelgestelde vragen over Azure dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Welke Azure-regio's bevatten momenteel Azure dev Spaces?

Zie [ondersteunde regio's][supported-regions] voor een volledige lijst met beschik bare regio's.

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>Kan ik mijn AKS-cluster migreren met Azure-ontwikkel ruimten naar een andere regio?

Ja, als u uw AKS-cluster met Azure-ontwikkel ruimten wilt verplaatsen naar een andere [ondersteunde regio][supported-regions], raden we u aan om een nieuw cluster in de andere regio te maken en Azure dev Spaces te installeren en te configureren en uw resources en toepassingen te implementeren in uw nieuwe cluster. Zie [Migrate to Azure Kubernetes service (AKS) (Engelstalig)][aks-migration]voor meer informatie over het migreren van AKS.

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>Kan ik Azure dev Spaces gebruiken met bestaande Dockerfiles-of helm-grafieken?

Ja, als uw project al een Dockerfile-of helm-grafiek bevat, kunt u deze bestanden gebruiken met Azure dev Spaces. Wanneer u `azds prep`uitvoert, gebruikt u de para meter `--chart` en geeft u de locatie van de grafiek op. Met Azure dev Spaces wordt nog steeds een bestand *azds. yaml* en *Dockerfile. Developer* gegenereerd, maar wordt geen bestaande Dockerfile of een helm-grafiek vervangen of gewijzigd. Mogelijk moet u de *azds. yaml* -en *Dockerfile.* -bestanden wijzigen om alles goed te laten werken met uw bestaande toepassing wanneer u `azds up`uitvoert.

Wanneer u uw eigen Dockerfile-of helm-grafiek gebruikt, gelden de volgende beperkingen:
* Als er slechts één Dockerfile wordt gebruikt, moet dit alles bevatten wat u nodig hebt om ontwikkelings scenario's mogelijk te maken, zoals de taal-SDK, niet alleen de runtime. Als u gebruikmaakt van een afzonderlijke Dockerfile voor Azure-ontwikkel ruimten, zoals een Dockerfile. ontwikkelen, moet alles wat u nodig hebt om ontwikkel scenario's in te scha kelen, in die Dockerfile zijn opgenomen.
* Uw helm-grafiek moet ondersteuning bieden voor het door geven van een deel van of de gehele afbeeldings code als waarde uit *Values. yaml*.
* Als u iets wijzigt met inkomend verkeer, kunt u uw helm-grafiek ook bijwerken om de ingangs oplossing van Azure dev Spaces te gebruiken.
* Als u de [routerings mogelijkheden van Azure dev Spaces][dev-spaces-routing]wilt gebruiken, moeten alle services voor een afzonderlijk project binnen een enkele Kubernetes-naam ruimte passen en moeten ze worden geïmplementeerd met eenvoudige naam, bijvoorbeeld *service-a*. In de standaard helm-grafieken kunt u deze naamgevings update uitvoeren door een waarde voor de eigenschap *fullnameOverride* op te geven.

Als u uw eigen Dockerfile-of helm-grafiek wilt vergelijken met een bestaande versie die werkt met Azure dev Spaces, controleert u de bestanden die in de [Quick][quickstart-cli]start zijn gegenereerd.


## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>Kan ik de bestanden wijzigen die zijn gegenereerd door Azure dev Spaces?

Ja, u kunt het *azds. yaml* -bestand, de Dockerfile-en helm-grafiek [die door Azure dev Spaces is gegenereerd, wijzigen wanneer u uw project voorbereidt][dev-spaces-prep]. Het wijzigen van deze bestanden verandert de manier waarop het project wordt gemaakt en uitgevoerd.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Kan ik Azure dev Spaces gebruiken zonder een openbaar IP-adres?

Nee, u kunt geen Azure dev-ruimten inrichten op een AKS-cluster zonder een openbaar IP-adres. Er is een openbaar IP-adres [nodig door Azure dev Spaces voor route ring][dev-spaces-routing].

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Kan ik mijn eigen ingangen gebruiken met Azure dev Spaces?

Ja, u kunt uw eigen inkomend verkeer configureren aan de kant van de ingangen die Azure dev Spaces maakt. U kunt bijvoorbeeld [traefik][ingress-traefik] of [NGINX][ingress-nginx]gebruiken.

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Kan ik HTTPS gebruiken met Azure dev Spaces?

Ja, u kunt uw eigen inkomend verkeer configureren met HTTPS met behulp van [traefik][ingress-https-traefik] of [NGINX][ingress-https-nginx].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Kan ik Azure dev Spaces gebruiken in een cluster dat gebruikmaakt van CNI in plaats van kubenet? 

Ja, u kunt Azure dev Spaces gebruiken in een AKS-cluster dat gebruikmaakt van CNI voor netwerken. U kunt bijvoorbeeld Azure dev Spaces gebruiken in een AKS-cluster met [bestaande Windows-containers][windows-containers], die gebruikmaken van cni voor netwerken. Meer informatie over het gebruik van CNI voor netwerken met Azure dev Spaces is [hier](configure-networking.md#using-azure-cni)beschikbaar.

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Kan ik Azure dev Spaces gebruiken met Windows-containers?

Momenteel is Azure dev Spaces alleen bedoeld om te worden uitgevoerd op Linux en alleen knoop punten, maar u kunt Azure dev-ruimten uitvoeren op een AKS-cluster met [bestaande Windows-containers][windows-containers].

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Kan ik Azure dev Spaces gebruiken op AKS-clusters waarop de door API server geautoriseerde IP-adresbereiken zijn ingeschakeld?

Ja, u kunt Azure dev Spaces gebruiken in AKS-clusters met [API-server geautoriseerde IP-][aks-auth-range] adresbereiken ingeschakeld. Meer informatie over het gebruik van een AKS-cluster met API-server geautoriseerde IP-adresbereiken ingeschakeld met Azure dev Spaces is [hier](configure-networking.md#using-api-server-authorized-ip-ranges)beschikbaar.

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Kan ik Azure dev Spaces gebruiken in AKS-clusters met beperkt uitgaand verkeer voor cluster knooppunten?

Ja, u kunt Azure dev Spaces gebruiken in AKS-clusters met beperkt uitgaand [verkeer voor cluster knooppunten][aks-restrict-egress-traffic] die zijn ingeschakeld wanneer de juiste FQDN-namen zijn toegestaan. Meer informatie over het gebruik van een AKS-cluster met beperkt uitgaand verkeer voor knoop punten die zijn ingeschakeld met Azure dev Spaces is [hier](configure-networking.md#ingress-and-egress-network-traffic-requirements)beschikbaar.

## <a name="can-i-use-azure-dev-spaces-on-rbac-enabled-aks-clusters"></a>Kan ik Azure dev Spaces gebruiken in AKS-clusters met RBAC-functionaliteit?

Ja, u kunt Azure dev Spaces gebruiken in AKS-clusters met of zonder RBAC ingeschakeld.

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>Wat gebeurt er wanneer ik inschakelingen voor het project in Visual Studio Activeer?

Wanneer u Visual Studio gebruikt om uw project voor te bereiden, hebt u de mogelijkheid om in te scha kelen voor uw service. Als u inschakelt, wordt er een openbaar eind punt gemaakt voor toegang tot uw service wanneer deze wordt uitgevoerd op uw AKS-cluster. Dit is optioneel. Als u geen ingang inschakelt, is uw service alleen toegankelijk vanuit uw AKS-cluster.

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-migration]: ../aks/aks-migration.md
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-prep]: how-dev-spaces-works.md#prepare-your-code
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[quickstart-cli]: quickstart-cli.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[windows-containers]: how-to/run-dev-spaces-windows-containers.md