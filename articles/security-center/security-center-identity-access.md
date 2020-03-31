---
title: Identiteit en toegang bewaken in Azure Security Center | Microsoft Docs
description: Lees hoe u de functies voor identiteit en toegang in Azure Security Center gebruikt om problemen met de toegang en identiteit van uw gebruikers te bewaken.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/16/2020
ms.author: memildin
ms.openlocfilehash: 152168bbadd5815659bc5f70c91bd2a28f5e049d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481956"
---
# <a name="monitor-identity-and-access"></a>Identiteit en toegang bewaken

> [!TIP]
> Vanaf maart 2020 worden de identiteits- en toegangsaanbevelingen van Azure Security Center opgenomen in alle abonnementen op de gratis prijscategorie. Als u abonnementen op de gratis laag hebt, wordt de Secure Score beïnvloed omdat deze niet eerder zijn beoordeeld op hun identiteit en toegangsbeveiliging. 

Wanneer Security Center potentiële beveiligingsproblemen identificeert, worden er aanbevelingen gemaakt die u begeleiden bij het configureren van de benodigde besturingselementen om uw resources te verharden en te beschermen.

De beveiligingsperimeter is geëvolueerd van een netwerkperimeter naar een identiteitsperimeter. Beveiliging wordt minder over het verdedigen van uw netwerk en meer over het verdedigen van uw gegevens, evenals het beheren van de veiligheid van uw apps en gebruikers. Tegenwoordig bevinden zich steeds meer gegevens en apps in de cloud en is identiteit het nieuwe perimeternetwerk.

Door identiteitsactiviteiten te controleren, u proactieve acties uitvoeren voordat een incident plaatsvindt, of reactieve acties om een aanvalspoging te stoppen. Beveiligingscentrum kan bijvoorbeeld afgeschafte accounts (accounts die niet langer nodig zijn en geblokkeerd zijn voor het aanmelden door Azure Active Directory) markeren voor verwijdering. 

Voorbeelden van aanbevelingen die u zien in de sectie Beveiliging van **identiteits- en toegangsbronnen** van Azure Security Center zijn:

- MFA moet zijn ingeschakeld voor accounts met eigenaarmachtigingen voor uw abonnement
- Er moeten maximaal 3 eigenaren worden aangewezen voor uw abonnement
- Externe accounts met leesmachtigingen moeten uit uw abonnement worden verwijderd
- Afgeschafte accounts moeten uit uw abonnement worden verwijderd

Zie [Aanbevelingen](recommendations-reference.md#recs-identity)voor identiteit en toegang voor meer informatie over deze aanbevelingen en een volledige lijst met aanbevelingen die u hier zien.

> [!NOTE]
> Als uw abonnement meer dan 600 accounts heeft, kan Security Center de identiteitsaanbevelingen niet uitvoeren tegen uw abonnement. Aanbevelingen die niet worden uitgevoerd, worden hieronder vermeld onder 'niet-beschikbare beoordelingen'.
Security Center kan de identiteitsaanbevelingen niet uitvoeren tegen de beheerdersagents van een Cloud Solution Provider (CSP).
>


Alle aanbevelingen voor identiteit en toegang zijn beschikbaar binnen twee beveiligingsbesturingselementen op de pagina **Aanbevelingen:**

- Toegang en machtigingen beheren 
- MFA inschakelen

![De twee beveiligingscontroles met de aanbevelingen met betrekking tot identiteit en toegang](media/security-center-identity-access/two-security-controls-for-identity-and-access.png)


## <a name="enable-multi-factor-authentication-mfa"></a>Multi-factor authenticatie inschakelen (MFA)

Voor het inschakelen van MFA zijn [ad-tenantmachtigingen (Azure Active Directory) vereist.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

- Als u een premium-editie van AD hebt, schakelt u MFA in met [voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).

- Gebruikers van AD Free Edition kunnen **beveiligingsstandaards** inschakelen in Azure Active Directory zoals beschreven in de [AD-documentatie,](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) maar de aanbeveling van het Beveiligingscentrum om MFA in te schakelen, wordt nog steeds weergegeven.


## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over aanbevelingen die van toepassing zijn op andere Azure-brontypen:

- [Uw machines en toepassingen beveiligen in Azure Security Center](security-center-virtual-machine-protection.md)
- [Protecting your network in Azure Security Center](security-center-network-recommendations.md) (Uw netwerk beveiligen in Azure Security Center)
- [Uw Azure SQL-service en -gegevens beveiligen in Azure Security Center](security-center-sql-service-recommendations.md)