---
title: Toegang met Azure Active Directory autoriseren
description: In dit artikel vindt u informatie over het verlenen van toegang tot Event Hubs-resources met behulp van Azure Active Directory.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 569399b2b5f676e11cd6540126e0b26dc0d26198
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992859"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>Toegang tot Event Hubs resources autoriseren met behulp van Azure Active Directory
Azure Event Hubs ondersteunt het gebruik van Azure Active Directory (Azure AD) om aanvragen voor Event Hubs bronnen goed te keuren. Met Azure AD kunt u gebruikmaken van op rollen gebaseerd toegangs beheer (RBAC) om machtigingen toe te kennen aan een beveiligingsprincipal. Dit kan een gebruiker, groep of toepassings Service-Principal zijn. Zie [informatie over de verschillende rollen](../role-based-access-control/overview.md)voor meer informatie over rollen en roltoewijzingen.

## <a name="overview"></a>Overzicht
Wanneer een beveiligingsprincipal (een gebruiker, groep of toepassing) probeert toegang te krijgen tot een Event Hubs resource, moet de aanvraag worden geautoriseerd. Met Azure AD is toegang tot een resource een proces dat uit twee stappen bestaat. 

 1. Eerst wordt de identiteit van de beveiligingsprincipal geverifieerd en wordt een OAuth 2,0-token geretourneerd. 
 1. Vervolgens wordt het token door gegeven als onderdeel van een aanvraag aan de Event Hubs-service om toegang tot de opgegeven bron te autoriseren.

De verificatie stap vereist dat een toepassings aanvraag een OAuth 2,0-toegangs token bevat tijdens runtime. Als een toepassing wordt uitgevoerd binnen een Azure-entiteit, zoals een Azure-VM, een schaalset voor virtuele machines of een Azure function-app, kan deze een beheerde identiteit gebruiken om toegang te krijgen tot de resources. Zie [toegang tot azure Event hubs-resources verifiëren met Azure Active Directory en beheerde identiteiten voor Azure-resources](authenticate-managed-identity.md)voor meer informatie over het verifiëren van aanvragen die door een beheerde identiteit naar Event hubs service worden verzonden. 

Voor de autorisatie stap moeten een of meer RBAC-rollen worden toegewezen aan de beveiligingsprincipal. Azure Event Hubs biedt RBAC-rollen die sets machtigingen voor Event Hubs bronnen omvatten. De rollen die zijn toegewezen aan een beveiligingsprincipal, bepalen de machtigingen die de principal heeft. Zie voor meer informatie over RBAC-rollen [Ingebouwde RBAC-rollen voor Azure Event hubs](#built-in-rbac-roles-for-azure-event-hubs). 

Systeem eigen toepassingen en webtoepassingen die aanvragen indienen bij Event Hubs kunnen ook worden geautoriseerd met Azure AD. Zie [toegang tot Azure-Event hubs verifiëren met Azure AD vanuit een toepassing](authenticate-application.md)voor meer informatie over het aanvragen van een toegangs token en het gebruik ervan om aanvragen voor Event hubs resources te autoriseren. 

## <a name="assign-rbac-roles-for-access-rights"></a>RBAC-rollen toewijzen voor toegangs rechten
Met Azure Active Directory (Azure AD) worden de toegangs rechten voor beveiligde bronnen geautoriseerd via [op rollen gebaseerd toegangs beheer (RBAC)](../role-based-access-control/overview.md). Azure Event Hubs definieert een set ingebouwde RBAC-rollen die algemene sets machtigingen bevatten die worden gebruikt voor toegang tot Event Hub gegevens, en u kunt ook aangepaste rollen definiëren voor toegang tot de gegevens.

Wanneer een RBAC-rol is toegewezen aan een Azure AD-beveiligings-principal, verleent Azure toegang tot de resources voor die beveiligings-principal. De toegang kan worden beperkt tot het niveau van het abonnement, de resource groep, de Event Hubs naam ruimte of een andere resource. Een beveiligings-principal voor Azure AD kan een gebruiker, een groep, een service-principal van de toepassing of een [beheerde identiteit voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)zijn.

## <a name="built-in-rbac-roles-for-azure-event-hubs"></a>Ingebouwde RBAC-rollen voor Azure Event Hubs
Azure biedt de volgende ingebouwde RBAC-rollen voor het verlenen van toegang tot Event Hubs gegevens met behulp van Azure AD en OAuth:

- [Eigenaar van Azure Event hubs-gegevens](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner-preview): Gebruik deze rol om volledige toegang tot Event Hubs resources te geven.
- [Afzender van Azure Event hubs gegevens](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver-preview): Gebruik deze rol om de toegang tot Event Hubs resources te verlenen.
- [Gegevens ontvanger van Azure Event hubs](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender-preview): Gebruik deze rol om de verbruiks-en ontvangst toegang tot Event Hubs resources te verlenen.

## <a name="resource-scope"></a>Bron bereik 
Voordat u een RBAC-rol toewijst aan een beveiligingsprincipal, bepaalt u het bereik van toegang dat de beveiligingsprincipal moet hebben. Aanbevolen procedures bepalen dat het altijd het beste is om alleen het smalle mogelijke bereik toe te kennen.

In de volgende lijst worden de niveaus beschreven waarmee u toegang tot Event Hubs resources kunt bereiken, te beginnen met het smalle bereik:

- **Consumentengroep**: In dit bereik is roltoewijzing alleen van toepassing op deze entiteit. Momenteel biedt de Azure Portal geen ondersteuning voor het toewijzen van een RBAC-rol aan een beveiligingsprincipal op dit niveau. 
- **Event hub**: De roltoewijzing is van toepassing op de Event hub-entiteit en de Consumer groep.
- **Naam ruimte**: Roltoewijzing omvat de volledige topologie van Event Hubs onder de naam ruimte en aan de Consumer-groep die eraan is gekoppeld.
- **Resourcegroep**: De roltoewijzing is van toepassing op alle Event Hubs resources onder de resource groep.
- **Abonnement**: De roltoewijzing is van toepassing op alle Event Hubs resources in alle resource groepen in het abonnement.

> [!NOTE]
> Houd er rekening mee dat de toewijzing van RBAC-rollen tot vijf minuten kan duren. 

Zie voor meer informatie over hoe ingebouwde rollen worden gedefinieerd [begrijpen functie definities](../role-based-access-control/role-definitions.md#management-and-data-operations). Zie voor meer informatie over het maken van aangepaste RBAC-rollen [aangepaste rollen maken voor op rollen gebaseerd Azure-Access Control](../role-based-access-control/custom-roles.md).

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het toewijzen van een ingebouwde RBAC-rol aan een beveiligingsprincipal, vindt u in [toegang tot Event hubs resources verifiëren met behulp van Azure Active Directory](authenticate-application.md).
- Meer informatie [over het maken van aangepaste rollen met RBAC](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole).
- Meer informatie [over het gebruik van Azure Active Directory met eh](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)

Raadpleeg de volgende verwante artikelen:

- [Aanvragen voor Azure Event Hubs verifiëren vanuit een toepassing met behulp van Azure Active Directory](authenticate-application.md)
- [Een beheerde identiteit verifiëren met Azure Active Directory om toegang te krijgen tot Event Hubs bronnen](authenticate-managed-identity.md)
- [Aanvragen voor Azure Event Hubs verifiëren met behulp van hand tekeningen voor gedeelde toegang](authenticate-shared-access-signature.md)
- [Toegang tot Event Hubs resources autoriseren met behulp van hand tekeningen voor gedeelde toegang](authorize-access-shared-access-signature.md)