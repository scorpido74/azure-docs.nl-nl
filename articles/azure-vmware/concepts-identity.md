---
title: 'Concepten: identiteit en toegang'
description: Meer informatie over de identiteits-en toegangs concepten van de Azure VMware-oplossing (AVS)
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: ce85b60b38db86af14b9e9aa06e568436dc76658
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82740952"
---
# <a name="azure-vmware-solution-avs-identity-concepts"></a>Azure VMware-oplossing (AVS)-identiteits concepten

Een vCenter-Server en NSX-T-beheer worden ingericht wanneer een privécloud wordt geïmplementeerd. U gebruikt vCenter voor het beheren van werk belastingen van virtuele machines en NSX-T-beheer voor het uitbreiden van het software-gedefinieerd netwerk van de privécloud.

Toegang en identiteits beheer gebruiken CloudAdmin groeps rechten voor vCenter en beperkte beheerders rechten voor NSX-T-beheer. Dit beleid zorgt ervoor dat uw particuliere cloud platform automatisch kan worden bijgewerkt. Dit levert regel matig de nieuwste functies en patches. Zie het [artikel concepten][concepts-upgrades] van de privécloud-upgrades voor meer informatie over de upgrades van de privécloud.

## <a name="vcenter-access-and-identity"></a>toegang tot vCenter en identiteit

Bevoegdheden in vCenter worden gegeven via de groep CloudAdmin. Deze groep kan lokaal worden beheerd in vCenter of via de integratie van eenmalige aanmelding voor vCenter LDAP met Azure Active Directory. U hebt de mogelijkheid om die integratie in te scha kelen nadat u een privécloud hebt geïmplementeerd.

De CloudAdmin-en CloudGlobalAdmin-bevoegdheden worden weer gegeven in de volgende tabel.

|  Machtigingenset           | CloudAdmin | CloudGlobalAdmin | Opmerking |
| :---                     |    :---:   |       :---:      |   :--:  |
|  Waarschuwingen                  | Een CloudAdmin-gebruiker heeft alle alarm bevoegdheden voor waarschuwingen in de compute-resource pool en Vm's.     |          --        |  -- |
|  Automatisch implementeren             |  --  |        --        |  Micro soft beheert hostbeheer.  |
|  Certificaten            |  --  |        --       |  Micro soft biedt certificaat beheer.  |
|  Inhoudsbibliotheek         | Een CloudAdmin-gebruiker heeft bevoegdheden voor het maken en gebruiken van bestanden in een inhouds bibliotheek.    |         Ingeschakeld met SSO.         |  Micro soft zal bestanden in de inhouds bibliotheek distribueren naar ESXi-hosts.  |
|  Datacenter              |  --  |        --          |  Micro soft voert alle Data Center-bewerkingen uit.  |
|  Gegevensarchief               | Data Store. AllocateSpace, Data Store. browse, Datastore.Config, Data Store. Delete File, Data Store. File Management, Data Store. UpdateVirtualMachineMetadata     |    --    |   -- |
|  ESX-agent beheer       |  --  |         --       |  Micro soft voert alle bewerkingen uit.  |
|  Map                  |  Een CloudAdmin-gebruiker heeft alle machtigingen voor de map.     |  --  |  --  |
|  Globaal                  |  Global. CancelTask, Global. GlobalTag, Global. Health, Global. LogEvent, Global. ManageCustomFields, Global. ServiceManagers, Global. SetCustomField, Global.SystemTag         |                  |    |
|  Host                    |  Host. HBR. HbrManagement      |        --          |  Micro soft doet alle andere host-bewerkingen.  |
|  InventoryService        |  InventoryService. tagging      |        --          |  --  |
|  Netwerk                 |  Netwerk. assign    |                  |  Micro soft doet alle andere netwerk bewerkingen.  |
|  Machtigingen             |  --  |        --       |  Micro soft heeft alle machtigings bewerkingen.  |
|  Op profielen gebaseerde opslag  |  --  |        --       |  Micro soft voert alle profiel bewerkingen uit.  |
|  Resource                |  Een CloudAdmin-gebruiker heeft alle resource bevoegdheden.        |      --       | --   |
|  Geplande taak          |  Een CloudAdmin-gebruiker heeft alle ScheduleTask-bevoegdheden.   |   --   | -- |
|  Sessies                |  Sessions. GlobalMessage, sessies. ValidateSession      |   --   |  Micro soft doet alle andere sessie bewerkingen.  |
|  Opslag weergaven           |  StorageViews. View   |        --          |  Micro soft doet alle andere bewerkingen voor opslag weergave (service configureren).  |
|  Taken                   |  --  |  --   |  Micro soft beheert uitbrei dingen waarmee taken worden beheerd.  |
|  vApp                    |  Een CloudAdmin-gebruiker heeft alle vApp-bevoegdheden.  |  --  |  --  |
|  Virtuele machine         |  Een CloudAdmin-gebruiker heeft alle VirtualMachine-bevoegdheden.  |  --  |  --  |
|  vService                |  Een CloudAdmin-gebruiker heeft alle vService-bevoegdheden.  |  --  |  --  |

## <a name="nsx-t-manager-access-and-identity"></a>Toegangs-en identiteits beheer NSX-T

U hebt toegang tot NSX-T Manager met het account ' Administrator '. Dit account heeft volledige bevoegdheden en stelt u in staat T1-routers, logische switches en alle services te maken en te beheren. De volledige bevoegdheden in NSX-T bieden u ook toegang tot de NSX-T T0-router. Een wijziging in de T0-router kan leiden tot slechte netwerk prestaties of een verlies van toegang tot een privécloud. Om aan de ondersteunings vereisten te voldoen, is het vereist dat u een ondersteunings aanvraag in de Azure Portal opent om eventuele wijzigingen aan te vragen aan uw NSX-T T0-router.
  
## <a name="next-steps"></a>Volgende stappen

De volgende stap is om meer te weten te komen over de concepten van een [upgrade][concepts-upgrades]van de privécloud.

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md