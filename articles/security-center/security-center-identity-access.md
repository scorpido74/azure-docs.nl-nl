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
ms.date: 03/06/2020
ms.author: memildin
ms.openlocfilehash: 183b81134b2fe72a539cc6460a05d828342aafbb
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086480"
---
# <a name="monitor-identity-and-access"></a>Identiteit en toegang bewaken

> [!TIP]
> Vanaf 2020 maart zijn de aanbevelingen voor identiteits-en toegangs toegang van Azure Security Center opgenomen in alle abonnementen op de prijs categorie gratis. Als u abonnementen op de gratis laag hebt, wordt de beveiligde score beïnvloed omdat deze nog niet eerder is beoordeeld op hun identiteits-en toegangs beveiliging. 

Wanneer Security Center mogelijke beveiligings problemen identificeert, worden er aanbevelingen gemaakt die u door het proces van het configureren van de benodigde besturings elementen leiden om uw resources te beschermen en te beveiligen.

De beveiligings verbinding is vanuit een netwerk verbinding met een identiteits verbinding ontwikkeld. De beveiliging wordt verminderd met het beschermen van uw netwerk en het beschermen van uw gegevens, en het beheren van de beveiliging van uw apps en gebruikers. Tegenwoordig bevinden zich steeds meer gegevens en apps in de cloud en is identiteit het nieuwe perimeternetwerk.

Door identiteitsactiviteiten te bewaken, kunt u proactieve maatregelen treffen voordat een incident plaatsvindt of reactieve maatregelen nemen om een aanvalspoging te stoppen. Voor beelden van aanbevelingen die u kunt zien in het gedeelte over het beveiligen van de **identiteits-en toegangs** resource van Azure Security Center zijn:

- MFA moet zijn ingeschakeld voor accounts met eigenaars machtigingen voor uw abonnement
- Er moeten Maxi maal drie eigen aren worden opgegeven voor uw abonnement
- Afgeschafte accounts moeten worden verwijderd uit uw abonnement
- Externe accounts met lees machtigingen moeten worden verwijderd uit uw abonnement

Zie [aanbevelingen voor identiteits-en toegangs rechten](recommendations-reference.md#recs-identity)voor een volledige lijst met aanbevelingen die hier kunnen worden weer gegeven.

> [!NOTE]
> Als uw abonnement meer dan 600 accounts bevat, kunnen Security Center de identiteits aanbevelingen niet uitvoeren op uw abonnement. De aanbevelingen die niet worden uitgevoerd, worden vermeld onder "niet-beschik bare evaluaties" hieronder.
Security Center kan de identiteits aanbevelingen niet uitvoeren op de beheer agenten van een Cloud Solution Provider (CSP)-partner.
>


Alle aanbevelingen voor identiteits-en toegangs beveiliging zijn beschikbaar in twee beveiligings controles op de pagina **aanbevelingen** :

- Toegang en machtigingen beheren 
- MFA inschakelen

![De twee beveiligings controles met de aanbevelingen met betrekking tot identiteit en toegang](media/security-center-identity-access/two-security-controls-for-identity-and-access.png)


## <a name="enable-multi-factor-authentication-mfa"></a>Multi-factor Authentication (MFA) inschakelen

Voor het inschakelen van MFA zijn [Tenant machtigingen voor Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)vereist. 

- Als u een Premium-editie van AD hebt, schakelt u MFA in met behulp van [voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).

- Gebruikers van de AD Free Edition kunnen **standaard instellingen voor beveiliging** inschakelen in azure Active Directory, zoals beschreven in de [ad-documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) , maar de Security Center aanbeveling om MFA in te scha kelen, wordt nog steeds weer gegeven.


## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over de aanbevelingen die van toepassing zijn op andere Azure-resource typen:

- [Uw machines en toepassingen beveiligen in Azure Security Center](security-center-virtual-machine-protection.md)
- [Protecting your network in Azure Security Center](security-center-network-recommendations.md) (Uw netwerk beveiligen in Azure Security Center)
- [Uw Azure SQL-service en-gegevens beveiligen in Azure Security Center](security-center-sql-service-recommendations.md)