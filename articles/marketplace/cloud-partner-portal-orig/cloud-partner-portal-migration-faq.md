---
title: Veelgestelde vragen over de migratie naar partnercentrum | Azure Marketplace
description: In dit artikel worden veelgestelde vragen over de migratie van aanbiedingen van Cloud Partner Portal naar Partner Center beantwoord.
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mingshen
ms.openlocfilehash: 672153eba4aa2b739b67694f939c4796b39ac4c6
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274378"
---
# <a name="frequently-asked-questions-for-migrating-from-the-cloud-partner-portal-to-partner-center"></a>Veelgestelde vragen voor het migreren van het Cloud Partner Portal naar Partner Center

In dit artikel worden veelgestelde vragen over de migratie van aanbiedingen van het Cloud Partner Portal naar partnercentrum beantwoord.

## <a name="what-does-offer-migration-mean"></a>Wat betekent aanbod migratie?

We verplaatsen uw aanbiedingsgegevens van het Cloud Partner Portal naar partnercentrum met wijzigingen in de publicatie- en beheerervaring van het aanbod.

| Onderwerp  | Wijzigingen  |
|-------|----------|
| **Publicatie en aanbod management ervaring** | Je hebt een verbeterde gebruikerservaring met een intuïtieve interface in Partner Center. Zie [Wat zijn de verschillen tussen Partner Center en de Cloud Partner Portal voor](#what-are-the-differences-between-partner-center-and-the-cloud-partner-portal) meer informatie? |
| **Beschikbaarheid van uw aanbiedingen op de markt** | Er zijn geen wijzigingen. Als uw aanbieding live is op de markt, blijft het live tijdens en nadat de migratie is voltooid. |
| **Nieuwe aankopen en implementaties** | Er zijn geen wijzigingen. Uw klanten zullen uw aanbiedingen zonder onderbrekingen kunnen blijven kopen en implementeren. |
| **Uitbetalingen** | Alle aankopen en implementaties die tijdens of na de migratie plaatsvinden, worden gewoon aan u uitbetaald. |
|**API-integraties met bestaande [API's voor Cloud Partner Portal](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)** | Bestaande API's van cloudpartnersportal blijven na de migratie worden ondersteund en uw bestaande integraties blijven werken. Zie [Zal de API's van cloudpartnerportal rest na migratie worden ondersteund?](#will-the-cloud-partner-portal-rest-apis-be-supported-post-migration) |
| | |

## <a name="can-i-still-access-the-cloud-partner-portal-and-manage-my-offers-during-migration"></a>Kan ik nog steeds toegang krijgen tot de Cloud Partner Portal en mijn aanbiedingen beheren tijdens de migratie?

Aanbiedingen worden gemigreerd naar Partner Center vanaf 13 april gedurende de maand mei. Gedurende deze periode heb je zoals gewoonlijk toegang tot de Cloud Partner Portal, behalve de tijd die je aanbiedt voor migratie.
Terwijl uw aanbiedingen worden gemigreerd, hebben ze de status 'Vergrendeld – verplaatsen naar partnercentrum', zoals te zien is in de volgende schermafbeelding. Deze migratieperiode moet minder dan 24 uur zijn. Gedurende deze periode u geen updates uitvoeren voor uw aanbiedingen. Je ontvangt een e-mailmelding nadat we de migratie van je aanbiedingen hebben voltooid.

:::image type="content" source="media/migration-faq/all-offers-1.png" alt-text="Illustreert de status van gemigreerde aanbiedingen.":::

Nadat uw aanbiedingen zijn gemigreerd, worden ze **gedurende een beperkte periode** alleen in de Cloud Partner Portal. Hun status toont 'Verplaatst naar partnercentrum' en bevat een link naar uw aanbieding in partnercentrum, zoals in de volgende schermafbeeldingen wordt weergegeven. Vanaf dit punt beheert u updates voor al uw aanbiedingen of maakt u uitsluitend nieuwe aanbiedingen via Partner Center,

:::image type="content" source="media/migration-faq/all-offers-2.png" alt-text="Illustreert het bericht voor aanbiedingen die zijn gemigreerd naar Partner Center":::

:::image type="content" source="media/migration-faq/offer-has-moved.png" alt-text="Hiermee wordt de pagina Cloud Partner Portal weergegeven voor een gemigreerde aanbieding.":::

## <a name="how-will-i-create-new-offers"></a>Hoe maak ik nieuwe aanbiedingen?

Vanuit de Cloud Partner Portal wordt u op gedragen om nieuwe aanbiedingen te maken in partnercentrum

:::image type="content" source="media/migration-faq/create-new-offer-1.png" alt-text="Illustreert het menu om een nieuwe aanbieding te maken in Cloud Partner Portal":::

Nadat u een nieuwe aanbieding hebt geselecteerd, ziet u een bericht, zoals het volgende.

:::image type="content" source="media/migration-faq/create-new-offer-2.png" alt-text="Illustreert het bericht dat is ontvangen bij het maken van een nieuwe aanbieding in CPP":::

## <a name="do-i-need-to-create-a-new-account-to-manage-offers-in-partner-center"></a>Moet ik een nieuw account aanmaken om aanbiedingen in Partner Center te beheren?

Nee. Uw onderliggende account blijft behouden en u moet deze al beheren in het Partnercenter. Dit betekent dat als u een bestaande partner bent, u uw bestaande Cloud Partner Portal-accountreferenties gebruiken om u na de migratie aan te melden bij partnercentrum. Alleen aanbiedingen en de bijbehorende beheerervaring gaan van het Cloud Partner Portal naar partnercenter. We vragen je geen nieuwe accounts aan te maken, omdat je aanbiedingen niet worden gekoppeld aan het nieuwe account.

## <a name="i-see-a-message-in-the-cloud-partner-portal-to-activate-my-account-what-does-this-mean"></a>Ik zie een bericht in de Cloud Partner Portal om mijn account te activeren, wat betekent dit?

We hebben nog een paar details van u nodig om uw account naar partnercentrum te migreren en u in staat te stellen uw aanbiedingen in Partner Center te beheren nadat de aanbiedingsmigratie is voltooid. Zie [Accountmigratie van Cloud Partner Portal naar Partner Center voor](https://docs.microsoft.com/azure/marketplace/partner-center-portal/account-migration-from-cpp-to-pc)meer informatie over accountactivering.

De stappen die nodig zijn om de activering van het account te voltooien, zijn afhankelijk van uw accountrol.

| Accountrol | Activeringsstappen |
|--------------|----------------|
|Eigenaar | Ga naar de [pagina Publisher-profiel](https://cloudpartner.azure.com/#profile) in de Cloud Partner Portal en klik op de koppeling in de banner om te activeren. Je wordt doorgestuurd naar Partner Center om de accountactivering te voltooien. |
| Inzender | Alleen een gebruiker in het account met een eigenaarrol kan het account activeren. Neem contact op met uw accounteigenaren om de activering van het account te voltooien. Uw accounteigenaren moeten worden vermeld in het bannerbericht. |
| | |

## <a name="im-having-trouble-logging-in-to-my-account-and-opening-a-support-ticket"></a>Ik heb problemen met inloggen op mijn account en het openen van een ondersteuningsticket

Als je niet inloggen op je account, kun je een [ondersteuningsticket openen.](https://partner.microsoft.com/support/v2/?stage=1)

## <a name="where-can-i-find-documentation-on-the-new-partner-center-publishing-experience"></a>Waar vind ik documentatie over de nieuwe publicatieervaring van het Partner Center?

Ga naar de [commerciële marktplaats documentatie](https://docs.microsoft.com/azure/marketplace/). Vouw vervolgens **Commerciële Marketplace-portal**  > uit in partnercentrum**Maak een nieuwe aanbieding** om de help-onderwerpen te zien voor het maken van elk type aanbieding.

:::image type="content" source="media/migration-faq/marketplace-help-topics.png" alt-text="Illustreert de help-onderwerpen voor partnercentrum":::

### <a name="what-are-the-differences-between-partner-center-and-the-cloud-partner-portal"></a>Wat zijn de verschillen tussen Partner Center en de Cloud Partner Portal?

Mogelijk merkt u de volgende verschillen tussen het Cloud Partner Portal en partnercentrum.

### <a name="modular-publishing-capabilities"></a>Mogelijkheden voor modulair publiceren

Partnercentrum biedt een modulaire publicatieoptie waarmee u de wijzigingen selecteren die u wilt publiceren in plaats van altijd alle updates tegelijk te publiceren. In de volgende schermafbeelding ziet u bijvoorbeeld dat de enige wijzigingen die zijn geselecteerd om te worden gepubliceerd, de wijzigingen in **eigenschappen** en **aanbieding aanbieden**zijn.

:::image type="content" source="media/migration-faq/review-and-publish-migration.png" alt-text="Illustreert de pagina Voorbeeld en publicatie":::

De updates die u niet publiceert, worden opgeslagen als concepten. Blijf je aanbiedingsvoorbeeld gebruiken om je aanbieding te verifiëren voordat je het live maakt voor het publiek.

### <a name="rich-text-format"></a>Tekstnotatie met opmaak

Verbeter je aanbieding en plan de beschrijving met de pagina Tekst rijk op de pagina Aanbiedingsaanbieding en Aanbiedingplannen.

:::image type="content" source="media/migration-faq/rich-text-editor-migration.png" alt-text="Illustreert de teksteditor met rijke tekst":::

### <a name="enhanced-preview-options"></a>Uitgebreide voorbeeldopties

Partnercentrum bevat een [vergelijkingsfunctie](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#compare-changes-to-marketplace-offers) met verbeterde filteropties. Dit geeft u de mogelijkheid om te vergelijken met de preview en live versies van het aanbod.

:::image type="content" source="media/migration-faq/enhanced-preview.png" alt-text="Illustreert de vergelijkingsfunctie":::

### <a name="branding-and-navigation-changes"></a>Wijzigingen in branding en navigatie

U zult merken dat sommige branding veranderingen. SKU's worden bijvoorbeeld gebrandmerkt als 'Plannen' in partnercentrum.

:::image type="content" source="media/migration-faq/plan-overview-migration.png" alt-text="Illustreert het planoverzicht.":::

Ook worden de informatie die u hebt verstrekt op de **pagina's Marketplace** of S**torefront Details** (Consulting Service, Power BI app) in de Cloud Partner Portal verzameld op de pagina **Aanbiedingsvermelding** in Partnercenter.

:::image type="content" source="media/migration-faq/offer-listing-migration.png" alt-text="Hiermee wordt de aanbiedingspagina weergegeven.":::

De informatie die u hebt gebruikt om SKU's op één pagina in de Cloud Partner Portal te verstrekken, kan nu op verschillende pagina's in het Partnercentrum worden verzameld:

* Pagina instellen plannen
* Aanbiedingspagina plannen
* Beschikbaarheidspagina plannen
* Plan technische configuratie pagina, zoals weergegeven in deze screenshot.

:::image type="content" source="media/migration-faq/tech-config-migration.png" alt-text="Hiermee wordt de pagina Technische configuratie plannen weergegeven.":::

Uw aanbiedings-ID wordt nu weergegeven op de linkernavigatiebalk van de aanbieding.

:::image type="content" source="media/migration-faq/offer-id-offer-overview.png" alt-text="Hiermee wordt het linkernavigatiemenu geïllustreerd met de aanbiedings-ID.":::

### <a name="stop-selling-an-offer"></a>Stop met het verkopen van een aanbieding

U een aanvraag indienen om een aanbieding rechtstreeks vanuit het Partnercenter-portaal niet meer op de marktplaats te [verkopen.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan) De optie is beschikbaar op de **overzichtspagina aanbieding** voor uw aanbieding.

:::image type="content" source="media/migration-faq/stop-selling-migration.png" alt-text="Hiermee wordt de pagina Aanbiedingsoverzicht weergegeven met de optie Stoppen met verkopen.":::

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Welke pagina's in Partner Center komen overeen met pagina's die ik heb gebruikt in de Cloud Partner Portal?

In de volgende tabel worden overeenkomstige koppelingen tussen de twee portalen weergegeven.

| Pagina | Koppeling CloudPartnerportal | Koppeling Partnercentrum |
|------|---------------------------|---------------------|
| **Pagina voor Alle aanbiedingen** | https://cloudpartner.azure.com/#alloffers | https://partner.microsoft.com/dashboard/commercial-marketplace/overview |
| **Pagina voor Alle uitgevers** | https://cloudpartner.azure.com/#publishers | https://partner.microsoft.com/dashboard/account/v3/publishers/list |
| **Publisher-profiel** | https://cloudpartner.azure.com/#profile | https://partner.microsoft.com/dashboard/account/management |
| **Pagina voor Gebruikers** | https://cloudpartner.azure.com/#users | https://partner.microsoft.com/dashboard/account/usermanagement |
| **Geschiedenispagina** | https://cloudpartner.azure.com/#history | De functie Geschiedenis wordt nog niet ondersteund in partnercentrum. |
| **Insights-dashboard** | https://cloudpartner.azure.com/#insights | https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary |
| **Uitbetalingsrapport** | https://cloudpartner.azure.com/#insights/payout | https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| | |

## <a name="will-the-cloud-partner-portal-rest-apis-be-supported-post-migration"></a>Worden de API's van Cloud Partner Portal REST ondersteund na migratie?

De API's van cloudpartnerportalen zijn geïntegreerd met het Partner Center en blijven werken nadat uw aanbiedingen zijn gemigreerd naar partnercentrum. De integratie brengt kleine veranderingen met zich mee. Bekijk de wijzigingen in de onderstaande tabel om ervoor te zorgen dat uw code blijft werken na de migratie naar partnercentrum.

| **Api** | **Beschrijving wijzigen** | **Impact** |
| ------- | ---------------------- | ---------- |
| POST publiceren, GoLive, Annuleren | Voor gemigreerde aanbiedingen heeft de antwoordkop een andere indeling, maar blijft deze op dezelfde manier werken, waarbij een relatief pad wordt aangenomen om de bewerkingsstatus op te halen. | Wanneer u een van de bijbehorende POST-aanvragen voor een aanbieding verzendt, heeft de koptekst Locatie een van de twee indelingen, afhankelijk van de migratiestatus van de aanbieding:<ul><li>Niet-gemigreerde aanbiedingen<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Gemigreerde aanbiedingen<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| GET-bewerking | Voor aanbiedingen die eerder worden ondersteund 'notification-email' veld in het antwoord, zal dit veld worden afgeschaft en niet meer geretourneerd voor gemigreerde aanbiedingen. | Voor gemigreerde aanbiedingen sturen we geen meldingen meer naar de lijst met e-mails die in de aanvragen zijn opgegeven. In plaats daarvan wordt de API-service afgestemd op het e-mailproces van meldingen in Partnercentrum om e-mails te verzenden. In het bijzonder worden meldingen verzonden naar het e-mailadres dat is ingesteld in de sectie Contactgegevens van de verkoper van uw accountinstellingen in het Partnercentrum, om u op de hoogte te stellen van de voortgang van de werking.<br><br>Controleer het e-mailadres dat is ingesteld in de sectie Contactgegevens van verkoper in de [accountinstellingen](https://partner.microsoft.com/dashboard/account/management) in het partnercentrum om te controleren of de juiste e-mail wordt verstrekt voor meldingen.  |
| | | |
