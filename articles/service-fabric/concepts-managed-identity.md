---
title: Overzicht van beheerde identiteiten Service Fabric
description: Dit artikel bevat een overzicht van beheerde identiteiten en de bijbehorende toepassingen naar Azure Service Fabric.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: dc7dafa59596537456accde66e878c06f9e5ca23
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2019
ms.locfileid: "75528144"
---
# <a name="managed-identity-for-service-fabric-application-preview"></a>Beheerde identiteit voor Service Fabric toepassing (preview-versie)

Een veelvoorkomende uitdaging bij het bouwen van cloud-apps is het beheren van de referenties in uw code voor verificatie bij cloudservices. Het beveiligen van referenties is een belang rijke taak, omdat ze nooit worden weer gegeven op werk stations voor ontwikkel aars en niet zijn ingecheckt in broncode beheer. Met de functie beheerde identiteit voor Azure-resources in Azure Active Directory (Azure AD) is dit probleem opgelost. De functie biedt Azure-services met een automatisch beheerde identiteit in Azure AD. U kunt de identiteit gebruiken voor verificatie bij alle services die ondersteuning bieden voor Azure AD-verificatie, inclusief Key Vault, zonder referenties in de code.

De beheerde identiteits functie voor Azure-resources is gratis met Azure AD voor Azure-abonnementen. Er zijn geen extra kosten.

> [!NOTE]
> De beheerde identiteit voor Azure-resources is de nieuwe naam voor de service, voorheen bekend als Managed Service Identity (MSI).

## <a name="terminology"></a>Terminologie

De volgende termen worden gebruikt in de documentatieset beheerde identiteit voor Azure resources:

- **Client-id** : een unieke id die wordt gegenereerd door Azure AD die is gekoppeld aan een toepassing en Service-Principal tijdens de eerste inrichting (ook de [toepassings-id](/azure/active-directory/develop/developer-glossary#application-id-client-id).)

- **Principal-id** : de object-id van het Service-Principal-object voor uw beheerde identiteit die wordt gebruikt om op rollen gebaseerde toegang tot een Azure-resource te verlenen.

- **Service-Principal** : een Azure Active Directory-object dat de projectie van een Aad-toepassing in een bepaalde Tenant vertegenwoordigt (ook [Service-Principal](../active-directory/develop/developer-glossary.md#service-principal-object)weer gegeven.)


## <a name="about-managed-identities-in-azure"></a>Beheerde identiteiten in azure

- [Typen beheerde identiteiten (MI) in azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work)

- [Hoe werkt door het systeem toegewezen beheerde identiteiten in azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-a-system-assigned-managed-identity-works-with-an-azure-vm)

- [Hoe werkt door de gebruiker gedefinieerde beheerde identiteit (MI) in azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-a-user-assigned-managed-identity-works-with-an-azure-vm)


## <a name="supported-scenarios-for-service-fabric-applications"></a>Ondersteunde scenario's voor Service Fabric toepassingen

Beheerde identiteiten voor Service Fabric worden alleen ondersteund in azure geïmplementeerde Service Fabric-clusters en alleen voor toepassingen die zijn geïmplementeerd als Azure-resources. Er kan geen identiteit worden toegewezen aan een toepassing die niet is geïmplementeerd als een Azure-resource. De ondersteuning voor beheerde identiteiten in azure Service Fabric cluster bestaat uit twee fasen:

1. Een of meer beheerde identiteiten toewijzen aan de resource van de toepassing. aan een toepassing kunnen respectievelijk een door het systeem toegewezen identiteit en/of Maxi maal 32 door de gebruiker toegewezen identiteiten worden toegewezen.

2. In de definitie van de toepassing wijst u een van de identiteiten die aan de toepassing zijn toegewezen toe aan een afzonderlijke service die de toepassing omvat.

De door het systeem toegewezen identiteit van een toepassing is uniek voor die toepassing. een door de gebruiker toegewezen identiteit is een zelfstandige resource die kan worden toegewezen aan meerdere toepassingen. Binnen een toepassing kan één identiteit (of toegewezen systeem of gebruikers toegewezen) aan meerdere services van de toepassing worden toegewezen, maar aan elke afzonderlijke service kan slechts één identiteit worden toegewezen. Ten slotte moet aan een service expliciet een identiteit worden toegewezen om toegang te krijgen tot deze functie. Als gevolg hiervan kan de toewijzing van de identiteit van een toepassing aan de samenstellende services van een toepassings isolatie-een service alleen gebruikmaken van de identiteit die eraan is toegewezen (en helemaal geen als er geen expliciet aan is toegewezen.)  

De lijst met ondersteunde scenario's voor de preview-versie is als volgt:

   - Een nieuwe toepassing met een of meer services en een of meer toegewezen identiteiten implementeren

   - Wijs een of meer beheerde identiteiten toe aan een bestaande toepassing om toegang te krijgen tot Azure-resources. de toepassing moet zijn geïmplementeerd als een Azure-resource zelf


De volgende scenario's worden niet ondersteund of worden niet aanbevolen. Houd er rekening mee dat deze acties mogelijk niet worden geblokkeerd, maar kan leiden tot storingen in uw toepassingen:

   - De identiteiten die aan een toepassing zijn toegewezen, verwijderen of wijzigen. Als u wijzigingen moet aanbrengen, dient u afzonderlijke implementaties in te dienen om eerst een nieuwe identiteits toewijzing toe te voegen en vervolgens een eerder toegewezen account te verwijderen. Het verwijderen van een identiteit uit een bestaande toepassing kan ongewenste gevolgen hebben, zoals het verlaten van uw toepassing in een status die niet kan worden geüpgraded. Het is veilig om de toepassing te verwijderen als het verwijderen van een identiteit nood zakelijk is. Houd er rekening mee dat hiermee de door het systeem toegewezen identiteit (indien gedefinieerd) die aan de toepassing is gekoppeld, wordt verwijderd en alle koppelingen met de door de gebruiker toegewezen identiteiten die aan de toepassing zijn toegewezen, worden verwijderd.

   - SF-ondersteuning voor beheerde identiteiten is op dit moment niet geïntegreerd in de [AzureServiceTokenProvider](../key-vault/service-to-service-authentication.md); de integratie wordt bereikt door het einde van de preview-periode voor de functie Managed Identity.

>
> [!NOTE]
>
> Deze functie is beschikbaar als preview-versie. Dit kan worden onderhevig aan regel matige wijzigingen en is mogelijk niet geschikt voor productie-implementaties.

## <a name="next-steps"></a>Volgende stappen
* [Een nieuw Azure Service Fabric-cluster implementeren met beheerde identiteits ondersteuning](./configure-new-azure-service-fabric-enable-managed-identity.md) 
* [Ondersteuning van beheerde identiteiten inschakelen in een bestaand Azure Service Fabric-cluster](./configure-existing-cluster-enable-managed-identity-token-service.md)
* [Een Azure Service Fabric-toepassing implementeren met een door het systeem toegewezen beheerde identiteit](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Een Azure Service Fabric-toepassing implementeren met een door de gebruiker toegewezen beheerde identiteit](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Maak gebruik van de beheerde identiteit van een Service Fabric toepassing vanuit service code](./how-to-managed-identity-service-fabric-app-code.md)
* [Een Azure Service Fabric-toepassing toegang verlenen tot andere Azure-resources](./how-to-grant-access-other-resources.md)
* [Toepassings geheimen declareren en gebruiken als KeyVaultReferences](./service-fabric-keyvault-references.md) 
