---
title: Veelgestelde vragen over de migratie naar partner Center | Azure Marketplace
description: Dit artikel heeft betrekking op veelgestelde vragen over de migratie van aanbiedingen van Cloud Partner-portal naar het partner centrum.
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mingshen
ms.openlocfilehash: 672153eba4aa2b739b67694f939c4796b39ac4c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81274378"
---
# <a name="frequently-asked-questions-for-migrating-from-the-cloud-partner-portal-to-partner-center"></a>Veelgestelde vragen over de migratie van de Cloud Partner-portal naar het partner centrum

Dit artikel heeft betrekking op veelgestelde vragen over de migratie van aanbiedingen van de Cloud Partner-portal naar partner Center.

## <a name="what-does-offer-migration-mean"></a>Wat betekent migratie van aanbod?

We verplaatsen uw aanbiedings gegevens van de Cloud Partner-portal naar het partner centrum met wijzigingen in de ervaring voor het publiceren en beheren van aanbiedingen.

| Onderwerp  | Gewijzigde  |
|-------|----------|
| **Beheer ervaring publiceren en aanbieden** | U hebt een verbeterde gebruikers ervaring met een intuïtieve interface in Partner Center. Zie [Wat zijn de verschillen tussen partner centrum en de Cloud Partner-Portal?](#what-are-the-differences-between-partner-center-and-the-cloud-partner-portal) voor meer informatie. |
| **Beschik baarheid van uw aanbiedingen in Marketplace** | Er zijn geen wijzigingen. Als uw aanbieding Live is in de Marketplace, blijft deze live tijdens en nadat de migratie is voltooid. |
| **Nieuwe aankopen en implementaties** | Er zijn geen wijzigingen. Uw klanten blijven uw aanbiedingen zonder onderbrekingen kunnen kopen en implementeren. |
| **Uitbetalingen** | Alle aankopen en implementaties die zich voordoen tijdens of na de migratie, blijven als normaal worden betaald. |
|**API-integraties met bestaande [Cloud Partner-Portal-api's](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)** | Bestaande Cloud Partner-portal Api's worden nog steeds ondersteund nadat de migratie is voltooid en uw bestaande integraties blijven werken. Zie voor meer informatie [de Cloud Partner-Portal rest api's worden ondersteund na de migratie?](#will-the-cloud-partner-portal-rest-apis-be-supported-post-migration) |
| | |

## <a name="can-i-still-access-the-cloud-partner-portal-and-manage-my-offers-during-migration"></a>Kan ik nog steeds toegang krijgen tot de Cloud Partner-portal en mijn aanbiedingen beheren tijdens de migratie?

Aanbiedingen worden binnen de maand van mei gemigreerd naar het partner centrum. Gedurende deze periode kunt u de Cloud Partner-portal zoals gebruikelijk openen, met uitzonde ring van de tijd die u aanbiedt om te worden gemigreerd.
Hoewel uw aanbiedingen worden gemigreerd, hebben ze de status vergrendeld: verplaatsen naar partner Center, zoals in de volgende scherm afbeelding wordt weer gegeven. Deze migratie periode moet minder dan 24 uur zijn. Gedurende deze periode kunt u geen updates meer aan uw aanbiedingen door voeren. U ontvangt een e-mail melding nadat de migratie van uw aanbiedingen is voltooid.

:::image type="content" source="media/migration-faq/all-offers-1.png" alt-text="Illustreert de status van gemigreerde aanbiedingen.":::

Nadat uw aanbiedingen zijn gemigreerd, zijn ze in de modus alleen-lezen **voor een beperkte periode** in de Cloud Partner-Portal. Hun status wordt weer gegeven "verplaatst naar partner Center" en bevatten een koppeling naar uw aanbieding in Partner Center, zoals wordt weer gegeven in de volgende scherm afbeeldingen. Vanaf dit punt beheert u updates voor al uw aanbiedingen of maakt u alleen nieuwe aanbiedingen via partner centrum.

:::image type="content" source="media/migration-faq/all-offers-2.png" alt-text="Illustreert het bericht dat is opgegeven voor aanbiedingen die zijn gemigreerd naar het partner centrum":::

:::image type="content" source="media/migration-faq/offer-has-moved.png" alt-text="Illustreert de Cloud Partner-portal pagina voor een gemigreerde aanbieding.":::

## <a name="how-will-i-create-new-offers"></a>Hoe kan ik nieuwe aanbiedingen maken?

Vanuit het Cloud Partner-portal wordt u omgeleid om nieuwe aanbiedingen in partner centrum te maken

:::image type="content" source="media/migration-faq/create-new-offer-1.png" alt-text="Illustreert het menu voor het maken van een nieuwe aanbieding in Cloud Partner-portal":::

Nadat u een nieuwe aanbieding hebt geselecteerd, wordt er een bericht weer gegeven, bijvoorbeeld het volgende.

:::image type="content" source="media/migration-faq/create-new-offer-2.png" alt-text="Illustreert het bericht dat wordt ontvangen bij het maken van een nieuwe aanbieding in CPP":::

## <a name="do-i-need-to-create-a-new-account-to-manage-offers-in-partner-center"></a>Moet ik een nieuw account maken voor het beheren van aanbiedingen in Partner Center?

Nee. Uw onderliggende account blijft behouden en u moet het dus al beheren in het partner centrum. Dit betekent dat als u een bestaande partner bent, u uw bestaande Cloud Partner-portal-account referenties kunt gebruiken om u aan te melden bij Partner Center na de migratie. Alleen aanbiedingen en de bijbehorende beheer ervaring worden verplaatst van de Cloud Partner-portal naar het partner centrum. U wordt gevraagd om geen nieuwe accounts te maken omdat uw aanbiedingen niet worden gekoppeld aan het nieuwe account.

## <a name="i-see-a-message-in-the-cloud-partner-portal-to-activate-my-account-what-does-this-mean"></a>Ik zie een bericht in de Cloud Partner-portal om mijn account te activeren. wat betekent dit?

We hebben nog een paar details nodig van u om uw account correct te migreren naar het partner centrum en u in Partner Center uw aanbiedingen te laten beheren nadat de aanbiedings migratie is voltooid. Zie [account migratie van Cloud Partner-Portal naar partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/account-migration-from-cpp-to-pc)voor meer informatie over het activeren van accounts.

De stappen die nodig zijn om de account activering te volt ooien, zijn afhankelijk van de rol van uw account.

| Account functie | Activerings stappen |
|--------------|----------------|
|Eigenaar | Ga in het Cloud Partner-portal naar de profiel pagina van [Publisher](https://cloudpartner.azure.com/#profile) en klik vervolgens op de koppeling in de banner om te activeren. U wordt omgeleid naar het partner centrum om de activering van het account te volt ooien. |
| Inzender | Alleen een gebruiker in het account met een rol eigenaar kan het account activeren. Neem contact op met de eigen aren van uw account om de account activering te volt ooien. De eigen aren van uw account moeten worden vermeld in het banner bericht. |
| | |

## <a name="im-having-trouble-logging-in-to-my-account-and-opening-a-support-ticket"></a>Ik heb problemen met het aanmelden bij mijn account en het openen van een ondersteunings ticket

Als u zich niet kunt aanmelden bij uw account, kunt u een [ondersteunings ticket](https://partner.microsoft.com/support/v2/?stage=1)openen.

## <a name="where-can-i-find-documentation-on-the-new-partner-center-publishing-experience"></a>Waar vind ik documentatie over de nieuwe ervaring voor het publiceren van partner Centers?

Ga naar de [documentatie voor commerciële Marketplace](https://docs.microsoft.com/azure/marketplace/). Vouw vervolgens de **Portal voor commerciële Marketplace uit in het partner centrum**  > **Create a new offer** om de Help-onderwerpen voor het maken van elk type aanbieding te bekijken.

:::image type="content" source="media/migration-faq/marketplace-help-topics.png" alt-text="Illustreert de Help-onderwerpen voor partner centrum":::

### <a name="what-are-the-differences-between-partner-center-and-the-cloud-partner-portal"></a>Wat zijn de verschillen tussen partner centrum en de Cloud Partner-portal?

Mogelijk ziet u de volgende verschillen tussen de Cloud Partner-portal en het partner centrum.

### <a name="modular-publishing-capabilities"></a>Modulaire publicatie mogelijkheden

Het partner centrum biedt een modulaire publicatie optie waarmee u de wijzigingen kunt selecteren die u wilt publiceren in plaats van alle updates altijd tegelijk te publiceren. In de volgende scherm afbeelding ziet u bijvoorbeeld dat de wijzigingen in de **Eigenschappen** en de **aanbieding**worden weer gegeven.

:::image type="content" source="media/migration-faq/review-and-publish-migration.png" alt-text="Illustreert de pagina voor beeld en publiceren":::

De updates die u niet publiceert, worden opgeslagen als concept. Ga door met de preview-versie van uw aanbieding om uw aanbieding te verifiëren voordat u deze beschikbaar maakt voor het publiek.

### <a name="rich-text-format"></a>RTF-indeling

Verbeter uw aanbieding en plan de beschrijving met de RTF-editor op de pagina aanbieding en aanbieding plannen.

:::image type="content" source="media/migration-faq/rich-text-editor-migration.png" alt-text="Illustreert de RTF-editor":::

### <a name="enhanced-preview-options"></a>Verbeterde preview-opties

Het partner centrum bevat een [vergelijkings functie](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#compare-changes-to-marketplace-offers) met verbeterde filter opties. Dit biedt u de mogelijkheid om te vergelijken met de preview-versie en de live versies van de aanbieding.

:::image type="content" source="media/migration-faq/enhanced-preview.png" alt-text="Illustreert de functie Compare":::

### <a name="branding-and-navigation-changes"></a>Huismerk en navigatie wijzigingen

U ziet enkele huismerk wijzigingen. "Sku's" worden bijvoorbeeld aangemerkt als "plannen" in het partner centrum.

:::image type="content" source="media/migration-faq/plan-overview-migration.png" alt-text="Illustreert het plan overzicht.":::

Daarnaast worden de informatie die u hebt gebruikt om de pagina's met details van de **Marketplace** of S**Torefront** (Consulting Service, Power bi app) in de Cloud Partner-Portal, verzameld op de **aanbiedings** pagina in partner centrum.

:::image type="content" source="media/migration-faq/offer-listing-migration.png" alt-text="Illustreert de pagina met de aanbieding.":::

De informatie die u hebt gebruikt om Sku's te bieden op één pagina in de Cloud Partner-portal, kan nu worden verzameld op verschillende pagina's in het partner centrum:

* Pagina instellen plannen
* Aanbiedings pagina plannen
* Pagina Beschik baarheid plannen
* Plan de pagina technische configuratie, zoals wordt weer gegeven in deze scherm opname.

:::image type="content" source="media/migration-faq/tech-config-migration.png" alt-text="Illustreert de pagina technische configuratie plannen.":::

Uw aanbiedings-ID wordt nu weer gegeven in de linkernavigatiebalk van de aanbieding.

:::image type="content" source="media/migration-faq/offer-id-offer-overview.png" alt-text="Illustreert het navigatie menu links met de aanbiedings-ID.":::

### <a name="stop-selling-an-offer"></a>Stoppen met verkopen van een aanbieding

U kunt aanvragen om het [verkopen van een aanbieding](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan) op Marketplace rechtstreeks vanuit de portal van partner centrum te stoppen. De optie is beschikbaar op de **overzichts** pagina van de aanbieding voor uw aanbieding.

:::image type="content" source="media/migration-faq/stop-selling-migration.png" alt-text="Illustreert de pagina overzicht van aanbieding met de optie voor het stoppen van verkopen.":::

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Welke pagina's in het partner centrum komen overeen met de pagina's die ik heb gebruikt in de Cloud Partner-portal?

In de volgende tabel ziet u de bijbehorende koppelingen tussen de twee portals.

| Pagina | Koppeling Cloud Partner-portal | Koppeling partner centrum |
|------|---------------------------|---------------------|
| **Pagina voor Alle aanbiedingen** | https://cloudpartner.azure.com/#alloffers | https://partner.microsoft.com/dashboard/commercial-marketplace/overview |
| **Pagina voor Alle uitgevers** | https://cloudpartner.azure.com/#publishers | https://partner.microsoft.com/dashboard/account/v3/publishers/list |
| **Publisher-profiel** | https://cloudpartner.azure.com/#profile | https://partner.microsoft.com/dashboard/account/management |
| **Pagina voor Gebruikers** | https://cloudpartner.azure.com/#users | https://partner.microsoft.com/dashboard/account/usermanagement |
| **Pagina geschiedenis** | https://cloudpartner.azure.com/#history | De geschiedenis functie wordt nog niet ondersteund in partner centrum. |
| **Insights-dash board** | https://cloudpartner.azure.com/#insights | https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary |
| **Uitbetalings rapport** | https://cloudpartner.azure.com/#insights/payout | https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| | |

## <a name="will-the-cloud-partner-portal-rest-apis-be-supported-post-migration"></a>Worden de Cloud Partner-portal REST Api's ondersteund na de migratie?

De Cloud Partner-portal-Api's zijn geïntegreerd met partner centrum en blijven werken nadat uw aanbiedingen zijn gemigreerd naar het partner centrum. De integratie introduceert kleine wijzigingen. Bekijk de wijzigingen in de tabel hieronder om te controleren of uw code blijft werken na de migratie naar het partner centrum.

| **API** | **Beschrijving wijzigen** | **Impact** |
| ------- | ---------------------- | ---------- |
| POST publiceren, GoLive, annuleren | Voor gemigreerde aanbiedingen heeft de reactie header een andere indeling, maar blijft op dezelfde manier werken, waarbij een relatief pad wordt opgegeven om de bewerkings status op te halen. | Bij het verzenden van een van de bijbehorende POST-aanvragen voor een aanbieding heeft de locatie header een van de twee notaties, afhankelijk van de migratie status van de aanbieding:<ul><li>Niet-gemigreerde aanbiedingen<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Gemigreerde aanbiedingen<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| GET-bewerking | Voor aanbiedingen waarvoor eerder het veld meldings-e in het antwoord wordt ondersteund, wordt dit veld afgeschaft en wordt het niet meer geretourneerd voor gemigreerde aanbiedingen. | Voor gemigreerde aanbiedingen worden er geen meldingen meer verzonden naar de lijst met e-mail berichten die in de aanvragen zijn opgegeven. In plaats daarvan wordt de API-service uitgelijnd met het e-mail proces voor meldingen in het partner centrum om e-mail berichten te verzenden. In het bijzonder worden meldingen verzonden naar het e-mail adres dat is ingesteld in het gedeelte contact gegevens van de verkoper van uw account instellingen in partner centrum om u te informeren over de voortgang van de bewerking.<br><br>Bekijk het e-mail adres dat is ingesteld in het gedeelte contact gegevens van de verkoper in de [account instellingen](https://partner.microsoft.com/dashboard/account/management) in Partner Center om te controleren of het juiste e-mail bericht is ontvangen voor meldingen.  |
| | | |
