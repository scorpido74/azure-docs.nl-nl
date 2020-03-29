---
title: Multitenant-toepassingen inschakelen - Azure Digital Twins | Microsoft Documenten
description: Multitenant Azure Active Directory-toepassingen configureren voor Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 6e1321e01d8d12974a2704f4478b02a26c14142f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264930"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Multitenant-toepassingen inschakelen met Azure Digital Twins

Ontwikkelaars van oplossingen die voortbouwen op Azure Digital Twins, kunnen vinden dat ze meerdere klanten willen ondersteunen met één service of oplossing. In feite behoren *multitenant-toepassingen* tot de meest voorkomende Azure Digital Twins-configuraties.

In dit document wordt beschreven hoe u een Azure Digital Twins-app configureert om verschillende Azure Active Directory-tenants en -klanten te ondersteunen.

## <a name="multitenancy"></a>Multitenancy

Een *multitenant-bron* is één ingerichte instantie die meerdere klanten ondersteunt. Elke klant heeft zijn eigen onafhankelijke gegevens en privileges. De ervaring van elke klant is geïsoleerd van elkaars, zodat hun "weergave" van de toepassing is verschillend.

Lees [Multitenant-toepassingen in Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications)voor meer informatie over multitenancy.

## <a name="problem-scenario"></a>Probleemscenario

Overweeg in dit scenario een ontwikkelaar die een Azure Digital Twins-oplossing **(DEVELOPER)** bouwt en een klant die die oplossing gebruikt (**KLANT):**

- **DEVELOPER** heeft een Azure-abonnement met een Azure Active Directory-tenant.
- **DEVELOPER** implementeert een Azure Digital Twins-exemplaar in hun Azure-abonnement. Azure Active Directory heeft automatisch een serviceprincipal gemaakt in de Azure Active **Directory-tenant**van ONTWIKKELAAR.
- Gebruikers binnen de Azure Active Directory-tenant van **DEVELOPER**kunnen vervolgens [OAuth 2.0-tokens aanschaffen](./security-authenticating-apis.md) via de Azure Digital Twins-service.
- **DEVELOPER** maakt nu een mobiele app die direct integreert met de Azure Digital Twins Management API's.
- **DEVELOPER** staat **klant** het gebruik van de mobiele applicatie toe.
- **De klant** moet gemachtigd zijn om de Azure Digital Twins Management API te gebruiken binnen de toepassing van **DEVELOPER.**

Het probleem:

- Wanneer **de klant** zich aanmeldt bij de toepassing van **ONTWIKKELAARS,** kan de app geen tokens aanschaffen voor gebruikers van **klanten**om te verifiëren met de Azure Digital Twins Management API's.
- Er wordt een uitzondering gemaakt in Azure Active Directory, die aangeeft dat Azure Digital Twins niet wordt herkend in de map van **de klant.**

## <a name="problem-solution"></a>Probleemoplossing

Om het vorige probleemscenario op te lossen, zijn de volgende acties nodig om een Azure Digital Twins-serviceprincipal te maken binnen de Azure Active Directory-tenant van de **klant:**

- Als **DE KLANT** nog geen Azure-abonnement heeft met een Azure Active Directory-tenant:

  - **De**Azure Active Directory-tenantbeheerder van Azure Directory van de klant moet een [Azure-abonnement voor betalen per gebruik](https://azure.microsoft.com/offers/ms-azr-0003p/)aanschaffen.
  - **De**Azure Active Directory-tenantbeheerder van DE KLANT moet zijn tenant vervolgens [koppelen aan het nieuwe abonnement.](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)

- Op de [Azure-portal](https://portal.azure.com)neemt de Azure Active Directory-tenantbeheerder van **DE KLANT**de volgende stappen:

  1. Zoeken naar **abonnementen** in het bovenste Azure-zoekveld. Selecteer **Abonnementen**.
  1. Selecteer het abonnement met de Azure Active Directory-tenant die moet worden gebruikt in de toepassing van **ONTWIKKELAAR.**

     [![Azure Active Directory-abonnementen](media/multitenant/ad-subscriptions.png)](media/multitenant/ad-subscriptions.png#lightbox)

  1. Selecteer **Resourceproviders**.
  1. Zoeken naar **Microsoft.IoTSpaces**.
  1. Selecteer **Registreren**.

     [![Azure Active Directory-bronproviders](media/multitenant/ad-resource-providers.png)](media/multitenant/ad-resource-providers.png#lightbox)
  
## <a name="next-steps"></a>Volgende stappen

- Lees voor meer informatie over het gebruik van door gebruikers gedefinieerde functies met Azure Digital Twins [Hoe u door azure digital twins door de gebruiker gedefinieerde functies maakt.](./how-to-user-defined-functions.md)

- Lees Hoe u [Azure Digital Twins-toegangsbeheer kunt](./security-create-manage-role-assignments.md)maken en beheren voor meer informatie over het gebruik van op rollen gebaseerd toegangsbeheer om de toepassing verder te beveiligen met roltoewijzingen.
