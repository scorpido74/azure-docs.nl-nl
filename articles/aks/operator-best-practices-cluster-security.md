---
title: Aanbevolen procedures voor operatoren - Clusterbeveiliging in Azure Kubernetes Services (AKS)
description: Lees de aanbevolen procedures voor de clusteroperator voor het beheren van clusterbeveiliging en -upgrades in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: f02f6588946e2b63a1a092aba15603d1685e8207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594801"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Aanbevolen procedures voor clusterbeveiliging en -upgrades in Azure Kubernetes Service (AKS)

Terwijl u clusters beheert in Azure Kubernetes Service (AKS), is de beveiliging van uw workloads en gegevens een belangrijke overweging. Vooral wanneer u multi-tenant clusters uitvoert met logische isolatie, moet u de toegang tot resources en workloads beveiligen. Om het risico op aanvallen te minimaliseren, moet u er ook voor zorgen dat u de nieuwste beveiligingsupdates van Kubernetes en knooppuntOS toepast.

In dit artikel wordt gespitst op het beveiligen van uw AKS-cluster. Procedures voor:

> [!div class="checklist"]
> * Azure Active Directory- en op rollen gebaseerde toegangsbesturingselementen gebruiken om api-servertoegang te beveiligen
> * Beveiligde containertoegang tot knooppuntbronnen
> * Een AKS-cluster upgraden naar de nieuwste Kubernetes-versie
> * Nodes up-to-date houden en automatisch beveiligingspatches toepassen

U ook de aanbevolen procedures voor [het beheer van containerafbeeldingen][best-practices-container-image-management] en voor [podbeveiliging][best-practices-pod-security]lezen.

U azure [Kubernetes Services-integratie met Security Center][security-center-aks] ook gebruiken om bedreigingen te detecteren en aanbevelingen te bekijken voor het beveiligen van uw AKS-clusters.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Beveiligde toegang tot de API-server en clusterknooppunten

**Richtlijnen voor aanbevolen procedures** - Het beveiligen van toegang tot de Kubernetes API-Server is een van de belangrijkste dingen die u doen om uw cluster te beveiligen. Integreer Kubernetes role-based access control (RBAC) met Azure Active Directory om de toegang tot de API-server te beheren. Met deze besturingselementen u AKS op dezelfde manier beveiligen als wanneer u de toegang tot uw Azure-abonnementen beveiligen.

De Kubernetes API-server biedt één verbindingspunt voor aanvragen om acties binnen een cluster uit te voeren. Als u de toegang tot de API-server wilt beveiligen en controleren, beperkt u de toegang en verstrekt u de minst bevoorrechte toegangsmachtigingen die nodig zijn. Deze aanpak is niet uniek voor Kubernetes, maar is vooral belangrijk wanneer het AKS-cluster logisch geïsoleerd is voor gebruik met meerdere tenants.

Azure Active Directory (AD) biedt een bedrijfsklare oplossing voor identiteitsbeheer die integreert met AKS-clusters. Aangezien Kubernetes geen oplossing voor identiteitsbeheer biedt, kan het anders moeilijk zijn om een gedetailleerde manier te bieden om de toegang tot de API-server te beperken. Met azure AD-geïntegreerde clusters in AKS gebruikt u uw bestaande gebruikers- en groepsaccounts om gebruikers te verifiëren naar de API-server.

![Azure Active Directory-integratie voor AKS-clusters](media/operator-best-practices-cluster-security/aad-integration.png)

Gebruik Kubernetes RBAC en Azure AD-integratie om de API-server te beveiligen en het minste aantal machtigingen te bieden dat nodig is voor een scopeset resources, zoals één naamruimte. Verschillende gebruikers of groepen in Azure AD kunnen verschillende RBAC-rollen krijgen. Met deze gedetailleerde machtigingen u de toegang tot de API-server beperken en een duidelijk controlespoor van uitgevoerde acties bieden.

De aanbevolen aanbevolen best practice is om groepen te gebruiken om toegang te bieden tot bestanden en mappen ten opzichte van individuele identiteiten, azure *AD-groepslidmaatschap* te gebruiken om gebruikers te binden aan RBAC-rollen in plaats van individuele *gebruikers.* Naarmate het groepslidmaatschap van een gebruiker verandert, zouden de toegangsmachtigingen voor het AKS-cluster dienovereenkomstig veranderen. Als u de gebruiker rechtstreeks aan een rol bindt, kan de functie veranderen. De Azure AD-groepslidmaatschappen worden bijgewerkt, maar machtigingen voor het AKS-cluster zouden dat niet weerspiegelen. In dit scenario krijgt de gebruiker uiteindelijk meer machtigingen dan een gebruiker nodig heeft.

Zie Aanbevolen procedures voor verificatie en [autorisatie in AKS voor][aks-best-practices-identity]meer informatie over Azure AD-integratie en RBAC.

## <a name="secure-container-access-to-resources"></a>Beveiligde containertoegang tot resources

**Richtlijnen voor aanbevolen procedures** - Beperk de toegang tot acties die containers kunnen uitvoeren. Geef het minste aantal machtigingen op en vermijd het gebruik van root/privileged escalation.

Op dezelfde manier dat u gebruikers of groepen het minste aantal vereiste bevoegdheden moet verlenen, moeten containers ook worden beperkt tot alleen de acties en processen die ze nodig hebben. Om het risico op aanvallen te minimaliseren, configureer t u geen toepassingen en containers waarvoor geëscaleerde bevoegdheden of hoofdtoegang vereist zijn. Stel bijvoorbeeld `allowPrivilegeEscalation: false` in het podmanifest. Deze *pod beveiligingscontexten* zijn ingebouwd in Kubernetes en u extra machtigingen definiëren, zoals de gebruiker of groep om te draaien als, of welke Linux-mogelijkheden bloot te leggen. Zie Toegang tot de [pod beveiligen tot bronnen voor][pod-security-contexts]meer aanbevolen procedures.

Voor meer gedetailleerde controle van containeracties u ook ingebouwde Linux-beveiligingsfuncties zoals *AppArmor* en *seccomp*gebruiken. Deze functies worden gedefinieerd op knooppuntniveau en vervolgens geïmplementeerd via een podmanifest. Ingebouwde Linux-beveiligingsfuncties zijn alleen beschikbaar op Linux-knooppunten en -pods.

> [!NOTE]
> Kubernetes-omgevingen, in AKS of elders, zijn niet helemaal veilig voor vijandig multi-tenant gebruik. Extra beveiligingsfuncties zoals *AppArmor,* *seccomp,* *Pod Security Policies*, of meer fijnmazige role-based access controls (RBAC) voor knooppunten maken exploits moeilijker. Echter, voor echte beveiliging bij het uitvoeren van vijandige multi-tenant workloads, een hypervisor is het enige niveau van beveiliging die u moet vertrouwen. Het beveiligingsdomein voor Kubernetes wordt het hele cluster, geen individueel knooppunt. Voor dit soort vijandige multi-tenant workloads moet u fysiek geïsoleerde clusters gebruiken.

### <a name="app-armor"></a>App Armor

Om de acties die containers kunnen uitvoeren te beperken, u de [AppArmor][k8s-apparmor] Linux kernel security module gebruiken. AppArmor is beschikbaar als onderdeel van het onderliggende AKS-knooppuntBEsturingssysteem en is standaard ingeschakeld. U maakt AppArmor-profielen die acties zoals lezen, schrijven of uitvoeren of systeemfuncties zoals het monteren van bestandssystemen beperken. Standaard AppArmor-profielen beperken `/proc` `/sys` de toegang tot verschillende en locaties en bieden een middel om containers logisch te isoleren van het onderliggende knooppunt. AppArmor werkt voor elke toepassing die draait op Linux, niet alleen Kubernetes pods.

![AppArmor-profielen die worden gebruikt in een AKS-cluster om containeracties te beperken](media/operator-best-practices-container-security/apparmor.png)

Om AppArmor in actie te zien, maakt het volgende voorbeeld een profiel dat het schrijven naar bestanden voorkomt. [SSH][aks-ssh] naar een AKS-knooppunt, maak vervolgens een bestand met de naam *deny-write.profile* en plak de volgende inhoud:

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  
  file,
  # Deny all file writes.
  deny /** w,
}
```

AppArmor profielen worden `apparmor_parser` toegevoegd met behulp van de opdracht. Voeg het profiel toe aan AppArmor en geef de naam op van het profiel dat in de vorige stap is gemaakt:

```console
sudo apparmor_parser deny-write.profile
```

Er is geen uitvoer geretourneerd als het profiel correct is ontleed en toegepast op AppArmor. Je wordt teruggebracht naar de opdrachtprompt.

Maak vanaf uw lokale machine nu een podmanifest met de naam *aks-apparmor.yaml* en plak de volgende inhoud. In dit manifest wordt `container.apparmor.security.beta.kubernetes` een annotatie gedefinieerd voor het toevoegen van verwijzingen naar het *deny-write-profiel* dat in de vorige stappen is gemaakt:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-apparmor
  annotations:
    container.apparmor.security.beta.kubernetes.io/hello: localhost/k8s-apparmor-example-deny-write
spec:
  containers:
  - name: hello
    image: busybox
    command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]
```

De voorbeeldpod implementeren met de opdracht [kubectl toepassen:][kubectl-apply]

```console
kubectl apply -f aks-apparmor.yaml
```

Als de pod is geïmplementeerd, gebruikt u de [opdracht kubectl exec][kubectl-exec] om naar een bestand te schrijven. De opdracht kan niet worden uitgevoerd, zoals in de volgende voorbeelduitvoer wordt weergegeven:

```
$ kubectl exec hello-apparmor touch /tmp/test

touch: /tmp/test: Permission denied
command terminated with exit code 1
```

Zie [AppArmor-profielen in Kubernetes][k8s-apparmor]voor meer informatie over AppArmor.

### <a name="secure-computing"></a>Veilig computergebruik

Terwijl AppArmor werkt voor elke Linux-applicatie, [seccomp *(sec*ure *comp*uting)][seccomp] werkt op procesniveau. Seccomp is ook een Linux kernel security module, en wordt native ondersteund door de Docker runtime gebruikt door AKS nodes. Met seccomp zijn de procesoproepen die containers kunnen uitvoeren beperkt. U maakt filters die bepalen welke acties u wilt toestaan of weigeren en gebruikt vervolgens annotaties in een YAML-manifest van een pod om te associëren met het seccomp-filter. Dit sluit aan bij de beste praktijk van alleen het verlenen van de container de minimale machtigingen die nodig zijn om uit te voeren, en niet meer.

Als u seccomp in actie wilt zien, maakt u een filter dat voorkomt dat machtigingen voor een bestand worden gewijzigd. [SSH][aks-ssh] naar een AKS-knooppunt, maak vervolgens een seccomp filter met de naam */var/lib/kubelet/seccomp/prevent-chmod* en plak de volgende inhoud:

```
{
  "defaultAction": "SCMP_ACT_ALLOW",
  "syscalls": [
    {
      "name": "chmod",
      "action": "SCMP_ACT_ERRNO"
    }
  ]
}
```

Maak nu vanuit uw lokale machine een podmanifest met de naam *aks-seccomp.yaml* en plak de volgende inhoud. Dit manifest definieert een `seccomp.security.alpha.kubernetes.io` annotatie voor en verwijst naar het *prevent-chmod-filter* dat in de vorige stap is gemaakt:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: chmod-prevented
  annotations:
    seccomp.security.alpha.kubernetes.io/pod: localhost/prevent-chmod
spec:
  containers:
  - name: chmod
    image: busybox
    command:
      - "chmod"
    args:
     - "777"
     - /etc/hostname
  restartPolicy: Never
```

De voorbeeldpod implementeren met de opdracht [kubectl toepassen:][kubectl-apply]

```console
kubectl apply -f ./aks-seccomp.yaml
```

Bekijk de status van de pods met de opdracht [kubectl get pods.][kubectl-get] De pod meldt een fout. De `chmod` opdracht wordt niet uitgevoerd door het secondecomp-filter, zoals in de volgende voorbeelduitvoer wordt weergegeven:

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

Zie [Seccomp-beveiligingsprofielen voor Docker voor][seccomp]meer informatie over beschikbare filters.

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Regelmatig updaten naar de nieuwste versie van Kubernetes

**Richtlijnen voor aanbevolen procedures** - Om op de hoogte te blijven van nieuwe functies en bugfixes, wordt u regelmatig geüupgradet naar de Kubernetes-versie in uw AKS-cluster.

Kubernetes brengt nieuwe functies in een sneller tempo uit dan meer traditionele infrastructuurplatforms. Kubernetes-updates bevatten nieuwe functies en bug- of beveiligingsoplossingen. Nieuwe functies gaan meestal door een *alfa-* en *bètastatus* voordat ze *stabiel* worden en zijn algemeen beschikbaar en aanbevolen voor productiegebruik. Met deze releasecyclus u Kubernetes bijwerken zonder regelmatig wijzigingen door te maken of uw implementaties en sjablonen aan te passen.

AKS ondersteunt vier kleine versies van Kubernetes. Dit betekent dat wanneer een nieuwe kleine patchversie wordt geïntroduceerd, de oudste secundaire versie en patchreleases die worden ondersteund, worden teruggetrokken. Kleine updates voor Kubernetes gebeuren op een periodieke basis. Zorg ervoor dat u een governance-proces om te controleren en te upgraden als dat nodig is, zodat u niet uit de ondersteuning vallen. Zie [Ondersteunde Kubernetes-versies AKS][aks-supported-versions] voor meer informatie

Als u wilt controleren welke versies beschikbaar zijn voor uw cluster, gebruikt u de opdracht [az aks-upgrades][az-aks-get-upgrades] zoals weergegeven in het volgende voorbeeld:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Vervolgens u uw AKS-cluster upgraden met de [upgradeopdracht az aks.][az-aks-upgrade] Het upgradeproces maakt veilig een node tegelijk vast, plant pods op resterende knooppunten en implementeert vervolgens een nieuw knooppunt met de nieuwste OS- en Kubernetes-versies.

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
```

Zie [Ondersteunde Kubernetes-versies in AKS][aks-supported-versions] en [Upgrade een AKS-cluster][aks-upgrade]voor meer informatie over upgrades in AKS.

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Linux-knooppuntupdates en reboots verwerken met kured

**Richtlijnen voor beste praktijken** - AKS downloadt en installeert automatisch beveiligingsoplossingen op elke Linux-knooppunten, maar start niet automatisch opnieuw op indien nodig. Gebruik `kured` om te kijken voor in afwachting van reboots, dan veilig cordon en afvoer van het knooppunt om het knooppunt opnieuw op te starten, de updates toe te passen en zo veilig mogelijk met betrekking tot het OS. Voor Windows Server-knooppunten (momenteel in preview in AKS) voert u regelmatig een AKS-upgradebewerking uit om pods veilig te verbinden en af te voeren en bijgewerkte knooppunten te implementeren.

Elke avond krijgen Linux-knooppunten in AKS beveiligingspatches beschikbaar via hun distro-updatekanaal. Dit gedrag wordt automatisch geconfigureerd omdat de knooppunten worden geïmplementeerd in een AKS-cluster. Om verstoring en mogelijke impact op het uitvoeren van workloads te minimaliseren, worden knooppunten niet automatisch opnieuw opgestart als een beveiligingspatch of kernelupdate dit vereist.

De open-source [kured (KUbernetes REboot Daemon)][kured] project door Weaveworks horloges voor lopende node reboots. Wanneer een Linux-knooppunt updates toepast waarvoor een herstart vereist is, wordt het knooppunt veilig aangesloten en afgevoerd om de pods op andere knooppunten in het cluster te verplaatsen en te plannen. Zodra het knooppunt opnieuw is opgestart, wordt het weer toegevoegd aan het cluster en kubernetes hervat het plannen van pods op het. Om verstoring te minimaliseren, mag slechts één knooppunt tegelijk `kured`opnieuw worden opgestart door .

![De AKS node reboot proces met kured](media/operator-best-practices-cluster-security/node-reboot-process.png)

Als u fijnere korrelcontrole wilt `kured` over wanneer opnieuw wordt opgestart, u met Prometheus worden geïntegreerd om opnieuw opstarten te voorkomen als er andere onderhoudsgebeurtenissen of clusterproblemen aan de gang zijn. Deze integratie minimaliseert extra complicaties door knooppunten opnieuw op te starten terwijl u actief andere problemen oplost.

Zie [Beveiligings- en kernelupdates toepassen op knooppunten in AKS voor][aks-kured]meer informatie over het verwerken van opnieuw opstarten van knooppunten.

## <a name="next-steps"></a>Volgende stappen

Dit artikel richtte zich op het beveiligen van uw AKS-cluster. Zie de volgende artikelen om een aantal van deze gebieden te implementeren:

* [Azure Active Directory integreren met AKS][aks-aad]
* [Een AKS-cluster upgraden naar de nieuwste versie van Kubernetes][aks-upgrade]
* [Beveiligingsupdates verwerken en knooppunt opnieuw opstarten met kured][aks-kured]

<!-- EXTERNAL LINKS -->
[kured]: https://github.com/weaveworks/kured
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[aks-supported-versions]: supported-kubernetes-versions.md
[aks-upgrade]: upgrade-cluster.md
[aks-best-practices-identity]: concepts-identity.md
[aks-kured]: node-updates-kured.md
[aks-aad]: azure-ad-integration.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
[security-center-aks]: /azure/security-center/azure-kubernetes-service-integration
