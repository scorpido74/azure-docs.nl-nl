---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 05/29/2018
ms.author: azcspmt;jonbeck;cynthn;amverma
ms.custom: include file
ms.openlocfilehash: 0930fa371500125c15cd969b9e9f4b7a2853612e
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174946"
---
## <a name="deployment-considerations"></a>Overwegingen bij de implementatie
* **Azure-abonnement** : als u meer dan een aantal computerintensieve exemplaren wilt implementeren, kunt u een abonnement op basis van betalen naar gebruik of andere aankoop opties overwegen. Als u een [gratis account van Azure](https://azure.microsoft.com/free/) gebruikt, kunt u slechts een paar Azure Compute-resources van Azure gebruiken.

* **Prijzen en beschik baarheid** : deze VM-grootten worden alleen aangeboden in de prijs categorie Standard. Controleer of de beschik [bare producten per regio beschikbaar zijn](https://azure.microsoft.com/global-infrastructure/services/) in azure-regio's. 
* **Quotum voor kernen** : u moet mogelijk het quotum voor kernen in uw Azure-abonnement verhogen van de standaard waarde. Uw abonnement kan ook het aantal kernen beperken dat u kunt implementeren in bepaalde VM-grootte families, inclusief de H-serie. Als u een quotum toename wilt aanvragen, opent u gratis [een aanvraag voor een online klant ondersteuning](../articles/azure-supportability/how-to-create-azure-support-request.md) . (De standaard limieten kunnen variëren, afhankelijk van de categorie abonnement.)
  
  > [!NOTE]
  > Neem contact op met de ondersteuning van Azure als er grootschalige capaciteits behoeften zijn. Azure-quota zijn krediet limieten, geen capaciteits garanties. Ongeacht uw quotum worden er alleen kosten in rekening gebracht voor kernen die u gebruikt.
  > 
  > 
* **Virtueel netwerk** : een virtueel Azure- [netwerk](https://azure.microsoft.com/documentation/services/virtual-network/) is niet vereist voor het gebruik van de compute-intensieve exemplaren. Voor veel implementaties hebt u echter mini maal een Azure Virtual Network op basis van de Cloud of een site-naar-site-verbinding nodig als u toegang nodig hebt tot on-premises resources. Als dat nodig is, maakt u een nieuw virtueel netwerk om de exemplaren te implementeren. Het toevoegen van Compute-intensieve Vm's aan een virtueel netwerk in een affiniteits groep wordt niet ondersteund.
* **Grootte wijzigen** : vanwege hun gespecialiseerde hardware kunt u alleen Compute-intensieve instanties binnen dezelfde grootte familie (H-serie of COMPUTE-intensieve A-serie) wijzigen. U kunt bijvoorbeeld alleen het formaat van een VM van de H-serie wijzigen van de grootte van de h-serie in een andere. Bovendien wordt het wijzigen van de grootte van een niet-reken intensief formaat naar een berekenings intensief formaat niet ondersteund.  

## <a name="rdma-capable-instances"></a>Met RDMA compatibele exemplaren
Een subset van de computerintensieve instanties (A8, A9, H16r, H16mr, HB en HC) functie een netwerk interface voor RDMA-connectiviteit (Remote Direct Memory Access). De geselecteerde N-serie-grootten die zijn aangewezen met r, zoals de NC24rs-configuraties (NC24rs_v2 en NC24rs_v3), zijn ook geschikt voor RDMA. Deze interface is een aanvulling op de standaard Azure-netwerk interface die beschikbaar is voor andere VM-grootten. 
  
Met deze interface kunnen de RDMA-compatibele instanties via een InfiniBand (IB)-netwerk communiceren, met behulp van EDR-tarieven voor HB, HC, FDR-tarieven voor virtuele machines van de N-serie van H16r, H16mr en RDMA, en zijn de prijzen voor A8 en A9 virtual machines. Deze RDMA-mogelijkheden kunnen de schaal baarheid en prestaties van bepaalde MPI-toepassingen (Message Passing Interface) verhogen. Zie de details in de tabellen op deze pagina voor meer informatie over de snelheid.

> [!NOTE]
> In azure wordt IP over IB alleen ondersteund op de SR-IOV ingeschakelde Vm's (SR-IOV voor InfiniBand, momenteel HB en HC). RDMA via IB wordt ondersteund voor alle exemplaren met RDMA-functionaliteit.
>

