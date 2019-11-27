---
title: Veelgestelde vragen over Cloudyn in Azure | Microsoft Docs
description: In dit artikel vindt u antwoorden op enkele veelgestelde vragen over Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: troubleshooting
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: faa7181ebc7b886fbe06e4ac01c704016d4b0c51
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230103"
---
# <a name="frequently-asked-questions-for-cloudyn"></a>Veelgestelde vragen over Cloudyn

In dit artikel komen enkele veelgestelde vragen over Cloudyn. Als u vragen hebt over Cloudyn, kunt u deze vragen op [Veelgestelde](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-cloudyn-cost-management?forum=Cloudyn)vragen over Cloudyn.

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>Hoe kan ik problemen met het algemene indirecte enterprise instellen oplossen?

Wanneer u de beheerportal van Cloudyn voor het eerst gebruikt, ziet u mogelijk de volgende berichten als u een Enterprise Agreement- of Cloud Solution Provider-gebruiker (CSP) bent:

- "De opgegeven API-sleutel is geen inschrijvings sleutel op het hoogste niveau die wordt weer gegeven in de wizard **Cloudyn instellen** .
- 'Directe inschrijving: geen' weergegeven in de Enterprise Agreement-portal.
- "Er zijn geen gebruiksgegevens is gevonden voor de afgelopen 30 dagen. Neem contact op met uw leverancier om te controleren of de opmaak is ingeschakeld voor uw Azure-account' weergegeven in de Cloudyn-portal.

De voorgaande berichten geven aan dat u een Azure Enterprise Agreement via een wederverkoper of CSP hebt aangeschaft. Uw wederverkoper of CSP moet _markeren_ inschakelen voor uw Azure-account zodat u uw gegevens kunt weergeven in Cloudyn.

Los de problemen als volgt op:

1. Uw wederverkoper moet _markeren_ voor uw account inschakelen. Raadpleeg de [Onboarding-handleiding voor indirecte klanten](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide) voor meer instructies.

2. U genereert de Azure Enterprise Overeenkomst-sleutel om Cloudyn te gebruiken. Zie [uw Azure EA toevoegen](quick-register-ea.md#register-with-cloudyn) of [uw EA-inschrijvings-ID en API-sleutel vinden](https://youtu.be/u_phLs_udig)voor instructies.

Alleen een Azure-servicebeheerder kan Cloudyn inschakelen. U hebt onvoldoende co-beheerdersmachtigingen.

Voordat u de Azure Enterprise Agreement API-sleutel voor het instellen van Cloudyn kunt genereren, moet u de Azure-facturerings-API inschakelen door de instructies te volgen op:

- [Overzicht van Rapportage-API's voor Enterprise-klanten](../billing/billing-enterprise-api.md)
- [Microsoft Azure enterprise portal rapportage-API](https://ea.azure.com/helpdocs/reportingAPI) onder **Gegevenstoegang tot de API inschakelen**


U moet wellicht ook afdelingsbeheerders, accounteigenaars en enterprise-administrators machtigingen geven voor _Kosten weergeven_ met de facturerings-API.

## <a name="why-dont-i-see-optimizer-recommendations"></a>Waarom zie ik geen aanbevelingen optimaliseren?

Aanbeveling informatie is alleen beschikbaar voor accounts die worden geactiveerd. In **optimalisatie** rapport Categorieën worden geen aanbevelings gegevens weer geven voor accounts die niet meer worden *geactiveerd*, waaronder:

- Optimalisatie van Manager
- Sizing Optimization
- Inefficiëntie

Als u niet alle gegevens van de aanbeveling Optimizer weergeven, klikt u vervolgens hebt waarschijnlijk u accounts die unactivated zijn. Voor het activeren van een account dat u wilt registreren bij uw Azure-referenties.

Een account te activeren:

1.  Klik in de rechterbovenhoek van de portal van Cloudyn op **Settings** en selecteer **Cloud Accounts**.
2.  Zoek op het tabblad Microsoft Azure accounts naar accounts met een niet- **geactiveerd** abonnement.
3.  Rechts van een niet-geactiveerd account, klikt u op het **bewerkings** symbool dat lijkt op een potlood.
4.  Uw tenant-ID en tarief-ID wordt automatisch gedetecteerd. Klik op **Volgende**.
5.  U bent omgeleid naar de Azure-portal. Aanmelden bij de portal en autoriseren van Cloudyn Collector voor toegang tot uw Azure-gegevens.
6.  Vervolgens wordt u omgeleid naar de beheer pagina van de Cloudyn-accounts en wordt uw abonnement bijgewerkt met de status van het **actieve** account. U ziet een groen vinkje symbool.
7.  Als u niet een groen vinkje voor een of meer van de abonnementen ziet, betekent dit dat u bent niet gemachtigd om een reader-app (de CloudynCollector) voor het abonnement te maken. Herhaal stap 3 en 4 moet een gebruiker met hogere machtigingen voor het abonnement.  

Nadat u de voorgaande stappen hebt voltooid, kunt u optimaliseren aanbevelingen weergeven binnen één tot twee dagen. Echter, duurt het tot vijf dagen voordat de optimalisatie van de volledige gegevens beschikbaar is.


## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>Hoe schakel ik onderbroken of uitgesloten gebruikers?

Eerst kijken we naar het meest voorkomende scenario waarmee gebruikers accounts *initiallySuspended*kunnen ophalen.

> Admin1 is mogelijk een Microsoft Cloud solution provider of Enterprise Agreement gebruiker. Hun organisatie is klaar om te beginnen met het gebruik van Cloudyn.  Hij wordt geregistreerd via Azure portal en zich bij de Cloudyn-portal aanmeldt. Als degene die de Cloudyn-service registreert en zich aanmeldt bij de Cloudyn-Portal, wordt admin1 de *primaire beheerder*. Admin1 maakt niet alle gebruikersaccounts. Met de Cloudyn-portal maken ze echter Azure-accounts en stellen ze een entiteits hiërarchie in. Admin1 informeert Admin2, een Tenant beheerder, die ze moeten registreren bij Cloudyn en zich aan te melden bij de Cloudyn-Portal.
>
> Admin2 wordt geregistreerd via Azure portal. Wanneer ze zich echter proberen aan te melden bij de Cloudyn-Portal, wordt er een fout melding weer gegeven dat het account is **geschorst**. De primaire Admin1,-beheerder is op de hoogte gesteld van de account-onderbreking. Admin1 moet een Admin2's-account activeren en toegang verlenen aan de *beheer entiteit* voor de juiste entiteiten en gebruikers beheer en het gebruikers account actief toestaan.


Als u een waarschuwing met een verzoek om toegang te verlenen voor een gebruiker ontvangt, moet u het gebruikersaccount activeren.

Het gebruikersaccount activeren:

1. Meld u aan Cloudyn met behulp van het gebruikersaccount voor Azure met beheerdersrechten die u hebt gebruikt voor het instellen van Cloudyn. Of meld u aan met een gebruikersaccount dat toegang heeft gekregen.
2. Selecteer het tandwiel pictogram in de rechter bovenhoek en selecteer **gebruikers beheer**.
3. De gebruiker niet vinden, selecteert u het potloodsymbool en bewerk vervolgens de gebruiker.
4. Wijzig onder **gebruikers status**de status van **opgeschort** in **actief**.

Gebruikersaccounts van Cloudyn verbinding maken met behulp van eenmalige aanmelding van Azure. Als een gebruiker het wachtwoord mistypes, ze mogelijk toegang meer hebt tot Cloudyn, zelfs als ze nog steeds toegang Azure tot.

Als u uw e-mail adres in Cloudyn wijzigt vanuit het standaard adres in azure, kan uw account worden vergrendeld. Mogelijk wordt ' status initiallySuspended ' weer gegeven. Als uw account is vergrendeld, neem dan contact op met een andere beheerder als u uw account opnieuw wilt.

U wordt aangeraden dat u ten minste twee Cloudyn administrator-accounts maken als een van de accounts is vergrendeld.

Als u niet bij de Cloudyn-portal aanmelden, zorgt u ervoor dat u de correcte URL gebruikt voor aanmelding bij Cloudyn. Gebruik [https://azure.cloudyn.com](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade).

Vermijd het gebruik van de Cloudyn direct-URL https://app.cloudyn.com.

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>Hoe kan ik unactivated accounts met Azure-referenties activeren?

Als uw Azure-accounts worden gedetecteerd door Cloudyn, gegevens van cost onmiddellijk vindt u in rapporten op basis van kosten. Voor Cloudyn om gegevens van gebruik en de prestaties te bieden, moet u echter uw Azure-referenties voor de accounts registreren. Zie [een account toevoegen of een abonnement bijwerken](activate-subs-accounts.md#add-an-account-or-update-a-subscription)voor instructies.

Als u wilt toevoegen voor een account, Azure-referenties in de Cloudyn-portal, selecteert u het symbool bewerken aan de rechterkant van de accountnaam, niet het abonnement.

Totdat uw Azure-referenties zijn toegevoegd aan Cloudyn, wordt het account weer gegeven als niet _geactiveerd_.

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>Hoe ik meerdere accounts en entiteiten toevoegen aan een bestaand abonnement?

Als u meer entiteiten worden gebruikt om aanvullende Enterprise overeenkomsten toevoegen aan een Cloudyn-abonnement. Zie [entiteiten maken en beheren](tutorial-user-access.md#create-and-manage-entities)voor meer informatie.

Voor CSP's:

Als u extra CSP-accounts wilt toevoegen aan een entiteit, selecteert u **MSP-toegang** in plaats van **Enter prise** wanneer u de nieuwe entiteit maakt. Als uw account is geregistreerd als een Enterprise Agreement, en u wilt toevoegen van de CSP-referenties, mogelijk ondersteunend personeel van Cloudyn om de accountinstellingen van uw te wijzigen. Als u een betaald Azure-abonnee bent, kunt u een nieuwe ondersteuningsaanvraag maken in Azure portal. Selecteer **Help en ondersteuning**en selecteer vervolgens **nieuwe ondersteunings aanvraag**.

## <a name="currency-symbols-in-cloudyn-reports"></a>Valutasymbolen in Cloudyn-rapporten

Mogelijk hebt u meerdere Azure-accounts met behulp van verschillende valuta's. Kostenrapporten in Cloudyn worden echter niet meer dan één Valutatype per rapport weergegeven.

Als u meerdere abonnementen hebt die gebruikmaken van verschillende valuta's, worden een bovenliggende entiteit en de valuta's van de onderliggende entiteiten weer gegeven in USD **$** . Onze voorgestelde aanbevolen procedure is om te voorkomen dat met behulp van verschillende valuta's in de entiteitshiërarchie van dezelfde. Al uw abonnementen ingedeeld in de entiteitsstructuur van een moeten met andere woorden, gebruikt u dezelfde valuta.

Cloudyn automatisch detecteert de valuta van uw Enterprise Agreement-abonnement en geeft deze juist in rapporten.  Cloudyn geeft echter alleen USD **$** weer voor CSP-en Web-direct Azure-accounts.

## <a name="what-are-cloudyn-data-refresh-timelines"></a>Wat zijn de gegevens Cloudyn tijdlijnen vernieuwen?

Cloudyn heeft de volgende gegevens vernieuwen tijdlijnen:

- **Eerste**: nadat u hebt ingesteld, kan het tot 24 uur duren om kosten gegevens weer te geven in Cloudyn. Het kan ook voor Cloudyn voor het verzamelen van voldoende gegevens om schaling aanbevelingen weer te geven tot tien dagen duren.
- **Dagelijks**: vanaf de tiende dag tot het einde van elke maand, moeten Cloudyn uw gegevens weer geven vanaf de vorige dag na ongeveer UTC + 3 de volgende dag.
- **Maandelijks**: van de eerste dag tot de tiende dag van elke maand, Cloudyn mogelijk uw gegevens weer gegeven tot aan het einde van de vorige maand.

Cloudyn verwerkt de gegevens voor de vorige dag waarop de volledige gegevens uit de vorige dag beschikbaar is. Gegevens van de vorige dag zijn gewoonlijk beschikbaar in Cloudyn door over UTC + 3 elke dag. Sommige gegevens, zoals tags, kan een extra 24 uur voor het verwerken van duren.

Gegevens voor de huidige maand niet beschikbaar is voor de verzameling aan het begin van elke maand. Serviceproviders voltooien tijdens de periode, de facturering voor de vorige maand. Gegevens van de vorige maand wordt weergegeven in Cloudyn 5 tot 10 dagen na het begin van elke maand. Gedurende deze tijd ziet u mogelijk alleen de afgeschreven kosten van de vorige maand. U ziet mogelijk niet dagelijks facturering of gebruik gegevens. Wanneer de gegevens beschikbaar komt, verwerkt Cloudyn het met terugwerkende kracht. Na de verwerking, wordt de maandelijkse gegevens tussen de vijfde dag en de tiende dag van elke maand weergegeven.

Als er een vertraging optreden bij het verzenden van gegevens van Azure naar Cloudyn, is nog steeds gegevens geregistreerd in Azure. De gegevens worden overgedragen aan Cloudyn wanneer de verbinding wordt hersteld.

## <a name="cost-fluctuations-in-cloudyn-cost-reports"></a>Kosten schommelingen in Kostenrapporten van Cloudyn

Kostenrapporten kunnen fluctuaties in de kosten weergeven wanneer cloudserviceproviders bijgewerkte facturering bestanden verzenden. Fluctuerend kosten optreden wanneer er nieuwe bestanden worden ontvangen van een cloudserviceprovider buiten de normale dagelijks of maandelijks schema reporting. Kosten wijzigingen niet worden veroorzaakt door Cloudyn herberekening.

Gedurende de maand zijn alle facturering bestanden die zijn verzonden door uw cloudserviceprovider een schatting van uw dagelijkse kosten. Soms gegevens regelmatig wordt bijgewerkt, soms meerdere keren per dag. Er zijn updates vaker met AWS dan Azure. Totaal blijft stabiel wanneer het berekenen van facturering voor de vorige maand voltooid is en de uiteindelijke facturering bestand wordt ontvangen. Normaal gesproken door de 10e van de maand.

Wijzigingen optreden wanneer u aanpassingen van de kosten van uw cloudserviceprovider ontvangt. Tegoed ontvangen is een voorbeeld. Wijzigingen kunnen maanden optreden nadat de desbetreffende maand is gesloten. Wijzigingen worden weergegeven wanneer een herberekening wordt gemaakt door uw cloudserviceprovider. Cloudyn-updates de historische gegevens om ervoor te zorgen dat alle aanpassingen worden herberekend. Hij controleert ook dat de kosten worden weergegeven nauwkeurig in deze rapporten.

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>Hoe kan een directe CSP Cloudyn-toegang configureren voor indirecte CSP-klanten of partners?

Zie voor instructies [indirecte CSP-toegang configureren in Cloudyn](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn).

## <a name="what-causes-the-optimizer-menu-item-to-appear"></a>Wat de oorzaak van het optimalisatieprogramma menu-item wordt weergegeven?

Nadat u Azure Resource Manager toegang en gegevens hebt toegevoegd, ziet u de optie **Optimizer** . Zie Hoe kan ik niet- [geactiveerde accounts activeren met Azure-referenties](#how-do-i-activate-unactivated-accounts-with-azure-credentials) om Azure Resource Manager toegang te activeren?

## <a name="is-cloudyn-agent-based"></a>Is de Cloudyn-agent gebaseerd?

Nee. Agents worden niet gebruikt. Metrische gegevens van de virtuele machine van Azure voor virtuele machines zijn verzameld uit de Microsoft Insights-API. Als u verzamelen van metrische gegevens van virtuele Azure-machines wilt, moeten deze diagnostische instellingen zijn ingeschakeld.

## <a name="do-cloudyn-reports-show-more-than-one-ad-tenant-per-report"></a>Kunnen de Cloudyn-rapporten weergeven per rapport meer dan één AD-tenant?

Ja. U kunt [een bijbehorende Cloud account entiteit maken](tutorial-user-access.md#create-and-manage-entities) voor elke AD-Tenant die u hebt. U kunt vervolgens alle gegevens van uw Azure AD-tenant en andere cloudproviders platform, waaronder Amazon-webservices en Google Cloud Platform weergeven.
