---
title: Uw Microsoft Azure-factuur weergeven en downloaden
description: Hierin wordt beschreven hoe u uw Microsoft Azure-factuur kunt weergeven en downloaden.
keywords: facturering, factuur, factuur downloaden, Azure-factuur, Azure-gebruik
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: banders
ms.openlocfilehash: 4dc5f88f7a5994e5bcd50d71c86bf1ba35b10734
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "68321771"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Uw Microsoft Azure-factuur weergeven en downloaden

Voor de meeste abonnementen kunt u uw factuur downloaden via de [Azure-portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) of naar uw e-mailadres laten verzenden. Als u een Azure-klant bent met een Enterprise Agreement (EA-klant), kunt u de facturen van uw organisatie niet downloaden. Facturen worden verzonden naar iedereen die is ingesteld voor ontvangst van facturen voor de inschrijving.

Alleen bepaalde rollen zijn gemachtigd om facturen weer te geven. Zoals de accountbeheerder of de beheerder van de onderneming. Zie [Toegang tot factureringsgegevens voor Azure beheren](billing-manage-access.md) voor meer informatie over het verkrijgen van toegang tot factureringsgegevens.

Als u een [Microsoft-klantovereenkomst](#check-your-access-to-a-microsoft-customer-agreement) hebt, moet u een van de volgende rollen hebben om uw facturen te kunnen ontvangen:

- Eigenaar van het factureringsprofiel
- Bijdrager
- Lezer
- Factuurbeheerder

Zie [Rollen en taken voor het factureringsprofiel](billing-understand-mca-roles.md#billing-profile-roles-and-tasks) voor meer informatie over factureringsrollen voor Microsoft-klantovereenkomsten.

## <a name="noinvoice"></a> Waarom u geen factuur ontvangt

Er kunnen verschillende redenen voor zijn dat u geen factuur ziet:

- Het is minder dan 30 dagen geleden dat u zich geabonneerd hebt op Azure.

- Azure factureert u aan het einde van uw factureringsperiode. Het is dus mogelijk dat er nog geen factuur is gegenereerd. Wacht tot het einde van de factureringsperiode.

- U bent niet gemachtigd om facturen te bekijken. Als u een Microsoft-klantovereenkomst hebt, moet u eigenaar, bijdrager, lezer of factuurbeheerder van het factureringsprofiel zijn. Voor andere abonnementen ziet u mogelijk geen oude facturen als u niet de accountbeheerder bent. Zie [Toegang tot factureringsgegevens voor Azure beheren](billing-manage-access.md) voor meer informatie over het verkrijgen van toegang tot factureringsgegevens.

- Als u een gratis proefversie of een maandelijks tegoed voor uw abonnement hebt, ontvangt u alleen een factuur wanneer u het maandelijks tegoed overschrijdt. Als u een Microsoft-klantovereenkomst hebt, ontvangt u altijd een factuur.

## <a name="download-your-azure-invoices-pdf"></a>Uw Azure-facturen (.pdf) downloaden

Voor de meeste abonnementen kunt u uw factuur downloaden via de Azure-portal of naar uw e-mailadres laten verzenden. Als u een Microsoft-klantovereenkomst hebt, raadpleegt u [Facturen downloaden voor een Microsoft-klantovereenkomst downloaden](#download-invoices-for-a-microsoft-customer-agreement).

### <a name="download-invoices-for-an-individual-subscription"></a>Facturen voor een afzonderlijk abonnement downloaden

1. Selecteer uw abonnement op de pagina [Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) in de Azure-portal als [een gebruiker met toegang tot facturen](billing-manage-access.md).

2. Selecteer **Facturen**.

    ![Schermopname met de optie Facturering en gebruik](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. Klik op **Factuur downloaden** om een kopie van uw PDF-factuur weer te geven. Als u de melding **Niet beschikbaar** ziet, raadpleegt u [Waarom zie ik geen factuur voor de afgelopen betalingsperiode?](#noinvoice)

    ![Schermopname van de factureringsperioden, de downloadoptie en de totale kosten voor elke factureringsperiode](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. U kunt uw dagelijks gebruik ook bekijken als u op de factureringsperiode klikt.

Zie [Meer informatie over uw factuur voor Microsoft Azure](billing-understand-your-bill.md) voor meer informatie over uw factuur. Zie [Onverwachte kosten voorkomen met Azure-facturering en kostenbeheer](billing-getting-started.md) voor hulp bij het beheren van uw kosten.

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Facturen voor een Microsoft-klantovereenkomst downloaden

Voor elk [factureringsprofiel](billing-mca-overview.md#billing-profiles) in de Microsoft-klant overeenkomst worden facturen gegenereerd. U moet eigenaar, bijdrager, lezer of factuurbeheerder van een factureringsprofiel zijn om facturen te kunnen downloaden van de Azure-portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek naar *Kostenbeheer en facturering*.
1. Selecteer een factureringsprofiel. Afhankelijk van uw toegang moet u mogelijk eerst een factureringsrekening selecteren.
1. Selecteer **Facturen**.
1. Zoek in het factuurraster de rij van de factuur die u wilt downloaden.
1. Klik op het weglatingsteken (`...`) aan het einde van de rij.
    ![Schermopname van het weglatingsteken aan het einde van de rij](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)
1. Selecteer **Factuur** in het contextmenu voor downloaden.

    ![Schermopname van het contextmenu](./media/billing-download-azure-invoice/contextmenu.png)

Als u geen factuur voor de laatste factureringsperiode ziet, raadpleegt u [Waarom wordt er geen factuur weergegeven voor de laatste factureringsperiode?](#noinvoice)

## <a name="get-your-invoice-in-email-pdf"></a>Uw factuur via e-mail (.pdf) ontvangen

U kunt zich aanmelden en extra ontvangers configureren om uw Azure-factuur in een e-mailbericht te ontvangen. Deze functie is mogelijk niet beschikbaar voor bepaalde abonnementen, zoals ondersteuningsaanbiedingen, Enterprise Agreements of Azure in Open. Als u een Microsoft-klantovereenkomst hebt, gaat u naar het volgende gedeelte [De facturen van uw factureringsprofiel ontvangen via e-mail](#get-your-subscriptions-invoices-in-email).

### <a name="get-your-subscriptions-invoices-in-email"></a>De facturen van uw abonnement via e-mail ontvangen

1. Selecteer uw abonnement op de pagina [Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Meld u aan voor elk van uw abonnementen. Klik achtereenvolgens op **Facturen** en **Verstuur mijn factuur via e-mail**.

    ![Schermopname met de stappen voor aanmelden](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Klik op **Aanmelden** en accepteer de voor waarden.

    ![Schermopname met stap 2 van de aanmelding](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. Wanneer u de overeenkomst hebt geaccepteerd, kunt u extra ontvangers configureren. Wanneer een ontvanger wordt verwijderd, wordt het e-mailadres niet meer bewaard. Als u van gedachten verandert, moet u het e-mailadres opnieuw toevoegen.

    ![Schermopname met stap 3 van de aanmelding](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Hebt u de stappen gevolgd, maar toch geen e-mail ontvangen? Controleer dan of uw e-mailadres klopt in de [communicatievoorkeuren in uw profiel](https://account.windowsazure.com/profile).

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Afmelden voor het via e-mail ontvangen van de facturen van uw abonnement

Volg de voorgaande stappen om u af te melden voor ontvangst van uw factuur via e-mail en klik op **Afmelden voor facturen via e-mail**. Met deze optie worden alle e-mailadressen verwijderd die zijn ingesteld voor het ontvangen van facturen via e-mail. Als u zich weer aanmeldt, kunt u ontvangers opnieuw configureren.

 ![Schermopname met de stappen voor afmelden](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Facturen voor uw Microsoft-klantovereenkomst ontvangen via e-mail

Als u een Microsoft-klantovereenkomst hebt, kunt u zich aanmelden om uw factuur via e-mail te ontvangen. Alle eigenaren van het factureringsprofiel, bijdragers en factuurbeheerders ontvangen de factuur via e-mail. Lezers kunnen de voorkeur voor e-mailfacturen niet bijwerken.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek naar **Kostenbeheer en facturering**.
1. Selecteer een factureringsprofiel. Afhankelijk van uw toegang moet u mogelijk eerst een factureringsrekening selecteren.
1. Selecteer onder **Alle instellingen** de optie **Eigenschappen**.
1. Selecteer onder **E-mailfactuur** de optie **Voorkeur voor e-facturen bijwerken** .

    ![Schermopname van eigenschappen voor e-facturen](./media/billing-download-azure-invoice/billingprofile-email.png)

1. Selecteer **Aanmelden**.
1. Klik op **Update**.

### <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Meld u af voor ontvangst van uw Microsoft-klantovereenkomst via e-mail

Volg de voorgaande stappen om u af te melden voor ontvangst van uw factuur via e-mail en klik op **Afmelden**. Ook alle eigenaren, bijdragers, lezers en factuurbeheerders worden afgemeld voor ontvangst van de factuur via e-mail. Als u een lezer bent, kunt u de voorkeur voor e-mailfacturen niet wijzigen.



## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Uw toegang tot een Microsoft-klantovereenkomst controleren
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over uw factuur en kosten:

- [Uw Microsoft Azure-gebruik en -kosten weergeven en downloaden](billing-download-azure-daily-usage.md)
- [Meer informatie over uw factuur voor Microsoft Azure](billing-understand-your-bill.md)
- [Informatie over begrippen op uw Azure-factuur](billing-understand-your-invoice.md)
- [Informatie over begrippen voor gedetailleerd gebruik van Microsoft Azure](billing-understand-your-usage.md)
- [De Azure-prijzen voor uw organisatie weergeven](billing-ea-pricing.md)

Als u een Microsoft-klantovereenkomst hebt, raadpleegt u:

- [Informatie over de kosten op de factuur voor uw factureringsprofiel](billing-mca-understand-your-bill.md)
- [Informatie over begrippen op de factuur voor uw factureringsprofiel](billing-mca-understand-your-invoice.md)
- [Informatie over het bestand voor Azure-gebruik en -kosten voor uw factureringsprofiel](billing-mca-understand-your-usage.md)
- [Belastingdocumenten voor uw factureringsprofiel bekijken en downloaden](billing-mca-download-tax-document.md)
- [De Azure-prijzen voor uw organisatie weergeven](billing-ea-pricing.md)
