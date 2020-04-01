---
title: Zelfstudie - Een ingress-controller voor toepassingsgateway maken in Azure Kubernetes-service
description: In deze zelfstudie maakt u een Kubernetes-cluster met Azure Kubernetes-service met Application Gateway als ingress-controller
keywords: azure devops terraform application gateway ingress aks kubernetes
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 6b48d0acb654f0b0643c0754e53f6bc6ea76bb45
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945324"
---
# <a name="tutorial-create-an-application-gateway-ingress-controller-in-azure-kubernetes-service"></a>Zelfstudie: Een ingress-controller voor application gateway maken in Azure Kubernetes-service

[Azure Kubernetes Service (AKS)](/azure/aks/) beheert uw gehoste Kubernetes-omgeving. Met AKS kunt u snel en eenvoudig in containers geplaatste toepassingen implementeren en beheren, zonder dat u kennis hoeft te hebben van het indelen van containers. AKS elimineert ook de last van het offline halen van applicaties voor operationele en onderhoudstaken. Met behulp van AKS kunnen deze taken - waaronder het inrichten, upgraden en schalen van resources - on-demand worden uitgevoerd.

Een ingress-controller biedt verschillende functies voor Kubernetes-services. Deze functies omvatten reverse proxy, configureerbare verkeer routering, en TLS beëindiging. Kubernetes-invallenworden gebruikt om de invallende regels voor afzonderlijke Kubernetes-services te configureren. Met behulp van een invallende controller en invallenregels kan één IP-adres verkeer doorsturen naar meerdere services in een Kubernetes-cluster. Al deze functionaliteit wordt geleverd door Azure [Application Gateway,](/azure/Application-Gateway/)waardoor het een ideale Ingress-controller is voor Kubernetes op Azure. 

In deze zelfstudie leert u hoe u de volgende taken uitvoert:

> [!div class="checklist"]
> * Maak een [Kubernetes-cluster](https://www.redhat.com/en/topics/containers/what-is-kubernetes) met AKS met Application Gateway als Ingress-controller.
> * Gebruik HCL (HashiCorp Language) om een Kubernetes-cluster te definiëren.
> * Gebruik Terraform om de bron van application gateway te maken.
> * Gebruik Terraform en AKS om een Kubernetes-cluster te maken.
> * Gebruik de kubectl-tool om de beschikbaarheid van een Kubernetes-cluster te testen.

## <a name="prerequisites"></a>Vereisten

- **Azure-abonnement**: als u nog geen abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) aan voordat u begint.

- **Terraform configureren**: volg de aanwijzingen in het artikel [Terraform en toegang tot Azure configureren](terraform-install-configure.md)

- **Azure-brongroep:** Als u geen Azure-brongroep hebt om te gebruiken voor de demo, [maakt u een Azure-brongroep](/azure/azure-resource-manager/manage-resource-groups-portal#create-resource-groups). Let op de naam en locatie van de resourcegroep, aangezien deze waarden worden gebruikt in de demo.

- **Azure service-principal**: volg de aanwijzingen in de sectie **Create the service principal** (de service-principal maken) in het artikel [Create an Azure service principal with Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) (een Azure-service-principal maken met Azure CLI). Noteer de waarden voor appId, displayName en password.

- **De serviceprincipalobject-id verkrijgen:** Voer de volgende opdracht uit in Cloud Shell:`az ad sp list --display-name <displayName>`

## <a name="create-the-directory-structure"></a>De mapstructuur maken

De eerste stap is het maken van een map voor de Terraform-configuratiebestanden voor de oefening.

1. Blader naar [Azure Portal](https://portal.azure.com).

1. Azure [Cloud Shell openen](/azure/cloud-shell/overview).

1. Ga naar de map `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Maak een map met de naam `terraform-aks-appgw-ingress`.

    ```bash
    mkdir terraform-aks-appgw-ingress
    ```

1. Maak de nieuwe directory de actieve directory:

    ```bash
    cd terraform-aks-appgw-ingress
    ```

## <a name="declare-the-azure-provider"></a>De Azure-provider declareren

Maak het Terraform-configuratiebestand waarin de Azure-provider wordt gedeclareerd.

1. Maak in Cloud Shell een bestand met de naam `main.tf`.

    ```bash
    code main.tf
    ```

1. Plak de volgende code in de editor:

    ```hcl
    provider "azurerm" {
      # The "feature" block is required for AzureRM provider 2.x. 
      # If you are using version 1.x, the "features" block is not allowed.
      version = "~>2.0"
      features {}
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. Sla het**&lt;** bestand op ( Ctrl>S ) en sluit de editor af**&lt;(Ctrl>Q).**

## <a name="define-input-variables"></a>Invoervariabelen definiëren

Maak het terraform-configuratiebestand met alle variabelen die nodig zijn voor deze implementatie.

1. Maak in Cloud Shell een bestand met de naam `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Plak de volgende code in de editor:
    
    ```hcl
    variable "resource_group_name" {
      description = "Name of the resource group."
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
      description = "Disk size (in GB) to provision for each of the agent pool nodes. This value ranges from 0 to 1023. Specifying 0 applies the default disk size for that agentVMSize."
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

1. Sla het**&lt;** bestand op ( Ctrl>S ) en sluit de editor af**&lt;(Ctrl>Q).**

## <a name="define-the-resources"></a>De resources definiëren 
Maak een Terraform-configuratiebestand waarin alle resources worden gemaakt. 

1. Maak in Cloud Shell een bestand met de naam `resources.tf`.

    ```bash
    code resources.tf
    ```

1. Plak het volgende codeblok om een lokaal blok te maken voor berekende variabelen die opnieuw kunnen worden gebruikt:

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

1. Plak het volgende codeblok om een gegevensbron voor de groep Resource te maken, de nieuwe gebruikersidentiteit:

    ```hcl
    data "azurerm_resource_group" "rg" {
      name = var.resource_group_name
    }

    # User Assigned Identities 
    resource "azurerm_user_assigned_identity" "testIdentity" {
      resource_group_name = data.azurerm_resource_group.rg.name
      location            = data.azurerm_resource_group.rg.location

      name = "identity1"

      tags = var.tags
    }
    ```

1. Plak het volgende codeblok om basisnetwerkbronnen te maken:

    ```hcl
    resource "azurerm_virtual_network" "test" {
      name                = var.virtual_network_name
      location            = data.azurerm_resource_group.rg.location
      resource_group_name = data.azurerm_resource_group.rg.name
      address_space       = [var.virtual_network_address_prefix]

      subnet {
        name           = var.aks_subnet_name
        address_prefix = var.aks_subnet_address_prefix
      }

      subnet {
        name           = "appgwsubnet"
        address_prefix = var.app_gateway_subnet_address_prefix
      }

      tags = var.tags
    }

    data "azurerm_subnet" "kubesubnet" {
      name                 = var.aks_subnet_name
      virtual_network_name = azurerm_virtual_network.test.name
      resource_group_name  = data.azurerm_resource_group.rg.name
    }

    data "azurerm_subnet" "appgwsubnet" {
      name                 = "appgwsubnet"
      virtual_network_name = azurerm_virtual_network.test.name
      resource_group_name  = data.azurerm_resource_group.rg.name
    }

    # Public Ip 
    resource "azurerm_public_ip" "test" {
      name                         = "publicIp1"
      location                     = data.azurerm_resource_group.rg.location
      resource_group_name          = data.azurerm_resource_group.rg.name
      allocation_method            = "Static"
      sku                          = "Standard"

      tags = var.tags
    }
    ```

1. Plak het volgende codeblok om de toepassingsgatewaybron te maken:

    ```hcl
    resource "azurerm_application_gateway" "network" {
      name                = var.app_gateway_name
      resource_group_name = data.azurerm_resource_group.rg.name
      location            = data.azurerm_resource_group.rg.location

      sku {
        name     = var.app_gateway_sku
        tier     = "Standard_v2"
        capacity = 2
      }

      gateway_ip_configuration {
        name      = "appGatewayIpConfig"
        subnet_id = data.azurerm_subnet.appgwsubnet.id
      }

      frontend_port {
        name = local.frontend_port_name
        port = 80
      }

      frontend_port {
        name = "httpsPort"
        port = 443
      }

      frontend_ip_configuration {
        name                 = local.frontend_ip_configuration_name
        public_ip_address_id = azurerm_public_ip.test.id
      }

      backend_address_pool {
        name = local.backend_address_pool_name
      }

      backend_http_settings {
        name                  = local.http_setting_name
        cookie_based_affinity = "Disabled"
        port                  = 80
        protocol              = "Http"
        request_timeout       = 1
      }

      http_listener {
        name                           = local.listener_name
        frontend_ip_configuration_name = local.frontend_ip_configuration_name
        frontend_port_name             = local.frontend_port_name
        protocol                       = "Http"
      }

      request_routing_rule {
        name                       = local.request_routing_rule_name
        rule_type                  = "Basic"
        http_listener_name         = local.listener_name
        backend_address_pool_name  = local.backend_address_pool_name
        backend_http_settings_name = local.http_setting_name
      }

      tags = var.tags

      depends_on = ["azurerm_virtual_network.test", "azurerm_public_ip.test"]
    }
    ```

1. Plak het volgende codeblok om roltoewijzingen te maken:

    ```hcl
    resource "azurerm_role_assignment" "ra1" {
      scope                = data.azurerm_subnet.kubesubnet.id
      role_definition_name = "Network Contributor"
      principal_id         = var.aks_service_principal_object_id 

      depends_on = ["azurerm_virtual_network.test"]
    }

    resource "azurerm_role_assignment" "ra2" {
      scope                = azurerm_user_assigned_identity.testIdentity.id
      role_definition_name = "Managed Identity Operator"
      principal_id         = var.aks_service_principal_object_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity"]
    }

    resource "azurerm_role_assignment" "ra3" {
      scope                = azurerm_application_gateway.network.id
      role_definition_name = "Contributor"
      principal_id         = azurerm_user_assigned_identity.testIdentity.principal_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }

    resource "azurerm_role_assignment" "ra4" {
      scope                = data.azurerm_resource_group.rg.id
      role_definition_name = "Reader"
      principal_id         = azurerm_user_assigned_identity.testIdentity.principal_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    ```

1. Plak het volgende codeblok om het Kubernetes-cluster te maken:

    ```hcl
    resource "azurerm_kubernetes_cluster" "k8s" {
      name       = var.aks_name
      location   = data.azurerm_resource_group.rg.location
      dns_prefix = var.aks_dns_prefix

      resource_group_name = data.azurerm_resource_group.rg.name

      linux_profile {
        admin_username = var.vm_user_name

        ssh_key {
          key_data = file(var.public_ssh_key_path)
        }
      }

      addon_profile {
        http_application_routing {
          enabled = false
        }
      }

      default_node_pool {
        name            = "agentpool"
        node_count      = var.aks_agent_count
        vm_size         = var.aks_agent_vm_size
        os_disk_size_gb = var.aks_agent_os_disk_size
        vnet_subnet_id  = data.azurerm_subnet.kubesubnet.id
      }

      service_principal {
        client_id     = var.aks_service_principal_app_id
        client_secret = var.aks_service_principal_client_secret
      }

      network_profile {
        network_plugin     = "azure"
        dns_service_ip     = var.aks_dns_service_ip
        docker_bridge_cidr = var.aks_docker_bridge_cidr
        service_cidr       = var.aks_service_cidr
      }

      depends_on = ["azurerm_virtual_network.test", "azurerm_application_gateway.network"]
      tags       = var.tags
    }

    ```

1. Sla het**&lt;** bestand op ( Ctrl>S ) en sluit de editor af**&lt;(Ctrl>Q).**

De code in deze sectie bepaalt de naam van het cluster, de locatie en de resource_group_name. De `dns_prefix` waarde - die deel uitmaakt van de volledig gekwalificeerde domeinnaam (FQDN) die wordt gebruikt om toegang te krijgen tot het cluster - is ingesteld.

Met `linux_profile` de record u de instellingen configureren waarmee u inloggen op de werknemersknooppunten met SSH.

Met AKS betaalt u alleen voor de werkknooppunten. De `agent_pool_profile` record configureert de details voor deze werkknooppunten. Het `agent_pool_profile record` omvat het aantal te maken werkknooppunten en het type werknemersknooppunten. Als u het cluster in de toekomst moet opschalen `count` of schalen, wijzigt u de waarde in deze record.

## <a name="create-a-terraform-output-file"></a>Een Terraform-uitvoerbestand maken

[Met Terraform-uitvoer](https://www.terraform.io/docs/configuration/outputs.html) u waarden definiëren die aan de gebruiker worden gemarkeerd wanneer `terraform output` Terraform een plan toepast en met de opdracht kunnen worden opgevraagd. In deze sectie maakt u een uitvoerbestand waarmee via [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) toegang wordt verkregen tot het cluster.

1. Maak in Cloud Shell een bestand met de naam `output.tf`.

    ```bash
    code output.tf
    ```

1. Plak de volgende code in de editor:

    ```hcl
    output "client_key" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_key
    }

    output "client_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate
    }

    output "cluster_ca_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate
    }

    output "cluster_username" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.username
    }

    output "cluster_password" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.password
    }

    output "kube_config" {
        value = azurerm_kubernetes_cluster.k8s.kube_config_raw
    }

    output "host" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.host
    }

    output "identity_resource_id" {
        value = azurerm_user_assigned_identity.testIdentity.id
    }

    output "identity_client_id" {
        value = azurerm_user_assigned_identity.testIdentity.client_id
    }
    ```

1. Sla het**&lt;** bestand op ( Ctrl>S ) en sluit de editor af**&lt;(Ctrl>Q).**

## <a name="configure-azure-storage-to-store-terraform-state"></a>Azure-opslag configureren om de status Terraform op te slaan

In Terraform wordt de status lokaal bijgehouden via het bestand `terraform.tfstate`. Dit patroon werkt goed in een omgeving met één persoon. In een praktischere omgeving met meerdere personen moet u echter met behulp van [Azure Storage](/azure/storage/) de status bijhouden op de server. In deze sectie leert u de benodigde opslagaccountgegevens op te halen en een opslagcontainer te maken. De Terraform staatsinformatie wordt vervolgens in die container opgeslagen.

1. Selecteer **opslagaccounts**in de Azure-portal onder **Azure-services**. (Als de optie **Opslagaccounts** niet zichtbaar is op de hoofdpagina, selecteert u **Meer services** en zoekt en selecteert u deze.)

1. Selecteer op de pagina **Opslagaccounts** de naam van het opslagaccount waarop Terraform de status moet opslaan. U kunt bijvoorbeeld het opslagaccount gebruiken dat u hebt gemaakt toen u Cloud Shell de eerste keer opende.  De opslagaccountnaam die door Cloud Shell wordt gemaakt, begint gewoonlijk met `cs`, gevolgd door een willekeurige tekenreeks met cijfers en letters. 

    Let op het opslagaccount dat u selecteert, omdat u het later nodig hebt.

1. Selecteer op de opslagaccountpagina de optie **Toegangssleutels**.

    ![Opslagaccountmenu](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account.png)

1. Noteer de waarde van de **key1-** **sleutel**. (Als u het pictogram rechts van de sleutel selecteert, wordt de waarde gekopieerd naar het klembord.)

    ![Toegangssleutels voor opslagaccount](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account-access-key.png)

1. Maak in Cloud Shell een container in uw Azure-opslagaccount. Vervang de tijdelijke aanduidingen door de juiste waarden voor uw Azure-opslagaccount.

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Het Kubernetes-cluster maken
In deze sectie ziet u hoe u de opdracht `terraform init` gebruikt om de resources te maken die zijn gedefinieerd in de configuratiebestanden die u in de vorige secties hebt gemaakt.

1. In Cloud Shell, initialiseren Terraform. Vervang de tijdelijke aanduidingen door de juiste waarden voor uw Azure-opslagaccount.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
  
    De `terraform init` opdracht geeft het succes weer van het initialiseren van de backend en providerplug-in:

    ![Voorbeeld van de resultaten van de opdracht 'terraform init'](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-init-complete.png)

1. Maak in Cloud Shell `terraform.tfvars`een bestand met de naam:

    ```bash
    code terraform.tfvars
    ```

1. Plak de volgende variabelen die eerder zijn gemaakt in de editor. Gebruik . `az account list-locations`

    ```hcl
    resource_group_name = "<Name of the Resource Group already created>"

    location = "<Location of the Resource Group>"
      
    aks_service_principal_app_id = "<Service Principal AppId>"
      
    aks_service_principal_client_secret = "<Service Principal Client Secret>"
      
    aks_service_principal_object_id = "<Service Principal Object Id>"
        
    ```

1. Sla het**&lt;** bestand op ( Ctrl>S ) en sluit de editor af**&lt;(Ctrl>Q).**

1. Voer de opdracht `terraform plan` uit om het Terraform-plan te maken waarmee de infrastructuurelementen worden gedefinieerd. 

    ```bash
    terraform plan -out out.plan
    ```

    De `terraform plan` opdracht toont de bronnen die `terraform apply` worden gemaakt wanneer u de opdracht uitvoert:

    ![Voorbeeld van de resultaten van de opdracht 'terraform plan'](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-plan-complete.png)

1. Voer de opdracht `terraform apply` uit om het plan voor het maken van het Kubernetes-cluster toe te passen. Het proces om een Kubernetes-cluster te maken kan enkele minuten duren, wat resulteert in de timing van de Cloud Shell-sessie. Als de Cloud Shell-sessie een time-out heeft, u de stappen volgen in de sectie 'Herstellen van een time-out van Cloud Shell' zodat u de zelfstudie voltooien.

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

## <a name="install-azure-ad-pod-identity"></a>Azure AD Pod-identiteit installeren

Azure Active Directory Pod Identity biedt op token gebaseerde toegang tot [Azure Resource Manager.](/azure/azure-resource-manager/resource-group-overview)

[Azure AD Pod Identity](https://github.com/Azure/aad-pod-identity) voegt de volgende componenten toe aan uw Kubernetes-cluster:

  - Kubernetes [CRD's](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/): `AzureIdentity`, `AzureAssignedIdentity``AzureIdentityBinding`
  - [MIC-component (Managed Identity Controller)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic)
  - [Component Node Managed Identity (NMI)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi)

Als RBAC is **ingeschakeld,** voert u de volgende opdracht uit om Azure AD Pod Identity op uw cluster te installeren:

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
```

Als RBAC is **uitgeschakeld,** voert u de volgende opdracht uit om Azure AD Pod Identity in uw cluster te installeren:

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
```

## <a name="install-helm"></a>Helm installeren

De code in [Helm](/azure/aks/kubernetes-helm) deze sectie maakt gebruik van `application-gateway-kubernetes-ingress` Helm - Kubernetes package manager - om het pakket te installeren:

1. Als RBAC is **ingeschakeld,** voert u de volgende set opdrachten uit om Helm te installeren en te configureren:

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

1. Als RBAC is **uitgeschakeld,** voert u de volgende opdracht uit om Helm te installeren en te configureren:

    ```bash
    helm init
    ```

1. Voeg de AGIC Helm repository toe:

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="install-ingress-controller-helm-chart"></a>Helmdiagram voor invallencontroller installeren

1. Downloaden `helm-config.yaml` om AGIC te configureren:

    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```

1. Bewerk `helm-config.yaml` de juiste waarden `appgw` `armAuth` voor en secties en voer de juiste waarden in.

    ```bash
    code helm-config.yaml
    ```

    De waarden worden als volgt beschreven:

    - `verbosityLevel`: Hiermee stelt u het niveau van de agic-houtkap in. Zie [Logboekregistratieniveaus](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels) voor mogelijke waarden.
    - `appgw.subscriptionId`: De Azure-abonnements-id voor de App-gateway. Voorbeeld: `a123b234-a3b4-557d-b2df-a0bc12de1234`
    - `appgw.resourceGroup`: Naam van de Azure Resource Group waarin App Gateway is gemaakt. 
    - `appgw.name`: Naam van de Application Gateway. Bijvoorbeeld: `applicationgateway1`.
    - `appgw.shared`: Deze booleaanse vlag `false`moet standaard worden ingesteld op . Stel `true` in op als u een [Shared App Gateway](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway)nodig hebt.
    - `kubernetes.watchNamespace`: Geef de naamruimte op, waarop AGIC moet kijken. De naamruimte kan een enkele tekenreekswaarde zijn of een door komma's gescheiden lijst met naamruimten. Als u deze variabele buiten laat of deze instelt op lege of lege tekenreeks, wordt in Ingress Controller alle toegankelijke naamruimten geobserveerd.
    - `armAuth.type`: Een waarde `aadPodIdentity` `servicePrincipal`van een van beide of .
    - `armAuth.identityResourceID`: Resource-ID van de beheerde identiteit.
    - `armAuth.identityClientId`: De client-id van de identiteit.
    - `armAuth.secretJSON`: Alleen nodig wanneer het type `armAuth.type` Service Principal `servicePrincipal`Secret wordt gekozen (wanneer is ingesteld op ).

    Belangrijkste opmerkingen:
    - De `identityResourceID` waarde wordt gemaakt in het terraformulierscript `echo "$(terraform output identity_resource_id)"`en kan worden gevonden door uit te voeren: .
    - De `identityClientID` waarde wordt gemaakt in het terraformulierscript `echo "$(terraform output identity_client_id)"`en kan worden gevonden door uit te voeren: .
    - De `<resource-group>` waarde is de resourcegroep van uw App Gateway.
    - De `<identity-name>` waarde is de naam van de gemaakte identiteit.
    - Alle identiteiten voor een bepaald abonnement `az identity list`kunnen worden vermeld met behulp van: .

1. Installeer het pakket ingress-controller van Application Gateway:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

### <a name="install-a-sample-app"></a>Een voorbeeld-app installeren

Zodra u de App Gateway, AKS en AGIC hebt geïnstalleerd, u een voorbeeld-app installeren via [Azure Cloud Shell:](https://shell.azure.com/)

1. Gebruik de opdracht krul om het YAML-bestand te downloaden:

    ```bash
    curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
    ```

2. Pas het YAML-bestand toe:

    ```bash
    kubectl apply -f aspnetapp.yaml
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u niet meer nodig bent, verwijdert u de bronnen die in dit artikel zijn gemaakt.  

Vervang de tijdelijke aanduiding door de juiste waarde. Alle bronnen binnen de opgegeven resourcegroep worden verwijderd.

```azurecli
az group delete -n <resource-group>
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Controller voor inkomend verkeer van Application Gateway](https://azure.github.io/application-gateway-kubernetes-ingress/)