---
title: Verbinding maken met Microsoft Azure Stack edge-apparaat en deze beheren via de Windows Power shell-interface | Microsoft Docs
description: Hierin wordt beschreven hoe u verbinding maakt met en vervolgens Azure Stack Edge beheert via de Windows Power shell-interface.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 85e95dc4138fd638c8db9f5c98a7064153c7ef17
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181643"
---
# <a name="manage-an-azure-stack-edge-gpu-device-via-windows-powershell"></a>Een Azure Stack Edge GPU-apparaat beheren via Windows Power shell

Met Azure Stack EDGE-oplossing kunt u gegevens verwerken en via het netwerk verzenden naar Azure. In dit artikel worden enkele van de configuratie-en beheer taken voor uw Azure Stack edge-apparaat beschreven. U kunt de Azure Portal, de lokale webgebruikersinterface of de Windows Power shell-interface gebruiken om uw apparaat te beheren.

In dit artikel wordt uitgelegd hoe u verbinding kunt maken met de Power shell-interface van het apparaat en de taken die u met deze interface kunt uitvoeren. 


## <a name="connect-to-the-powershell-interface"></a>Verbinding maken met de Power shell-interface

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Een ondersteunings pakket maken

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Certificaat uploaden

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

U kunt ook IoT Edge certificaten uploaden om een beveiligde verbinding in te scha kelen tussen uw IoT Edge apparaat en de downstream-apparaten waarmee verbinding kan worden gemaakt. Er zijn drie IoT Edge certificaten (*PEM* -indeling) die u moet installeren:

- Basis-CA-certificaat of de eigenaar-CA
- CA-certificaat van apparaat
- Certificaat van de apparaats sleutel

In het volgende voor beeld ziet u het gebruik van deze cmdlet om IoT Edge certificaten te installeren:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
Wanneer u deze cmdlet uitvoert, wordt u gevraagd het wacht woord voor de netwerk share op te geven.

Ga voor meer informatie over certificaten naar [Azure IOT Edge certificaten](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) of [Installeer certificaten op een gateway](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway).

## <a name="view-device-information"></a>Apparaatgegevens weer geven
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="view-gpu-driver-information"></a>Informatie over GPU-stuur programma weer geven

Als de compute-functie op uw apparaat is geconfigureerd, kunt u ook de informatie over het GPU-stuur programma ophalen via de Power shell-interface. 

1. [Verbinding maken met de Power shell-interface](#connect-to-the-powershell-interface).
2. Gebruik de `Get-HcsGpuNvidiaSmi` om de informatie over het GPU-stuur programma op te halen voor uw apparaat.

    In het volgende voor beeld ziet u het gebruik van deze cmdlet:

    ```powershell
    Get-HcsGpuNvidiaSmi
    ```
    Noteer de stuur programma-informatie van de voorbeeld uitvoer van deze cmdlet.

    ```powershell    
    +-----------------------------------------------------------------------------+    
    | NVIDIA-SMI 440.64.00    Driver Version: 440.64.00    CUDA Version: 10.2     |    
    |-------------------------------+----------------------+----------------------+    
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |    
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |    
    |===============================+======================+======================|    
    |   0  Tesla T4            On   | 000029CE:00:00.0 Off |                    0 |    
    | N/A   60C    P0    29W /  70W |   1539MiB / 15109MiB |      0%      Default |    
    +-------------------------------+----------------------+----------------------+    
    |   1  Tesla T4           On  | 0000AD50:00:00.0 Off |                    0 |
    | N/A   58C    P0    29W /  70W |    330MiB / 15109MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    ```

## <a name="enable-multi-process-service-mps"></a>Service voor meerdere processen inschakelen (MPS)

Een MPS (multi-process service) op NVIDIA-Gpu's biedt een mechanisme waarbij Gpu's kunnen worden gedeeld door meerdere taken, waarbij elke taak een percentage van de resources van de GPU toegewezen heeft. Voer de volgende stappen uit om MPS in te scha kelen op uw Azure Stack edge-apparaat:

1. Voordat u begint, moet u het volgende doen: 

    1. U hebt [uw Azure stack edge-apparaat](azure-stack-edge-gpu-deploy-activate.md) geconfigureerd en geactiveerd met een Azure stack edge/data Box gateway resource in Azure.
    1. U hebt [Compute op dit apparaat geconfigureerd in de Azure Portal](azure-stack-edge-deploy-configure-compute.md#configure-compute).
    
1. [Verbinding maken met de Power shell-interface](#connect-to-the-powershell-interface).
1. Gebruik de volgende opdracht om MPS op uw apparaat in te scha kelen.

    ```powershell
    Start-HcsGpuMPS
    ```

## <a name="reset-your-device"></a>Het apparaat opnieuw instellen

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Reken logboeken ophalen

Als de compute-functie op uw apparaat is geconfigureerd, kunt u de reken logboeken ook ophalen via de Power shell-interface.

1. [Verbinding maken met de Power shell-interface](#connect-to-the-powershell-interface).
2. Gebruik de `Get-AzureDataBoxEdgeComputeRoleLogs` om de reken logboeken voor uw apparaat op te halen.

    In het volgende voor beeld ziet u het gebruik van deze cmdlet:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection    
    ```

    Hier volgt een beschrijving van de para meters die worden gebruikt voor de cmdlet:
    - `Path`: Geef een netwerkpad op naar de share waar u het Compute-pakket wilt maken.
    - `Credential`: Geef de gebruikers naam op voor de netwerk share. Wanneer u deze cmdlet uitvoert, moet u het wacht woord voor delen opgeven.
    - `FullLogCollection`: Met deze para meter zorgt u ervoor dat in het logboek pakket alle reken logboeken worden opgenomen. Het logboek pakket bevat standaard slechts een subset van de logboeken.



## <a name="debug-kubernetes-issues-related-to-iot-edge"></a>Fout opsporing voor Kubernetes-problemen met betrekking tot IoT Edge

Wanneer het Kubernetes-cluster wordt gemaakt, wordt er ook een standaard gebruiker `aseuser` gemaakt die aan een systeem naam ruimte is gekoppeld `iotedge` . U kunt deze gebruikers-en systeem naam ruimte gebruiken om problemen met de IoT Edge op te sporen.  

### <a name="create-config-file-for-system-namespace"></a>Het configuratie bestand voor de systeem naam ruimte maken

Om problemen op te lossen, moet u eerst het `config` bestand maken dat overeenkomt `iotedge` met de naam ruimte met `aseuser` .

Voer de `Get-HcsKubernetesUserConfig -AseUser` opdracht uit en sla de uitvoer op als `config` bestand (geen bestands extensie). Sla het bestand op in de `.kube` map van uw gebruikers profiel op de lokale computer.

Hier volgt een voor beeld van de uitvoer van de `Get-HcsKubernetesUserConfig` opdracht.

```PowerShell
[10.100.10.10]: PS>Get-HcsKubernetesUserConfig -AseUser
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01EVXhNekl4TkRRME5sb1hEVE13TURVeE1USXhORFEwTmxvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBS0M1CjlJbzRSU2hudG90QUdxdjNTYmRjOVd4UmJDYlRzWXU5S0RQeU9xanVoZE1UUE9PcmROOGNoa0x4NEFyZkZaU1AKZithUmhpdWZqSE56bWhucnkvZlprRGdqQzQzRmV5UHZzcTZXeVVDV0FEK2JBdi9wSkJDbkg2MldoWGNLZ1BVMApqU1k0ZkpXenNFbzBaREhoeUszSGN3MkxkbmdmaEpEanBQRFJBNkRWb2pIaktPb29OT1J1dURvUHpiOTg2dGhUCkZaQXJMZjRvZXRzTEk1ZzFYRTNzZzM1YVhyU0g3N2JPYVVsTGpYTzFYSnpFZlZWZ3BMWE5xR1ZqTXhBMVU2b1MKMXVJL0d1K1ArY
===========CUT=========================================CUT===================
    server: https://compute.myasegpu1.wdshcsso.com:6443
    name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: aseuser
    name: aseuser@kubernetes
current-context: aseuser@kubernetes
kind: Config
preferences: {}
users:
- name: aseuser
    user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJY1hOTXRPU2VwbG93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBMU1UTXlNVFEwTkRaYUZ3MHlNVEExTVRNeU1UVXhNVEphTUJJeApFREFPQmdOVkJBTVRCMkZ6WlhWelpYSXdnZ0VpTUEwR0NTcUdTSWIzRFFFQkFRVUFBNElCRHdBd2dnRUtBb0lCCkFRRHVjQ1pKdm9qNFIrc0U3a1EyYmVjNEJkTXdpUEhmU2R2WnNDVVY0aTRRZGY1Yzd0dkE3OVRSZkRLQTY1d08Kd0h0QWdlK3lLK0hIQ1Qyd09RbWtNek1RNjZwVFEzUlE0eVdtRDZHR1cWZWMExBR1hFUUxWWHRuTUdGCi0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==

[10.100.10.10]: PS>
```

Op een Azure Stack edge-apparaat waarop de compute-rol is geconfigureerd, `kubectl` zijn alle opdrachten beschikbaar voor het controleren of oplossen van problemen met modules. Voer `kubectl --help` uit vanuit het opdracht venster om een lijst met beschik bare opdrachten weer te geven.

```PowerShell
C:\Users\myuser>kubectl --help
kubectl controls the Kubernetes cluster manager.

Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
    create         Create a resource from a file or from stdin.
    expose         Take a replication controller, service, deployment or pod and expose it as a new Kubernetes Service
    run            Run a particular image on the cluster
    set            Set specific features on objects
    run-container  Run a particular image on the cluster. This command is deprecated, use "run" instead
==============CUT=============CUT============CUT========================

Usage:
    kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).

C:\Users\myuser>
```

Voor een uitgebreide lijst met `kubectl` opdrachten gaat u naar [ `kubectl` materiaal](https://kubernetes.io/docs/reference/kubectl/cheatsheet/).


### <a name="to-get-ip-of-service-or-module-exposed-outside-of-kubernetes-cluster"></a>Het IP-adres van de service of module die buiten het Kubernetes-cluster wordt weer gegeven, ophalen

Voer de volgende opdracht uit om het IP-adres van een taakverdelings service of modules te verkrijgen die buiten de Kubernetes worden weer gegeven:

`kubectl get svc -n iotedge`

Hieronder ziet u een voor beeld van de uitvoer van alle services of modules die buiten het Kubernetes-cluster worden weer gegeven. 


```powershell
C:\Users\user>kubectl get svc -n iotedge
NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                       AGE
edgehub        LoadBalancer   10.103.52.225   10.128.44.243   443:31987/TCP,5671:32336/TCP,8883:30618/TCP   34h
iotedged       ClusterIP      10.107.236.20   <none>          35000/TCP,35001/TCP                           3d8h
webserverapp   LoadBalancer   10.105.186.35   10.128.44.244   8080:30976/TCP                                16h

C:\Users\user>
```
Het IP-adres in de externe IP-kolom komt overeen met het externe eind punt voor de service of de module. U kunt ook [het externe IP-adres in het Kubernetes-dash board ophalen](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules).


### <a name="to-check-if-module-deployed-successfully"></a>Controleren of de module is geïmplementeerd

Compute-modules zijn containers waarvoor een bedrijfs logica is geïmplementeerd. Er kunnen meerdere containers worden uitgevoerd op een Kubernetes-pod. Als u wilt controleren of een compute-module is geïmplementeerd, voert u de `get pods` opdracht uit en controleert u of de container (overeenkomend met de module Compute) wordt uitgevoerd.

Voer de volgende opdracht uit om de lijst met alle bereiken in een specifieke naam ruimte weer te geven:

`get pods -n <namespace>`

Hieronder ziet u een voor beeld van een uitvoer van alle peulen die in de `iotedge` naam ruimte worden uitgevoerd.

```
C:\Users\myuser>kubectl get pods -n iotedge
NAME                        READY   STATUS    RESTARTS   AGE
edgeagent-cf6d4ffd4-q5l2k   2/2     Running   0          20h
edgehub-8c9dc8788-2mvwv     2/2     Running   0          56m
filemove-66c49984b7-h8lxc   2/2     Running   0          56m
iotedged-675d7f4b5f-9nml4   1/1     Running   0          20h

C:\Users\myuser>
```

De status **status geeft aan dat** alle peulen in de naam ruimte worden uitgevoerd en de kant-en- **klare** is het aantal containers dat is geïmplementeerd in een pod. In het voor gaande voor beeld wordt alle peulen uitgevoerd en zijn alle modules die in elk van de peulen zijn geïmplementeerd, actief. 

Voor een uitgebreidere uitvoer van een specifieke pod voor een bepaalde naam ruimte kunt u de volgende opdracht uitvoeren:

`kubectl describe pod <pod name> -n <namespace>` 

De voorbeeld uitvoer wordt hier weer gegeven.

```
C:\Users\myuser>kubectl describe pod filemove-66c49984b7 -n iotedge
Name:           filemove-66c49984b7-h8lxc
Namespace:      iotedge
Priority:       0
Node:           k8s-1hwf613cl-1hwf613/10.139.218.12
Start Time:     Thu, 14 May 2020 12:46:28 -0700
Labels:         net.azure-devices.edge.deviceid=myasegpu-edge
                net.azure-devices.edge.hub=myasegpu2iothub.azure-devices.net
                net.azure-devices.edge.module=filemove
                pod-template-hash=66c49984b7
Annotations:    net.azure-devices.edge.original-moduleid: filemove
Status:         Running
IP:             172.17.75.81
IPs:            <none>
Controlled By:  ReplicaSet/filemove-66c49984b7
Containers:
    proxy:
    Container ID:   docker://fd7975ca78209a633a1f314631042a0892a833b7e942db2e7708b41f03e8daaf
    Image:          azureiotedge/azureiotedge-proxy:0.1.0-beta8
    Image ID:       docker://sha256:5efbf6238f13d24bab9a2b499e5e05bc0c33ab1587d6cf6f289cdbe7aa667563
    Port:           <none>
    Host Port:      <none>
    State:          Running
        Started:      Thu, 14 May 2020 12:46:30 -0700
    Ready:          True
    Restart Count:  0
    Environment:
        PROXY_LOG:  Debug
=============CUT===============================CUT===========================
Volumes:
    config-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-config
    Optional:  false
    trust-bundle-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-trust-bundle
    Optional:  false
    myasesmb1local:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1local
    ReadOnly:   false
    myasesmb1:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1
    ReadOnly:   false
    filemove-token-pzvw8:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  filemove-token-pzvw8
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                    node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>


C:\Users\myuser>
```

### <a name="to-get-container-logs"></a>Container logboeken ophalen

Als u de logboeken voor een module wilt ophalen, voert u de volgende opdracht uit:

`kubectl logs <pod_name> -n <namespace> --all-containers` 

Omdat met `all-containers` de vlag alle logboeken voor alle containers worden gedumpt, is het gebruik van de optie een goede manier om de recente fouten te bekijken `--tail 10` .

Hier volgt een voor beeld van een uitvoer. 

```
C:\Users\myuser>kubectl logs filemove-66c49984b7-h8lxc -n iotedge --all-containers --tail 10
DEBUG 2020-05-14T20:40:42Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:12Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
05/14/2020 19:46:44: Info: Opening module client connection.
05/14/2020 19:46:45: Info: Open done.
05/14/2020 19:46:45: Info: Initializing with input: /home/input, output: /home/output, protocol: Amqp.
05/14/2020 19:46:45: Info: IoT Hub module client initialized.

C:\Users\myuser>
```

## <a name="exit-the-remote-session"></a>De externe sessie afsluiten

Als u de externe Power shell-sessie wilt afsluiten, sluit u het Power shell-venster.

## <a name="next-steps"></a>Volgende stappen

- [Azure stack Edge](azure-stack-edge-gpu-deploy-prep.md) implementeren in azure Portal.
