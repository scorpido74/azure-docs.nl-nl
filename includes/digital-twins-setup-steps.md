---
author: baanders
description: het bestand include for Steps Overview in azure Digital Apparaatdubbels Setup
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: bf442da43fd7945bf69fbb889ef0c517b8832809
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92478829"
---
>[!NOTE]
>Deze bewerkingen zijn bedoeld om te worden voltooid door een gebruiker met de mogelijkheid om zowel resources als gebruikers toegang te beheren op het Azure-abonnement. Hoewel sommige stappen met lagere machtigingen kunnen worden uitgevoerd, is samen werking van iemand met deze machtigingen vereist om een bruikbaar exemplaar volledig in te stellen. Meer informatie hierover vindt u in de sectie [*vereisten: vereiste machtigingen*](#prerequisites-permission-requirements) hieronder.

Volledige installatie voor een nieuw exemplaar van de Azure Digital Apparaatdubbels bestaat uit twee delen:
1. **Het exemplaar maken**
2. **Machtigingen voor gebruikers toegang instellen**: Azure-gebruikers moeten de rol *Azure Digital apparaatdubbels data owner* hebben op de Azure Digital apparaatdubbels-instantie om deze en de gegevens te kunnen beheren. In deze stap wordt u als eigenaar/beheerder van het Azure-abonnement deze rol toegewezen aan de persoon die uw Azure Digital Apparaatdubbels-exemplaar gaat beheren. Dit kan uzelf of iemand anders in uw organisatie zijn.

[!INCLUDE [digital-twins-role-rename-note.md](digital-twins-role-rename-note.md)]