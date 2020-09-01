---
title: Azure-Arc op Kubernetes inschakelen op Azure Stack edge-apparaat | Microsoft Docs
description: Hierin wordt beschreven hoe u Azure-Arc op een bestaand Kubernetes-cluster op uw Azure Stack edge-apparaat inschakelt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/25/2020
ms.author: alkohli
ms.openlocfilehash: 03bf2a01ad4c1d5d017d881bf171806fa33e8799
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89084486"
---
# <a name="enable-azure-arc-on-kubernetes-cluster-on-your-azure-stack-edge-device"></a>Azure Arc op Kubernetes-cluster op uw Azure Stack edge-apparaat inschakelen

Dit artikel laat u zien hoe u Azure Arc kunt inschakelen op een bestaand Kubernetes-cluster op uw Azure Stack edge-apparaat. 

Deze procedure is bedoeld voor gebruikers die de Kubernetes- [workloads op Azure stack edge-apparaat](azure-stack-edge-gpu-kubernetes-workload-management.md) hebben bekeken en die bekend zijn met de concepten van [Wat is Azure Arc enabled Kubernetes (preview)?](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview).


## <a name="prerequisites"></a>Vereisten

Voordat u Azure Arc op Kubernetes-cluster kunt inschakelen, moet u ervoor zorgen dat u de volgende vereisten hebt voltooid op uw Azure Stack edge-apparaat en de client die u gaat gebruiken voor toegang tot het apparaat:

### <a name="for-device"></a>Voor het apparaat

1. U hebt aanmeld referenties voor een Azure Stack edge-apparaat van 1 knoop punt.
    1. Het apparaat wordt geactiveerd. Zie [het apparaat activeren](azure-stack-edge-gpu-deploy-activate.md).
    1. Op het apparaat is de compute-rol geconfigureerd via Azure Portal en heeft het een Kubernetes-cluster. Zie [Configure Compute](azure-stack-edge-gpu-deploy-configure-compute.md).

1. U hebt de eigenaar toegang tot het abonnement. U hebt deze toegang nodig tijdens de stap voor de roltoewijzing voor uw service-principal.
 

### <a name="for-client-accessing-the-device"></a>Voor client toegang tot het apparaat

1. U hebt een Windows-client systeem dat wordt gebruikt voor toegang tot het Azure Stack edge-apparaat.
  
    - Windows Power shell 5,0 of hoger wordt uitgevoerd op de client. Als u de meest recente versie van Windows Power shell wilt downloaden, gaat u naar [Windows Power Shell installeren](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-7).
    
    - U kunt ook een andere client met een [ondersteund besturings systeem](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) hebben. In dit artikel wordt de procedure beschreven voor het gebruik van een Windows-client. 
    
1. U hebt de procedure die wordt beschreven in [toegang tot het Kubernetes-cluster op Azure stack edge-apparaat](azure-stack-edge-gpu-create-kubernetes-cluster.md)voltooid. U hebt het volgende:
    
    - Geïnstalleerd `kubectl` op de client  <!--and saved the `kubeconfig` file with the user configuration to C:\\Users\\&lt;username&gt;\\.kube. -->
    
    - Zorg ervoor dat de `kubectl` client versie niet meer dan één versie van de Kubernetes-Master versie die wordt uitgevoerd op uw Azure stack edge-apparaat. 
      - Gebruiken `kubectl version` om te controleren welke versie van kubectl op de client wordt uitgevoerd. Noteer de volledige versie.
      - Ga in de lokale gebruikers interface van uw Azure Stack edge-apparaat naar **Software-update** en noteer het versie nummer van de Kubernetes-server. 
    
        ![Het versie nummer van de Kubernetes-server controleren](media/azure-stack-edge-gpu-connect-powershell-interface/verify-kubernetes-version-1.png)      
      
      - Controleer of deze twee versies compatibel zijn. 

<!-- az cli version requirements-->

## <a name="register-kubernetes-resource-providers"></a>Kubernetes-resource providers registreren

Voordat u het Kubernetes-cluster via Azure Arc configureert, moet u uw abonnement inschakelen en `Microsoft.Kubernetes` registreren `Microsoft.KubernetesConfiguration` . 

1. Als u een resource provider wilt inschakelen, gaat u in het Azure Portal naar het abonnement dat u wilt gebruiken voor de implementatie. Ga naar **resource providers**. 
1. Zoek in het rechterdeel venster naar de providers die u wilt toevoegen. In dit voor beeld `Microsoft.Kubernetes` en `Microsoft.KubernetesConfiguration` .

    ![Kubernetes-resource providers registreren](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-1.png)

1. Selecteer een resource provider en selecteer aan de bovenkant van de opdracht balk de optie **registreren**. De registratie duurt enkele minuten. 

    ![Kubernetes-resource providers registreren](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-2.png)

1. Vernieuw de gebruikers interface totdat u ziet dat de resource provider is geregistreerd. Herhaal dit proces voor beide resource providers.
    
    ![Kubernetes-resource providers registreren](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-4.png)

U kunt resource providers ook registreren via de `az cli` . Zie voor meer informatie [registreren van de twee providers voor Azure Arc enabled Kubernetes](../azure-arc/kubernetes/connect-cluster.md#register-the-two-providers-for-azure-arc-enabled-kubernetes)

## <a name="create-service-principal-assign-role"></a>Een service-principal maken, een rol toewijzen

1. Zorg ervoor dat u `Subscription ID` en de naam van de resource groep die u hebt gebruikt voor de resource-implementatie voor uw Azure stack Edge-service. Als u de abonnements-ID wilt ophalen, gaat u naar uw Azure Stack Edge-resource in de Azure Portal. Ga naar **overzicht > Essentials**.

    ![Abonnements-ID ophalen](media/azure-stack-edge-gpu-connect-powershell-interface/get-subscription-id-1.png)

    Als u de naam van de resource groep wilt ophalen, gaat u naar **Eigenschappen**.

    ![Naam van resource groep ophalen](media/azure-stack-edge-gpu-connect-powershell-interface/get-resource-group-name-1.png)

1. Als u een Service-Principal wilt maken, gebruikt u de volgende opdracht via de `az cli` .

    `az as sp create-for-rbac --skip assignment --name "<Informative name for service principal>"`  

    Voor informatie over hoe u zich aanmeldt bij `az cli` , [Start u Cloud Shell in azure Portal](../cloud-shell/quickstart-powershell.md?view=azure-cli-latest#start-cloud-shell)

    Hier volgt een voorbeeld. 
    
    ```azurecli
    PS /home/user> az ad sp create-for-rbac --skip-assignment --name "https://azure-arc-for-ase-k8s"
    {
      "appId": "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b",
      "displayName": "azure-arc-for-ase-k8s",
      "name": "https://azure-arc-for-ase-k8s",
      "password": "<password>",
      "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
    }
    PS /home/user>
    ```

1. Noteer de `appID` ,, `name` `password` en `tenantID` Wanneer u deze in de volgende opdracht gebruikt als invoer.

1. Nadat u de nieuwe Service-Principal hebt gemaakt, wijst `Kubernetes Cluster - Azure Arc Onboarding` u de rol toe aan de zojuist gemaakte principal. Dit is een ingebouwde Azure-rol (gebruik de functie-ID in de opdracht) met beperkte machtigingen. Gebruik de volgende opdracht:

    `az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee <appId-from-service-principal> --scope /subscriptions/<SubscriptionID>/resourceGroups/<Resource-group-name>`

    Hier volgt een voorbeeld.
    
    ```azurecli
    PS /home/user> az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b --scope /subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1
    {
      "canDelegate": null,
      "id": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1/providers/Microsoft.Authorization/roleAssignments/59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "name": "59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "principalId": "b045b3fe-8745-4097-9674-91cb0afaad91",
      "principalType": "ServicePrincipal",
      "resourceGroup": "myaserg1",
      "roleDefinitionId": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
      "scope": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1",
      "type": "Microsoft.Authorization/roleAssignments"
    }
    PS /home/user>
    ```
    Voor meer informatie over het maken van een Service-Principal en het uitvoeren van de roltoewijzing, raadpleegt u de stappen in [een Azure-service-principal voor het maken van een open-boog inschakelen](https://docs.microsoft.com/azure/azure-arc/kubernetes/create-onboarding-service-principal).


## <a name="enable-arc-on-kubernetes-cluster"></a>Arc op Kubernetes-cluster inschakelen

Voer de volgende stappen uit om het Kubernetes-cluster voor Azure Arc management te configureren:

1. [Verbinding maken met de Power shell-interface](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) van uw apparaat.

1. Type:

    `Set-HcsKubernetesAzureArcAgent -SubscriptionId "<Your Azure Subscription Id>" -ResourceGroupName "<Resource Group Name>" -ResourceName "<Azure Arc resource name (shouldn't exist already)>" -Location "<Region associated with resource group>" -TenantId "<Tenant Id of service principal>" -ClientId "<App id of service principal>" -ClientSecret "<Password of service principal>"`

    Als u Azure-Arc wilt implementeren op Azure Stack edge-apparaat, moet u ervoor zorgen dat u een [ondersteunde regio gebruikt voor Azure Arc](../azure-arc/kubernetes/overview.md#supported-regions). Azure Arc is momenteel beschikbaar als preview-versie. 
    
    Hier volgt een voorbeeld:
   
    ```powershell
    [10.128.44.240]: PS>Set-HcsKubernetesAzureArcAgent -SubscriptionId "062c67a6-019b-40af-a775-c4dc1abe56ed" -ResourceGroupName "myaserg1" -ResourceName "myasetestresarc" -Location "WestEurope" -TenantId "72f988bf-86f1-41af-91ab-2d7cd011db47" -ClientId "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b" -ClientSecret "<password>"
        [10.128.44.240]: PS>
    ```
    
    In de Azure Portal moet een resource worden gemaakt met de naam die u in de voor gaande opdracht hebt ingevoerd.

    ![Naar Azure Arc-resource gaan](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. Als u wilt controleren of Azure Arc is ingeschakeld, voert u de volgende opdracht uit vanuit de Power shell-Interface:

    `kubectl get deployments -n azure-arc`

    Met deze opdracht vindt u alle toepassingen die zijn geïmplementeerd in de `azure-arc` naam ruimte die overeenkomt met Azure Arc.

    Hier volgt een voor beeld van een uitvoer waarin de Azure Arc-agents worden weer gegeven die zijn geïmplementeerd op uw Kubernetes-cluster in de `azure-arc` naam ruimte. 


    ```powershell
    [10.128.44.240]: PS>kubectl get deployments -n azure-arc
    NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
    cluster-metadata-operator   1/1     1            1           45m
    clusteridentityoperator     1/1     1            1           45m
    config-agent                1/1     1            1           45m
    connect-agent               1/1     1            1           45m
    controller-manager          1/1     1            1           45m
    flux-logs-agent             1/1     1            1           45m
    metrics-agent               1/1     1            1           45m
    resource-sync-agent         1/1     1            1           45m
    [10.128.44.240]: PS>
    ```

    U kunt ook een lijst krijgen met de peulen die worden uitgevoerd op uw Kubernetes-cluster in de `azure-arc` naam ruimte. Een Pod is een toepassings container, of proces, dat wordt uitgevoerd op uw Kubernetes-cluster. 

    Gebruik de volgende opdracht:
    
    `kubectl get pods -n azure-arc`
    
    Hier volgt een voorbeeld uitvoer.
    
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n azure-arc
    NAME                                         READY   STATUS    RESTARTS   AGE
    cluster-metadata-operator-64cbdf95b4-s2q52   2/2     Running   0          16m
    clusteridentityoperator-6f6dbccf7-nwnxg      3/3     Running   0          16m
    config-agent-7df5bf497b-mjm8k                3/3     Running   0          16m
    connect-agent-5d4c766764-m7h46               1/1     Running   0          16m
    controller-manager-777555fb57-t7tdp          3/3     Running   0          16m
    flux-logs-agent-845476c899-zcmtj             2/2     Running   0          16m
    metrics-agent-84d6fc8f4d-g9jkm               2/2     Running   0          16m
    resource-sync-agent-8f88dbf96-zgxjj          3/3     Running   0          16m
    [10.128.44.240]: PS>
    ```


Zoals in de voor gaande uitvoer wordt weer gegeven, bestaat de Azure Arc ingeschakelde Kubernetes uit een aantal agents (opera tors) die in uw cluster worden uitgevoerd en die zijn geïmplementeerd in de `azure-arc` naam ruimte.

- `config-agent`: Hiermee wordt het verbonden cluster gewatcheerd voor configuratie bronnen voor broncode beheer die zijn toegepast op het cluster en de nalevings status van updates
- `controller-manager`: is een operator van Opera tors en organiseert de interacties tussen onderdelen van Azure Arc
- `metrics-agent`: verzamelt metrische gegevens van andere Arc-agents om ervoor te zorgen dat deze agents de optimale prestaties vertonen
- `cluster-metadata-operator`: verzamelt de meta gegevens van het cluster, de Cluster versie, het aantal knoop punten en de versie van de Azure Arc-agent
- `resource-sync-agent`: synchroniseert de hierboven vermelde meta gegevens van het cluster naar Azure
- `clusteridentityoperator`: Azure Arc enabled Kubernetes ondersteunt momenteel de toegewezen identiteit van het systeem. clusteridentityoperator onderhoudt het Managed Service Identity (MSI)-certificaat dat door andere agents wordt gebruikt voor communicatie met Azure.
- `flux-logs-agent`: verzamelt logboeken van de stroom operators die zijn geïmplementeerd als onderdeel van de configuratie van broncode beheer.
- `connect-agent`: praat met de Azure Arc-resource.

### <a name="remove-arc-from-the-kubernetes-cluster"></a>Boog uit het Kubernetes-cluster verwijderen

Voer de volgende stappen uit om Azure Arc management te verwijderen:

1. 1. [Verbinding maken met de Power shell-interface](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) van uw apparaat.
2. Type:

    `Remove-HcsKubernetesAzureArcAgent` 


## <a name="next-steps"></a>Volgende stappen

Zie [een STAATLOZE PHP-Gastenboek toepassing implementeren met redis via GitOps op een Azure stack edge-apparaat](azure-stack-edge-gpu-deploy-stateless-application-git-ops-guestbook.md) voor meer informatie over het uitvoeren van een implementatie van Azure Arc.
