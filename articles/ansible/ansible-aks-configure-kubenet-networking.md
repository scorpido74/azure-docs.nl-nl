---
title: 'Zelf studie: kubenet-netwerken configureren in azure Kubernetes service (AKS) met behulp van Ansible'
description: Meer informatie over het gebruik van Ansible voor het configureren van kubenet-netwerken in azure Kubernetes service (AKS)-cluster
keywords: ansible, azure, devops, bash, Cloud shell, Playbook, AKS, container, AKS, kubernetes
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: bfb19371ad651439c087cebd03023d48852ee2df
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156889"
---
# <a name="tutorial-configure-kubenet-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Zelf studie: kubenet-netwerken configureren in azure Kubernetes service (AKS) met behulp van Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Met AKS kunt u een cluster implementeren met behulp van de volgende netwerk modellen:

- [Kubenet-netwerken](/azure/aks/configure-kubenet) : netwerk bronnen worden doorgaans gemaakt en geconfigureerd als het AKS-cluster wordt geïmplementeerd.
- [Azure container Networking interface (cni)-netwerken](/azure/aks/configure-azure-cni) -AKS-cluster is verbonden met bestaande virtuele netwerk bronnen en-configuraties.

Zie [Network concepten for Applications in AKS](/azure/aks/concepts-network)(Engelstalig) voor meer informatie over netwerken voor uw toepassingen in AKS.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Een AKS-cluster maken
> * Azure kubenet-netwerken configureren

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-virtual-network-and-subnet"></a>Een virtueel netwerk en een subnet maken

Met de Playbook-code in deze sectie worden de volgende Azure-resources gemaakt:

- Virtueel netwerk
- Subnet binnen het virtuele netwerk

Sla het volgende playbook op als `vnet.yml`:

```yml
- name: Create vnet
  azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefixes_cidr:
          - 10.0.0.0/8

- name: Create subnet
  azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefix_cidr: 10.240.0.0/16
      virtual_network_name: "{{ name }}"
  register: subnet
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Een AKS-cluster maken in het virtuele netwerk

Met de Playbook-code in deze sectie maakt u een AKS-cluster in een virtueel netwerk. 

Sla het volgende playbook op als `aks.yml`:

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with vnet
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
          vnet_subnet_id: "{{ vnet_subnet_id }}"
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      network_profile:
          network_plugin: kubenet
          pod_cidr: 192.168.0.0/16
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.0.0.10
          service_cidr: 10.0.0.0/16
  register: aks
```

Hier volgen enkele belang rijke opmerkingen waarmee u rekening moet houden wanneer u werkt met de voor beeld-Playbook:

- Gebruik `azure_rm_aks_version` module om de ondersteunde versie te vinden.
- Het `vnet_subnet_id` is het subnet dat in de vorige sectie is gemaakt.
- De `network_profile` definieert de eigenschappen voor de kubenet-netwerk invoeg toepassing.
- De `service_cidr` wordt gebruikt voor het toewijzen van interne services in het AKS-cluster aan een IP-adres. Dit IP-adres bereik moet een adres ruimte zijn die niet ergens anders in uw netwerk wordt gebruikt. 
- Het `dns_service_ip` adres moet het adres '. 10 ' zijn van het IP-adres bereik van uw service.
- De `pod_cidr` moet een grote adres ruimte zijn die elders niet in uw netwerk omgeving wordt gebruikt. Het adres bereik moet groot genoeg zijn voor het aantal knoop punten dat u naar verwachting omhoog wilt schalen. U kunt dit adres bereik niet wijzigen wanneer het cluster is geïmplementeerd.
- Het IP-adres bereik van Pod wordt gebruikt om een/24-adres ruimte toe te wijzen aan elk knoop punt in het cluster. In het volgende voor beeld wijst de `pod_cidr` van 192.168.0.0/16 het eerste knoop punt 192.168.0.0/24, het tweede knoop punt 192.168.1.0/24 en het derde knoop punt 192.168.2.0/24 toe.
- Naarmate het cluster wordt geschaald of bijgewerkt, blijft Azure een IP-adres bereik van pod toewijzen aan elk nieuw knoop punt.
- De Playbook laadt `ssh_key` van `~/.ssh/id_rsa.pub`. Als u het wijzigt, gebruikt u de indeling met één regel, te beginnen met ' ssh-rsa ' (zonder de aanhalings tekens).
- De waarden `client_id` en `client_secret` worden geladen vanuit `~/.azure/credentials`, het standaard referentie bestand. U kunt deze waarden instellen voor uw service-principal of deze waarden laden vanuit omgevings variabelen:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="associate-the-network-resources"></a>De netwerk resources koppelen

Wanneer u een AKS-cluster maakt, worden er een netwerk beveiligings groep en route tabel gemaakt. Deze resources worden beheerd door AKS en bijgewerkt wanneer u Services maakt en weergeeft. Koppel de netwerk beveiligings groep en de route tabel als volgt aan het subnet van het virtuele netwerk. 

Sla de volgende Playbook op als `associate.yml`.

```yml
- name: Get route table
  azure_rm_routetable_facts:
      resource_group: "{{ node_resource_group }}"
  register: routetable

- name: Get network security group
  azure_rm_securitygroup_facts:
      resource_group: "{{ node_resource_group }}"
  register: nsg

- name: Parse subnet id
  set_fact:
      subnet_name: "{{ vnet_subnet_id | regex_search(subnet_regex, '\\1') }}"
      subnet_rg: "{{ vnet_subnet_id | regex_search(rg_regex, '\\1') }}"
      subnet_vn: "{{ vnet_subnet_id | regex_search(vn_regex, '\\1') }}"
  vars:
      subnet_regex: '/subnets/(.+)'
      rg_regex: '/resourceGroups/(.+?)/'
      vn_regex: '/virtualNetworks/(.+?)/'

- name: Associate network resources with the node subnet
  azure_rm_subnet:
      name: "{{ subnet_name[0] }}"
      resource_group: "{{  subnet_rg[0] }}"
      virtual_network_name: "{{ subnet_vn[0] }}"
      security_group: "{{ nsg.ansible_facts.azure_securitygroups[0].id }}"
      route_table: "{{ routetable.route_tables[0].id }}"
```

Hier volgen enkele belang rijke opmerkingen waarmee u rekening moet houden wanneer u werkt met de voor beeld-Playbook:

- De `node_resource_group` is de naam van de resource groep waarin de AKS-knoop punten worden gemaakt.
- Het `vnet_subnet_id` is het subnet dat in de vorige sectie is gemaakt.


## <a name="run-the-sample-playbook"></a>De voorbeeld Playbook uitvoeren

In deze sectie vindt u de volledige voorbeeld Playbook die de taken aanroept die in dit artikel worden gemaakt. 

Sla het volgende playbook op als `aks-kubenet.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Associate network resources with the node subnet
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
           node_resource_group: "{{ aks.node_resource_group }}"
       include_tasks: associate.yml

     - name: Get details of the AKS
       azure_rm_aks_facts:
           name: "{{ name }}"
           resource_group: "{{ resource_group }}"
           show_kubeconfig: user
       register: output

     - name: Show AKS cluster detail
       debug:
           var: output.aks[0]
```

Breng de volgende wijzigingen aan in de sectie `vars`:

- Wijzig de `aksansibletest` waarde voor de `resource_group` sleutel in de naam van de resource groep.
- Wijzig de `aksansibletest`-waarde voor de `name` sleutel in de naam van uw AKS.
- Wijzig de `eastus`-waarde voor de `Location` sleutel in de locatie van de resource groep.

Voer de volledige Playbook uit met de opdracht `ansible-playbook`:

```bash
ansible-playbook aks-kubenet.yml
```

Als de Playbook wordt uitgevoerd, worden de resultaten weer gegeven die vergelijkbaar zijn met de volgende uitvoer:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exist] 
ok: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-kubenet/vnet.yml for localhost

TASK [Create vnet] 
ok: [localhost]

TASK [Create subnet] 
ok: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-kubenet/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Associate network resources with the node subnet] 
included: /home/devops/aks-kubenet/associate.yml for localhost

TASK [Get route table] 
ok: [localhost]

TASK [Get network security group] 
ok: [localhost]

TASK [Parse subnet id] 
ok: [localhost]

TASK [Associate network resources with the node subnet] 
changed: [localhost]

TASK [Get details of the AKS] 
ok: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "output.aks[0]": {
        "id": /subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "apiVersion: ...",
        "location": "eastus",
        "name": "aksansibletest",
        "properties": {
            "agentPoolProfiles": [
                {
                    "count": 3,
                    "maxPods": 110,
                    "name": "nodepool1",
                    "osDiskSizeGB": 100,
                    "osType": "Linux",
                    "storageProfile": "ManagedDisks",
                    "vmSize": "Standard_D2_v2",
                    "vnetSubnetID": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
                }
            ],
            "dnsPrefix": "aksansibletest",
            "enableRBAC": false,
            "fqdn": "aksansibletest-cda2b56c.hcp.eastus.azmk8s.io",
            "kubernetesVersion": "1.12.6",
            "linuxProfile": {
                "adminUsername": "azureuser",
                "ssh": {
                    "publicKeys": [
                        {
                            "keyData": "ssh-rsa ..."
                        }
                    ]
                }
            },
            "networkProfile": {
                "dnsServiceIP": "10.0.0.10",
                "dockerBridgeCidr": "172.17.0.1/16",
                "networkPlugin": "kubenet",
                "podCidr": "192.168.0.0/16",
                "serviceCidr": "10.0.0.0/16"
            },
            "nodeResourceGroup": "MC_aksansibletest_pcaksansibletest_eastus",
            "provisioningState": "Succeeded",
            "servicePrincipalProfile": {
                "clientId": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
            }
        },
        "type": "Microsoft.ContainerService/ManagedClusters"
    }
}

PLAY RECAP 
localhost                  : ok=15   changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, verwijdert u de resources die u in dit artikel hebt gemaakt. 

Sla de volgende code op als `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

Vervang in het gedeelte `vars` de tijdelijke aanduiding `{{ resource_group_name }}` door de naam van uw resource groep.

Voer de Playbook uit met de opdracht `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studie: Azure container Networking interface (CNI)-netwerken configureren in AKS met behulp van Ansible](./ansible-aks-configure-cni-networking.md)