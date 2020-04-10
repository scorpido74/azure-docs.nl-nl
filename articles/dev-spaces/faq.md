---
title: Veelgestelde vragen over Azure Dev Spaces
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Antwoorden vinden op enkele van de veelgestelde vragen over Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s '
ms.openlocfilehash: f3ac6ec3c3ddbe8ff508befba2eb4a8423e66f07
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998734"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Veelgestelde vragen over Azure Dev Spaces

Hiermee worden veelgestelde vragen over Azure Dev Spaces beantwoord.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Welke Azure-regio's bieden momenteel Azure Dev Spaces?

Zie [ondersteunde regio's][supported-regions] voor een volledige lijst met beschikbare regio's.

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>Kan ik mijn AKS-cluster migreren met Azure Dev Spaces naar een andere regio?

Ja, als u uw AKS-cluster wilt verplaatsen met Azure Dev Spaces naar een andere [ondersteunde regio,][supported-regions]raden we u aan een nieuw cluster in de andere regio te maken en azure dev Spaces te installeren en te configureren en uw resources en toepassingen te implementeren in uw nieuwe cluster. Zie Migreren naar Azure Kubernetes [Service (AKS) voor][aks-migration]meer informatie over het migreren van AKS.

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>Kan ik Azure Dev Spaces gebruiken met bestaande Dockerfiles of Helm-diagrammen?

Ja, als uw project al een Dockerfile of een Helm-diagram heeft, u deze bestanden gebruiken met Azure Dev Spaces. Wanneer u `azds prep`uitvoert, `--chart` gebruikt u de parameter en geeft u de locatie van de grafiek op. Azure Dev Spaces genereert nog steeds een *bestand azds.yaml* en *Dockerfile.develop,* maar het zal een bestaand Dockerfile of een Helm-diagram niet vervangen of wijzigen. Het kan nodig zijn om de *azds.yaml* en *Dockerfile.develop* bestanden te wijzigen `azds up`om alles correct te laten werken met uw bestaande toepassing bij het uitvoeren van .

Bij het gebruik van uw eigen Dockerfile- of Helm-diagram zijn er de volgende beperkingen:
* Als u slechts één Dockerfile gebruikt, moet het alles bevatten wat u nodig hebt om ontwikkelingsscenario's in te schakelen, zoals de taal-SDK, niet alleen de runtime. Als u een afzonderlijk Dockerfile voor Azure Dev Spaces gebruikt, zoals een Dockerfile.develop, moet alles wat u nodig hebt om ontwikkelscenario's in te schakelen, in dat Dockerfile worden opgenomen.
* Uw helmdiagram moet het passeren van een deel van of de volledige afbeeldingstag ondersteunen als waarde van *values.yaml*.
* Als u iets wijzigt met binnendringen, u uw Helm-diagram ook bijwerken om de ingress-oplossing van Azure Dev Spaces te gebruiken.
* Als u de [routeringsmogelijkheden van Azure Dev Spaces][dev-spaces-routing]wilt gebruiken, moeten alle services voor een afzonderlijk project binnen één Naamruimte van Kubernetes passen en moeten ze worden geïmplementeerd met eenvoudige naamgeving, bijvoorbeeld *service-a.* In standaardhelmdiagrammen kan deze naamgevingsupdate worden uitgevoerd door een waarde op te geven voor de eigenschap *fullnameOverride.*

Als u uw eigen Dockerfile- of Helm-diagram wilt vergelijken met een bestaande versie die werkt met Azure Dev Spaces, controleert u de bestanden die in de [quickstart zijn][quickstart-cli]gegenereerd.


## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>Kan ik de bestanden wijzigen die zijn gegenereerd door Azure Dev Spaces?

Ja, u het *azds.yaml-bestand,* dockerfile en helmdiagram wijzigen [dat is gegenereerd door Azure Dev Spaces bij de voorbereiding van uw project.][dev-spaces-prep] Als u deze bestanden wijzigt, verandert de manier waarop het project wordt gebouwd en uitgevoerd.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Kan ik Azure Dev Spaces gebruiken zonder een openbaar IP-adres?

Nee, u Azure Dev Spaces niet inrichten op een AKS-cluster zonder een openbaar IP-adres. Azure Dev Spaces heeft een openbaar IP-ip [nodig voor routering.][dev-spaces-routing]

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Kan ik mijn eigen ingress gebruiken met Azure Dev Spaces?

Ja, u uw eigen binnendringen configureren samen met de inbinnendringen die Azure Dev Spaces maakt. U bijvoorbeeld [traefik][ingress-traefik] of [NGINX][ingress-nginx]gebruiken.

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Kan ik HTTPS gebruiken met Azure Dev Spaces?

Ja, u uw eigen binnendringen configureren met HTTPS met behulp van [traefik][ingress-https-traefik] of [NGINX.][ingress-https-nginx]

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Kan ik Azure Dev Spaces gebruiken op een cluster dat CNI gebruikt in plaats van kubenet? 

Ja, u Azure Dev Spaces gebruiken op een AKS-cluster dat CNI gebruikt voor netwerken. U bijvoorbeeld Azure Dev Spaces gebruiken op een AKS-cluster met [bestaande Windows-containers][windows-containers], waarmee CNI wordt gebruikt voor netwerken. Meer informatie over het gebruik van CNI voor netwerken met Azure Dev Spaces is [hier](configure-networking.md#using-azure-cni)beschikbaar.

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Kan ik Azure Dev Spaces gebruiken met Windows Containers?

Momenteel is Azure Dev Spaces alleen bedoeld om op Linux-pods en -knooppunten te draaien, maar u Azure Dev Spaces uitvoeren op een AKS-cluster met [bestaande Windows-containers.][windows-containers]

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Kan ik Azure Dev Spaces gebruiken op AKS-clusters met API-server geautoriseerde IP-adresbereiken ingeschakeld?

Ja, u Azure Dev Spaces gebruiken op AKS-clusters met [API-server geautoriseerde IP-adresbereiken][aks-auth-range] ingeschakeld. Meer informatie over het gebruik van een AKS-clusters met API-server geautoriseerde IP-adresbereiken ingeschakeld met Azure Dev Spaces is [hier](configure-networking.md#using-api-server-authorized-ip-ranges)beschikbaar.

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Kan ik Azure Dev Spaces gebruiken op AKS-clusters met beperkt uitgangsverkeer voor clusterknooppunten?

Ja, u Azure Dev Spaces gebruiken op AKS-clusters met [beperkt uitgangsverkeer voor clusterknooppunten][aks-restrict-egress-traffic] ingeschakeld zodra de juiste FQDN's zijn toegestaan. Meer informatie over het gebruik van een AKS-clusters met beperkt uitgangsverkeer voor clusterknooppunten die zijn ingeschakeld met Azure Dev Spaces, is [hier](configure-networking.md#ingress-and-egress-network-traffic-requirements)beschikbaar.

## <a name="can-i-use-azure-dev-spaces-on-rbac-enabled-aks-clusters"></a>Kan ik Azure Dev Spaces gebruiken op AKS-clusters met RBAC??

Ja, u Azure Dev Spaces gebruiken op AKS-clusters met of zonder RBAC ingeschakeld.

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>Wat gebeurt er als ik inschakel voor het project in Visual Studio?

Wanneer u Visual Studio gebruikt om uw project voor te bereiden, u binnendringen inschakelen voor uw service. Als u inschakeling inschakelt, wordt een openbaar eindpunt gemaakt om toegang te krijgen tot uw service wanneer deze wordt uitgevoerd op uw AKS-cluster, wat optioneel is. Als u invallen niet inschakelt, is uw service alleen toegankelijk vanuit uw AKS-cluster.

## <a name="can-i-use-pod-managed-identities-with-azure-dev-spaces"></a>Kan ik podbeheerde identiteiten gebruiken met Azure Dev Spaces?

Azure Dev Spaces biedt momenteel geen ondersteuning voor het gebruik van [podbeheerde identiteiten][aks-pod-managed-id] op AKS-clusters met Azure Dev Spaces ingeschakeld. Als u podbeheerde identiteiten hebt geïnstalleerd en deze wilt verwijderen, u meer details vinden in de [notities verwijderen.][aks-pod-managed-id-uninstall]

## <a name="can-i-use-azure-dev-spaces-with-multiple-microservices-in-an-application"></a>Kan ik Azure Dev Spaces gebruiken met meerdere microservices in een toepassing?

Ja, u Azure Dev Spaces gebruiken in een toepassing met meerdere microservices, maar u moet de afzonderlijke microservices bij de wortel voorbereiden en uitvoeren. De azure dev Spaces CLI, Azure Dev Spaces VS Code-extensie en de Visual Studio Azure Development-workload verwachten dat het *azds.yaml-bestand* aan de basis van de microservice ligt om uit te voeren en te debuggen. Zie de [voorbeeldtoepassing Bike Sharing][bike-sharing] voor een voorbeeld van meerdere microservices in één toepassing.

In Visual Studio Code is het mogelijk om [afzonderlijke projecten in één werkruimte][vs-code-multi-root-workspaces] te openen en ze afzonderlijk te debuggen via Azure Dev Spaces. Elk van de projecten moet op zichzelf staan en voorbereid zijn op Azure Dev Spaces.

In Visual Studio is het mogelijk om .NET Core-oplossingen te configureren voor foutopsporing via Azure Dev Spaces.

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-migration]: ../aks/aks-migration.md
[aks-pod-managed-id]: ../aks/developer-best-practices-pod-security.md#use-pod-managed-identities
[aks-pod-managed-id-uninstall]: https://github.com/Azure/aad-pod-identity#uninstall-notes
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[bike-sharing]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[dev-spaces-prep]: how-dev-spaces-works-prep.md
[dev-spaces-routing]: how-dev-spaces-works-routing.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[quickstart-cli]: quickstart-cli.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[vs-code-multi-root-workspaces]: https://code.visualstudio.com/docs/editor/multi-root-workspaces
[windows-containers]: how-to/run-dev-spaces-windows-containers.md