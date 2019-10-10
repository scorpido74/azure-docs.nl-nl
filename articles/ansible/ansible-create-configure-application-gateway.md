---
title: 'Zelf studie: webverkeer beheren met Azure-toepassing gateway met behulp van Ansible'
description: Meer informatie over hoe u Ansible gebruikt om een Azure Application Gateway te maken en te configureren voor het beheren van webverkeer
keywords: ansible, azure, devops, bash, playbook, application gateway, load balancer, webverkeer
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 1dd547fb59a41a90de18d595a392b64ef518023a
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241891"
---
# <a name="tutorial-manage-web-traffic-with-azure-application-gateway-using-ansible"></a>Zelf studie: webverkeer beheren met Azure-toepassing gateway met behulp van Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[Azure Application Gateway](/azure/application-gateway/overview) is een load balancer voor webverkeer waarmee u het verkeer naar uw webapps kunt beheren. Op basis van het bron-IP-adres en de poort, routert traditionele load balancers verkeer naar een doel-IP-adres en-poort. Application Gateway geeft u een nauw keuriger niveau van het beheer van verkeer op basis van de URL. U kunt bijvoorbeeld definiëren dat als `images` het pad van de URL is, verkeer wordt doorgestuurd naar een specifieke set servers (ook wel een groep genoemd) die is geconfigureerd voor installatie kopieën.

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

Met de Playbook-code in deze sectie maakt u een Azure-resource groep. Een resource groep is een logische container waarin Azure-resources worden geconfigureerd.  

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

Voor het uitvoeren van de Playbook raadpleegt u de volgende opmerkingen:

- De naam van de resource groep is `myResourceGroup`. Deze waarde wordt in de zelf studie gebruikt.
- De resource groep wordt gemaakt op de locatie @no__t 0.

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Netwerkbronnen maken

Met de Playbook-code in deze sectie wordt een virtueel netwerk gemaakt waarmee de toepassings gateway kan communiceren met andere resources.

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

Voor het uitvoeren van de Playbook raadpleegt u de volgende opmerkingen:

* De sectie `vars` bevat de waarden die worden gebruikt voor het maken van de netwerk resources. 
* U moet deze waarden wijzigen voor uw specifieke omgeving.

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>Servers maken

Met de Playbook-code in deze sectie maakt u twee Azure-container instanties met HTTPD-installatie kopieën die moeten worden gebruikt als webservers voor de toepassings gateway.  

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

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>De toepassingsgateway maken

Met de Playbook-code in deze sectie maakt u een toepassings gateway met de naam `myAppGateway`.  

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

Voor het uitvoeren van de Playbook raadpleegt u de volgende opmerkingen:

* `appGatewayIP` is gedefinieerd in het `gateway_ip_configurations`-blok. Er is een subnetverwijzing vereist voor de IP-configuratie van de gateway.
* `appGatewayBackendPool` is gedefinieerd in het `backend_address_pools`-blok. Een toepassingsgateway moet ten minste één back-endadresgroep hebben.
* `appGatewayBackendHttpSettings` is gedefinieerd in het `backend_http_settings_collection`-blok. Hiermee geeft u op dat poort 80 en een HTTP-protocol worden gebruikt voor communicatie.
* `appGatewayHttpListener` is gedefinieerd in het `backend_http_settings_collection`-blok. Dit is de standaard-listener die aan appGatewayBackendPool is gekoppeld.
* `appGatewayFrontendIP` is gedefinieerd in het `frontend_ip_configurations`-blok. Hiermee wordt myAGPublicIPAddress aan appGatewayHttpListener toegewezen.
* `rule1` is gedefinieerd in het `request_routing_rules`-blok. Dit is de standaardregel voor doorsturen die aan appGatewayHttpListener is gekoppeld.

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook appgw_create.yml
```

Het kan enkele minuten duren voordat de toepassingsgateway is gemaakt.

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

1. In de sectie [een resource groep maken](#create-a-resource-group) geeft u een locatie op. Noteer de waarde ervan.

1. In de sectie [netwerk bronnen maken](#create-network-resources) geeft u het domein op. Noteer de waarde ervan.

1. Voor de test-URL door het volgende patroon te vervangen door de locatie en het domein: `http://<domain>.<location>.cloudapp.azure.com`.

1. Blader naar de test-URL.

1. Als u de volgende pagina ziet, werkt de toepassingsgateway zoals verwacht.

    ![Testen of een toepassingsgateway werkt](media/ansible-application-gateway-configure/application-gateway.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, verwijdert u de resources die u in dit artikel hebt gemaakt. 

Sla de volgende code op als `cleanup.yml`:

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

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Ansible in Azure](/azure/ansible/)