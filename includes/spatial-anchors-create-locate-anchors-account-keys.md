---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 9bd213b63b69a25fb2530cd8f6659abf5357616a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76694465"
---
## <a name="set-up-authentication"></a>Verificatie instellen

Als u toegang wilt krijgen tot de service, moet u een accountsleutel, toegangstoken of Azure Active Directory auth-token verstrekken. U hier ook meer over lezen op de [pagina Verificatieconcept.](/azure/spatial-anchors/concepts/authentication)

### <a name="account-keys"></a>Accountsleutels

Accountsleutels zijn een referentie waarmee uw toepassing kan verifiëren met de Azure Spatial Anchors-service. Het beoogde doel van Account Keys is om u te helpen snel aan de slag te gaan. Vooral tijdens de ontwikkelingsfase van de integratie van uw toepassing met Azure Spatial Anchors. Als zodanig u AccountKeys gebruiken door ze tijdens de ontwikkeling in te sluiten in uw clienttoepassingen. Naarmate u verder komt dan de ontwikkeling, is het ten zeerste aan te raden om over te stappen op een verificatiemechanisme dat productieniveau is, ondersteund door Access Tokens of Azure Active Directory-gebruikersverificatie. Als u een accountsleutel voor ontwikkeling wilt ophalen, gaat u naar uw Azure Spatial Anchors-account en navigeert u naar het tabblad Sleutels.
