---
title: Zelfstudie - Webverkeer beheren met Azure Application Gateway met Ansible
description: Meer informatie over hoe u Ansible gebruikt om een Azure Application Gateway te maken en te configureren voor het beheren van webverkeer
keywords: ansible, azure, devops, bash, playbook, application gateway, load balancer, webverkeer
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 07f75e39b8c6f592ecd4c48697527493b1109bb9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156611"
---
# <a name="tutorial-manage-web-traffic-with-azure-application-gateway-using-ansible"></a>Zelfstudie: Webverkeer beheren met Azure Application Gateway met Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[Azure Application Gateway](/azure/application-gateway/overview) is een load balancer voor webverkeer waarmee u het verkeer naar uw webapps kunt beheren. Op basis van het IP-adres en de poort van de bron leiden traditionele load balancers het verkeer naar een IP-adres en de poort van de bestemming. Application Gateway geeft u een fijner niveau van controle waar het verkeer kan worden gerouteerd op basis van de URL. U bijvoorbeeld definiëren `images` dat als het pad van DE URL is, het verkeer wordt doorgestuurd naar een specifieke set servers (een pool bekend) die zijn geconfigureerd voor afbeeldingen.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Een netwerk instellen
> * Twee Azure-containerinstanties maken met HTTPD-installatiekopieën
> * Een toepassingsgateway maken die werkt met de Azure-containerinstanties in de serverpool

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Met de playbook-code in deze sectie wordt een Azure-brongroep geveld. Een resourcegroep is een logische container waarin Azure-resources zijn geconfigureerd.  

Sla het volgende playbook op als `rg.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

Zie de volgende opmerkingen voordat u het draaiboek uitvoert:

- De naam van `myResourceGroup`de resourcegroep is . Deze waarde wordt gebruikt tijdens de zelfstudie.
- De resourcegroep wordt `eastus` gemaakt op de locatie.

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Netwerkbronnen maken

De playbook-code in deze sectie maakt een virtueel netwerk om de toepassingsgateway in staat te stellen te communiceren met andere bronnen.

Sla het volgende playbook op als `vnet_create.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    vnet_name: myVNet 
    subnet_name: myAGSubnet 
    publicip_name: myAGPublicIPAddress
    publicip_domain: mydomain
  tasks:
    - name: Create a virtual network
      azure_rm_virtualnetwork:
        name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
            - 10.1.0.0/16
            - 172.100.0.0/16
        dns_servers:
            - 127.0.0.1
            - 127.0.0.2

    - name: Create a subnet
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: 10.1.0.0/24

    - name: Create a public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}" 
        allocation_method: Dynamic
        name: "{{ publicip_name }}"
        domain_name_label: "{{ publicip_domain }}"
```

Zie de volgende opmerkingen voordat u het draaiboek uitvoert:

* De `vars` sectie bevat de waarden die worden gebruikt om de netwerkbronnen te maken. 
* U moet deze waarden voor uw specifieke omgeving wijzigen.

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>Servers maken

Met de playbook-code in deze sectie worden twee Azure-containerexemplaren gemaakt met HTTPD-afbeeldingen die moeten worden gebruikt als webservers voor de toepassingsgateway.  

Sla het volgende playbook op als `aci_create.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Create a container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_1_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80

    - name: Create another container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_2_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80
```

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>De toepassingsgateway maken

Met de playbook-code in deze `myAppGateway`sectie wordt een toepassingsgateway met de naam .  

Sla het volgende playbook op als `appgw_create.yml`:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    vnet_name: myVNet
    subnet_name: myAGSubnet
    location: eastus
    publicip_name: myAGPublicIPAddress
    appgw_name: myAppGateway
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Get info of Subnet
      azure_rm_resource_facts:
        api_version: '2018-08-01'
        resource_group: "{{ resource_group }}"
        provider: network
        resource_type: virtualnetworks
        resource_name: "{{ vnet_name }}"
        subresource:
          - type: subnets
            name: "{{ subnet_name }}"
      register: subnet

    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_1_name }}"
      register: aci_1_output
    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_2_name }}"
      register: aci_2_output

    - name: Create instance of Application Gateway
      azure_rm_appgateway:
        resource_group: "{{ resource_group }}"
        name: "{{ appgw_name }}"
        sku:
          name: standard_small
          tier: standard
          capacity: 2
        gateway_ip_configurations:
          - subnet:
              id: "{{ subnet.response[0].id }}"
            name: appGatewayIP
        frontend_ip_configurations:
          - public_ip_address: "{{ publicip_name }}"
            name: appGatewayFrontendIP
        frontend_ports:
          - port: 80
            name: appGatewayFrontendPort
        backend_address_pools:
          - backend_addresses:
              - ip_address: "{{ aci_1_output.response[0].properties.ipAddress.ip }}"
              - ip_address: "{{ aci_2_output.response[0].properties.ipAddress.ip }}"
            name: appGatewayBackendPool
        backend_http_settings_collection:
          - port: 80
            protocol: http
            cookie_based_affinity: enabled
            name: appGatewayBackendHttpSettings
        http_listeners:
          - frontend_ip_configuration: appGatewayFrontendIP
            frontend_port: appGatewayFrontendPort
            name: appGatewayHttpListener
        request_routing_rules:
          - rule_type: Basic
            backend_address_pool: appGatewayBackendPool
            backend_http_settings: appGatewayBackendHttpSettings
            http_listener: appGatewayHttpListener
            name: rule1
```

Zie de volgende opmerkingen voordat u het draaiboek uitvoert:

* `appGatewayIP`wordt gedefinieerd in `gateway_ip_configurations` het blok. Er is een subnetverwijzing vereist voor de IP-configuratie van de gateway.
* `appGatewayBackendPool`wordt gedefinieerd in `backend_address_pools` het blok. Een toepassingsgateway moet ten minste één back-endadresgroep hebben.
* `appGatewayBackendHttpSettings`wordt gedefinieerd in `backend_http_settings_collection` het blok. Hierin wordt aangegeven dat poort 80 en een HTTP-protocol worden gebruikt voor communicatie.
* `appGatewayHttpListener`wordt gedefinieerd in `backend_http_settings_collection` het blok. Dit is de standaard-listener die aan appGatewayBackendPool is gekoppeld.
* `appGatewayFrontendIP`wordt gedefinieerd in `frontend_ip_configurations` het blok. Hiermee wordt myAGPublicIPAddress aan appGatewayHttpListener toegewezen.
* `rule1`wordt gedefinieerd in `request_routing_rules` het blok. Dit is de standaardregel voor doorsturen die aan appGatewayHttpListener is gekoppeld.

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook appgw_create.yml
```

Het kan enkele minuten duren voordat de toepassingsgateway is gemaakt.

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

1. In de sectie [Een resourcegroep maken](#create-a-resource-group) geeft u een locatie op. Let op de waarde ervan.

1. In de sectie [Netwerkbronnen maken](#create-network-resources) geeft u het domein op. Let op de waarde ervan.

1. Voor de test-URL door het volgende patroon `http://<domain>.<location>.cloudapp.azure.com`te vervangen door de locatie en het domein: .

1. Blader naar de url van de test.

1. Als u de volgende pagina ziet, werkt de toepassingsgateway zoals verwacht.

    ![Testen of een toepassingsgateway werkt](media/ansible-application-gateway-configure/application-gateway.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u niet meer nodig bent, verwijdert u de bronnen die in dit artikel zijn gemaakt. 

Sla de volgende `cleanup.yml`code op als :

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Ansible in Azure](/azure/ansible/)