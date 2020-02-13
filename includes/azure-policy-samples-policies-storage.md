---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 7aa6abb1a5fa9c969ca5e6d0730bed3b461fe81b
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172882"
---
|Naam |Beschrijving |Effect (s) |Version |
|---|---|---|---|
|[Toegestane opslagaccount-SKU's](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/AllowedStorageSkus_Deny.json) |Met dit beleid kunt u een set opslag account-Sku's opgeven die uw organisatie kan implementeren. |Weigeren |1.0.0 |
|[Onbeperkte netwerk toegang tot opslag accounts controleren](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |Controleer onbeperkte netwerk toegang in de firewall-instellingen van uw opslag account. In plaats daarvan configureert u netwerk regels zodat alleen toepassingen van toegestane netwerken toegang hebben tot het opslag account. Om verbindingen van specifieke internet-of lokale clients toe te staan, kan toegang worden verleend aan verkeer van specifieke Azure Virtual Networks of voor open bare IP-adresbereiken voor Internet |Controle, uitgeschakeld |1.0.0 |
|[Geavanceerde beveiliging tegen bedreigingen implementeren voor opslag accounts](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/StorageAdvancedThreatProtection_Deploy.json) |Met dit beleid kunt u geavanceerde beveiliging tegen bedreigingen inschakelen voor opslag accounts. |DeployIfNotExists, uitgeschakeld |1.0.0 |
|[Geografisch redundante opslag moet zijn ingeschakeld voor opslag accounts](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/GeoRedundant_StorageAccounts_Audit.json) |Dit beleid controleert elk opslag account met geografisch redundante opslag is niet ingeschakeld. |Controle, uitgeschakeld |1.0.0 |
|[Beveiligde overdracht naar opslag accounts moet zijn ingeschakeld](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |Controleer requirment van beveiligde overdracht in uw opslag account. Beveiligde overdracht is een optie die ervoor zorgt dat uw opslag account alleen aanvragen van beveiligde verbindingen (HTTPS) accepteert. Gebruik van HTTPS zorgt voor verificatie tussen de-server en de service en beveiligt gegevens onderweg vanuit netwerklaag aanvallen, zoals man-in-the-Middle, inkomend en inbreuken |Controleren, weigeren, uitgeschakeld |1.0.0 |
|[Opslag accounts moeten toegang toestaan vanuit vertrouwde micro soft-Services](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |Sommige Microsoft-services die interactie met de storage-accounts hebben werken van netwerken die toegang via netwerkregels kunnen niet worden toegekend. Om te helpen bij dit type service werken zoals bedoeld, kunt u de set vertrouwde Microsoft-services om de netwerkregels over te slaan. Deze services wordt sterke verificatie gebruikt voor toegang tot het opslagaccount. |Controleren, weigeren, uitgeschakeld |1.0.0 |
|[Opslag accounts moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Classic_AuditForClassicStorages_Audit.json) |Gebruik nieuwe Azure Resource Manager voor uw opslag accounts om beveiligings uitbreidingen te bieden zoals: sterker toegangs beheer (RBAC), betere controle, Azure Resource Manager op basis van implementatie en beheer, toegang tot beheerde identiteiten, toegang tot de sleutel kluis voor geheimen, op Azure AD gebaseerde verificatie en ondersteuning voor Tags en resource groepen voor eenvoudiger beveiligings beheer |Controleren, weigeren, uitgeschakeld |1.0.0 |
