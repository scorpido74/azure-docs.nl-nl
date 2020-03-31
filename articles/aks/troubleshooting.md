---
title: Veelvoorkomende Azure Kubernetes-serviceproblemen oplossen
description: Meer informatie over het oplossen en oplossen van veelvoorkomende problemen bij het gebruik van Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: saudas
ms.openlocfilehash: 7bdabf2ec109fe96c28185bd1a2a680ce19c2650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368329"
---
# <a name="aks-troubleshooting"></a>AKS-problemen oplossen

Wanneer u AKS-clusters (Azure Kubernetes Service) maakt of beheert, u af en toe problemen ondervinden. In dit artikel worden enkele veelvoorkomende problemen beschreven en stappen voor het oplossen van problemen beschreven.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>In het algemeen, waar vind ik informatie over het debuggen kubernetes problemen?

Probeer de [officiële handleiding voor het oplossen van Kubernetes-clusters.](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/)
Er is ook een [handleiding voor probleemoplossing,](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)gepubliceerd door een Microsoft-technicus voor het oplossen van problemen met pods, knooppunten, clusters en andere functies.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Ik krijg een "quotum overschreden" fout tijdens het maken of upgraden. Wat moet ik doen? 

Je moet [kernen aanvragen.](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Wat is de maximale instelling voor pods per knooppunt voor AKS?

De maximale instelling voor pods per knooppunt is standaard 30 als u een AKS-cluster implementeert in de Azure-portal.
De maximale instelling voor pods per knooppunt is standaard 110 als u een AKS-cluster implementeert in het Azure CLI. (Zorg ervoor dat u de nieuwste versie van de Azure CLI gebruikt). Deze standaardinstelling kan worden `–-max-pods` gewijzigd door `az aks create` de vlag in de opdracht te gebruiken.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Ik krijg een onvoldoendeSubnetSize fout tijdens het implementeren van een AKS-cluster met geavanceerde netwerken. Wat moet ik doen?

Als Azure CNI (geavanceerde netwerken) wordt gebruikt, wijst AKS IP-adressen toe op basis van de geconfigureerde 'max-pods'. Op basis van de geconfigureerde max pods per knooppunt moet de subnetgrootte groter zijn dan het product van het aantal knooppunten en de maximale pod per knooppuntinstelling. In de volgende vergelijking wordt het volgende beschreven:

Subnetgrootte > aantal knooppunten in het cluster (rekening houdend met de toekomstige schalingsvereisten) * max pods per knooppuntset.

Zie [Plan IP-adressering voor uw cluster voor](configure-azure-cni.md#plan-ip-addressing-for-your-cluster)meer informatie.

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Mijn pod zit vast in de CrashLoopBackOff-modus. Wat moet ik doen?

Er kunnen verschillende redenen zijn waarom de pod vast zit in die modus. Je zou kunnen kijken naar:

* De pod zelf, `kubectl describe pod <pod-name>`met behulp van .
* De logs, `kubectl logs <pod-name>`met behulp van .

Zie [Foutopsporingstoepassingen voor](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods)meer informatie over het oplossen van podproblemen.

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Ik probeer RBAC in te schakelen op een bestaand cluster. Hoe kan ik dat doen?

Helaas wordt het inschakelen van op rollen gebaseerde toegangscontrole (RBAC) op bestaande clusters op dit moment niet ondersteund. U moet expliciet nieuwe clusters maken. Als u de CLI gebruikt, is RBAC standaard ingeschakeld. Als u de AKS-portal gebruikt, is er een schakelknop beschikbaar om RBAC in te schakelen in de werkstroom voor maken.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Ik heb een cluster gemaakt met RBAC ingeschakeld met behulp van de Azure CLI met standaardinstellingen of de Azure-portal, en nu zie ik veel waarschuwingen op het Kubernetes-dashboard. Het dashboard werkte zonder waarschuwingen. Wat moet ik doen?

De reden voor de waarschuwingen op het dashboard is dat het cluster nu is ingeschakeld met RBAC en de toegang tot het is standaard uitgeschakeld. In het algemeen is deze aanpak een goede praktijk, omdat de standaardbelichting van het dashboard voor alle gebruikers van het cluster kan leiden tot beveiligingsbedreigingen. Als u het dashboard nog steeds wilt inschakelen, volgt u de stappen in [dit blogbericht](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Ik kan geen verbinding maken met het dashboard. Wat moet ik doen?

De eenvoudigste manier om toegang te krijgen `kubectl proxy`tot uw service buiten het cluster is door uit te voeren , welke proxy's aanvragen naar uw localhost-poort 8001 naar de Kubernetes API-server worden verzonden. Van daaruit kan de API-server `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`proxy naar uw service: .

Als u het Kubernetes-dashboard niet ziet, controleert u of de `kube-proxy` pod in de `kube-system` naamruimte wordt uitgevoerd. Als deze niet in een lopende toestand is, verwijdert u de pod en wordt deze opnieuw opgestart.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Ik kan geen logboeken krijgen met behulp van kubectl-logboeken of ik kan geen verbinding maken met de API-server. Ik krijg "Fout van server: fout draaien backend: dial tcp...". Wat moet ik doen?

Zorg ervoor dat de standaardnetwerkbeveiligingsgroep niet wordt gewijzigd en dat zowel poort 22 als 9000 open staan voor verbinding met de API-server. Controleer met `tunnelfront` de `kubectl get pods --namespace kube-system` opdracht of de pod wordt uitgevoerd in de naamruimte van het *kube-systeem.* Als dit niet het is, forceert u de pod en wordt deze opnieuw opgestart.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Ik probeer te upgraden of schalen en krijg een "bericht: Het wijzigen van eigenschap 'imageReference' is niet toegestaan" fout. Hoe los ik dit probleem op?

Mogelijk krijgt u deze fout omdat u de tags in de agentknooppunten in het AKS-cluster hebt gewijzigd. Het wijzigen en verwijderen van tags en andere eigenschappen van resources in de resourcegroep MC_* kan tot onverwachte resultaten leiden. Als u de resources onder de groep MC_* in het AKS-cluster wijzigt, wordt de doelstelling serviceniveau (SLO) gebroken.

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Ik ontvang fouten dat mijn cluster in mislukte status is en upgraden of schalen werkt pas als het is opgelost

*Deze hulp bij het oplossen van problemen wordthttps://aka.ms/aks-cluster-failed*

Deze fout treedt op wanneer clusters om meerdere redenen een mislukte status invoeren. Volg de onderstaande stappen om de status van het mislukte cluster op te lossen voordat u de eerder mislukte bewerking opnieuw probeert:

1. Totdat het cluster `failed` niet `upgrade` meer `scale` bestaat en bewerkingen niet worden uitgevoerd. Veelvoorkomende hoofdproblemen en oplossingen zijn:
    * Schalen met **onvoldoende CRP-quotum (Compute).** Schaal het cluster eerst terug naar een stabiele doelstatus binnen het quotum om het op te lossen. Volg vervolgens deze [stappen om een wijziging van het rekenquotum aan te vragen](../azure-portal/supportability/resource-manager-core-quotas-request.md) voordat u probeert opnieuw op te schalen buiten de oorspronkelijke quotumlimieten.
    * Een cluster schalen met geavanceerde netwerken en **onvoldoende subnetbronnen (netwerkbronnen).** Schaal het cluster eerst terug naar een stabiele doelstatus binnen het quotum om het op te lossen. Volg vervolgens [deze stappen om een verhoging van het resourcequotum aan te vragen](../azure-resource-manager/templates/error-resource-quota.md#solution) voordat u probeert opnieuw op te schalen buiten de oorspronkelijke quotumlimieten.
2. Zodra de onderliggende oorzaak van upgradefouten is opgelost, moet uw cluster in een geslaagde status zijn. Zodra een opgevolgd status is geverifieerd, probeert u de oorspronkelijke bewerking opnieuw.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Ik ontvang fouten wanneer ik probeer te upgraden of te schalen waarin staat dat mijn cluster momenteel wordt bijgewerkt of is mislukt

*Deze hulp bij het oplossen van problemen wordthttps://aka.ms/aks-pending-upgrade*

Upgrade- en schaalbewerkingen op een cluster met één knooppuntgroep of een cluster met [meerdere knooppuntgroepen](use-multiple-node-pools.md) sluiten elkaar uit. U geen cluster- of knooppuntgroep tegelijk upgraden en schalen. In plaats daarvan moet elk bewerkingstype worden voltooid op de doelbron voordat de volgende aanvraag op dezelfde resource wordt ingediend. Als gevolg hiervan zijn bewerkingen beperkt wanneer actieve upgrade- of schaalbewerkingen plaatsvinden of worden geprobeerd en vervolgens mislukt. 

U de `az aks show -g myResourceGroup -n myAKSCluster -o table` diagnose van het probleem uitvoeren om de gedetailleerde status op uw cluster op te halen. Op basis van het resultaat:

* Als het cluster actief wordt bijgewerkt, wacht u tot de bewerking wordt beëindigd. Als dit is gelukt, probeert u de eerder mislukte bewerking opnieuw.
* Als de upgrade van het cluster is mislukt, volgt u de stappen die in de vorige sectie zijn beschreven.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Kan ik mijn cluster verplaatsen naar een ander abonnement of mijn abonnement met mijn cluster naar een nieuwe tenant?

Als u uw AKS-cluster hebt verplaatst naar een ander abonnement of het clusterabonnement naar een nieuwe tenant, verliest het cluster functionaliteit als gevolg van het verliezen van roltoewijzingen en serviceprincipalsrechten. **AKS biedt geen ondersteuning voor het verplaatsen van clusters tussen abonnementen of tenants** vanwege deze beperking.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Ik ontvang fouten die functies proberen te gebruiken waarvoor virtuele machineschaalsets nodig zijn

*Deze hulp bij het oplossen van problemen wordt vanuit aka.ms/aks-vmss-enablement*

Mogelijk ontvangt u fouten die aangeven dat uw AKS-cluster zich niet op een virtuele machineschaalset bevindt, zoals het volgende voorbeeld:

**AgentPool 'agentpool' heeft automatisch schalen ingesteld als ingeschakeld, maar is niet op virtuele machineschaalsets**

Als u functies zoals de clusterautoscaler of meerdere knooppuntgroepen wilt gebruiken, moeten AKS-clusters worden gemaakt die gebruikmaken van virtuele machineschaalsets. Fouten worden geretourneerd als u functies probeert te gebruiken die afhankelijk zijn van virtuele machineschaalsets en u een regelmatig, niet-virtueel AKS-cluster voor machines wilt targeten.

Volg de *stappen Voordat u begint met* stappen in het juiste document om een AKS-cluster correct te maken:

* [De clusterautoscaler gebruiken](cluster-autoscaler.md)
* [Meerdere knooppuntgroepen maken en gebruiken](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Welke naamgevingsbeperkingen worden afgedwongen voor AKS-resources en -parameters?

*Deze hulp bij het oplossen van problemen wordt vanuit aka.ms/aks-naming-rules*

Naamgevingsbeperkingen worden geïmplementeerd door zowel het Azure-platform als aks. Als een resourcenaam of parameter een van deze beperkingen verbreekt, wordt een fout geretourneerd waarin u wordt gevraagd een andere invoer te geven. De volgende algemene naamgevingsrichtlijnen zijn van toepassing:

* Clusternamen moeten 1-63 tekens zijn. De enige toegestane tekens zijn letters, cijfers, streepjes en underscores. Het eerste en laatste teken moet een letter of een getal zijn.
* De naam van *de AKS-MC_* resourcegroep combineert de naam van de resourcegroep en de naam van de resourcebron. De automatisch gegenereerde `MC_resourceGroupName_resourceName_AzureRegion` syntaxis van mag niet groter zijn dan 80 chars. Verlaag indien nodig de lengte van de naam van uw resourcegroep of AKS-clusternaam.
* Het *dns-voorvoegsel* moet beginnen en eindigen met alfanumerieke waarden en moet tussen 1-54 tekens liggen. Geldige tekens bevatten alfanumerieke waarden en koppeltekens (-). Het *dnsPrefix* kan geen speciale tekens bevatten, zoals een periode (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Ik ontvang fouten wanneer ik het cluster probeer te maken, bijwerken, schalen, verwijderen of upgraden, die bewerking is niet toegestaan omdat een andere bewerking wordt uitgevoerd.

*Deze hulp bij het oplossen van problemen wordt vanuit aka.ms/aks-pending-operation*

Clusterbewerkingen zijn beperkt wanneer een eerdere bewerking nog in uitvoering is. Als u een gedetailleerde status van `az aks show -g myResourceGroup -n myAKSCluster -o table` uw cluster wilt ophalen, gebruikt u de opdracht. Gebruik indien nodig uw eigen resourcegroep en AKS-clusternaam.

Op basis van de uitvoer van de clusterstatus:

* Als het cluster zich in een andere inrichtingstoestand *bevindt* dan geslaagd of *mislukt,* wacht dan tot de bewerking (*Upgraden / bijwerken / Maken / Schalen / Verwijderen / migreren*) eindigt. Wanneer de vorige bewerking is voltooid, probeert u opnieuw de nieuwste clusterbewerking.

* Als het cluster een mislukte upgrade heeft, volgt u de beschreven stappen [waarin ik fouten ontvang waarin mijn cluster is mislukt en het upgraden of schalen werkt pas nadat het is opgelost.](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed)

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>Ik ontvang fouten die mijn serviceprincipal niet is gevonden toen ik een nieuw cluster probeer te maken zonder een bestaand cluster door te geven.

Bij het maken van een AKS-cluster is een serviceprincipal nodig om namens u resources te maken. AKS biedt de mogelijkheid om een nieuwe te laten maken tijdens het maken van een cluster, maar dit vereist azure Active Directory om de nieuwe serviceprincipal binnen een redelijke tijd volledig te propageren om het cluster te laten slagen in het maken. Wanneer deze propagatie te lang duurt, wordt de validatie niet gemaakt omdat het geen beschikbare serviceprincipal kan vinden om dit te doen. 

Gebruik hiervoor de volgende oplossingen:
1. Gebruik een bestaande serviceprincipal die al is gepropageerd tussen regio's en bestaat om in AKS te worden ingevoerd tijdens het maken van een cluster.
2. Als u automatiseringsscripts gebruikt, voegt u tijdvertragingen toe tussen het maken van serviceprincipals en het maken van AKS-clusteren.
3. Als u Azure-portal gebruikt, keert u na enkele minuten terug naar de clusterinstellingen en probeert u de validatiepagina opnieuw.

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>Ik ontvang fouten na het beperken van mijn uitgaande verkeer

Bij het beperken van het uitgangsverkeer van een AKS-cluster, zijn er [vereiste en optionele aanbevolen](limit-egress-traffic.md) uitgaande poorten / netwerkregels en FQDN / toepassingsregels voor AKS. Als uw instellingen in strijd zijn met een van deze `kubectl` regels, u bepaalde opdrachten mogelijk niet uitvoeren. U ook fouten zien bij het maken van een AKS-cluster.

Controleer of uw instellingen niet in strijd zijn met een van de vereiste of optionele aanbevolen uitgaande poorten / netwerkregels en FQDN / toepassingsregels.

## <a name="azure-storage-and-aks-troubleshooting"></a>Azure Storage en AKS Probleemoplossing

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Wat zijn de aanbevolen stabiele versies van Kubernetes voor Azure disk? 

| Kubernetes-versie | Aanbevolen versie |
| -- | :--: |
| 1.12 | 1.12.9 of hoger |
| 1.13 | 1.13.6 of hoger |
| 1.14 | 1.14.2 of hoger |


### <a name="what-versions-of-kubernetes-have-azure-disk-support-on-the-sovereign-cloud"></a>Welke versies van Kubernetes hebben Azure Disk-ondersteuning op de Sovereign Cloud?

| Kubernetes-versie | Aanbevolen versie |
| -- | :--: |
| 1.12 | 1.12.0 of hoger |
| 1.13 | 1.13.0 of hoger |
| 1.14 | 1.14.0 of hoger |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>WaitForAttach is mislukt voor Azure Disk: ontwering "/dev/disk/azure/scsi1/lun1": ongeldige syntaxis

In Kubernetes-versie 1.10 kan MountVolume.WaitForAttach mislukken met een azure disk opnieuw te monteren.

Op Linux ziet u mogelijk een fout in de DevicePath-indeling. Bijvoorbeeld:

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

In Windows ziet u mogelijk een fout van het foutiaatnummer (DevicePath). Bijvoorbeeld:

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

Dit probleem is opgelost in de volgende versies van Kubernetes:

| Kubernetes-versie | Vaste versie |
| -- | :--: |
| 1,10 | 1.10.2 of hoger |
| 1.11 | 1.11.0 of hoger |
| 1.12 en hoger | N.v.t. |

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Fout bij het instellen van uid en gid in mountOptions for Azure Disk

Azure Disk maakt standaard gebruik van het ext4,xfs-bestandssysteem en mountOptions zoals uid=x,gid=x kunnen niet worden ingesteld op het monteren. Als u bijvoorbeeld mountOptions uid=999,gid=999 hebt geprobeerd in te stellen, ziet u een fout als:

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

U het probleem als volgt beperken:

* [Configureer de beveiligingscontext voor een pod](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) door uid in runAsUser en gid in fsGroup in te stellen. De volgende instelling stelt bijvoorbeeld podrun in als hoofd, waardoor deze toegankelijk is voor elk bestand:

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
  > Sinds gid en uid zijn gemonteerd als wortel of 0 standaard. Als gid of uid zijn ingesteld als niet-root, bijvoorbeeld `chown` 1000, zal Kubernetes gebruiken om alle mappen en bestanden onder die schijf te wijzigen. Deze bewerking kan tijdrovend zijn en kan het monteren van de schijf zeer traag maken.

* Gebruik `chown` in initContainers om gid en uid in te stellen. Bijvoorbeeld:

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="error-when-deleting-azure-disk-persistentvolumeclaim-in-use-by-a-pod"></a>Fout bij het verwijderen van Azure Disk PersistentVolumeClaim die wordt gebruikt door een pod

Als u een Azure Disk PersistentVolumeClaim probeert te verwijderen die door een pod wordt gebruikt, ziet u mogelijk een fout. Bijvoorbeeld:

```console
$ kubectl describe pv pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06
...
Message:         disk.DisksClient#Delete: Failure responding to request: StatusCode=409 -- Original Error: autorest/azure: Service returned an error. Status=409 Code="OperationNotAllowed" Message="Disk kubernetes-dynamic-pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06 is attached to VM /subscriptions/{subs-id}/resourceGroups/MC_markito-aks-pvc_markito-aks-pvc_westus/providers/Microsoft.Compute/virtualMachines/aks-agentpool-25259074-0."
```

In Kubernetes-versie 1.10 en hoger is er standaard een persistentvolumeclaim-beveiligingsfunctie ingeschakeld om deze fout te voorkomen. Als u een versie van Kubernetes gebruikt die niet de oplossing voor dit probleem heeft, u dit probleem beperken door de pod te verwijderen met behulp van de PersistentVolumeClaim voordat u de PersistentVolumeClaim verwijdert.


### <a name="error-cannot-find-lun-for-disk-when-attaching-a-disk-to-a-node"></a>Fout "Kan Lun niet vinden voor schijf" bij het koppelen van een schijf aan een knooppunt

Wanneer u een schijf aan een knooppunt koppelt, ziet u mogelijk de volgende fout:

```console
MountVolume.WaitForAttach failed for volume "pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6" : Cannot find Lun for disk kubernetes-dynamic-pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6
```

Dit probleem is opgelost in de volgende versies van Kubernetes:

| Kubernetes-versie | Vaste versie |
| -- | :--: |
| 1,10 | 1.10.10 of hoger |
| 1.11 | 1.11.5 of hoger |
| 1.12 | 1.12.3 of hoger |
| 1.13 | 1.13.0 of hoger |
| 1.14 en later | N.v.t. |

Als u een versie van Kubernetes gebruikt die de oplossing voor dit probleem niet heeft, u het probleem beperken door enkele minuten te wachten en opnieuw te proberen.

### <a name="azure-disk-attachdetach-failure-mount-issues-or-io-errors-during-multiple-attachdetach-operations"></a>Azure Disk attach/detach failure, mount issues, or I/O errors during multiple attach/detach operations Azure Disk attach/detach failure, mount issues, or I/O errors during multiple attach/detach operations Azure Disk attach/detach failure, mount issues, or I/O errors during multiple attach/detach operations Azure Disk

Vanaf Kubernetes versie 1.9.2 ziet u bij het parallel uitvoeren van meerdere aanmaak-/ontkoppelbewerkingen mogelijk de volgende schijfproblemen als gevolg van een vuile VM-cache:

* Schijfstoringen koppelen/loskoppelen
* I/O-fouten in schijf
* Onverwacht schijfloslating van VM
* VM die in mislukte status loopt als gevolg van het koppelen van niet-bestaande schijf

Dit probleem is opgelost in de volgende versies van Kubernetes:

| Kubernetes-versie | Vaste versie |
| -- | :--: |
| 1,10 | 1.10.12 of hoger |
| 1.11 | 1.11.6 of hoger |
| 1.12 | 1.12.4 of hoger |
| 1.13 | 1.13.0 of hoger |
| 1.14 en later | N.v.t. |

Als u een versie van Kubernetes gebruikt die niet de oplossing voor dit probleem heeft, u het probleem beperken door het onderstaande te proberen:

* Als er een schijf wacht om voor een lange periode los te maken, probeer dan de schijf handmatig los te maken

### <a name="azure-disk-waiting-to-detach-indefinitely"></a>Azure Disk wacht om voor onbepaalde tijd los te maken

In sommige gevallen, als een Azure Disk losmaken operatie mislukt op de eerste poging, zal het niet opnieuw proberen de losmaken operatie en blijft gekoppeld aan het oorspronkelijke knooppunt VM. Deze fout kan optreden bij het verplaatsen van een schijf van het ene knooppunt naar het andere. Bijvoorbeeld:

```console
[Warning] AttachVolume.Attach failed for volume "pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" : Attach volume "kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" to instance "/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-0" failed with compute.VirtualMachinesClient#CreateOrUpdate: Failure sending request: StatusCode=0 -- Original Error: autorest/azure: Service returned an error. Status= Code="ConflictingUserInput" Message="Disk '/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9' cannot be attached as the disk is already owned by VM '/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-1'."
```

Dit probleem is opgelost in de volgende versies van Kubernetes:

| Kubernetes-versie | Vaste versie |
| -- | :--: |
| 1.11 | 1.11.9 of hoger |
| 1.12 | 1.12.7 of hoger |
| 1.13 | 1.13.4 of hoger |
| 1.14 en later | N.v.t. |

Als u een versie van Kubernetes gebruikt die niet de oplossing voor dit probleem heeft, u het probleem beperken door de schijf handmatig los te maken.

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Azure Disk detacheren fout leidt tot mogelijke race conditie probleem en ongeldige gegevensschijf lijst

Wanneer een Azure Disk niet loskomt, wordt deze tot zes keer opnieuw geprobeerd om de schijf los te koppelen met exponentiële back-off. Het zal ook een node-level lock op de lijst van gegevensschijven voor ongeveer 3 minuten. Als de schijflijst gedurende die periode handmatig wordt bijgewerkt, zoals een handmatige aan-of ontkoppelbewerking, zorgt dit ervoor dat de schijflijst die wordt vastgehouden door het vergrendelingsscherm op knooppuntniveau verouderd is en instabiliteit veroorzaakt op de knooppuntVM.

Dit probleem is opgelost in de volgende versies van Kubernetes:

| Kubernetes-versie | Vaste versie |
| -- | :--: |
| 1.12 | 1.12.9 of hoger |
| 1.13 | 1.13.6 of hoger |
| 1.14 | 1.14.2 of hoger |
| 1.15 en later | N.v.t. |

Als u een versie van Kubernetes gebruikt die niet de oplossing voor dit probleem heeft en uw knooppunt-VM een verouderde schijflijst heeft, u het probleem beperken door alle niet-bestaande schijven van de VM los te koppelen als één bulkbewerking. **Het individueel loskoppelen van niet-bestaande schijven kan mislukken.**


### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Groot aantal Azure-schijven zorgt voor langzame koppeling/ontkoppeling

Wanneer het aantal Azure Disks dat is gekoppeld aan een node VM groter is dan 10, kunnen de bewerkingen voor koppelen en loskoppelen traag zijn. Dit probleem is een bekend probleem en er zijn op dit moment geen tijdelijke oplossingen.

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Azure Disk-losfout die leidt tot potentiële node-VM in mislukte status

In sommige randgevallen kan een Azure Disk-ontkoppeling gedeeltelijk mislukken en de node-vm in een mislukte status laten.

Dit probleem is opgelost in de volgende versies van Kubernetes:

| Kubernetes-versie | Vaste versie |
| -- | :--: |
| 1.12 | 1.12.10 of hoger |
| 1.13 | 1.13.8 of hoger |
| 1.14 | 1.14.4 of hoger |
| 1.15 en later | N.v.t. |

Als u een versie van Kubernetes gebruikt die niet de oplossing voor dit probleem heeft en uw knooppunt-VM in een mislukte status verkeert, u het probleem beperken door de VM-status handmatig bij te werken met een van de onderstaande opties:

* Voor een op een beschikbaarheidsset gebaseerd cluster:
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* Voor een vmss-gebaseerd cluster:
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Azure-bestanden en AKS-probleemoplossing

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Wat zijn de aanbevolen stabiele versies van Kubernetes voor Azure-bestanden?
 
| Kubernetes-versie | Aanbevolen versie |
| -- | :--: |
| 1.12 | 1.12.6 of hoger |
| 1.13 | 1.13.4 of hoger |
| 1.14 | 1.14.0 of hoger |

### <a name="what-versions-of-kubernetes-have-azure-files-support-on-the-sovereign-cloud"></a>Welke versies van Kubernetes hebben Azure Files-ondersteuning op de Sovereign Cloud?

| Kubernetes-versie | Aanbevolen versie |
| -- | :--: |
| 1.12 | 1.12.0 of hoger |
| 1.13 | 1.13.0 of hoger |
| 1.14 | 1.14.0 of hoger |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Wat zijn de standaard mountOptions bij het gebruik van Azure Files?

Aanbevolen instellingen:

| Kubernetes-versie | fileMode en dirMode-waarde|
| -- | :--: |
| 1.12.0 - 1.12.1 | 0755 |
| 1.12.2 en hoger | 0777 |

Als u een cluster met Kubernetes-versie 1.8.5 of hoger gebruikt en dynamisch het permanente volume met een opslagklasse maakt, kunnen bevestigingsopties worden opgegeven op het object opslagklasse. In het volgende voorbeeld wordt *0777 ingesteld:*

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

Enkele extra handige *mountOptions-instellingen:*

* *mfsymlinks* zorgt ervoor dat Azure Files symbolische koppelingen (cifs) ondersteunen
* *nobrl* voorkomt het verzenden van byte-bereikvergrendelingsaanvragen naar de server. Deze instelling is noodzakelijk voor bepaalde toepassingen die breken met cifs stijl verplichte byte bereik sloten. De meeste cifs-servers ondersteunen nog geen aanvragen van adviesbyte-bereikvergrendelingen. Als *nobrl*niet wordt gebruikt, kunnen toepassingen die breken met cifs-stijl verplichte bytebereikvergrendelingen foutberichten veroorzaken die vergelijkbaar zijn met:
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Fout "kan geen machtigingen wijzigen" bij het gebruik van Azure Files

Wanneer u PostgreSQL uitvoert op de plug-in Azure Files, ziet u mogelijk een fout die vergelijkbaar is met:

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

Deze fout wordt veroorzaakt door de plug-in Azure Files met behulp van het cifs/SMB-protocol. Bij het gebruik van het cifs/SMB-protocol konden de bestands- en mapmachtigingen niet worden gewijzigd na montage.

Als u dit probleem wilt oplossen, gebruikt u *subPath* samen met de plug-in Azure Disk. 

> [!NOTE] 
> Voor ext3/4 schijftype is er een verloren+gevonden map nadat de schijf is opgemaakt.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>Azure Files heeft een hoge latentie in vergelijking met Azure Disk bij het verwerken van veel kleine bestanden

In sommige gevallen, zoals het verwerken van veel kleine bestanden, u een hoge latentie ervaren bij het gebruik van Azure Files in vergelijking met Azure Disk.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>Fout bij het inschakelen van de instelling 'Toegang toestaan van geselecteerd netwerk toe staan vanuit geselecteerd netwerk' op het opslagaccount

Als u *toegang van een geselecteerd netwerk inschakelt* op een opslagaccount dat wordt gebruikt voor dynamische inrichting in AKS, krijgt u een foutmelding wanneer AKS een bestandsshare maakt:

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Deze fout is te wijten aan de Kubernetes *persistentvolume-controller* niet op het netwerk gekozen bij het instellen *toegang toestaan van geselecteerde netwerk*.

U het probleem beperken door [statische inrichting met Azure Files te gebruiken.](azure-files-volume.md)

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Azure Files kan niet opnieuw worden gemonteerd in de Windows-pod

Als een Windows-pod met een Azure Files-bevestiging wordt verwijderd en vervolgens opnieuw wordt gemaakt op hetzelfde knooppunt, mislukt de houder. Deze fout is `New-SmbGlobalMapping` vanwege het mislukken van de opdracht omdat de Azure Files-houder al op het knooppunt is gemonteerd.

U ziet bijvoorbeeld een fout die vergelijkbaar is met:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Dit probleem is opgelost in de volgende versies van Kubernetes:

| Kubernetes-versie | Vaste versie |
| -- | :--: |
| 1.12 | 1.12.6 of hoger |
| 1.13 | 1.13.4 of hoger |
| 1.14 en later | N.v.t. |

### <a name="azure-files-mount-fails-due-to-storage-account-key-changed"></a>Azure Files mount mislukt door dat opslagaccountsleutel is gewijzigd

Als uw opslagaccountsleutel is gewijzigd, worden azure-bestanden mogelijk gestart met fouten.

U het probleem beperken door het veld *azurestorageaccountkey* handmatig bij te werken in Azure-bestandsgeheim met uw door een basisbeheerde opslagaccountsleutel met basis64-gecodeerde opslagaccountsleutel.

Als u uw opslagaccountsleutel in base64 `base64`wilt coderen, u. Bijvoorbeeld:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Als u uw geheim `kubectl edit secret`Azure-bestand wilt bijwerken, gebruikt u . Bijvoorbeeld:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Na een paar minuten probeert het agentknooppunt de azure-bestandsbevestiging opnieuw met de bijgewerkte opslagsleutel.

### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>Clusterautoscaler kan niet schalen met fout kan knooppuntgroepgroottes niet oplossen

Als uw clusterautoscaler niet wordt opgeschaald/omlaag wordt geschaald en u een fout ziet zoals hieronder in de [clusterautoscaler-logboeken][view-master-logs].

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

Deze fout is te wijten aan een upstream cluster autoscaler race voorwaarde waarbij de cluster autoscaler eindigt met een andere waarde dan degene die daadwerkelijk in het cluster. Als u deze status wilt herstellen, schakelt u de [clusterautoscaler][cluster-autoscaler]eenvoudig uit en schakelt u deze opnieuw in.

### <a name="slow-disk-attachment-getazuredisklun-takes-10-to-15-minutes-and-you-receive-an-error"></a>Langzame schijfbijlage, GetAzureDiskLun duurt 10 tot 15 minuten en u ontvangt een fout

Op Kubernetes-versies **die ouder zijn dan 1.15.0** u een foutmelding ontvangen, zoals Error **WaitForAttach Kan Lun niet vinden voor schijf.**  De tijdelijke oplossing hiervoor is om ongeveer 15 minuten te wachten en opnieuw te proberen.

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md
