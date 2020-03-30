---
title: Toegang autoriseren met behulp van Azure Active Directory
description: In dit artikel vindt u informatie over het toestaan van toegang tot bronnen van gebeurtenishubs met Azure Active Directory.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 1fa8f7a48c03ead7e939185b23834b3049b3e21c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064862"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>Toegang tot gebeurtenishubsresources autoriseren met Azure Active Directory
Azure Event Hubs ondersteunt het gebruik van Azure Active Directory (Azure AD) om aanvragen te autoriseren voor gebeurtenishubsresources. Met Azure AD u RBAC (Role-based access control) gebruiken om machtigingen toe te kennen aan een beveiligingsprincipal, die mogelijk een gebruiker of een principal van de toepassingsservice is. Zie [De verschillende rollen begrijpen](../role-based-access-control/overview.md)voor meer informatie over rollen en roltoewijzingen.

## <a name="overview"></a>Overzicht
Wanneer een beveiligingsprincipal (een gebruiker of een toepassing) probeert toegang te krijgen tot een gebeurtenishubsbron, moet de aanvraag worden geautoriseerd. Met Azure AD is toegang tot een bron een proces in twee stappen. 

 1. Eerst wordt de identiteit van de beveiligingsprincipal geverifieerd en wordt een OAuth 2.0-token geretourneerd. De resourcenaam om een `https://eventhubs.azure.net/`token aan te vragen is . Voor Kafka-clients is `https://<namespace>.servicebus.windows.net`de bron om een token aan te vragen .
 1. Vervolgens wordt het token doorgegeven als onderdeel van een verzoek aan de Gebeurtenishubs-service om toegang tot de opgegeven bron te autoriseren.

De verificatiestap vereist dat een toepassingsaanvraag een OAuth 2.0-toegangstoken bevat tijdens runtime. Als een toepassing wordt uitgevoerd binnen een Azure-entiteit, zoals een Azure VM, een virtuele machineschaalset of een Azure-functie-app, kan deze een beheerde identiteit gebruiken om toegang te krijgen tot de bronnen. Zie [Toegang tot Azure Event Hubs-bronnen verifiëren met Azure Active Directory en beheerde identiteiten voor Azure Resources](authenticate-managed-identity.md)voor meer informatie over het verifiëren van aanvragen van een beheerde identiteit naar De Hubs-service voor gebeurtenishubs. 

De autorisatiestap vereist dat een of meer RBAC-rollen worden toegewezen aan de beveiligingsprincipal. Azure Event Hubs biedt RBAC-rollen die reeksen machtigingen voor gebeurtenishubs-bronnen omvatten. De rollen die zijn toegewezen aan een beveiligingsprincipal bepalen de machtigingen die de opdrachtgever zal hebben. Zie [Ingebouwde RBAC-rollen voor Azure Event Hubs voor](#built-in-rbac-roles-for-azure-event-hubs)meer informatie over RBAC-rollen. 

Native toepassingen en webtoepassingen die aanvragen indienen voor gebeurtenishubs, kunnen ook autoriseren met Azure AD. Zie Toegang tot Azure Event Hubs met Azure AD verifiëren met Azure AD vanuit een toepassing voor meer informatie over het aanvragen van een toegangstoken en het gebruiken om aanvragen voor Gebeurtenishubs-bronnen [te autoriseren.](authenticate-application.md) 

## <a name="assign-rbac-roles-for-access-rights"></a>RBAC-rollen toewijzen voor toegangsrechten
Azure Active Directory (Azure AD) geeft toegangsrechten voor beveiligde bronnen door middel [van rbac (role-based access control).](../role-based-access-control/overview.md) Azure Event Hubs definieert een set ingebouwde RBAC-rollen die veelvoorkomende sets machtigingen omvatten die worden gebruikt om toegang te krijgen tot gebeurtenishubgegevens en u ook aangepaste rollen definiëren voor toegang tot de gegevens.

Wanneer een RBAC-rol is toegewezen aan een Azure AD-beveiligingsprincipal, verleent Azure toegang tot deze bronnen voor die beveiligingsprincipal. Toegang kan worden beperkt tot het abonnementsniveau, de resourcegroep, de naamruimte van gebeurtenishubs of een resource eronder. Een Azure AD-beveiligingsprincipal kan een gebruiker of een hoofdvan de toepassingsservice of een [beheerde identiteit voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)zijn.

## <a name="built-in-rbac-roles-for-azure-event-hubs"></a>Ingebouwde RBAC-rollen voor Azure-gebeurtenishubs
Azure biedt de volgende ingebouwde RBAC-rollen voor het toestaan van toegang tot gebeurtenishubsgegevens met Azure AD en OAuth:

- [Eigenaar Azure Event Hubs Data](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner): gebruik deze rol om volledige toegang te geven tot bronnen van Event Hubs.
- [Azure Event Hubs Data sender](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver): Gebruik deze rol om het verzenden toegang te geven tot Event Hubs resources.
- [Azure Event Hubs Data-ontvanger](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender): Gebruik deze rol om de verbruikende/ontvangende toegang te geven tot bronnen van Event Hubs.

## <a name="resource-scope"></a>Resourcebereik 
Voordat u een RBAC-rol toewijst aan een beveiligingsprincipal, bepaalt u de toegangsruimte die de beveiligingsprincipal moet hebben. Best practices dicteren dat het altijd het beste is om alleen de smalst mogelijke ruimte te verlenen.

In de volgende lijst worden de niveaus beschreven waarop u toegang tot bronnen voor gebeurtenishubs openen, te beginnen met het kleinste bereik:

- **Consumentengroep**: Bij deze scope is roltoewijzing alleen van toepassing op deze entiteit. Momenteel biedt de Azure-portal geen ondersteuning voor het toewijzen van een RBAC-rol aan een beveiligingsprincipal op dit niveau. 
- **Gebeurtenishub**: Roltoewijzing is van toepassing op de entiteit Event Hub en de consumentengroep eronder.
- **Naamruimte:** Roltoewijzing omvat de volledige topologie van gebeurtenishubs onder de naamruimte en aan de consumentengroep die eraan is gekoppeld.
- **Resourcegroep**: Roltoewijzing is van toepassing op alle bronnen van gebeurtenishubs onder de resourcegroep.
- **Abonnement:** Roltoewijzing is van toepassing op alle bronnen van gebeurtenishubs in alle resourcegroepen in het abonnement.

> [!NOTE]
> - Houd er rekening mee dat het tot vijf minuten kan duren voordat RBAC-roltoewijzingen worden uitgevoerd. 
> - Deze inhoud is van toepassing op zowel Event Hubs als Event Hubs voor Apache Kafka. Zie [Gebeurtenishubs voor Kafka - beveiliging en verificatie](event-hubs-for-kafka-ecosystem-overview.md#security-and-authentication)voor meer informatie over ondersteuning voor gebeurtenishubs voor Kafka.


Zie [Roldefinities begrijpen](../role-based-access-control/role-definitions.md#management-and-data-operations)voor meer informatie over hoe ingebouwde rollen worden gedefinieerd. Zie Aangepaste rollen maken voor [Azure Role-Based Access Control](../role-based-access-control/custom-roles.md)voor informatie over het maken van aangepaste RBAC-rollen.



## <a name="samples"></a>Voorbeelden
- [Voorbeelden van Microsoft.Azure.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Deze voorbeelden maken gebruik van de oude **Microsoft.Azure.EventHubs-bibliotheek,** maar u deze eenvoudig bijwerken naar de nieuwste **Azure.Messaging.EventHubs-bibliotheek.** Zie de [handleiding voor het migreren van Microsoft.Azure.EventHubs naar Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md)als u het voorbeeld wilt verplaatsen van het gebruik van de oude bibliotheek naar de nieuwe bibliotheek.
- [Azure.Messaging.EventHubs-voorbeelden](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Dit voorbeeld is bijgewerkt om de nieuwste **Azure.Messaging.EventHubs-bibliotheek** te gebruiken.
- [Event Hubs voor Kafka - OAuth monsters](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth). 


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het toewijzen van een ingebouwde RBAC-rol aan een beveiligingsprincipal, zie [Toegang verifiëren tot bronnen van gebeurtenishubs met Azure Active Directory](authenticate-application.md).
- Meer informatie over [het maken van aangepaste rollen met RBAC](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole).
- Meer informatie over [het gebruik van Azure Active Directory met EH](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)

Zie de volgende gerelateerde artikelen:

- [Aanvragen voor Azure Event Hubs verifiëren vanuit een toepassing met Azure Active Directory](authenticate-application.md)
- [Een beheerde identiteit verifiëren met Azure Active Directory om toegang te krijgen tot gebeurtenishubsbronnen](authenticate-managed-identity.md)
- [Aanvragen voor Azure Event Hubs verifiëren met behulp van gedeelde toegangshandtekeningen](authenticate-shared-access-signature.md)
- [Toegang tot bronnen van gebeurtenishubs autoriseren met behulp van gedeelde toegangshandtekeningen](authorize-access-shared-access-signature.md)
