---
author: baanders
description: bestand voor starter info in azure Digital Apparaatdubbels-installatie toevoegen
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: dcc616dd8d30bfadb751df9ba24d71d72675428a
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371448"
---
>[!NOTE]
>Deze bewerkingen zijn bedoeld om te worden voltooid door een gebruiker met een rol voor *eigenaar* van het Azure-abonnement. Hoewel sommige onderdelen kunnen worden voltooid zonder deze verhoogde bevoegdheid, is de samen werking van een eigenaar vereist om een bruikbaar exemplaar volledig in te stellen. Meer informatie hierover vindt u in de sectie [*vereisten: vereiste machtigingen*](#prerequisites-permission-requirements) hieronder.

Volledige installatie voor een nieuw Azure Digital Apparaatdubbels-exemplaar bestaat uit drie onderdelen:
1. **Het exemplaar maken**
2. De **toegangs machtigingen van uw gebruiker instellen**: uw Azure-gebruiker moet de rol *Azure Digital Apparaatdubbels Owner (preview)* voor het exemplaar hebben om beheer activiteiten uit te voeren. In deze stap wijst u deze rol toe (als u een eigenaar van het Azure-abonnement bent) of een eigenaar van uw abonnement om deze toe te wijzen aan u.
3. **Toegangs machtigingen voor client toepassingen instellen**: het is gebruikelijk om een client toepassing te schrijven die u gebruikt om te communiceren met uw exemplaar. Als u wilt dat de client-app toegang krijgt tot uw Azure Digital Apparaatdubbels, moet u een *app-registratie* instellen in [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) die de client toepassing zal gebruiken voor verificatie bij het exemplaar.

U hebt een Azure-abonnement nodig om door te gaan. U kunt [hier](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)een gratis waarde instellen.

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>Vereisten: machtigings vereisten

Als u alle stappen in dit artikel wilt uitvoeren, moet u worden geclassificeerd als een eigenaar van uw Azure-abonnement. 

U kunt uw machtigings niveau controleren door deze opdracht uit te voeren in Cloud Shell:

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

Als u een eigenaar bent, `roleDefinitionName` is de waarde in de uitvoer *eigenaar*:

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/owner-role.png" alt-text="Cloud Shell venster met de uitvoer van de opdracht AZ Role Assignment List":::

Als u merkt dat de waarde *Inzender* of iets anders is dan de *eigenaar*, kunt u door gaan op een van de volgende manieren:
* Neem contact op met de eigenaar van uw abonnement en vraag de eigenaar om de stappen in dit artikel namens u uit te voeren
* Neem contact op met de eigenaar van uw abonnement of iemand met de rol van beheerder voor gebruikers toegang voor het abonnement en vraag om u te helpen bij de eigenaar van het abonnement, zodat u de juiste machtigingen hebt om door te gaan. Of dit geschikt is, is afhankelijk van uw organisatie en uw rol.
