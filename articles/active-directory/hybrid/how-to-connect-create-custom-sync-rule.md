---
title: Een synchronisatie regel aanpassen in Azure AD Connect | Microsoft Docs '
description: Dit onderwerp bevat stappen voor het oplossen van problemen met het installeren van Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75300824d94ecbd77f426539ce99da6caaa2e27a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85359990"
---
# <a name="how-to-customize-a-synchronization-rule"></a>Een synchronisatieregel aanpassen

## <a name="recommended-steps"></a>**Aanbevolen stappen**

U kunt de editor voor synchronisatie regels gebruiken om een nieuwe synchronisatie regel te bewerken of te maken. U moet een geavanceerde gebruiker zijn om wijzigingen in de synchronisatie regels aan te brengen. Eventuele verkeerde wijzigingen kunnen ertoe leiden dat objecten uit uw doelmap worden verwijderd. Lees de [Aanbevolen documenten](#recommended-documents) om expertise in synchronisatie regels te krijgen. Ga als volgt te werk om een synchronisatie regel te wijzigen:

* Start de synchronisatie-editor vanuit het toepassingsmenu op het bureaublad, zoals hieronder wordt weergegeven:

    ![Menu voor synchronisatieregeleditor](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/syncruleeditormenu.png)

* Als u een standaard synchronisatie regel wilt aanpassen, kloont u de bestaande regel door te klikken op de knop bewerken in de editor voor synchronisatie regels, waarmee een kopie wordt gemaakt van de standaard regel standaard en deze wordt uitgeschakeld. Sla de gekloonde regel op met een prioriteit die kleiner is dan 100.  De prioriteit bepaalt welke regel WINS (lagere numerieke waarde) een conflict oplossing als er sprake is van een conflict met de kenmerk stroom.

    ![Editor voor synchronisatie regels](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/clonerule.png)

* Bij het wijzigen van een specifiek kenmerk moet u het kenmerk wijzigen alleen in de gekloonde regel gebruiken.  Schakel vervolgens de standaard regel in zodat het gewijzigde kenmerk afkomstig is van de gekloonde regel en andere kenmerken worden gekozen uit de standaard standaard regel. 

* In het geval dat de berekende waarde van het gewijzigde kenmerk NULL is in uw gekloonde regel en niet NULL is in de standaard regel voor standaard, wordt de niet-NULL-waarde gewonnen en wordt de NULL-waarde vervangen. Als u niet wilt dat een NULL-waarde wordt vervangen door een waarde die niet NULL is, wijst u AuthoritativeNull toe aan de gekloonde regel.

* Als u een **uitgaande** regel wilt wijzigen, wijzigt u het filter van de editor voor synchronisatie regels.

## <a name="recommended-documents"></a>**Aanbevolen documenten**
* [Azure AD Connect synchronisatie: technische concepten](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Azure AD Connect synchronisatie: uitleg van de architectuur](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Azure AD Connect Sync: uitleg over declaratieve inrichting](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Azure AD Connect Sync: uitleg over declaratieve inrichtings expressies](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Azure AD Connect-synchronisatie: inzicht in de standaardconfiguratie](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Azure AD Connect Sync: informatie over gebruikers, groepen en contact personen](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Azure AD Connect Sync: schaduw kenmerken](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Volgende stappen
- [Azure AD Connect synchronisatie](how-to-connect-sync-whatis.md).
- [Wat is een Hybrid Identity?](whatis-hybrid-identity.md).
