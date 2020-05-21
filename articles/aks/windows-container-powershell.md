---
title: Een Windows Server-container maken op een Azure Kubernetes service (AKS)-cluster
description: Meer informatie over hoe u snel een Kubernetes-cluster kunt maken, hoe u een toepassing implementeert in een Windows Server-container in azure Kubernetes service (AKS) met behulp van Power shell.
services: container-service
ms.topic: article
ms.date: 05/12/2020
ms.openlocfilehash: d488935a9f45d775578179cb58423d882a1cee27
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83724986"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-powershell"></a>Een Windows Server-container maken op een Azure Kubernetes service (AKS)-cluster met behulp van Power shell

Azure Kubernetes Service (AKS) is een beheerde Kubernetes-service waarmee u snel clusters kunt implementeren en beheren. In dit artikel implementeert u een AKS-cluster met behulp van Power shell. U implementeert ook een `ASP.NET` voorbeeld toepassing in een Windows Server-container naar het cluster.

![Afbeelding van bladeren naar ASP.NET-voorbeeld toepassing](media/windows-container-powershell/asp-net-sample-app.png)

In dit artikel wordt ervan uitgegaan dat u basis informatie krijgt over Kubernetes-concepten. Zie [Kubernetes-kernconcepten voor Azure Kubernetes Service-cluster (AKS)][kubernetes-concepts] voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis](https://azure.microsoft.com/free/) account aan voordat u begint.

Als u Power shell lokaal wilt gebruiken, moet u voor dit artikel de AZ Power shell-module installeren en verbinding maken met uw Azure-account met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) . Zie [Install Azure PowerShell][install-azure-powershell](Engelstalig) voor meer informatie over het installeren van de AZ Power shell-module.

> [!IMPORTANT]
> In dit artikel wordt gebruikgemaakt van een preview-versie van de Power shell-module AZ. AKS. U moet deze afzonderlijk van de AZ Power shell-module installeren met behulp van de volgende opdracht: `Install-Module -Name Az.AKS -AllowPrerelease -RequiredVersion 1.1.0-preview` . Zodra de preview-versie van de Power shell-module AZ. AKS algemeen beschikbaar is, wordt deze onderdeel van toekomstige AZ Power shell-module releases en is deze systeem eigen beschikbaar vanuit Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Als u meerdere Azure-abonnementen hebt, kiest u het juiste abonnement waarin de resources moeten worden gefactureerd. Selecteer een specifieke abonnements-ID met behulp van de cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="limitations"></a>Beperkingen

De volgende beperkingen zijn van toepassing wanneer u AKS-clusters maakt en beheert die ondersteuning bieden voor meerdere knooppunt groepen:

* U kunt de eerste knooppunt groep niet verwijderen.

De volgende extra beperkingen zijn van toepassing op Windows Server-knooppunt groepen:

* Het AKS-cluster kan Maxi maal 10 knooppunt groepen bevatten.
* Het AKS-cluster kan Maxi maal 100 knoop punten in elke knooppunt groep bevatten.
* De naam van de Windows Server-knooppunt groep heeft een limiet van 6 tekens.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een [Azure-resource groep](/azure/azure-resource-manager/resource-group-overview) is een logische groep waarin Azure-resources worden geïmplementeerd en beheerd. Wanneer u een resourcegroep maakt, wordt u gevraagd een locatie op te geven. Op deze locatie worden de meta gegevens van de resource groep opgeslagen, maar ook de resources die in Azure worden uitgevoerd als u geen andere regio opgeeft tijdens het maken van resources. Maak een resource groep met behulp van de cmdlet [New-AzResourceGroup][new-azresourcegroup] .

In het volgende voorbeeld wordt een resourcegroep met de naam **myResourceGroup** gemaakt op de locatie **VS - oost**.

> [!NOTE]
> In dit artikel wordt de Power shell-syntaxis gebruikt voor de opdrachten in deze zelf studie. Als u Azure Cloud Shell gebruikt, zorg er dan voor dat de vervolg keuzelijst in de linkerbovenhoek van het Cloud Shell venster is ingesteld op **Power shell**.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

In de volgende voorbeelduitvoer ziet u dat de resourcegroep is gemaakt:

```Output
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

Gebruik het `ssh-keygen` opdracht regel programma voor het genereren van een SSH-sleutel paar. Zie voor meer informatie [snelle stappen: maken en gebruiken van een openbaar persoonlijk sleutel paar van de SSH voor Linux-vm's in azure](/azure/virtual-machines/linux/mac-create-ssh-keys).

Als u een AKS-cluster wilt uitvoeren dat knooppunt Pools ondersteunt voor Windows Server-containers, moet uw cluster gebruikmaken van een netwerk beleid dat gebruikmaakt van de invoeg toepassing [Azure cni][azure-cni-about] (Geavanceerd). Zie [Azure cni-netwerken configureren][use-advanced-networking]voor meer informatie over het plannen van de vereiste subnet bereiken en netwerk overwegingen. Gebruik de cmdlet [New-AzAks][new-azaks] hieronder om een AKS-cluster te maken met de naam **myAKSCluster**. In het volgende voor beeld worden de benodigde netwerk bronnen gemaakt als ze niet bestaan.

> [!NOTE]
> Om ervoor te zorgen dat uw cluster betrouwbaar werkt, moet u ten minste twee knoop punten in de standaard knooppunt groep uitvoeren.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzAKS -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 2 -KubernetesVersion 1.16.7 -NetworkPlugin azure -NodeVmSetType VirtualMachineScaleSets -WindowsProfileAdminUserName akswinuser -WindowsProfileAdminUserPassword $Password
```

> [!Note]
> Als u het AKS-cluster niet kunt maken omdat de versie niet wordt ondersteund in deze regio, kunt u de `Get-AzAksVersion -Location eastus` opdracht gebruiken om de lijst met ondersteunde versies te vinden voor deze regio.

Na enkele minuten is de opdracht voltooid en retourneert deze informatie over het cluster. Af en toe kan het cluster langer dan een paar minuten duren. In deze gevallen Maxi maal 10 minuten toestaan.

## <a name="add-a-windows-server-node-pool"></a>Een Windows Server-knooppunt groep toevoegen

Standaard wordt een AKS-cluster gemaakt met een knooppunt groep waarvoor Linux-containers kunnen worden uitgevoerd. Gebruik `New-AzAksNodePool` de cmdlet om een knooppunt groep toe te voegen die Windows Server-containers naast de Linux-knooppunt groep kan uitvoeren.

```azurepowershell-interactive
New-AzAksNodePool -ResourceGroupName myResourceGroup -ClusterName myAKSCluster -OsType Windows -Name npwin -KubernetesVersion 1.16.7
```

Met de bovenstaande opdracht maakt u een nieuwe knooppunt groep met de naam **npwin** en voegt u deze toe aan de **myAKSCluster**. Bij het maken van een knooppunt groep voor het uitvoeren van Windows Server-containers is de standaard waarde voor **VmSize** **Standard_D2s_v3**. Als u ervoor kiest de para meter **VmSize** in te stellen, controleert u de lijst met [beperkte VM-grootten][restricted-vm-sizes]. De minimale aanbevolen grootte is **Standard_D2s_v3**. De vorige opdracht gebruikt ook het standaard-subnet in de standaard-vnet dat is gemaakt tijdens het uitvoeren `New-AzAks` .

## <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster

Als u een Kubernetes-cluster wilt beheren, gebruikt u [kubectl][kubectl], de Kubernetes-opdrachtregelclient. Als u Azure Cloud Shell gebruikt, is `kubectl` al geïnstalleerd. Als u `kubectl` lokaal wilt installeren, gebruikt u de `Install-AzAksKubectl` cmdlet:

```azurepowershell-interactive
Install-AzAksKubectl
```

Als u wilt configureren `kubectl` om verbinding te maken met uw Kubernetes-cluster, gebruikt u de cmdlet [import-AzAksCredential][import-azakscredential] . Bij deze opdracht worden referenties gedownload en wordt Kubernetes CLI geconfigureerd voor het gebruik van deze referenties.

```azurepowershell-interactive
Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
```

Als u de verbinding met uw cluster wilt controleren, gebruikt u de opdracht [kubectl get][kubectl-get] om een lijst met clusterknooppunten te retourneren.

```azurepowershell-interactive
kubectl get nodes
```

In de volgende voorbeeld uitvoer ziet u alle knoop punten in het cluster. Zorg ervoor dat de status van alle knoop punten **gereed**is:

```Output
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.16.7
aksnpwin987654                      Ready    agent   108s   v1.16.7
```

## <a name="run-the-application"></a>De toepassing uitvoeren

In een Kubernetes-manifestbestand wordt een gewenste status voor het cluster gedefinieerd, zoals welke containerinstallatiekopieën moeten worden uitgevoerd. In dit artikel wordt een manifest gebruikt om alle objecten te maken die nodig zijn om de voorbeeld toepassing ASP.NET uit te voeren in een Windows Server-container. Dit manifest bevat een [Kubernetes-implementatie][kubernetes-deployment] voor de voorbeeld toepassing ASP.net en een externe [Kubernetes-service][kubernetes-service] voor toegang tot de toepassing vanaf internet.

De voorbeeld toepassing ASP.NET wordt meegeleverd als onderdeel van de [.NET Framework][dotnet-samples] -voor beelden en wordt uitgevoerd in een Windows Server-container. AKS vereist dat Windows Server-containers worden gebaseerd op installatie kopieën van **Windows server 2019** of hoger. In het manifest bestand Kubernetes moet ook een [knooppunt kiezer][node-selector] worden gedefinieerd, zodat uw AKS-cluster de pod van uw ASP.net-voorbeeld toepassing uitvoert op een knoop punt waarop Windows Server-containers kunnen worden uitgevoerd.

Maak een bestand met de naam `sample.yaml` en kopieer de volgende YAML-definitie naar het bestand. Als u Azure Cloud Shell gebruikt, kan dit bestand worden gemaakt met behulp van `vi` of `nano`, zoals bij een virtueel of fysiek systeem:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

Implementeer de toepassing met de opdracht [kubectl apply][kubectl-apply] en geef de naam op van uw YAML-manifest:

```azurepowershell-interactive
kubectl apply -f sample.yaml
```

In de volgende voorbeeld uitvoer ziet u dat de implementatie en service zijn gemaakt:

```Output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>De toepassing testen

Wanneer de toepassing wordt uitgevoerd, stelt een Kubernetes-service de front-end van de toepassing beschikbaar op internet.
Dit proces kan enkele minuten duren. Af en toe kan de service langer dan een paar minuten duren om in te richten. In deze gevallen Maxi maal 10 minuten toestaan.

Gebruik de opdracht [kubectl get service][kubectl-get] met het argument `--watch` om de voortgang te controleren.

```azurepowershell-interactive
kubectl get service sample --watch
```

In eerste instantie wordt het **externe IP-adres** voor de **voorbeeld** service weer gegeven als **in behandeling**.

```Output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Zodra het **Extern IP-adres** is gewijzigd van **in behandeling** in een echt openbaar IP-adres, gebruikt u `CTRL-C` om het controleproces van `kubectl` te stoppen. In de volgende voorbeelduitvoer ziet u een geldig openbaar IP-adres dat aan de service is toegewezen:

```Output
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Als u de voor beeld-app in actie wilt zien, opent u een webbrowser op het externe IP-adres van uw service.

![Afbeelding van bladeren naar ASP.NET-voorbeeld toepassing](media/windows-container-powershell/asp-net-sample-app.png)

> [!Note]
> Als u een time-out voor de verbinding krijgt bij het laden van de pagina, moet u controleren of de voor beeld-app klaar is met de volgende opdracht `kubectl get pods --watch` . Soms wordt de Windows-container niet gestart op het moment dat het externe IP-adres beschikbaar is.

## <a name="delete-cluster"></a>Cluster verwijderen

Wanneer het cluster niet meer nodig is, gebruikt u de cmdlet [Remove-AzResourceGroup][remove-azresourcegroup] om de resource groep, de container service en alle gerelateerde resources te verwijderen.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

> [!NOTE]
> Wanneer u het cluster verwijdert, wordt de Azure Active Directory-service-principal die door het AKS-cluster wordt gebruikt niet verwijderd. Zie [Overwegingen voor en verwijdering van AKS service-principal][sp-delete] voor stappen voor het verwijderen van de service-principal. Als u een beheerde identiteit hebt gebruikt, wordt de identiteit beheerd door het platform en hoeft niet te worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een Kubernetes-cluster geïmplementeerd en een `ASP.NET` voorbeeld toepassing in een Windows Server-container geïmplementeerd. [Open het Kubernetes-webdashboard][kubernetes-dashboard] voor het cluster dat u hebt gemaakt.

Voor meer informatie over AKS en een volledig stapsgewijs voorbeeld van code tot implementatie gaat u naar de zelfstudie over Kubernetes-clusters.

> [!div class="nextstepaction"]
> [AKS-zelfstudie][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[use-advanced-networking]: configure-azure-cni.md
[new-azaks]: /powershell/module/az.aks/new-azaks
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
