---
title: Klantleads configureren | Azure Marketplace
description: Klantleads configureren in commerciële marktplaats.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: a1ec89dfd2dda91a10f2cc00b6ca4d9d7abbf032
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731141"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>Leads van klanten van uw marketplace-aanbieding

Leads zijn klanten die geïnteresseerd zijn in of het implementeren van uw aanbiedingen via de [Azure Marketplace](https://azuremarketplace.microsoft.com) of van [AppSource.](https://appsource.microsoft.com) U ontvangt klantleads zodra uw aanbieding op de marktplaats is gepubliceerd. In dit artikel wordt uitgelegd:

* Hoe uw marktplaatsaanbod leads van klanten genereert, zodat u geen zakelijke kansen mist. 
* Verbind uw CRM met uw aanbod, zodat u uw leads op één centrale locatie beheren.
* Begrijp de leadgegevens die we u sturen, zodat u klanten opvolgen die contact met u hebben opgenomen.

## <a name="generate-customer-leads"></a>Klantleads genereren

Hier zijn plaatsen waar een lead wordt gegenereerd:

1. Wanneer een klant instemt met het delen van hun informatie na het selecteren van "Neem contact met mij op" van de marktplaats. Deze lead is een **eerste interesselead,** waarbij we informatie delen over de klant die interesse heeft getoond in het verkrijgen van uw product. De lead is de bovenkant van de acquisitietrechter.

      ![Dynamics 365 Neem contact met mij op](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

2. Wanneer een klant 'Nu krijgen' of 'Nu maken' (in de [Azure-portal)](https://portal.azure.com/)selecteert om uw aanbieding te krijgen, is deze lead een **actieve lead,** waarbij we informatie delen over een klant die is begonnen met het implementeren van uw product.

    ![SQL Get it Now](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Windows Server maken](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

3. Een klant neemt een "Proefrit" of start een "Gratis proefversie" van uw aanbieding. Testdrives of gratis proefversies zijn versnelde mogelijkheden voor u om uw bedrijf direct te delen met potentiële klanten zonder enige toetredingsdrempels.

    ![Dynamics 365-testrit](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Dynamics 365-testrit](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Verbinding maken met uw CRM-systeem

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Inzicht in leadgegevens

Elke lead die u ontvangt tijdens het klantacquisitieproces heeft gegevens op specifieke gebieden. Het eerste veld waar naar `LeadSource` moet worden gezocht is het veld, dat deze indeling volgt: **Source-Action** | **Offer**.

**Bronnen:** De waarde voor dit veld wordt ingevuld op basis van de marktdie de lead heeft gegenereerd. Mogelijke waarden `"AzureMarketplace"` `"AzurePortal"`zijn `"AppSource (SPZA)"`, , en .

**Acties:** De waarde voor dit veld wordt ingevuld op basis van de actie die de klant heeft uitgevoerd in de marketplace, waardoor de lead is gegenereerd. 

Mogelijke waarden zijn:

- "INS" -- Installatie. Deze actie is op Azure Marketplace of AppSource wanneer een klant uw product koopt.
- "PLT" - staat voor Partner Led Trial. Deze actie is op AppSource wanneer een klant de optie Contact met mij gebruikt.
- "DNC" -- Neem geen contact op. Deze actie staat op AppSource wanneer een partner die is crossop uw app-pagina wordt vermeld, wordt verzocht gecontacteerd te worden. We delen de heads-up dat deze klant is vermeld in uw app, maar ze hoeven niet te worden gecontacteerd.
- 'Maken' - Deze actie bevindt zich alleen in de Azure-portal en wordt gegenereerd wanneer een klant uw aanbieding naar zijn account koopt.
- "StartTestDrive" - Deze actie is alleen voor teststations en wordt gegenereerd wanneer een klant zijn testrit start.

**Aanbiedingen:** Je hebt mogelijk meerdere aanbiedingen op de markt. De waarde voor dit veld wordt ingevuld op basis van de aanbieding die de lead heeft gegenereerd. De uitgevers-id en aanbiedings-id worden zowel in dit veld verzonden als zijn waarden die u hebt opgegeven toen u de aanbieding op de marktplaats hebt gepubliceerd.

In de volgende voorbeelden worden voorbeeldwaarden in de verwachte indeling weergegeven: `publisherid.offerid` 

1. `checkpoint.check-point-r77-10sg-byol`
1. `bitnami.openedxcypress`
1. `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-info"></a>Klantgegevens

De informatie van de klant wordt via meerdere velden verzonden. In het volgende voorbeeld worden de klantgegevens weergegeven die in een lead zijn opgenomen.

- Voornaam: John
- Achternaam: Smith
- E-mail:\@jsmith microsoft.com
- Telefoon: 1234567890
- Land: VS
- Bedrijf: Microsoft
- Benaming: CTO

>[!Note]
>Niet alle gegevens in het vorige voorbeeld zijn altijd beschikbaar voor elke lead. Omdat u leads krijgt van meerdere stappen zoals vermeld in de sectie Klantleads, u de leads het beste verwerken door de records te dedupliceren en de follow-ups te personaliseren. Op deze manier krijgt elke klant een passend bericht en creëer je een unieke relatie.

## <a name="best-practices-for-lead-management"></a>Best practices voor leadmanagement

1. *Proces* - Definieer een duidelijk verkoopproces, met mijlpalen, KPI's en duidelijke teameigendom.
2. *Kwalificatie* - Definieer voorwaarden, die aangeven of een lead volledig gekwalificeerd is. Zorg ervoor dat verkoop- of marketingvertegenwoordigers leads zorgvuldig kwalificeren voordat ze het volledige verkoopproces doorlopen.
3. *Follow-up* - Vergeet niet om follow-up, verwachten dat de typische transactie te vereisen 5 tot 12 follow-up gesprekken
4. *Nurture* - Koester je leads om je op weg te krijgen naar een hogere winstmarge.

## <a name="leads-frequently-asked-questions"></a>Veelgestelde vragen leiden

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Waar kan ik hulp krijgen bij het instellen van mijn hoofdbestemming?

U [hier](#connect-to-your-crm-system) documentatie vinden of https://partner.microsoft.com/support/v2/?stage=1 een ondersteuningsticket indienen via **selecteer vervolgens 'aanbiedingscreatie'** → **uw type aanbieding** → **'leadmanagementconfiguratie'.**

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-on-the-marketplace"></a>Moet ik een leadbestemming configureren om een aanbieding op de marktplaats te publiceren?

Het antwoord is afhankelijk van het type aanbod dat u publiceert. SaaS en Dynamics 365 for Customer Engagement biedt om als 'Contact Me' te vermelden, alle Dynamics 365 for Operations-aanbiedingen, alle Dynamics 365 Business Central-aanbiedingen en alle Consulting Service-aanbiedingen vereisen een verbinding met een hoofdbestemming. Als uw aanbiedingstype niet is vermeld, is dit niet vereist. Het wordt echter aanbevolen om een hoofdbestemming te configureren, zodat u geen zakelijke kansen mist.

### <a name="how-can-i-find-the-test-lead"></a>Hoe kan ik de testlead vinden?

Zoek `"MSFT_TEST"` naar in uw hoofdbestemming, hier is een voorbeeld testlead van Microsoft:

```
company = MSFT_TEST_636573304831318844
country = US
description = MSFT_TEST_636573304831318844
email = MSFT_TEST_636573304831318844@test.com
encoding = UTF-8
encoding = UTF-8
first_name = MSFT_TEST_636573304831318844
last_name = MSFT_TEST_636573304831318844
lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|<Offer Name>
oid = 00Do0000000ZHog
phone = 1234567890
title = MSFT_TEST_636573304831318844
```

### <a name="i-have-a-live-offer-but-im-not-seeing-any-leads"></a>Ik heb een live aanbod, maar ik zie geen aanwijzingen?

Zorg ervoor dat uw verbinding met de hoofdbestemming geldig is. We sturen je een testlead nadat je op je aanbieding in partnercentrum hebt gepubliceerd. Als u de testlead ziet, is de verbinding geldig. U uw leadverbinding ook testen door te proberen de aanbiedingsvoorbeeld te verkrijgen tijdens de voorbeeldstap door te klikken op 'nu kopen', 'contact met mij' of 'gratis proefversie' op de vermelding op de marktplaats.

Zorg er ook voor dat u op zoek bent naar de juiste gegevens. De inhoud in de sectie [Leadgegevens begrijpen](#understand-lead-data) van dit document beschrijft de leadgegevens die we naar uw hoofdbestemming verzenden.

### <a name="i-have-configured-azure-blob-as-my-lead-destination-why-dont-i-see-the-lead"></a>Ik heb Azure BLOB geconfigureerd als mijn hoofdbestemming, waarom zie ik de lead niet?

De Azure Blob-leadbestemming wordt niet langer ondersteund, zodat u klantleads mist die door uw aanbieding worden gegenereerd. Schakel over naar een van de andere [opties voor hoofdbestemming](./commercial-marketplace-get-customer-leads.md). 

### <a name="i-received-an-email-from-marketplace-why-cant-i-find-the-lead-in-my-crm"></a>Ik heb een e-mail ontvangen van Marketplace, waarom kan ik de lead niet vinden in mijn CRM?

Het is mogelijk dat het e-maildomein van de eindgebruiker van .edu is. Om privacyredenen geven we geen persoonlijke identificeerbare informatie van .edu-domein door. Stuur een supportticket in via https://partner.microsoft.com/support/v2/?stage=1.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Ik heb Azure Table geconfigureerd als mijn hoofdbestemming, hoe kan ik de leads bekijken?

U hebt toegang tot de leadgegevens die zijn opgeslagen in de Azure-tabel vanuit de Azure-portal of u [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gratis downloaden en installeren om de tabellengegevens van uw Azure-opslagaccount weer te geven.

### <a name="i-have-configured-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-lead-is-sent-by-marketplace"></a>Ik heb Azure Table geconfigureerd als mijn hoofdbestemming, kan ik een melding krijgen wanneer een nieuwe lead wordt verzonden door Marketplace?

Ja, volg de instructies voor het instellen van een Microsoft-stroom die een e-mail verzendt als [hier](./commercial-marketplace-lead-management-instructions-azure-table.md)een lead wordt toegevoegd aan de Azure-tabel in de documentatie.

### <a name="i-have-configured-salesforce-as-my-lead-destination-why-cant-i-find-the-leads"></a>Ik heb Salesforce geconfigureerd als mijn hoofdbestemming, waarom kan ik de leads niet vinden?

Controleer of het formulier 'web to lead' een verplicht veld is op basis van een picklist. Zo ja, schakel het veld over naar een niet-verplicht tekstveld.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Er was een probleem met mijn hoofdbestemming, en ik miste enkele aanwijzingen. Kan ik ze in een e-mail naar mij laten sturen?

Vanwege het beleid voor identificeerbare gegevens van privé-identificeerbaar informatie kunnen we geen leadgegevens delen via onbeveiligde e-mail.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Ik heb Azure Table geconfigureerd als mijn hoofdbestemming, hoeveel kost dit?

Lead gen-gegevens zijn laag (<1 GB voor bijna alle uitgevers). De kosten zijn afhankelijk van het aantal ontvangen leads, als 1.000 leads worden ontvangen in een maand, kost het ongeveer 50 cent. Zie [opslagprijzen](https://azure.microsoft.com/pricing/details/storage/)voor meer informatie over opslagprijzen.

Als uw vraag nog steeds niet wordt beantwoord, neemt u contact op met ondersteuning via aka.ms/marketplacepublishersupport en selecteert u **'aanmaken aanbieden'** → **uw type aanbieding** → **'leadmanagementconfiguratie'.** 

### <a name="i-am-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-who-to-receive-these-emails"></a>Ik ontvang e-mailmeldingen wanneer nieuwe klantleads worden ontvangen. Hoe kan ik configureren wie deze e-mails kan ontvangen?

Krijg toegang tot uw aanbieding in partnercentrum en navigeer naar de pagina **Installatie van aanbiedingen** -> Lead **Management** -> **Bewerken**. Werk de e-mailadressen bij onder het **e-mailadres contact.**

## <a name="next-steps"></a>Volgende stappen

Zodra de technische set-up is op zijn plaats, moet u deze leads op te nemen in uw huidige verkoop & marketing strategie en operationele processen. We zijn geïnteresseerd in een beter begrip van uw totale verkoopproces en willen nauw met u samenwerken bij het leveren van hoogwaardige leads en voldoende gegevens om u succesvol te maken. We zijn blij met uw feedback over hoe we de leads die we u sturen kunnen optimaliseren en verbeteren met aanvullende gegevens om deze klanten succesvol te maken. Laat het ons weten als je geïnteresseerd bent in [het geven van feedback](mailto:AzureMarketOnboard@microsoft.com) en suggesties om je verkoopteam succesvoller te laten zijn met Marketplace Leads.
