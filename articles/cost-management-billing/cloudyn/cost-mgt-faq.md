---
title: Veelgestelde vragen over Cloudyn in Azure
description: Ontdek hoe u de Cloudyn-portal kunt gebruiken om algemene indirecte ondernemingsinstallatieproblemen op te lossen en antwoord te krijgen op andere veelgestelde vragen.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 7b78d9d7bf725e3d82c88360c822455de1301ef6
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88688025"
---
# <a name="frequently-asked-questions-for-cloudyn"></a>Veelgestelde vragen over Cloudyn

In dit artikel worden enkele veelgestelde vragen over Cloudyn behandeld. Als u vragen hebt over Cloudyn, kunt u deze stellen op [Veelgestelde vragen over Cloudyn](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-cloudyn-cost-management?forum=Cloudyn).

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>Hoe kan ik algemene indirecte ondernemingsinstallatieproblemen oplossen?

Wanneer u de beheerportal van Cloudyn voor het eerst gebruikt, ziet u mogelijk de volgende berichten als u een Enterprise Agreement- of Cloud Solution Provider-gebruiker (CSP) bent:

- De opgegeven API-sleutel is geen inschrijvingssleutel van het hoogste niveau weergegeven in de wizard voor het **instellen van Cloudyn**.
- Directe inschrijving – Nee weergegeven in de Enterprise Agreement-portal.
- Er zijn geen gebruiksgegevens gevonden voor de afgelopen 30 dagen. Neem contact op met uw leverancier om te controleren of de markering is ingeschakeld voor uw Azure-account weergegeven in de Cloudyn-portal.

De voorgaande berichten geven aan dat u een Azure Enterprise Agreement via een wederverkoper of CSP hebt aangeschaft. Uw wederverkoper of CSP moet _markeren_ inschakelen voor uw Azure-account zodat u uw gegevens kunt weergeven in Cloudyn.

Los de problemen als volgt op:

1. Uw wederverkoper moet _markeren_ voor uw account inschakelen. Raadpleeg de [Onboarding-handleiding voor indirecte klanten](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide) voor meer instructies.

2. U genereert de Azure Enterprise Overeenkomst-sleutel om Cloudyn te gebruiken. Zie [Uw Azure EA toevoegen](quick-register-ea.md#register-with-cloudyn) of [How to Find Your EA Enrollment ID and API Key](https://youtu.be/u_phLs_udig) voor instructies (Engelstalig).

Alleen een Azure-servicebeheerder kan Cloudyn inschakelen. U hebt onvoldoende co-beheerdersmachtigingen.

Voordat u de Azure Enterprise Agreement API-sleutel voor het instellen van Cloudyn kunt genereren, moet u de Azure-facturerings-API inschakelen door de instructies te volgen op:

- [Overzicht van Rapportage-API's voor Enterprise-klanten](../manage/enterprise-api.md)
- [Microsoft Azure enterprise portal rapportage-API](https://ea.azure.com/helpdocs/reportingAPI) onder **Gegevenstoegang tot de API inschakelen**


U moet wellicht ook afdelingsbeheerders, accounteigenaars en enterprise-administrators machtigingen geven voor _Kosten weergeven_ met de facturerings-API.

## <a name="why-dont-i-see-optimizer-recommendations"></a>Waarom zie ik geen Optimizer-aanbevelingen?

Aanbevelingsinformatie is alleen beschikbaar voor accounts die zijn geactiveerd. U ziet geen aanbevelingsinformatie in **Optimizer**-rapportcategorieën voor accounts die *niet geactiveerd* zijn, waaronder:

- Optimalisatiebeheer
- Grootte van optimalisatie aanpassen
- Inefficiënties

Als u geen Optimizer-aanbevelingsgegevens kunt weergeven, hebt u waarschijnlijk accounts die niet zijn geactiveerd. Om een account te activeren, moet u het account registreren met uw Azure-referenties.

Een account activeren:

1.    Klik in de rechterbovenhoek van de portal van Cloudyn op **Settings** en selecteer **Cloud Accounts**.
2.    Zoek op het tabblad Microsoft Azure-accounts naar accounts met een **niet-geactiveerd** abonnement.
3.    Klik rechts van een niet-geactiveerd account op het symbool **bewerken**, dat lijkt op een potlood.
4.    Uw tenant-id en tarief-id worden automatisch gedetecteerd. Klik op **Volgende**.
5.    U wordt omgeleid naar de Azure-portal. Meld u aan bij de portal en autoriseer Cloudyn Collector voor toegang tot uw Azure-gegevens.
6.    U wordt omgeleid naar de pagina voor het beheren van Cloudyn-accounts, en uw abonnement wordt bijgewerkt naar de accountstatus **Actief**. Er wordt een groen vinkje weergegeven.
7.    Als u geen groen vinkje ziet voor een of meer van de abonnementen, betekent dit dat u niet bent gemachtigd om een lezer-app (CloudynCollector) te maken voor het abonnement. Een gebruiker met hogere machtigingen voor het abonnement moet stap 3 en 4 herhalen.  

Nadat u de voorgaande stappen hebt voltooid, kunt u Optimizer-aanbevelingen binnen een tot twee dagen weergeven. Het kan echter tot wel vijf dagen duren voordat alle optimalisatiegegevens beschikbaar zijn.


## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>Hoe kan ik (tijdelijk) vergrendelde gebruikers inschakelen?

Eerst kijken we naar de meestvoorkomende oorzaak voor gebruikersaccounts met de status *initiallySuspended* (tijdelijk vergrendeld).

> Admin1 kan bijvoorbeeld een Microsoft Cloud Solution Provider- of Enterprise Agreement-gebruiker zijn. Zijn organisatie is klaar om Cloudyn te gaan gebruiken.  Hij registreert de Azure-portal en meldt zich aan bij de Cloudyn-portal. Omdat hij de Cloudyn-service registreert en zich aanmeldt bij de Cloudyn-portal, wordt Admin1 de *primaire beheerder*. Admin1 maakt geen gebruikersaccounts. Met de Cloudyn-portal maken ze echter Azure-accounts en stellen ze een entiteitshiërarchie in. Admin1 informeert Admin2, een tenantbeheerder, dat ze zich moeten registreren bij Cloudyn en zich moeten aanmelden bij de Cloudyn-portal.
>
> Admin2 registreert zich via de Azure-portal. Wanneer ze zich echter proberen aan te melden bij de Cloudyn-portal, wordt er een foutbericht weergegeven dat het account **vergrendeld** is. De primaire beheerder, Admin1, wordt op de hoogte gesteld van de blokkering. Admin1 moet het account van Admin2 activeren en *beheerdersentiteitstoegang* verlenen voor de juiste entiteiten. Hiermee wordt gebruikersbeheertoegang toegestaan en het gebruikersaccount geactiveerd.


Als u een waarschuwing krijgt met een verzoek om toegang voor een gebruiker toe te staan, moet u het gebruikersaccount activeren.

Het gebruikersaccount activeren:

1. Meld u aan bij Cloudyn met behulp van het gebruikersaccount voor Azure-beheerdersrechten dat u hebt gebruikt om Cloudyn in te stellen. Of meld u aan met een gebruikersaccount waaraan beheerderstoegang is verleend.
2. Klik in de rechterbovenhoek op het tandwielpictogram en selecteer **User Management** (Gebruikersbeheer).
3. Zoek de gebruiker, selecteer het potloodsymbool en bewerk de gebruiker.
4. Wijzig onder **User status** (gebruikersstatus) de status **Suspended** (vergrendeld) in **Active** (actief).

Voor Cloudyn-gebruikersaccounts wordt verbinding gemaakt via eenmalige aanmelding vanuit Azure. Als gebruikers het verkeerde wachtwoord invoeren, wordt hun toegang tot Cloudyn mogelijk vergrendeld, ook als ze nog wel toegang tot Azure hebben.

Als u in Cloudyn een ander e-mailadres opgeeft dan het standaardadres in Azure, kan uw account worden vergrendeld. Mogelijk wordt 'status initiallySuspended' weergegeven. Als uw gebruikersaccount is vergrendeld, neemt u contact op met een andere beheerder om uw account opnieuw in te stellen.

We raden u aan ten minste twee Cloudyn-beheerdersaccounts te maken voor het geval een van de accounts wordt vergrendeld.

Als u zich niet kunt aanmelden bij de Cloudyn-portal, moet u nagaan of u wel de juiste URL gebruikt om u aan te melden bij Cloudyn. Gebruik [https://azure.cloudyn.com](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade).

Gebruik niet de rechtstreekse Cloudyn-URL `https://app.cloudyn.com`.

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>Hoe kan ik niet-geactiveerde accounts activeren met Azure-referenties?

Zodra uw Azure-accounts worden gedetecteerd door Cloudyn, worden er direct kostengegevens vermeld in kostenrapporten. U moet echter uw Azure-referenties voor de accounts registreren om gebruik te kunnen maken van gebruiks- en prestatiegegevens in Cloudyn. Zie [Een account toevoegen of een abonnement bijwerken](activate-subs-accounts.md#add-an-account-or-update-a-subscription) voor instructies.

Als u Azure-referenties voor een account wilt toevoegen, selecteert u in de Cloudyn-portal het symbool voor bewerken, rechts van de accountnaam, niet het abonnement.

Pas als uw Azure-referenties zijn toegevoegd aan Cloudyn, wordt het account weergegeven als _niet-geactiveerd_.

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>Hoe kan ik meerdere accounts en entiteiten toevoegen aan een bestaand abonnement?

U kunt aanvullende Enterprise-overeenkomsten aan een Cloudyn-abonnement toevoegen met behulp van aanvullende entiteiten. Zie [Entiteiten maken en beheren](tutorial-user-access.md#create-and-manage-entities) voor meer informatie.

Voor CSP's:

Als u extra CSP-accounts wilt toevoegen aan een entiteit, selecteert u **MSP-toegang** in plaats van **Enterprise** wanneer u de nieuwe entiteit maakt. Als uw account is geregistreerd als Enterprise Agreement-account en u CSP-referenties wilt toevoegen, moet het Cloudyn-ondersteuningspersoneel mogelijk uw accountinstellingen wijzigen. Als u een betalende Azure-abonnee bent, kunt u in de Azure-portal een nieuwe ondersteuningsaanvraag maken. Selecteer **Help + ondersteuning** en vervolgens **Nieuwe ondersteuningsaanvraag**.

## <a name="currency-symbols-in-cloudyn-reports"></a>Valutasymbolen in Cloudyn-rapporten

Mogelijk hebt u meerdere Azure-accounts waarin verschillende valuta's worden gebruikt. Kostenrapporten in Cloudyn bevatten echter niet meer dan één valuta type per rapport.

Als u meerdere abonnementen hebt die gebruikmaken van verschillende valuta's, worden de valuta's van een bovenliggende entiteit en de onderliggende entiteiten weergegeven in USD **$** . Onze voorgestelde best practice is om geen verschillende valuta's in één entiteitshiërarchie te gebruiken. Met andere woorden, gebruik dezelfde valuta voor alle abonnementen die in een entiteitsstructuur zijn georganiseerd.

De valuta van uw Enterprise Agreement-abonnement wordt in Cloudyn automatisch gedetecteerd en juist weergegeven in rapporten.  In Cloudyn wordt echter alleen USD **$** weergegeven voor CSP-Azure-accounts en Azure-accounts die rechtstreeks via internet worden gebruikt.

## <a name="what-are-cloudyn-data-refresh-timelines"></a>Wat zijn tijdlijnen voor het vernieuwen van Cloudyn-gegevens?

Cloudyn heeft de volgende tijdlijnen voor het vernieuwen van gegevens:

- **Oorspronkelijk**: Na het instellen kan het tot 24 uur duren voordat u kostengegevens kunt bekijken in Cloudyn. Het kan ook tot 10 dagen duren voordat in Cloudyn voldoende gegevens zijn verzameld om grootte-aanbevelingen weer te geven.
- **Dagelijks**: Vanaf de tiende dag tot het einde van elke maand zouden uw gegevens bij weergave in Cloudyn up-to-date moeten zijn vanaf de vorige dag na ongeveer UTC+3 de volgende dag.
- **Maandelijks**: Vanaf de eerste tot de tiende dag van elke maand ziet u in Cloudyn mogelijk alleen uw gegevens tot en met het einde van de vorige maand.

In Cloudyn worden gegevens voor de vorige dag verwerkt zodra alle gegevens van de vorige dag beschikbaar zijn. De gegevens van de vorige dag zijn doorgaans dagelijks beschikbaar in Cloudyn om ongeveer UTC+3. Het kan nog eens 24 uur duren om bepaalde gegevens, zoals tags, te verwerken.

Gegevens voor de huidige maand kunnen pas worden verzameld aan het begin van de volgende maand. Gedurende de periode voltooien serviceproviders hun facturering voor de afgelopen maand. De gegevens van de vorige maand worden in Cloudyn 5 tot 10 dagen na het begin van de maand weergegeven. Gedurende deze tijd ziet u mogelijk alleen de afgeschreven kosten van de vorige maand. Mogelijk worden er geen dagelijkse facturerings- of gebruiksgegevens weergegeven. Zodra de gegevens beschikbaar zijn, worden deze met terugwerkende kracht in Cloudyn verwerkt. Na de verwerking worden alle maandelijkse gegevens weergegeven tussen de vijfde en tiende dag van elke maand.

Als er een vertraging optreedt bij het verzenden van gegevens van Azure naar Cloudyn, worden de gegevens nog steeds opgenomen in Azure. De gegevens worden overgebracht naar Cloudyn wanneer de verbinding is hersteld.

## <a name="cost-fluctuations-in-cloudyn-cost-reports"></a>Kostenschommelingen in Cloudyn-kostenrapporten

Kostenrapporten kunnen kostenschommelingen vertonen wanneer cloudserviceproviders bijgewerkte factureringsbestanden verzenden. Kostenschommelingen kunnen zich voordoen wanneer er nieuwe bestanden worden ontvangen van een cloudserviceprovider buiten de gebruikelijke dagelijkse of maandelijkse rapportage. Kostenwijzigingen zijn niet het gevolg van herberekening in Cloudyn.

Gedurende de maand zijn alle factureringsbestanden die door uw cloudserviceprovider worden verzonden, een schatting van uw dagelijkse kosten. Gegevens worden regelmatig bijgewerkt, soms meerdere keren per dag. Updates zijn gebruikelijker bij AWS dan Azure. De totale kosten moeten ongeveer gelijk zijn als de factuurberekening voor de vorige maand is voltooid en het definitieve factureringsbestand wordt ontvangen. Gewoonlijk is dat op de tiende dag van de maand.

Er vinden wijzigingen plaats wanneer u kostencorrecties van uw cloudserviceprovider ontvangt. Als u een tegoed krijgt, bijvoorbeeld. Ook maanden na de betreffende maand kunnen er nog wijzigingen worden doorgevoerd. Wijzigingen worden weergegeven wanneer de cloudserviceprovider een herberekening uitvoert. De historische gegevens worden in Cloudyn bijgewerkt om ervoor te zorgen dat alle correcties opnieuw worden berekend. Tevens wordt ervoor gezorgd dat de kosten juist worden weergegeven in rapporten.

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>Hoe kan een directe CSP toegang tot Cloudyn configureren voor indirecte CSP-klanten of -partners?

Zie [Indirecte CSP-toegang in Cloudyn configureren](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn) voor instructies.

## <a name="what-causes-the-optimizer-menu-item-to-appear"></a>Waarom wordt de menuopdracht Optimizer weergegeven?

Nadat u Azure Resource Manager-toegang hebt toegevoegd en er gegevens zijn verzameld, ziet u de optie **Optimizer**. Zie [Hoe kan ik niet-geactiveerde accounts activeren met Azure-referenties?](#how-do-i-activate-unactivated-accounts-with-azure-credentials) als u Azure Resource Manager-toegang wilt activeren.

## <a name="is-cloudyn-agent-based"></a>Is Cloudyn gebaseerd op een agent?

Nee. Agents worden niet gebruikt. De metrische gegevens van virtuele Azure-machines worden verzameld via de Microsoft Insights-API. Als u metrische gegevens van virtuele Azure-machines wilt verzamelen, moeten diagnostische instellingen zijn ingeschakeld.

## <a name="do-cloudyn-reports-show-more-than-one-ad-tenant-per-report"></a>Worden er meerdere AD-tenants per rapport weergegeven in Cloudyn-rapporten?

Ja. U kunt [een bijbehorende cloudaccountentiteit maken](tutorial-user-access.md#create-and-manage-entities) voor elke AD-tenant die u hebt. Vervolgens kunt u alle gegevens van uw Azure AD-tenants en andere cloudplatformproviders weergeven, inclusief Amazon Web Services en Google Cloud Platform.
