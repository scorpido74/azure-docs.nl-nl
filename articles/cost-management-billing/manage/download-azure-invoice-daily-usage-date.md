---
title: Uw Azure-factuur en de dagelijkse gebruiksgegevens downloaden
description: In dit artikel wordt beschreven hoe u uw Azure-factuur en de dagelijkse gebruiksgegevens kunt downloaden of weergeven.
keywords: factuur,factuur downloaden,azure-factuur,azure-gebruik
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: a3930698bb2999ebaad4d78e0b545b83cc968ef2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690031"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Uw Azure-factuur en de dagelijkse gebruiksgegevens downloaden of weergeven

Voor de meeste abonnementen kunt u uw factuur downloaden via de [Azure-portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) of naar uw e-mailadres laten verzenden. Als u een Azure-klant bent met een Enterprise Agreement (EA-klant), kunt u de facturen van uw organisatie niet downloaden. Facturen worden verzonden naar iedereen die is ingesteld voor ontvangst van facturen voor de inschrijving.

Als u een EA-klant bent of een [Microsoft-klantovereenkomst](#check-access-to-a-microsoft-customer-agreement) hebt, kunt u gegevens van het gebruik downloaden in de [Azure-portal](https://portal.azure.com/).

Alleen bepaalde rollen hebben toestemming om factuur- en gebruiksgegevens op te vragen, zoals de accountbeheerder of de Enterprise-beheerder. Zie [Toegang tot factureringsgegevens voor Azure beheren](manage-billing-access.md) voor meer informatie over het verkrijgen van toegang tot factureringsgegevens.

Als u een Microsoft-klantovereenkomst hebt, kunt u alleen factuur- en gebruiksgegevens bekijken als u een eigenaar, inzender of lezer van het factureringsprofiel bent of een factuurbeheerder. Zie [Rollen en taken voor factureringsprofielen](understand-mca-roles.md#billing-profile-roles-and-tasks) voor meer informatie over factureringsrollen voor Microsoft-klantovereenkomsten.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="download-your-azure-invoices-pdf"></a>Uw Azure-facturen (.pdf) downloaden

Voor de meeste abonnementen kunt u uw factuur downloaden via de Azure-portal of naar uw e-mailadres laten verzenden. Als u een Microsoft-klantovereenkomst hebt, raadpleegt u Facturen downloaden voor een factureringsprofiel.

### <a name="download-invoices-for-an-individual-subscription"></a>Facturen voor een afzonderlijk abonnement downloaden

1. Selecteer uw abonnement op de pagina [Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) in de Azure-portal als [een gebruiker met toegang tot facturen](manage-billing-access.md).

2. Selecteer **Facturen**.

    ![Schermopname met de optie Facturering en gebruik](./media/download-azure-invoice-daily-usage-date/billingandusage.png)

3. Klik op de downloadknop om een exemplaar van uw PDF-factuur te downloaden en selecteer vervolgens **Factuur downloaden**. Als u de melding **Niet beschikbaar** ziet, raadpleegt u [Waarom zie ik geen factuur voor de afgelopen betalingsperiode?](#noinvoice)

    ![Schermopname van de factureringsperioden, de downloadoptie en de totale kosten voor elke factureringsperiode](./media/download-azure-invoice-daily-usage-date/downloadinvoice.png)

4. U kunt ook een dagelijkse uitsplitsing van de verbruikte hoeveelheden en geschatte kosten downloaden door op **CSV downloaden** te klikken.

    ![Schermopname met de pagina voor factuur en gebruik downloaden](./media/download-azure-invoice-daily-usage-date/usageandinvoice.png)

Zie [Meer informatie over uw factuur voor Microsoft Azure](../understand/review-individual-bill.md) voor meer informatie over uw factuur. Zie [Onverwachte kosten voorkomen met Azure-facturering en -kostenbeheer](getting-started.md) voor hulp bij het beheren van uw kosten.

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Facturen voor een Microsoft-klantovereenkomst downloaden

Voor elk [factureringsprofiel](../understand/mca-overview.md#billing-profiles) in de Microsoft-klant overeenkomst worden facturen gegenereerd. U moet eigenaar, bijdrager, lezer of factuurbeheerder van een factureringsprofiel zijn om facturen te kunnen downloaden van de Azure-portal.

1. Zoek naar **Kostenbeheer en facturering**.
2. Selecteer een factureringsprofiel.
3. Selecteer **Facturen**.
4. Zoek in het factuurraster de rij van de factuur die u wilt downloaden.
5. Klik op de downloadknop aan het einde van de rij.
6. Selecteer **Factuur** in het contextmenu voor downloaden.

Als u geen factuur voor de laatste factureringsperiode ziet, raadpleegt u **Aanvullende informatie.** <!-- Fix this -->
### <a name="why-dont-i-see-an-invoice-for-the-last-billing-period"></a><a name="noinvoice"></a>Waarom zie ik geen factuur voor de laatste factureringsperiode?

Er kunnen verschillende redenen voor zijn dat u geen factuur ziet:

- Het is minder dan 30 dagen geleden dat u zich geabonneerd hebt op Azure.

- De factuur is nog niet gegenereerd. Wacht tot het einde van de factureringsperiode.

- U bent niet gemachtigd om facturen te bekijken. Als u een Microsoft-klantovereenkomst hebt, moet u eigenaar, bijdrager of lezer van het factureringsprofiel zijn, of factuurbeheerder. Voor andere abonnementen ziet u mogelijk geen oude facturen als u niet de accountbeheerder bent. Zie [Toegang tot factureringsgegevens voor Azure beheren](manage-billing-access.md) voor meer informatie over het verkrijgen van toegang tot factureringsgegevens.

- Als u een gratis proefversie gebruikt of een maandelijks tegoed met uw abonnement dat u niet overschrijdt, ontvangt u geen factuur tenzij u een Microsoft-klantovereenkomst hebt.

## <a name="get-your-invoice-in-email-pdf"></a>Uw factuur via e-mail (.pdf) ontvangen

U kunt zich aanmelden en extra ontvangers configureren om uw Azure-factuur in een e-mailbericht te ontvangen. Deze functie is mogelijk niet beschikbaar voor bepaalde abonnementen, zoals ondersteuningsaanbiedingen, Enterprise Agreements of Azure in Open. Als u een Microsoft-klantovereenkomst hebt, gaat u naar Facturen voor uw Microsoft-klantovereenkomst ontvangen via e-mail.

### <a name="get-your-subscriptions-invoices-in-email"></a>Facturen van uw abonnement via e-mail ontvangen

1. Selecteer uw abonnement op de pagina [Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Meld u aan voor elk van uw abonnementen. Klik achtereenvolgens op **Facturen** en **Verstuur mijn factuur via e-mail**.

    ![Schermopname met de stappen voor aanmelden](./media/download-azure-invoice-daily-usage-date/invoicesdeeplink01.png)

2. Klik op **Aanmelden** en accepteer de voor waarden.

    ![Schermopname met stap 2 van de aanmelding](./media/download-azure-invoice-daily-usage-date/invoicearticlestep02.png)

3. Nadat u de overeenkomst hebt geaccepteerd, kunt u extra ontvangers configureren. Wanneer een ontvanger wordt verwijderd, wordt het e-mailadres niet meer bewaard. Als u van gedachten verandert, moet u de ontvanger opnieuw toevoegen.

    ![Schermopname met stap 3 van de aanmelding](./media/download-azure-invoice-daily-usage-date/invoicearticlestep03.png)

Hebt u de stappen gevolgd, maar toch geen e-mail ontvangen? Controleer dan of uw e-mailadres klopt in de [communicatievoorkeuren in uw profiel](https://account.windowsazure.com/profile).

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Afmelden voor het via e-mail ontvangen van de facturen van uw abonnement

Volg de bovenstaande stappen om u af te melden voor ontvangst van uw factuur via e-mail en klik op **Afmelden voor facturen via e-mail**. Met deze optie worden alle e-mailadressen verwijderd die zijn ingesteld voor het ontvangen van facturen via e-mail. Als u zich weer aanmeldt, kunt u opnieuw ontvangers configureren.

 ![Schermopname met de stappen voor afmelden](./media/download-azure-invoice-daily-usage-date/invoicearticlestep04.png)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Facturen voor uw Microsoft-klantovereenkomst ontvangen via e-mail

Als u een Microsoft-klantovereenkomst hebt, kunt u zich aanmelden om uw factuur via e-mail te ontvangen. Alle eigenaren, bijdragers of lezers van het factureringsprofiel, en factuurbeheerders, ontvangen de factuur via e-mail. Lezers kunnen de voorkeur voor e-mailfacturen niet bijwerken.

1. Zoek naar **Kostenbeheer en facturering**.
1. Selecteer een factureringsprofiel.
1. Selecteer onder **Alle instellingen** de optie **Eigenschappen**.
1. Selecteer onder **E-mailfactuur** de optie **Voorkeur voor e-facturen bijwerken** .
1. Selecteer **Aanmelden**.
1. Klik op **Update**.

### <a name="opt-out-of-getting-your-billing-profile-invoices-in-email"></a>Afmelden voor het via e-mail ontvangen van facturen

Volg de bovenstaande stappen om u af te melden voor ontvangst van uw factuur via e-mail en klik op **Afmelden**. Ook alle eigenaren, bijdragers, lezers en factuurbeheerders worden afgemeld voor ontvangst van de factuur via e-mail. Als u een lezer bent, kunt u de voorkeur voor e-mailfacturen niet wijzigen.

## <a name="download-usage-in-azure-portal"></a>Uw gebruik downloaden in de Azure-portal

 Voor de meeste abonnementen kunt u deze stappen volgen om uw dagelijks gebruik te vinden:

1. Selecteer uw abonnement op de pagina [Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) in de Azure-portal als [een gebruiker met toegang tot facturen](manage-billing-access.md).

2. Selecteer **Facturen**.

    ![Schermopname met de optie Facturering en gebruik](./media/download-azure-invoice-daily-usage-date/billingandusage.png)

3. Klik op de downloadknop van de factuurperiode die u wilt controleren.

4. Download een dagelijkse uitsplitsing van de verbruikte hoeveelheden en geschatte kosten door op **CSV downloaden** te klikken.  Het kan enkele minuten duren om het CSV-bestand voor te bereiden.

### <a name="download-usage-for-ea-customers"></a>Gebruiksgegevens downloaden voor EA-klanten

Om als een EA-klant gebruiksgegevens weer te geven en te downloaden, moet u een Enterprise-beheerder, accounteigenaar of afdelingsbeheerder zijn en moet het beleid Kosten weergeven zijn ingeschakeld.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek naar *Kostenbeheer en facturering*.

    ![Schermopname van de zoekopdracht in de Azure-portal](./media/download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Selecteer **Gebruik + kosten**.
1. Selecteer **Downloaden** voor de maand waarvan u de gegevens wilt downloaden.

### <a name="download-usage-for-your-microsoft-customer-agreement"></a>Gebruiksgegevens voor uw Microsoft-klantovereenkomst downloaden

Als u gebruiksgegevens voor een factureringsprofiel wilt weergeven en downloaden, moet u een eigenaar, bijdrager of lezer van het factureringsprofiel zijn of de factuurbeheerder.

#### <a name="download-usage-for-billed-charges"></a>Gebruiksgegevens voor gefactureerde kosten downloaden

1. Zoek naar **Kostenbeheer en facturering**.
2. Selecteer een factureringsprofiel.
3. Selecteer **Facturen**.
4. Ga in het factuurraster naar de rij van de factuur die overeenkomt met de gebruiksgegevens die u wilt downloaden.
5. Klik op het beletselteken (`...`) aan het einde van de rij.
6. Selecteer **Azure-gebruik en -kosten** in het contextmenu voor het downloaden.

#### <a name="download-usage-for-open-charges"></a>Gebruiksgegevens voor niet-gefactureerde kosten downloaden

U kunt ook het gebruik van de maand tot heden voor de huidige factureringsperiode downloaden. U ziet dan de kosten die nog niet zijn gefactureerd.

1. Zoek naar **Kostenbeheer en facturering**.
2. Selecteer een factureringsprofiel.
3. Klik op de blade **Overzicht** op **Azure-gebruik en-kosten downloaden**.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Toegang tot een Microsoft-klantovereenkomst controleren
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u een vraag wilt stellen of hulp nodig hebt, maakt u een [ondersteuningsaanvraag](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over uw factuur en kosten:

- [Meer informatie over uw factuur voor Microsoft Azure](../understand/review-individual-bill.md)
- [Informatie over begrippen op uw Azure-factuur](../understand/understand-invoice.md)
- [Informatie over begrippen voor gedetailleerd gebruik van Microsoft Azure](../understand/understand-usage.md)
- [De Azure-prijzen voor uw organisatie weergeven](ea-pricing.md)

Als u een Microsoft-klantovereenkomst hebt, raadpleegt u:

- [Informatie over de kosten op de factuur voor uw factureringsprofiel](../understand/review-customer-agreement-bill.md)
- [Informatie over begrippen op de factuur voor uw factureringsprofiel](../understand/mca-understand-your-invoice.md)
- [Informatie over het bestand voor Azure-gebruik en -kosten voor uw factureringsprofiel](../understand/mca-understand-your-usage.md)
- [Belastingdocumenten voor uw factureringsprofiel bekijken en downloaden](../understand/mca-download-tax-document.md)
- [Azure-prijzen voor uw organisatie bekijken en downloaden](ea-pricing.md)
