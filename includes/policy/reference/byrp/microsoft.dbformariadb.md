---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c4619610cc8e54f9fbe556e3d12668a680a2e261
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91859621"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Geografisch redundante back-up moet zijn ingeschakeld voor Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Met Azure Database for MariaDB kunt u de optie redundantie voor uw database server kiezen. Het kan worden ingesteld op een geografisch redundante back-upopslag waarin de gegevens niet alleen worden opgeslagen in de regio waarin uw server wordt gehost, maar die ook wordt gerepliceerd naar een gekoppeld gebied om de herstel optie te bieden in het geval van een storing in een regio. Het configureren van geografisch redundante opslag voor back-up is alleen toegestaan tijdens het maken van de server. |Controle, uitgeschakeld |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[De MariaDB-server moet gebruikmaken van een service-eindpunt voor een virtueel netwerk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |De firewall regels op basis van een virtueel netwerk worden gebruikt om verkeer van een specifiek subnet in te scha kelen om te Azure Database for MariaDB terwijl het verkeer binnen de Azure-grens blijft. Dit beleid biedt een manier om te controleren of de Azure Database for MariaDB over het virtuele netwerk service-eind punt wordt gebruikt. |AuditIfNotExists, uitgeschakeld |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[Het privé-eindpunt moet worden ingeschakeld voor MariaDB-servers](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |Met verbindingen met een privé-eind punt wordt beveiligde communicatie afgedwongen door een persoonlijke verbinding in te scha kelen Azure Database for MariaDB. Configureer een verbinding voor een persoonlijk eind punt om toegang tot verkeer alleen vanaf bekende netwerken mogelijk te maken en te voor komen dat toegang tot alle andere IP-adressen, inclusief in Azure. |AuditIfNotExists, uitgeschakeld |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[Openbare netwerktoegang moet worden uitgeschakeld voor MariaDB-servers](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |Het uitschakelen van de eigenschap open bare netwerk toegang verbetert de beveiliging door ervoor te zorgen dat uw Azure Database for MariaDB alleen toegankelijk is vanuit een persoonlijk eind punt. Met deze configuratie wordt de toegang tot een open bare adres ruimte buiten Azure IP-bereik uitgeschakeld en worden alle aanmeldingen die overeenkomen met de firewall regels op basis van IP of virtueel netwerk, geweigerd. |Controle, uitgeschakeld |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
