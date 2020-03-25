---
title: Uw Microsoft Azure-factuur weergeven en downloaden
description: Hierin wordt beschreven hoe u uw Microsoft Azure-factuur kunt weergeven en downloaden.
keywords: facturering, factuur, factuur downloaden, Azure-factuur, Azure-gebruik
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 691d27acebf238e84265870e8c01976bfc2412b2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77200262"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Uw Microsoft Azure-factuur weergeven en downloaden

Voor de meeste abonnementen kunt u uw factuur downloaden via de [Azure-portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) of naar uw e-mailadres laten verzenden. Als u een Azure-klant bent met een Enterprise Agreement (EA-klant), kunt u de facturen van uw organisatie niet downloaden. Facturen worden verzonden naar iedereen die is ingesteld voor ontvangst van facturen voor de inschrijving.

Alleen bepaalde rollen zijn gemachtigd om facturen weer te geven. Zoals de accountbeheerder of de beheerder van de onderneming. Zie [Toegang tot factureringsgegevens voor Azure beheren](../manage/manage-billing-access.md) voor meer informatie over het verkrijgen van toegang tot factureringsgegevens.

Als u een Microsoft-klantovereenkomst (MCA) hebt, moet u een van de volgende rollen hebben om uw facturen te kunnen ontvangen:

- Eigenaar van het factureringsprofiel
- Inzender
- Lezer
- Factuurbeheerder

Als u een Microsoft Partner-overeenkomst (MPA) hebt, kan alleen de rol Globale beheerder en de rol Beheerderagent in de partnerorganisatie Azure-facturen weergeven en downloaden. [Controleer uw type factureringsrekening](#check-your-billing-account-type) om te zien welke machtigingen u nodig hebt.

<!-- For more information about billing roles for Microsoft Customer Agreements, see [Billing profile roles and tasks](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks). -->

## <a name="why-you-might-not-get-an-invoice"></a><a name="noinvoice"></a> Waarom u geen factuur ontvangt

Er kunnen verschillende redenen voor zijn dat u geen factuur ziet:

- Het is minder dan 30 dagen geleden dat u zich geabonneerd hebt op Azure.

- Azure factureert u aan het einde van uw factureringsperiode. Het is dus mogelijk dat er nog geen factuur is gegenereerd. Wacht tot het einde van de factureringsperiode.

- U bent niet gemachtigd om facturen te bekijken. Als u een MCA of MPA hebt, moet u de eigenaar van het factureringsprofiel, inzender, lezer of factuurbeheerder zijn. Voor andere abonnementen ziet u mogelijk geen oude facturen als u niet de accountbeheerder bent. Zie [Toegang tot factureringsgegevens voor Azure beheren](../manage/manage-billing-access.md) voor meer informatie over het verkrijgen van toegang tot factureringsgegevens.

- Als u een gratis proefversie of een maandelijks tegoed voor uw abonnement hebt, ontvangt u alleen een factuur wanneer u het maandelijks tegoed overschrijdt. Als u een Microsoft-klantovereenkomst of Microsoft Partner-overeenkomst hebt, ontvangt u altijd een factuur.

## <a name="download-invoices-in-the-azure-portal"></a>Facturen downloaden in de Azure-portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek naar *Kostenbeheer en facturering*.
1. Afhankelijk van uw toegang moet u mogelijk een factureringsrekening of factureringsprofiel selecteren.
1. Selecteer **Facturen** onder **Facturering** in het menu aan de linkerkant.
1. Zoek in het factuurraster de rij van de factuur die u wilt downloaden.
1. Klik op het downloadpictogram of beletselteken (`...`) aan het einde van de rij.
1. Selecteer **Factuur** in het downloadmenu.

Zie [Meer informatie over uw factuur voor Microsoft Azure](review-individual-bill.md) voor meer informatie over uw factuur. Zie [Onverwachte kosten voorkomen met Azure-facturering en kostenbeheer](../manage/getting-started.md) voor hulp bij het beheren van uw kosten.

## <a name="get-your-subscriptions-invoices-in-email"></a>Facturen van uw abonnement via e-mail ontvangen

U kunt zich aanmelden en extra ontvangers configureren om uw Azure-factuur in een e-mailbericht te ontvangen. Deze functie is mogelijk niet beschikbaar voor bepaalde abonnementen, zoals ondersteuningsaanbiedingen, Enterprise Agreements of Azure in Open.

1. Selecteer uw abonnement op de pagina [Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Meld u aan voor elk van uw abonnementen. Klik achtereenvolgens op **Facturen** en **Verstuur mijn factuur via e-mail**.

    ![Schermopname met de stappen voor aanmelden](./media/download-azure-invoice/invoicesdeeplink01.png)

2. Klik op **Aanmelden** en accepteer de voor waarden.

    ![Schermopname met stap 2 van de aanmelding](./media/download-azure-invoice/invoicearticlestep02.png)

3. Wanneer u de overeenkomst hebt geaccepteerd, kunt u extra ontvangers configureren. Wanneer een ontvanger wordt verwijderd, wordt het e-mailadres niet meer bewaard. Als u van gedachten verandert, moet u het e-mailadres opnieuw toevoegen.

    ![Schermopname met stap 3 van de aanmelding](./media/download-azure-invoice/invoicearticlestep03.png)

Hebt u de stappen gevolgd, maar toch geen e-mail ontvangen? Controleer dan of uw e-mailadres klopt in de [communicatievoorkeuren in uw profiel](https://account.windowsazure.com/profile).

## <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Afmelden voor het via e-mail ontvangen van de facturen van uw abonnement

Volg de voorgaande stappen om u af te melden voor ontvangst van uw factuur via e-mail en klik op **Afmelden voor facturen via e-mail**. Met deze optie worden alle e-mailadressen verwijderd die zijn ingesteld voor het ontvangen van facturen via e-mail. Als u zich weer aanmeldt, kunt u opnieuw ontvangers configureren.

 ![Schermopname met de stappen voor afmelden](./media/download-azure-invoice/invoicearticlestep04.png)

<!-- Does following section apply to MPA too? -->
## <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Facturen voor uw Microsoft-klantovereenkomst ontvangen via e-mail

Als u een factureringsaccount voor een Microsoft-klantovereenkomst hebt, kunt u zich aanmelden om uw factuur via e-mail te ontvangen. Alle gebruikers met de rol van eigenaar, inzender, lezer of factuurbeheerder in een factureringsprofiel ontvangen hun factuur via een e-mailbericht.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Zoek naar **Kostenbeheer en facturering**.

   ![Schermopname van zoekopdracht in portal naar abonnementen](./media/download-azure-invoice/search-cmb.png)

1. Selecteer aan de linkerkant de optie **Factureringsprofielen**. Selecteer in de lijst met factureringsprofielen een factureringsprofiel om de bijbehorende facturen per e-mail te ontvangen.

   [![Schermopname met lijst van factureringsprofielen](./media/download-azure-invoice/mca-select-profile.png)](./media/download-azure-invoice/mca-select-profile-zoomed-in.png#lightbox)

1. Selecteer **Eigenschappen** aan de linkerkant en selecteer vervolgens **Voorkeur voor e-facturen bijwerken**.

   [![Schermopname met lijst van factureringsprofielen](./media/download-azure-invoice/mca-select-update-email-preferences.png)](./media/download-azure-invoice/mca-select-update-email-preferences.png#lightbox)

1. Selecteer **Aanmelden** en klik vervolgens op **Bijwerken**.

   [![Schermopname met lijst van factureringsprofielen](./media/download-azure-invoice/mca-select-email-opt-in.png)](./media/download-azure-invoice/mca-select-email-opt-in.png#lightbox)

## <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Meld u af voor ontvangst van uw Microsoft-klantovereenkomst via e-mail

Volg de voorgaande stappen om u af te melden voor ontvangst van uw factuur via e-mail en klik op **Afmelden**. Alle gebruikers met de rol van eigenaar, inzender, lezer of factuurbeheerder worden afgemeld voor het ontvangen van een factuur per e-mail.

## <a name="give-others-access-to-your-microsoft-customer-agreement-invoices"></a>Anderen toegang geven tot facturen voor uw Microsoft-klantovereenkomst

U kunt anderen toegang geven om facturen te bekijken, te downloaden en te betalen door hen de rol van factuurbeheerder toe te wijzen voor een factureringsprofiel. Als u ervoor hebt gekozen om facturen per e-mail bericht te ontvangen, krijgen deze gebruikers de facturen ook per e-mail.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Zoek naar **Kostenbeheer en facturering**.

   ![Schermopname van zoekopdracht in portal naar abonnementen](./media/download-azure-invoice/search-cmb.png)

1. Selecteer aan de linkerkant de optie **Factureringsprofielen**. Selecteer in de lijst factureringsprofielen een factureringsprofiel waarvoor u de rol van factuurbeheerder wilt toewijzen.

   [![Schermopname met lijst van factureringsprofielen](./media/download-azure-invoice/mca-select-profile.png)](./media/download-azure-invoice/mca-select-profile-zoomed-in.png#lightbox)

1. Selecteer **Toegangsbeheer (IAM)** aan de linkerkant en selecteer vervolgens **Toevoegen** bovenaan de pagina.

   [![Schermopname van de pagina Toegangsbeheer](./media/download-azure-invoice/mca-select-access-control.png)](./media/download-azure-invoice/mca-select-access-control-zoomed-in.png#lightbox)

1. Selecteer **Factuurbeheerder** in de vervolgkeuzelijst Rol. Voer het e-mailadres in van de gebruiker die u toegang wilt verlenen. Selecteer **Opslaan** om de rol toe te wijzen.

   [![Schermopname van het toevoegen van een gebruiker als een factuurbeheerder](./media/download-azure-invoice/mca-added-invoice-manager.png)](./media/download-azure-invoice/mca-added-invoice-manager.png#lightbox)

## <a name="check-your-billing-account-type"></a>Uw type factureringsrekening controleren
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over uw factuur en kosten:

- [Uw Microsoft Azure-gebruik en -kosten weergeven en downloaden](download-azure-daily-usage.md)
- [Meer informatie over uw factuur voor Microsoft Azure](review-individual-bill.md)
- [Informatie over begrippen op uw Azure-factuur](understand-invoice.md)
- [Informatie over begrippen voor gedetailleerd gebruik van Microsoft Azure](understand-usage.md)
- [De Azure-prijzen voor uw organisatie weergeven](../manage/ea-pricing.md)

Als u een Microsoft-klantovereenkomst hebt, raadpleegt u:

- [Informatie over de kosten op de factuur voor uw factureringsprofiel](review-customer-agreement-bill.md)
- [Informatie over begrippen op de factuur voor uw factureringsprofiel](mca-understand-your-invoice.md)
- [Informatie over het bestand voor Azure-gebruik en -kosten voor uw factureringsprofiel](mca-understand-your-usage.md)
- [Belastingdocumenten voor uw factureringsprofiel bekijken en downloaden](mca-download-tax-document.md)
- [Azure-prijzen voor uw organisatie bekijken en downloaden](../manage/ea-pricing.md)
