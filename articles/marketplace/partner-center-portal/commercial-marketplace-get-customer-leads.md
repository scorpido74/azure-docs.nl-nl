---
title: Leads van klanten configureren | Azure Marketplace
description: Klanten leads in commerciële Marketplace configureren.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: 31dcc8c1e35b627b231dbe2a62998c8514d05a20
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69902648"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>Leads van klanten van uw Marketplace-aanbieding

Leads zijn klanten die geïnteresseerd zijn in of implementeren uw aanbiedingen vanuit de [Azure Marketplace](https://azuremarketplace.microsoft.com) of via [AppSource](https://appsource.microsoft.com). Zodra uw aanbieding naar de Marketplace is gepubliceerd, ontvangt u leads van klanten. In dit artikel wordt uitgelegd:

* Hoe uw Marketplace-aanbieding klanten leads genereert, zodat u geen zakelijke kansen meer mist. 
* Verbind uw CRM met uw aanbieding, zodat u uw leads kunt beheren op één centrale locatie.
* Meer informatie over de lead gegevens die we u sturen, zodat u klanten kunt opvolgen die aan u zijn terechtgekomen.

## <a name="generate-customer-leads"></a>Leads van klanten genereren

Hier vindt u plaatsen waar een lead wordt gegenereerd:

1. Wanneer een klant toestemming heeft gegeven om hun gegevens te delen nadat ik contact met mij op de Marketplace heb geselecteerd. Deze lead is een **eerste geïnteresseerde** lead, waar we informatie delen over de klant die geïnteresseerd is in het ophalen van uw product. De lead is de bovenkant van de verwervings trechter.

      ![Dynamics 365 contact met mij opnemen](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

2. Wanneer een klant ' nu downloaden ' of ' maken ' (in de [Azure Portal](https://portal.azure.com/)) selecteert om uw aanbieding te ontvangen, is deze lead een **actieve lead**, waar we informatie delen over een klant die de implementatie van uw product heeft gestart.

    ![SQL nu ophalen](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Windows Server maken](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

3. Een klant krijgt een ' test station ' of start een ' gratis proef versie ' van uw aanbieding. Test stations of gratis proef versies zijn versnelde kansen om uw bedrijf direct te delen met potentiële klanten zonder enige obstakels in te brengen.

    ![Dynamics 365-test station](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Dynamics 365-test station](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Verbinding maken met uw CRM-systeem

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Informatie over leads

Elke lead die u ontvangt tijdens het klant verwervings proces heeft gegevens in specifieke velden. Het eerste veld dat moet worden gezocht, is `LeadSource` het veld met de volgende notatie: | **Aanbieding**voor de bron actie.

**Bronnen**: De waarde voor dit veld wordt ingevuld op basis van de Marketplace die de lead heeft gegenereerd. Mogelijke waarden zijn `"AzureMarketplace"`, `"AzurePortal"`en `"AppSource (SPZA)"`.

**Acties**: De waarde voor dit veld wordt ingevuld op basis van de actie die de klant heeft ondernomen op de Marketplace, waardoor de lead werd gegenereerd. 

Mogelijke waarden zijn:

- ' INS '--installatie. Deze actie bevindt zich op Azure Marketplace of AppSource wanneer een klant uw product koopt.
- "PLT": staat voor een partner-LED-proef versie. Deze actie is op AppSource wanneer een klant de optie contact opnemen gebruikt.
- "DNC"--geen contact opnemen. Deze actie bevindt zich op AppSource wanneer een partner die op de pagina van uw app staat, wordt gevraagd om contact met u op te nemen. De koppen worden gedeeld die deze klant heeft door gegeven aan uw app, maar er hoeven geen contact mee te worden gemaakt.
- ' Maken ': deze actie bevindt zich alleen in de Azure Portal en wordt gegenereerd wanneer een klant uw aanbieding aan hun account koopt.
- "StartTestDrive": deze actie geldt alleen voor test stations en wordt gegenereerd wanneer een klant hun test drive start.

**Aanbiedingen**: Mogelijk hebt u meerdere aanbiedingen op Marketplace. De waarde voor dit veld wordt ingevuld op basis van de aanbieding die de lead heeft gegenereerd. De uitgevers-ID en aanbiedings-ID worden beide in dit veld verzonden en zijn de waarden die u hebt ingevoerd tijdens het publiceren van de aanbieding naar de Marketplace.

In de volgende voor beelden worden voorbeeld waarden in de `publisherid.offerid`verwachte indeling weer gegeven: 

1. `checkpoint.check-point-r77-10sg-byol`
1. `bitnami.openedxcypress`
1. `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-info"></a>Klant gegevens

De gegevens van de klant worden verzonden via meerdere velden. In het volgende voor beeld ziet u de klant gegevens die zijn opgenomen in een lead.

- Voor Letterlijk
- Naam Smith
- E-mail: jsmith\@Microsoft.com
- Telefoons 1234567890
- Land/regio: VS
- Bedrijfs Microsoft
- Hoofd CTO

>[!Note]
>Niet alle gegevens in het vorige voor beeld zijn altijd beschikbaar voor elke lead. Omdat u leads van meerdere stappen krijgt, zoals vermeld in de sectie leads van klanten, is de beste manier om de leads af te handelen en de opvolging van de records te personaliseren. Op deze manier krijgt elke klant een passend bericht en maakt u een unieke relatie.

## <a name="best-practices-for-lead-management"></a>Aanbevolen procedures voor het beheer van leads

1. *Proces* : Definieer een duidelijk verkoop proces met mijl palen, kpi's en het wissen van de team eigendom.
2. *Kwalificatie* : Definieer vereisten, die aangeven of een lead volledig is gekwalificeerd. Zorg ervoor dat verkoop-of marketing vertegenwoordigers leads goed kwalificeren voordat ze via het volledige verkoop proces door lopen.
3. *Follow-up* : Vergeet niet om te volgen, verwacht dat de gebruikelijke trans actie 5 tot 12 opvolgings oproepen vereist
4. *Nurture* -nurture uw leads om u op weg te helpen met een hogere winst marge.

## <a name="leads-frequently-asked-questions"></a>Veelgestelde vragen over leads

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Waar kan ik hulp krijgen bij het instellen van mijn lead bestemming?

U kunt [hier](#connect-to-your-crm-system) documentatie vinden of een ondersteunings ticket indienen via aka.MS/marketplacepublishersupport. Selecteer vervolgens **' aanbieding maken '** → **uw type aanbod** → **' configuratie van lead beheer '.**

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-on-the-marketplace"></a>Moet ik een lead bestemming configureren om een aanbieding op Marketplace te publiceren?

Het antwoord is afhankelijk van het type aanbieding dat u publiceert. SaaS en Dynamics 365 voor klanten engagement aanbiedingen aanbieden als ' contact persoon ', alle Dynamics 365 voor de operationele aanbiedingen, alle Dynamics 365 Business Central-aanbiedingen en alle advies service-aanbiedingen vereisen een verbinding met een lead bestemming. Als uw aanbiedings type niet wordt vermeld, is dit niet vereist. U kunt echter het beste een lead bestemming configureren zodat u geen zakelijke kansen meer hebt.

### <a name="how-can-i-find-the-test-lead"></a>Hoe kan ik de test lead vinden?

`“MSFT_TEST”` Zoek naar de doel locatie van de lead, hier volgt een voor beeld van een test lead van micro soft:

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

### <a name="i-have-a-live-offer-but-im-not-seeing-any-leads"></a>Ik heb een live aanbieding, maar ik zie geen leads?

Zorg ervoor dat de verbinding met de doel locatie van de lead geldig is. We sturen u een test lead nadat u op uw aanbieding hebt geklikt in het partner centrum. Als u de test leider ziet, is de verbinding geldig. U kunt ook uw lead verbinding testen door te proberen de preview-versie van de aanbieding te verkrijgen tijdens de preview-stap door te klikken op nu downloaden, contact opnemen of gratis proef versie van de aanbieding in de Marketplace.

Zorg er ook voor dat u de juiste gegevens zoekt. De inhoud van de sectie [lead gegevens begrijpen](#understand-lead-data) van dit document beschrijft de lead gegevens die we verzenden naar uw lead bestemming.

### <a name="i-have-configured-azure-blob-as-my-lead-destination-why-dont-i-see-the-lead"></a>Ik heb Azure BLOB geconfigureerd als mijn lead doel, waarom zie ik de lead niet?

De Azure Blob-doel locatie wordt niet meer ondersteund, zodat er geen potentiële klant leads ontbreken die door uw aanbieding zijn gegenereerd. Schakel over naar een van de andere opties voor de [doel bestemming](./commercial-marketplace-get-customer-leads.md). 

### <a name="i-received-an-email-from-marketplace-why-cant-i-find-the-lead-in-my-crm"></a>Ik heb een e-mail ontvangen van Marketplace, waarom kan ik de lead niet vinden in mijn CRM?

Het is mogelijk dat het e-mail domein van de eind gebruiker afkomstig is van. edu. Uit het oogpunt van privacy geven we geen PII-gegevens door van het domein. edu. Dien een ondersteunings ticket in via aka.ms/marketplacepublishersupport.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Ik heb Azure Table geconfigureerd als mijn lead-bestemming, hoe kan ik de leads bekijken?

U kunt toegang krijgen tot de lead gegevens die zijn opgeslagen in de Azure-tabel vanuit Azure Portal, of u kunt [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gratis downloaden en installeren om de tabel gegevens van uw Azure Storage-account weer te geven.

### <a name="i-have-configured-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-lead-is-sent-by-marketplace"></a>Ik heb Azure Table als mijn lead bestemming geconfigureerd, kan ik een melding ontvangen wanneer een nieuwe lead door Marketplace wordt verzonden?

Ja, volg de instructies voor het instellen van een micro soft flow waarmee een e-mail bericht wordt verzonden als een lead wordt toegevoegd aan [](./commercial-marketplace-lead-management-instructions-azure-table.md)de Azure-tabel in de documentatie.

### <a name="i-have-configured-salesforce-as-my-lead-destination-why-cant-i-find-the-leads"></a>Ik heb Sales Force geconfigureerd als mijn lead-doel waarom kan ik de leads niet vinden?

Controleer of het formulier Web to lead een verplicht veld is op basis van een selectie lijst. Zo ja, schakelt u over naar het veld naar een niet-verplicht tekst veld.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Er is een probleem met mijn lead doel en er zijn enkele leads gemist. Kan ik ze in een e-mail bericht naar mij verzenden?

Als gevolg van PII-beleid (persoonlijke Identificeer bare informatie) kunnen we geen lead gegevens delen via onbeveiligde e-mail berichten.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Ik heb de Azure-tabel als mijn lead bestemming geconfigureerd, wat kost het dan kosten?

De gegevens van de lead zijn laag (< 1 GB voor bijna alle uitgevers). De kosten zijn afhankelijk van het aantal ontvangen leads, als 1.000-leads per maand worden ontvangen, kosten ongeveer 50 cent. Zie [prijzen voor opslag](https://azure.microsoft.com/pricing/details/storage/)voor meer informatie over prijzen voor opslag.

Als uw vraag nog steeds niet wordt beantwoord, neemt u contact op met de ondersteuning via aka.ms/marketplacepublishersupport en selecteert u vervolgens **aanbieding maken** → **uw type aanbieding** → **' configuratie van lead beheer '.** 

## <a name="next-steps"></a>Volgende stappen

Zodra de technische configuratie is geïmplementeerd, moet u deze leads opnemen in uw huidige verkoop & marketing strategie en operationele processen. We zijn geïnteresseerd in een beter inzicht in uw algehele verkoop proces en willen nauw keurig samen werken op het leveren van leads van hoge kwaliteit en voldoende gegevens om u te laten slagen. We stellen uw feedback op de manier waarop we u kunnen helpen bij het optimaliseren en verbeteren van de leads die wij u met aanvullende gegevens sturen om deze klanten succesvol te maken. Laat het ons weten als u geïnteresseerd bent in [feedback](mailto:AzureMarketOnboard@microsoft.com) en suggesties zodat uw verkoop team succes voller kan maken met leads in de Marketplace.
