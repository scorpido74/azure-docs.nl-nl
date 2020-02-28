---
title: Container bewakings oplossing in Azure Monitor | Microsoft Docs
description: Met de container bewakings oplossing in Azure Monitor kunt u uw docker-en Windows-container-hosts op één locatie weer geven en beheren.
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/22/2019
ms.openlocfilehash: 171f897f6e110e8f759281c139addab477ecede3
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77664691"
---
# <a name="container-monitoring-solution-in-azure-monitor"></a>Container bewakings oplossing in Azure Monitor

![Symbool van containers](./media/containers/containers-symbol.png)

In dit artikel wordt beschreven hoe u de container bewakings oplossing kunt instellen en gebruiken in Azure Monitor, waarmee u uw docker-en Windows-container-hosts op één locatie kunt weer geven en beheren. Docker is een software-virtualisatie-systeem gebruikt voor het maken van containers voor het automatiseren van software-implementatie naar de IT-infrastructuur.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

De oplossing laat zien welke containers worden uitgevoerd, welke containerinstallatiekopie deze nu worden uitgevoerd en waarop de containers worden uitgevoerd. Hier vindt u gedetailleerde controle-informatie met opdrachten die worden gebruikt met containers. En u kunt containers oplossen weer te geven en gecentraliseerde logboeken zoeken zonder dat om Docker of Windows-hosts op afstand weer te geven. Hier vindt u containers die mogelijk veel ruis veroorzaken en in beslag nemen veel bronnen op een host. Daarnaast vindt u gecentraliseerde CPU, geheugen, opslag en gebruiks- en netwerkgegevens voor containers. Op computers waarop Windows wordt uitgevoerd, kunt u centraliseren en vergelijken van Logboeken van Windows Server, Hyper-V en Docker-containers. De oplossing biedt ondersteuning voor de volgende containerorchestrators:

- Docker Swarm
- DC/OS
- Kubernetes
- Infrastructuur van service
- Red Hat OpenShift

Als u containers hebt geïmplementeerd in [Azure service Fabric](../../service-fabric/service-fabric-overview.md), raden we u aan om zowel de [service Fabric oplossing](../../service-fabric/service-fabric-diagnostics-oms-setup.md) als deze oplossing in te scha kelen voor de bewaking van cluster gebeurtenissen. Voordat u de Service Fabric-oplossing inschakelt, raadpleegt u [de service Fabric-oplossing](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md) om te begrijpen wat deze biedt en hoe u deze kunt gebruiken.

Zie de [Azure Kubernetes-service bewaken](../../azure-monitor/insights/container-insights-overview.md)als u geïnteresseerd bent in het bewaken van de prestaties van uw workloads die zijn geïmplementeerd in Kubernetes-omgevingen die worden gehost op Azure Kubernetes service (AKS). De container bewakings oplossing biedt geen ondersteuning voor het bewaken van het platform.  

In het volgende diagram ziet u de relaties tussen verschillende container-hosts en-agents met Azure Monitor.

![Diagram van containers](./media/containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Systeemvereisten en ondersteunde platforms

Voordat u begint, lees de volgende informatie om te controleren of u voldoet aan de vereisten.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Bewakingsoplossing voor containers, ondersteuning voor Docker-Orchestrator en OS-platform

De volgende tabel bevat een overzicht van de ondersteuning van docker-indeling en besturingssysteem controle van de container inventaris, prestaties en logboeken met Azure Monitor.   

| | ACS | Linux | Windows | Container<br>Inventaris | Installatiekopie<br>Inventaris | Knooppunt<br>Inventaris | Container<br>Prestaties | Container<br>Gebeurtenis | Gebeurtenis<br>Logboek | Container<br>Logboek |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosphere<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Swarm | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Service<br>Fabric | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Red Hat openen<br>Shift | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>(zelfstandig) | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Linux-server<br>(zelfstandig) | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |

### <a name="docker-versions-supported-on-linux"></a>Docker-versies ondersteund in Linux

- Docker 1.11-1.13
- Docker CE en EE v17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>x64 Linux-distributies ondersteund als hosts van de container

- Ubuntu 14.04 LTS en 16.04 LTS
- CoreOS(stable)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- openSUSE 42.2 LEAP
- CentOS 7.2 en 7.3
- SLES 12
- RHEL 7.2 en 7.3
- Red Hat OpenShift Containerplatform (OCP) 3.4 en 3.5
- ACS-Mesosphere DC/OS 1.7.3-1.8.8
- ACS-Kubernetes 1.4.5-1.6
    - Kubernetes-gebeurtenissen, inventaris van Kubernetes en processen van de container worden alleen ondersteund met versie 1.4.1-45 en hoger van de Log Analytics-agent voor Linux
- ACS Docker Swarm

[!INCLUDE [log-analytics-agent-note.md](../../../includes/log-analytics-agent-note.md)] 

### <a name="supported-windows-operating-system"></a>Ondersteunde Windows-besturingssysteem

- Windows Server 2016
- Windows 10 Anniversary Edition (Professional of Enterprise)

### <a name="docker-versions-supported-on-windows"></a>Docker-versies ondersteund op Windows

- Docker 1.12 en 1.13
- Docker 17.03.0 en hoger

## <a name="installing-and-configuring-the-solution"></a>Installeren en configureren van de oplossing

Gebruik de volgende informatie om de oplossing te installeren en configureren.

1. Voeg de container bewakings oplossing toe aan uw Log Analytics-werk ruimte vanuit [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) of door gebruik te maken van het proces dat wordt beschreven in [bewakings oplossingen toevoegen van de Oplossingengalerie](../../azure-monitor/insights/solutions.md).

2. Installeer en Docker gebruiken met een Log Analytics-agent. Op basis van uw besturingssysteem en de Docker-orchestrator, kunt u de volgende methoden gebruiken om de agent te configureren.
   - Voor zelfstandige hosts:
     - Op ondersteunde Linux-besturings systemen installeert en voert u docker en installeert en configureert u vervolgens de [log Analytics-agent voor Linux](../../azure-monitor/learn/quick-collect-linux-computer.md).  
     - In CoreOS, kunt u de Log Analytics-agent voor Linux niet uitvoeren. In plaats daarvan kunt u een beperkte versie van de Log Analytics-agent voor Linux uitvoeren. Bekijk de Linux-container hosts, waaronder CoreOS of Azure Government Linux-container hosts, inclusief CoreOS als u met containers in Azure Government Cloud werkt.
     - Installeer op Windows Server 2016 en Windows 10 de docker-engine en de client vervolgens verbinding maken met een agent om informatie te verzamelen en deze naar Azure Monitor te verzenden. Controleer de [installatie en configuratie van Windows-container-hosts](#install-and-configure-windows-container-hosts) als u een Windows-omgeving hebt.
   - Voor het indelen van Docker-meerdere host:
     - Als u een Red Hat open Shift-omgeving hebt, raadpleegt u Configure a Log Analytics agent for Red Hat open SHIFT.
     - Als u een Kubernetes-cluster met behulp van de Azure Container Service:
       - Bekijk [een log Analytics Linux-agent configureren voor Kubernetes](#configure-a-log-analytics-linux-agent-for-kubernetes).
       - Bekijk [een log Analytics Windows-agent configureren voor Kubernetes](#configure-a-log-analytics-windows-agent-for-kubernetes).
       - Raadpleeg use helm om Log Analytics-agent te implementeren op Linux Kubernetes.
     - Als u een Azure Container Service DC/OS-cluster hebt, kunt u meer informatie vinden op [een Azure container service DC/OS-cluster met Azure monitor bewaken](../../container-service/dcos-swarm/container-service-monitoring-oms.md).
     - Als u beschikt over een docker Swarm-omgeving, kunt u meer informatie vinden op een Log Analytics agent configureren voor docker Swarm.
     - Als u een Service Fabric cluster hebt, kunt u meer informatie vinden op [containers controleren met Azure monitor](../../service-fabric/service-fabric-diagnostics-oms-containers.md).

Raadpleeg de [docker-engine in Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) -artikel voor meer informatie over het installeren en configureren van uw docker-engines op computers met Windows.

> [!IMPORTANT]
> Docker moet worden uitgevoerd **voordat** u de [log Analytics-agent voor Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) installeert op de container-hosts. Als u de agent al hebt geïnstalleerd voordat u Docker installeert, moet u de Log Analytics-agent voor Linux opnieuw installeren. Zie de [docker-website](https://www.docker.com)voor meer informatie over docker.

### <a name="install-and-configure-linux-container-hosts"></a>Installeer en configureer de hosts van de Linux-container

Nadat u Docker hebt geïnstalleerd, gebruikt u de volgende instellingen voor uw containerhost configureren van de agent voor gebruik met Docker. U moet eerst uw Log Analytics-werkruimte-ID en sleutel, die u in de Azure-portal vinden kunt. Klik in uw werk ruimte op **Quick Start** > **computers** om uw **werk ruimte-id** en **primaire sleutel**weer te geven.  Kopieer en plak beide in uw favoriete editor.

**Voor alle Linux-container hosts, met uitzonde ring van CoreOS:**

- Zie [log Analytics agent Overview](../../azure-monitor/platform/log-analytics-agent.md)voor meer informatie en de stappen voor het installeren van de log Analytics-agent voor Linux.

**Voor alle Linux-container hosts, waaronder CoreOS:**

Start de container die u wilt bewaken. Wijzig en gebruik het volgende voorbeeld:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**Voor alle Azure Government Linux-container hosts, waaronder CoreOS:**

Start de container die u wilt bewaken. Wijzig en gebruik het volgende voorbeeld:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Overschakelen van het gebruik van een geïnstalleerde Linux-agent naar een in een container**

Als u eerder hebt gebruikt van de agent rechtstreeks-geïnstalleerd en u wilt gebruiken in plaats daarvan een agent die wordt uitgevoerd in een container, moet u eerst de Log Analytics-agent voor Linux te verwijderen. Zie [de log Analytics-agent voor Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) verwijderen voor meer informatie over het verwijderen van de agent.  

#### <a name="configure-a-log-analytics-agent-for-docker-swarm"></a>Configureren van een Log Analytics-agent voor Docker Swarm

U kunt de Log Analytics-agent uitvoeren als een globale service op het Docker Swarm. Gebruik de volgende informatie om een Log Analytics-agent-service te maken. U moet uw Log Analytics-werkruimte-ID en primaire sleutel opgeven.

- Voer de volgende op het hoofdknooppunt.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Beveiligde geheimen voor Docker Swarm

Gebruik de volgende informatie om te maken van uw geheime informatie voor Docker Swarm, zodra het geheim voor werkruimte-ID en primaire sleutel is gemaakt.

1. Voer de volgende op het hoofdknooppunt.

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. Controleer of dat geheimen juist zijn gemaakt.

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. Voer de volgende opdracht om te koppelen van de geheimen in de beperkte Log Analytics-agent.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-a-log-analytics-agent-for-red-hat-openshift"></a>Een Log Analytics-agent voor Red Hat OpenShift configureren

Er zijn drie manieren om toe te voegen van de Log Analytics-agent voor Red Hat OpenShift om te beginnen met het verzamelen van bewakingsgegevens van de container.

* [De log Analytics-agent voor Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) rechtstreeks op elk open Shift-knoop punt installeren  
* [Log Analytics VM-extensie inschakelen](../../azure-monitor/learn/quick-collect-azurevm.md) op elk open Shift-knoop punt dat zich bevindt in azure  
* De Log Analytics-agent installeren als een open Shift-daemon-set  

In deze sectie behandelen we de stappen die nodig zijn voor het installeren van de Log Analytics-agent als een OpenShift-daemon-set.  

1. Meld u aan bij het knoop punt open Shift Master en kopieer het yaml [-bestand OCP-omsagent. yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) van github naar het hoofd knooppunt en wijzig de waarde met uw log Analytics werk ruimte-id en met uw primaire sleutel.
2. Voer de volgende opdrachten uit om een project te maken voor Azure Monitor en het gebruikers account in te stellen.

    ```
    oc adm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Voor het implementeren van de daemon-set, voert u het volgende uit:

    `oc create -f ocp-omsagent.yaml`

4. Controleer of dat deze is geconfigureerd en naar behoren werkt, typt u het volgende:

    `oc describe daemonset omsagent`  

    en de uitvoer moet eruitzien als:

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

Als u gebruiken van geheimen wilt voor het beveiligen van uw Log Analytics-werkruimte-ID en primaire sleutel bij het gebruik van de Log Analytics-agent-daemon-set yaml-bestand, moet u de volgende stappen uitvoeren.

1. Meld u aan bij het knoop punt openshift Master en kopieer het yaml [-bestand OCP-DS-omsagent. yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) en het script voor het genereren van een geheim [OCP-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) uit github.  Met dit script genereert de geheimen yaml-bestand voor Log Analytics-werkruimte-ID en primaire sleutel voor het beveiligen van uw informatie secrete.  
2. Voer de volgende opdrachten uit om een project te maken voor Azure Monitor en het gebruikers account in te stellen. Het geheim genereren van het script vraagt om de Log Analytics werk ruimte-ID `<WSID>` en de primaire-sleutel `<KEY>` en na voltooiing wordt het bestand OCP-Secret. yaml gemaakt.  

    ```
    oc adm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Het geheime bestand door het uitvoeren van de volgende implementeren:

    `oc create -f ocp-secret.yaml`

4. Implementatie controleren door het uitvoeren van de volgende:

    `oc describe secret omsagent-secret`  

    en de uitvoer moet eruitzien als:  

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

5. Implementeer de Log Analytics-agent-daemon-set yaml-bestand door het uitvoeren van de volgende:

    `oc create -f ocp-ds-omsagent.yaml`  

6. Implementatie controleren door het uitvoeren van de volgende:

    `oc describe ds oms`

    en de uitvoer moet eruitzien als:

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

#### <a name="configure-a-log-analytics-linux-agent-for-kubernetes"></a>Configureren van een Log Analytics Linux-agent voor Kubernetes

Voor Kubernetes, kunt u een script gebruiken om de geheimen yaml-bestand voor uw werkruimte-ID en primaire sleutel voor het installeren van de Log Analytics-agent voor Linux te genereren. Op de [log Analytics pagina docker Kubernetes github](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) zijn bestanden die u kunt gebruiken met of zonder uw geheime gegevens.

- De standaard Log Analytics-agent voor Linux DaemonSet heeft geen geheime informatie (omsagent.yaml)
- De Log Analytics-agent voor Linux DaemonSet yaml-bestand maakt gebruik van geheime informatie (omsagent-ds-secrets.yaml) met behulp van scripts geheim genereren om de geheimen (omsagentsecret.yaml) yaml-bestand te genereren.

U kunt omsagent DaemonSets maken met of zonder geheimen.

**Standaard OMSagent Daemonset yaml-bestand zonder geheimen**

- Voor het standaard Log Analytics agent daemon yaml-bestand, vervangt u de `<WSID>` en `<KEY>` naar uw WSID en sleutel. Kopieer het bestand naar het hoofdknooppunt en voert de volgende opdracht uit:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Standaard OMSagent Daemonset yaml-bestand met geheimen**

1. Gebruik van Log Analytics-agent DaemonSet met behulp van geheime informatie, maken de geheimen eerst.
    1. Kopieer het script en de geheime sjabloonbestand en zorg ervoor dat ze zich in dezelfde map.
        - Geheim script - geheim gen.sh genereren
        - geheime sjabloon - geheim template.yaml
    2. Voer het script, zoals in het volgende voorbeeld. Het script vraagt om de Log Analytics-werkruimte-ID en primaire sleutel en nadat u deze invoert, het script maakt u een geheim yaml-bestand, zodat u deze kunt uitvoeren.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. Maak de schil geheimen door het uitvoeren van de volgende:
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. Om te controleren, voert u het volgende uit:

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        Uitvoer moet eruitzien als:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        Uitvoer moet eruitzien als:

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

    5. Uw omsagent-daemon maken-set door ```sudo kubectl create -f omsagent-ds-secrets.yaml``` uit te voeren

2. Controleer of de Log Analytics-agent DaemonSet is uitgevoerd, die vergelijkbaar is met het volgende:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```

Bij Kubernetes wordt een script te gebruiken voor het genereren van de geheimen yaml-bestand voor de werkruimte-ID en primaire sleutel voor de Log Analytics-agent voor Linux. Gebruik de volgende voorbeeld informatie met het [omsagent yaml-bestand](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) om uw geheime gegevens te beveiligen.

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

#### <a name="configure-a-log-analytics-windows-agent-for-kubernetes"></a>Configureren van een Log Analytics-Windows-agent voor Kubernetes

Voor Windows Kubernetes, kunt u een script gebruiken voor het genereren van de geheimen yaml-bestand voor uw werkruimte-ID en primaire sleutel voor het installeren van de Log Analytics-agent. Op de [log Analytics pagina docker Kubernetes github](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) zijn bestanden die u kunt gebruiken met uw geheime gegevens.  U moet de Log Analytics-agent voor de hoofd- en agentknooppunten afzonderlijk installeren.  

1. Gebruik van Log Analytics-agent DaemonSet met behulp van geheime informatie op de Hoofddoelserver knooppunt, meld u aan en maakt u eerst de geheimen.
    1. Kopieer het script en de geheime sjabloonbestand en zorg ervoor dat ze zich in dezelfde map.
        - Geheim script - geheim gen.sh genereren
        - geheime sjabloon - geheim template.yaml

    2. Voer het script, zoals in het volgende voorbeeld. Het script vraagt om de Log Analytics-werkruimte-ID en primaire sleutel en nadat u deze invoert, het script maakt u een geheim yaml-bestand, zodat u deze kunt uitvoeren.

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Uw omsagent-daemon maken-set door ```kubectl create -f omsagentsecret.yaml``` uit te voeren
    4. Als u wilt controleren, voert u het volgende uit:

        ```
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        Uitvoer moet eruitzien als:

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

    5. Uw omsagent-daemon maken-set door ```kubectl create -f ws-omsagent-de-secrets.yaml``` uit te voeren

2. Controleer of de Log Analytics-agent DaemonSet is uitgevoerd, die vergelijkbaar is met het volgende:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Volg de stappen in de sectie [Windows-container-hosts installeren en configureren](#install-and-configure-windows-container-hosts)als u de agent wilt installeren op het worker-knoop punt waarop Windows wordt uitgevoerd.

#### <a name="use-helm-to-deploy-log-analytics-agent-on-linux-kubernetes"></a>Helm gebruiken voor het implementeren van Log Analytics-agent op Linux Kubernetes

Voor het gebruik van helm om Log Analytics-agent op uw Linux Kubernetes-omgeving te implementeren, moet u de volgende stappen uitvoeren.

1. Uw omsagent-daemon maken-set door ```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms``` uit te voeren
2. De resultaten ziet eruit als het volgende:

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

3. U kunt de status van de omsagent controleren door uit te voeren: ```helm status "omsagent"``` en de uitvoer ziet er ongeveer als volgt uit:

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
   
    Ga voor meer informatie naar [container Solution helm Chart](https://aka.ms/omscontainerhelm).

### <a name="install-and-configure-windows-container-hosts"></a>Installeer en configureer de hosts van de Windows-container

Gebruik de informatie in de sectie installeren en configureren van de hosts van de Windows-container.

#### <a name="preparation-before-installing-windows-agents"></a>Voorbereiding voor de installatie van Windows-agents

Voordat u agents op computers waarop Windows wordt uitgevoerd installeren, moet u de Docker-service configureren. Met de configuratie kan de Windows-agent of de Azure Monitor virtuele-machine-extensie de docker TCP-socket gebruiken, zodat de agents extern toegang hebben tot de docker-daemon en gegevens kunnen vastleggen voor bewaking.

##### <a name="to-configure-the-docker-service"></a>De docker-service configureren  

Voer de volgende Power shell-opdrachten uit om TCP-pipe en named pipe voor Windows Server in te scha kelen:

```
Stop-Service docker
dockerd --unregister-service
dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
Start-Service docker
```

Zie [docker-engine in Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon)voor meer informatie over de configuratie van de docker-daemon die wordt gebruikt met Windows-containers.

#### <a name="install-windows-agents"></a>Windows-agents installeren

Als u Windows- en Hyper-V-containers bewaken, installeert u Microsoft Monitoring Agent (MMA) op Windows-computers die containerhosts zijn. Zie [Windows-computers verbinden met Azure monitor](../../azure-monitor/platform/agent-windows.md)voor computers met Windows in uw on-premises omgeving. Voor virtuele machines die worden uitgevoerd in azure verbindt u deze met Azure Monitor met behulp van de extensie van de [virtuele machine](../../azure-monitor/learn/quick-collect-azurevm.md).

U kunt Windows-containers die worden uitgevoerd in Service Fabric bewaken. Alleen [virtuele machines die worden uitgevoerd in azure](../../azure-monitor/learn/quick-collect-azurevm.md) en [computers met Windows in uw on-premises omgeving](../../azure-monitor/platform/agent-windows.md) worden momenteel ondersteund voor service Fabric.

U kunt controleren of de Container Monitoring solution juist is ingesteld voor Windows. Zoek naar *ContainerManagement.xxx*om te controleren of de Management Pack correct is gedownload. De bestanden moeten zich in de map C:\Program Files\Microsoft Monitoring Agent\Agent\Health State\Management servicepacks.

## <a name="solution-components"></a>Oplossingsonderdelen

Ga vanuit het Azure Portal naar de *Oplossingengalerie* en voeg de **container bewakings oplossing**toe. Als u van Windows-agents gebruikmaakt, is klikt u vervolgens de volgende managementpack geïnstalleerd op elke computer met een agent wanneer u deze oplossing toevoegt. Er zijn geen configuraties of onderhoud is vereist voor het managementpack.

- *ContainerManagement.xxx* geïnstalleerd in C:\Program Files\Microsoft monitoring Agent\Agent\Health service State\Management packs

## <a name="container-data-collection-details"></a>Details van container gegevens verzamelen

De oplossing Container Monitoring verzamelt verschillende metrische gegevens en logboekbestanden prestatiegegevens van containerhosts en -containers met agents die u inschakelt.

Gegevens worden elke drie minuten worden verzameld door de volgende typen van de agent.

- [Log Analytics-agent voor Linux](../../azure-monitor/learn/quick-collect-linux-computer.md)
- [Windows-agent](../../azure-monitor/platform/agent-windows.md)
- [VM-extensie Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md)

### <a name="container-records"></a>Container-records

De volgende tabel ziet u voorbeelden van records die zijn verzameld door de Container Monitoring-oplossing en de gegevenstypen die worden weergegeven in de resultaten van de logboekzoekopdracht.

| Gegevenstype | Het gegevenstype in zoeken in Logboeken | Velden |
| --- | --- | --- |
| Prestaties voor hosts en -containers | `Perf` | Computer, ObjectName, CounterName &#40;% processortijd, schijf leest MB, schijf schrijft MB, gebruik van geheugen (MB), netwerk ontvangen Bytes, netwerk verzenden Bytes, Processor gebruik seconde, netwerk&#41;, CounterValue, TimeGenerated, itempad, SourceSystem |
| Container-inventaris | `ContainerInventory` | TimeGenerated, de Computer, de containernaam, ContainerHostname, afbeelding, ImageTag, ContainerState, ExitCode, EnvironmentVar, opdracht, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Inventarisatie van container-installatiekopie | `ContainerImageInventory` | TimeGenerated, Computer, afbeelding, ImageTag, ImageSize, VirtualSize, die wordt uitgevoerd, is onderbroken, gestopt, is mislukt, SourceSystem, ImageID, TotalContainer |
| Container-logboek | `ContainerLog` | TimeGenerated, Computer, afbeeldings-ID, containernaam van de, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Container service-logboek | `ContainerServiceLog`  | TimeGenerated, Computer, TimeOfCommand, afbeelding, opdracht, SourceSystem, ContainerID |
| Inventaris van containerknooppunten | `ContainerNodeInventory_CL`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventaris van Kubernetes | `KubePodInventory_CL` | TimeGenerated, Computer, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, SourceSystem |
| Containerverwerking | `ContainerProcess_CL` | TimeGenerated, Computer, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Kubernetes-gebeurtenissen | `KubeEvents_CL` | TimeGenerated, Computer, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s, SourceComponent_s, SourceSystem, bericht |

Labels die zijn toegevoegd aan *PodLabel* -gegevens typen zijn uw eigen aangepaste labels. De toegevoegde PodLabel labels weergegeven in de tabel zijn voorbeelden. `PodLabel_deployment_s`, `PodLabel_deploymentconfig_s`is `PodLabel_docker_registry_s` echter verschillen in de gegevensverzameling van uw omgeving en lijkt het algemeen op `PodLabel_yourlabel_s`.

## <a name="monitor-containers"></a>Containers bewaken
Nadat u de oplossing hebt ingeschakeld in de Azure Portal, toont de tegel **containers** samenvattings informatie over de container-hosts en de containers die worden uitgevoerd in hosts.

![Containers-tegel](./media/containers/containers-title.png)

De tegel geeft een overzicht van hoeveel containers er in de omgeving en of ze zijn mislukt, wordt uitgevoerd of gestopt.

### <a name="using-the-containers-dashboard"></a>Met behulp van de Containers-dashboard

Klik op de tegel **containers** . Daar ziet u de weergaven die zijn geordend op:

- **Container gebeurtenissen** -container status en computers met mislukte containers weer geven.
- **Container logboeken** : toont een grafiek van de container logboek bestanden die in de loop van de tijd worden gegenereerd en een lijst met computers met het hoogste aantal logboek bestanden.
- **Kubernetes-gebeurtenissen** : toont een grafiek van Kubernetes-gebeurtenissen die zijn gegenereerd gedurende een bepaalde periode en een lijst van de redenen waarom de gebeurtenissen door peul zijn gegenereerd. *Deze gegevensset wordt alleen gebruikt in Linux-omgevingen.*
- **Kubernetes-naam ruimte inventaris** : toont het aantal naam ruimten en het gehele getal en toont de hiërarchie. *Deze gegevensset wordt alleen gebruikt in Linux-omgevingen.*
- **Container knooppunt inventaris** : toont het aantal indelings typen dat wordt gebruikt op container knooppunten/-hosts. De computer knooppunten /-hosts worden ook weergegeven op het aantal containers. *Deze gegevensset wordt alleen gebruikt in Linux-omgevingen.*
- **Inventaris van container installatie kopieën** : hier wordt het totale aantal gebruikte container installatie kopieën en het aantal afbeeldings typen weer gegeven. Het aantal afbeeldingen worden ook weergegeven met het label de installatiekopie.
- **Status van containers** : toont het totale aantal container knooppunten/hostcomputers waarop containers worden uitgevoerd. Computers worden ook weergegeven door het aantal actieve hosts.
- **Container proces** : toont een lijn diagram van container processen die gedurende een periode worden uitgevoerd. Containers worden ook vermeld door uit te voeren opdracht/proces binnen containers. *Deze gegevensset wordt alleen gebruikt in Linux-omgevingen.*
- **Prestaties van de container CPU** : toont een lijn diagram van het gemiddelde CPU-gebruik gedurende een bepaalde periode voor computer knooppunten/-hosts. Ook een lijst met de computer-knooppunten/hosts op basis van Gemiddeld CPU-gebruik.
- **Prestaties van container geheugen** : toont een lijn diagram van het geheugen gebruik gedurende een bepaalde periode. Bevat ook gebruik gebaseerd op de instantienaam van de computergeheugen.
- **Computer prestaties** : toont lijn diagrammen van het percentage CPU-prestaties gedurende een bepaalde periode, percentage van het geheugen gebruik gedurende een bepaalde periode en MB aan vrije schijf ruimte na verloop van tijd. U kunt de muisaanwijzer over een regel in een grafiek om meer details weer te geven.

Elk gebied van het dashboard is een visuele representatie van een zoekopdracht die wordt uitgevoerd op de verzamelde gegevens.

![Dashboard containers](./media/containers/containers-dash01.png)

![Dashboard containers](./media/containers/containers-dash02.png)

Klik in het gebied **container status** op het bovenste gedeelte, zoals hieronder wordt weer gegeven.

![Containersstatus van](./media/containers/containers-status.png)

Log Analytics wordt geopend, waarin informatie over de status van uw containers wordt weer gegeven.

![Log Analytics voor containers](./media/containers/containers-log-search.png)

Hier kunt kunt u de zoekopdracht om te wijzigen om de gewenste informatie vindt dat u geïnteresseerd bent in bewerken. Zie [logboek query's in azure monitor](../log-query/log-query-overview.md)voor meer informatie over logboek query's.

## <a name="troubleshoot-by-finding-a-failed-container"></a>Problemen oplossen met het vinden van een mislukte container

Log Analytics een container als **mislukt** markeert als deze is afgesloten met een afsluit code die niet gelijk is aan nul. In het gebied **mislukte containers** ziet u een overzicht van de fouten en fouten in de omgeving.

### <a name="to-find-failed-containers"></a>Mislukte containers vinden

1. Klik op het **status gebied container** .  
   status van ![containers](./media/containers/containers-status.png)
2. Log Analytics wordt geopend en de status van uw containers wordt weer gegeven, zoals in het volgende voor beeld.  
   ![status van containers](./media/containers/containers-log-search.png)
3. Vouw de fout regel uit en klik op + om de criteria aan de query toe te voegen. Vervolgens voert u een opmerking uit op de regel samenvatten in de query.
   ![mislukte containers](./media/containers/containers-state-failed-select.png)  
1. Voer de query uit en vouw vervolgens een regel uit in de resultaten om de afbeeldings-ID weer te geven.  
   ![mislukte containers](./media/containers/containers-state-failed.png)  
1. Typ het volgende in de logboek query. `ContainerImageInventory | where ImageID == <ImageID>` om details weer te geven over de afbeelding, zoals de afbeeldings grootte en het aantal gestopte en mislukte installatie kopieën.  
   ![mislukte containers](./media/containers/containers-failed04.png)

## <a name="query-logs-for-container-data"></a>Query logboeken voor container gegevens

Als u problemen met een specifieke fout, kunt u om te zien waar het optreedt in uw omgeving. De volgende typen logboeken ziet u een query om de informatie te krijgen die u wilt maken.

- **ContainerImageInventory** : gebruik dit type als u wilt zoeken naar informatie die is ingedeeld op afbeelding en om afbeeldings gegevens zoals afbeeldings-id's of-groottes weer te geven.
- **ContainerInventory** : gebruik dit type als u informatie wilt over de container locatie, wat hun namen zijn en welke installatie kopieën ze uitvoeren.
- **ContainerLog** : gebruik dit type als u specifieke informatie en vermeldingen in het fouten logboek wilt zoeken.
- **ContainerNodeInventory_CL**  Gebruik dit type als u wilt dat de informatie over de host of het knoop punt waar de containers zich bevinden. Het biedt u Docker-versie, orchestration-type, opslag en netwerkgegevens.
- **ContainerProcess_CL** Gebruik dit type om snel te zien hoe het proces wordt uitgevoerd in de container.
- **ContainerServiceLog** : gebruik dit type als u informatie wilt vinden over de audit trail voor de docker-daemon, zoals starten, stoppen, verwijderen of pull-opdrachten.
- **KubeEvents_CL**  Gebruik dit type om de Kubernetes-gebeurtenissen te bekijken.
- **KubePodInventory_CL**  Gebruik dit type als u inzicht wilt krijgen in de gegevens van de cluster hiërarchie.


### <a name="to-query-logs-for-container-data"></a>Logboeken voor container gegevens opvragen

* Kies een afbeelding waarvan u weet dat onlangs is mislukt en de foutenlogboeken zoeken. Zoek eerst de naam van een container met de installatie kopie met een **ContainerInventory** zoeken. Zoek bijvoorbeeld naar `ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    ![zoeken naar Ubuntu-containers](./media/containers/search-ubuntu.png)

  Vouw een rij in de resultaten uit om de Details voor die container weer te geven.

## <a name="example-log-queries"></a>Voorbeeld logboek query's

Vaak is het handig om te maken van query's beginnen met een voorbeeld of twee en vervolgens wijzigen om aan te passen in uw omgeving. Als uitgangs punt kunt u experimenteren met het gebied voor **voorbeeld query's** om meer geavanceerde query's te maken.

![Query's voor containers](./media/containers/containers-queries.png)

## <a name="saving-log-queries"></a>Logboek query's opslaan

Het opslaan van query's is een standaard functie in Azure Monitor. Door deze zijn opgeslagen, hebt u die u hebt gevonden nuttig bij de hand hebt voor toekomstig gebruik.

Nadat u een query hebt gemaakt die u nuttig vindt, slaat u deze op door te klikken op **Favorieten** boven aan de zoek pagina voor Logboeken. U kunt deze later eenvoudig openen vanaf de pagina **mijn dash board** .

## <a name="next-steps"></a>Volgende stappen

[Query logboeken](../log-query/log-query-overview.md) om gedetailleerde container gegevens records weer te geven.
