---
title: Ondersteunde topologieën en scenario's voor Azure AD Connect-cloudprovisioning
description: In dit onderwerp worden de vereisten en de hardwarevereisten voor cloudprovisioning beschreven.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 386af46bbee623d37bc914d2ee9130c914c6c885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620877"
---
# <a name="azure-ad-connect-cloud-provisioning-supported-topologies-and-scenarios"></a>Ondersteunde topologieën en scenario's voor Azure AD Connect-cloudprovisioning
In dit artikel worden verschillende on-premises en Azure Active Directory (Azure AD) topologieën beschreven die gebruik maken van Azure AD Connect cloud provisioning. Dit artikel bevat alleen ondersteunde configuraties en scenario's.

> [!IMPORTANT]
> Microsoft biedt geen ondersteuning voor het wijzigen of exploiteren van Azure AD Connect cloudprovisioning buiten de configuraties of acties die formeel zijn gedocumenteerd. Een van deze configuraties of acties kan resulteren in een inconsistente of niet-ondersteunde status van Azure AD Connect cloud provisioning. Daarom biedt Microsoft geen technische ondersteuning voor dergelijke implementaties.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>Dingen om te onthouden over alle scenario's en topologieën
Het volgende is een lijst met informatie om in gedachten te houden bij het selecteren van een oplossing.

- Gebruikers en groepen moeten uniek worden geïdentificeerd in alle bossen
- Matching over forests vindt niet plaats met cloudprovisioning
- Een gebruiker of groep mag slechts één keer in alle bossen vertegenwoordigd zijn
- Het bronanker voor objecten wordt automatisch gekozen.  Het maakt gebruik van ms-DS-ConsistencyGuid indien aanwezig, anders objectGUId wordt gebruikt.
- U het kenmerk dat wordt gebruikt voor bronanker niet wijzigen.

## <a name="single-forest-single-azure-ad-tenant"></a>Eén forest, één Azure AD-tenant
![Topologie voor één forest en één tenant](media/plan-cloud-provisioning-topologies/single-forest.png)

De eenvoudigste topologie is één on-premises forest, met één of meerdere domeinen en één Azure AD-tenant.  Zie [Zelfstudie: een enkel forest met één Azure AD-tenant](tutorial-single-forest.md) voor een voorbeeld van dit scenario.


## <a name="multi-forest-single-azure-ad-tenant"></a>Meerdere forest, één Azure AD-tenant
![Topologie voor een multi-forest en één tenant](media/plan-cloud-provisioning-topologies/multi-forest.png)

Een gemeenschappelijke topologie is een meerdere AD-forests, met één of meerdere domeinen en één Azure AD-tenant.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Bestaand forest met Azure AD Connect, nieuw forest met cloudprovisioning
![Topologie voor één forest en één tenant](media/plan-cloud-provisioning-topologies/existing-forest-new-forest.png)

Dit scenario is topologie is vergelijkbaar met het multi-forest scenario, maar dit betreft een bestaande Azure AD Connect-omgeving en vervolgens brengen op een nieuw forest met behulp van Azure AD Connect cloud provisioning.  Zie [Zelfstudie: een bestaand forest met één Azure AD-tenant voor](tutorial-existing-forest.md) een voorbeeld van dit scenario.

## <a name="piloting-azure-ad-connect-cloud-provisioning-in-an-existing-hybrid-ad-forest"></a>Azure AD Connect-cloudinrichting in een bestaand hybride AD-forest besturen
![Topologie voor één forest en](media/plan-cloud-provisioning-topologies/migrate.png) één tenant Het pilotscenario omvat het bestaan van zowel Azure AD Connect als Azure AD Connect cloudprovisioning in hetzelfde forest en het opslaan van de gebruikers en groepen dienovereenkomstig. OPMERKING: Een object moet slechts in één van de gereedschappen in het bereik zijn. 

Zie [Zelfstudie: Azure AD Connect-cloudprovisioning in een bestaand gesynchroniseerd AD-forest leiden voor](tutorial-pilot-aadc-aadccp.md) een voorbeeld van dit scenario



## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect-cloudinrichting?](what-is-cloud-provisioning.md)

