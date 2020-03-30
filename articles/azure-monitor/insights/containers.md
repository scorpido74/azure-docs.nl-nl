---
title: Oplossing voor containerbewaking in Azure-monitor | Microsoft Documenten
description: Met de oplossing Containermonitoring in Azure Monitor u uw Docker- en Windows-containerhosts op één locatie weergeven en beheren.
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/22/2019
ms.openlocfilehash: 171f897f6e110e8f759281c139addab477ecede3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664691"
---
# <a name="container-monitoring-solution-in-azure-monitor"></a>Containermonitoringoplossing in Azure-monitor

![Het symbool van containers](./media/containers/containers-symbol.png)

In dit artikel wordt beschreven hoe u de oplossing Containermonitoring in Azure Monitor instellen en gebruiken, waarmee u uw Docker- en Windows-containerhosts op één locatie bekijken en beheren. Docker is een softwarevirtualisatiesysteem dat wordt gebruikt om containers te maken die de implementatie van software naar hun IT-infrastructuur automatiseren.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

De oplossing laat zien welke containers worden uitgevoerd, welke containerafbeelding ze uitvoeren en waar containers worden uitgevoerd. U gedetailleerde controle-informatie weergeven met opdrachten die worden gebruikt bij containers. En u containers oplossen door gecentraliseerde logboeken te bekijken en te doorzoeken zonder docker- of Windows-hosts op afstand te hoeven bekijken. U containers vinden die luidruchtig kunnen zijn en overtollige resources verbruiken op een host. En u gecentraliseerde CPU-, geheugen-, opslag- en netwerkgebruik- en prestatiegegevens voor containers bekijken. Op computers met Windows u logboeken van Windows Server-, Hyper-V- en Docker-containers centraliseren en vergelijken. De oplossing ondersteunt de volgende containerorchestrators:

- Docker Swarm
- DC/OS
- Kubernetes
- Service Fabric
- Red Hat OpenShift

Als u containers hebt geïmplementeerd in [Azure Service Fabric,](../../service-fabric/service-fabric-overview.md)raden we u aan zowel de [Service Fabric-oplossing](../../service-fabric/service-fabric-diagnostics-oms-setup.md) als deze oplossing in te schakelen om bewaking van clustergebeurtenissen op te nemen. Voordat u de Service Fabric-oplossing inschakelt, bekijkt [u De Service Fabric-oplossing gebruiken](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md) om te begrijpen wat deze biedt en hoe deze te gebruiken.

Zie [Monitor Azure Kubernetes Service](../../azure-monitor/insights/container-insights-overview.md)als u de prestaties wilt controleren van uw workloads die zijn geïmplementeerd in Kubernetes-omgevingen die worden gehost op Azure Kubernetes Service (AKS). De Container Monitoring-oplossing ondersteunt geen monitoring van dat platform.  

In het volgende diagram worden de relaties tussen verschillende containerhosts en agents met Azure Monitor weergegeven.

![Containersdiagram](./media/containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Systeemvereisten en ondersteunde platforms

Voordat u begint, controleert u de volgende details om te controleren of u aan de vereisten voldoet.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Ondersteuning voor containermonitoringoplossingen voor Docker Orchestrator en OS-platform

In de volgende tabel wordt de ondersteuning voor docker-orkestratie en besturingssysteembewaking van containervoorraad, -prestaties en logboeken met Azure Monitor beschreven.   

| | ACS | Linux | Windows | Container<br>Inventaris | Installatiekopie<br>Inventaris | Knooppunt<br>Inventaris | Container<br>Prestaties | Container<br>Gebeurtenis | Gebeurtenis<br>Logboek | Container<br>Logboek |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosfeer<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Zwerm | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Service<br>Fabric | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Rode Hoed Open<br>SHIFT | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>(stand-alone) | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Linux-server<br>(stand-alone) | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |

### <a name="docker-versions-supported-on-linux"></a>Docker-versies ondersteund op Linux

- Docker 1.11 tot 1.13
- Docker CE en EE v17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>x64 Linux-distributies ondersteund als containerhosts

- Ubuntu 14.04 LTS en 16.04 LTS
- CoreOS(stabiel)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- openSUSE LEAP 42.2
- CentOS 7.2 en 7.3
- SLES 12
- RHEL 7.2 en 7.3
- Red Hat OpenShift Container Platform (OCP) 3.4 en 3.5
- ACS Mesosphere DC/OS 1.7.3 tot 1.8.8
- ACS Kubernetes 1.4.5 tot 1.6
    - Kubernetes-gebeurtenissen, Kubernetes-inventaris en containerprocessen worden alleen ondersteund met versie 1.4.1-45 en later van de Log Analytics-agent voor Linux
- ACS Docker Zwerm

[!INCLUDE [log-analytics-agent-note.md](../../../includes/log-analytics-agent-note.md)] 

### <a name="supported-windows-operating-system"></a>Ondersteund Windows-besturingssysteem

- Windows Server 2016
- Windows 10 Anniversary Edition (professioneel of zakelijk)

### <a name="docker-versions-supported-on-windows"></a>Docker-versies die worden ondersteund op Windows

- Docker 1.12 en 1.13
- Docker 17.03.0 en hoger

## <a name="installing-and-configuring-the-solution"></a>De oplossing installeren en configureren

Gebruik de volgende informatie om de oplossing te installeren en configureren.

1. Voeg de oplossing containerbewaking toe aan uw Log Analytics-werkruimte vanuit [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) of met behulp van het proces dat is beschreven in [Bewakingsoplossingen toevoegen vanuit de Galerie Oplossingen.](../../azure-monitor/insights/solutions.md)

2. Installeer en gebruik Docker met een Log Analytics-agent. Op basis van uw besturingssysteem en Docker orchestrator u de volgende methoden gebruiken om uw agent te configureren.
   - Voor zelfstandige hosts:
     - Installeer en voer Docker uit en installeer de [Log Analytics-agent voor Linux](../../azure-monitor/learn/quick-collect-linux-computer.md)en installeer deze op ondersteunde Linux-besturingssystemen.  
     - Op CoreOS u de Log Analytics-agent voor Linux niet uitvoeren. In plaats daarvan voert u een containerversie van de Log Analytics-agent voor Linux uit. Bekijk Linux-containerhosts, waaronder CoreOS- of Azure Government Linux-containerhosts, waaronder CoreOS, als u met containers in Azure Government Cloud werkt.
     - Installeer op Windows Server 2016 en Windows 10 de Docker Engine en de client en sluit vervolgens een agent aan om informatie te verzamelen en naar Azure Monitor te verzenden. [Controleer Windows-containerhosts installeren en configureren](#install-and-configure-windows-container-hosts) als u een Windows-omgeving hebt.
   - Voor Docker multi-host orchestration:
     - Als u een Red Hat OpenShift-omgeving hebt, bekijkt u Een Log Analytics-agent configureren voor Red Hat OpenShift.
     - Als u een Kubernetes-cluster hebt met de Azure Container Service:
       - Bekijk [Een Log Analytics Linux-agent configureren voor Kubernetes](#configure-a-log-analytics-linux-agent-for-kubernetes).
       - Bekijk [Een Windows-agent log-analyse configureren voor Kubernetes](#configure-a-log-analytics-windows-agent-for-kubernetes).
       - Bekijk Helm om Log Analytics-agent te implementeren op Linux Kubernetes.
     - Als u een Azure Container Service DC/OS-cluster hebt, leest u meer bij [Monitor een Azure Container Service DC/OS-cluster met Azure Monitor](../../container-service/dcos-swarm/container-service-monitoring-oms.md).
     - Als u een Docker Swarm-modushebt, leest u meer bij Een Logboekanalyse-agent configureren voor Docker Swarm.
     - Als u een cluster servicestructuur hebt, leest u meer bij [Monitor-containers met Azure Monitor](../../service-fabric/service-fabric-diagnostics-oms-containers.md).

Bekijk het artikel [Docker Engine op Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) voor meer informatie over het installeren en configureren van uw Docker-engines op computers met Windows.

> [!IMPORTANT]
> Docker moet worden uitgevoerd **voordat** u de [Log Analytics-agent voor Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) op uw containerhosts installeert. Als u de agent al hebt geïnstalleerd voordat u Docker installeert, moet u de Log Analytics-agent voor Linux opnieuw installeren. Zie de [Docker-website](https://www.docker.com)voor meer informatie over Docker.

### <a name="install-and-configure-linux-container-hosts"></a>Linux-containerhosts installeren en configureren

Nadat u Docker hebt geïnstalleerd, gebruikt u de volgende instellingen voor uw containerhost om de agent te configureren voor gebruik met Docker. Eerst hebt u uw Log Analytics-werkruimte-id en -sleutel nodig, die u vinden in de Azure-portal. Klik in uw werkruimte op **Snelstartcomputers** > **Computers** om uw **werkruimte-id** en **primaire sleutel weer te geven.**  Kopieer en plak beide in uw favoriete editor.

**Voor alle Linux containerhosts behalve CoreOS:**

- Zie Overzicht van de Log [Analytics-agent](../../azure-monitor/platform/log-analytics-agent.md)voor meer informatie en stappen over het installeren van de Log Analytics-agent voor Linux.

**Voor alle Linux containerhosts, inclusief CoreOS:**

Start de container die u wilt controleren. Wijzig en gebruik het volgende voorbeeld:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**Voor alle Linux-containerhosts van Azure Government, waaronder CoreOS:**

Start de container die u wilt controleren. Wijzig en gebruik het volgende voorbeeld:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Overschakelen van het gebruik van een geïnstalleerde Linux-agent naar een in een container**

Als u eerder de direct geïnstalleerde agent hebt gebruikt en in plaats daarvan een agent wilt gebruiken die in een container wordt uitgevoerd, moet u eerst de agent Log Analytics voor Linux verwijderen. Zie [De log-analyse-agent voor Linux verwijderen](../../azure-monitor/learn/quick-collect-linux-computer.md) om te begrijpen hoe u de agent met succes verwijderen.  

#### <a name="configure-a-log-analytics-agent-for-docker-swarm"></a>Een Log Analytics-agent configureren voor Docker Swarm

U de Log Analytics-agent als een wereldwijde service uitvoeren op Docker Swarm. Gebruik de volgende gegevens om een Log Analytics-agentservice te maken. U moet uw Log Analytics Workspace ID en primaire sleutel opgeven.

- Voer het volgende uit op het hoofdknooppunt.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Veilige geheimen voor Docker Swarm

Voor Docker Swarm, zodra het geheim voor Workspace ID en Primaire Sleutel is gemaakt, gebruikt u de volgende informatie om uw geheime informatie te maken.

1. Voer het volgende uit op het hoofdknooppunt.

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. Controleer of geheimen goed zijn gemaakt.

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. Voer de volgende opdracht uit om de geheimen aan de containerized Log Analytics-agent te monteren.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-a-log-analytics-agent-for-red-hat-openshift"></a>Een Log Analytics-agent configureren voor Red Hat OpenShift

Er zijn drie manieren om de Log Analytics-agent toe te voegen aan Red Hat OpenShift om containerbewakingsgegevens te verzamelen.

* [Installeer de Log Analytics-agent voor Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) rechtstreeks op elk OpenShift-knooppunt  
* [Log Analytics VM-extensie inschakelen](../../azure-monitor/learn/quick-collect-azurevm.md) op elk OpenShift-knooppunt dat in Azure woont  
* De Log Analytics-agent installeren als een OpenShift-daemon-set  

In deze sectie behandelen we de stappen die nodig zijn om de Log Analytics-agent te installeren als een OpenShift daemon-set.  

1. Meld u aan bij het OpenShift-hoofdknooppunt en kopieer het [yaml-bestand ocp-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) van GitHub naar uw hoofdknooppunt en wijzig de waarde met uw Log Analytics Workspace ID en met uw primaire sleutel.
2. Voer de volgende opdrachten uit om een project voor Azure Monitor te maken en het gebruikersaccount in te stellen.

    ```
    oc adm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Voer het volgende uit om de daemon-set te implementeren:

    `oc create -f ocp-omsagent.yaml`

4. Als u wilt controleren of het is geconfigureerd en correct werkt, typt u het volgende:

    `oc describe daemonset omsagent`  

    en de output moet lijken op:

    ```
    [ocpadmin@khm-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

Als u geheimen wilt gebruiken om uw Log Analytics Workspace ID en primaire sleutel te beveiligen wanneer u het yaml-bestand van de Log Analytics-agent daemon gebruikt, voert u de volgende stappen uit.

1. Meld u aan bij het OpenShift-hoofdknooppunt en kopieer het yaml-bestand [ocp-ds-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) en het geheime genererende script [ocp-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) van GitHub.  Dit script genereert het geheimenyaml-bestand voor Log Analytics Workspace ID en primaire sleutel om uw geheime informatie te beveiligen.  
2. Voer de volgende opdrachten uit om een project voor Azure Monitor te maken en het gebruikersaccount in te stellen. Het geheime genererende script vraagt om `<WSID>` uw `<KEY>` Log Analytics Workspace ID en primaire sleutel en na voltooiing wordt het ocp-secret.yaml-bestand gemaakt.  

    ```
    oc adm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Implementeer het geheime bestand door het volgende uit te voeren:

    `oc create -f ocp-secret.yaml`

4. Controleer de implementatie door het volgende uit te voeren:

    `oc describe secret omsagent-secret`  

    en de output moet lijken op:  

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe secret omsagent-secret  
    Name:           omsagent-secret  
    Namespace:      omslogging  
    Labels:         <none>  
    Annotations:    <none>  

    Type:   Opaque  

    Data  
    ====  
    KEY:    89 bytes  
    WSID:   37 bytes  
    ```

5. Implementeer het yaml-bestand van de Log Analytics-agent daemon door het volgende uit te voeren:

    `oc create -f ocp-ds-omsagent.yaml`  

6. Controleer de implementatie door het volgende uit te voeren:

    `oc describe ds oms`

    en de output moet lijken op:

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

#### <a name="configure-a-log-analytics-linux-agent-for-kubernetes"></a>Een Log Analytics Linux-agent configureren voor Kubernetes

Voor Kubernetes gebruikt u een script om het geheimenyaml-bestand voor uw Workspace ID en primaire sleutel te genereren om de Log Analytics-agent voor Linux te installeren. Op de [Pagina Van Kubernetes GitHub van Log Analytics-docker](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) zijn er bestanden die u met of zonder uw geheime informatie gebruiken.

- De Standaard Log Analytics-agent voor Linux DaemonSet heeft geen geheime informatie (omsagent.yaml)
- De Log Analytics agent voor Linux DaemonSet yaml bestand maakt gebruik van geheime informatie (omsagent-ds-secrets.yaml) met geheime generatie scripts om de geheimen yaml (omsagentsecret.yaml) bestand te genereren.

U ervoor kiezen om omsagent DaemonSets te maken met of zonder geheimen.

**Standaard OMSagent DaemonSet yaml-bestand zonder geheimen**

- Vervang voor het standaard Bestand van Logboekanalyse-agent DaemonSet yaml het `<WSID>` en uw WSID en `<KEY>` KEY. Kopieer het bestand naar uw hoofdknooppunt en voer het volgende uit:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Standaard OMSagent DaemonSet yaml-bestand met geheimen**

1. Als u Log Analytics-agent DaemonSet wilt gebruiken met behulp van geheime informatie, maakt u eerst de geheimen.
    1. Kopieer het script en het geheime sjabloonbestand en zorg ervoor dat ze zich in dezelfde map bevinden.
        - Geheim genererend script - secret-gen.sh
        - geheime sjabloon - secret-template.yaml
    2. Voer het script uit, zoals het volgende voorbeeld. Het script vraagt om de Log Analytics Workspace ID en primary key en nadat u ze hebt ingevoerd, maakt het script een geheim yaml-bestand, zodat u het uitvoeren.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. Maak de pod Geheimen door het volgende uit te voeren:
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. Voer het volgende uit om te verifiëren:

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        Output moet lijken op:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        Output moet lijken op:

        ```
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Maak je omsagent daemon-set door uit te voeren```sudo kubectl create -f omsagent-ds-secrets.yaml```

2. Controleer of de Logboekanalyse-agent DaemonSet wordt uitgevoerd, vergelijkbaar met het volgende:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```

Gebruik voor Kubernetes een script om het geheimenyaml-bestand voor Workspace ID en Primaire sleutel voor de Log Analytics-agent voor Linux te genereren. Gebruik de volgende voorbeeldinformatie met het [omsagent yaml-bestand](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) om uw geheime informatie te beveiligen.

```
keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
Name:           omsagent-secret
Namespace:      default
Labels:         <none>
Annotations:    <none>

Type:   Opaque

Data
====
WSID:   36 bytes
KEY:    88 bytes
```

#### <a name="configure-a-log-analytics-windows-agent-for-kubernetes"></a>Een Windows-agent log Analytics configureren voor Kubernetes

Voor Windows Kubernetes gebruikt u een script om het yaml-bestand voor geheimen voor uw Workspace ID en primaire sleutel te genereren om de Log Analytics-agent te installeren. Op de [Pagina Van Kubernetes GitHub van Log Analytics-docker](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) zijn er bestanden die u gebruiken met uw geheime informatie.  U moet de log-analyse-agent afzonderlijk installeren voor de hoofd- en agentknooppunten.  

1. Als u Log Analytics-agent DaemonSet wilt gebruiken met behulp van geheime informatie op het hoofdknooppunt, meldt u zich eerst aan en maakt u eerst de geheimen.
    1. Kopieer het script en het geheime sjabloonbestand en zorg ervoor dat ze zich in dezelfde map bevinden.
        - Geheim genererend script - secret-gen.sh
        - geheime sjabloon - secret-template.yaml

    2. Voer het script uit, zoals het volgende voorbeeld. Het script vraagt om de Log Analytics Workspace ID en primary key en nadat u ze hebt ingevoerd, maakt het script een geheim yaml-bestand, zodat u het uitvoeren.

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Maak je omsagent daemon-set door uit te voeren```kubectl create -f omsagentsecret.yaml```
    4. Voer het volgende uit om het volgende te controleren:

        ```
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        Output moet lijken op:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Maak je omsagent daemon-set door uit te voeren```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Controleer of de Logboekanalyse-agent DaemonSet wordt uitgevoerd, vergelijkbaar met het volgende:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Als u de agent wilt installeren op het werknemersknooppunt waarop Windows wordt uitgevoerd, voert u de stappen in de sectie [installatie en configureert u Windows-containerhosts.](#install-and-configure-windows-container-hosts)

#### <a name="use-helm-to-deploy-log-analytics-agent-on-linux-kubernetes"></a>Gebruik Helm om Log Analytics-agent te implementeren op Linux Kubernetes

Voer de volgende stappen uit om het roer te gebruiken om Log Analytics-agent te implementeren op uw Linux Kubernetes-omgeving.

1. Maak je omsagent daemon-set door uit te voeren```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
2. De resultaten lijken op het volgende:

    ```
    NAME:   omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED

    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     3s

    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         3s
    ```

3. U de status van de omsagent controleren door uit te voeren: ```helm status "omsagent"``` en de uitvoer ziet er als volgt uit:

    ```
    keiko@k8s-master-3814F33-0:~$ helm status omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED
 
    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     17m
 
    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         17m
    ```
   
    Voor meer informatie u terecht op [Container Solution Helm Chart](https://aka.ms/omscontainerhelm).

### <a name="install-and-configure-windows-container-hosts"></a>Windows-containerhosts installeren en configureren

Gebruik de informatie in de sectie om Windows-containerhosts te installeren en te configureren.

#### <a name="preparation-before-installing-windows-agents"></a>Voorbereiding voor het installeren van Windows-agents

Voordat u agents installeert op computers waarop Windows wordt uitgevoerd, moet u de Docker-service configureren. Met de configuratie kan de Windows-agent of de azure monitor-extensie voor virtuele machine de Docker TCP-socket gebruiken, zodat de agents op afstand toegang hebben tot de Docker-daemon en gegevens kunnen vastleggen voor bewaking.

##### <a name="to-configure-the-docker-service"></a>De Docker-service configureren  

Voer de volgende PowerShell-opdrachten uit om TCP-pipe en benoemde pipe voor Windows Server in te schakelen:

```
Stop-Service docker
dockerd --unregister-service
dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
Start-Service docker
```

Zie [Docker Engine op Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon)voor meer informatie over de docker daemon-configuratie die wordt gebruikt met Windows-containers.

#### <a name="install-windows-agents"></a>Windows-agents installeren

Als u Windows- en Hyper-V-containerbewaking wilt inschakelen, installeert u de Microsoft Monitoring Agent (MMA) op Windows-computers die containerhosts zijn. Zie [Windows-computers verbinden met Azure Monitor](../../azure-monitor/platform/agent-windows.md)voor computers met Windows in uw on-premises omgeving. Voor virtuele machines die in Azure worden uitgevoerd, verbindt u ze met Azure Monitor met behulp van de [extensie virtuele machine.](../../azure-monitor/learn/quick-collect-azurevm.md)

U Windows-containers controleren die op Service Fabric worden uitgevoerd. Momenteel worden echter alleen [virtuele machines die in Azure worden uitgevoerd](../../azure-monitor/learn/quick-collect-azurevm.md) en computers met Windows in uw [on-premises omgeving](../../azure-monitor/platform/agent-windows.md) momenteel ondersteund voor Service Fabric.

U controleren of de oplossing containerbewaking correct is ingesteld voor Windows. Als u wilt controleren of het beheerpakket goed is gedownload, zoekt u *naar ContainerManagement.xxx.* De bestanden moeten zich in de map C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs bevinden.

## <a name="solution-components"></a>Oplossingsonderdelen

Navigeer vanuit de Azure-portal naar de *galerie met oplossingen* en voeg de oplossing voor **containerbewaking**toe. Als u Windows-agents gebruikt, wordt het volgende beheerpakket op elke computer geïnstalleerd met een agent wanneer u deze oplossing toevoegt. Er is geen configuratie of onderhoud vereist voor het beheerpakket.

- *ContainerManagement.xxx* geïnstalleerd in C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs

## <a name="container-data-collection-details"></a>Details van het verzamelen van containergegevens

De containermonitoringoplossing verzamelt verschillende prestatiestatistieken en registreert gegevens van containerhosts en containers met behulp van agents die u inschakelt.

Gegevens worden elke drie minuten verzameld door de volgende agenttypen.

- [Log Analytics-agent voor Linux](../../azure-monitor/learn/quick-collect-linux-computer.md)
- [Windows-agent](../../azure-monitor/platform/agent-windows.md)
- [VM-extensie log-analyse](../../azure-monitor/learn/quick-collect-azurevm.md)

### <a name="container-records"></a>Containerrecords

In de volgende tabel worden voorbeelden weergegeven van records die zijn verzameld door de oplossing Containermonitoring en de gegevenstypen die worden weergegeven in de zoekresultaten van logboeken.

| Gegevenstype | Gegevenstype in Logboekzoeken | Velden |
| --- | --- | --- |
| Prestaties voor hosts en containers | `Perf` | Computer, objectnaam, contranaam &#40;%Processortijd, Schijf leest MB, Disk Writes MB, Geheugengebruik MB, Netwerk ontvangen Bytes, Network Send Bytes, Processor Usage sec, Network&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Containervoorraad | `ContainerInventory` | Timegenerated, Computer, containernaam, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, Environmentvar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Containerafbeeldingsvoorraad | `ContainerImageInventory` | TimeGenerated, Computer, Image, ImageTag, ImageSize, VirtualSize, Running, Paused, Stopped, Failed, SourceSystem, ImageID, TotalContainer |
| Containerlogboek | `ContainerLog` | TimeGenerated, Computer, image ID, containernaam, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Containerservicelogboek | `ContainerServiceLog`  | TimeGenerated, Computer, TimeOfCommand, Image, Command, SourceSystem, ContainerID |
| Voorraad containerknooppunt | `ContainerNodeInventory_CL`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Kubernetes-voorraad | `KubePodInventory_CL` | TimeGenerated, Computer, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, SourceSystem |
| Containerproces | `ContainerProcess_CL` | TimeGenerated, Computer, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Kubernetes-gebeurtenissen | `KubeEvents_CL` | TimeGenerated, Computer, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s, SourceComponent_s, SourceSystem, Message |

Labels die zijn toegevoegd aan *PodLabel-gegevenstypen* zijn uw eigen aangepaste labels. De bijgesloten PodLabel-labels in de tabel zijn voorbeelden. Dus, `PodLabel_deployment_s` `PodLabel_deploymentconfig_s`, `PodLabel_docker_registry_s` , zal verschillen in de gegevens `PodLabel_yourlabel_s`set van uw omgeving en generiek lijken .

## <a name="monitor-containers"></a>Containers bewaken
Nadat u de oplossing hebt ingeschakeld in de Azure-portal, wordt op de tegel **Containers** beknopte informatie weergegeven over uw containerhosts en de containers die in hosts worden uitgevoerd.

![Containers tegel](./media/containers/containers-title.png)

De tegel geeft een overzicht van het aantal containers dat u in de omgeving hebt en of deze zijn mislukt, uitgevoerd of gestopt.

### <a name="using-the-containers-dashboard"></a>Het dashboard Containers gebruiken

Klik op de tegel **Containers.** Vanaf daar zie je weergaven georganiseerd door:

- **Containergebeurtenissen** - Toont containerstatus en computers met mislukte containers.
- **Containerlogboeken** - Toont een grafiek met containerlogboekbestanden die in de loop van de tijd zijn gegenereerd en een lijst met computers met het hoogste aantal logboekbestanden.
- **Kubernetes-gebeurtenissen** - Toont een grafiek met Kubernetes-gebeurtenissen die in de loop van de tijd zijn gegenereerd en een lijst met de redenen waarom pods de gebeurtenissen hebben gegenereerd. *Deze gegevensset wordt alleen gebruikt in Linux-omgevingen.*
- **Kubernetes Namespace Inventory** - Toont het aantal naamruimten en pods en toont hun hiërarchie. *Deze gegevensset wordt alleen gebruikt in Linux-omgevingen.*
- **Containerknooppuntvoorraad** - Geeft het aantal orchestration-typen weer dat wordt gebruikt op containerknooppunten/hosts. De computerknooppunten/hosts worden ook vermeld op het aantal containers. *Deze gegevensset wordt alleen gebruikt in Linux-omgevingen.*
- **Containerafbeeldingen inventaris** - Toont het totale aantal gebruikte containerafbeeldingen en het aantal afbeeldingstypen. Het aantal afbeeldingen wordt ook vermeld door de afbeeldingstag.
- **Containersstatus** : hiermee wordt het totale aantal containerknooppunten/hostcomputers weergegeven waarop containers zijn uitgevoerd. Computers worden ook vermeld door het aantal lopende hosts.
- **Containerproces** - Toont een lijndiagram van containerprocessen die in de loop van de tijd worden uitgevoerd. Containers worden ook vermeld door het uitvoeren van command/process binnen containers. *Deze gegevensset wordt alleen gebruikt in Linux-omgevingen.*
- **Container CPU-prestaties** - Toont een lijndiagram van het gemiddelde CPU-gebruik in de loop van de tijd voor computerknooppunten/hosts. Vermeldt ook de computerknooppunten/hosts op basis van het gemiddelde CPU-gebruik.
- **Prestaties van containergeheugen** - Toont een lijndiagram met geheugengebruik in de loop van de tijd. Vermeldt ook het gebruik van het computergeheugen op basis van de naam van de instantie.
- **Computerprestaties** - Toont lijndiagrammen van het percentage CPU-prestaties in de loop van de tijd, het percentage geheugengebruik in de loop van de tijd en megabytes aan vrije schijfruimte in de loop van de tijd. U over elke regel in een grafiek zweven om meer details weer te geven.

Elk gebied van het dashboard is een visuele weergave van een zoekopdracht die wordt uitgevoerd op verzamelde gegevens.

![Dashboard containers](./media/containers/containers-dash01.png)

![Dashboard containers](./media/containers/containers-dash02.png)

Klik in het gebied **Containerstatus** op het bovenste gebied, zoals hieronder wordt weergegeven.

![Status containers](./media/containers/containers-status.png)

Log Analytics wordt geopend en geeft informatie weer over de status van uw containers.

![Logboekanalyse voor containers](./media/containers/containers-log-search.png)

Vanaf hier u de zoekopdracht bewerken om deze te wijzigen om de specifieke informatie te vinden waarin u geïnteresseerd bent. Zie [Logboekquery's in Azure Monitor](../log-query/log-query-overview.md)voor meer informatie over logboekquery's.

## <a name="troubleshoot-by-finding-a-failed-container"></a>Problemen oplossen door een mislukte container te zoeken

Log Analytics markeert een container als Mislukt als **deze** is afgesloten met een niet-nulexitcode. U een overzicht zien van de fouten en fouten in de omgeving in het gebied **Mislukte containers.**

### <a name="to-find-failed-containers"></a>Mislukte containers zoeken

1. Klik op het gebied **Containerstatus.**  
   ![containersstatus](./media/containers/containers-status.png)
2. Log Analytics opent en geeft de status van uw containers weer, vergelijkbaar met de volgende.  
   ![containersstatus](./media/containers/containers-log-search.png)
3. Vouw de regel Mislukt uit en klik op + om de criteria aan de query toe te voegen. Geef vervolgens commentaar op de regel Samenvatten in de query.
   ![mislukte containers](./media/containers/containers-state-failed-select.png)  
1. Voer de query uit en vouw vervolgens een regel uit in de resultaten om de afbeeldings-id weer te geven.  
   ![mislukte containers](./media/containers/containers-state-failed.png)  
1. Typ het volgende in de logboekquery. `ContainerImageInventory | where ImageID == <ImageID>`om details over de afbeelding te zien, zoals de grootte van de afbeelding en het aantal gestopte en mislukte afbeeldingen.  
   ![mislukte containers](./media/containers/containers-failed04.png)

## <a name="query-logs-for-container-data"></a>Querylogboeken voor containergegevens

Wanneer u een specifieke fout oplost, kan deze helpen om te zien waar deze zich in uw omgeving voordoet. Met de volgende logboektypen u query's maken om de gewenste informatie terug te sturen.

- **ContainerImageInventory** : gebruik dit type wanneer u informatie probeert te vinden die is georganiseerd op basis van afbeeldingen en afbeeldingsgegevens zoals afbeeldings-id's of -formaten wilt weergeven.
- **ContainerInventory** : gebruik dit type wanneer u informatie wilt over de locatie van de container, wat de naam ervan is en welke afbeeldingen ze uitvoeren.
- **ContainerLog** : gebruik dit type wanneer u specifieke foutlogboekgegevens en vermeldingen wilt vinden.
- **ContainerNodeInventory_CL**  Gebruik dit type wanneer u de informatie wilt over host/node waar containers zich bevinden. Het biedt u Docker-versie, orkestratietype, opslag en netwerkinformatie.
- **ContainerProcess_CL** Gebruik dit type om snel het proces in de container te zien.
- **ContainerServiceLog** : gebruik dit type wanneer u controlespoorgegevens voor de Docker-daemon probeert te vinden, zoals opdrachten starten, stoppen, verwijderen of trekken.
- **KubeEvents_CL**  Gebruik dit type om de Kubernetes-gebeurtenissen te bekijken.
- **KubePodInventory_CL**  Gebruik dit type wanneer u de clusterhiërarchiegegevens wilt begrijpen.


### <a name="to-query-logs-for-container-data"></a>Logboeken voor containergegevens opvragen

* Kies een afbeelding waarvan u weet dat deze onlangs is mislukt en zoek de foutlogboeken ervoor. Begin met het zoeken naar een containernaam waarop die afbeelding wordt uitgevoerd met een **containervoorraadzoekopdracht.** Zoek bijvoorbeeld naar`ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    ![Zoek naar Ubuntu-containers](./media/containers/search-ubuntu.png)

  Vouw elke rij in de resultaten uit om details voor die container weer te geven.

## <a name="example-log-queries"></a>Voorbeeldlogboekquery's

Het is vaak handig om query's te bouwen die beginnen met een voorbeeld of twee en deze vervolgens aan te passen aan uw omgeving. Als uitgangspunt u experimenteren met het gebied **Voorbeeldquery's** om u te helpen meer geavanceerde query's te maken.

![Containersquery's](./media/containers/containers-queries.png)

## <a name="saving-log-queries"></a>Logboekquery's opslaan

Het opslaan van query's is een standaardfunctie in Azure Monitor. Door ze op te slaan, heb je die je nuttig hebt gevonden voor toekomstig gebruik.

Nadat u een query hebt gemaakt die u nuttig vindt, slaat u deze op door boven aan de pagina Logboekzoeken op **Favorieten** te klikken. Vervolgens u het later eenvoudig openen vanaf de pagina **Mijn dashboard.**

## <a name="next-steps"></a>Volgende stappen

[Querylogboeken](../log-query/log-query-overview.md) om gedetailleerde containergegevensrecords weer te geven.
