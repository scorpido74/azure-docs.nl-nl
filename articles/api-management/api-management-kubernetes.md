---
title: Azure API-beheer gebruiken met Microservices geïmplementeerd in Azure Kubernetes Service | Microsoft Documenten
description: In dit artikel worden de opties beschreven voor het implementeren van API-beheer met AKS
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/14/2019
ms.author: apimpm
ms.openlocfilehash: 1d6773b4daac256234c33bf50fb3736d585ac505
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75480994"
---
# <a name="use-azure-api-management-with-microservices-deployed-in-azure-kubernetes-service"></a>Azure API-beheer gebruiken met microservices die zijn geïmplementeerd in Azure Kubernetes-service

Microservices zijn perfect voor het bouwen van API's. Met [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) (AKS) u snel een op [microservices gebaseerde architectuur](https://docs.microsoft.com/azure/architecture/guide/architecture-styles/microservices) implementeren en beheren in de cloud. U azure [api beheer (API-beheer)](https://aka.ms/apimrocks) vervolgens gebruiken om uw microservices te publiceren als API's voor intern en extern verbruik. In dit artikel worden de opties beschreven voor het implementeren van API-beheer met AKS. Het veronderstelt basiskennis van Kubernetes, API Management en Azure-netwerken. 

## <a name="background"></a>Achtergrond

Bij het publiceren van microservices als API's voor consumptie, kan het een uitdaging zijn om de communicatie tussen de microservices en de clients die ze consumeren te beheren. Er is een veelheid van cross-cutting betreft, zoals authenticatie, autorisatie, beperking, caching, transformatie en monitoring. Deze problemen zijn geldig, ongeacht of de microservices worden blootgesteld aan interne of externe clients. 

Het [API-gatewaypatroon](https://docs.microsoft.com/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern) lost deze problemen op. Een API-gateway dient als voordeur naar de microservices, ontkoppelt clients van uw microservices, voegt een extra beveiligingslaag toe en vermindert de complexiteit van uw microservices door de last van het afhandelen van cross cutting-problemen te verwijderen. 

[Azure API Management](https://aka.ms/apimrocks) is een turnkey oplossing om uw API-gatewaybehoeften op te lossen. U snel een consistente en moderne gateway voor uw microservices maken en deze publiceren als API's. Als api-beheeroplossing voor de volledige levenscyclus biedt het ook extra mogelijkheden, waaronder een selfservice-ontwikkelaarsportal voor API-detectie, API-levenscyclusbeheer en API-analyses.

Bij gebruik samen bieden AKS en API Management een platform voor het implementeren, publiceren, beveiligen, bewaken en beheren van uw op microservices gebaseerde API's. In dit artikel zullen we een aantal opties doorlopen voor het implementeren van AKS in combinatie met API Management. 

## <a name="kubernetes-services-and-apis"></a>Kubernetes-services en API's

In een Kubernetes-cluster worden containers geïmplementeerd in [Pods](https://kubernetes.io/docs/concepts/workloads/pods/pod/), die vluchtig zijn en een levenscyclus hebben. Wanneer een werknemersknooppunt overlijdt, gaan de pods die op het knooppunt worden uitgevoerd, verloren. Daarom kan het IP-adres van een Pod op elk gewenst moment wijzigen. We kunnen er niet op vertrouwen om met de pod te communiceren. 

Om dit probleem op te lossen, introduceerde Kubernetes het concept van [Services.](https://kubernetes.io/docs/concepts/services-networking/service/) Een Kubernetes-service is een abstractielaag die een logische groep Pods definieert en externe verkeersblootstelling, taakverdeling en servicedetectie voor die Pods mogelijk maakt. 

Wanneer we klaar zijn om onze microservices als API's te publiceren via API-beheer, moeten we nadenken over hoe we onze Services in Kubernetes kunnen toewijzen aan API's in API-beheer. Er zijn geen vaste regels. Het hangt af van hoe u uw zakelijke mogelijkheden of domeinen in het begin hebt ontworpen en verdeeld in microservices. Als de pods achter een Service bijvoorbeeld verantwoordelijk zijn voor alle bewerkingen op een bepaalde resource (bijvoorbeeld klant), kan de Service worden toegewezen aan één API. Als bewerkingen op een resource zijn onderverdeeld in meerdere microservices (bijvoorbeeld GetOrder, PlaceOrder), kunnen meerdere Services logisch worden samengevoegd tot één API in API-beheer (zie Fig. 1). 

De mappings kunnen ook evolueren. Aangezien API Management een gevel creëert voor de microservices, kunnen we onze microservices in de loop van de tijd refactor en juist aanpassen. 

![Services toewijzen aan API's](./media/api-management-aks/service-api-mapping.png)

## <a name="deploy-api-management-in-front-of-aks"></a>API-beheer implementeren voor AKS

Er zijn een paar opties voor het implementeren van API Management voor een AKS-cluster. 

Hoewel een AKS-cluster altijd wordt geïmplementeerd in een virtueel netwerk (VNet), hoeft een API-beheerexemplaar niet te worden geïmplementeerd in een VNet. Wanneer API Management zich niet binnen het cluster VNet bevindt, moet het AKS-cluster openbare eindpunten publiceren waarmee API-beheer verbinding kan maken. In dat geval is er behoefte aan het beveiligen van de verbinding tussen API Management en AKS. Met andere woorden, we moeten ervoor zorgen dat het cluster alleen alleen toegankelijk is via API Management. Laten we de opties doornemen. 

### <a name="option-1-expose-services-publicly"></a>Optie 1: Services openbaar maken

Services in een AKS-cluster kunnen openbaar worden weergegeven met [servicetypen](https://docs.microsoft.com/azure/aks/concepts-network) NodePort, LoadBalancer of ExternalName. In dit geval zijn diensten rechtstreeks toegankelijk vanaf het openbare internet. Na het implementeren van API Management voor het cluster, moeten we ervoor zorgen dat al het inkomende verkeer door API-beheer gaat door verificatie toe te passen in de microservices. API-beheer kan bijvoorbeeld een toegangstoken opnemen in elk verzoek dat aan het cluster wordt gedaan. Elke microservice is verantwoordelijk voor het valideren van het token voordat het de aanvraag wordt verwerkt. 


Dit is misschien de eenvoudigste optie om API-beheer te implementeren voor AKS, vooral als u al verificatielogica hebt geïmplementeerd in uw microservices. 

![Services rechtstreeks publiceren](./media/api-management-aks/direct.png)

Pros:
* Eenvoudige configuratie aan de API-beheerkant omdat deze niet in het cluster VNet hoeft te worden geïnjecteerd
* Geen wijzigingen aan de AKS-kant als Services al openbaar zijn blootgesteld en verificatielogica al bestaat in microservices

Tegens:
* Potentieel beveiligingsrisico als gevolg van de zichtbaarheid van serviceeindpunten
* Geen single-entry point voor binnenkomend clusterverkeer
* Bemoeilijkt microservices met dubbele verificatielogica

### <a name="option-2-install-an-ingress-controller"></a>Optie 2: Een Ingress-controller installeren

Hoewel optie 1 misschien gemakkelijker is, heeft het opmerkelijke nadelen zoals hierboven vermeld. Als een API-beheerinstantie zich niet in het cluster VNet bevindt, is Wederzijdse TLS-verificatie (mTLS) een robuuste manier om ervoor te zorgen dat het verkeer veilig en vertrouwd is in beide richtingen tussen een API-beheerinstantie en een AKS-cluster. 

Wederzijdse TLS-verificatie [wordt native ondersteund](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates) door API Management en kan worden ingeschakeld in Kubernetes door het installeren van een [Ingress Controller](https://docs.microsoft.com/azure/aks/ingress-own-tls) (Fig. 3). Als gevolg hiervan wordt verificatie uitgevoerd in de Ingress Controller, wat de microservices vereenvoudigt. Bovendien u de IP-adressen van API-beheer toevoegen aan de toegestane lijst van Ingress om ervoor te zorgen dat alleen API-beheer toegang heeft tot het cluster.  

 
![Publiceren via een invallende controller](./media/api-management-aks/ingress-controller.png)


Pros:
* Eenvoudige configuratie aan de API-beheerkant omdat deze niet hoeft te worden geïnjecteerd in het cluster VNet en mTLS native wordt ondersteund
* Centraliseert de beveiliging voor binnenkomend clusterverkeer op de laag Ingress Controller
* Vermindert beveiligingsrisico's door openbaar zichtbare clustereindpunten te minimaliseren

Tegens:
* Verhoogt de complexiteit van de clusterconfiguratie als gevolg van extra werk om de Ingress-controller te installeren, configureren en onderhouden en certificaten te beheren die worden gebruikt voor mTLS
* Beveiligingsrisico's door publieke zichtbaarheid van eindpunt(en) van invallende controller


Wanneer u API's publiceert via API-beheer, is het eenvoudig en gebruikelijk om de toegang tot deze API's te beveiligen met abonnementssleutels. Ontwikkelaars die de gepubliceerde API's moeten gebruiken, moeten een geldige abonnementssleutel in HTTP-verzoeken opnemen wanneer ze naar deze API's bellen. Anders worden de aanroepen onmiddellijk geweigerd door de API Management-gateway. Ze worden niet doorgestuurd naar de back-end diensten.

Om een abonnementssleutel te krijgen voor toegang tot API's, is een abonnement vereist. Een abonnement is in wezen een benoemde container voor een paar abonnementssleutels. Ontwikkelaars die de gepubliceerde API's moeten gebruiken, kunnen abonnementen krijgen. En ze hebben geen goedkeuring nodig van API-uitgevers. API-uitgevers kunnen ook abonnementen rechtstreeks maken voor API-consumenten.

### <a name="option-3-deploy-apim-inside-the-cluster-vnet"></a>Optie 3: APIM implementeren in het cluster VNet

In sommige gevallen kunnen klanten met wettelijke beperkingen of strenge beveiligingsvereisten optie 1 en 2 niet-haalbare oplossingen vinden vanwege openbaar blootgestelde eindpunten. In andere landen kunnen het AKS-cluster en de toepassingen die de microservices verbruiken zich binnen hetzelfde VNet bevinden, vandaar dat er geen reden is om het cluster openbaar te maken, omdat al het API-verkeer binnen het VNet blijft. Voor deze scenario's u API-beheer implementeren in het cluster VNet. [API Management Premium-laag](https://aka.ms/apimpricing) ondersteunt VNet-implementatie. 

Er zijn twee manieren om [API Management te implementeren in een VNet](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet) - Extern en intern. 

Als API-consumenten zich niet in het cluster VNet bevinden, moet de externe modus (fig. 4) worden gebruikt. In deze modus wordt de API Management gateway geïnjecteerd in het cluster VNet, maar toegankelijk vanaf openbaar internet via een externe load balancer. Het helpt om het cluster volledig te verbergen, terwijl externe clients nog steeds de microservices kunnen gebruiken. Bovendien u Azure-netwerkmogelijkheden zoals Network Security Groups (NSG) gebruiken om het netwerkverkeer te beperken.

![Externe VNet-modus](./media/api-management-aks/vnet-external.png)

Als alle API-consumenten zich binnen het cluster VNet bevinden, kan de interne modus (fig. 5) worden gebruikt. In deze modus wordt de API Management gateway geïnjecteerd in het cluster VNET en alleen toegankelijk vanuit deze VNet via een interne load balancer. Er is geen manier om de API Management gateway of het AKS-cluster te bereiken vanaf openbaar internet. 

![Interne VNet-modus](./media/api-management-aks/vnet-internal.png)

 In beide gevallen is het AKS-cluster niet openbaar zichtbaar. In vergelijking met optie 2 is de Ingress Controller mogelijk niet nodig. Afhankelijk van uw scenario en configuratie is verificatie mogelijk nog steeds vereist tussen API-beheer en uw microservices. Als er bijvoorbeeld een Service Mesh wordt aangenomen, is er altijd wederzijdse TLS-verificatie nodig. 

Pros:
* De veiligste optie omdat het AKS-cluster geen openbaar eindpunt heeft
* Vereenvoudigt clusterconfiguratie omdat deze geen openbaar eindpunt heeft
* Mogelijkheid om zowel API-beheer als AKS in het VNet te verbergen met behulp van de interne modus
* Mogelijkheid om netwerkverkeer te beheren met Azure-netwerkmogelijkheden, zoals Network Security Groups (NSG)

Tegens:
* Verhoogt de complexiteit van het implementeren en configureren van API-beheer om binnen het VNet te werken

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [netwerkconcepten voor toepassingen in AKS](https://docs.microsoft.com/azure/aks/concepts-network)
* Meer informatie over [het gebruik van API-beheer met virtuele netwerken](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)





