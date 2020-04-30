---
title: Privileged Identity Management implementeren (PIM)-Azure AD | Microsoft Docs
description: Hierin wordt beschreven hoe u de implementatie van Azure AD Privileged Identity Management (PIM) plant.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/28/2020
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51a60d307ca7dac139db0097283fc08e9e41624c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82233530"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Azure AD Privileged Identity Management implementeren (PIM)

In deze stapsgewijze hand leiding wordt beschreven hoe u de implementatie van Privileged Identity Management (PIM) kunt plannen in uw organisatie van Azure Active Directory (Azure AD).

> [!TIP]
> In dit artikel ziet u items die zijn gemarkeerd als:
> 
> : heavy_check_mark: **micro soft raadt** aan
> 
> Dit zijn algemene aanbevelingen en u moet alleen implementeren als ze van toepassing zijn op uw specifieke bedrijfs behoeften.

## <a name="learn-about-privileged-identity-management"></a>Meer informatie over Privileged Identity Management

Azure AD Privileged Identity Management helpt u bij het beheren van geprivilegieerde beheerders rollen in azure AD, Azure-resources en andere online services van micro soft. In een wereld waar bevoorrechte identiteiten zijn toegewezen en zijn verg eten, biedt Privileged Identity Management oplossingen zoals just-in-time-toegang, werk stromen voor aanvragen en volledig geïntegreerde toegangs beoordelingen zodat u in real-time schadelijke activiteiten van geprivilegieerde rollen kunt identificeren, detecteren en voor komen. Wanneer u Privileged Identity Management implementeert voor het beheren van uw bevoegde rollen in uw organisatie, wordt het risico aanzienlijk verminderd terwijl halen waardevol inzicht heeft in de activiteiten van uw geprivilegieerde rollen.

### <a name="business-value-of-privileged-identity-management"></a>Bedrijfs waarde van Privileged Identity Management

**Beheer Risico's** : Beveilig uw organisatie door het principe van [minimale bevoegdheden toegang](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) en just-in-time-toegang af te dwingen. Door het aantal permanente toewijzingen van gebruikers aan bevoorrechte rollen en het afdwingen van goed keuringen en MFA voor uitbrei ding te minimaliseren, kunt u de beveiligings Risico's die betrekking hebben op bevoorrechte toegang in uw organisatie, aanzienlijk verminderen. Als u de minimale bevoegdheden afdwingt en just-in-time-toegang, kunt u ook een overzicht van de toegang tot geprivilegieerde rollen bekijken en beveiligings problemen volgen wanneer deze zich voordoen.

**Adres naleving en governance** -implementatie privileged Identity Management maakt een omgeving voor een on-continue identiteits bestuur. De just-in-time verhoging van geprivilegieerde identiteiten biedt een manier om Privileged Identity Management om de privileged Access-activiteiten in uw organisatie bij te houden. U kunt ook meldingen weer geven en ontvangen voor alle toewijzingen van permanente en in aanmerking komende rollen binnen uw organisatie. U kunt met behulp van toegangs controle regel matig de niet-gemachtigde identiteiten controleren en verwijderen en ervoor zorgen dat uw organisatie voldoet aan de meest uitgebreide identiteits-, toegangs-en beveiligings vereisten.

**Kosten verlagen** : kosten verlagen door inefficiëntie, menselijke fout en beveiligings problemen te elimineren door privileged Identity Management correct te implementeren. Het netto resultaat is een vermindering van Cyber-misdrijven die zijn gekoppeld aan bevoorrechte identiteiten, die kostbaar en moeilijk te herstellen zijn. Privileged Identity Management helpt uw organisatie ook de kosten te verlagen die zijn gekoppeld aan de controle van toegangs gegevens wanneer het gaat om te voldoen aan de regels en standaarden.

Zie [Wat is Azure AD privileged Identity Management?](pim-configure.md)voor meer informatie.

### <a name="licensing-requirements"></a>Licentievereisten

Als u Privileged Identity Management wilt gebruiken, moet uw directory over een van de volgende betaalde of proef licenties beschikken:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Zie [licentie vereisten voor het gebruik van privileged Identity Management](subscription-requirements.md)voor meer informatie.

### <a name="key-terminology"></a>Belangrijkste terminologie

| Term of concept | Beschrijving |
| --- | --- |
| in aanmerking komend | Een roltoewijzing die vereist dat een gebruiker een of meer acties uitvoert om de rol te kunnen gebruiken. Als een gebruiker in aanmerking komt voor een rol, betekent dit dat de gebruiker de rol kan activeren wanneer deze nodig is om bevoegde taken uit te voeren. Er is geen verschil in de toegang voor iemand met een permanente roltoewijzing ten opzichte van iemand die in aanmerking komt voor een roltoewijzing. Het enige verschil is dat sommige gebruikers niet voortdurend toegang nodig hebben. |
| activeren | Het proces van het uitvoeren van een of meer acties om de rol te kunnen gebruiken waarvoor de gebruiker in aanmerking komt. Acties kunnen bijvoorbeeld een meervoudige verificatiecontrole, het opgeven van een zakelijke reden of het vragen om toestemming bij aangewezen fiatteurs zijn. |
| Just-in-time-toegang (JIT) | Een model waarbij gebruikers tijdelijke machtigingen ontvangen om bepaalde taken uit te mogen voeren, waardoor kwaadwillende of onbevoegde gebruikers geen toegang kunnen krijgen na het verlopen van deze machtigingen. Toegang wordt alleen verleend wanneer gebruikers deze nodig hebben. |
| Principe van toegang met minimale bevoegdheden | Een aanbevolen beveiligingsprocedure waarbij alle gebruikers enkel de minimale bevoegdheden krijgt toegewezen die nodig zijn om de taken uit te voeren waarvoor ze bevoegd zijn. Met deze procedure wordt het aantal globale beheerders tot het minimum beperkt en worden er specifieke beheerdersrollen gebruikt voor bepaalde scenario's. |

Zie [terminologie](pim-configure.md#terminology)voor meer informatie.

### <a name="high-level-overview-of-how-privileged-identity-management-works"></a>Overzicht op hoog niveau van de werking van Privileged Identity Management

1. Privileged Identity Management is zo ingesteld dat gebruikers in aanmerking komen voor bevoegde rollen.
1. Wanneer een in aanmerking komende gebruiker hun bevoorrechte rol moet gebruiken, activeren ze de rol in Privileged Identity Management.
1. Afhankelijk van de Privileged Identity Management instellingen die voor de rol zijn geconfigureerd, moet de gebruiker bepaalde stappen volt ooien (zoals het uitvoeren van multi-factor Authentication, goed keuring of het opgeven van een reden).
1. Zodra de gebruiker zijn of haar rol heeft geactiveerd, krijgt deze de rol voor een vooraf geconfigureerde tijds periode.
1. Beheerders kunnen een geschiedenis van alle Privileged Identity Management activiteiten in het audit logboek weer geven. Ze kunnen hun Azure AD-organisaties ook verder beveiligen en voldoen aan de naleving met behulp van Privileged Identity Management-functies, zoals toegangs beoordelingen en waarschuwingen.

Zie [Wat is Azure AD privileged Identity Management?](pim-configure.md)voor meer informatie.

### <a name="roles-that-can-be-managed-by-privileged-identity-management"></a>Rollen die kunnen worden beheerd door Privileged Identity Management

**Azure AD-rollen** : deze rollen bevinden zich allemaal in azure Active Directory (zoals globale beheerder, Exchange-beheerder en beveiligings beheerder). Meer informatie over de rollen en hun functionaliteit vindt u in de [beheerders bevoegdheden in azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Zie [minst geprivilegieerde rollen per taak](../users-groups-roles/roles-delegate-by-task.md)voor hulp bij het bepalen van de rollen die aan uw beheerders worden toegewezen.

**Azure-resource rollen** : deze rollen zijn gekoppeld aan een Azure-resource, resource groep, abonnement of beheer groep. Privileged Identity Management biedt just-in-time-toegang tot zowel ingebouwde rollen als eigenaar, beheerder van gebruikers toegang en Inzender, evenals [aangepaste rollen](../../role-based-access-control/custom-roles.md). Zie [op rollen gebaseerd toegangs beheer (RBAC)](../../role-based-access-control/overview.md)voor meer informatie over Azure-resource rollen.

Zie [rollen die u niet kunt beheren in privileged Identity Management](pim-roles.md)voor meer informatie.

## <a name="plan-your-deployment"></a>Uw implementatie plannen

In deze sectie wordt aandacht besteed aan wat u moet doen voordat u Privileged Identity Management in uw organisatie implementeert. Het is essentieel om de instructies te volgen en inzicht te krijgen in de concepten in deze sectie, omdat ze u helpen bij het maken van het beste plan dat is afgestemd op de bevoegde identiteiten van uw organisatie.

### <a name="identify-your-stakeholders"></a>Identificeer uw belanghebbenden

De volgende sectie helpt u bij het identificeren van alle belanghebbenden die betrokken zijn bij het project en die moeten worden afgemeld, beoordeeld of op de hoogte moeten blijven. Het bevat afzonderlijke tabellen voor het implementeren van Privileged Identity Management voor Azure AD-rollen en Privileged Identity Management voor Azure-resource rollen. Voeg belanghebbenden toe aan de volgende tabel die geschikt is voor uw organisatie.

- SO = afmelden voor dit project
- R = dit project controleren en een invoer opgeven
- Ik = op de hoogte van dit project

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>Belanghebbenden: Privileged Identity Management voor Azure AD-rollen

| Naam | Rol | Bewerking |
| --- | --- | --- |
| Naam en e-mail adres | **Identiteits architect of Azure Global-beheerder**<br/>Een vertegenwoordiger van het team voor identiteits beheer dat verantwoordelijk is voor het bepalen van de manier waarop deze wijziging wordt afgestemd op de infra structuur voor identiteits beheer in uw organisatie. | SO/R/I |
| Naam en e-mail adres | **Eigenaar van service/regel beheer**<br/>Een vertegenwoordiger van de IT-eigen aars van een service of een groep services. Ze zijn belang rijk voor het nemen van beslissingen en het helpen bij het inrollen van Privileged Identity Management voor hun team. | SO/R/I |
| Naam en e-mail adres | **Eigenaar van beveiliging**<br/>Een vertegenwoordiger van het beveiligings team dat zich kan afmelden dat het plan voldoet aan de beveiligings vereisten van uw organisatie. | SO/R |
| Naam en e-mail adres | **IT-ondersteunings Manager/Help Desk**<br/>Een vertegenwoordiger van de IT-ondersteunings organisatie die in het perspectief van de Help Desk invoer kan bieden over de ondersteuning van deze wijziging. | R/I |
| Naam en e-mail adres voor pilot gebruikers | **Gebruikers met geprivilegieerde rollen**<br/>De groep gebruikers waarvoor privileged Identity Management is geïmplementeerd. Ze moeten weten hoe ze hun rollen kunnen activeren zodra Privileged Identity Management is geïmplementeerd. | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-resource-roles"></a>Belanghebbenden: Privileged Identity Management voor Azure-resource rollen

| Naam | Rol | Bewerking |
| --- | --- | --- |
| Naam en e-mail adres | **Eigenaar van abonnement/resource**<br/>Een vertegenwoordiger van de IT-eigen aars van elk abonnement of resource dat u wilt implementeren Privileged Identity Management voor | SO/R/I |
| Naam en e-mail adres | **Eigenaar van beveiliging**<br/>Een vertegenwoordiger van het beveiligings team dat zich kan afmelden dat het plan voldoet aan de beveiligings vereisten van uw organisatie. | SO/R |
| Naam en e-mail adres | **IT-ondersteunings Manager/Help Desk**<br/>Een vertegenwoordiger van de IT-ondersteunings organisatie die in het perspectief van de Help Desk invoer kan bieden over de ondersteuning van deze wijziging. | R/I |
| Naam en e-mail adres voor pilot gebruikers | **RBAC-rollen gebruikers**<br/>De groep gebruikers waarvoor privileged Identity Management is geïmplementeerd. Ze moeten weten hoe ze hun rollen kunnen activeren zodra Privileged Identity Management is geïmplementeerd. | I |

### <a name="enable-privileged-identity-management"></a>Privileged Identity Management inschakelen

Als onderdeel van het plannings proces moet u eerst toestemming geven voor Privileged Identity Management en deze inschakelen door [gebruik](pim-getting-started.md) te maken van het privileged Identity Management-artikel. Als u Privileged Identity Management inschakelt, hebt u toegang tot bepaalde functies die specifiek zijn ontworpen om u te helpen bij de implementatie.

Als uw doel is om Privileged Identity Management voor Azure-resources te implementeren, moet u onze [Azure-resources ontdekken om te beheren in privileged Identity Management](pim-resource-roles-discover-resources.md) artikel. Alleen eigen aren van abonnementen en beheer groepen kunnen deze resources op Privileged Identity Management ontdekken en onboarden. Nadat de service onboarding is uitgevoerd, is de PIM-functionaliteit beschikbaar voor eigen aars op alle niveaus, waaronder beheer groep, abonnement, resource groep en resource. Als u een globale beheerder bent die probeert Privileged Identity Management te implementeren voor uw Azure-resources, kunt u toegang tot het [beheer van alle Azure-abonnementen verhogen](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) om uzelf toegang te geven tot alle Azure-resources in de Directory voor detectie. Er wordt echter wel geadviseerd om goed keuring te krijgen van elk van uw abonnements eigenaren voordat u hun resources beheert met Privileged Identity Management.

### <a name="enforce-principle-of-least-privilege"></a>Principe van minimale bevoegdheid afdwingen

Het is belang rijk om ervoor te zorgen dat u het principe van minimale bevoegdheden in uw organisatie hebt afgedwongen voor zowel uw Azure AD-als uw Azure-resource rollen.

#### <a name="azure-ad-roles"></a>Azure AD-rollen

Voor Azure AD-rollen is het gebruikelijk voor organisaties om de rol van globale beheerder toe te wijzen aan een groot aantal beheerders als de meeste beheerders slechts één of twee specifieke beheerders rollen nodig hebben. Ook de toewijzingen van geprivilegieerde rollen blijven onbeheerd.

> [!NOTE]
> Veelvoorkomende Valk uilen in Role delegering:
>
> - De beheerder die verantwoordelijk is voor Exchange, krijgt de rol globale beheerder, zelfs als ze alleen de rol Exchange-beheerder nodig hebben om hun dag-naar-dag-taak uit te voeren.
> - De rol van de globale beheerder wordt toegewezen aan een Office-beheerder omdat de beheerder zowel beveiligings-als share point-beheerders rollen nodig heeft en het is gemakkelijker om één rol te delegeren.
> - De beheerder heeft een rol van beveiligings beheerder toegewezen om een taak lang geleden uit te voeren, maar is nooit verwijderd.

Volg deze stappen om het principe van minimale bevoegdheden voor uw Azure AD-rollen af te dwingen.

1. Inzicht in de granulatie van de rollen door de [beschik bare Azure AD-beheerders rollen](../users-groups-roles/directory-assign-admin-roles.md#available-roles)te lezen en begrijpen. U en uw team moeten ook verwijzen naar [beheerders rollen per identiteits taak in azure AD](../users-groups-roles/roles-delegate-by-task.md), waarin de minst privileged Role voor specifieke taken wordt uitgelegd.

1. Lijst met geprivilegieerde rollen in uw organisatie. U kunt de [wizard privileged Identity Management](pim-security-wizard.md#run-the-wizard) gebruiken om naar een pagina te gaan, zoals in het volgende voor blad.

    ![Deel venster met geprivilegieerde rollen detecteren met wie privileged roles](./media/pim-deployment-plan/discover-privileged-roles-users.png)

1. Voor alle globale beheerders in uw organisatie, Lees waarom ze de rol nodig hebben. Als de taak van de persoon kan worden uitgevoerd door een of meer gedetailleerde beheerders rollen, moet u op basis van het lezen van de vorige documentatie de taken van de rol globale beheerder verwijderen en de toewijzingen dienovereenkomstig in Azure Active Directory maken (als referentie: micro soft heeft momenteel alleen ongeveer 10 beheerders met de rol van globale beheerder. Meer informatie over de [manier waarop micro soft gebruikmaakt van privileged Identity Management](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)).

1. Voor alle andere Azure AD-rollen bekijkt u de lijst met toewijzingen, identificeert u beheerders die de rol niet meer nodig hebben en verwijdert u deze uit hun toewijzingen.

Als u de laatste twee stappen wilt automatiseren, kunt u toegangs beoordelingen gebruiken in Privileged Identity Management. Volg de stappen in [een toegangs beoordeling starten voor Azure AD-rollen in privileged Identity Management](pim-how-to-start-security-review.md), kunt u een toegangs beoordeling instellen voor elke Azure AD-rol die een of meer leden heeft.

![Een toegangs beoordelings venster maken voor Azure AD-rollen](./media/pim-deployment-plan/create-access-review.png)

U moet de revisoren instellen op **leden (zelf)**. Hiermee wordt een e-mail verzonden naar alle leden van de rol om hen te vragen om te bevestigen of ze toegang nodig hebben. U moet ook een **reden voor goed keuring vereisen** inschakelen in de geavanceerde instellingen, zodat gebruikers kunnen aangeven waarom ze de rol nodig hebben. Op basis van deze informatie kunt u gebruikers verwijderen uit overbodige rollen en meer gedetailleerde beheerders rollen delegeren in het geval van globale Administrators.

Toegangs beoordelingen zijn gebaseerd op e-mail berichten om personen te melden hun toegang tot de rollen te controleren. Als u privileged accounts hebt waaraan geen e-mail berichten zijn gekoppeld, vult u het tweede veld voor e-mail op deze accounts in. Zie [proxyAddresses-kenmerk in azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad)voor meer informatie.

#### <a name="azure-resource-roles"></a>Azure-resourcerollen

Voor Azure-abonnementen en-resources kunt u een soortgelijk toegangs beoordelings proces instellen om de rollen in elk abonnement of elke resource te controleren. Het doel van dit proces is het minimaliseren van de toewijzingen voor eigenaar en gebruikers toegang die zijn gekoppeld aan elk abonnement of resource en voor het verwijderen van overbodige toewijzingen. Organisaties delegeren echter vaak dergelijke taken aan de eigenaar van elk abonnement of resource, omdat ze een beter inzicht hebben in de specifieke rollen (met name aangepaste rollen).

Als u een IT-beheerder bent die de rol van globale beheerder probeert te implementeren Privileged Identity Management voor Azure-resources in uw organisatie, kunt u toegang tot het [beheer van alle Azure-abonnementen verhogen](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) om toegang te krijgen tot elk abonnement. U kunt vervolgens elke eigenaar van het abonnement zoeken en ermee werken om overbodige toewijzingen te verwijderen en de roltoewijzing van de eigenaar te minimaliseren.

Gebruikers met de rol eigenaar voor een Azure-abonnement kunnen ook [toegangs Beoordelingen voor Azure-resources](pim-resource-roles-start-access-review.md) gebruiken om onnodige roltoewijzingen te controleren en te verwijderen, vergelijkbaar met het proces dat eerder voor Azure AD-rollen is beschreven.

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>Bepalen welke roltoewijzingen moeten worden beveiligd door Privileged Identity Management

Na het opschonen van geprivilegieerde roltoewijzingen in uw organisatie, moet u beslissen welke rollen u met Privileged Identity Management wilt beveiligen.

Als een rol wordt beveiligd door Privileged Identity Management, moeten in aanmerking komende gebruikers die hieraan zijn toegewezen, de bevoegdheden verhogen die worden verleend door de rol. Het verhogings proces kan ook het verkrijgen van goed keuring, het uitvoeren van multi-factor Authentication en/of een reden geven waarom ze worden geactiveerd. Privileged Identity Management kunt ook verhogingen volgen via meldingen en de controle gebeurtenis logboeken van Privileged Identity Management en Azure AD.

U kunt kiezen welke rollen moeten worden beveiligd met Privileged Identity Management lastig zijn en voor elke organisatie verschillend zijn. Deze sectie bevat onze best practice-advies voor Azure AD-en Azure-resource rollen.

#### <a name="azure-ad-roles"></a>Azure AD-rollen

Het is belang rijk om de beveiliging van Azure AD-rollen met het grootste aantal machtigingen te bepalen. Op basis van gebruiks patronen voor alle Privileged Identity Management klanten, zijn de belangrijkste 10 Azure AD-rollen die worden beheerd door Privileged Identity Management:

1. Globale beheerder
1. Beveiligingsbeheerder
1. Gebruikers beheerder
1. Exchange-beheerder
1. SharePoint-beheerder
1. Intune-beheerder
1. Beveiligingslezer
1. Servicebeheerder
1. Factureringsbeheerder
1. Skype voor Bedrijven-beheerder

> [!TIP]
> : heavy_check_mark: **micro soft raadt** u aan om al uw globale beheerders en beveiligings beheerders te beheren door gebruik te maken van privileged Identity Management als een eerste stap, aangezien ze het meest schadelijk kunnen zijn voor het geval dat er is aangetast.

Het is belang rijk om te bepalen welke gegevens en machtigingen het meest gevoelig zijn voor uw organisatie. Een voor beeld hiervan is het mogelijk dat sommige organisaties hun Power BI beheerdersrol of hun rol team beheerder met Privileged Identity Management willen beveiligen, omdat ze de mogelijkheid hebben om toegang te krijgen tot gegevens en/of kern werk stromen te wijzigen.

Als er rollen zijn waaraan gast gebruikers zijn toegewezen, zijn ze met name kwetsbaar voor aanvallen.

> [!TIP]
> : heavy_check_mark: **micro soft raadt** u aan om alle rollen met gast gebruikers te beheren met privileged Identity Management om het risico te verminderen dat is gekoppeld aan gast gebruikers accounts.

Lees functies zoals de Directory Reader, Message Center Reader en Security Reader worden soms minder belang rijk vergeleken met andere rollen, aangezien ze geen schrijf machtiging hebben. Er zijn echter ook enkele klanten die deze rollen beschermen, omdat aanvallers die toegang tot deze accounts hebben verkregen, mogelijk gevoelige gegevens kunnen lezen, zoals persoons gegevens. Houd rekening met het volgende wanneer u wilt bepalen of lezer-rollen in uw organisatie moeten worden beheerd met Privileged Identity Management.

#### <a name="azure-resource-roles"></a>Azure-resourcerollen

Wanneer u besluit welke roltoewijzingen moeten worden beheerd met Privileged Identity Management voor Azure-resource, moet u eerst de abonnementen/resources identificeren die het belangrijkst zijn voor uw organisatie. Voor beelden van dergelijke abonnementen/resources zijn:

- Resources die de meest gevoelige gegevens hosten
- Bronnen waarvan de kern, klant gerichte toepassingen afhankelijk zijn

Als u een globale beheerder bent die problemen ondervindt bij het bepalen welke abonnementen/resources het belangrijkst zijn, moet u contact opnemen met de eigen aren van het abonnement in uw organisatie voor het verzamelen van een lijst met resources die worden beheerd door elk abonnement. Vervolgens moet u samen met de eigen aars van het abonnement de resources groeperen op basis van het Ernst niveau, in het geval dat ze zijn aangetast (laag, gemiddeld, hoog). U moet de prioriteit van resources beheren met Privileged Identity Management op basis van dit niveau.

> [!TIP]
> : heavy_check_mark: **micro soft raadt** u aan om te werken met abonnements-en resource-eigen aren van essentiële services om privileged Identity Management werk stroom in te stellen voor alle rollen binnen gevoelige abonnementen/resources.

Privileged Identity Management voor Azure-resources ondersteunt tijdgebonden service accounts. U dient service accounts precies hetzelfde te behandelen als hoe u een normaal gebruikers account zou behandelen.

Voor abonnementen/resources die niet zo belang rijk zijn, hoeft u Privileged Identity Management voor alle rollen niet in te stellen. U moet echter wel de beheerders rollen eigenaar en gebruikers toegang beveiligen met Privileged Identity Management.

> [!TIP]
> : heavy_check_mark: **micro soft raadt** u aan om eigenaar-rollen en beheerders rollen van alle abonnementen/resources te beheren met behulp van privileged Identity Management.

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Beslissen welke roltoewijzingen permanent of in aanmerking komen

Wanneer u de lijst met rollen die door Privileged Identity Management moeten worden beheerd, hebt vastgesteld, moet u bepalen welke gebruikers de in aanmerking komende rol en de permanent actieve rol moeten krijgen. Permanente actieve rollen zijn de normale rollen die worden toegewezen via Azure Active Directory en Azure-resources, terwijl in aanmerking komende rollen alleen kunnen worden toegewezen in Privileged Identity Management.

> [!TIP]
> : heavy_check_mark: **micro soft raadt** u aan om permanent actieve toewijzingen te hebben voor zowel Azure AD-rollen als Azure-resource rollen, met uitzonde ring van de aanbevolen twee verhouds [toegangs accounts](../users-groups-roles/directory-emergency-access.md), die de permanente globale beheerdersrol moeten hebben.

Hoewel we de permanente beheerder aanraden, is het soms moeilijk voor organisaties om dit meteen te bereiken. Hier volgen enkele dingen die u moet overwegen bij het nemen van deze beslissing:

- De frequentie van uitbrei ding van bevoegdheden: als de gebruiker de privileged slechts eenmaal nodig heeft, hoeft deze niet permanent te worden toegewezen. Aan de andere kant, als de gebruiker de rol voor de dagelijkse taak nodig heeft en Privileged Identity Management de productiviteit aanzienlijk zou verlagen, kunnen ze worden overwogen voor de permanente rol.
- Specifieke aanvragen voor uw organisatie: als de persoon die de in aanmerking komende rol heeft gekregen van een zeer onenig team of een leidinggevende Executive naar het punt dat het verhogings proces communiceert en afdwingt lastig is, kunnen ze worden overwogen voor de permanente rol.

> [!TIP]
> : heavy_check_mark: **micro soft raadt** u aan om terugkerende toegangs beoordelingen in te stellen voor gebruikers met permanente roltoewijzingen (indien van toepassing). Meer informatie over terugkerende toegangs beoordeling vindt u in de laatste sectie van dit implementatie plan

### <a name="draft-your-privileged-identity-management-settings"></a>Uw Privileged Identity Management-instellingen ontwerpen

Voordat u uw Privileged Identity Management-oplossing implementeert, is het verstandig om uw Privileged Identity Management-instellingen te ontwerpen voor elke geprivilegieerde rol die uw organisatie gebruikt. In deze sectie vindt u enkele voor beelden van Privileged Identity Management instellingen voor bepaalde rollen (ze zijn alleen ter informatie en zijn mogelijk anders voor uw organisatie). Elk van deze instellingen wordt gedetailleerd uitgelegd met de aanbevelingen van micro soft na de tabellen.

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Privileged Identity Management instellingen voor Azure AD-rollen

| Rol | MFA vereisen | Melding | Incident ticket | Goed keuring vereisen | Fiatteur | Activerings duur | Permanente beheerder |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Globale beheerder | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Andere globale beheerders | 1 uur | Accounts voor toegang in nood gevallen |
| Exchange-beheerder | :heavy_check_mark: | :heavy_check_mark: | BxDxH | BxDxH | Geen | 2 uur | Geen |
| Helpdesk beheerder | BxDxH | BxDxH | :heavy_check_mark: | BxDxH | Geen | 8 uur | Geen |

#### <a name="privileged-identity-management-settings-for-azure-resource-roles"></a>Privileged Identity Management instellingen voor Azure-resource rollen

| Rol | MFA vereisen | Melding | Goed keuring vereisen | Fiatteur | Activerings duur | Actieve beheerder | Actieve verval datum | Verval datum in aanmerking komend |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Eigenaar van kritieke abonnementen | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Andere eigen aars van het abonnement | 1 uur | Geen | N.v.t. | 3 maand |
| Gebruikers toegangs beheerder van minder kritieke abonnementen | :heavy_check_mark: | :heavy_check_mark: | BxDxH | Geen | 1 uur | Geen | N.v.t. | 3 maand |
| Inzender voor virtuele machines | BxDxH | :heavy_check_mark: | BxDxH | Geen | 3 uur | Geen | N.v.t. | 6 maanden |

In de volgende tabel worden de instellingen beschreven.

| Instelling | Beschrijving |
| --- | --- |
| Rol | De naam van de rol waarvoor u de instellingen wilt definiëren. |
| MFA vereisen | Of de in aanmerking komende gebruiker MFA moet uitvoeren voordat de rol wordt geactiveerd.<br/><br/> : heavy_check_mark: **micro soft raadt** u aan MFA af te dwingen voor alle beheerders rollen, met name als de rollen gast gebruikers hebben. |
| Melding | Als deze eigenschap is ingesteld op True, wordt een e-mail melding door de beheerder van de bevoegde rol en de beveiligings beheerder in de organisatie ontvangen wanneer een in aanmerking komende gebruiker de rol activeert.<br/><br/>**Opmerking:** Sommige organisaties hebben geen e-mail adres dat is gekoppeld aan hun beheerders accounts. Als u deze e-mail meldingen wilt ontvangen, moet u een alternatief e-mail adres instellen, zodat beheerders deze e-mail berichten ontvangen. |
| Incident ticket | Hiermee wordt aangegeven of de in aanmerking komende gebruiker een incident ticket nummer moet vastleggen wanneer hun rol wordt geactiveerd. Deze instelling helpt een organisatie bij het identificeren van elke activering met een intern incident nummer om ongewenste activeringen te beperken.<br/><br/> : heavy_check_mark: **micro soft raadt** u aan om te profiteren van de ticket nummers van een incident om privileged Identity Management te koppelen aan uw interne systeem. Dit is met name handig voor goed keurders die context nodig hebben voor de activering. |
| Goed keuring vereisen | Hiermee wordt aangegeven of de in aanmerking komende gebruiker goed keuring moet krijgen om de rol te activeren.<br/><br/> : heavy_check_mark: **micro soft raadt** u aan om goed keuring in te stellen voor rollen met de meeste machtigingen. Op basis van gebruiks patronen van alle Privileged Identity Management klanten, globale beheerder, gebruikers beheerder, Exchange-beheerder, beveiligings beheerder en wachtwoord beheerder zijn de meest voorkomende rollen met goedkeurings instellingen. |
| Fiatteur | Als goed keuring is vereist voor het activeren van de in aanmerking komende rol, vermeldt u de personen die de aanvraag moeten goed keuren. Privileged Identity Management stelt de fiatteur standaard in op alle gebruikers die een bevoegde rol beheerder zijn, ongeacht of ze permanent of in aanmerking komen.<br/><br/>**Opmerking:** Als een gebruiker in aanmerking komt voor een Azure AD-rol en een goed keurder van de rol, kunnen ze zichzelf niet goed keuren.<br/><br/> : heavy_check_mark: **micro soft adviseert** dat u goed keurders kiest voor degenen die het belangrijkst zijn voor de specifieke rol en de frequente gebruikers in plaats van een globale beheerder. |
| Activerings duur | De tijds duur dat een gebruiker wordt geactiveerd in de rol voordat deze verloopt. |
| Permanente beheerder | Lijst met gebruikers die een permanente beheerder voor de rol worden (nooit hoeven te activeren).<br/><br/> : heavy_check_mark: **micro soft raadt** aan dat u geen permanente beheerder hebt voor alle rollen, met uitzonde ring van globale beheerders. Meer informatie hierover vindt u in de sectie wie moet worden gemaakt en die permanent actief moeten zijn in dit plan. |
| Actieve beheerder | Voor Azure-resources is de actieve beheerder de lijst met gebruikers die nooit moeten worden geactiveerd om de rol te gebruiken. Dit wordt niet aangeduid als een permanente beheerder zoals in azure AD-rollen omdat u een verloop tijd kunt instellen voor wanneer de gebruiker deze rol gaat verliezen. |
| Actieve verval datum | Een actieve roltoewijzing voor Azure-resource rollen verlopen na deze geconfigureerde tijds periode. U kunt kiezen uit 15 dagen, 1 maand, 3 maanden, 6 maanden, 1 jaar of permanent actief. |
| Verval datum in aanmerking komend | Een in aanmerking komende roltoewijzing voor Azure-resource rollen verlopen na deze geconfigureerde tijds periode. U kunt kiezen uit 15 dagen, 1 maand, 3 maanden, 6 maanden, 1 jaar of permanent in aanmerking komen. |

## <a name="implement-your-solution"></a>Uw oplossing implementeren

De kern van de juiste planning is de basis waarop u een toepassing kunt implementeren met Azure Active Directory.  Het biedt intelligente beveiliging en integratie die het onboarding vereenvoudigt tijdens het verkorten van de tijd voor geslaagde implementaties.  Met deze combi natie zorgt u ervoor dat uw toepassing is geïntegreerd met gemak en de tijd voor de eind gebruikers verkleint.

### <a name="identify-test-users"></a>Test gebruikers identificeren

Gebruik deze sectie om een set gebruikers en of groepen gebruikers te identificeren om de implementatie te valideren. Op basis van de instellingen die u hebt geselecteerd in het gedeelte planning, identificeert u de gebruikers die u wilt testen voor elke rol.

> [!TIP]
> : heavy_check_mark: **micro soft raadt** u aan om service-eigen aren van elke Azure AD-rol de test gebruikers te maken, zodat ze vertrouwd kunnen raken met het proces en een interne advocator worden voor de implementatie.

In deze tabel identificeert u de test gebruikers waarmee wordt gecontroleerd of de instellingen voor elke rol werken.

| Rolnaam | Gebruikers testen |
| --- | --- |
| &lt;Rolnaam&gt; | &lt;Gebruikers om de rol te testen&gt; |
| &lt;Rolnaam&gt; | &lt;Gebruikers om de rol te testen&gt; |

### <a name="test-implementation"></a>Implementatie testen

Nu u de test gebruikers hebt geïdentificeerd, gebruikt u deze stap om Privileged Identity Management te configureren voor uw test gebruikers. Als uw organisatie Privileged Identity Management werk stroom wil opnemen in uw eigen interne toepassing in plaats van Privileged Identity Management in de Azure Portal te gebruiken, worden alle bewerkingen in Privileged Identity Management ook ondersteund via onze [Graph API](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root).

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>Privileged Identity Management configureren voor Azure AD-rollen

1. [Configureer de functie-instellingen van Azure AD](pim-how-to-change-default-settings.md) op basis van wat u hebt gepland.

1. Navigeer naar **Azure AD-rollen**, klik op **rollen**en selecteer vervolgens de functie die u zojuist hebt geconfigureerd.

1. Als ze voor de groep test gebruikers al een permanente beheerder zijn, kunt u ze in aanmerking komen door ernaar te zoeken en ze te converteren van permanent naar in aanmerking komend door te klikken op de drie puntjes in hun rij. Als ze nog geen roltoewijzingen hebben, kunt u [een nieuwe in aanmerking komende toewijzing maken](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role).

1. Herhaal stap 1-3 voor alle rollen die u wilt testen.

1. Zodra u de test gebruikers hebt ingesteld, moet u ze de koppeling sturen voor het activeren van [hun Azure AD-rol](pim-how-to-activate-role.md).

#### <a name="configure-privileged-identity-management-for-azure-resource-roles"></a>Privileged Identity Management voor Azure-resource rollen configureren

1. [Configureer de instellingen van de Azure-resource functie](pim-resource-roles-configure-role-settings.md) voor een rol binnen een abonnement of resource die u wilt testen.

1. Navigeer naar **Azure-resources** voor dat abonnement en klik op **rollen**, selecteer de rol die u zojuist hebt geconfigureerd.

1. Als de groep test gebruikers al een actieve beheerder is, kunt u deze in aanmerking komen door ernaar te zoeken en [hun roltoewijzing](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment)bij te werken. Als ze nog geen rol hebben, kunt u [een nieuwe rol toewijzen](pim-resource-roles-assign-roles.md#assign-a-role).

1. Herhaal stap 1-3 voor alle rollen die u wilt testen.

1. Zodra u de test gebruikers hebt ingesteld, moet u ze de koppeling sturen om [hun Azure-resource functie te activeren](pim-resource-roles-activate-your-roles.md).

Gebruik deze fase om te controleren of alle configuratie die u voor de rollen hebt ingesteld, correct werkt. Gebruik de volgende tabel om uw tests te documenteren. U moet deze fase ook gebruiken om de communicatie met de betrokken gebruikers te optimaliseren.

| Rol | Verwacht gedrag tijdens de activering | Werkelijke resultaten |
| --- | --- | --- |
| Globale beheerder | (1) MFA vereisen<br/>(2) goed keuring vereisen<br/>(3) fiatteur ontvangt een melding en kan goed keuren<br/>(4) rol verloopt na vooraf ingestelde tijd |  |
| Eigenaar van abonnement *X* | (1) MFA vereisen<br/>(2) de in aanmerking komende toewijzing verloopt na de geconfigureerde tijds periode |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>Privileged Identity Management met betrokken belanghebbenden communiceren

Wanneer u Privileged Identity Management implementeert, worden er extra stappen geïntroduceerd voor gebruikers van geprivilegieerde rollen. Hoewel Privileged Identity Management beveiligings problemen die zijn gekoppeld aan bevoorrechte identiteiten aanzienlijk reduceert, moet de wijziging effectief worden gecommuniceerd vóór de implementatie van de hele organisatie. Afhankelijk van het aantal betrokken beheerders, selecteren organisaties vaak een intern document, een video of een e-mail bericht over de wijziging. Deze communicatie bevat vaak de volgende informatie:

- Wat is PIM?
- Wat is het voor deel voor de organisatie?
- Die worden beïnvloed
- Wanneer moet PIM worden getotaliseerd
- Welke extra stappen zijn vereist voor gebruikers om hun rol te activeren
    - U moet koppelingen naar onze documentatie verzenden:
    - [Azure AD-rollen activeren](pim-how-to-activate-role.md)
    - [Azure-resource rollen activeren](pim-resource-roles-activate-your-roles.md)
- Contact gegevens of koppeling naar Help Desk voor problemen met PIM

> [!TIP]
> : heavy_check_mark: **micro soft raadt** u aan om de tijd in te stellen met uw Help Desk/ondersteunings team om deze via de privileged Identity Management werk stroom te door lopen (als uw organisatie een intern it-ondersteunings team heeft). Geef ze de juiste documenten en uw contact gegevens.

### <a name="move-to-production"></a>Verplaatsen naar productie

Wanneer het testen is voltooid en geslaagd, gaat u Privileged Identity Management naar productie door alle stappen in de test fasen te herhalen voor alle gebruikers van elke functie die u in uw Privileged Identity Management configuratie hebt gedefinieerd. Voor Privileged Identity Management voor Azure AD-rollen, testen en implementeren organisaties vaak Privileged Identity Management voor globale beheerders voordat ze Privileged Identity Management testen en implementeren voor andere rollen. In de tussen tijd voor Azure resource testen en implementeren organisaties doorgaans Privileged Identity Management één Azure-abonnement tegelijk.

### <a name="in-the-case-a-rollback-is-needed"></a>Als er een terugdraai actie nodig is

Als Privileged Identity Management niet naar wens werkt in de productie omgeving, kunnen de volgende terugdraai stappen u helpen om terug te keren naar een bekende goede staat voordat u Privileged Identity Management instelt:

#### <a name="azure-ad-roles"></a>Azure AD-rollen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Open **Azure AD privileged Identity Management**.
1. Klik op **Azure AD-rollen** en klik vervolgens op **rollen**.
1. Voor elke rol die u hebt geconfigureerd, klikt u op het weglatings teken (**...**) voor alle gebruikers met een in aanmerking komende toewijzing.
1. Klik op de optie **permanent maken** om de roltoewijzing permanent te maken.

#### <a name="azure-resource-roles"></a>Azure-resourcerollen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Open **Azure AD privileged Identity Management**.
1. Klik op **Azure-resources** en klik vervolgens op een abonnement of resource die u wilt terugzetten.
1. Klik op **rollen**.
1. Voor elke rol die u hebt geconfigureerd, klikt u op het weglatings teken (**...**) voor alle gebruikers met een in aanmerking komende toewijzing.
1. Klik op de optie **permanent maken** om de roltoewijzing permanent te maken.

## <a name="next-steps-after-deploying"></a>Volgende stappen na de implementatie

Het implementeren van Privileged Identity Management in productie is een belang rijke stap voor het beveiligen van de bevoegde identiteiten van uw organisatie. Met de implementatie van Privileged Identity Management worden extra Privileged Identity Management functies geleverd die u moet gebruiken voor beveiliging en naleving.

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>Privileged Identity Management waarschuwingen gebruiken om uw bevoegde toegang te beveiligen

Gebruik de ingebouwde waarschuwings functionaliteit van Privileged Identity Management om uw organisatie beter te beveiligen. Zie [Security Alerts](pim-how-to-configure-security-alerts.md#security-alerts)(Engelstalig) voor meer informatie. Deze waarschuwingen omvatten: beheerders die geen geprivilegieerde rollen gebruiken, de rollen worden toegewezen buiten Privileged Identity Management, worden de rollen te vaak geactiveerd. Als u uw organisatie volledig wilt beveiligen, moet u de lijst met waarschuwingen regel matig door lopen en de problemen oplossen. U kunt uw waarschuwingen op de volgende manier weer geven en herstellen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Open **Azure AD privileged Identity Management**.
1. Klik op **Azure AD-rollen** en klik vervolgens op **waarschuwingen**.

> [!TIP]
> : heavy_check_mark: **micro soft raadt** aan dat u alle waarschuwingen die zijn gemarkeerd met hoge urgentie onmiddellijk wilt afhandelen. Voor gemiddeld en lage Ernst waarschuwingen moet u op de hoogte blijven en wijzigingen aanbrengen als u denkt dat er sprake is van een beveiligings risico.

Als een van de specifieke waarschuwingen niet nuttig is of niet van toepassing is op uw organisatie, kunt u de waarschuwing altijd negeren op de pagina waarschuwingen. U kunt deze instelling altijd later op de pagina Azure AD-instellingen herstellen.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Terugkerende toegangs beoordelingen instellen om de bevoegde identiteiten van uw organisatie regel matig te controleren

Toegangs beoordelingen zijn de beste manier om gebruikers te vragen die zijn toegewezen aan geprivilegieerde rollen of specifieke revisoren, ongeacht of elke gebruiker de bevoorrechte identiteit nodig heeft. Toegangs beoordelingen zijn handig als u de kwets baarheid wilt beperken en wilt blijven voldoen aan het beleid. Voor meer informatie over het starten van een toegangs beoordeling raadpleegt u [Azure AD-rollen toegangs beoordelingen](pim-how-to-start-security-review.md) en [toegangs Beoordelingen voor Azure-resource rollen](pim-resource-roles-start-access-review.md). Voor sommige organisaties is het uitvoeren van periodieke toegangs beoordeling vereist om aan de wetten en voor Schriften te blijven voldoen en voor anderen, is toegangs beoordeling de beste manier om de principal van minimale bevoegdheden in uw organisatie af te dwingen.

> [!TIP]
> : heavy_check_mark: **micro soft raadt** u aan om per kwar taal toegangs beoordelingen in te stellen voor al uw Azure AD-en Azure-resource rollen.

In de meeste gevallen is de revisor voor Azure AD-rollen de gebruikers zelf, terwijl de revisor voor Azure-resource rollen de eigenaar is van het abonnement waarin de rol zich bevindt. Het is echter vaak het geval wanneer bedrijven geprivilegieerde accounts hebben die niet zijn gekoppeld aan het e-mail adres van een bepaalde persoon. In deze gevallen wordt er geen lees-en-beoordeling van de toegang gegeven.

> [!TIP]
> : heavy_check_mark: **micro soft raadt** u aan om een secundair e-mail adres toe te voegen voor alle accounts met geprivilegieerde roltoewijzingen die niet zijn gekoppeld aan een regel matig gecontroleerd e-mail adres

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Het maximale uit uw audit logboek halen om de beveiliging en naleving te verbeteren

Het audit logboek is de plek waar u up-to-date kunt blijven en voldoen aan de voor Schriften. Privileged Identity Management wordt momenteel een geschiedenis van 30 dagen opgeslagen in het controle logboek van uw organisatie, met inbegrip van:

- Activering/deactivering van in aanmerking komende rollen
- Roltoewijzings activiteiten binnen en buiten Privileged Identity Management
- Wijzigingen in rolinstellingen
- Activiteiten voor activering van rollen met goedkeurings instellingen aanvragen/goed keuren/weigeren
- Bijwerken naar waarschuwingen

U kunt deze controle Logboeken openen als u een globale beheerder of een beheerder van een bevoegde rol bent. Zie de [controle geschiedenis voor Azure AD-rollen](pim-how-to-use-audit-log.md) en de [controle geschiedenis voor Azure-resource rollen](azure-pim-resource-rbac.md)voor meer informatie.

> [!TIP]
> : heavy_check_mark: **micro soft raadt** u aan ten minste één beheerder te laten door alle controle gebeurtenissen wekelijks te lezen en uw controle gebeurtenissen maandelijks te exporteren.

Als u uw audit gebeurtenissen automatisch wilt opslaan gedurende een langere periode, wordt het controle logboek van Privileged Identity Management automatisch gesynchroniseerd naar de [audit logboeken van Azure AD](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> : heavy_check_mark: **micro soft raadt** u aan om [Azure-logboek bewaking](../reports-monitoring/concept-activity-logs-azure-monitor.md) in te stellen voor het archiveren van controle gebeurtenissen in een Azure-opslag account voor de nood zaak van beveiliging en naleving.
