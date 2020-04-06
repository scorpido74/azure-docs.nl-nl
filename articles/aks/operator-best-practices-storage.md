---
title: Aanbevolen procedures voor opslag en back-up
titleSuffix: Azure Kubernetes Service
description: Lees de aanbevolen procedures voor de clusteroperator voor opslag, gegevensversleuteling en back-ups in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 90abf2e36fd46c707904d87f00362091fe931743
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668102"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Aanbevolen procedures voor opslag en back-ups in Azure Kubernetes Service (AKS)

Terwijl u clusters maakt en beheert in Azure Kubernetes Service (AKS), hebben uw toepassingen vaak opslag nodig. Het is belangrijk om inzicht te krijgen in de prestatiebehoeften en toegangsmethoden voor pods, zodat u de juiste opslag voor toepassingen bieden. De grootte van het AKS-knooppunt kan van invloed zijn op deze opslagkeuzes. U moet ook plannen voor manieren om een back-up en test het herstelproces voor aangesloten opslag.

Dit artikel over aanbevolen procedures richt zich op opslagoverwegingen voor clusteroperators. In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Welke soorten opslag zijn beschikbaar
> * Aks-knooppunten correct verkleinen voor opslagprestaties
> * Verschillen tussen dynamische en statische inrichting van volumes
> * Manieren om een back-up te maken en uw gegevensvolumes te beveiligen

## <a name="choose-the-appropriate-storage-type"></a>Het juiste opslagtype kiezen

**Richtlijnen voor aanbevolen procedures** - Inzicht in de behoeften van uw toepassing om de juiste opslag te kiezen. Gebruik krachtige opslag met SSD-backs voor productieworkloads. Plan voor netwerkopslag wanneer er behoefte is aan meerdere gelijktijdige verbindingen.

Toepassingen vereisen vaak verschillende soorten en snelheden van opslag. Hebben uw toepassingen opslag nodig die verbinding maakt met afzonderlijke pods of die worden gedeeld via meerdere pods? Is de opslag voor alleen-lezen toegang tot gegevens, of om grote hoeveelheden gestructureerde gegevens te schrijven? Deze opslagbehoeften bepalen het meest geschikte type opslag om te gebruiken.

In de volgende tabel worden de beschikbare opslagtypen en hun mogelijkheden beschreven:

| Gebruiksvoorbeeld | Volumeplug-in | Eén keer lezen/schrijven | Alleen-lezen veel | Lees/schrijf veel | Ondersteuning voor Windows Server-container |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| Gedeelde configuratie       | Azure Files   | Ja | Ja | Ja | Ja |
| Gestructureerde app-gegevens        | Azure-schijven   | Ja | Nee  | Nee  | Ja |
| Ongestructureerde gegevens, bestandssysteembewerkingen | [BlobFuse BlobFuse][blobfuse] | Ja | Ja | Ja | Nee |

De twee primaire typen opslag voor volumes in AKS worden ondersteund door Azure Disks of Azure Files. Om de beveiliging te verbeteren, gebruiken beide typen opslag standaard Azure Storage Service Encryption (SSE) die gegevens in rust versleutelt. Schijven kunnen momenteel niet worden versleuteld met Azure Disk Encryption op het AKS-knooppuntniveau.

Azure-bestanden zijn momenteel beschikbaar in de standaardprestatielaag. Azure-schijven zijn beschikbaar in standaard- en Premium-prestatielagen:

- *Premium* schijven worden ondersteund door krachtige solid-state schijven (SSD's). Premium schijven worden aanbevolen voor alle productieworkloads.
- *Standaard* schijven worden ondersteund door gewone draaiende schijven (HDD's), en zijn goed voor archivering of zelden geopende gegevens.

Inzicht in de toepassingsprestaties en toegangspatronen om de juiste opslaglaag te kiezen. Zie overzicht van [Azure Managed Disks][managed-disks] voor meer informatie over groottes en prestatielagen van beheerde schijven

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Opslagklassen maken en gebruiken om toepassingsbehoeften te definiëren

Het type opslag dat u gebruikt, wordt gedefinieerd met *Kubernetes-opslagklassen.* Er wordt vervolgens naar de opslagklasse verwezen in de pod of implementatiespecificatie. Deze definities werken samen om de juiste opslag te maken en deze aan te sluiten op pods. Zie [Opslagklassen in AKS][aks-concepts-storage-classes]voor meer informatie.

## <a name="size-the-nodes-for-storage-needs"></a>Grootte van de knooppunten voor opslagbehoeften

**Richtlijnen voor beste praktijken** - Elke knooppuntgrootte ondersteunt een maximum aantal schijven. Verschillende knooppuntgroottes bieden ook verschillende hoeveelheden lokale opslag en netwerkbandbreedte. Plan de vereisten van uw toepassing om de juiste grootte van knooppunten te implementeren.

AKS-knooppunten worden uitgevoerd als Azure VM's. Er zijn verschillende soorten en maten vm beschikbaar. Elke VM-grootte biedt een andere hoeveelheid kernbronnen, zoals CPU en geheugen. Deze VM-formaten hebben een maximum aantal schijven dat kan worden bevestigd. De opslagprestaties variëren ook tussen VM-formaten voor de maximale lokale en gekoppelde schijf-IOPS (invoer-/uitvoerbewerkingen per seconde).

Als uw toepassingen Azure Disks als opslagoplossing vereisen, plant en kiest u een geschikte virtuele VM-grootte. De hoeveelheid CPU en geheugen is niet de enige factor wanneer u een VM-grootte kiest. De opslagmogelijkheden zijn ook belangrijk. Zowel de *Standard_B2ms* als *Standard_DS2_v2* VM-formaten bevatten bijvoorbeeld een vergelijkbare hoeveelheid CPU- en geheugenbronnen. De potentiële opslagprestaties zijn verschillend, zoals in de volgende tabel wordt weergegeven:

| Knooppunttype en grootte | vCPU | Geheugen (GiB) | Max. aantal gegevensschijven | IOPS zonder cache | Maximale doorvoer zonder cache (MBps) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1,920                  | 22.5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6,400                  | 96                             |

Hier, de *Standard_DS2_v2* maakt het dubbele van het aantal aangesloten schijven, en biedt drie tot vier keer de hoeveelheid IOPS en schijfdoorvoer. Als u alleen naar de belangrijkste rekenbronnen hebt *Standard_B2ms* gekeken en de kosten hebt vergeleken, u de Standard_B2ms-vm-grootte kiezen en slechte opslagprestaties en -beperkingen hebben. Werk samen met uw team voor applicatieontwikkeling om inzicht te krijgen in hun opslagcapaciteit en prestatiebehoeften. Kies de juiste VM-grootte voor de AKS-knooppunten om aan hun prestatiebehoeften te voldoen of deze te overtreffen. Regelmatig basislijntoepassingen om de VM-grootte zo nodig aan te passen.

Zie [Grootte voor virtuele Linux-machines in Azure voor][vm-sizes]meer informatie over beschikbare VM-formaten.

## <a name="dynamically-provision-volumes"></a>Dynamisch voorzieningsvolumes

**Richtlijnen voor aanbevolen procedures** - Om de beheeroverhead te verminderen en u te laten schalen, u geen permanente volumes maken en toewijzen. Gebruik dynamische inrichting. Definieer in uw opslagklassen het juiste terugvorderingsbeleid om onnodige opslagkosten te minimaliseren zodra pods zijn verwijderd.

Wanneer u opslag aan pods moet koppelen, gebruikt u aanhoudende volumes. Deze permanente volumes kunnen handmatig of dynamisch worden gemaakt. Handmatige creatie van permanente volumes voegt beheeroverhead toe en beperkt uw vermogen om te schalen. Gebruik dynamische permanente volumebepaling om het opslagbeheer te vereenvoudigen en uw toepassingen zo nodig te laten groeien en schalen.

![Permanente volumeclaims in een AKS-cluster (Azure Kubernetes Services)](media/concepts-storage/persistent-volume-claims.png)

Met een permanente volumeclaim (PVC) u dynamisch opslag maken als dat nodig is. De onderliggende Azure-schijven worden gemaakt terwijl pods daarom vragen. In de poddefinitie vraagt u om een volume te maken en aan een aangewezen bergpad te koppelen.

Zie [Persistent Volumes Claims][aks-concepts-storage-pvcs]voor de concepten voor het dynamisch maken en gebruiken van volumes.

Als u deze volumes in actie wilt zien, ziet u hoe u dynamisch een permanent volume maakt en gebruikt met [Azure Disks][dynamic-disks] of [Azure Files.][dynamic-files]

Stel als onderdeel van de definities van uw opslagklasse het juiste *terugvorderen beleid*in . Met deze terugvorderingregelt Beleid het gedrag van de onderliggende Azure-opslagbron wanneer de pod wordt verwijderd en het aanhoudende volume mogelijk niet meer vereist is. De onderliggende opslagbron kan worden verwijderd of behouden voor gebruik met een toekomstige pod. Het terugvorderingsbeleid kan worden ingesteld om *te behouden* of *te verwijderen*. Begrijp uw toepassingsbehoeften en implementeer regelmatig controles voor opslag die worden bewaard om de hoeveelheid niet-gebruikte opslag die wordt gebruikt en gefactureerd te minimaliseren.

Zie beleid voor [opslagclaim][reclaim-policy]voor meer informatie over opties voor opslagklasse .

## <a name="secure-and-back-up-your-data"></a>Uw gegevens beveiligen en een back-up maken

**Richtlijnen voor aanbevolen procedures** : back-ups maken van uw gegevens met behulp van een geschikt hulpmiddel voor uw opslagtype, zoals Velero of Azure Site Recovery. Controleer de integriteit en beveiliging van die back-ups.

Wanneer uw toepassingen gegevens opslaan en verbruiken, moet u regelmatig back-ups of momentopnamen van die gegevens maken. Azure Disks kan gebruik maken van ingebouwde momentopnametechnologieën. Mogelijk moet u zoeken naar uw toepassingen om schrijfbewerkingen naar de schijf door te spoelen voordat u de momentopnamebewerking uitvoert. [Velero][velero] kan een back-up maken van permanente volumes, samen met extra clusterbronnen en configuraties. Als u de status niet uit uw toepassingen [verwijderen,][remove-state]maakt u een back-up van de gegevens van permanente volumes en test u regelmatig de herstelbewerkingen om de gegevensintegriteit en de vereiste processen te verifiëren.

Begrijp de beperkingen van de verschillende benaderingen van back-ups van gegevens en als u uw gegevens moet quiesce voorafgaand aan snapshot. Met back-ups van gegevens u niet per se uw toepassingsomgeving van clusterimplementatie herstellen. Zie Aanbevolen procedures voor [bedrijfscontinuïteit en herstel na noodgevallen in AKS voor][best-practices-multi-region]meer informatie over deze scenario's.

## <a name="next-steps"></a>Volgende stappen

Dit artikel richtte zich op best practices voor opslag in AKS. Zie [Opslagconcepten voor toepassingen in AKS voor][aks-concepts-storage]meer informatie over de basisprincipes van opslag in Kubernetes.

<!-- LINKS - External -->
[velero]: https://github.com/heptio/velero
[blobfuse]: https://github.com/Azure/azure-storage-fuse

<!-- LINKS - Internal -->
[aks-concepts-storage]: concepts-storage.md
[vm-sizes]: ../virtual-machines/linux/sizes.md
[dynamic-disks]: azure-disks-dynamic-pv.md
[dynamic-files]: azure-files-dynamic-pv.md
[reclaim-policy]: concepts-storage.md#storage-classes
[aks-concepts-storage-pvcs]: concepts-storage.md#persistent-volume-claims
[aks-concepts-storage-classes]: concepts-storage.md#storage-classes
[managed-disks]: ../virtual-machines/linux/managed-disks-overview.md
[best-practices-multi-region]: operator-best-practices-multi-region.md
[remove-state]: operator-best-practices-multi-region.md#remove-service-state-from-inside-containers
