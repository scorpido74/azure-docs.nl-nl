---
title: Toegang tot Azure Event Hubs autoriseren
description: In dit artikel vindt u informatie over verschillende opties voor het toestaan van toegang tot Azure Event Hubs-bronnen.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: f44be4e1d3d1186f0122bd4669ae800ab42e31d6
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521297"
---
# <a name="authorize-access-to-azure-event-hubs"></a>Toegang tot Azure Event Hubs autoriseren
Elke keer dat u gebeurtenissen/gegevens publiceert of gebruikt vanuit een gebeurtenishub, probeert uw client toegang te krijgen tot bronnen van Event Hubs. Elk verzoek om een beveiligde bron moet worden geautoriseerd, zodat de service ervoor kan zorgen dat de client over de vereiste machtigingen beschikt om de gegevens te publiceren/consumeren. 

Azure Event Hubs biedt de volgende opties voor het toestaan van toegang tot beveiligde bronnen:

- Azure Active Directory
- Handtekening voor gedeelde toegang

> [!NOTE]
> Dit artikel is van toepassing op zowel Gebeurtenishubs als Apache Kafka-scenario's. [Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) 

## <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory (Azure AD)-integratie voor Gebeurtenishubs-bronnen biedt rbac (role-based access control) voor fijnmazige controle over de toegang van een client tot resources. U rbac (role-based access control) gebruiken om machtigingen te verlenen aan beveiligingsprincipal, die mogelijk een gebruiker, een groep of een hoofdvoorde van de toepassingsservice is. De beveiligingsprincipal is geverifieerd door Azure AD om een OAuth 2.0-token terug te sturen. Het token kan worden gebruikt om een aanvraag om toegang te krijgen tot een Gebeurtenishubs-bron.

Zie de volgende artikelen voor meer informatie over het verifiëren met Azure AD:

- [Aanvragen voor Azure Event Hubs verifiëren met Azure Active Directory](authenticate-application.md)
- [Toegang tot bronnen van gebeurtenishubs autoriseren met Azure Active Directory](authorize-access-azure-active-directory.md).

## <a name="shared-access-signatures"></a>Handtekeningen voor gedeelde toegang 
Shared access signatures (SAS) voor Event Hubs-bronnen bieden beperkte gedelegeerde toegang tot bronnen voor gebeurtenishubs. Het toevoegen van beperkingen op tijdsinterval waarvoor de handtekening geldig is of op machtigingen die het verleent, biedt flexibiliteit bij het beheren van resources. Zie [Verifiëren met gedeelde toegangshandtekeningen (SAS)](authenticate-shared-access-signature.md)voor meer informatie. 

Het toestaan van gebruikers of toepassingen met behulp van een OAuth 2.0-token dat is geretourneerd door Azure AD biedt superieure beveiliging en gebruiksgemak ten opzichte van sas (Shared Access Signatures). Met Azure AD is het niet nodig om de toegangstokens op te slaan met uw code en potentiële beveiligingsproblemen te riskeren. Hoewel u shared access signatures (SAS) blijven gebruiken om fijnmazige toegang tot Event Hubs-bronnen te verlenen, biedt Azure AD vergelijkbare mogelijkheden zonder dat u SAS-tokens hoeft te beheren of zich zorgen hoeft te maken over het intrekken van een gecompromitteerde SAS. 

Standaard zijn alle Bronnen van Event Hubs beveiligd en alleen beschikbaar voor de eigenaar van het account. Hoewel u een van de hierboven beschreven autorisatiestrategieën gebruiken om clients toegang te verlenen tot Event Hub-bronnen. Microsoft raadt aan om Azure AD te gebruiken wanneer dat mogelijk is voor maximale beveiliging en gebruiksgemak.

Zie Toegang tot bronnen voor [gebeurtenishubs toestaan met behulp van gedeelde toegangshandtekeningen](authorize-access-shared-access-signature.md)voor meer informatie over autorisatie via SAS.

## <a name="next-steps"></a>Volgende stappen
- Review [RBAC monsters](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) gepubliceerd in onze GitHub repository. 
- Zie de volgende artikelen:
    - [Aanvragen voor Azure Event Hubs verifiëren vanuit een toepassing met Azure Active Directory](authenticate-application.md)
    - [Een beheerde identiteit verifiëren met Azure Active Directory om toegang te krijgen tot gebeurtenishubsbronnen](authenticate-managed-identity.md)
    - [Aanvragen voor Azure Event Hubs verifiëren met behulp van gedeelde toegangshandtekeningen](authenticate-shared-access-signature.md)
    - [Toegang tot gebeurtenishubsresources autoriseren met Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Toegang tot bronnen van gebeurtenishubs autoriseren met behulp van gedeelde toegangshandtekeningen](authorize-access-shared-access-signature.md)

