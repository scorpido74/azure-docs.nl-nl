---
title: Werkbelasting beveiligingen voor uw Kubernetes-workloads
description: Meer informatie over het gebruik van de Azure Security Center set van beveiligings aanbevelingen voor Kubernetes beveiliging van werk belasting
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/12/2020
ms.author: memildin
ms.openlocfilehash: 138b3b35633b432193a1972421f05d0a8e52b90a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301353"
---
# <a name="protect-your-kubernetes-workloads"></a>Kubernetes-workloads beveiligen

Op deze pagina wordt beschreven hoe u de beveiligings aanbevelingen van Azure Security Center gebruikt voor Kubernetes beveiliging van werk belastingen.

Meer informatie over deze functies in [Aanbevolen procedures voor workload Protection met behulp van Kubernetes Admission Control](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

Security Center biedt meer container beveiligings functies als u Azure Defender inschakelt. Specifiek:

- Uw container registers scannen op beveiligings problemen met [Azure Defender voor container registers](defender-for-container-registries-introduction.md)
- Ontvang realtime waarschuwingen voor bedreigingen detectie voor uw K8s-clusters [Azure Defender voor Kubernetes](defender-for-kubernetes-introduction.md)

> [!TIP]
> Zie de [sectie container](recommendations-reference.md#recs-containers) van de naslag tabel met aanbevelingen voor een lijst met *alle* beveiligings aanbevelingen die kunnen worden weer gegeven voor Kubernetes-clusters en-knoop punten.



## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Release status:|Preview|
|Koers|Gratis|
|Vereiste rollen en machtigingen:|**Eigenaar** of **beveiligings beheerder** voor het bewerken van een toewijzing<br>**Lezer** voor het weer geven van de aanbevelingen|
|Ondersteunde clusters|Kubernetes v 1.14 (of hoger) is vereist<br>Geen PodSecurityPolicy-resource (oud PSP-model) op de clusters<br>Windows-knoop punten worden niet ondersteund|
|Clouds|![Yes](./media/icons/yes-icon.png) Commerciële Clouds<br>![No](./media/icons/no-icon.png) National/soeverein (US Gov, China gov, andere gov)|
|||


## <a name="set-up-your-workload-protection"></a>Uw werk belasting beveiliging instellen

Azure Security Center bevat een bundel van aanbevelingen die beschikbaar zijn wanneer u de **Azure Policy-invoeg toepassing voor Kubernetes**hebt geïnstalleerd.

1. Als u de aanbevelingen wilt configureren, moet u eerst de invoeg toepassing installeren op:

    1. Zoek op de pagina aanbevelingen naar de aanbeveling met de naam **Azure Policy invoeg toepassing voor Kubernetes moet zijn geïnstalleerd en ingeschakeld op uw clusters**.

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes.png" alt-text="Aanbeveling * * Azure Policy invoeg toepassing voor Kubernetes moet worden geïnstalleerd en ingeschakeld op uw clusters * *":::

        > [!TIP]
        > De aanbeveling is opgenomen in vijf verschillende beveiligings mechanismen en maakt het niet uit welke u in de volgende stap selecteert.

    1. Selecteer in een van de beveiligings besturings elementen de aanbeveling om de resources te zien waarop u de toevoegen kunt installeren en selecteer **herstellen**. 

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes-details.png" alt-text="Details van aanbevelings pagina voor * * Azure Policy invoeg toepassing voor Kubernetes moet worden geïnstalleerd en ingeschakeld op uw clusters * *":::

1. Ongeveer 30 minuten nadat de installatie van de invoeg toepassing is voltooid, wordt in Security Center de status van de clusters weer gegeven voor de volgende aanbevelingen, elk in de relevante beveiligings controle zoals weer gegeven:

    > [!TIP]
    > Sommige aanbevelingen hebben para meters die moeten worden aangepast via Azure Policy om ze effectief te gebruiken. Als u bijvoorbeeld wilt profiteren van de aanbevolen **container installatie kopieën alleen van vertrouwde registers, moet**u uw vertrouwde registers definiëren.
    > 
    > Als u de vereiste para meters niet opgeeft voor de aanbevelingen die moeten worden geconfigureerd, worden uw werk belastingen weer gegeven als beschadigd.

    | Naam aanbeveling                                                                   | Beveiligings beheer                         | Configuratie vereist |
    |---------------------------------------------------------------------------------------|------------------------------------------|------------------------|
    | De CPU-en geheugen limieten van de container moeten worden afgedwongen (preview-versie)                          | Toepassingen beveiligen tegen DDoS-aanval | No                     |
    | Geprivilegieerde containers moeten worden vermeden (preview-versie)                                     | Toegang en machtigingen beheren            | No                     |
    | Onveranderbaar (alleen-lezen) hoofd bestands systeem moet worden afgedwongen voor containers (preview-versie)     | Toegang en machtigingen beheren            | No                     |
    | Container met bevoegdheids escalatie moet worden vermeden (preview-versie)                       | Toegang en machtigingen beheren            | No                     |
    | Containers uitvoeren als hoofd gebruiker moet worden vermeden (preview-versie)                           | Toegang en machtigingen beheren            | No                     |
    | Containers die gevoelige host-naam ruimten delen, moeten worden vermeden (preview-versie)              | Toegang en machtigingen beheren            | No                     |
    | Er moeten mini maal bevoegde Linux-mogelijkheden worden afgedwongen voor containers (preview-versie)       | Toegang en machtigingen beheren            | **Ja**                |
    | Het gebruik van pod HostPath-volume koppelingen moet worden beperkt tot een bekende lijst (preview-versie)    | Toegang en machtigingen beheren            | **Ja**                |
    | Containers moeten worden geluisterd op toegestane poorten (preview-versie)                              | Onbevoegde netwerk toegang beperken     | **Ja**                |
    | Services mogen alleen op toegestane poorten Luis teren (preview)                                | Onbevoegde netwerk toegang beperken     | **Ja**                |
    | Het gebruik van hostnetwerkadapters en poorten moet worden beperkt (preview-versie)                     | Onbevoegde netwerk toegang beperken     | **Ja**                |
    | Het negeren of uitschakelen van containers AppArmor profiel moet worden beperkt (preview-versie) | Beveiligingsconfiguraties herstellen        | **Ja**                |
    | Container installatie kopieën moeten alleen worden geïmplementeerd vanuit vertrouwde registers (preview)            | Beveiligings problemen oplossen                | **Ja**                |


1. Voor de aanbevelingen waarvoor para meters moeten worden aangepast, stelt u de para meters in:

    1. Selecteer in het menu van Security Center het **beveiligings beleid**.
    1. Selecteer het betreffende abonnement.
    1. Selecteer in de sectie **Security Center standaard beleid** de optie **effectief beleid weer geven**.
    1. Selecteer ' ASC standaard '.
    1. Open het tabblad **para meters** en wijzig de waarden zoals vereist.
    1. Selecteer **Beoordelen en opslaan**.
    1. Selecteer **Opslaan**.


1. Om een van de aanbevelingen af te dwingen, 

    1. Open de pagina aanbeveling Details en selecteer **weigeren**:

        :::image type="content" source="./media/defender-for-kubernetes-usage/enforce-workload-protection-example.png" alt-text="De optie weigeren voor de para meter Azure Policy":::

        Hiermee opent u het deel venster waarin u het bereik hebt ingesteld. 

    1. Wanneer u het bereik hebt ingesteld, selecteert u **wijzigen in weigeren**.

1. Om te zien welke aanbevelingen van toepassing zijn op uw clusters:

    1. Open de pagina [Asset Inventory](asset-inventory.md) van Security Center en gebruik het resource type filter om **Services te Kubernetes**.

    1. Selecteer een cluster om te onderzoeken en Bekijk de beschik bare aanbevelingen hiervoor. 

1. Wanneer u een aanbeveling bekijkt vanuit de werk belasting beveiliging, ziet u het aantal betrokken peulen (' Kubernetes-onderdelen ') naast het cluster. Voor een lijst van het specifieke peul selecteert u het cluster en vervolgens kiest u **actie ondernemen**.

    :::image type="content" source="./media/defender-for-kubernetes-usage/view-affected-pods-for-recommendation.gif" alt-text="Het betrokken peul weer geven voor een K8s-aanbeveling"::: 

1. Als u de afdwinging wilt testen, gebruikt u de volgende twee Kubernetes-implementaties:

    - Een is voor een goede implementatie, conform de aanbevelingen voor de beveiliging van werk Last.
    - De andere is voor een slechte implementatie, niet-compatibel met *een* van de aanbevelingen.

    Implementeer het voor beeld. YAML-bestanden als-is of gebruik deze als referentie om uw eigen workload te herstellen (stap VIII)  


## <a name="healthy-deployment-example-yaml-file"></a>Voor beeld van een goede implementatie. yaml-bestand

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-healthy-deployment
  labels:
    app: redis
spec:
  replicas: 3
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
      annotations:
        apparmor.security.beta.kubernetes.io/pod: runtime/default
        container.apparmor.security.beta.kubernetes.io/redis: runtime/default
    spec:
      containers:
      - name: redis
        image: healthyClusterRegistry.azurecr.io/redis:latest
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: 100m
            memory: 250Mi
        securityContext:
          privileged: false
          readOnlyRootFilesystem: true
          allowPrivilegeEscalation: false
          runAsNonRoot: true
          runAsUser: 1000
---
apiVersion: v1
kind: Service
metadata:
  name: redis-healthy-service
spec:
  type: LoadBalancer
  selector:
    app: redis
  ports:
  - port: 80
    targetPort: 80
```

## <a name="unhealthy-deployment-example-yaml-file"></a>Voor beeld van een slechte implementatie. yaml-bestand

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-unhealthy-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:      
      labels:
        app: nginx
    spec:
      hostNetwork: true
      hostPID: true 
      hostIPC: true
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 9001
          hostPort: 9001
        securityContext:
          privileged: true
          readOnlyRootFilesystem: false
          allowPrivilegeEscalation: true
          runAsUser: 0
          capabilities:
            add:
              - NET_ADMIN
        volumeMounts:
        - mountPath: /test-pd
          name: test-volume
          readOnly: true
      volumes:
      - name: test-volume
        hostPath:
          # directory location on host
          path: /tmp
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-unhealthy-service
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
  - port: 6001
    targetPort: 9001
```



## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u Kubernetes werk belasting beveiliging kunt configureren. 

Zie de volgende pagina's voor meer gerelateerde materialen: 

- [Aanbevelingen voor Security Center voor containers](recommendations-reference.md#recs-containers)
- [Waarschuwingen voor het AKS-cluster niveau](alerts-reference.md#alerts-akscluster)
- [Waarschuwingen voor container niveau hostniveau](alerts-reference.md#alerts-containerhost)