---
title: Privileged Identity Management (PIM) implementeren - Azure AD | Microsoft Documenten
description: Beschrijft hoe u de implementatie van Azure AD Privileged Identity Management (PIM) plannen.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/04/2020
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8c77b3454026aa309d979bd938674e7c3ae7b6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025993"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Azure AD Privileged Identity Management (PIM) implementeren

In deze stapsgewijze handleiding wordt beschreven hoe u de implementatie van Privileged Identity Management (PIM) plannen in uw Azure AD-organisatie (Azure AD).

> [!TIP]
> In dit artikel ziet u objecten die zijn gemarkeerd als:
> 
> :heavy_check_mark: **Microsoft raadt aan**
> 
> Dit zijn algemene aanbevelingen, en u moet alleen implementeren als ze van toepassing zijn op uw specifieke bedrijfsbehoeften.

## <a name="learn-about-privileged-identity-management"></a>Meer informatie over privileged identity management

Azure AD Privileged Identity Management helpt u bij het beheren van bevoorrechte beheerrollen in Azure AD, Azure-bronnen en andere Microsoft Online Services. In een wereld waarin bevoorrechte identiteiten worden toegewezen en vergeten, biedt Privileged Identity Management oplossingen zoals just-in-time toegang, goedkeuringsworkflows aanvragen en volledig geïntegreerde toegangsbeoordelingen, zodat u schadelijke gegevens identificeren, ontdekken en voorkomen activiteiten van bevoorrechte rollen in real time. Het implementeren van Privileged Identity Management om uw bevoorrechte rollen in uw hele organisatie te beheren, vermindert de risico's sterk en brengt waardevolle inzichten naar buiten over de activiteiten van uw bevoorrechte rollen.

### <a name="business-value-of-privileged-identity-management"></a>Bedrijfswaarde van Privileged Identity Management

**Risico's beheren** : beveilig uw organisatie door het principe van de [minste toegang tot bevoegdheden](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) en just-in-time-toegang af te dwingen. Door het aantal permanente toewijzingen van gebruikers naar bevoorrechte rollen te minimaliseren en goedkeuringen en MFA voor hoogte af te dwingen, u beveiligingsrisico's in verband met bevoorrechte toegang in uw organisatie sterk verminderen. Als u de minste bevoegdheden en just-in-time-toegang afdwingt, u ook een geschiedenis van toegang tot bevoorrechte rollen bekijken en beveiligingsproblemen opsporen terwijl deze zich voordoen.

**Compliance en governance aanpakken** - Het implementeren van Privileged Identity Management creëert een omgeving voor doorlopende identiteitsbeheer. Just-in-time verhoging van bevoorrechte identiteiten biedt een manier voor Privileged Identity Management om bij te houden van privileged access activiteiten in uw organisatie. U ook meldingen bekijken en ontvangen voor alle opdrachten van permanente en in aanmerking komende rollen binnen uw organisatie. Via toegangscontrole u regelmatig onnodige bevoorrechte identiteiten controleren en verwijderen en ervoor zorgen dat uw organisatie voldoet aan de strengste identiteits-, toegangs- en beveiligingsstandaarden.

**Kosten verlagen** : verlaag de kosten door inefficiënties, menselijke fouten en beveiligingsproblemen te elimineren door Privileged Identity Management correct te implementeren. Het nettoresultaat is een vermindering van cybermisdaden in verband met bevoorrechte identiteiten, die duur en moeilijk te herstellen zijn. Privileged Identity Management helpt uw organisatie ook om de kosten te verlagen die gepaard gaan met het controleren van toegangsgegevens als het gaat om het voldoen aan regelgeving en normen.

Zie Wat is Azure AD Privileged Identity Management voor meer [informatie?](pim-configure.md).

### <a name="licensing-requirements"></a>Licentievereisten

Als u Privileged Identity Management wilt gebruiken, moet uw directory een van de volgende betaalde of proeflicenties hebben:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Zie [Licentievereisten voor het gebruik van Privileged Identity Management voor](subscription-requirements.md)meer informatie.

### <a name="key-terminology"></a>Belangrijkste terminologie

| Term of concept | Beschrijving |
| --- | --- |
| in aanmerking komend | Een roltoewijzing die vereist dat een gebruiker een of meer acties uitvoert om de rol te kunnen gebruiken. Als een gebruiker in aanmerking komt voor een rol, betekent dit dat de gebruiker de rol kan activeren wanneer deze nodig is om bevoegde taken uit te voeren. Er is geen verschil in de toegang voor iemand met een permanente roltoewijzing ten opzichte van iemand die in aanmerking komt voor een roltoewijzing. Het enige verschil is dat sommige gebruikers niet voortdurend toegang nodig hebben. |
| activeren | Het proces van het uitvoeren van een of meer acties om de rol te kunnen gebruiken waarvoor de gebruiker in aanmerking komt. Acties kunnen bijvoorbeeld een meervoudige verificatiecontrole, het opgeven van een zakelijke reden of het vragen om toestemming bij aangewezen fiatteurs zijn. |
| Just-in-time-toegang (JIT) | Een model waarbij gebruikers tijdelijke machtigingen ontvangen om bepaalde taken uit te mogen voeren, waardoor kwaadwillende of onbevoegde gebruikers geen toegang kunnen krijgen na het verlopen van deze machtigingen. Toegang wordt alleen verleend wanneer gebruikers deze nodig hebben. |
| Principe van toegang met minimale bevoegdheden | Een aanbevolen beveiligingsprocedure waarbij alle gebruikers enkel de minimale bevoegdheden krijgt toegewezen die nodig zijn om de taken uit te voeren waarvoor ze bevoegd zijn. Met deze procedure wordt het aantal globale beheerders tot het minimum beperkt en worden er specifieke beheerdersrollen gebruikt voor bepaalde scenario's. |

Zie [Terminologie](pim-configure.md#terminology)voor meer informatie .

### <a name="high-level-overview-of-how-privileged-identity-management-works"></a>Overzicht op hoog niveau van hoe Privileged Identity Management werkt

1. Privileged Identity Management is zo ingesteld dat gebruikers in aanmerking komen voor bevoorrechte rollen.
1. Wanneer een in aanmerking komende gebruiker zijn bevoorrechte rol moet gebruiken, activeert hij de rol in Privileged Identity Management.
1. Afhankelijk van de instellingen voor voorkeursidentiteitsbeheer die voor de rol zijn geconfigureerd, moet de gebruiker bepaalde stappen uitvoeren (zoals het uitvoeren van meervoudige verificatie, het verkrijgen van goedkeuring of het opgeven van een reden.)
1. Zodra de gebruiker zijn rol met succes activeert, krijgt hij de rol voor een vooraf geconfigureerde periode.
1. Beheerders kunnen een geschiedenis van alle privileged identity management-activiteiten bekijken in het controlelogboek. Ze kunnen hun Azure AD-organisaties ook verder beveiligen en voldoen aan de naleving met behulp van privileged identity management-functies zoals toegangsbeoordelingen en waarschuwingen.

Zie Wat is Azure AD Privileged Identity Management voor meer [informatie?](pim-configure.md).

### <a name="roles-that-can-be-managed-by-privileged-identity-management"></a>Rollen die kunnen worden beheerd door Privileged Identity Management

**Azure AD-rollen** : deze rollen bevinden zich allemaal in Azure Active Directory (zoals globale beheerder, Exchange Administrator en beveiligingsbeheerder). U meer lezen over de rollen en de functionaliteit ervan in [beheerdersrolmachtigingen in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Zie de [minste bevoorrechte rollen per taak](../users-groups-roles/roles-delegate-by-task.md)voor hulp bij het bepalen van welke rollen u wilt toewijzen.

**Azure-bronrollen** : deze rollen zijn gekoppeld aan een Azure-bron-, resourcegroep-, abonnements- of beheergroep. Privileged Identity Management biedt just-in-time toegang tot zowel ingebouwde rollen zoals Eigenaar, User Access Administrator en Inzender, evenals [aangepaste rollen.](../../role-based-access-control/custom-roles.md) Zie [RBAC (RBAC)](../../role-based-access-control/overview.md)voor meer informatie over Azure-bronrollen.

Zie [Rollen die u niet beheren in Privileged Identity Management](pim-roles.md)voor meer informatie.

## <a name="plan-your-deployment"></a>Uw implementatie plannen

In dit gedeelte wordt gefocusd op wat u moet doen voordat u Privileged Identity Management in uw organisatie implementeert. Het is essentieel om de instructies te volgen en de concepten in deze sectie te begrijpen, omdat ze u zullen begeleiden bij het maken van het beste plan op maat van de bevoorrechte identiteiten van uw organisatie.

### <a name="identify-your-stakeholders"></a>Identificeer uw stakeholders

In de volgende sectie u alle belanghebbenden identificeren die betrokken zijn bij het project en die moeten ondertekenen, beoordelen of op de hoogte blijven. Het bevat afzonderlijke tabellen voor het implementeren van Privileged Identity Management voor Azure AD-rollen en Privileged Identity Management voor Azure-bronrollen. Voeg belanghebbenden toe aan de volgende tabel, indien van toepassing voor uw organisatie.

- SO = Afmelden voor dit project
- R = Dit project bekijken en input leveren
- I = Op de hoogte van dit project

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>Belanghebbenden: privileged Identity Management voor Azure AD-rollen

| Name | Rol | Actie |
| --- | --- | --- |
| Naam en e-mail | **Identiteitsarchitect of Azure Global Administrator**<br/>Een vertegenwoordiger van het identity management team dat bepaalt hoe deze verandering is afgestemd op de kern infrastructuur voor identiteitsbeheer in uw organisatie. | SO/R/I |
| Naam en e-mail | **Service-eigenaar / Lijnmanager**<br/>Een vertegenwoordiger van de IT-eigenaren van een dienst of een groep diensten. Ze zijn de sleutel tot het nemen van beslissingen en het helpen uitrollen van Privileged Identity Management voor hun team. | SO/R/I |
| Naam en e-mail | **Eigenaar beveiliging**<br/>Een vertegenwoordiger van het beveiligingsteam die kan ondertekenen dat het plan voldoet aan de beveiligingsvereisten van uw organisatie. | SO/R |
| Naam en e-mail | **IT support manager / Helpdesk**<br/>Een vertegenwoordiger van de IT-ondersteuningsorganisatie die vanuit helpdeskperspectief input kan leveren over de ondersteuning van deze verandering. | R/I |
| Naam en e-mail voor pilootgebruikers | **Gebruikers van bevoorrechte rollen**<br/>De groep gebruikers waarvoor privileged identity management is geïmplementeerd. Ze moeten weten hoe ze hun rol kunnen activeren zodra Privileged Identity Management is geïmplementeerd. | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-resource-roles"></a>Belanghebbenden: privileged Identity Management voor Azure-resourcerollen

| Name | Rol | Actie |
| --- | --- | --- |
| Naam en e-mail | **Abonnement / broneigenaar**<br/>Een vertegenwoordiger van de IT-eigenaren van elk abonnement of resource waarvoor u Privileged Identity Management wilt implementeren | SO/R/I |
| Naam en e-mail | **Eigenaar beveiliging**<br/>Een vertegenwoordiger van het beveiligingsteam die kan ondertekenen dat het plan voldoet aan de beveiligingsvereisten van uw organisatie. | SO/R |
| Naam en e-mail | **IT support manager / Helpdesk**<br/>Een vertegenwoordiger van de IT-ondersteuningsorganisatie die vanuit helpdeskperspectief input kan leveren over de ondersteuning van deze verandering. | R/I |
| Naam en e-mail voor pilootgebruikers | **RBAC-rolgebruikers**<br/>De groep gebruikers waarvoor privileged identity management is geïmplementeerd. Ze moeten weten hoe ze hun rol kunnen activeren zodra Privileged Identity Management is geïmplementeerd. | I |

### <a name="enable-privileged-identity-management"></a>Privileged Identity Management inschakelen

Als onderdeel van het planningsproces moet u eerst instemmen met en Privileged Identity Management inschakelen door ons te volgen met behulp van het artikel [Privileged Identity Management.](pim-getting-started.md) Het inschakelen van Privileged Identity Management geeft u toegang tot een aantal functies die speciaal zijn ontworpen om te helpen bij uw implementatie.

Als het doel is om Privileged Identity Management te implementeren voor Azure-resources, moet u onze [Discover Azure-resources](pim-resource-roles-discover-resources.md) volgen die u beheren in het artikel Privileged Identity Management. Alleen eigenaren van abonnementen en beheergroepen kunnen deze bronnen ontdekken en aan boord nemen van Privileged Identity Management. Nadat de PIM-functionaliteit is aanboord, is deze beschikbaar voor eigenaren op alle niveaus, inclusief beheergroep, abonnement, resourcegroep en resource. Als u een globale beheerder bent die Privileged Identity Management probeert te implementeren voor uw Azure-bronnen, u [de toegang verhgelden om alle Azure-abonnementen](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) te beheren om uzelf toegang te geven tot alle Azure-bronnen in de map voor detectie. We raden u echter aan om goedkeuring te krijgen van elk van uw abonnementseigenaren voordat u hun resources beheert met Privileged Identity Management.

### <a name="enforce-principle-of-least-privilege"></a>Principe van minste bevoegdheden afdwingen

Het is belangrijk om ervoor te zorgen dat u het principe van de minste bevoegdheden in uw organisatie hebt afgedwongen voor zowel uw Azure AD- als uw Azure-bronrollen.

#### <a name="azure-ad-roles"></a>Azure AD-rollen

Voor Azure AD-rollen is het gebruikelijk dat organisaties de rol Globale beheerder toewijzen aan een aanzienlijk aantal beheerders wanneer de meeste beheerders slechts één of twee specifieke beheerdersrollen nodig hebben. Bevoorrechte roltoewijzingen worden ook meestal onbeheerd gelaten.

> [!NOTE]
> Veelvoorkomende valkuilen in roldelegatie:
>
> - De beheerder die verantwoordelijk is voor Exchange krijgt de rol globale beheerder, ook al hebben ze alleen de rol van Exchange Administrator nodig om hun dagelijkse taak uit te voeren.
> - De rol Globale beheerder is toegewezen aan een Office-beheerder omdat de beheerder zowel beveiligings- als SharePoint-beheerdersrollen nodig heeft en het eenvoudiger is om slechts één rol te delegeren.
> - De beheerder heeft al lang geleden een beveiligingsbeheerrol toegewezen gekregen om een taak uit te voeren, maar is nooit verwijderd.

Volg deze stappen om het principe van de minste bevoegdheden voor uw Azure AD-rollen af te dwingen.

1. Begrijp de granulariteit van de rollen door de [beschikbare Azure AD-beheerdersrollen te](../users-groups-roles/directory-assign-admin-roles.md#available-roles)lezen en te begrijpen. U en uw team moeten ook verwijzen naar [beheerdersrollen per identiteitstaak in Azure AD,](../users-groups-roles/roles-delegate-by-task.md)waarin de minst bevoorrechte rol voor specifieke taken wordt uitgelegd.

1. Vermeld wie bevoorrechte rollen in uw organisatie heeft. U de [wizard Privileged Identity Management](pim-security-wizard.md#run-the-wizard) gebruiken om naar een pagina als de volgende pagina te gaan.

    ![Venster met bevoorrechte rollen ontdekken waarin wordt weergegeven wie bevoorrechte rollen heeft](./media/pim-deployment-plan/discover-privileged-roles-users.png)

1. Voor alle globale beheerders in uw organisatie, ontdek waarom ze de rol nodig hebben. Op basis van het lezen van de vorige documentatie moet u deze verwijderen uit de rol van globale beheerder als de taak van de persoon kan worden uitgevoerd door een of meer gedetailleerde beheerdersrollen en deze uit de rol globale beheerder verwijderen en toewijzingen dienovereenkomstig maken in Azure Active Directory (als referentie: Microsoft heeft momenteel slechts ongeveer 10 beheerders met de rol Globale Beheerder. Meer informatie over [hoe Microsoft Privileged Identity Management gebruikt).](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)

1. Bekijk voor alle andere Azure AD-rollen de lijst met toewijzingen, identificeerbeheerders die de rol niet meer nodig hebben en verwijder ze uit hun toewijzingen.

Als u de laatste twee stappen wilt automatiseren, u toegangsbeoordelingen gebruiken in Privileged Identity Management. Als u de stappen volgt om [een toegangscontrole voor Azure AD-rollen in Privileged Identity Management](pim-how-to-start-security-review.md)te starten, u een toegangscontrole instellen voor elke Azure AD-rol met een of meer leden.

![Een venster voor toegangscontrole voor Azure AD-rollen maken](./media/pim-deployment-plan/create-access-review.png)

U moet de beoordelaars instellen op **leden (zelf).** Dit stuurt een e-mail naar alle leden in de rol om hen te laten bevestigen of ze de toegang nodig hebben. U moet ook **Reden vereisen inschakelen bij goedkeuring** in de geavanceerde instellingen, zodat gebruikers kunnen aangeven waarom ze de rol nodig hebben. Op basis van deze informatie u gebruikers uit onnodige rollen verwijderen en meer gedetailleerde beheerdersrollen delegeren in het geval van globale beheerders.

Toegangsbeoordelingen zijn afhankelijk van e-mails om mensen op de hoogte te stellen van hun toegang tot de rollen. Als u bevoegde accounts hebt waaraan geen e-mails zijn gekoppeld, moet u het secundaire e-mailveld voor die accounts invullen. Zie [het kenmerk proxyAdressen in Azure AD voor](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad)meer informatie.

#### <a name="azure-resource-roles"></a>Azure-resourcerollen

Voor Azure-abonnementen en -bronnen u een vergelijkbaar access-controleproces instellen om de rollen in elk abonnement of resource te bekijken. Het doel van dit proces is om de toewijzingen van eigenaar- en gebruikerstoegangsbeheerders die aan elk abonnement of resource zijn gekoppeld, te minimaliseren en onnodige toewijzingen te verwijderen. Organisaties delegeren dergelijke taken echter vaak aan de eigenaar van elk abonnement of resource omdat ze een beter begrip hebben van de specifieke rollen (vooral aangepaste rollen).

Als u een IT-beheerder bent met de rol Globale beheerder die probeert privileged identity management voor Azure-bronnen in uw organisatie te implementeren, u [de toegang verhgelden om alle Azure-abonnementen](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) te beheren om toegang te krijgen tot elk abonnement. U vervolgens elke eigenaar van een abonnement vinden en met hen samenwerken om onnodige toewijzingen te verwijderen en de toewijzing van eigenarenterollen te minimaliseren.

Gebruikers met de rol Eigenaar voor een Azure-abonnement kunnen ook [toegangsbeoordelingen voor Azure-resources](pim-resource-roles-start-access-review.md) gebruiken om onnodige roltoewijzingen te controleren en te verwijderen die vergelijkbaar zijn met het proces dat eerder is beschreven voor Azure AD-rollen.

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>Beslissen welke roltoewijzingen moeten worden beschermd door Privileged Identity Management

Nadat u bevoorrechte roltoewijzingen in uw organisatie hebt opgemaakt, moet u beslissen welke rollen u wilt beschermen met Privileged Identity Management.

Als een rol wordt beschermd door Privileged Identity Management, moeten in aanmerking komende gebruikers die eraan zijn toegewezen, worden verhoogd om de bevoegdheden van de rol te gebruiken. Het hoogteproces kan ook bestaan uit het verkrijgen van goedkeuring, het uitvoeren van multi-factor authenticatie, en / of het verstrekken van een reden waarom ze activeren. Privileged Identity Management kan ook verhogingen bijhouden via meldingen en de logboeken van privileged Identity Management en Azure AD-controlegebeurtenislogboeken.

Het kiezen van welke rollen te beschermen met Privileged Identity Management kan moeilijk zijn en zal verschillen voor elke organisatie. In deze sectie vindt u ons advies over de beste praktijken voor Azure AD- en Azure-bronrollen.

#### <a name="azure-ad-roles"></a>Azure AD-rollen

Het is belangrijk om prioriteit te geven aan het beschermen van Azure AD-rollen met het meeste aantal machtigingen. Op basis van gebruikspatronen bij alle klanten van Privileged Identity Management zijn de top 10 Azure AD-rollen die worden beheerd door Privileged Identity Management:

1. Globale beheerder
1. Beveiligingsbeheerder
1. Gebruikersbeheerder
1. Exchange-beheerder
1. SharePoint-beheerder
1. Intune-beheerder
1. Beveiligingslezer
1. Servicebeheerder
1. Factureringsbeheerder
1. Skype voor Bedrijven-beheerder

> [!TIP]
> :heavy_check_mark: **Microsoft raadt** u aan al uw globale beheerders en beveiligingsbeheerders te beheren met behulp van Privileged Identity Management als eerste stap, omdat zij degenen zijn die het meeste kwaad kunnen doen wanneer ze worden gecompromitteerd.

Het is belangrijk om te overwegen welke gegevens en machtigingen het meest gevoelig zijn voor uw organisatie. Sommige organisaties willen bijvoorbeeld hun Power BI Administrator-rol of hun Rol van Teams-beheerder beveiligen met Privileged Identity Management, omdat ze toegang hebben tot gegevens en/of kernworkflows kunnen wijzigen.

Als er rollen zijn toegewezen aan gastgebruikers, zijn ze bijzonder kwetsbaar voor aanvallen.

> [!TIP]
> :heavy_check_mark: **Microsoft raadt** u aan alle rollen te beheren met gastgebruikers die Privileged Identity Management gebruiken om risico's in verband met gecompromitteerde gastgebruikersaccounts te verminderen.

Lezersrollen zoals de Directory Reader, Message Center Reader en Security Reader worden soms verondersteld minder belangrijk te zijn in vergelijking met andere rollen omdat ze geen schrijftoestemming hebben. We hebben echter gezien dat sommige klanten deze rollen ook beschermen omdat aanvallers die toegang hebben gekregen tot deze accounts mogelijk gevoelige gegevens kunnen lezen, zoals persoonlijke gegevens. U moet hiermee rekening houden bij de beslissing of lezerrollen in uw organisatie moeten worden beheerd met privileged identity management.

#### <a name="azure-resource-roles"></a>Azure-resourcerollen

Wanneer u beslist welke roltoewijzingen moeten worden beheerd met privileged identity management voor Azure-bron, moet u eerst de abonnementen/resources identificeren die het meest van vitaal belang zijn voor uw organisatie. Voorbeelden van dergelijke abonnementen/bronnen zijn:

- Bronnen die de meest gevoelige gegevens hosten
- Bronnen waarvan kerntoepassingen afhankelijk zijn van

Als u een globale beheerder bent die moeite heeft met het bepalen welke abonnementen/resources het belangrijkst zijn, moet u contact opnemen met abonnementseigenaren in uw organisatie om een lijst met resources te verzamelen die door elk abonnement worden beheerd. U moet vervolgens samenwerken met de eigenaren van het abonnement om de resources te groeperen op basis van ernstniveau in het geval dat ze worden gecompromitteerd (laag, gemiddeld, hoog). U moet prioriteit geven aan het beheren van resources met Privileged Identity Management op basis van dit ernstniveau.

> [!TIP]
> :heavy_check_mark: **Microsoft raadt** u aan om met abonnements-/broneigenaren van kritieke services te werken om de workflow Voor bevoegde identiteitsbeheer in te stellen voor alle rollen binnen gevoelige abonnementen/resources.

Privileged Identity Management voor Azure-resources ondersteunt serviceaccounts die aan de tijd gebonden zijn. U moet serviceaccounts precies hetzelfde behandelen als hoe u een account van een gewone gebruiker zou behandelen.

Voor abonnementen/resources die niet zo kritisch zijn, hoeft u geen privileged identity management in te stellen voor alle rollen. U moet echter nog steeds de rollen beheerder van de eigenaar en gebruikerstoegang beveiligen met Privileged Identity Management.

> [!TIP]
> :heavy_check_mark: **Microsoft raadt** u aan eigenaarrollen en gebruikerstoegangsbeheerdersrollen van alle abonnementen/bronnen te beheren met behulp van Privileged Identity Management.

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Beslissen welke roltoewijzingen permanent of in aanmerking moeten komen

Zodra u hebt besloten welke lijst met rollen moet worden beheerd door Privileged Identity Management, moet u beslissen welke gebruikers de in aanmerking komende rol moeten krijgen ten opzichte van de permanent actieve rol. Permanent actieve rollen zijn de normale rollen die zijn toegewezen via Azure Active Directory- en Azure-resources, terwijl in aanmerking komende rollen alleen kunnen worden toegewezen in Privileged Identity Management.

> [!TIP]
> :heavy_check_mark: **Microsoft raadt u aan** nul permanent actieve toewijzingen te hebben voor zowel Azure AD-rollen als Azure-resourcerollen, met andere dan de aanbevolen twee [noodtoegangsaccounts voor break-glass,](../users-groups-roles/directory-emergency-access.md)die de permanente rol globale beheerder moeten hebben.

Hoewel we zero standing administrator aanbevelen, is het soms moeilijk voor organisaties om dit meteen te bereiken. Hier zijn dingen om te overwegen bij het maken van deze beslissing:

- Hoogtefrequentie : als de gebruiker de bevoegde toewijzing maar één keer nodig heeft, moet hij de permanente toewijzing niet hebben. Aan de andere kant, als de gebruiker de rol nodig heeft voor zijn dagelijkse werk en het gebruik van Privileged Identity Management zijn productiviteit sterk zou verminderen, kunnen ze in aanmerking komen voor de permanente rol.
- Gevallen die specifiek zijn voor uw organisatie - Als de persoon die de in aanmerking komende rol krijgt, van een zeer ver-of een hooggeplaatste uitvoerende macht is tot het punt dat het communiceren en afdwingen van het hoogteproces moeilijk is, kunnen ze worden overwogen voor de permanente rol.

> [!TIP]
> :heavy_check_mark: **Microsoft raadt** u aan terugkerende toegangsbeoordelingen in te stellen voor gebruikers met permanente roltoewijzingen (mocht u die hebben). Meer informatie over terugkerende toegangsbeoordeling in het laatste gedeelte van dit implementatieplan

### <a name="draft-your-privileged-identity-management-settings"></a>Uw instellingen voor voorkeursidentiteitsbeheer opstellen

Voordat u uw privileged identity management-oplossing implementeert, is het een goede gewoonte om uw instellingen voor voorkeursidentiteitsbeheer op te stellen voor elke bevoorrechte rol die uw organisatie gebruikt. In deze sectie worden enkele voorbeelden gegeven van instellingen voor voorkeursidentiteitsbeheer voor bepaalde rollen (deze zijn alleen ter referentie en kunnen voor uw organisatie verschillen). Elk van deze instellingen wordt in detail uitgelegd met de aanbevelingen van Microsoft na de tabellen.

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Instellingen voor privileged Identity Management voor Azure AD-rollen

| Rol | MFA vereisen | Melding | Incident ticket | Goedkeuring vereisen | Fiatteur | Activeringsduur | Permanente beheerder |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Globale beheerder | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Andere globale beheerders | 1 uur | Accounts voor noodtoegang |
| Exchange-beheerder | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | Geen | 2 uur | Geen |
| Helpdeskbeheerder | :x: | :x: | :heavy_check_mark: | :x: | Geen | 8 uur | Geen |

#### <a name="privileged-identity-management-settings-for-azure-resource-roles"></a>Instellingen voor bevoegde identiteitsbeheer voor Azure-bronrollen

| Rol | MFA vereisen | Melding | Goedkeuring vereisen | Fiatteur | Activeringsduur | Actieve beheerder | Actieve vervaldatum | In aanmerking komende vervaldatum |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Eigenaar van kritische abonnementen | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Andere eigenaren van het abonnement | 1 uur | Geen | N.v.t. | 3 maanden |
| Gebruikerstoegangsbeheerder van minder kritieke abonnementen | :heavy_check_mark: | :heavy_check_mark: | :x: | Geen | 1 uur | Geen | N.v.t. | 3 maanden |
| Inzender voor virtuele machines | :x: | :heavy_check_mark: | :x: | Geen | 3 uur | Geen | N.v.t. | 6 maanden |

In de volgende tabel worden elk van de instellingen beschreven.

| Instelling | Beschrijving |
| --- | --- |
| Rol | Naam van de rol waarvoor u de instellingen definieert. |
| MFA vereisen | Of de in aanmerking komende gebruiker MFA moet uitvoeren voordat de rol wordt geactiveerd.<br/><br/> :heavy_check_mark: **Microsoft raadt** u aan MFA af te dwingen voor alle beheerdersrollen, vooral als de rollen gastgebruikers hebben. |
| Melding | Als de globale beheerder, de beheerder van de bevoorrechte rol en de beveiligingsbeheerder in de organisatie zijn ingesteld, ontvangt deze een e-mailmelding wanneer een in aanmerking komende gebruiker de rol activeert.<br/><br/>**Let op:** Sommige organisaties hebben geen e-mailadres gekoppeld aan hun beheerdersaccounts, om deze e-mailmeldingen te ontvangen, moet u een alternatief e-mailadres instellen, zodat beheerders deze e-mails zullen ontvangen. |
| Incident ticket | Of de in aanmerking komende gebruiker een incidentticketnummer moet registreren bij het activeren van zijn of haar rol. Met deze instelling kan een organisatie elke activering identificeren met een intern incidentnummer om ongewenste activeringen te beperken.<br/><br/> :heavy_check_mark: **Microsoft raadt aan** gebruik te maken van incidentticketnummers om Privileged Identity Management aan uw interne systeem te koppelen. Dit is vooral handig voor fiatteurs die context nodig hebben voor de activering. |
| Goedkeuring vereisen | Of de in aanmerking komende gebruiker goedkeuring moet krijgen om de rol te activeren.<br/><br/> :heavy_check_mark: **Microsoft raadt** u aan goedkeuring in te stellen voor rollen met de meeste toestemming. Op basis van gebruikspatronen van alle klanten voor privileged identity management zijn globale beheerder, gebruikersbeheerder, exchange-beheerder, beveiligingsbeheerder en wachtwoordbeheerder de meest voorkomende rollen met goedkeuringsinstelling. |
| Fiatteur | Als goedkeuring vereist is om de in aanmerking komende rol te activeren, geeft u een lijst van de personen die de aanvraag moeten goedkeuren. Standaard stelt Privileged Identity Management de fiatter in op alle gebruikers die een bevoorrechte rolbeheerder zijn, ongeacht of ze permanent of in aanmerking komen.<br/><br/>**Let op:** Als een gebruiker zowel in aanmerking komt voor een Azure AD-rol als een goedkeurder van de rol, kan deze gebruiker zichzelf niet goedkeuren.<br/><br/> :heavy_check_mark: **Microsoft raadt** u aan om fiatteurs te kiezen als degenen die het meest goed geïnformeerd zijn over de specifieke rol en de frequente gebruikers in plaats van een globale beheerder. |
| Activeringsduur | Hoe lang een gebruiker in de rol wordt geactiveerd voordat deze verloopt. |
| Permanente beheerder | Lijst van gebruikers die een permanente beheerder voor de rol zullen zijn (nooit hoeven te activeren).<br/><br/> :heavy_check_mark: **Microsoft raadt u aan** voor alle rollen een nulstaande beheerder te hebben, behalve globale beheerders. Lees er meer over in wie in aanmerking moet komen en wie permanent actief moet zijn deel van dit plan. |
| Actieve beheerder | Voor Azure-resources is actieve beheerder de lijst met gebruikers die nooit hoeven te activeren om de rol te kunnen gebruiken. Dit wordt niet aangeduid als permanente beheerder zoals in Azure AD-rollen, omdat u een vervaldatum instellen voor wanneer de gebruiker deze rol verliest. |
| Actieve vervaldatum | Een actieve roltoewijzing voor Azure-resourcerollen verloopt na deze geconfigureerde periode. U kiezen uit 15 dagen, 1 maand, 3 maanden, 6 maanden, 1 jaar of permanent actief. |
| In aanmerking komende vervaldatum | Een in aanmerking komende roltoewijzing voor Azure-resourcerollen verloopt na deze geconfigureerde periode. U kiezen uit 15 dagen, 1 maand, 3 maanden, 6 maanden, 1 jaar of permanent in aanmerking komen. |

## <a name="implement-your-solution"></a>Implementeer uw oplossing

De basis van een goede planning is de basis waarop u een toepassing met succes implementeren met Azure Active Directory.  Het biedt intelligente beveiliging en integratie die onboarding vereenvoudigt en tegelijkertijd de tijd voor succesvolle implementaties verkort.  Deze combinatie zorgt ervoor dat uw toepassing met gemak wordt geïntegreerd en tegelijkertijd de downtime voor uw eindgebruikers wordt verzacht.

### <a name="identify-test-users"></a>Testgebruikers identificeren

Gebruik deze sectie om een groep gebruikers en of groepen gebruikers te identificeren om de implementatie te valideren. Op basis van de instellingen die u in de planningssectie hebt geselecteerd, u de gebruikers identificeren die u voor elke rol wilt testen.

> [!TIP]
> :heavy_check_mark: **Microsoft raadt** u aan service-eigenaren van elke Azure AD-rol de testgebruikers te maken, zodat ze vertrouwd kunnen raken met het proces en een interne pleitbezorger kunnen worden voor de uitrol.

Identificeer in deze tabel de testgebruikers die controleren of de instellingen voor elke rol werken.

| Rolnaam | Gebruikers testen |
| --- | --- |
| &lt;Rolnaam&gt; | &lt;Gebruikers om de rol te testen&gt; |
| &lt;Rolnaam&gt; | &lt;Gebruikers om de rol te testen&gt; |

### <a name="test-implementation"></a>Testimplementatie

Nu u de testgebruikers hebt geïdentificeerd, gebruikt u deze stap om Privileged Identity Management voor uw testgebruikers te configureren. Als uw organisatie de workflow voor privileged identity management wil opnemen in uw eigen interne toepassing in plaats van privileged identity management te gebruiken in de Azure-portal, worden alle bewerkingen in Privileged Identity Management ook ondersteund via onze [graph API.](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root)

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>Privileged Identity Management configureren voor Azure AD-rollen

1. [Configureer de azure AD-rolinstellingen](pim-how-to-change-default-settings.md) op basis van wat u hebt gepland.

1. Navigeer naar **Azure AD-rollen,** klik op **Rollen**en selecteer de rol die u zojuist hebt geconfigureerd.

1. Voor de groep testgebruikers, als ze al een permanente beheerder zijn, u ze in aanmerking laten komen door ernaar te zoeken en ze te converteren van permanent naar in aanmerking komen door op de drie puntjes op hun rij te klikken. Als ze de roltoewijzingen nog niet hebben, kun je [een nieuwe in aanmerking komende opdracht maken.](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role)

1. Herhaal stap 1-3 voor alle rollen die u wilt testen.

1. Zodra u de testgebruikers hebt ingesteld, moet u hen de koppeling sturen voor het [activeren van hun Azure AD-rol.](pim-how-to-activate-role.md)

#### <a name="configure-privileged-identity-management-for-azure-resource-roles"></a>Privileged Identity Management configureren voor Azure-bronrollen

1. [Configureer de instellingen van de Azure-bronrol](pim-resource-roles-configure-role-settings.md) voor een rol in een abonnement of bron die u wilt testen.

1. Navigeer naar **Azure-bronnen** voor dat abonnement en klik op **Rollen**, selecteer de rol die u zojuist hebt geconfigureerd.

1. Voor de groep testgebruikers u, als ze al een actieve beheerder zijn, deze in aanmerking laten komen door ernaar te zoeken en [hun roltoewijzing bij te werken.](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment) Als ze de rol nog niet hebben, u [een nieuwe rol toewijzen.](pim-resource-roles-assign-roles.md#assign-a-role)

1. Herhaal stap 1-3 voor alle rollen die u wilt testen.

1. Zodra u de testgebruikers hebt ingesteld, moet u hen de koppeling sturen voor het [activeren van hun Azure-bronrol.](pim-resource-roles-activate-your-roles.md)

U moet deze fase gebruiken om te controleren of alle configuratie die u voor de rollen hebt ingesteld, correct werkt. Gebruik de volgende tabel om uw tests te documenteren. U moet deze fase ook gebruiken om de communicatie met de betrokken gebruikers te optimaliseren.

| Rol | Verwacht gedrag tijdens activering | Werkelijke resultaten |
| --- | --- | --- |
| Globale beheerder | (1) Mfa vereisen<br/>2.<br/>(3) De goedkeurder ontvangt kennisgeving en kan<br/>(4) Rol verloopt na vooraf ingestelde tijd |  |
| Eigenaar van abonnement *X* | (1) Mfa vereisen<br/>(2) in aanmerking komende toewijzing verloopt na geconfigureerde periode |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>Communiceer privileged identity management met betrokken belanghebbenden

Als u Privileged Identity Management implementeert, worden extra stappen geïntroduceerd voor gebruikers met bevoorrechte rollen. Hoewel Privileged Identity Management beveiligingsproblemen in verband met bevoorrechte identiteiten sterk vermindert, moet de wijziging effectief worden gecommuniceerd voordat de implementatie voor de hele tenant wordt uitgevoerd. Afhankelijk van het aantal getroffen beheerders kiezen organisaties er vaak voor om een intern document, een video of een e-mail over de wijziging te maken. Vaak opgenomen in deze mededelingen zijn:

- Wat is PIM
- Wat is het voordeel voor de organisatie
- Wie zal worden beïnvloed
- Wanneer wordt PIM uitgerold
- Welke aanvullende stappen zijn vereist voor gebruikers om hun rol te activeren
    - Stuur links naar onze documentatie:
    - [Azure AD-rollen activeren](pim-how-to-activate-role.md)
    - [Azure-bronrollen activeren](pim-resource-roles-activate-your-roles.md)
- Contactgegevens of helpdeskkoppeling voor eventuele problemen in verband met PIM

> [!TIP]
> :heavy_check_mark: **Microsoft raadt** u aan om tijd in te stellen met uw helpdesk/ondersteuningsteam om ze door de workflow Van privileged identity management te leiden (als uw organisatie een intern IT-ondersteuningsteam heeft). Geef hen de juiste documentatie en uw contactgegevens.

### <a name="move-to-production"></a>Verplaatsen naar productie

Zodra uw testen is voltooid en succesvol is, verplaatst u Privileged Identity Management naar productie door alle stappen in de testfasen te herhalen voor alle gebruikers van elke rol die u hebt gedefinieerd in uw configuratie van het bevoegde identiteitsbeheer. Voor Privileged Identity Management voor Azure AD-rollen testen en implementeren organisaties vaak Privileged Identity Management voor globale beheerders voordat ze Privileged Identity Management voor andere rollen testen en uitrollen. Ondertussen testen en implementeren organisaties voor Azure-resources normaal gesproken Privileged Identity Management één Azure-abonnement tegelijk.

### <a name="in-the-case-a-rollback-is-needed"></a>In het geval dat een terugdraaiing nodig is

Als Privileged Identity Management niet naar wens werkt in de productieomgeving, kunnen de volgende terugdraaistappen u helpen om terug te keren naar een bekende goede status voordat u Privileged Identity Management instelt:

#### <a name="azure-ad-roles"></a>Azure AD-rollen

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Azure **AD Privileged Identity Management openen**.
1. Klik op **Azure AD-rollen** en klik vervolgens op **Rollen**.
1. Voor elke rol die u hebt geconfigureerd, klikt u op de ellips (**...**) voor alle gebruikers met een in aanmerking komende toewijzing.
1. Klik **op** de optie Permanent maken om de roltoewijzing permanent te maken.

#### <a name="azure-resource-roles"></a>Azure-resourcerollen

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Azure **AD Privileged Identity Management openen**.
1. Klik op **Azure-resources** en klik vervolgens op een abonnement of resource die u wilt terugdraaien.
1. Klik **op Rollen**.
1. Voor elke rol die u hebt geconfigureerd, klikt u op de ellips (**...**) voor alle gebruikers met een in aanmerking komende toewijzing.
1. Klik **op** de optie Permanent maken om de roltoewijzing permanent te maken.

## <a name="next-steps-after-deploying"></a>Volgende stappen na implementatie

Het succesvol implementeren van Privileged Identity Management in productie is een belangrijke stap voorwaarts in termen van het beveiligen van de bevoorrechte identiteiten van uw organisatie. Met de implementatie van Privileged Identity Management komen extra Privileged Identity Management-functies die u moet gebruiken voor beveiliging en naleving.

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>Waarschuwingen voor privileged identity management gebruiken om uw bevoorrechte toegang te beveiligen

U moet de ingebouwde waarschuwingsfunctionaliteit van Privileged Identity Management gebruiken om uw tenant beter te beschermen. Zie [beveiligingswaarschuwingen voor](pim-how-to-configure-security-alerts.md#security-alerts)meer informatie. Deze waarschuwingen omvatten: beheerders gebruiken geen bevoorrechte rollen, rollen worden toegewezen buiten Privileged Identity Management, rollen worden te vaak en meer geactiveerd. Om uw organisatie volledig te beschermen, moet u regelmatig uw lijst met waarschuwingen doornemen en de problemen oplossen. U uw waarschuwingen op de volgende manier weergeven en oplossen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Azure **AD Privileged Identity Management openen**.
1. Klik op **Azure AD-rollen** en klik vervolgens op **Waarschuwingen**.

> [!TIP]
> :heavy_check_mark: **Microsoft raadt** u aan om alle waarschuwingen die met hoge ernst zijn gemarkeerd, onmiddellijk te behandelen. Voor waarschuwingen met gemiddelde en lage ernst moet u op de hoogte blijven en wijzigingen aanbrengen als u denkt dat er een beveiligingsbedreiging is.

Als een van de specifieke waarschuwingen niet nuttig is of niet van toepassing is op uw organisatie, u de waarschuwing altijd op de pagina waarschuwingen verwijderen. U dit ontslag altijd later terugdraaien op de pagina Azure AD-instellingen.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Terugkerende toegangsbeoordelingen instellen om regelmatig de bevoorrechte identiteiten van uw organisatie te controleren

Toegangsbeoordelingen zijn de beste manier voor u om gebruikers met bevoorrechte rollen of specifieke revisoren te vragen of elke gebruiker de bevoorrechte identiteit nodig heeft. Toegangsbeoordelingen zijn geweldig als u het aanvalsoppervlak wilt verminderen en compliant wilt blijven. Zie [Azure AD-rollen toegang tot beoordelingen](pim-how-to-start-security-review.md) en [overzichten voor Azure-bronrollen voor](pim-resource-roles-start-access-review.md)meer informatie over het starten van een toegangscontrole . Voor sommige organisaties is het uitvoeren van periodieke toegangscontrole vereist om te blijven voldoen aan wet- en regelgeving, terwijl voor anderen toegangscontrole de beste manier is om het principe van de minste bevoegdheden in uw organisatie af te dwingen.

> [!TIP]
> :heavy_check_mark: **Microsoft raadt** u aan driemaandelijkse toegangsbeoordelingen in te stellen voor al uw Azure AD- en Azure-bronrollen.

In de meeste gevallen is de revisor voor Azure AD-rollen de gebruikers zelf, terwijl de revisor voor Azure-bronrollen de eigenaar is van het abonnement, waarin de rol zich bevindt. Het is echter vaak het geval wanneer bedrijven bevoorrechte accounts hebben die niet zijn gekoppeld aan het e-mailadres van een bepaalde persoon. In die gevallen leest en beoordeelt niemand de toegang.

> [!TIP]
> :heavy_check_mark: **Microsoft raadt** u aan een secundair e-mailadres toe te voegen voor alle accounts met bevoorrechte roltoewijzingen die niet zijn gekoppeld aan een regelmatig gecontroleerd e-mailadres

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Haal het meeste uit uw controlelogboek om de beveiliging en naleving te verbeteren

Het auditlogis de plek waar u up-to-date blijven en voldoen aan de regelgeving. Privileged Identity Management slaat momenteel een geschiedenis van 30 dagen van alle geschiedenis van uw organisatie op in het controlelogboek, waaronder:

- Activering/deactivering van in aanmerking komende rollen
- Roltoewijzingsactiviteiten binnen en buiten Privileged Identity Management
- Wijzigingen in rolinstellingen
- Activiteiten aanvragen/goedkeuren/weigeren voor functieactivering met goedkeuringsinstelling
- Bijwerken naar waarschuwingen

U hebt toegang tot deze controlelogboeken als u een globale beheerder of een bevoorrechte rolbeheerder bent. Zie [controlegeschiedenis voor Azure AD-rollen](pim-how-to-use-audit-log.md) en [auditgeschiedenis voor Azure-bronrollen voor](azure-pim-resource-rbac.md)meer informatie.

> [!TIP]
> :heavy_check_mark: **Microsoft raadt** u aan om ten minste één beheerder wekelijks alle controlegebeurtenissen te laten lezen en uw controlegebeurtenissen maandelijks te exporteren.

Als u uw controlegebeurtenissen automatisch voor een langere periode wilt opslaan, wordt het controlelogboek van Privileged Identity Management automatisch gesynchroniseerd met de [Azure AD-controlelogboeken.](../reports-monitoring/concept-audit-logs.md)

> [!TIP]
> :heavy_check_mark: **Microsoft raadt** u aan [azure-logboekbewaking](../reports-monitoring/concept-activity-logs-azure-monitor.md) in te stellen om controlegebeurtenissen in een Azure-opslagaccount te archiveren voor de noodzaak van beveiliging en naleving.
