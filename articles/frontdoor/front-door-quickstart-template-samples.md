---
title: Sjabloonvoorbeelden azure resource manager - Azure Front Door
description: Sjabloonvoorbeelden azure resource manager voor Azure Front Door
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2020
ms.author: sharadag
ms.openlocfilehash: 61ce63b15d2126a25b444e97acc8a3ea3964296b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985810"
---
# <a name="azure-resource-manager-deployment-model-templates-for-front-door"></a>Sjablonen voor implementatiemodellen van Azure Resource Manager voor Front Door

De volgende tabel bevat koppelingen naar Azure Resource Manager-implementatiemodelsjablonen voor Azure Front Door. 

| | |
| ---| ---|
| [Create a basic Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-basic) (Eenvoudige Front Door maken)| Hiermee maakt u een eenvoudige Front Door-configuratie met één back-end. |
| [Create a Front Door with multiple backends and backend pools and URL based routing](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-multiple-backends) (Front Door maken met meerdere back-ends en back-endpools en URL-routering)| Hiermee maakt u een Front Door met taakverdeling die is geconfigureerd voor meerdere back-ends in een back-endpool en ook voor back-endpools op basis van het URL-pad. |
| [Aan boord van een aangepast domein met Voordeur](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-custom-domain)| Een aangepast domein aan uw Front Door toevoegen. |
| [Voordeur maken met geofiltering](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering)| Maak een voordeur die het verkeer uit bepaalde landen/regio's toestaat/blokkeert. |
| [Control Health Probes for your backends on Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-health-probes) (Statustests beheren voor uw back-ends in Front Door)| Werk Front Door bij om de instellingen van de statustest te wijzigen door het testpad bij te werken en tevens de intervallen waarnaar de tests worden verzonden. |
| [Create Front Door with Active/Standby backend configuration](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-priority-lb) (Front Door maken met Actief/Stand-by-back-endconfiguratie)| Hiermee maakt u een Front Door waarmee routering op basis van prioriteit wordt gedemonstreerd voor een Actief/Stand-by-toepassingstopologie, dat wil zeggen dat standaard alle verkeer naar de primaire back-end (met de hoogste prioriteit) wordt verzonden totdat deze niet meer beschikbaar is. |
| [Create Front Door with caching enabled for certain routes](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-create-caching) Front Door maken waarbij opslaan in cache voor bepaalde routes is ingeschakeld)| Hiermee maakt u een Front Door waarvoor opslaan in de cache is ingeschakeld voor de gedefinieerde routeringsconfiguratie, waardoor dus statische assets voor uw workload in de cache worden opgeslagen. |
| [Configure Session Affinity for your Front Door host names](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-session-affinity) (Sessieaffiniteit configureren voor de hostnamen van uw Front Door) | Hiermee wordt een Front Door bijgewerkt om sessieaffiniteit in te schakelen voor uw front-endhost. Hierdoor wordt direct aansluitend verkeer van dezelfde gebruikerssessie naar dezelfde back-end verzonden. |
| [Configure Front Door for client IP whitelisting or blacklisting](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip) (Front Door configureren voor goedkeuren of afkeuren van IP-adressen van clients)| Hiermee wordt een Front Door geconfigureerd om verkeer van bepaalde IP-adressen van clients te beperken met behulp van aangepast toegangsbeheer met IP-adressen van clients. |
| [Voordeur configureren om actie te ondernemen met specifieke http-parameters](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-http-params)| Hiermee wordt een Front Door geconfigureerd zodat bepaald verkeer op basis van http-parameters in de inkomende aanvraag wordt toegestaan of geblokkeerd door aangepaste regels te gebruiken voor toegangsbeheer met http-parameters. |
| [Voordeursnelheid beperken configureren](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-rate-limiting)| Hiermee wordt een Front Door geconfigureerd om de snelheid van inkomend verkeer voor een bepaalde front-endhost te beperken. |
| | |

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
