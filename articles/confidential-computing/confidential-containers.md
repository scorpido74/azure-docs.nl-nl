---
title: Vertrouwelijke containers op Azure Kubernetes service (AKS)
description: Meer informatie over niet-gewijzigde container ondersteuning op vertrouwelijke containers.
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 9/22/2020
ms.author: amgowda
ms.service: container-service
ms.openlocfilehash: 528b843e24e1d63e4822c253b3636ef490e8fe8e
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90995838"
---
# <a name="confidential-containers"></a>Vertrouwelijke containers

## <a name="overview"></a>Overzicht

Ontwikkel aars in staat stellen een **bestaande docker-toepassing (nieuw of bestaand)** te gebruiken en deze veilig uit te voeren op Azure Kubernetes service (AKS) via ondersteuning voor vertrouwelijke computer knooppunten.

Vertrouwelijke containers beschermen:

- gegevens integriteit 
- vertrouwelijkheid van gegevens
- code-integriteit
- container code beveiliging via versleuteling
- op hardware gebaseerde garanties
- uitvoeren van bestaande apps toestaan
- de hoofdmap van de vertrouwens relatie van de hardware maken

Een op hardware gebaseerde Trusted Execution Environment (TEE) is een belang rijk onderdeel dat wordt gebruikt om sterke garanties te bieden via hardware-en software metingen van TCB-onderdelen (Trusted Computing Base). Verificatie van deze metingen helpt bij het valideren van de verwachte berekening en het verifiëren van het onrecht matig wijzigen van de container-apps.

Vertrouwelijke containers bieden ondersteuning voor aangepaste toepassingen die zijn ontwikkeld met **python, Java, node js, enzovoort, of verpakte software toepassingen zoals NGINX, redis cache, MemCache**, enzovoort, die ongewijzigd kunnen worden uitgevoerd op AKS.

Vertrouwelijke containers zijn het snelste pad naar de container vertrouwelijkheid, met inbegrip van de container beveiliging via versleuteling, waardoor liften en verschuivingen zonder/minimale wijzigingen in uw bedrijfs logica.

![De conversie van de vertrouwelijke container](./media/confidential-containers/conf-con-deploy-process.jpg)


## <a name="confidential-container-enablers"></a>Vertrouwelijke container inschakelen

Als u een bestaande docker-container wilt uitvoeren, moeten toepassingen op vertrouwelijke computing knooppunten een abstractie laag of SGX-software gebruiken om gebruik te maken van de speciale CPU-instructieset. Met de SGX-software kan uw gevoelige toepassings code ook worden beveiligd en wordt een directe uitvoering naar CPU gemaakt om het gast besturingssysteem, de host OS of de Hyper Visor te verwijderen. Deze bescherming vermindert de algehele kwets bare kwets gebieden en beveiligings problemen met de lagen van het besturings systeem of Hyper Visor.

Vertrouwelijke containers worden volledig ondersteund op AKS en ingeschakeld via Azure-partners en OSS-projecten (open source software). Ontwikkel aars kunnen software providers kiezen op basis van de functies, integratie met Azure-Services en hulpprogramma ondersteuning.

## <a name="partner-enablers"></a>Partner-Ontwikkelers
> [!NOTE]
> De onderstaande oplossingen worden aangeboden via Azure-partners en kunnen licentie kosten in rekening worden gebracht. Controleer de software voorwaarden van de partner onafhankelijk. 

### <a name="fortanix"></a>Fortanix

[Fortanix](https://www.fortanix.com/) biedt ontwikkel aars een keuze uit een portal en een CLI-ervaring om hun toepassingen in containers te plaatsen en deze te converteren naar voor SGX geschikte, vertrouwelijke containers zonder dat ze de toepassing hoeven te wijzigen of opnieuw te compileren. Fortanix biedt de flexibiliteit om de breedste set toepassingen uit te voeren en te beheren, waaronder bestaande toepassingen, nieuwe enclave-systeem eigen toepassingen en vooraf verpakte toepassingen. Gebruikers kunnen beginnen met de gebruikers interface van [enclave Manager](https://em.fortanix.com/) of [rest api's](https://www.fortanix.com/api/em/) om vertrouwelijke containers te maken door de [Quick Start](https://support.fortanix.com/hc/en-us/articles/360049658291-Fortanix-Confidential-Container-on-Azure-Kubernetes-Service) gids voor de Azure Kubernetes-service te volgen.

![Fortanix-implementatie proces](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>Scone (Scontain)

[Scone](https://scontain.com/index.html?lang=en) biedt ondersteuning voor beveiligings beleid dat certificaten, sleutels en geheimen kan genereren, en ervoor zorgt dat ze alleen zichtbaar zijn voor de certificering van een toepassing. Op deze manier worden de services van een toepassing automatisch met behulp van TLS verklaard, zonder dat u de toepassingen noch TLS hoeft te wijzigen. Dit wordt uitgelegd aan de hand van een eenvoudige fles toepassing: https://sconedocs.github.io/flask_demo/  

SCONE kan bestaande binaire bestanden converteren naar toepassingen die worden uitgevoerd binnen enclaves zonder dat de toepassing hoeft te worden gewijzigd of dat de toepassing opnieuw moet worden gecompileerd. SCONE beschermt ook geïnterpreteerde talen als python door zowel gegevens bestanden als Python-code bestanden te versleutelen. Met de hulp van een SCONE-beveiligings beleid kan een van de versleutelde bestanden worden beveiligd tegen onbevoegde toegang, wijzigingen en terugdraai acties. Hoe ' sconify ' een bestaande python-toepassing wordt [hier](https://sconedocs.github.io/sconify_image/) beschreven

![Scontain-stroom](./media/confidential-containers/scone-workflow.png)

Scone-implementaties op vertrouwelijke computing knooppunten met AKS worden volledig ondersteund en geïntegreerd. Ga hier aan de slag met een voorbeeld toepassing https://sconedocs.github.io/aks/

### <a name="anjuna"></a>Anjuna

[Anjuna](https://www.anjuna.io/) biedt software voor SGX-platforms waarmee u ongewijzigde containers kunt uitvoeren op AKS. Lees [hier](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)meer over de **aanstaande** functionaliteit en de gebruikers stroom.

Ga [hier](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp) aan de slag met een voor beeld-redis cache en een aangepaste python-toepassing

![Anjuna-proces](./media/confidential-containers/anjuna-process-flow.png)

## <a name="oss-enablers"></a>OSS-Schakelers 
> [!NOTE]
> De onderstaande oplossingen worden aangeboden via open-source projecten en zijn niet rechtstreeks gekoppeld aan Azure vertrouwelijk Computing (ACC) of micro soft.  

### <a name="graphene"></a>Graphene

[Graphene](https://grapheneproject.io/) is een licht gewicht gast besturingssysteem, ontworpen om één Linux-toepassing met minimale vereisten voor de host uit te voeren. Graphene kan toepassingen uitvoeren in een geïsoleerde omgeving met voor delen die vergelijkbaar zijn met het uitvoeren van een complete versie van het besturings systeem en heeft goede hulp middelen voor het converteren van bestaande docker-container toepassing naar graphene afgeschermde containers.

Ga [hier](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks) aan de slag met een voorbeeld toepassing en-implementatie op AKS

### <a name="occlum"></a>Occlum
[Occlum](https://occlum.io/) is een geheugen veilige, multi-process bibliotheek besturingssysteem (LibOS) voor Intel SGX. Hierdoor kunnen oudere toepassingen worden uitgevoerd op SGX met weinig wijzigingen in de bron code. Occlum beveiligt de vertrouwelijkheid van werk belastingen van gebruikers, terwijl een eenvoudige lift en verschuiving kan worden uitgevoerd naar bestaande docker-toepassingen.

Occlum biedt ondersteuning voor AKS-implementaties. Volg de instructies voor de implementatie van de verschillende voor beelden [van apps](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md)


## <a name="confidential-containers-demo"></a>Demo van vertrouwelijke containers
Bekijk de demo van de vertrouwelijke gezondheids zorg met vertrouwelijke containers. Voor beeld is [hier](https://github.com/Azure-Samples/confidential-container-samples/blob/main/confidential-healthcare-scone-confinf-onnx/README.md)beschikbaar. 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>Contact opnemen

Hebt u vragen over uw implementatie of wilt u een enabler worden? Een e-mail verzenden naar acconaks@microsoft.com

## <a name="reference-links"></a>Referentie koppelingen

[Microsoft Azure Attestation](../attestation/overview.md)

[DCsv2 Virtual Machines](virtual-machine-solutions.md)

[Azure Kubernetes Service (AKS)](../aks/intro-kubernetes.md)
