---
author: baanders
description: bestand toevoegen voor overzicht van de stappen en vereisten voor machtigingen in azure Digital Apparaatdubbels Setup
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: b87d45a8be68a77df7b06ebd6e80562ccbe0e444
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009640"
---
>[!NOTE]
>Deze bewerkingen zijn bedoeld om te worden voltooid door een gebruiker met de mogelijkheid om zowel resources als gebruikers toegang te beheren op het Azure-abonnement. Hoewel sommige stappen met lagere machtigingen kunnen worden uitgevoerd, is samen werking van iemand met deze machtigingen vereist om een bruikbaar exemplaar volledig in te stellen. Meer informatie hierover vindt u in de sectie [*vereisten: vereiste machtigingen*](#prerequisites-permission-requirements) hieronder.

Volledige installatie voor een nieuw Azure Digital Apparaatdubbels-exemplaar bestaat uit drie onderdelen:
1. **Het exemplaar maken**
2. **Machtigingen voor gebruikers toegang instellen**: Azure-gebruikers moeten de rol van *Azure Digital apparaatdubbels-eigenaar (preview)* hebben op de Azure Digital apparaatdubbels-instantie om deze en de gegevens te kunnen beheren. In deze stap wordt u als eigenaar/beheerder van het Azure-abonnement deze rol toegewezen aan de persoon die uw Azure Digital Apparaatdubbels-exemplaar gaat beheren. Dit kan uzelf of iemand anders in uw organisatie zijn.
3. **Toegangs machtigingen voor client toepassingen instellen**: het is gebruikelijk om een client toepassing te schrijven die wordt gebruikt om met uw exemplaar te communiceren. Als u wilt dat de client-app toegang krijgt tot uw Azure Digital Apparaatdubbels, moet u een *app-registratie* instellen in [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) die de client toepassing zal gebruiken voor verificatie bij het exemplaar.

U hebt een Azure-abonnement nodig om door te gaan. U kunt [hier](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)een gratis waarde instellen.

## <a name="prerequisites-permission-requirements"></a>Vereisten: machtigings vereisten

Als u alle stappen in dit artikel wilt uitvoeren, moet u een [rol hebben in uw abonnement](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) met de volgende machtigingen:
* Azure-resources maken en beheren
* Gebruikers toegang tot Azure-resources beheren (inclusief het verlenen en delegeren van machtigingen)

Algemene rollen die aan deze vereiste voldoen, zijn *eigenaar*, *account beheerder*of de combi natie van beheerder en *mede werker*van de *gebruikers toegang* . Voor een volledige uitleg van de rollen en machtigingen, met inbegrip van de machtigingen die zijn opgenomen in andere rollen, gaat u naar [*klassieke abonnements beheerders rollen, Azure-rollen en Azure AD-rollen*](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) in de documentatie van Azure RBAC.

Als u uw rol in uw abonnement wilt weer geven, gaat u naar de [pagina Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) in de Azure Portal (u kunt deze koppeling gebruiken of zoeken naar *abonnementen* met de zoek balk van de portal). Zoek de naam van het abonnement dat u gebruikt en Bekijk uw rol hiervoor in de kolom *mijn rol* :

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png" alt-text="Weer gave van de pagina Abonnementen in de Azure Portal, waarbij de gebruiker wordt weer gegeven als eigenaar" lightbox="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png":::

Als u merkt dat de waarde *Inzender*is of een andere rol waarvoor niet de vereiste machtigingen zijn beschreven, kunt u contact opnemen met de gebruiker in uw abonnement die *wel* over deze machtigingen beschikt (zoals een abonnements eigenaar of account beheerder) en op een van de volgende manieren te werk gaan:
* Vraag de stappen in dit artikel namens u uit te voeren
* Vraag de gebruiker om uw rol over het abonnement uit te breiden zodat u de juiste machtigingen hebt om door te gaan. Of dit geschikt is, is afhankelijk van uw organisatie en uw rol.