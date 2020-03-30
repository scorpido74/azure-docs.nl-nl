---
title: Beleid voor voorwaardelijke toegang migreren - Azure Active Directory
description: Meer informatie over wat u moet weten om klassiek beleid te migreren in de Azure-portal.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33b1f76dd1489e00115d0f805add8d754038df84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185914"
---
# <a name="conditional-access-classic-policy-migration"></a>Klassieke beleidsmigratie voor voorwaardelijke toegang

Voorwaardelijke toegang is het hulpmiddel dat wordt gebruikt door Azure Active Directory om signalen samen te brengen, beslissingen te nemen en organisatiebeleid af te dwingen. Conditional Access vormt de kern van het nieuwe identiteitsgestuurde controlevliegtuig. Hoewel het doel nog steeds hetzelfde is, heeft de release van de nieuwe Azure-portal aanzienlijke verbeteringen aangebracht in de manier waarop voorwaardelijke toegang werkt.

Overweeg het beleid dat u niet hebt gemaakt in de Azure-portal te migreren omdat:

- U nu scenario's aanpakken die u voorheen niet aankon.
- U het aantal beleidsregels dat u moet beheren verminderen door ze te consolideren.
- U al uw beleid voor voorwaardelijke toegang op één centrale locatie beheren.
- De klassieke Azure-portal wordt buiten gebruik gesteld.

In dit artikel wordt uitgelegd wat u moet weten om uw bestaande beleid voor voorwaardelijke toegang te migreren naar het nieuwe framework.

## <a name="classic-policies"></a>Klassiek beleid

In de [Azure-portal](https://portal.azure.com)vindt u beleid voor voorwaardelijke toegang onder voorwaardelijke**toegang**tot Azure Active**Directory-beveiliging** >  **Azure Active Directory** > . Uw organisatie heeft mogelijk ook oudere voorwaardentoegangsbeleidsregels die niet met deze pagina zijn gemaakt. Deze beleidsregels worden genoemd *klassiek beleid*. Klassiek beleid zijn beleid voor voorwaardelijke toegang, die u hebt gemaakt in:

- De klassieke Azure-portal
- De Intune klassieke portal
- De Intune App Protection-portal

Op de pagina **Voorwaardelijke toegang** u toegang krijgen tot uw klassieke beleid door op [**Klassiekbeleid**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) te klikken in de sectie **Beheren.** 

![Voorwaardelijke toegang in Azure AD met klassieke beleidsweergave](./media/policy-migration/71.png)

De **weergave Klassiek-beleid** biedt u de mogelijkheid om:

- Filter uw klassieke beleid.
- Schakel klassiek beleid uit.
- Bekijk de instellingen van een klassiek beleid en schakel het uit.

   ![Klassieke beleidsdetails, waaronder bestaande beleidsconfiguratie](./media/policy-migration/74.png)

> [!WARNING]
> Eenmaal uitgeschakeld kan een klassiek beleid niet opnieuw worden ingeschakeld.

Met de detailsweergave van een klassiek beleid u de instellingen documenteren, de opgenomen of uitgesloten groepen wijzigen en het beleid uitschakelen.

![Beleidsdetails - Groepen die moeten worden opgenomen of uitgesloten](./media/policy-migration/75.png)

Door de geselecteerde groepen te wijzigen of door specifieke groepen uit te sluiten, u het effect van een uitgeschakeld klassiek beleid voor een paar testgebruikers testen voordat u het beleid uitschakelt voor alle opgenomen gebruikers en groepen.
 
## <a name="migration-considerations"></a>Overwegingen bij migraties

In dit artikel worden azure AD Conditional Access-beleidsregels ook wel *nieuw beleid*genoemd .
Uw klassieke beleid blijft naast uw nieuwe beleid werken totdat u ze uitschakelt of verwijdert. 

De volgende aspecten zijn belangrijk in het kader van een beleidsconsolidatie:

- Hoewel klassieke beleidsregels zijn gekoppeld aan een specifieke cloud-app, u zoveel cloud-apps selecteren als nodig is in een nieuw beleid.
- Besturingselementen voor een klassiek beleid en een nieuw beleid voor een cloud-app vereisen dat aan alle besturingselementen *(EN)* wordt voldaan. 
- In een nieuw beleid u:
   - Combineer meerdere voorwaarden indien vereist door uw scenario. 
   - Selecteer verschillende subsidievereisten als toegangscontrole en combineer deze met een logische *OK* (vereist een van de geselecteerde besturingselementen) of met een logische *EN* (vereisen alle geselecteerde besturingselementen).

### <a name="office-365-exchange-online"></a>Office 365 Exchange online

Als u klassiek beleid voor **Office 365 Exchange online** wilt migreren met Exchange Active **Sync** als voorwaarde voor client-apps, u deze mogelijk niet samenvoegen in één nieuw beleid. 

Dit is bijvoorbeeld het geval als u alle typen client-apps wilt ondersteunen. In een nieuw beleid met **Exchange Active Sync** als voorwaarde voor client-apps u geen andere client-apps selecteren.

![Voorwaardelijke toegang voor het selecteren van client-apps](./media/policy-migration/64.png)

Een consolidatie in één nieuw beleid is ook niet mogelijk als uw klassieke beleid meerdere voorwaarden bevat. Een nieuw beleid met **Exchange Active Sync** als client-apps-voorwaarde geconfigureerd, biedt geen ondersteuning voor andere voorwaarden:   

![Exchange ActiveSync biedt geen ondersteuning voor de geselecteerde voorwaarden](./media/policy-migration/08.png)

Als u een nieuw beleid hebt waarin **Exchange Active Sync** is geconfigureerd als voorwaarde voor client-apps, moet u ervoor zorgen dat niet alle andere voorwaarden zijn geconfigureerd. 

![Voorwaarden voor voorwaardelijke toegang](./media/policy-migration/16.png)
 
Met klassiek beleid op basis van apps voor Office 365 Exchange Online, dat **Exchange Active Sync** als voorwaarde voor client-apps bevat, kunnen **ondersteunde** en **niet-ondersteunde** apparaatplatforms worden ondersteund. Hoewel u geen afzonderlijke apparaatplatforms configureren in een gerelateerd nieuw beleid, u de ondersteuning beperken tot alleen [ondersteunde apparaatplatforms.](concept-conditional-access-conditions.md#device-platforms) 

![Voorwaardelijke toegang selecteren Exchange ActiveSync](./media/policy-migration/65.png)

U meerdere klassieke beleidsregels consolideren die **Exchange Active Sync** als voorwaarde voor client-apps bevatten als ze:

- Alleen **Exchange Active Sync** als voorwaarde 
- Verschillende vereisten voor het verlenen van toegang geconfigureerd

Een veelvoorkomend scenario is de consolidatie van:

- Een klassiek beleid op basis van apparaten van de klassieke Azure-portal 
- Een klassiek beleid op basis van apps in de intune-app-beveiligingsportal 
 
In dit geval u uw klassieke beleid samenvoegen tot één nieuw beleid waarin beide vereisten zijn geselecteerd.

![Subsidiecontroles voor voorwaardelijke toegang](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Apparaatplatformen

Klassiek beleid met app-gebaseerde besturingselementen zijn vooraf geconfigureerd met iOS en Android als voorwaarde voor het apparaatplatform. 

In een nieuw beleid moet u de [apparaatplatforms](concept-conditional-access-conditions.md#device-platforms) selecteren die u afzonderlijk wilt ondersteunen.

![Selectie van voorwaardelijke toegangsapparaatplatforms](./media/policy-migration/41.png)

## <a name="next-steps"></a>Volgende stappen

- [Gebruik de modus alleen-rapport voor voorwaardelijke toegang om de impact van nieuwe beleidsbeslissingen te bepalen.](concept-conditional-access-report-only.md)
- Zie [Algemene beleidsregels voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)als u wilt weten hoe u een beleid voor voorwaardelijke toegang configureren.
- Als u klaar bent om beleid voor voorwaardelijke toegang voor uw omgeving te configureren, raadpleegt u het artikel [How To: Plan uw implementatie voor voorwaardelijke toegang in Azure Active Directory](plan-conditional-access.md). 
