---
title: Een Kubernetes-cluster maken met Application Gateway als controller voor inkomend verkeer, met behulp van Azure Kubernetes Service (AKS)
description: Zelfstudie voor het maken van een Kubernetes-cluster met Application Gateway als controller voor inkomend verkeer, met behulp van Azure Kubernetes Service
services: terraform
ms.service: azure
keywords: terraform, devops, virtuele machine, azure, kubernetes, inkomend verkeer, application gateway
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 0373b254a900fd34232bb6863c93802fa7b51aab
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169963"
---
# <a name="create-a-kubernetes-cluster-with-application-gateway-ingress-controller-using-azure-kubernetes-service-and-terraform"></a>Een Kubernetes-cluster maken met een Application Gateway-controller voor inkomend verkeer, met behulp van Azure Kubernetes Service en Terraform
[Azure Kubernetes Service (AKS)](/azure/aks/) beheert uw gehoste Kubernetes-omgeving. Met AKS kunt u snel en eenvoudig in containers geplaatste toepassingen implementeren en beheren, zonder dat u kennis hoeft te hebben van het indelen van containers. Het verlicht ook de last van actieve bewerkingen en onderhoud door inrichten, upgraden en bronnen op aanvraag schalen mogelijk te maken, zonder uw toepassingen offline te brengen.

Een controller voor inkomend verkeer is een stukje software dat omgekeerde proxy’s, configureerbare verkeersroutering en TLS-beëindiging voor Kubernetes-services biedt. Kubernetes-resources voor inkomend verkeer worden gebruikt om de regels en routes voor uitgaand verkeer worden geconfigureerd voor individuele Kubernetes-services. Met behulp van een controller en regels voor inkomend verkeer kan er één enkel IP-adres worden gebruikt voor het routeren van verkeer naar meerdere services in een Kubernetes-cluster. Alle bovenstaande functionaliteiten worden aangeboden door Azure [Application Gateway](/azure/Application-Gateway/), waardoor het een perfecte controller voor inkomend verkeer is voor Kubernetes op Azure. 

In deze zelfstudie leert u hoe u de volgende taken uitvoert bij het maken van een [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes)-cluster met Application Gateway als controller voor inkomend verkeer, met behulp van AKS:

> [!div class="checklist"]
> * HCL (HashiCorp Configuration Language) gebruiken om een Kubernetes-cluster te definiëren
> * Terraform gebruiken om een Application Gateway-resource te maken
> * Terraform en AKS gebruiken om een Kubernetes-cluster te maken
> * Het hulpprogramma kubectl gebruiken om de beschikbaarheid van een Kubernetes-cluster te testen

## <a name="prerequisites"></a>Vereisten

- **Azure-abonnement**: Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) aan voordat u begint.

- **Terraform configureren**: Volg de aanwijzingen in het artikel [Terraform en toegang tot Azure configureren](/azure/virtual-machines/linux/terraform-install-configure)

- **Azure-service-principal**: volg de aanwijzingen in de sectie **De service-principal maken** in het artikel [Een Azure-service-principal maken met de Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Noteer de waarden voor appId, displayName en password.
  - Noteer de Object-id van de service-principal die wordt weergegeven wanneer u de volgende opdracht hebt uitgevoerd

    ```azurecli
    az ad sp list --display-name <displayName>
    ```

## <a name="create-the-directory-structure"></a>De directorystructuur maken
De eerste stap is het maken van een map voor de Terraform-configuratiebestanden voor de oefening.

1. Blader naar [Azure Portal](https://portal.azure.com).

1. Open [Azure Cloud Shell](/azure/cloud-shell/overview). Als u nog geen omgeving hebt geselecteerd, selecteert u **Bash** als uw omgeving.

    ![Cloud Shell-prompt](./media/terraform-k8s-cluster-appgw-with-tf-aks/azure-portal-cloud-shell-button-min.png)

1. Ga naar de map `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Maak een directory met de naam `terraform-aks-k8s`.

    ```bash
    mkdir terraform-aks-appgw-ingress
    ```

1. Ga naar de nieuwe map:

    ```bash
    cd terraform-aks-appgw-ingress
    ```

## <a name="declare-the-azure-provider"></a>De Azure-provider declareren
Maak het Terraform-configuratiebestand waarin de Azure-provider wordt gedeclareerd.

1. Maak in Cloud Shell een bestand met de naam `main.tf`.

    ```bash
    vi main.tf
    ```

1. Activeer de invoegmodus door op de toets I te drukken.

1. Plak de volgende code in de editor:

    ```hcl
    provider "azurerm" {
        version = "~>1.18"
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. Verlaat de invoegmodus door op **Esc** te drukken.

1. Sla het bestand op en sluit vi Editor af met de volgende opdracht:

    ```bash
    :wq
    ```

## <a name="define-input-variables"></a>Invoervariabelen definiëren
Maak het terraform-configuratie bestand met een lijst met alle variabelen die vereist zijn voor deze implementatie.

1. Maak in Cloud Shell een bestand met de naam `variables.tf`.

    ```bash
    vi variables.tf
    ```

1. Activeer de invoegmodus door op de toets I te drukken.

1. Plak de volgende code in de editor:
    
    ```hcl
    variable "resource_group_name" {
      description = "Name of the resource group already created."
    }
    
    variable "location" {
      description = "Location of the cluster."
    }
    
    variable "aks_service_principal_app_id" {
      description = "Application ID/Client ID  of the service principal. Used by AKS to manage AKS related resources on Azure like vms, subnets."
    }
    
    variable "aks_service_principal_client_secret" {
      description = "Secret of the service principal. Used by AKS to manage Azure."
    }
    
    variable "aks_service_principal_object_id" {
      description = "Object ID of the service principal."
    }
    
    variable "virtual_network_name" {
      description = "Virtual network name"
      default     = "aksVirtualNetwork"
    }
    
    variable "virtual_network_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/8"
    }
    
    variable "aks_subnet_name" {
      description = "AKS Subnet Name."
      default     = "kubesubnet"
    }
    
    variable "aks_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/16"
    }
    
    variable "app_gateway_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.1.0.0/16"
    }
    
    variable "app_gateway_name" {
      description = "Name of the Application Gateway."
      default = "ApplicationGateway1"
    }
    
    variable "app_gateway_sku" {
      description = "Name of the Application Gateway SKU."
      default = "Standard_v2"
    }
    
    
    variable "app_gateway_tier" {
      description = "Tier of the Application Gateway SKU."
      default = "Standard_v2"
    }
    
    
    variable "aks_name" {
      description = "Name of the AKS cluster."
      default     = "aks-cluster1"
    }
    variable "aks_dns_prefix" {
      description = "Optional DNS prefix to use with hosted Kubernetes API server FQDN."
      default     = "aks"
    }
    
    
    variable "aks_agent_os_disk_size" {
      description = "Disk size (in GB) to provision for each of the agent pool nodes. This value ranges from 0 to 1023. Specifying 0 will apply the default disk size for that agentVMSize."
      default     = 40
    }
    
    variable "aks_agent_count" {
      description = "The number of agent nodes for the cluster."
      default     = 3
    }
    
    variable "aks_agent_vm_size" {
      description = "The size of the Virtual Machine."
      default     = "Standard_D3_v2"
    }
    
    variable "kubernetes_version" {
      description = "The version of Kubernetes."
      default     = "1.11.5"
    }
    
    variable "aks_service_cidr" {
      description = "A CIDR notation IP range from which to assign service cluster IPs."
      default     = "10.0.0.0/16"
    }
    
    variable "aks_dns_service_ip" {
      description = "Containers DNS server IP address."
      default     = "10.0.0.10"
    }
    
    variable "aks_docker_bridge_cidr" {
      description = "A CIDR notation IP for Docker bridge."
      default     = "172.17.0.1/16"
    }
    
    variable "aks_enable_rbac" {
      description = "Enable RBAC on the AKS cluster. Defaults to false."
      default     = "false"
    }
    
    variable "vm_user_name" {
      description = "User name for the VM"
      default     = "vmuser1"
    }
    
    variable "public_ssh_key_path" {
      description = "Public key path for SSH."
      default     = "~/.ssh/id_rsa.pub"
    }
    
    variable "tags" {
      type = "map"
    
      default = {
        source = "terraform"
      }
    }
    ```

## <a name="define-the-resources"></a>De resources definiëren 
Maak een Terraform-configuratiebestand waarin alle resources worden gemaakt. 

1. Maak in Cloud Shell een bestand met de naam `resources.tf`.

    ```bash
    vi resources.tf
    ```

1. Activeer de invoegmodus door op de toets I te drukken.

1. Plak de volgende codeblokken in de editor:

    a. Maak een lokale blok kering voor berekende variabelen die u opnieuw kunt gebruiken.

    ```hcl
    # # Locals block for hardcoded names. 
    locals {
        backend_address_pool_name      = "${azurerm_virtual_network.test.name}-beap"
        frontend_port_name             = "${azurerm_virtual_network.test.name}-feport"
        frontend_ip_configuration_name = "${azurerm_virtual_network.test.name}-feip"
        http_setting_name              = "${azurerm_virtual_network.test.name}-be-htst"
        listener_name                  = "${azurerm_virtual_network.test.name}-httplstn"
        request_routing_rule_name      = "${azurerm_virtual_network.test.name}-rqrt"
        app_gateway_subnet_name = "appgwsubnet"
    }
    ```

    b. Maak een gegevens bron voor de resource groep en de nieuwe gebruikers-id.

    ```hcl
    data "azurerm_resource_group" "rg" {
      name = "${var.resource_group_name}"
    }
    
    # User Assigned Idntities 
    resource "azurerm_user_assigned_identity" "testIdentity" {
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      location            = "${data.azurerm_resource_group.rg.location}"
    
      name = "identity1"
    
      tags = "${var.tags}"
    }
    ```

    c. Basis netwerk bronnen maken.

    ```hcl
    resource "azurerm_virtual_network" "test" {
      name                = "${var.virtual_network_name}"
      location            = "${data.azurerm_resource_group.rg.location}"
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      address_space       = ["${var.virtual_network_address_prefix}"]
    
      subnet {
        name           = "${var.aks_subnet_name}"
        address_prefix = "${var.aks_subnet_address_prefix}" 
      }
    
      subnet {
        name           = "appgwsubnet"
        address_prefix = "${var.app_gateway_subnet_address_prefix}"
      }
    
      tags = "${var.tags}"
    }
    
    data "azurerm_subnet" "kubesubnet" {
      name                 = "${var.aks_subnet_name}"
      virtual_network_name = "${azurerm_virtual_network.test.name}"
      resource_group_name  = "${data.azurerm_resource_group.rg.name}"
    }
    
    data "azurerm_subnet" "appgwsubnet" {
      name                 = "appgwsubnet"
      virtual_network_name = "${azurerm_virtual_network.test.name}"
      resource_group_name  = "${data.azurerm_resource_group.rg.name}"
    }
    
    # Public Ip 
    resource "azurerm_public_ip" "test" {
      name                         = "publicIp1"
      location                     = "${data.azurerm_resource_group.rg.location}"
      resource_group_name          = "${data.azurerm_resource_group.rg.name}"
      public_ip_address_allocation = "static"
      sku                          = "Standard"
    
      tags = "${var.tags}"
    }
    ```

    d. Application Gateway resource maken.

    ```hcl
    resource "azurerm_application_gateway" "network" {
      name                = "${var.app_gateway_name}"
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      location            = "${data.azurerm_resource_group.rg.location}"
    
      sku {
        name     = "${var.app_gateway_sku}"
        tier     = "Standard_v2"
        capacity = 2
      }
    
      gateway_ip_configuration {
        name      = "appGatewayIpConfig"
        subnet_id = "${data.azurerm_subnet.appgwsubnet.id}"
      }
    
      frontend_port {
        name = "${local.frontend_port_name}"
        port = 80
      }
    
      frontend_port {
        name = "httpsPort"
        port = 443
      }
    
      frontend_ip_configuration {
        name                 = "${local.frontend_ip_configuration_name}"
        public_ip_address_id = "${azurerm_public_ip.test.id}"
      }
    
      backend_address_pool {
        name = "${local.backend_address_pool_name}"
      }
    
      backend_http_settings {
        name                  = "${local.http_setting_name}"
        cookie_based_affinity = "Disabled"
        port                  = 80
        protocol              = "Http"
        request_timeout       = 1
      }
    
      http_listener {
        name                           = "${local.listener_name}"
        frontend_ip_configuration_name = "${local.frontend_ip_configuration_name}"
        frontend_port_name             = "${local.frontend_port_name}"
        protocol                       = "Http"
      }
    
      request_routing_rule {
        name                       = "${local.request_routing_rule_name}"
        rule_type                  = "Basic"
        http_listener_name         = "${local.listener_name}"
        backend_address_pool_name  = "${local.backend_address_pool_name}"
        backend_http_settings_name = "${local.http_setting_name}"
      }
    
      tags = "${var.tags}"
    
      depends_on = ["azurerm_virtual_network.test", "azurerm_public_ip.test"]
    }
    ```

    e. Roltoewijzingen maken.

    ```hcl
    resource "azurerm_role_assignment" "ra1" {
      scope                = "${data.azurerm_subnet.kubesubnet.id}"
      role_definition_name = "Network Contributor"
      principal_id         = "${var.aks_service_principal_object_id }"
    
      depends_on = ["azurerm_virtual_network.test"]
    }
    
    resource "azurerm_role_assignment" "ra2" {
      scope                = "${azurerm_user_assigned_identity.testIdentity.id}"
      role_definition_name = "Managed Identity Operator"
      principal_id         = "${var.aks_service_principal_object_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity"]
    }
    
    resource "azurerm_role_assignment" "ra3" {
      scope                = "${azurerm_application_gateway.network.id}"
      role_definition_name = "Contributor"
      principal_id         = "${azurerm_user_assigned_identity.testIdentity.principal_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    
    resource "azurerm_role_assignment" "ra4" {
      scope                = "${data.azurerm_resource_group.rg.id}"
      role_definition_name = "Reader"
      principal_id         = "${azurerm_user_assigned_identity.testIdentity.principal_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    ```

    f. Maak de Kubernetes-cluster.

    ```hcl
    resource "azurerm_kubernetes_cluster" "k8s" {
      name       = "${var.aks_name}"
      location   = "${data.azurerm_resource_group.rg.location}"
      dns_prefix = "${var.aks_dns_prefix}"
    
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
    
      linux_profile {
        admin_username = "${var.vm_user_name}"
    
        ssh_key {
          key_data = "${file(var.public_ssh_key_path)}"
        }
      }
    
      addon_profile {
        http_application_routing {
          enabled = false
        }
      }
    
      agent_pool_profile {
        name            = "agentpool"
        count           = "${var.aks_agent_count}"
        vm_size         = "${var.aks_agent_vm_size}"
        os_type         = "Linux"
        os_disk_size_gb = "${var.aks_agent_os_disk_size}"
        vnet_subnet_id  = "${data.azurerm_subnet.kubesubnet.id}"
      }
    
      service_principal {
        client_id     = "${var.aks_service_principal_app_id}"
        client_secret = "${var.aks_service_principal_client_secret}"
      }
    
      network_profile {
        network_plugin     = "azure"
        dns_service_ip     = "${var.aks_dns_service_ip}"
        docker_bridge_cidr = "${var.aks_docker_bridge_cidr}"
        service_cidr       = "${var.aks_service_cidr}"
      }
    
      depends_on = ["azurerm_virtual_network.test", "azurerm_application_gateway.network"]
      tags       = "${var.tags}"
    }
    
    ```

    Met de bovenstaande code worden de naam van het cluster, de locatie en de resource_group_name ingesteld. Daarnaast wordt de waarde ingesteld van dns_prefix (die deel uitmaakt van de Fully Qualified Domain Name (FQDN) waarmee toegang wordt verkregen tot het cluster).

    Met het **linux_profile**-record kunt u de instellingen configureren waarmee aanmelding bij de werkknooppunten via SSH mogelijk is.

    Met AKS betaalt u alleen voor de werkknooppunten. Met het **agent_pool_profile**-record configureert u de details voor deze werkknooppunten. Het **agent_pool_profile**-record bevat het aantal te maken werkknooppunten en het type werkknooppunten. Als u het cluster in de toekomst omhoog of omlaag wilt schalen, wijzigt u de waarde **aantal** in dit record.

1. Verlaat de invoegmodus door op **Esc** te drukken.

1. Sla het bestand op en sluit vi Editor af met de volgende opdracht:

    ```bash
    :wq
    ```


## <a name="create-a-terraform-output-file"></a>Een Terraform-uitvoerbestand maken
Met [Terraform-uitvoer](https://www.terraform.io/docs/configuration/outputs.html) kunt u waarden definiëren die voor de gebruiker worden gemarkeerd wanneer in Terraform een plan wordt toegepast. Deze uitvoer kan worden opgevraagd met de opdracht `terraform output`. In deze sectie maakt u een uitvoerbestand waarmee via [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) toegang wordt verkregen tot het cluster.

1. Maak in Cloud Shell een bestand met de naam `output.tf`.

    ```bash
    vi output.tf
    ```

1. Activeer de invoegmodus door op de toets I te drukken.

1. Plak de volgende code in de editor:

    ```hcl
    output "client_key" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_key}"
    }

    output "client_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate}"
    }

    output "cluster_ca_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate}"
    }

    output "cluster_username" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.username}"
    }

    output "cluster_password" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.password}"
    }

    output "kube_config" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config_raw}"
    }

    output "host" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.host}"
    }
    ```

1. Verlaat de invoegmodus door op **Esc** te drukken.

1. Sla het bestand op en sluit vi Editor af met de volgende opdracht:

    ```bash
    :wq
    ```

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Azure Storage instellen voor opslag van de Terraform-status
In Terraform wordt de status lokaal bijgehouden via het bestand `terraform.tfstate`. Dit patroon werkt goed in een omgeving met één persoon. In een praktischere omgeving met meerdere personen moet u echter met behulp van [Azure Storage](/azure/storage/) de status bijhouden op de server. In deze sectie haalt u de benodigde opslagaccountgegevens (accountnaam en accountsleutel) op en maakt u een opslagcontainer om de Terraform-statusgegevens in op te slaan.

1. Selecteer in Azure Portal de optie **Alle services** in het linkermenu.

1. Selecteer **Opslagaccounts**.

1. Selecteer op het tabblad **Opslagaccounts** de naam van het opslagaccount waarin de Terraform-status moet worden opgeslagen. U kunt bijvoorbeeld het opslagaccount gebruiken dat u hebt gemaakt toen u Cloud Shell de eerste keer opende.  De opslagaccountnaam die door Cloud Shell wordt gemaakt, begint gewoonlijk met `cs`, gevolgd door een willekeurige tekenreeks met cijfers en letters. **Noteer de naam van het opslagaccount dat u selecteert, want deze hebben we later nog nodig.**

1. Selecteer op het opslagaccounttabblad **Toegangssleutels**.

    ![Opslagaccountmenu](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account.png)

1. Noteer de waarde van de **key1**-**sleutel**. (Als u het pictogram rechts van de sleutel selecteert, wordt de waarde gekopieerd naar het klembord.)

    ![Toegangssleutels voor opslagaccount](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account-access-key.png)

1. Maak in Cloud Shell een container in uw Azure-opslagaccount (vervang de tijdelijke aanduidingen &lt;YourAzureStorageAccountName> en &lt;YourAzureStorageAccountAccessKey> door de juiste waarden voor uw Azure-opslagaccount).

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Het Kubernetes-cluster maken
In deze sectie ziet u hoe u de opdracht `terraform init` gebruikt om de resources te maken die zijn gedefinieerd in de configuratiebestanden die u in de vorige secties hebt gemaakt.

1. Initialiseer Terraform in Cloud Shell (vervang de tijdelijke aanduidingen &lt;YourAzureStorageAccountName> en &lt;YourAzureStorageAccountAccessKey> door de juiste waarden voor uw Azure-opslagaccount).

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    Met de opdracht `terraform init` geeft u de status weer van het initialiseren van de back-end en providerinvoegtoepassing:

    ![Voorbeeld van de resultaten van de opdracht 'terraform init'](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-init-complete.png)

1. Maak een bestand met variabelen dat invoerwaarden bevat  Maak in Cloud Shell een bestand met de naam `main.tf`.

    ```bash
    vi terraform.tfvars
    ```

1. Activeer de invoegmodus door op de toets I te drukken.

1. Plak de volgende variabelen, die eerder zijn gemaakt, in de editor:

    ```hcl
      resource_group_name = <Name of the Resource Group already created>

      location = <Location of the Resource Group>
        
      aks_service_principal_app_id = <Service Principal AppId>
        
      aks_service_principal_client_secret = <Service Principal Client Secret>
        
      aks_service_principal_object_id = <Service Principal Object Id>
        
    ```

1. Verlaat de invoegmodus door op **Esc** te drukken.

1. Sla het bestand op en sluit vi Editor af met de volgende opdracht:

    ```bash
    :wq
    ```

1. Voer de opdracht `terraform plan` uit om het Terraform-plan te maken waarmee de infrastructuurelementen worden gedefinieerd. 

    ```bash
    terraform plan -out out.plan
    ```

    Met de opdracht `terraform plan` geeft u de resources weer die worden gemaakt tijdens het uitvoeren van de opdracht `terraform apply`:

    ![Voorbeeld van de resultaten van de opdracht 'terraform plan'](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-plan-complete.png)

1. Voer de opdracht `terraform apply` uit om het plan voor het maken van het Kubernetes-cluster toe te passen. Het proces voor het maken van een Kubernetes-cluster kan enige minuten duren, waardoor er een time-out kan optreden voor de Cloud Shell-sessie. Als er een time-out optreedt voor de Cloud Shell-sessie, kunt u de stappen in de sectie 'Herstellen na een time-out van Cloud Shell' volgen zodat u de zelfstudie kunt voltooien.

    ```bash
    terraform apply out.plan
    ```

    Met de opdracht `terraform apply` geeft u de resultaten weer van het maken van de resources die zijn gedefinieerd in uw configuratiebestanden:

    ![Voorbeeld van de resultaten van de opdracht 'terraform apply'](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-apply-complete.png)

1. Selecteer in de Azure-portal **Resourcegroepen** in het linkermenu om alle resources in de geselecteerde resourcegroep weer te geven die voor uw nieuwe Kubernetes-cluster zijn gemaakt.

    ![Cloud Shell-prompt](./media/terraform-k8s-cluster-appgw-with-tf-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Herstellen na een time-out van Cloud Shell
Als er een time-out is opgetreden voor de Cloud Shell-sessie, kunt u de volgende herstelstappen uitvoeren:

1. Start een Cloud Shell-sessie.

1. Ga naar de map met uw Terraform-configuratiebestanden.

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. Voer de volgende opdracht uit:

    ```bash
    export KUBECONFIG=./azurek8s
    ```
    
## <a name="test-the-kubernetes-cluster"></a>Het Kubernetes-cluster testen
U kunt het gemaakte cluster controleren met de Kubernetes-hulpprogramma's.

1. Haal de Kubernetes-configuratie op uit de Terraform-status en sla deze op in een bestand dat door kubectl kan worden gelezen.

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. Stel een omgevingsvariabele in zodat de juiste configuratie wordt opgehaald door kubectl.

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. Controleer de status van het cluster.

    ```bash
    kubectl get nodes
    ```

    De details van de werkknooppunten worden weergegeven. De status van al deze werkknooppunten moet **Gereed** zijn, zoals in de volgende afbeelding is weergegeven:

    ![Met het hulpprogramma kubectl kunt u de status van uw Kubernetes-cluster controleren](./media/terraform-k8s-cluster-appgw-with-tf-aks/kubectl-get-nodes.png)


## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u Terraform en AKS gebruikt om een Kubernetes-cluster te maken. Hier volgen enkele aanvullende resources voor meer informatie over Terraform in Azure.
 
 > [!div class="nextstepaction"] 
 > [Terraform Hub in Microsoft.com](https://docs.microsoft.com/azure/terraform/)
 
