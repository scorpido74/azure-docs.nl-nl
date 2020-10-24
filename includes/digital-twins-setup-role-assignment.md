---
author: baanders
description: bestand insluiten voor de stap toegangs machtigingen in azure Digital Apparaatdubbels Setup
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: cbcaf4b4ad1b6c00f8c452582b986b6ee3b2806e
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92478828"
---
Azure Digital Apparaatdubbels maakt gebruik van [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) voor op rollen gebaseerd toegangs beheer (RBAC). Dit betekent dat voordat een gebruiker gegevens vlak kan aanroepen naar uw Azure Digital Apparaatdubbels-exemplaar, aan die gebruiker een rol met de juiste machtigingen moet worden toegewezen.

Voor Azure Digital Apparaatdubbels is deze rol _**Azure Digital Apparaatdubbels data owner**_. Meer informatie over rollen en beveiliging vindt u in [*concepten: beveiliging voor Azure Digital apparaatdubbels Solutions*](../articles/digital-twins/concepts-security.md).

[!INCLUDE [digital-twins-role-rename-note.md](digital-twins-role-rename-note.md)]

In deze sectie wordt uitgelegd hoe u een roltoewijzing maakt voor een gebruiker in uw Azure Digital Apparaatdubbels-exemplaar met behulp van het e-mail adres van de gebruiker in de Azure AD-Tenant van uw Azure-abonnement. Afhankelijk van uw rol in uw organisatie, kunt u deze machtiging voor uzelf instellen, of deze instellen namens iemand anders die het Azure Digital Apparaatdubbels-exemplaar gaat beheren.

### <a name="assign-the-role"></a>De rol toewijzen

Als u een gebruiker machtigingen wilt geven voor het beheren van een Azure Digital Apparaatdubbels-exemplaar, moet u de rol van de _**Azure Digital apparaatdubbels-gegevens eigenaar**_ binnen het exemplaar toewijzen.

> [!NOTE]
> Houd er rekening mee dat deze rol afwijkt van de rol van Azure AD- *eigenaar* , die ook kan worden toegewezen aan het bereik van het Azure Digital apparaatdubbels-exemplaar. Dit zijn twee verschillende beheer rollen en de Azure AD- *eigenaar* verleent geen toegang tot functies van het gegevens vlak die worden verleend met de eigenaar van de *Azure Digital apparaatdubbels-gegevens*.