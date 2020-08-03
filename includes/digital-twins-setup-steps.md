---
author: baanders
description: het bestand include for Steps Overview in azure Digital Apparaatdubbels Setup
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: b8751685cfd273171cb8a5b500e4bed41ce2f59e
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407519"
---
>[!NOTE]
>Deze bewerkingen zijn bedoeld om te worden voltooid door een gebruiker met een rol voor *eigenaar* van het Azure-abonnement. Hoewel sommige onderdelen kunnen worden voltooid zonder deze verhoogde bevoegdheid, is de samen werking van een eigenaar vereist om een bruikbaar exemplaar volledig in te stellen. Meer informatie hierover vindt u in de sectie [*vereisten: vereiste machtigingen*](#prerequisites-permission-requirements) hieronder.

Volledige installatie voor een nieuw Azure Digital Apparaatdubbels-exemplaar bestaat uit drie onderdelen:
1. **Het exemplaar maken**
2. **Machtigingen voor gebruikers toegang instellen**: Azure-gebruikers moeten de rol van *Azure Digital apparaatdubbels-eigenaar (preview)* hebben op de Azure Digital apparaatdubbels-instantie om deze en de gegevens te kunnen beheren. In deze stap wordt u als eigenaar van het Azure-abonnement deze rol toegewezen aan de persoon die uw Azure Digital Apparaatdubbels-exemplaar gaat beheren. Dit kan uzelf of iemand anders in uw organisatie zijn.
3. **Toegangs machtigingen voor client toepassingen instellen**: het is gebruikelijk om een client toepassing te schrijven die wordt gebruikt om met uw exemplaar te communiceren. Als u wilt dat de client-app toegang krijgt tot uw Azure Digital Apparaatdubbels, moet u een *app-registratie* instellen in [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) die de client toepassing zal gebruiken voor verificatie bij het exemplaar.

U hebt een Azure-abonnement nodig om door te gaan. U kunt [hier](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)een gratis waarde instellen.
