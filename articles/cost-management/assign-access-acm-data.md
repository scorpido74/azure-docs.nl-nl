---
title: Toegang tot Azure Cost Management gegevens toewijzen
description: In dit artikel vindt u informatie over het toewijzen van machtigingen aan Azure Cost Management gegevens voor verschillende toegangs scopes.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: secdec18
ms.openlocfilehash: ebc56d27b7adc8f1fea9eafabe1b211f3f0ad560
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375131"
---
# <a name="assign-access-to-cost-management-data"></a>Toegang tot Cost Management gegevens toewijzen

Voor gebruikers met Azure Enter prise Agreements, een combi natie van machtigingen die in de Azure Portal en de Enter prise-Portal worden verleend, wordt het toegangs niveau van een gebruiker voor Azure Cost Management gegevens gedefinieerd. Voor gebruikers met andere typen Azure-accounts kunt u het Cost Management toegangs niveau van een gebruiker met behulp van Azure op rollen gebaseerd toegangs beheer eenvoudiger definiëren. In dit artikel wordt uitgelegd hoe u toegang tot Cost Management gegevens toewijst. Nadat de combi natie van machtigingen is toegewezen, worden de gegevens in Cost Management weer gegeven op basis van het bereik waartoe ze toegang hebben en over het bereik dat ze in de Azure Portal selecteren.

Het bereik dat een gebruiker selecteert, wordt overal in Cost Management gebruikt om gegevens consolidatie te bieden en de toegang tot kosten gegevens te beheren. Wanneer u scopes gebruikt, kunnen gebruikers deze niet meerdere selecteren. In plaats daarvan selecteren ze een groter bereik waarop onderliggende bereiken worden getotaliseerd en vervolgens wordt gefilterd op wat ze willen weer geven. Gegevens consolidatie is belang rijk om te begrijpen omdat sommige mensen geen toegang mogen hebben tot een bovenliggende scope waarvan de onderliggende bereiken worden getotaliseerd.

Bekijk [hoe u toegang kunt toewijzen met Azure Cost Management](https://www.youtube.com/watch?v=J997ckmwTa8) video voor meer informatie over het toewijzen van toegang voor het weer geven van kosten en kosten met op rollen gebaseerd toegangs beheer van Azure.

>[!VIDEO https://www.youtube.com/embed/J997ckmwTa8]

## <a name="cost-management-scopes"></a>Cost Management bereiken

Cost Management ondersteunt diverse typen Azure-accounts. Zie [Gegevens van kostenbeheer begrijpen](understand-cost-mgt-data.md) voor de volledige lijst met ondersteunde accounttypen. Het type account bepaalt de beschik bare bereiken.

### <a name="azure-ea-subscription-scopes"></a>Azure EA-abonnements bereiken

Voor het weer geven van kosten gegevens voor Azure EA-abonnementen moet een gebruiker ten minste lees toegang hebben tot een of meer van de volgende bereiken.

| **Bereik** | **Gedefinieerd op** | **Vereiste toegang om gegevens weer te geven** | **Vereiste EA-instelling** | **Consolideert gegevens tot** |
| --- | --- | --- | --- | --- |
| Factureringsaccount<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | Enterprise-beheerder | Geen | Alle abonnementen uit de Enterprise Overeenkomst |
| Afdeling | [https://ea.azure.com](https://ea.azure.com/) | Afdelingsbeheerder | **Da-weer gave-kosten** ingeschakeld | Alle abonnementen die horen bij een inschrijvingsaccount dat is gekoppeld aan de afdeling |
| Inschrijvingsaccount<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | Accounteigenaar | **Ao-weer gave-kosten** ingeschakeld | Alle abonnementen van het inschrijvingsaccount |
| Beheergroep | [https://portal.azure.com](https://portal.azure.com/) | Cost Management Reader (of Reader) | **Ao-weer gave-kosten** ingeschakeld | Alle abonnementen onder de beheergroep |
| Abonnement | [https://portal.azure.com](https://portal.azure.com/) | Cost Management Reader (of Reader) | **Ao-weer gave-kosten** ingeschakeld | Alle resources/resourcegroepen in het abonnement |
| Resourcegroep | [https://portal.azure.com](https://portal.azure.com/) | Cost Management Reader (of Reader) | **Ao-weer gave-kosten** ingeschakeld | Alle resources in de resourcegroep |

<sup>1</sup> het facturerings account wordt ook wel de Enterprise Agreement of inschrijving genoemd.

<sup>2</sup> het inschrijvings account wordt ook de eigenaar van het account genoemd.

In het volgende diagram ziet u de relatie tussen Cost Management bereiken met rollen en EA-Portal instellingen.

![Diagram van de relatie tussen Cost Management bereiken met rollen en EA-Portal instellingen](./media/assign-access-acm-data/scope-access-relationship-diagram.png)

Wanneer de **da-weergave kosten** zijn uitgeschakeld in de EA-Portal, ziet u een bericht waarin *de kosten voor uw organisatie zijn uitgeschakeld* wanneer u kosten voor afdelingen en accounts probeert weer te geven.

Op dezelfde manier wordt een bericht weer gegeven dat de kosten voor de registratie van *uw organisatie worden uitgeschakeld* wanneer u de kosten voor inschrijvings accounts, beheer groepen, abonnementen en resource groepen probeert weer te geven, wanneer de **ao-weergave** kosten zijn uitgeschakeld in de EA-Portal.

## <a name="other-azure-account-scopes"></a>Andere Azure-account bereiken

Voor het weer geven van kosten gegevens voor andere Azure-abonnementen moet een gebruiker ten minste lees toegang hebben tot een of meer van de volgende bereiken:

- Azure-account
- Beheergroep
- Resourcegroep

Er zijn verschillende bereiken beschikbaar na partners die klanten op de micro soft-klanten overeenkomst hebben opvolgd. CSP-klanten kunnen vervolgens Cost Management-functies gebruiken wanneer ze zijn ingeschakeld door hun CSP-partner. Zie [aan de slag met Azure Cost Management voor partners](get-started-partners.md)voor meer informatie.

## <a name="enable-access-to-costs-in-the-ea-portal"></a>Toegang tot de kosten in de EA-Portal inschakelen

Voor het afdelings bereik moet de optie **voor de da-weer gave-kosten** zijn **ingeschakeld** in de EA-Portal. Voor alle andere bereiken is de optie **voor het weer geven van Ao** in de EA-Portal **ingeschakeld** .

Een optie inschakelen:

1. Meld u aan bij de EA-Portal op [https://ea.azure.com](https://ea.azure.com) met een beheerders account voor ondernemingen.
2. Selecteer **beheren** in het linkerdeel venster.
3. Voor de cost management-bereiken waartoe u toegang wilt verlenen, schakelt u de optie kosten in op **da weer gave van kosten** en/of **ao weer geven**.  
    tabblad ![Enrollment met de opties voor het weer geven van DA-en AO-kosten @ no__t-1

Nadat de opties voor de weergave kosten zijn ingeschakeld, is voor de meeste scopes ook de machtiging op rollen gebaseerd toegangs beheer (RBAC) vereist in de Azure Portal.

## <a name="enterprise-administrator-role"></a>Rol van ondernemings Administrator

Een ondernemings Administrator heeft standaard toegang tot het facturerings account (Enterprise Agreement/inschrijving) en voor alle andere scopes, die een onderliggend bereik zijn. De Enter prise-beheerder wijst toegang toe aan scopes voor andere gebruikers. Als best practice voor bedrijfs continuïteit moet u altijd twee gebruikers met beheerders rechten voor het bedrijf hebben. In de volgende secties worden voor beelden gegeven van de Enter prise-beheerder die de toegang tot de scopes voor andere gebruikers toewijst.

## <a name="assign-billing-account-scope-access"></a>Toegang tot het account bereik toewijzen

Voor toegang tot het bereik van het betaal account is de machtiging ondernemings Administrator vereist in de EA-Portal. De Enter prise-beheerder heeft toegang tot het weer geven van de kosten voor de volledige EA-inschrijving of meerdere inschrijvingen. Er is geen actie vereist in de Azure Portal voor het bereik van het facturerings account.

1. Meld u aan bij de EA-Portal op [https://ea.azure.com](https://ea.azure.com) met een beheerders account voor ondernemingen.
2. Selecteer **beheren** in het linkerdeel venster.
3. Selecteer op het tabblad **inschrijving** de registratie die u wilt beheren.  
    @no__t uw inschrijving in het EA-Portal @ no__t-1 0select
4. Klik op **+ beheerder toevoegen**.
5. Selecteer in het vak beheerder toevoegen het verificatie type en typ het e-mail adres van de gebruiker.
6. Als de gebruiker alleen-lezen toegang moet hebben tot kosten-en gebruiks gegevens, selecteert u **Ja**in het vak **alleen-lezen**.  Anders selecteert u **Nee**.
7. Klik op **toevoegen** om het account te maken.  
    ![example informatie weer gegeven in het vak Administrator toevoegen @ no__t-1

Het kan tot 30 minuten duren voordat de nieuwe gebruiker toegang heeft tot gegevens in Cost Management.

### <a name="assign-department-scope-access"></a>Toegang tot het afdelings bereik toewijzen

Voor toegang tot het afdelings bereik is de afdelings beheerder (DA View-kosten) toegang tot de EA-Portal vereist. De afdelings beheerder heeft toegang om de kosten en gebruiks gegevens weer te geven die zijn gekoppeld aan een afdeling of aan meerdere afdelingen. De gegevens voor de afdeling bevatten alle abonnementen die horen bij een inschrijvings account dat aan de afdeling is gekoppeld. Er is geen actie vereist in de Azure Portal.

1. Meld u aan bij de EA-Portal op [https://ea.azure.com](https://ea.azure.com) met een beheerders account voor ondernemingen.
2. Selecteer **beheren** in het linkerdeel venster.
3. Selecteer op het tabblad **inschrijving** de registratie die u wilt beheren.
4. Klik op het tabblad **afdeling** en klik vervolgens op **beheerder toevoegen**.
5. Selecteer in het vak afdelings beheerder toevoegen het verificatie type en typ vervolgens het e-mail adres van de gebruiker.
6. Als de gebruiker alleen-lezen toegang moet hebben tot kosten-en gebruiks gegevens, selecteert u **Ja**in het vak **alleen-lezen**.  Anders selecteert u **Nee**.
7. Selecteer de afdelingen waaraan u beheer machtigingen voor de afdeling wilt verlenen.
8. Klik op **toevoegen** om het account te maken.  
    ![enter vereiste informatie in het vak Administrator toevoegen @ no__t-1

## <a name="assign-enrollment-account-scope-access"></a>Toegang tot bereik van inschrijvings account toewijzen

Voor toegang tot de scope van het inschrijvings account is toegang tot de account eigenaar (AO View-kosten) in de EA-Portal vereist. De eigenaar van het account kan de kosten en gebruiks gegevens weer geven die zijn gekoppeld aan de abonnementen die zijn gemaakt op basis van dat inschrijvings account. Er is geen actie vereist in de Azure Portal.

1. Meld u aan bij de EA-Portal op [https://ea.azure.com](https://ea.azure.com) met een beheerders account voor ondernemingen.
2. Selecteer **beheren** in het linkerdeel venster.
3. Selecteer op het tabblad **inschrijving** de registratie die u wilt beheren.
4. Klik op het tabblad **account** en klik vervolgens op **account toevoegen**.
5. Selecteer in het vak account toevoegen de **afdeling** waaraan u het account wilt koppelen, of als niet-toegewezen.
6. Selecteer het verificatie type en typ de account naam.
7. Typ het e-mail adres van de gebruiker en typ desgewenst het kosten centrum.
8. Klik op **toevoegen** om het account te maken.  
    ![enter vereiste informatie in het vak account toevoegen voor een inschrijvings account @ no__t-1

Nadat u de bovenstaande stappen hebt voltooid, wordt het gebruikers account een inschrijvings account in de Enter prise Portal en kunnen abonnementen worden gemaakt. De gebruiker heeft toegang tot de kosten en gebruiks gegevens voor de abonnementen die ze maken.

## <a name="assign-management-group-scope-access"></a>Toegang tot bereik van beheer groepen toewijzen

Voor toegang tot weer gave van het bereik van de beheer groep is ten minste Cost Management de machtiging Lezer (of lezer) vereist. U kunt machtigingen voor een beheer groep configureren in de Azure Portal. U moet ten minste de machtiging beheerder (of eigenaar) van de gebruiker voor de beheer groep hebben om toegang voor anderen in te scha kelen. En voor Azure EA-accounts moet u ook de instelling **ao weer geven** in de EA-Portal hebben ingeschakeld.

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Selecteer **alle services** in de zijbalk, zoek naar _beheer groepen_en selecteer vervolgens **beheer groepen**.
3. Selecteer de beheer groep in de hiërarchie.
4. Klik naast de naam van de beheer groep op **Details**.
5. Selecteer **Access Control (IAM)** in het linkerdeel venster.
6. Klik op **Add**.
7. Selecteer **Cost Management Reader**onder **rol**.
8. Onder **toegang toewijzen aan**selecteert u **Azure AD-gebruiker,-groep of-toepassing**.
9. Als u toegang wilt toewijzen, zoekt u de gebruiker en selecteert u deze.
10. Klik op **Opslaan**.  
    ![example informatie in het vak Machtigingen toevoegen voor een beheer groep @ no__t-1

## <a name="assign-subscription-scope-access"></a>Toegang tot het abonnements bereik toewijzen

Voor toegang tot een abonnement is ten minste Cost Management de machtiging Lezer (of lezer) vereist. U kunt machtigingen configureren voor een abonnement in de Azure Portal. U moet ten minste beschikken over de machtiging beheerder (of eigenaar) van de gebruiker voor het abonnement om toegang voor anderen in te scha kelen. En voor Azure EA-accounts moet u ook de instelling **ao weer geven** in de EA-Portal hebben ingeschakeld.

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Selecteer **alle services** in de zijbalk, zoek naar _abonnementen_en selecteer vervolgens **abonnementen**.
3. Selecteer uw abonnement.
4. Selecteer **Access Control (IAM)** in het linkerdeel venster.
5. Klik op **Add**.
6. Selecteer **Cost Management Reader**onder **rol**.
7. Onder **toegang toewijzen aan**selecteert u **Azure AD-gebruiker,-groep of-toepassing**.
8. Als u toegang wilt toewijzen, zoekt u de gebruiker en selecteert u deze.
9. Klik op **Opslaan**.

## <a name="assign-resource-group-scope-access"></a>Toegang tot het bereik van resource groepen toewijzen

Voor toegang tot een resource groep is ten minste Cost Management de machtiging Lezer (of lezer) vereist. U kunt machtigingen configureren voor een resource groep in de Azure Portal. U moet ten minste over de machtiging beheerder (of eigenaar) voor gebruikers toegang voor de resource groep beschikken om de toegang voor anderen in te scha kelen. En voor Azure EA-accounts moet u ook de instelling **ao weer geven** in de EA-Portal hebben ingeschakeld.

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Selecteer **alle services** in de zijbalk, zoek naar _resource groepen_en selecteer vervolgens **resource groepen**.
3. Selecteer de resource groep.
4. Selecteer **Access Control (IAM)** in het linkerdeel venster.
5. Klik op **Add**.
6. Selecteer **Cost Management Reader**onder **rol**.
7. Onder **toegang toewijzen aan**selecteert u **Azure AD-gebruiker,-groep of-toepassing**.
8. Als u toegang wilt toewijzen, zoekt u de gebruiker en selecteert u deze.
9. Klik op **Opslaan**.

## <a name="cross-tenant-authentication-issues"></a>Problemen met verificatie tussen tenants

Momenteel heeft Azure Cost Management beperkte ondersteuning voor verificatie tussen tenants. In sommige gevallen is het mogelijk dat er een fout bericht over **geweigerde toegang** wordt weer gegeven in de kosten analyse. Dit probleem kan optreden als u RBAC (op rollen gebaseerd toegangs beheer) configureert voor het abonnement van een andere Tenant en vervolgens probeert de kosten gegevens weer te geven.

*Het probleem*omzeilen: nadat u RBAC tussen tenants hebt geconfigureerd, wacht u een uur. Probeer vervolgens de kosten in kosten analyse weer te geven of Cost Management toegang te verlenen aan gebruikers in beide tenants.  


## <a name="next-steps"></a>Volgende stappen

- Als u de eerste Snelstartgids voor Cost Management nog niet hebt voltooid, kunt u deze lezen bij het analyseren van de [kosten](quick-acm-cost-analysis.md).
