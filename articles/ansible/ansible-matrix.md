---
title: Ansible-module en versie matrix voor Azure | Microsoft Docs
description: Overzicht met Ansible-modules en -versies voor Azure
keywords: ansible, rollen, matrix, versie, azure, devops
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 54e27c7570ba1cdbce7355740181d68a2f3efbac
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155980"
---
# <a name="ansible-module-and-version-matrix"></a>Overzicht met Ansible-modules en -versies

Ansible bevat een suite met modules die u kunt gebruiken bij het inrichten en configureren van Azure-resources. Deze resources omvatten virtuele machines, schaal sets, netwerk services en container Services. In dit artikel vindt u een overzicht van de verschillende Ansible-modules voor Azure en de Ansible-versies waarin ze worden geleverd.

## <a name="ansible-modules-for-azure"></a>Ansible-modules voor Azure

De volgende modules kunnen rechtstreeks worden uitgevoerd op externe hosts of via playbooks.  

Deze modules zijn beschikbaar via de officiële release van Ansible en uit de volgende micro soft Playbook-rollen.

> [!NOTE]
> Vanaf Ansible 2,9 worden de namen van alle * _facts modules gewijzigd in * _info om te voldoen aan de naamgevings Conventie van Ansible. De oude en hernoemde modules worden zodanig gekoppeld dat er een afschaffing waarschuwing wordt weer gegeven. alle modules werken net als voorheen.

| Ansible-module voor Azure                   |  Ansible 2,4 |  Ansible 2,5 |  Ansible 2,6 | Ansible 2,7 | Ansible 2,8 | Ansible 2,9 | Ansible-rol | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------|--------------|  
| **Computing**                    |           |                          |                          |                            |           |           |           |
| azure_rm_availabilityset                   | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_availabilityset_info              | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_batchaccount                       | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_deployment                         | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_deployment_info                   | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_functionapp                        | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_functionapp_info                  | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_gallery                            | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_gallery_info                       | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_galleryimage                       | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_galleryimage_info                  | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_galleryimageversion                | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_galleryimageversion_info           | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_image                              | -            | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_image_info                        | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_resource                           | -            | -                           | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_resource_info                     | -            | -                           | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_resourcegroup                      | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_resourcegroup_info                | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_snapshot                           | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_virtualmachine                     | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_virtualmachine_info               | -            | -                           | -            | Ja          | Ja          | Ja          | Ja          |
| azure_rm_virtualmachineextension            | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_virtualmachineextension_info      | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_virtualmachineimage_info          | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_virtualmachinescaleset             | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_virtualmachinescaleset_info       | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_virtualmachinescalesetextension_info | -            | -                        | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_virtualmachinescalesetinstance_info | -            | -                         | -            | -            | Ja          | Ja          | Ja          |
| **Netwerken**                              |              |                             |              |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | Ja          | Ja          | Ja          | Ja          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_applicationsecuritygroup_info     | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | Ja          | Ja          | Ja          |
| azure_rm_cdnendpoint_info                  | -            | -                         | -          | -            | Ja          | Ja          | Ja          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | Ja          | Ja          | Ja          |
| azure_rm_cdnprofile_info                   | -            | -                         | -          | -            | Ja          | Ja          | Ja          |
| azure_rm_dnsrecordset                       | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_dnsrecordset_info                 | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_dnszone                            | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_dnszone_info                      | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_firewall                           | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_firewall_info                      | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_loadbalancer                       | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_loadbalancer_info                 | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_networkinterface                   | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_networkinterface_info             | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_publicipaddress                    | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_publicipaddress_info              | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_route                              | -            | -                           | -            | Ja          | Ja          | Ja          | Ja          |
| azure_rm_routetable                         | -            | -                           | -            | Ja          | Ja          | Ja          | Ja          |
| azure_rm_routetable_info                   | -            | -                           | -            | Ja          | Ja          | Ja          | Ja          |
| azure_rm_securitygroup                      | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_securitygroup_info                 | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_subnet                             | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_subnet_info                       | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_trafficmanagerendpoint_info       | -            | -                         | -          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_trafficmanagerprofile_info        | -            | -                         | -          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_virtualnetwork                     | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_virtualnetwork_info               | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_virtualnetworkgateway              | -            | -                         | -          | -            | Ja          | Ja          | Ja          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | Ja          | Ja          | Ja          |
| azure_rm_virtualnetworkpeering_info         | -            | -                         | -          | -            | -            | Ja          | Ja          |
| **Storage**                    |           |                          |                          |                            |           |           |         |
| azure_rm_manageddisk                        | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_manageddisk_info                  | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_storageaccount                     | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_storageaccount_info               | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_storageblob                        | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| **Web**                    |           |                          |                          |                             |           |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_appserviceplan_info               | -            | -                         | -          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_webapp                             | -            | -                         | -          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_webapp_info                       | -            | -                         | -          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | Ja          | Ja          | Ja          |
| **Containers**                    |           |                          |                          |                            |           |           |          |
| azure_rm_acs                                | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_aks                                | -            | -                           | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_aks_info                          | -            | -                           | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_aksversion_info                   | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_containerinstance                  | -            | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_containerinstance_info            | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_containerregistry                  | -            | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_containerregistry_info            | -            | -                           | -            | Ja          | Ja          | Ja          | Ja          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | -          | -          | Ja          |
| azure_rm_containerregistryreplication_info  | -            | -                           | -            | -            | -          | -          | Ja          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | -          | -          | Ja          |
| azure_rm_containerregistrywebhook_info      | -            | -                           | -            | -            | -          | -          | Ja          |
| **Databases**                    |           |                          |                          |                             |           |           |          |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_cosmosdbaccount_info              | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_mariadbconfiguration_info         | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_mariadbdatabase_info              | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_mariadbfirewallrule_info          | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_mariadbserver_info                | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_mysqlconfiguration_info           | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_mysqldatabase                      | -            | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_mysqldatabase_info                | -            | -                           | -            | Ja          | Ja          | Ja          | Ja          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_mysqlfirewallrule_info            | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_mysqlserver                        | -            | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_mysqlserver_info                  | -            | -                           | -            | Ja          | Ja          | Ja          | Ja          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_postgresqlconfiguration_info      | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_postgresqldatabase                 | -            | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_postgresqldatabase_info           | -            | -                           | -            | Ja          | Ja          | Ja          | Ja          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_postgresqlfirewallrule_info       | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_postgresqlserver                   | -            | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_postgresqlserver_info             | -            | -                           | -            | Ja          | Ja          | Ja          | Ja          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_rediscache_info                   | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_sqldatabase                        | -            | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_sqldatabase_info                  | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_sqlelasticpool                    | -            | -                           | -            | -            | -          | -          | Ja          |
| azure_rm_sqlelasticpool_info               | -            | -                           | -            | -            | -          | -          | Ja          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Ja          | Ja          | Ja          | Ja          |
| azure_rm_sqlfirewallrule_info              | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_sqlserver                          | -            | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_sqlserver_info                    | -            | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| **Analytische gegevens**                    |           |                          |                          |                             |           |           |          |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_hdinsightcluster_info              | -            | -                           | -            | -            | -            | Ja          | Ja          |
| **Opneming**                    |           |                          |                          |                             |           |           |          |
| azure_rm_servicebus                         | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_servicebus_info                   | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| **Beveiliging**                    |           |                          |                          |                             |           |           |           |
| azure_rm_keyvault                           | -            | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_keyvault_info                     | -            | -                           | -              | -          | Ja          | Ja          | Ja          |
| azure_rm_keyvaultkey                        | -            | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_keyvaultkey_info                   | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_keyvaultsecret                     | -            | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_roleassignment_info               | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_roledefinition_info               | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| **DevOps**               |           |                          |                          |                             |           |           |                  |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_devtestlab_info                   | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_devtestlabarmtemplate_info        | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_devtestlabartifact_info           | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_devtestlabartifactsource_info     | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_devtestlabcustomimage_info         | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_devtestlabenvironment_info         | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_devtestlabpolicy_info              | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_devtestlabschedule_info            | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_devtestlabvirtualmachine_info | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_devtestlabvirtualnetwork_info     | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| **Azure Monitor**                           |              |                           |            |              |                 |           |              |
| azure_rm_autoscale                          | -            | -                         | -          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_autoscale_info                    | -            | -                         | -          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_loganalyticsworkspace_info        | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_monitorlogprofile                  | -            | -                           | -            | -            | -            | Ja          | Ja          |
| **Beheer en governance**     |              |                           |            |            |            |            |              |
| azure_rm_automationaccount        | -            | -                         | -          | -          | -          | Ja        | Ja          |
| azure_rm_automationaccount_info   | -            | -                         | -          | -          | -          | Ja        | Ja          |
| azure_rm_lock                     | -            | -                         | -          | -          | -          | Ja        | Ja          |
| azure_rm_lock_info                | -            | -                         | -          | -          | -          | Ja        | Ja          |
| **Internet of Things**            |              |                           |            |            |            |            |              |
| azure_rm_iotdevice                | -            | -                         | -          | -          | -          | Ja        | Ja          |
| azure_rm_iotdevice_info           | -            | -                         | -          | -          | -          | Ja        | Ja          |
| azure_rm_iotdevicemodule          | -            | -                         | -          | -          | -          | Ja        | Ja          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | Ja        | Ja          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | Ja        | Ja          |
| azure_rm_iothubconsumergroup      | -            | -                         | -          | -          | -          | Ja        | Ja          |

## <a name="introduction-to-playbook-role-for-azure"></a>Inleiding tot de Playbook-rol voor Azure

De [azure_preview_module rol Playbook](https://galaxy.ansible.com/Azure/azure_preview_modules/) omvat alle meest recente Azure-modules. De updates en oplossingen voor fouten worden op een meer wijze uitgevoerd dan de officiële Ansible-release. Als u Ansible gebruikt voor het inrichten van Azure-resources, raden we u aan om de `azure_preview_module` Playbook-functie te installeren.

De `azure_preview_module` Playbook-rol wordt elke drie weken vrijgegeven.

## <a name="next-steps"></a>Volgende stappen

Zie [rebruikbare Playbooks maken](https://docs.ansible.com/ansible/latest/playbooks_reuse.html)voor meer informatie over Playbook-rollen. 