---
title: Veelvoorkomende problemen met de Azure Kubernetes-service oplossen
description: Meer informatie over het oplossen van veelvoorkomende problemen bij het gebruik van Azure Kubernetes service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: saudas
ms.openlocfilehash: df3ca877570b6b3e3a34dd20d617ce3896f1dd99
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76120958"
---
# <a name="aks-troubleshooting"></a>AKS problemen oplossen

Wanneer u Azure Kubernetes service (AKS)-clusters maakt of beheert, kunnen er af en toe problemen optreden. In dit artikel worden enkele veelvoorkomende problemen beschreven en stappen voor probleem oplossing.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Waar vind ik informatie over problemen met fout opsporing in Kubernetes in het algemeen?

Probeer de [officiële hand leiding voor het oplossen van problemen met Kubernetes-clusters](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Er is ook een [hand leiding](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)voor het oplossen van problemen, gepubliceerd door een micro soft-Engineer voor het oplossen van problemen met peulen, knoop punten, clusters en andere functies.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Ik krijg de fout ' quotum overschreden ' tijdens het maken of upgraden. Wat zal ik doen? 

U moet [kernen aanvragen](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Wat is het maximum aantal instellingen per knoop punt voor AKS?

De maximale instelling per knoop punt is 30 standaard als u een AKS-cluster implementeert in de Azure Portal.
De maximale instelling per knoop punt is standaard 110 als u een AKS-cluster implementeert in de Azure CLI. (Zorg ervoor dat u de nieuwste versie van de Azure CLI gebruikt). Deze standaard instelling kan worden gewijzigd met behulp van de vlag `–-max-pods` in de `az aks create` opdracht.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Er wordt een insufficientSubnetSize-fout opgetreden tijdens het implementeren van een AKS-cluster met een geavanceerd netwerk. Wat zal ik doen?

Als Azure CNI (Advanced Networking) wordt gebruikt, wijst AKS IP-adressen toe op basis van het maximum aantal-peulen per knoop punt dat is geconfigureerd. Op basis van het geconfigureerde maximum van Peul per knoop punt moet de grootte van het subnet groter zijn dan het product van het aantal knoop punten en de maximum waarde voor pod per knoop punt. De volgende vergelijking bevat een overzicht:

De grootte van het subnet > aantal knoop punten in het cluster (waarbij rekening wordt gehouden met de toekomstige schaal vereisten) * Max.

Zie [IP-adres sering voor uw cluster plannen](configure-azure-cni.md#plan-ip-addressing-for-your-cluster)voor meer informatie.

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Mijn Pod is vastgelopen in de CrashLoopBackOff-modus. Wat zal ik doen?

Er kunnen verschillende redenen zijn waarom de pod in die modus vastloopt. U kunt het volgende bekijken:

* De pod zelf, met behulp van `kubectl describe pod <pod-name>`.
* De logboeken, met behulp van `kubectl logs <pod-name>`.

Zie [fouten opsporen in toepassingen](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods)voor meer informatie over het oplossen van problemen met Pod.

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Ik probeer RBAC in te scha kelen op een bestaand cluster. Hoe kan ik dat doen?

Helaas wordt het inschakelen van op rollen gebaseerd toegangs beheer (RBAC) op bestaande clusters op dit moment niet ondersteund. U moet expliciet nieuwe clusters maken. Als u de CLI gebruikt, is RBAC standaard ingeschakeld. Als u de AKS-Portal gebruikt, is een wissel knop voor het inschakelen van RBAC beschikbaar in de werk stroom maken.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Ik heb een cluster gemaakt waarop RBAC is ingeschakeld met behulp van de Azure CLI met de standaard instellingen of de Azure Portal, en nu worden er veel waarschuwingen weer gegeven op het Kubernetes-dash board. Het dash board dat wordt gebruikt om zonder waarschuwingen te werken. Wat zal ik doen?

De reden voor de waarschuwingen op het dash board is dat het cluster nu is ingeschakeld met RBAC en dat de toegang tot deze server standaard is uitgeschakeld. Over het algemeen is deze aanpak goed, omdat de standaard belichting van het dash board aan alle gebruikers van het cluster kan leiden tot beveiligings Risico's. Als u het dash board nog steeds wilt inschakelen, volgt u de stappen in [dit blog bericht](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Ik kan geen verbinding maken met het dash board. Wat zal ik doen?

De eenvoudigste manier om toegang te krijgen tot uw service buiten het cluster is om `kubectl proxy`uit te voeren, welke proxy's aanvragen verzonden naar de lokale poort 8001 van de Kubernetes-API-server. Vanaf daar kan de API-server worden geproxyeerd voor uw service: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Als u het Kubernetes-dash board niet ziet, controleert u of de `kube-proxy` pod wordt uitgevoerd in de naam ruimte `kube-system`. Als de status niet wordt uitgevoerd, verwijdert u de Pod en wordt de computer opnieuw opgestart.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Ik kan geen logboeken ophalen met behulp van kubectl-Logboeken of ik kan geen verbinding maken met de API-server. Ik krijg de fout melding van server: fout bij het kiezen van de back-end: Dial TCP.... Wat zal ik doen?

Zorg ervoor dat de standaard netwerk beveiligings groep niet is gewijzigd en dat poort 22 en 9000 zijn geopend voor verbinding met de API-server. Controleer of de `tunnelfront` pod wordt uitgevoerd in de *uitvoeren-systeem* naam ruimte met behulp van de `kubectl get pods --namespace kube-system` opdracht. Als dat niet het geval is, dwingt u het verwijderen van de pod af en wordt de computer opnieuw opgestart.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Ik probeer een upgrade uit te voeren of te schalen en krijg een fout bericht: de eigenschap imageReference wijzigen is niet toegestaan. Dit probleem Hoe kan ik oplossen?

Mogelijk krijgt u deze fout omdat u de tags in de agent knooppunten in het AKS-cluster hebt gewijzigd. Het wijzigen en verwijderen van tags en andere eigenschappen van resources in de MC_ *-resource groep kan leiden tot onverwachte resultaten. Als u de resources onder de groep MC_ * in het AKS-cluster wijzigt, wordt de serviceniveau doelstelling (SLO) verbroken.

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Er worden fouten weer ontvangen dat mijn cluster de status Mislukt heeft en het bijwerken of schalen werkt pas nadat het is opgelost

*Deze probleemoplossings hulp wordt omgeleid vanuit https://aka.ms/aks-cluster-failed*

Deze fout treedt op wanneer clusters een mislukte status om meerdere redenen invoeren. Volg de onderstaande stappen om de status van het cluster fout op te lossen voordat u de eerder mislukte bewerking opnieuw probeert uit te voeren:

1. `upgrade` en `scale` bewerkingen mislukken totdat het cluster niet meer `failed` status heeft. Veelvoorkomende basis problemen en oplossingen zijn:
    * Schalen met **onvoldoende Compute-quota (CRP)** . U kunt dit oplossen door uw cluster eerst terug te schalen naar een stabiele doel status binnen het quotum. Voer vervolgens de volgende [stappen uit om een toename van het reken quotum aan te vragen](../azure-portal/supportability/resource-manager-core-quotas-request.md) voordat u opnieuw opschaalt na de initiële quotum limieten.
    * Het schalen van een cluster met geavanceerde netwerken en **onvoldoende subnet-bronnen (netwerken)** . U kunt dit oplossen door uw cluster eerst terug te schalen naar een stabiele doel status binnen het quotum. Voer vervolgens [de volgende stappen uit om een verhoging van het resource quotum aan te vragen](../azure-resource-manager/templates/error-resource-quota.md#solution) voordat u opnieuw opschaalt na de initiële quotum limieten.
2. Zodra de onderliggende oorzaak van de upgrade fout is opgelost, moet uw cluster de status voltooid hebben. Als de status is gecontroleerd, voert u de oorspronkelijke bewerking opnieuw uit.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Er wordt een fout opgetreden bij het upgraden of schalen van de status die mijn cluster momenteel wordt bijgewerkt of waarvoor een upgrade is mislukt

*Deze probleemoplossings hulp wordt omgeleid vanuit https://aka.ms/aks-pending-upgrade*

Het bijwerken en schalen van bewerkingen op een cluster met een groep met één knoop punt of een cluster met [meerdere knooppunt Pools](use-multiple-node-pools.md) sluiten elkaar wederzijds uit. U kunt geen cluster-of knooppunt groep tegelijkertijd bijwerken en schalen. In plaats daarvan moet elk bewerkings type worden voltooid voor de doel resource vóór de volgende aanvraag op dezelfde resource. Als gevolg hiervan zijn bewerkingen beperkt wanneer actieve upgrade of schaal bewerkingen worden uitgevoerd of geprobeerd en vervolgens worden mislukt. 

Om het probleem op te lossen `az aks show -g myResourceGroup -n myAKSCluster -o table` uit te voeren om de gedetailleerde status van het cluster op te halen. Op basis van het resultaat:

* Als het cluster actief wordt bijgewerkt, wacht u totdat de bewerking wordt beëindigd. Als deze is geslaagd, voert u de bewerking opnieuw uit.
* Als het upgraden van het cluster is mislukt, volgt u de stappen in de vorige sectie.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Kan ik mijn cluster verplaatsen naar een ander abonnement of mijn abonnement met mijn cluster naar een nieuwe Tenant?

Als u uw AKS-cluster hebt verplaatst naar een ander abonnement of het cluster dat eigenaar is van een abonnement op een nieuwe Tenant, verliest het cluster de functionaliteit als gevolg van het verlies van roltoewijzingen en machtigingen voor service-principals. **AKS biedt geen ondersteuning voor het verplaatsen van clusters in abonnementen of tenants** als gevolg van deze beperking.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Ik ontvang fouten bij het gebruiken van functies waarvoor schaal sets voor virtuele machines zijn vereist

*Deze hulp bij het oplossen van problemen is gericht op aka.ms/aks-vmss-enablement*

Er kunnen fouten optreden die aangeven dat uw AKS-cluster zich niet op een schaalset voor virtuele machines bevindt, zoals in het volgende voor beeld:

**Agent pool ' agent pool ' heeft automatische schaling ingesteld op ingeschakeld, maar niet op Virtual Machine Scale Sets**

Als u functies zoals de cluster-automatische schaal of meerdere knooppunt groepen wilt gebruiken, moeten er AKS-clusters worden gemaakt die gebruikmaken van virtuele-machine schaal sets. Er worden fouten geretourneerd als u probeert functies te gebruiken die afhankelijk zijn van virtuele-machine schaal sets en u een normaal, niet-virtuele machine Scale set AKS-cluster hebt gericht.

Volg de *voordat u begint* met de stappen in het juiste document om een AKS-cluster op de juiste manier te maken:

* [De automatische schaal functie van het cluster gebruiken](cluster-autoscaler.md)
* [Meerdere knooppunt groepen maken en gebruiken](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Welke naam beperkingen worden afgedwongen voor AKS-resources en-para meters?

*Deze hulp bij het oplossen van problemen is gericht op aka.ms/aks-naming-rules*

Naam beperkingen worden geïmplementeerd door zowel het Azure-platform als de AKS. Als een resource naam of-para meter een van deze beperkingen uitbreekt, wordt er een fout bericht weer gegeven waarin u wordt gevraagd een andere invoer op te geven. De volgende algemene richt lijnen zijn van toepassing:

* Cluster namen moeten 1-63 tekens lang zijn. De enige toegestane tekens zijn letters, cijfers, streepjes en onderstreping. Het eerste en laatste teken moeten een letter of cijfer zijn.
* De naam van de resource groep AKS *MC_* een combi natie van de naam van de resource groep en de resource naam. De automatisch gegenereerde syntaxis van `MC_resourceGroupName_resourceName_AzureRegion` mag niet groter zijn dan 80 tekens. Als dat nodig is, vermindert u de lengte van de naam van de resource groep of de AKS-cluster naam.
* De *dnsPrefix* moet beginnen en eindigen met alfanumerieke waarden. Geldige tekens zijn alfanumerieke waarden en afbreek streepjes (-). De *dnsPrefix* mag geen speciale tekens bevatten, zoals een punt (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Ik ontvang fouten bij het maken, bijwerken, schalen, verwijderen of upgraden van een cluster. deze bewerking is niet toegestaan omdat er een andere bewerking wordt uitgevoerd.

*Deze hulp bij het oplossen van problemen is gericht op aka.ms/aks-pending-operation*

Cluster bewerkingen zijn beperkt wanneer er nog een eerdere bewerking wordt uitgevoerd. Gebruik de opdracht `az aks show -g myResourceGroup -n myAKSCluster -o table` om een gedetailleerde status van uw cluster op te halen. Gebruik uw eigen resource groep en AKS cluster naam waar nodig.

Op basis van de uitvoer van de cluster status:

* Als het cluster zich in een andere inrichtings status bevindt dan *geslaagd* of *mislukt*, wacht u totdat de bewerking (*bijwerken/bijwerken/maken/schalen/verwijderen/migreren*) is beëindigd. Wanneer de vorige bewerking is voltooid, moet u de meest recente cluster bewerking opnieuw proberen.

* Als de upgrade van het cluster is mislukt, volgt u de stappen [die worden beschreven waarbij ik fouten ontvang dat mijn cluster de status Mislukt heeft en dat de upgrade of schaal aanpassing pas werkt als het probleem is opgelost](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>Er wordt een fout melding ontvangen dat mijn Service-Principal niet is gevonden bij het maken van een nieuw cluster zonder dat dit wordt door gegeven.

Bij het maken van een AKS-cluster is een Service-Principal vereist om namens u resources te maken. AKS biedt de mogelijkheid om een nieuwe te maken op het moment dat het cluster wordt gemaakt, maar dit vereist dat Azure Active Directory de nieuwe Service-Principal in een redelijke tijd volledig moet door geven om het cluster te kunnen maken. Wanneer deze doorgifte te lang duurt, mislukt de validatie van het cluster om te worden gemaakt omdat er hiervoor geen beschik bare service-principal kan worden gevonden. 

Gebruik de volgende tijdelijke oplossingen:
1. Gebruik een bestaande service-principal die al is door gegeven in meerdere regio's en die bestaat voor AKS tijdens het maken van een cluster.
2. Als u automatiserings scripts gebruikt, voegt u vertragingen toe tussen het maken van service-principals en het maken van AKS-clusters.
3. Als u Azure Portal gebruikt, keert u terug naar de cluster instellingen tijdens het maken en voert u de validatie pagina na een paar minuten opnieuw uit.

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>Ik ontvang fouten na het beperken van mijn uitgaand verkeer

Bij het beperken van uitgaand verkeer van een AKS-cluster zijn er [vereiste en optionele aanbevolen](limit-egress-traffic.md) uitgaande poorten/netwerk regels en FQDN/toepassings regels voor AKS. Als uw instellingen conflicteren met een van deze regels, kunt u bepaalde `kubectl`-opdrachten mogelijk niet uitvoeren. Er worden ook fouten weer geven bij het maken van een AKS-cluster.

Controleer of de instellingen niet conflicteren met een van de vereiste of optionele aanbevolen uitgaande poorten/netwerk regels en FQDN/toepassings regels.

## <a name="azure-storage-and-aks-troubleshooting"></a>Problemen met Azure Storage en AKS oplossen

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Wat zijn de aanbevolen stabiele versies van Kubernetes voor Azure Disk? 

| Kubernetes-versie | Aanbevolen versie |
| -- | :--: |
| 1.12 | 1.12.9 of hoger |
| 1.13 | 1.13.6 of hoger |
| 1,14 | 1.14.2 of hoger |


### <a name="what-versions-of-kubernetes-have-azure-disk-support-on-the-sovereign-cloud"></a>Welke versies van Kubernetes hebben Azure-schijf ondersteuning op de soevereine Cloud?

| Kubernetes-versie | Aanbevolen versie |
| -- | :--: |
| 1.12 | 1.12.0 of hoger |
| 1.13 | 1.13.0 of hoger |
| 1,14 | 1.14.0 of hoger |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>WaitForAttach is mislukt voor Azure-schijf: het parseren van "/dev/disk/Azure/SCSI1/lun1": ongeldige syntaxis

Kubernetes-versie 1,10, MountVolume. WaitForAttach kan mislukken met een Azure-schijf opnieuw koppelen.

Op Linux ziet u mogelijk een onjuiste fout in de DevicePath-indeling. Bijvoorbeeld:

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

In Windows ziet u mogelijk een onjuiste fout code voor DevicePath (LUN). Bijvoorbeeld:

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

Dit probleem is opgelost in de volgende versies van Kubernetes:

| Kubernetes-versie | Vaste versie |
| -- | :--: |
| 1.10 | 1.10.2 of hoger |
| 1,11 | 1.11.0 of hoger |
| 1,12 en hoger | N/A |

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Fout bij het instellen van UID en GID in mountOptions voor Azure-schijf

Azure Disk maakt gebruik van het ext4-, xfs-bestands systeem standaard en mountOptions, zoals UID = x, GID = x kan niet worden ingesteld op koppel tijd. Als u bijvoorbeeld probeert mountOptions UID = 999, GID = 999, wordt een fout weer geven als:

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

U kunt het probleem oplossen door een van de volgende handelingen uit te voeren:

* [Configureer de beveiligings context voor een pod door de](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) UID in runAsUser en GID in fsGroup in te stellen. Met de volgende instelling wordt bijvoorbeeld pod uitvoeren als root ingesteld, zodat deze toegankelijk is voor elk bestand:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

  >[!NOTE]
  > Aangezien GID en UID standaard als root of 0 zijn gekoppeld. Als GID of UID als niet-basis is ingesteld, bijvoorbeeld 1000, gebruikt Kubernetes de `chown` om alle mappen en bestanden op die schijf te wijzigen. Deze bewerking kan veel tijd in beslag nemen en kan het koppelen van de schijf erg traag maken.

* Gebruik `chown` in initContainers om GID en UID in te stellen. Bijvoorbeeld:

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="error-when-deleting-azure-disk-persistentvolumeclaim-in-use-by-a-pod"></a>Fout bij het verwijderen van de Azure-schijf PersistentVolumeClaim die wordt gebruikt door een pod

Als u probeert een Azure-schijf PersistentVolumeClaim te verwijderen die wordt gebruikt door een Pod, ziet u mogelijk een fout melding. Bijvoorbeeld:

```console
$ kubectl describe pv pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06
...
Message:         disk.DisksClient#Delete: Failure responding to request: StatusCode=409 -- Original Error: autorest/azure: Service returned an error. Status=409 Code="OperationNotAllowed" Message="Disk kubernetes-dynamic-pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06 is attached to VM /subscriptions/{subs-id}/resourceGroups/MC_markito-aks-pvc_markito-aks-pvc_westus/providers/Microsoft.Compute/virtualMachines/aks-agentpool-25259074-0."
```

In Kubernetes versie 1,10 en hoger is de functie PersistentVolumeClaim Protection standaard ingeschakeld om deze fout te voor komen. Als u een versie van Kubernetes gebruikt die niet de oplossing voor dit probleem heeft, kunt u dit probleem oplossen door de Pod te verwijderen met behulp van de PersistentVolumeClaim voordat u de PersistentVolumeClaim verwijdert.


### <a name="error-cannot-find-lun-for-disk-when-attaching-a-disk-to-a-node"></a>Fout ' kan LUN niet vinden voor schijf ' bij het koppelen van een schijf aan een knoop punt

Wanneer u een schijf aan een knoop punt koppelt, ziet u mogelijk de volgende fout:

```console
MountVolume.WaitForAttach failed for volume "pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6" : Cannot find Lun for disk kubernetes-dynamic-pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6
```

Dit probleem is opgelost in de volgende versies van Kubernetes:

| Kubernetes-versie | Vaste versie |
| -- | :--: |
| 1.10 | 1.10.10 of hoger |
| 1,11 | 1.11.5 of hoger |
| 1.12 | 1.12.3 of hoger |
| 1.13 | 1.13.0 of hoger |
| 1,14 en hoger | N/A |

Als u een versie van Kubernetes gebruikt die niet de oplossing voor dit probleem heeft, kunt u het probleem verminderen door enkele minuten te wachten en het opnieuw te proberen.

### <a name="azure-disk-attachdetach-failure-mount-issues-or-io-errors-during-multiple-attachdetach-operations"></a>Fout bij koppelen/loskoppelen van Azure-schijf, koppel problemen of I/O-fouten tijdens meerdere toevoeg-en ontkoppelings bewerkingen

Bij het uitvoeren van meerdere koppelen/loskoppelen van Kubernetes-versie 1.9.2 worden mogelijk de volgende schijf problemen weer geven vanwege een vuile VM-cache:

* Fouten bij koppelen/loskoppelen van schijven
* Schijf-I/O-fouten
* Onverwachte schijf loskoppeling van virtuele machine
* De VM wordt niet in de status mislukt als gevolg van het koppelen van de niet-bestaande schijf

Dit probleem is opgelost in de volgende versies van Kubernetes:

| Kubernetes-versie | Vaste versie |
| -- | :--: |
| 1.10 | 1.10.12 of hoger |
| 1,11 | 1.11.6 of hoger |
| 1.12 | 1.12.4 of hoger |
| 1.13 | 1.13.0 of hoger |
| 1,14 en hoger | N/A |

Als u een versie van Kubernetes gebruikt die niet de oplossing voor dit probleem heeft, kunt u het probleem verhelpen door het onderstaande te proberen:

* Als er gedurende een lange periode een schijf ontstaat om deze te ontkoppelen, probeert u de schijf hand matig te ontkoppelen

### <a name="azure-disk-waiting-to-detach-indefinitely"></a>Azure-schijf wacht op het verbreken van onbepaalde tijd

Als tijdens de eerste poging een bewerking voor het loskoppelen van een Azure-schijf mislukt, wordt de koppelings bewerking niet opnieuw geprobeerd en blijft deze gekoppeld aan de oorspronkelijke VM van het knoop punt. Deze fout kan optreden bij het verplaatsen van een schijf van het ene knoop punt naar het andere. Bijvoorbeeld:

```console
[Warning] AttachVolume.Attach failed for volume “pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9” : Attach volume “kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" to instance “/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-0” failed with compute.VirtualMachinesClient#CreateOrUpdate: Failure sending request: StatusCode=0 -- Original Error: autorest/azure: Service returned an error. Status= Code=“ConflictingUserInput” Message=“Disk ‘/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9’ cannot be attached as the disk is already owned by VM ‘/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-1’.”
```

Dit probleem is opgelost in de volgende versies van Kubernetes:

| Kubernetes-versie | Vaste versie |
| -- | :--: |
| 1,11 | 1.11.9 of hoger |
| 1.12 | 1.12.7 of hoger |
| 1.13 | 1.13.4 of hoger |
| 1,14 en hoger | N/A |

Als u een versie van Kubernetes gebruikt die niet de oplossing voor dit probleem heeft, kunt u het probleem verminderen door de schijf hand matig te ontkoppelen.

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Fout bij het loskoppelen van de Azure-schijf vanwege een probleem met de potentiële race voorwaarde en de lijst met ongeldige gegevens schijven

Wanneer een Azure-schijf niet kan worden ontkoppeld, wordt er Maxi maal zes keer opnieuw geprobeerd om de schijf los te koppelen met behulp van exponentiële back-ups. Het bevat ook een vergren deling op knooppunt niveau voor de gegevens schijf lijst van ongeveer drie minuten. Als de lijst met schijven hand matig wordt bijgewerkt tijdens deze periode, zoals een hand matige koppelings-of ontkoppel bewerking, wordt de schijf lijst die wordt bewaard door de vergren deling op knooppunt niveau, verouderd en leidt dit tot instabiliteit op de VM van het knoop punt.

Dit probleem is opgelost in de volgende versies van Kubernetes:

| Kubernetes-versie | Vaste versie |
| -- | :--: |
| 1.12 | 1.12.9 of hoger |
| 1.13 | 1.13.6 of hoger |
| 1,14 | 1.14.2 of hoger |
| 1,15 en hoger | N/A |

Als u een versie van Kubernetes gebruikt die niet de oplossing voor dit probleem heeft en uw VM van het knoop punt heeft een verouderde lijst met schijven, kunt u het probleem verminderen door alle niet-bestaande schijven te ontkoppelen van de virtuele machine als een enkele, bulk bewerking. **Het afzonderlijk ontkoppelen van niet-bestaande schijven kan mislukken.**


### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Een groot aantal Azure-schijven veroorzaakt langzame koppeling/ontkoppeling

Wanneer het aantal Azure-schijven dat is gekoppeld aan een VM van een knoop punt groter is dan 10, worden de bewerkingen voor koppelen en loskoppelen mogelijk traag. Dit probleem is een bekend probleem en er zijn op dit moment geen tijdelijke oplossingen.

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Fout bij het losmaken van Azure-schijf vanwege een mogelijke VM-virtuele machine met de status mislukt

In sommige gevallen kan het loskoppelen van een Azure-schijf gedeeltelijk mislukken en de VM van het knoop punt in een mislukte status laten staan.

Dit probleem is opgelost in de volgende versies van Kubernetes:

| Kubernetes-versie | Vaste versie |
| -- | :--: |
| 1.12 | 1.12.10 of hoger |
| 1.13 | 1.13.8 of hoger |
| 1,14 | 1.14.4 of hoger |
| 1,15 en hoger | N/A |

Als u een versie van Kubernetes gebruikt die niet de oplossing voor dit probleem heeft en de VM van het knoop punt de status Mislukt heeft, kunt u het probleem verminderen door de VM-status hand matig bij te werken met een van de onderstaande opties:

* Voor een cluster op basis van beschikbaarheids sets:
    ```console
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* Voor een cluster op basis van VMSS:
    ```console
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Problemen met Azure Files en AKS oplossen

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Wat zijn de aanbevolen stabiele versies van Kubernetes voor Azure files?
 
| Kubernetes-versie | Aanbevolen versie |
| -- | :--: |
| 1.12 | 1.12.6 of hoger |
| 1.13 | 1.13.4 of hoger |
| 1,14 | 1.14.0 of hoger |

### <a name="what-versions-of-kubernetes-have-azure-files-support-on-the-sovereign-cloud"></a>Welke versies van Kubernetes hebben Azure Files ondersteuning voor de soevereine Cloud?

| Kubernetes-versie | Aanbevolen versie |
| -- | :--: |
| 1.12 | 1.12.0 of hoger |
| 1.13 | 1.13.0 of hoger |
| 1,14 | 1.14.0 of hoger |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Wat zijn de standaard mountOptions bij het gebruik van Azure Files?

Aanbevolen instellingen:

| Kubernetes-versie | File mode-en dirMode-waarde|
| -- | :--: |
| 1.12.0 - 1.12.1 | 0755 |
| 1.12.2 en hoger | 0777 |

Als u een cluster met Kuberetes-versie 1.8.5 of hoger gebruikt en het permanente volume dynamisch maakt met een opslag klasse, kunnen er koppelings opties worden opgegeven voor het opslag klassen object. In het volgende voor beeld wordt *0777*ingesteld:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

Enkele aanvullende nuttige *mountOptions* -instellingen:

* *mfsymlinks* zorgt ervoor dat Azure files mount (CIFS) symbolische koppelingen ondersteunt
* met *nobrl* wordt voor komen dat aanvragen voor het vergren delen van bytes naar de server worden verzonden. Deze instelling is nood zakelijk voor bepaalde toepassingen die worden onderbroken met de CIFS-stijl vergren delingen van een verplicht byte bereik. De meeste CIFS-servers bieden nog geen ondersteuning voor het aanvragen van advies-byte bereik vergrendelingen. Als u *nobrl*niet gebruikt, kunnen toepassingen die worden onderbroken met de CIFS-stijl die verplicht byte bereik zijn, fout berichten veroorzaken die vergelijkbaar zijn met:
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Fout: kan de machtigingen niet wijzigen bij gebruik van Azure Files

Wanneer u PostgreSQL uitvoert op de Azure Files-invoeg toepassing, wordt er mogelijk een fout bericht weer gegeven die vergelijkbaar is met het volgende:

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

Deze fout wordt veroorzaakt door de Azure Files-invoeg toepassing met behulp van het CIFS/SMB-protocol. Wanneer u het CIFS/SMB-protocol gebruikt, kunnen de machtigingen voor bestanden en mappen niet worden gewijzigd na het koppelen.

U kunt dit probleem oplossen door *subpad* samen met de Azure Disk-invoeg toepassing te gebruiken. 

> [!NOTE] 
> Voor ext3/4-schijf type is er een map gevonden die niet meer bestaat dan waar de schijf is geformatteerd.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>Azure Files heeft een hoge latentie vergeleken met de Azure-schijf bij het verwerken van veel kleine bestanden

In sommige gevallen, zoals bij het verwerken van veel kleine bestanden, kan er een hoge latentie optreden bij het gebruik van Azure Files in vergelijking met de Azure-schijf.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>Fout bij het inschakelen van de instelling toegang vanaf geselecteerd netwerk toestaan voor het opslag account

Als u *toegang toestaan vanuit geselecteerd netwerk* op een opslag account dat wordt gebruikt voor dynamische inrichting in AKS inschakelt, krijgt u een fout melding wanneer aks een bestands share maakt:

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Deze fout wordt veroorzaakt door het feit dat de Kubernetes *persistentvolume-controller* niet op het netwerk is gekozen wanneer de instelling *toegang vanaf het geselecteerde netwerk toestaan*is ingesteld.

U kunt het probleem oplossen met behulp van [statische inrichting met Azure files](azure-files-volume.md).

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Azure Files kan niet opnieuw worden gekoppeld in Windows pod

Als een Windows-pod met een Azure Files-koppeling wordt verwijderd en vervolgens opnieuw wordt gemaakt op hetzelfde knoop punt, mislukt de koppeling. Deze fout wordt veroorzaakt door het mislukken van de `New-SmbGlobalMapping`-opdracht omdat de Azure Files-koppeling al is gekoppeld aan het knoop punt.

Het is bijvoorbeeld mogelijk dat er een fout wordt weer gegeven die vergelijkbaar is met:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Dit probleem is opgelost in de volgende versies van Kubernetes:

| Kubernetes-versie | Vaste versie |
| -- | :--: |
| 1.12 | 1.12.6 of hoger |
| 1.13 | 1.13.4 of hoger |
| 1,14 en hoger | N/A |

### <a name="azure-files-mount-fails-due-to-storage-account-key-changed"></a>Azure Files koppelen mislukt omdat de sleutel van het opslag account is gewijzigd

Als de sleutel van uw opslag account is gewijzigd, ziet u mogelijk Azure Files koppel fouten.

U kunt het probleem oplossen door het veld *azurestorageaccountkey* hand matig bij te werken in azure file Secret met uw sleutel voor het opslag account met base64-code ring.

Als u de sleutel van uw opslag account wilt coderen in base64, kunt u `base64`gebruiken. Bijvoorbeeld:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Gebruik `kubectl edit secret`om uw Azure-geheim bestand bij te werken. Bijvoorbeeld:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Na een paar minuten probeert het agent knooppunt de Azure-bestands koppeling opnieuw uit te voeren met de bijgewerkte opslag sleutel.

### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>Het schalen van het cluster kan niet worden geschaald met de fout bij het herstellen van de grootte van de knooppunt groep

Als uw cluster automatisch schalen niet omhoog/omlaag wordt geschaald, ziet u een fout zoals hieronder in de [Logboeken van de cluster automatisch schalen][view-master-logs].

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

Deze fout wordt veroorzaakt door een upstream-voor waarde voor het automatisch schalen van clusters waarbij de cluster-automatische schaal functie eindigt op een andere waarde dan die werkelijk in het cluster. Als u deze status wilt weer geven, schakelt u de [automatisch schalen][cluster-autoscaler]van het cluster uit en weer in.

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md