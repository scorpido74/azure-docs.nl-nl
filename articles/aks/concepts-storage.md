---
title: Concepten - Opslag in Azure Kubernetes Services (AKS)
description: Meer informatie over opslag in Azure Kubernetes Service (AKS), inclusief volumes, aanhoudende volumes, opslagklassen en claims
services: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: 4bb19d7da971a82aef9c0e1fc092cc648ac49c4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595991"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Opslagopties voor toepassingen in Azure Kubernetes Service (AKS)

Toepassingen die worden uitgevoerd in Azure Kubernetes Service (AKS) moeten mogelijk gegevens opslaan en ophalen. Voor sommige toepassingsworkloads kan deze gegevensopslag lokale, snelle opslag op het knooppunt gebruiken die niet meer nodig is wanneer de pods worden verwijderd. Andere toepassingsworkloads vereisen mogelijk opslag die blijft bestaan op meer regelmatige gegevensvolumes binnen het Azure-platform. Mogelijk moeten meerdere pods dezelfde gegevensvolumes delen of gegevensvolumes opnieuw koppelen als de pod opnieuw op een ander knooppunt wordt gepland. Ten slotte moet u mogelijk gevoelige gegevens of configuratiegegevens van toepassingen in pods injecteren.

![Opslagopties voor toepassingen in een AKS-cluster (Azure Kubernetes Services)](media/concepts-storage/aks-storage-options.png)

Dit artikel introduceert de kernconcepten die opslag bieden aan uw toepassingen in AKS:

- [Volumes](#volumes)
- [Permanente volumes](#persistent-volumes)
- [Opslagklassen](#storage-classes)
- [Claims voor permanente volumes](#persistent-volume-claims)

## <a name="volumes"></a>Volumes

Toepassingen moeten vaak gegevens kunnen opslaan en ophalen. Aangezien Kubernetes individuele pods doorgaans behandelt als kortstondige, beschikbare resources, zijn er verschillende benaderingen beschikbaar voor toepassingen om gegevens te gebruiken en zo nodig te blijven bestaan. Een *volume* is een manier om gegevens op te slaan, op te halen en te blijven bestaan in pods en gedurende de levenscyclus van de toepassing.

Traditionele volumes voor het opslaan en ophalen van gegevens worden gemaakt als Kubernetes-bronnen die worden ondersteund door Azure Storage. U deze gegevensvolumes handmatig maken om rechtstreeks aan pods te worden toegewezen of kubernetes deze automatisch laten maken. Deze gegevensvolumes kunnen Azure Disks of Azure Files gebruiken:

- *Azure Disks* kan worden gebruikt om een Kubernetes *DataDisk-bron* te maken. Schijven kunnen Azure Premium-opslag gebruiken, ondersteund door krachtige SSD's of Azure Standard-opslag, ondersteund door gewone HDD's. Gebruik Premium-opslag voor de meeste productie- en ontwikkelingsworkloads. Azure Disks zijn gemonteerd als *ReadWriteOnce,* dus zijn alleen beschikbaar voor een enkele pod. Gebruik Azure Files voor opslagvolumes die met meerdere pods tegelijk toegankelijk zijn.
- *Azure-bestanden* kunnen worden gebruikt om een SMB 3.0-share te monteren dat wordt ondersteund door een Azure Storage-account op pods. Met bestanden u gegevens delen over meerdere knooppunten en pods. Bestanden kunnen Azure Standard-opslag gebruiken die wordt ondersteund door gewone HDD's of Azure Premium-opslag, ondersteund door krachtige SSD's.
> [!NOTE] 
> Azure Files ondersteunen premium opslag in AKS-clusters met Kubernetes 1.13 of hoger.

In Kubernetes kunnen volumes meer vertegenwoordigen dan alleen een traditionele schijf waar informatie kan worden opgeslagen en opgehaald. Kubernetes-volumes kunnen ook worden gebruikt als een manier om gegevens in een pod te injecteren voor gebruik door de containers. Veelvoorkomende extra volumetypen in Kubernetes zijn:

- *emptyDir* - Dit volume wordt vaak gebruikt als tijdelijke ruimte voor een pod. Alle containers in een pod hebben toegang tot de gegevens op het volume. Gegevens die naar dit volumetype zijn geschreven, blijven alleen bestaan voor de levensduur van de pod - wanneer de pod wordt verwijderd, wordt het volume verwijderd. Dit volume maakt meestal gebruik van de onderliggende lokale node schijfopslag, hoewel het ook alleen kan bestaan in het geheugen van het knooppunt.
- *geheim* - Dit volume wordt gebruikt om gevoelige gegevens in pods te injecteren, zoals wachtwoorden. U maakt eerst een geheim met behulp van de Kubernetes API. Wanneer u uw pod of implementatie definieert, kan een specifiek geheim worden aangevraagd. Geheimen worden alleen verstrekt aan knooppunten die een geplande pod die het vereist hebben, en het geheim wordt opgeslagen in *tmpfs,* niet geschreven naar schijf. Wanneer de laatste pod op een knooppunt waarvoor een geheim vereist is, wordt verwijderd uit de tmpfs van het knooppunt. Geheimen worden opgeslagen in een bepaalde naamruimte en zijn alleen toegankelijk via pods binnen dezelfde naamruimte.
- *configMap* - Dit volumetype wordt gebruikt om eigenschappen van sleutelwaardenpaar in pods te injecteren, zoals configuratie-informatie over toepassingen. In plaats van toepassingsconfiguratiegegevens in een containerafbeelding te definiëren, u deze definiëren als een Kubernetes-bron die eenvoudig kan worden bijgewerkt en toegepast op nieuwe exemplaren van pods wanneer deze worden geïmplementeerd. Net als met een Secret maakt u eerst een ConfigMap met de Kubernetes API. Deze ConfigMap kan vervolgens worden aangevraagd wanneer u een pod of implementatie definieert. ConfigMaps worden opgeslagen in een bepaalde naamruimte en is alleen toegankelijk via pods binnen dezelfde naamruimte.

## <a name="persistent-volumes"></a>Permanente volumes

Volumes die zijn gedefinieerd en gemaakt als onderdeel van de levenscyclus van de pod, bestaan alleen totdat de pod is verwijderd. Pods verwachten vaak dat hun opslag blijft als een pod tijdens een onderhoudsgebeurtenis opnieuw op een andere host wordt gepland, vooral in StatefulSets. Een *persistent volume* (PV) is een opslagbron die is gemaakt en beheerd door de Kubernetes API die kan bestaan na de levensduur van een afzonderlijke pod.

Azure-schijven of -bestanden worden gebruikt om persistentvolume op te geven. Zoals opgemerkt in het vorige gedeelte over volumes, wordt de keuze van schijven of bestanden vaak bepaald door de noodzaak van gelijktijdige toegang tot de gegevens of de prestatielaag.

![Permanente volumes in een AKS-cluster (Azure Kubernetes Services)](media/concepts-storage/persistent-volumes.png)

Een PersistentVolume kan *statisch* worden gemaakt door een clusterbeheerder of *dynamisch* worden gemaakt door de Kubernetes API-server. Als een pod is gepland en opslag opvraagt die momenteel niet beschikbaar is, kan Kubernetes de onderliggende Azure Disk- of Bestanden-opslag maken en deze aan de pod koppelen. Dynamische inrichting maakt gebruik van een *StorageClass* om te bepalen welk type Azure-opslag moet worden gemaakt.

## <a name="storage-classes"></a>Opslagklassen

Als u verschillende opslagniveaus wilt definiëren, zoals Premium en Standard, u een *StorageClass*maken. De StorageClass definieert ook het *reclaimPolicy*. Met deze terugvorderingregelt Beleid het gedrag van de onderliggende Azure-opslagbron wanneer de pod wordt verwijderd en het aanhoudende volume mogelijk niet meer vereist is. De onderliggende opslagbron kan worden verwijderd of behouden voor gebruik met een toekomstige pod.

In AKS worden twee initiële StorageClasses gemaakt:

- *standaard* - Gebruikt Azure Standard-opslag om een beheerde schijf te maken. Het terugwinbeleid geeft aan dat de onderliggende Azure Disk wordt verwijderd wanneer het permanente volume dat het heeft gebruikt, wordt verwijderd.
- *managed-premium* - Gebruikt Azure Premium-opslag om Beheerde schijf te maken. Het terugvorderingsbeleid geeft opnieuw aan dat de onderliggende Azure-schijf wordt verwijderd wanneer het permanente volume dat het heeft gebruikt, wordt verwijderd.

Als er geen StorageClass is opgegeven voor een persistent volume, wordt de standaard StorageClass gebruikt. Wees voorzichtig bij het aanvragen van permanente volumes, zodat ze gebruik maken van de juiste opslag die u nodig hebt. U een StorageClass maken `kubectl`voor extra behoeften met behulp van. In het volgende voorbeeld wordt Premium Managed Disks gebruikt en wordt aangegeven dat de onderliggende Azure-schijf moet worden *behouden* wanneer de pod wordt verwijderd:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-retain
provisioner: kubernetes.io/azure-disk
reclaimPolicy: Retain
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
```

## <a name="persistent-volume-claims"></a>Claims voor permanente volumes

Een PersistentVolumeClaim vraagt schijf- of bestandsopslag van een bepaalde StorageClass, toegangsmodus en grootte aan. De Kubernetes API-server kan de onderliggende opslagbron in Azure dynamisch inrichten als er geen bestaande bron is om de claim te vervullen op basis van de gedefinieerde StorageClass. De poddefinitie omvat de volumehouder zodra het volume is aangesloten op de pod.

![Permanente volumeclaims in een AKS-cluster (Azure Kubernetes Services)](media/concepts-storage/persistent-volume-claims.png)

Een persistentvolume is *gebonden aan* een PersistentVolumeClaim zodra een beschikbare opslagbron is toegewezen aan de pod die deze aanvraag. Er is een 1:1 mapping van persistente volumes aan claims.

In het volgende voorbeeld yaml-manifest wordt een aanhoudende volumeclaim weergegeven die gebruikmaakt van de *beheerde opslagklasse* en een Disk *5Gi-in* grootte aanvraagt:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

Wanneer u een poddefinitie maakt, wordt de claim voor permanente hoeveelheden opgegeven om de gewenste opslag op te vragen. U geeft vervolgens ook het *volumeOpvoor* uw toepassingen om gegevens te lezen en te schrijven. In het volgende voorbeeld yaml-manifest ziet u hoe de vorige claim voor persistent volume kan worden gebruikt om een volume op */mnt/azure*te monteren:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

## <a name="next-steps"></a>Volgende stappen

Zie [Aanbevolen procedures voor opslag en back-ups in AKS voor][operator-best-practices-storage]bijbehorende aanbevolen procedures.

Zie de volgende how-to-artikelen voor het maken van dynamische en statische volumes die Azure Disks of Azure Files gebruiken:

- [Een statisch volume maken met Azure-schijven][aks-static-disks]
- [Een statisch volume maken met Azure-bestanden][aks-static-files]
- [Een dynamisch volume maken met Azure Disks][aks-dynamic-disks]
- [Een dynamisch volume maken met Azure Files][aks-dynamic-files]

Zie de volgende artikelen voor meer informatie over de belangrijkste Kubernetes- en AKS-concepten:

- [Kubernetes / AKS-clusters en workloads][aks-concepts-clusters-workloads]
- [Kubernetes / AKS-identiteit][aks-concepts-identity]
- [Kubernetes / AKS-beveiliging][aks-concepts-security]
- [Kubernetes / AKS virtuele netwerken][aks-concepts-network]
- [Kubernetes / AKS-schaal][aks-concepts-scale]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[aks-static-disks]: azure-disk-volume.md
[aks-static-files]: azure-files-volume.md
[aks-dynamic-disks]: azure-disks-dynamic-pv.md
[aks-dynamic-files]: azure-files-dynamic-pv.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-storage]: operator-best-practices-storage.md
