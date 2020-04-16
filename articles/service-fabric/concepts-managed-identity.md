---
title: Beheerde identiteiten voor Azure
description: Meer informatie over het gebruik van beheerde identiteiten voor Azure met Service Fabric.
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: f6f3736bed4d3d59bce08d4df3ee0aa164a0a764
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415103"
---
# <a name="using-managed-identities-for-azure-with-service-fabric"></a>Beheerde identiteiten voor Azure gebruiken met ServiceFabric

Een veelvoorkomende uitdaging bij het bouwen van cloudtoepassingen is het veilig beheren van de referenties in uw code voor het verifiëren van verschillende services zonder ze lokaal op te slaan op een ontwikkelaarswerkstation of in bronbeheer. *Beheerde identiteiten voor Azure* lossen dit probleem op voor al uw resources in Azure Active Directory (Azure AD) door ze automatisch beheerde identiteiten binnen Azure AD te bieden. U de identiteit van een service gebruiken om te verifiëren voor elke service die Azure AD-verificatie ondersteunt, inclusief Key Vault, zonder dat er referenties zijn opgeslagen in uw code.

*Beheerde identiteiten voor Azure-bronnen* zijn gratis bij Azure AD voor Azure-abonnementen. Er zijn geen extra kosten.

> [!NOTE]
> *Beheerde identiteiten voor Azure* is de nieuwe naam voor de service die voorheen managed service identity (MSI) heette.

## <a name="concepts"></a>Concepten

Beheerde identiteiten voor Azure zijn gebaseerd op verschillende belangrijke concepten:

- **Client-id** - een unieke id die is gegenereerd door Azure AD en die tijdens de eerste inlevering is gekoppeld aan een toepassing en serviceprincipal (zie ook [toepassings-id](/azure/active-directory/develop/developer-glossary#application-id-client-id).)

- **Hoofd-id** : de object-id van het hoofdobject van de service voor uw beheerde identiteit dat wordt gebruikt om op rollen gebaseerde toegang tot een Azure-bron te verlenen.

- **Serviceprincipal** - een Azure Active Directory-object, dat de projectie van een AAD-toepassing in een bepaalde tenant vertegenwoordigt (zie ook [serviceprincipal](../active-directory/develop/developer-glossary.md#service-principal-object).)

Er zijn twee typen beheerde identiteit:

- Een **door het systeem toegewezen beheerde identiteit** is rechtstreeks ingeschakeld op een Azure-serviceinstantie.  De levenscyclus van een door het systeem toegewezen identiteit is uniek voor het Azure-serviceexemplaar waarop deze is ingeschakeld.
- Een **door de gebruiker toegewezen beheerde identiteit** wordt gemaakt als een zelfstandige Azure-resource. De identiteit kan worden toegewezen aan een of meer Azure-service-exemplaren en wordt afzonderlijk beheerd van de levenscyclus van die exemplaren.

Zie [Hoe werken beheerde identiteiten voor Azure-resources om](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) het verschil tussen beheerde identiteitstypen beter te begrijpen?

## <a name="supported-scenarios-for-service-fabric-applications"></a>Ondersteunde scenario's voor Service Fabric-toepassingen

Beheerde identiteiten voor Service Fabric worden alleen ondersteund in azure-geïmplementeerde Service Fabric-clusters en alleen voor toepassingen die zijn geïmplementeerd als Azure-resources; een toepassing die niet is geïmplementeerd als Azure-bron, kan geen identiteit toegewezen krijgen. Conceptueel gezien bestaat ondersteuning voor beheerde identiteiten in een Azure Service Fabric-cluster uit twee fasen:

1. Een of meer beheerde identiteiten toewijzen aan de toepassingsbron; aan een toepassing kan worden toegewezen aan één door het systeem toegewezen identiteit en/of maximaal 32 door de gebruiker toegewezen identiteiten.

2. Breng binnen de definitie van de toepassing een van de identiteiten die aan de toepassing zijn toegewezen in kaart aan een afzonderlijke service die de toepassing omvat.

De door het systeem toegewezen identiteit van een toepassing is uniek voor die toepassing; een door de gebruiker toegewezen identiteit is een zelfstandige bron, die kan worden toegewezen aan meerdere toepassingen. Binnen een toepassing kan één identiteit (toegewezen systeem of door de gebruiker toegewezen) worden toegewezen aan meerdere services van de toepassing, maar aan elke afzonderlijke service kan slechts één identiteit worden toegewezen. Ten slotte moet aan een service expliciet een identiteit worden toegewezen om toegang te hebben tot deze functie. In feite maakt het in kaart brengen van de identiteit van een toepassing aan de samenstellende services in-application het mogelijk om in-application isolatie te gebruiken : een service mag alleen de identiteit gebruiken die eraan is toegewezen.  

Momenteel worden de volgende scenario's ondersteund voor deze functie:

- Een nieuwe toepassing implementeren met een of meer services en een of meer toegewezen identiteiten

- Een of meer beheerde identiteiten toewijzen aan een bestaande (Azure-geïmplementeerde) toepassing om toegang te krijgen tot Azure-bronnen

De volgende scenario's worden niet ondersteund of niet aanbevolen; houd er rekening mee dat deze acties mogelijk niet worden geblokkeerd, maar kunnen leiden tot uitval in uw toepassingen:

- De identiteiten die aan een toepassing zijn toegewezen, verwijderen of wijzigen; Als u wijzigingen moet aanbrengen, dient u afzonderlijke implementaties in om eerst een nieuwe identiteitstoewijzing toe te voegen en vervolgens een eerder toegewezen implementatie te verwijderen. Het verwijderen van een identiteit uit een bestaande toepassing kan ongewenste effecten hebben, waaronder het verlaten van uw toepassing in een staat die niet kan worden geüderd. Het is veilig om de toepassing helemaal te verwijderen als het verwijderen van een identiteit noodzakelijk is; Let op: hiermee wordt de aan het systeem toegewezen identiteit (indien gedefinieerd) die aan de toepassing is gekoppeld, verwijderd en worden eventuele koppelingen met de door de gebruiker toegewezen identiteiten die aan de toepassing zijn toegewezen, verwijderd.

- Service Fabric-ondersteuning voor beheerde identiteiten is op dit moment niet geïntegreerd in de [AzureServiceTokenProvider.](../key-vault/service-to-service-authentication.md)

## <a name="next-steps"></a>Volgende stappen

- [Een nieuw Azure Service Fabric-cluster implementeren met ondersteuning voor beheerde identiteit](./configure-new-azure-service-fabric-enable-managed-identity.md)
- [Beheerde identiteitsondersteuning inschakelen in een bestaand Azure Service Fabric-cluster](./configure-existing-cluster-enable-managed-identity-token-service.md)
- [Een Azure Service Fabric-toepassing implementeren met een beheerde identiteit met systeemtoegewezen](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
- [Een Azure Service Fabric-toepassing implementeren met een door de gebruiker toegewezen beheerde identiteit](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
- [Gebruik maken van de beheerde identiteit van een Service Fabric-toepassing van servicecode](./how-to-managed-identity-service-fabric-app-code.md)
- [Een Azure Service Fabric-toepassing toegang verlenen tot andere Azure-bronnen](./how-to-grant-access-other-resources.md)
- [Toepassingsgeheimen declareren en gebruiken als KeyVaultReferences](./service-fabric-keyvault-references.md)
