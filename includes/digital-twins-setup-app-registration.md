---
author: baanders
description: bestand insluiten voor de stap toegangs machtigingen in azure Digital Apparaatdubbels Setup
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 45f55e6c18d7cc551a6b96504ad2ce2ec3f84d86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009641"
---
Zodra u een Azure Digital Apparaatdubbels-exemplaar hebt ingesteld, is het gebruikelijk om met die instantie te communiceren via een client toepassing. Als u een werkende client toepassing wilt maken, moet u ervoor zorgen dat de client-app kan verifiëren tegen Azure Digital Apparaatdubbels. Dit doet u door een [Azure Active Directory (Azure AD)-](../articles/active-directory/fundamentals/active-directory-whatis.md) **app-registratie** in te stellen voor de client-app die u wilt gebruiken.

Met deze app-registratie kunt u toegangs machtigingen voor de [Azure Digital apparaatdubbels-api's](../articles/digital-twins/how-to-use-apis-sdks.md)configureren. Later wordt de client-app geverifieerd op basis van de app-registratie, en worden daarom de geconfigureerde toegangs machtigingen voor de Api's verleend.

>[!TIP]
> Als eigenaar van een abonnement/beheerder kunt u de voor keur geven aan een nieuwe app-registratie voor elke nieuwe Azure Digital Apparaatdubbels-instantie, *of* om dit slechts één keer te doen, waarbij u één app-registratie instelt die wordt gedeeld tussen alle Azure Digital apparaatdubbels-instanties in het abonnement.

### <a name="create-the-registration"></a>De registratie maken