---
title: Multi tenant-toepassingen inschakelen-Azure Digital Apparaatdubbels | Microsoft Docs
description: Multi tenant-Azure Active Directory toepassingen configureren voor Azure Digital Apparaatdubbels.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 6e1321e01d8d12974a2704f4478b02a26c14142f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76264930"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Multi tenant-toepassingen met Azure Digital Apparaatdubbels inschakelen

Ontwikkel aars van oplossingen die op Azure Digital Apparaatdubbels bouwen, kunnen zien dat ze meerdere klanten willen ondersteunen met één service of oplossing. Multi *Tenant* -toepassingen zijn in feite een van de meest voorkomende Azure Digital apparaatdubbels-configuraties.

In dit document wordt beschreven hoe u een Azure Digital Apparaatdubbels-app kunt configureren voor de ondersteuning van verschillende Azure Active Directory tenants en klanten.

## <a name="multitenancy"></a>Multitenancy

Een resource voor meerdere *tenants* is één ingerichte instantie die ondersteuning biedt voor meer klanten. Elke klant heeft hun eigen onafhankelijke gegevens en bevoegdheden. De ervaring van elke klant is van elkaar geïsoleerd, zodat de ' weer gave ' van de toepassing DISTINCT is.

Lees voor meer informatie over multitenancy [multi tenant-toepassingen in azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications).

## <a name="problem-scenario"></a>Probleem scenario

In dit scenario kunt u een ontwikkelaar overwegen een Azure Digital Apparaatdubbels-oplossing (**Developer**) te bouwen en een klant die deze oplossing gebruikt (**klant**):

- **Ontwikkel aars** hebben een Azure-abonnement met een Azure Active Directory-Tenant.
- **Ontwikkelaar** implementeert een Azure Digital apparaatdubbels-exemplaar in hun Azure-abonnement. Azure Active Directory automatisch een service-principal gemaakt in de Azure Active Directory Tenant van de **ontwikkelaar**.
- Gebruikers in de Azure Active Directory-Tenant van de **ontwikkelaar**kunnen vervolgens [OAuth 2,0-tokens verkrijgen](./security-authenticating-apis.md) van de Azure Digital apparaatdubbels-service.
- **Ontwikkel aars** maakt nu een mobiele app die rechtstreeks kan worden geïntegreerd met de Azure Digital apparaatdubbels Management-api's.
- **Ontwikkel aars** kunnen **klanten** de mobiele toepassing gebruiken.
- De **klant** moet gemachtigd zijn om de Azure Digital APPARAATDUBBELS Management API te gebruiken in de toepassing van de **ontwikkelaar**.

Het probleem:

- Wanneer een **klant** zich aanmeldt bij de toepassing van de **ontwikkelaar**, kan de app geen tokens verkrijgen voor gebruikers van de **klant**om zich te verifiëren met de Azure Digital apparaatdubbels Management-api's.
- Er wordt een uitzonde ring gegeven in Azure Active Directory die aangeeft dat Azure Digital Apparaatdubbels niet wordt herkend in de directory van de **klant**.

## <a name="problem-solution"></a>Probleem oplossing

Om het scenario van het vorige probleem op te lossen, zijn de volgende acties nodig om een Azure Digital Apparaatdubbels Service-Principal te maken binnen de Azure Active Directory Tenant van de **klant**:

- Als de **klant** nog geen Azure-abonnement heeft met een Azure Active Directory Tenant:

  - De Azure Active Directory Tenant beheerder van de **klant**moet een [abonnement voor betalen per gebruik (Azure](https://azure.microsoft.com/offers/ms-azr-0003p/)) aanschaffen.
  - De Azure Active Directory Tenant beheerder van de **klant**moet de [Tenant vervolgens koppelen aan het nieuwe abonnement](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity).

- Op de [Azure Portal](https://portal.azure.com)heeft de Azure Active Directory Tenant beheerder van de **klant**de volgende stappen:

  1. Zoek naar **abonnementen** in het bovenste Azure-zoek veld. Selecteer **Abonnementen**.
  1. Selecteer het abonnement met de Azure Active Directory-Tenant die moet worden gebruikt in de toepassing van de **ontwikkelaar**.

     [![Azure Active Directory-abonnementen](media/multitenant/ad-subscriptions.png)](media/multitenant/ad-subscriptions.png#lightbox)

  1. **Resource providers**selecteren.
  1. Zoek naar **micro soft. IoTSpaces**.
  1. Selecteer **Registreren**.

     [![Azure Active Directory resource providers](media/multitenant/ad-resource-providers.png)](media/multitenant/ad-resource-providers.png#lightbox)
  
## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het gebruik van door de gebruiker gedefinieerde functies met Azure Digital Apparaatdubbels vindt u in het maken van door de [gebruiker gedefinieerde Azure Digital apparaatdubbels-functies](./how-to-user-defined-functions.md).

- Lees voor meer informatie over het gebruik van op rollen gebaseerd toegangs beheer om de toepassing verder te beveiligen met roltoewijzingen door [Azure Digital apparaatdubbels Access Control te maken en te beheren](./security-create-manage-role-assignments.md).
