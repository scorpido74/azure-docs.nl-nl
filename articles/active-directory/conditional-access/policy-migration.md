---
title: Beleid voor voorwaardelijke toegang migreren-Azure Active Directory
description: Meer informatie over wat u moet weten om klassiek beleid te migreren in de Azure Portal.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75d664f6e61dbbaaf0b8ab74c392596a206ff644
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380545"
---
# <a name="what-is-a-policy-migration-in-azure-active-directory-conditional-access"></a>Wat is een beleids migratie in Azure Active Directory voorwaardelijke toegang? 

[Voorwaardelijke toegang](../active-directory-conditional-access-azure-portal.md) is een mogelijkheid van Azure Active Directory (Azure AD) waarmee u kunt bepalen hoe geautoriseerde gebruikers toegang hebben tot uw Cloud-apps. Hoewel het doel nog steeds hetzelfde is, heeft de release van de nieuwe Azure Portal aanzienlijke verbeteringen geïntroduceerd in de werking van voorwaardelijke toegang.

U kunt de beleids regels die u niet hebt gemaakt, migreren in de Azure Portal omdat:

- U kunt nu scenario's aanpakken die u niet eerder kon afhandelen.
- U kunt het aantal beleids regels beperken dat u moet beheren door ze te consolideren.   
- U kunt al uw beleids regels voor voorwaardelijke toegang beheren op één centrale locatie.
- De klassieke Azure-portal wordt buiten gebruik gesteld.   

In dit artikel wordt uitgelegd wat u moet weten om uw bestaande beleids regels voor voorwaardelijke toegang naar het nieuwe Framework te migreren.
 
## <a name="classic-policies"></a>Klassiek beleid

In de [Azure Portal](https://portal.azure.com)is de pagina [voorwaardelijke toegang-beleid](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) uw toegangs punt voor uw beleid voor voorwaardelijke toegang. In uw omgeving kunt u echter ook beleid voor voorwaardelijke toegang hebben dat u niet met deze pagina hebt gemaakt. Deze beleids regels worden *klassieke beleids regels*genoemd. Klassieke beleids regels zijn beleids regels voor voorwaardelijke toegang, die u hebt gemaakt in:

- De klassieke Azure-Portal
- De klassieke intune-Portal
- De Intune-app-beveiliging Portal

Op de pagina **voorwaardelijke toegang** kunt u toegang krijgen tot uw klassieke beleids regels door te klikken op [**klassiek beleid (preview)** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) in de sectie **beheren** . 

![Azure Active Directory](./media/policy-migration/71.png)

De **klassieke beleids** weergave biedt u de volgende opties:

- Filter uw klassieke beleids regels.
 
   ![Azure Active Directory](./media/policy-migration/72.png)

- Klassieke beleids regels uitschakelen.

   ![Azure Active Directory](./media/policy-migration/73.png)
   
- Controleer de instellingen van een klassiek beleid (en om het uit te scha kelen).

   ![Azure Active Directory](./media/policy-migration/74.png)

Als u een klassiek beleid hebt uitgeschakeld, kunt u deze stap niet meer herstellen. Daarom kunt u het groepslid maatschap in een klassiek beleid wijzigen met behulp van de weer gave **Details** . 

![Azure Active Directory](./media/policy-migration/75.png)

Door de geselecteerde groepen te wijzigen of door specifieke groepen uit te sluiten, kunt u het effect van een uitgeschakeld klassiek beleid voor een aantal test gebruikers testen voordat u het beleid uitschakelt voor alle opgenomen gebruikers en groepen. 

## <a name="azure-ad-conditional-access-policies"></a>Beleid voor voorwaardelijke toegang van Azure AD

Met voorwaardelijke toegang in de Azure Portal kunt u al uw beleids regels beheren op één centrale locatie. Omdat de implementatie van de manier waarop voorwaardelijke toegang is gewijzigd, moet u vertrouwd zijn met de basis concepten voordat u uw klassieke beleids regels migreert.

Zie:

- [Wat is voorwaardelijke toegang in azure Active Directory](../active-directory-conditional-access-azure-portal.md) om meer te weten te komen over de basis concepten en de terminologie.
- [Aanbevolen procedures voor voorwaardelijke toegang in azure Active Directory](best-practices.md) voor hulp bij het implementeren van voorwaardelijke toegang in uw organisatie.
- Vereis [MFA vereisen voor specifieke apps met Azure Active Directory voorwaardelijke toegang](app-based-mfa.md) om vertrouwd te raken met de gebruikers interface in de Azure Portal.
 
## <a name="migration-considerations"></a>Overwegingen bij migraties

In dit artikel wordt het beleid voor voorwaardelijke toegang van Azure AD ook wel *nieuwe beleids regels*genoemd.
Uw klassieke beleids regels blijven naast uw nieuwe beleids regels werken, totdat u ze uitschakelt of verwijdert. 

De volgende aspecten zijn belang rijk in de context van een beleids consolidatie:

- Klassieke beleids regels zijn gekoppeld aan een specifieke Cloud-app, kunt u net zoveel Cloud-apps selecteren als u nodig hebt om een nieuw beleid te maken.
- Voor besturings elementen van een klassiek beleid en een nieuw beleid voor een Cloud-app moeten alle besturings elementen (*en*) worden voldaan. 
- In een nieuw beleid kunt u het volgende doen:
   - Combi neer meerdere voor waarden als dat nodig is voor uw scenario. 
   - Selecteer verschillende toekennings vereisten als toegangs beheer en combi neer deze met een logische *of* (een van de geselecteerde besturings elementen vereisen) of met een logische *en* (alle geselecteerde besturings elementen vereisen).

   ![Azure Active Directory](./media/policy-migration/25.png)

### <a name="office-365-exchange-online"></a>Office 365 Exchange Online

Als u klassiek beleid voor **Office 365 Exchange Online** wilt migreren dat **Exchange Active Sync** als client-apps voor waarde bevat, kunt u deze mogelijk niet samen voegen tot één nieuw beleid. 

Dit is bijvoorbeeld het geval als u alle typen client-Apps wilt ondersteunen. In een nieuw beleid met **Exchange Active Sync** als voor waarde voor client-apps, kunt u geen andere client-apps selecteren.

![Azure Active Directory](./media/policy-migration/64.png)

Een consolidatie in één nieuw beleid is ook niet mogelijk als uw klassieke beleids regels verschillende voor waarden bevatten. Een nieuw beleid met **Exchange Active Sync** als voor waarde voor client-apps geconfigureerd biedt geen ondersteuning voor andere voor waarden:   

![Azure Active Directory](./media/policy-migration/08.png)

Als u een nieuw beleid hebt met **Exchange Active Sync** als voor waarde voor client-apps geconfigureerd, moet u ervoor zorgen dat alle andere voor waarden niet zijn geconfigureerd. 

![Azure Active Directory](./media/policy-migration/16.png)
 
[Op apps gebaseerd](technical-reference.md#approved-client-app-requirement) klassiek beleid voor Office 365 Exchange Online met **Exchange Active Sync** als voor waarde client-apps **ondersteunde** en niet- **ondersteunde** [platformen](technical-reference.md#device-platform-condition)toestaan. Hoewel u geen individuele platformen kunt configureren in een gerelateerd nieuw beleid, kunt u de ondersteuning beperken tot alleen [ondersteunde apparaat platforms](technical-reference.md#device-platform-condition) . 

![Azure Active Directory](./media/policy-migration/65.png)

U kunt meerdere klassieke beleids regels samen voegen die **Exchange Active Sync** als voor waarde voor client-apps bevatten als ze beschikken over:

- Alleen **Exchange Active Sync** als voor waarde 
- Verschillende vereisten voor het verlenen van toegang geconfigureerd

Een veelvoorkomend scenario is de samen voeging van:

- Een op apparaten gebaseerd klassiek beleid vanuit de klassieke Azure-Portal 
- Een op apps gebaseerd klassiek beleid in de intune-portal voor app-beveiliging 
 
In dit geval kunt u uw klassieke beleids regels samen voegen in één nieuw beleid waarvoor beide vereisten zijn geselecteerd.

![Azure Active Directory](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Apparaat platforms

Klassieke beleids regels met [besturings elementen op basis](technical-reference.md#approved-client-app-requirement) van de app zijn vooraf geconfigureerd met IOS en Android als [platform voorwaarde voor het apparaat](technical-reference.md#device-platform-condition). 

In een nieuw beleid moet u de [platformen](technical-reference.md#device-platform-condition) selecteren die u afzonderlijk wilt ondersteunen.

![Azure Active Directory](./media/policy-migration/41.png)

## <a name="next-steps"></a>Volgende stappen

- Als u wilt weten hoe u een beleid voor voorwaardelijke toegang kunt configureren, raadpleegt u [MFA vereisen voor specifieke apps met voorwaardelijke toegang Azure Active Directory](app-based-mfa.md).
- Als u klaar bent voor het configureren van beleid voor voorwaardelijke toegang voor uw omgeving, raadpleegt u de [Aanbevolen procedures voor voorwaardelijke toegang in azure Active Directory](best-practices.md). 
