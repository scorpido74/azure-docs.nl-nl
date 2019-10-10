---
title: 'Zelf studie: apps configureren in Azure App Service met behulp van Ansible'
description: Meer informatie over het maken van een app in Azure App Service met Java 8 en de Tomcat-container runtime
keywords: ansible, azure, devops, bash, playbook, Azure App Service, web-app, Java
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 5104db6bd7fa57600c7212e041263971ca4c91d4
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242052"
---
# <a name="tutorial-configure-apps-in-azure-app-service-using-ansible"></a>Zelf studie: apps in Azure App Service configureren met behulp van Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Een app maken in Azure App Service met Java 8 en de Tomcat-container runtime
> * Een Azure Traffic Manager-profiel maken
> * Een Traffic Manager-eind punt definiëren met behulp van de gemaakte app

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-basic-app-service"></a>Een eenvoudige app service maken

De Playbook-code in deze sectie definieert de volgende resources:

* De Azure-resource groep waarbinnen het App Service plan en de app worden geïmplementeerd
* App service op Linux met Java 8 en de Tomcat-container runtime

Sla het volgende playbook op als `firstwebapp.yml`:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    webapp_name: myfirstWebApp
    plan_name: myAppServicePlan
    location: eastus
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create App Service on Linux with Java Runtime
      azure_rm_webapp:
        resource_group: "{{ resource_group }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        frameworks:
          - name: "java"
            version: "8"
            settings:
              java_container: tomcat
              java_container_version: 8.5
```

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook firstwebapp.yml
```

Nadat de Playbook is uitgevoerd, ziet u uitvoer die vergelijkbaar is met de volgende resultaten:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="create-an-app-and-use-azure-traffic-manager"></a>Een app maken en Azure Traffic Manager gebruiken

Met [Azure Traffic Manager](/azure/app-service/web-sites-traffic-manager) kunt u bepalen hoe aanvragen van webclients worden gedistribueerd naar apps in azure app service. Wanneer App Service-eindpunten worden toegevoegd aan een Azure Traffic Manager-profiel, wordt met Traffic Manager de status van de App Service-apps bijgehouden. Statussen omvatten: actief, gestopt en verwijderd. Traffic Manager wordt gebruikt om te bepalen welke eind punten het verkeer moeten ontvangen.

In App Service wordt een app uitgevoerd in een [App Service-plan](/azure/app-service/overview-hosting-plans). Een App Service plan definieert een set reken resources voor het uitvoeren van een app. U kunt het App Service-plan en de web-app in verschillende groepen beheren.

De Playbook-code in deze sectie definieert de volgende resources:

* De Azure-resource groep waarbinnen het App Service plan wordt geïmplementeerd
* App Service-plan
* De Azure-resource groep waarbinnen de app is geïmplementeerd
* App service op Linux met Java 8 en de Tomcat-container runtime
* Traffic Manager-profiel
* Traffic Manager eind punt met behulp van de gemaakte app

Sla het volgende playbook op als `webapp.yml`:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group_webapp: myResourceGroupWebapp
    resource_group: myResourceGroup
    webapp_name: myLinuxWebApp
    plan_name: myAppServicePlan
    location: eastus
    traffic_manager_profile_name: myTrafficManagerProfile
    traffic_manager_endpoint_name: myTrafficManagerEndpoint

  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group_webapp }}"
        location: "{{ location }}"

  - name: Create secondary resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

  - name: Create App Service Plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      location: "{{ location }}"
      is_linux: true
      sku: S1
      number_of_workers: 1

  - name: Create App Service on Linux with Java Runtime
    azure_rm_webapp:
        resource_group: "{{ resource_group_webapp }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        app_settings:
          testkey: "testvalue"
        frameworks:
          - name: java
            version: 8
            settings:
              java_container: "Tomcat"
              java_container_version: "8.5"

  - name: Get web app facts
    azure_rm_webapp_facts:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ webapp_name }}"
    register: webapp
    
  - name: Create Traffic Manager Profile
    azure_rm_trafficmanagerprofile:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ traffic_manager_profile_name }}"
      location: global
      routing_method: performance
      dns_config:
        relative_name: "{{ traffic_manager_profile_name }}"
        ttl:  60
      monitor_config:
        protocol: HTTPS
        port: 80
        path: '/'

  - name: Add endpoint to traffic manager profile, using created web site
    azure_rm_trafficmanagerendpoint:
      resource_group: "{{ resource_group_webapp }}"
      profile_name: "{{ traffic_manager_profile_name }}"
      name: "{{ traffic_manager_endpoint_name }}"
      type: azure_endpoints
      location: "{{ location }}"
      target_resource_id: "{{ webapp.webapps[0].id }}"
```

Voer de Playbook uit met behulp van de `ansible-playbook`-opdracht:

```bash
ansible-playbook webapp.yml
```

Nadat de Playbook is uitgevoerd, ziet u uitvoer die vergelijkbaar is met de volgende resultaten:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create resource group for app service plan] 
changed: [localhost]

TASK [Create App Service Plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
changed: [localhost]

TASK [Get web app facts] 
ok: [localhost]

TASK [Create Traffic Manager Profile] 
 [WARNING]: Azure API profile latest does not define an entry for TrafficManagerManagementClient

changed: [localhost]

TASK [Add endpoint to traffic manager profile, using the web site created above] 
changed: [localhost]

TASK [Get Traffic Manager Profile facts] 
ok: [localhost]

PLAY RECAP 
localhost                  : ok=9    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Zelf studie: apps schalen in Azure App Service met behulp van Ansible](/azure/ansible/ansible-scale-azure-web-apps)