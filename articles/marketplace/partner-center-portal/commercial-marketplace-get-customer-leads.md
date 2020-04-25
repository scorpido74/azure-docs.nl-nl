---
title: Leads van uw micro soft Commercial Marketplace-aanbieding configureren
description: Klanten leads configureren in micro soft Commercial Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 54c67656d7847b44c8fc83b33a4e03be3838cf76
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82131102"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>Leads van klanten van uw marketplace-aanbieding

Leads zijn klanten die geïnteresseerd zijn in of implementeren van uw aanbiedingen van micro soft [AppSource](https://appsource.microsoft.com) en [Azure Marketplace](https://azuremarketplace.microsoft.com). U ontvangt leads van klanten nadat uw aanbieding naar de Marketplace is gepubliceerd. In dit artikel wordt uitgelegd:

* Hoe uw Marketplace-aanbieding klanten leads genereert om ervoor te zorgen dat u geen zakelijke kansen meer mist. 
* Hoe koppelt u uw Customer Relationship Management-systeem (CRM) aan uw aanbieding zodat u uw leads kunt beheren op één centrale locatie.
* De lead gegevens die we u verzenden zodat u klanten kunt opvolgen die aan u zijn terechtgekomen.

## <a name="generate-customer-leads"></a>Leads van klanten genereren

Hier vindt u plaatsen waar een lead wordt gegenereerd:

- Een klant heeft toestemming gegeven om hun gegevens te delen nadat ze **contact met mij** hebben geselecteerd vanuit de commerciële Marketplace. Deze potentiële klant is een *eerste geïnteresseerde* lead. We delen informatie met u over de klant die geïnteresseerd is in het ophalen van uw product. De lead is de bovenkant van de verwervings trechter.

    ![Dynamics 365 contact met mij opnemen](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- Een klant selecteert de optie **nu downloaden** (of **selecteren in** het [Azure Portal](https://portal.azure.com/)) om uw aanbieding te ontvangen. Deze lead is een *actieve* lead. We delen informatie met u over de klant die is begonnen met het implementeren van uw product.

    ![SQL-knop nu ophalen](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Knop Windows Server maken](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- Een klant selecteert **test drive** of **gratis proef versie** om uw aanbieding uit te proberen. Test stations of gratis proef versies zijn versnelde kansen om uw bedrijf direct te delen met potentiële klanten zonder enige obstakels in te brengen.

    ![Knop Dynamics 365-test station](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Knop Dynamics 365 gratis proef versie](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Verbinding maken met uw CRM-systeem

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Informatie over leads

Elke lead die u ontvangt tijdens het klant verwervings proces heeft gegevens in specifieke velden. Het eerste veld dat moet worden uitgecheckt `LeadSource` , is het veld, dat de volgende indeling heeft: de**aanbieding**voor de **bron actie** | .

**Bronnen**: de waarde voor dit veld wordt ingevuld op basis van de Marketplace die de lead heeft gegenereerd. Mogelijke waarden zijn `"AzureMarketplace"`, `"AzurePortal"`en `"AppSource (SPZA)"`.

**Acties**: de waarde voor dit veld wordt ingevuld op basis van de actie die de klant heeft ondernomen op de Marketplace die de lead heeft gegenereerd.

Mogelijke waarden zijn:

- **' Ins '**: staat voor *installatie*. Deze actie bevindt zich in azure Marketplace of AppSource wanneer een klant uw product koopt.
- **"PLT"**: staat voor een door de *partner geleide proef versie*. Deze actie bevindt zich in AppSource wanneer een klant de optie **contact opnemen** selecteert.
- **"DNC"**: staat voor *geen contact persoon*. Deze actie bevindt zich in AppSource wanneer een partner die op uw app-pagina is genoteerd, wordt gevraagd om contact met u op te nemen. Er wordt een melding weer gegeven dat deze klant door de gebruiker is opgenomen in uw app, maar waarmee geen contact moet worden opgenomen.
- **' Maken '**: deze actie bevindt zich alleen in de Azure Portal en wordt gegenereerd wanneer een klant uw aanbieding aan hun account koopt.
- **"StartTestDrive"**: deze actie is alleen voor de optie **test drive** en wordt gegenereerd wanneer een klant hun test drive start.

**Aanbiedingen**: mogelijk hebt u meerdere aanbiedingen in de commerciële Marketplace. De waarde voor dit veld wordt ingevuld op basis van de aanbieding die de lead heeft gegenereerd. De uitgevers-ID en aanbiedings-ID worden beide in dit veld verzonden en zijn de waarden die u hebt ingevoerd tijdens het publiceren van de aanbieding naar de Marketplace.

In de volgende voor beelden worden waarden in de `publisherid.offerid`verwachte indeling weer gegeven: 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>Klant gegevens

De gegevens van de klant worden verzonden via meerdere velden. In het volgende voor beeld ziet u de klant gegevens die zijn opgenomen in een lead:

- Voor naam: John
- LastName: Smit
- E-mail: jsmith\@Microsoft.com
- Telefoon: 1234567890
- Land: VS
- Bedrijf: micro soft
- Titel: CTO

>[!NOTE]
>Niet alle gegevens in het vorige voor beeld zijn altijd beschikbaar voor elke lead. Omdat u leads van meerdere stappen krijgt, zoals vermeld in de sectie ' leads van klanten genereren ', kunt u de leads het beste afhandelen door de records te verdubbelen en de vervolg-ups aan te passen. Op deze manier krijgt elke klant een passend bericht en maakt u een unieke relatie.

## <a name="best-practices-for-lead-management"></a>Aanbevolen procedures voor het beheer van leads

- **Proces**: Definieer een duidelijk verkoop proces met mijl palen, analyses en duidelijke team eigendom.
- **Kwalificatie**: Definieer vereisten, die aangeven of een lead volledig is gekwalificeerd. Zorg ervoor dat verkoop-of marketing vertegenwoordigers leads goed kwalificeren voordat ze via het volledige verkoop proces door lopen.
- **Follow-up**: Vergeet niet om te volgen. Verwacht dat de gebruikelijke trans actie 5 tot 12 opvolgings aanroepen vereist.
- **Nurture**: nurture uw leads om u op weg te helpen met een hogere winst marge.

## <a name="leads-frequently-asked-questions"></a>Veelgestelde vragen over leads

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Waar kan ik hulp krijgen bij het instellen van mijn lead bestemming?

Volg de stappen in de sectie [verbinding maken met uw CRM-systeem](#connect-to-your-crm-system)of dien een ondersteunings ticket in via de [Help en ondersteuning van het partner centrum](https://partner.microsoft.com/support/v2/?stage=1). Selecteer vervolgens **aanbieding maken** > **uw type** > **configuratie van lead beheer**.

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>Moet ik een lead bestemming configureren om een aanbieding in de commerciële Marketplace te publiceren?

Het antwoord is afhankelijk van het type aanbieding dat u publiceert. Software as a Service (SaaS) en Dynamics 365 Customer engagement gebruiken **contact met mij** om een lijst te maken met alle Dynamics 365 voor Financiën en operationele aanbiedingen, alle Dynamics 365 Business Central-aanbiedingen en alle advies service aanbiedingen. Als gevolg hiervan is een verbinding met een lead bestemming vereist. Als uw aanbiedings type niet wordt vermeld, is er geen verbinding met een doel voor de lead vereist. We raden u aan om een doel voor de lead te configureren zodat u geen zakelijke kansen meer hebt.

### <a name="how-can-i-find-the-test-lead"></a>Hoe kan ik de test lead vinden?

Zoek naar `"MSFT_TEST"` de doel locatie van de lead. Hier volgt een voor beeld van een test lead van micro soft:

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

### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>Ik heb een live aanbieding, maar waarom krijg ik geen leads te zien?

Zorg ervoor dat de verbinding met de doel locatie van de lead geldig is. U ontvangt een test lead nadat u **publiceren** op uw aanbieding hebt geselecteerd in partner centrum. Als u de test leider ziet, is de verbinding geldig. U kunt ook uw lead verbinding testen door te proberen de preview-versie van de aanbieding te verkrijgen tijdens de preview-stap. Selecteer **nu downloaden**, **Neem contact met mij**op of **Maak een gratis proef versie** van de aanbieding in de commerciële Marketplace.

Zorg er ook voor dat u de juiste gegevens zoekt. De inhoud van de sectie [lead gegevens begrijpen](#understand-lead-data) van dit artikel beschrijft de lead gegevens die we verzenden naar uw lead bestemming.

### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>Ik heb Azure Blob-opslag geconfigureerd als mijn lead-doel, maar waarom zie ik de lead niet?

Azure Blob-opslag wordt niet langer ondersteund als een lead bestemming, dus er ontbreken potentiële klant leads die zijn gegenereerd door uw aanbieding. Schakel over naar een van de andere opties voor de [doel bestemming](./commercial-marketplace-get-customer-leads.md). 

### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>Ik heb een e-mail bericht ontvangen van de commerciële Marketplace, maar waarom kan ik de lead niet vinden in mijn CRM?

Het is mogelijk dat het e-mail domein van de eind gebruiker afkomstig is van. edu. Uit het oogpunt van privacy geven we geen persoons gegevens uit het domein. edu door. Verzend een ondersteunings ticket via het [partner centrum hulp en ondersteuning](https://partner.microsoft.com/support/v2/?stage=1).

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Ik heb een Azure-tabel geconfigureerd als mijn lead bestemming. Hoe kan ik de leads weer geven?

U kunt toegang krijgen tot de lead gegevens die zijn opgeslagen in de Azure-tabel vanuit het Azure Portal. U kunt [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) ook gratis downloaden en installeren om de tabel gegevens van uw Azure Storage-account weer te geven.

### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>Ik heb een Azure-tabel geconfigureerd als mijn lead bestemming. Kan ik een melding ontvangen wanneer een nieuwe lead voor een commerciële Marketplace wordt verzonden?

Ja. Volg de instructies in [beheer van leads configureren met behulp van een Azure-tabel](./commercial-marketplace-lead-management-instructions-azure-table.md) voor het instellen van een micro soft flow waarmee een e-mail wordt verzonden als een lead wordt toegevoegd aan de Azure-tabel.

### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>Ik heb Sales Force geconfigureerd als mijn lead-doel, maar waarom kan ik de leads niet vinden?

Controleer of het formulier web-naar-lead een verplicht veld is op basis van een keuze lijst. Als dat het geval is, schakelt u het veld in op een niet-verplicht tekst veld.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Er is een probleem met mijn lead doel en er zijn enkele leads gemist. Kan ik ze in een e-mail bericht naar mij verzenden?

Als gevolg van persoonlijk identificeerbaar informatie beleid, kunnen we geen lead gegevens delen via onbeveiligde e-mail berichten.

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Ik heb een Azure-tabel geconfigureerd als mijn lead bestemming. Hoeveel kost het?

De gegevens voor het genereren van leads zijn laag. Het is minder dan 1 GB voor bijna alle uitgevers. De kosten zijn afhankelijk van het aantal ontvangen leads. Als bijvoorbeeld 1.000-leads per maand worden ontvangen, zijn de kosten ongeveer 50 cent. Zie [Azure Storage-overzicht](https://azure.microsoft.com/pricing/details/storage/)voor meer informatie over prijzen voor opslag.

Als uw vraag niet wordt beantwoord, neemt u contact op met Microsoft Ondersteuning via de [Help en ondersteuning van het partner centrum](https://aka.ms/marketplacepublishersupport). Selecteer vervolgens **aanbieding maken** > **uw type** > **configuratie van lead beheer**.

### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>Ik ontvang e-mail meldingen wanneer er nieuwe leads van klanten worden ontvangen. Hoe kan ik iemand anders configureren voor het ontvangen van deze e-mail berichten?

Krijg toegang tot uw aanbieding in Partner Center en ga naar de pagina voor het instellen van de **aanbieding** > **lead beheer** > **bewerken**. Werk de e-mail adressen bij in het veld **e-mail adres van contact persoon** .

## <a name="next-steps"></a>Volgende stappen

Nadat de technische installatie is uitgevoerd, moet u deze leads opnemen in uw huidige verkoop-en marketing strategie en operationele processen. We zijn geïnteresseerd in een beter inzicht in uw algehele verkoop proces en willen nauw keurig samen werken om leads van hoge kwaliteit en voldoende gegevens te bieden om u te laten slagen. We stellen uw feedback op de manier waarop we u kunnen helpen bij het optimaliseren en verbeteren van de leads die wij u met aanvullende gegevens sturen om deze klanten succesvol te maken. Laat het ons weten als u geïnteresseerd bent in [feedback](mailto:AzureMarketOnboard@microsoft.com) en suggesties zodat uw verkoop team succes voller kan maken met leads voor commerciële Marketplace.
