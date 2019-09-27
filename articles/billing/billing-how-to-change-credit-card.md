---
title: Uw creditcard wijzigen voor Azure
description: Hierin wordt beschreven hoe u de creditcard wijzigt die wordt gebruikt voor het betalen van een Azure-abonnement.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: banders
ms.openlocfilehash: f89f9a08feb2ea2bcb7a9e8180002557df1d2b56
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300311"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>Een creditcard toevoegen, bijwerken of verwijderen voor Azure

Dit document is van toepassing op klanten die zich online met een creditcard hebben geregistreerd voor Azure.

In de Azure-portal kunt u een nieuwe creditcard toevoegen, een bestaande creditcard bijwerken, of een creditcard verwijderen die u niet gebruikt. U moet een [accountbeheerder](billing-subscription-transfer.md#whoisaa) zijn om deze wijzigingen door te voeren.

Als u een [Microsoft-klantovereenkomst](#check-access-to-a-microsoft-customer-agreement) hebt, zijn betalingswijzen gekoppeld aan factureringsprofielen. Meer informatie over [het wijzigen van de standaardbetalingswijze voor een factureringsprofiel](#change-payment-method-for-a-billing-profile). Alleen een gebruiker die zich heeft geregistreerd voor Azure kan de betalingswijze bijwerken.

**Wilt u overschakelen naar betalen via een factuur (cheque/directe overboeking)?** Zie [Per factuur betalen voor Azure-abonnementen](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## <a name="add-a-new-credit-card-to-an-azure-subscription"></a>Een nieuwe creditcard toevoegen aan een Azure-abonnement

1. Meld u als accountbeheerder aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek naar **Kostenbeheer en facturering**.

    ![Schermopname van de zoekopdracht](./media/billing-how-to-change-credit-card/search.png)

1. Selecteer het abonnement waar u de creditcard aan wilt toevoegen.
1. Selecteer **Betalingswijzen**.

    ![Schermopname met de optie voor Betalingswijze beheren geselecteerd.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Selecteer '+' In de linkerbovenhoek om een kaart toe te voegen Een creditcardformulier wordt aan de rechterkant weergegeven.
1. Voer de creditcardgegevens in.

    ![Schermopname van het toevoegen van een nieuwe creditcard.](./media/billing-how-to-change-credit-card/sub-add-new-x.png)

1. Schakel het selectievakje naast **Instellen als mijn actieve betalingswijze** boven het formulier in zodat deze creditcard de actieve betalingswijze wordt. Deze kaart wordt de actieve betalingswijze voor alle abonnementen waarvoor dezelfde kaart wordt gebruikt als voor het geselecteerde abonnement.

1. Selecteer **Next**.

Als u een foutmelding krijgt nadat u de creditcard hebt toegevoegd, raadpleegt u [Creditcard is geweigerd tijdens de registratie](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-card"></a>Bestaande creditcard bijwerken

Als uw creditcard wordt vernieuwd en het nummer blijft hetzelfde, werkt u de bestaande creditcardgegevens bij , zoals de verloopdatum. Als uw creditcardnummer is gewijzigd, omdat de creditcard is verloren, gestolen of verlopen, volgt u de stappen in de sectie [Een creditcard toevoegen als betalingswijze](#addcard). U hoeft de CVV niet bij te werken.

1. Meld u als accountbeheerder aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek naar **Kostenbeheer en facturering**.

    ![Schermopname van de zoekopdracht](./media/billing-how-to-change-credit-card/search.png)

1. Selecteer **Betalingswijzen**.

    ![Schermopname met de optie voor Betalingswijze beheren geselecteerd.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Klik op de creditcard die u wilt bewerken. Een creditcardformulier wordt aan de rechterkant weergegeven.

    ![Schermopname van een geselecteerde creditcard.](./media/billing-how-to-change-credit-card/edit-card-x.png)

1. Werk de creditcardgegevens bij.
1. Selecteer **Opslaan**.

## <a name="use-a-different-credit-card"></a>Een andere creditcard gebruiken

Als u meer abonnementen heeft met dezelfde actieve betalingswijze, wordt de actieve betalingswijze van al deze abonnementen bijgewerkt wanneer de betalingswijze van één van de abonnementen wordt gewijzigd.

1. Meld u als accountbeheerder aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek naar **Kostenbeheer en facturering**.

    ![Schermopname van de zoekopdracht](./media/billing-how-to-change-credit-card/search.png)

1. Selecteer het abonnement waar u de creditcard aan wilt toevoegen.
1. Selecteer **Betalingswijzen**.

    ![Schermopname met de optie voor Betalingswijze beheren geselecteerd.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Selecteer het vakje naast de creditcard waarvan u de actieve betalingswijze wilt maken.
1. Klik op **Als actief instellen**.
    ![Schermopname van een geselecteerde creditcard die is ingesteld als actief.](./media/billing-how-to-change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-card-from-the-account"></a>Een creditcard verwijderen van het account

1. Meld u als accountbeheerder aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **Kostenbeheer en facturering** aan de linkerkant van de pagina.

    ![Schermopname van de zoekopdracht](./media/billing-how-to-change-credit-card/search.png)

1. Selecteer onder **Facturering** de optie **Betalingswijzen**.

    ![Schermopname met de optie voor Betalingswijze beheren geselecteerd.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Selecteer het vakje naast de creditcard die u wilt verwijderen.
1. Klik op **Verwijderen**.

Als uw creditcard de actieve betalingswijze is voor een van uw Microsoft-abonnementen, kunt u deze niet verwijderen uit uw Azure-account. Wijzig de actieve betalingswijze voor alle abonnementen die zijn gekoppeld aan deze creditcard, en probeer het opnieuw

## <a name="change-payment-method-for-a-billing-profile"></a>Betalingswijze voor een factureringsprofiel wijzigen

Als u de betalingswijze voor een factureringsprofiel wilt wijzigen, moet u de persoon zijn die zich heeft geregistreerd bij Azure.

Als u de standaardbetalingswijze wilt wijzigen in cheque/directe overboeking, leest u hoe u [een factureringsprofiel kunt wijzigen in een cheque/directe overboeking](billing-how-to-pay-by-invoice.md).

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zoek naar **kostenbeheer en facturering**.
1. Klik in het menu aan de linkerkant op **Factureringsprofielen**.

    ![schermopname van het factureringsprofiel in het menu](./media/billing-how-to-change-credit-card/billing-profile.png)

1. Selecteer een factureringsprofiel.
1. Selecteer in het menu aan de linkerkant de optie **Betalingsmethoden**.

   ![Schermopname van de betalingswijze in het menu](./media/billing-how-to-change-credit-card/billing-profile-payment-methods.png)

1. Klik boven de standaardbetalingswijze op **Wijzigen**.

    ![Schermopname van de wijzigingsknop](./media/billing-how-to-change-credit-card/customer-led-switch-credit-card.png)

1. Selecteer een bestaande creditcard of voeg een nieuwe toe.

## <a name="troubleshooting"></a>Problemen oplossen
Als u fouten krijgt bij het toevoegen of bijwerken van uw creditcard, probeer dan uw browser in privémodus te openen.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen
In de volgende secties worden veelgestelde vragen beantwoord over het wijzigen van uw creditcardgegevens.

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Mijn abonnement is uitgeschakeld. Waarom kan ik mijn creditcard nu niet verwijderen?

Nadat uw abonnement is uitgeschakeld of geannuleerd, duurt het 90 dagen voordat uw abonnement definitief wordt verwijderd. Uw betalingswijze wordt bewaard gedurende de retentieperiode, voor het geval u het abonnement later weer wilt activeren. Na deze periode wordt het abonnement definitief verwijderd.

Als u de creditcard wilt verwijderen vóórdat de retentieperiode van 90 dagen is beëindigd, moet u [uw abonnement opnieuw activeren](billing-subscription-become-disable.md). [Neem contact op met Azure-ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) als het opnieuw activeren niet lukt.

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Waarom zie ik steeds: Uw aanmeldingssessie is verlopen. Klik hier om u opnieuw aan te melden?

Als u dit foutbericht blijft ontvangen, zelfs als u zich al hebt afgemeld en weer aangemeld, probeert u het opnieuw met een privébrowsersessie.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Hoe kan ik een andere creditcard gebruiken voor elk abonnement dat ik heb?

Als uw abonnementen al gebruikmaken van dezelfde creditcard, is het niet meer mogelijk ze te scheiden om verschillende creditcards te gebruiken. Als u zich echter aanmeldt voor een nieuw abonnement, kunt u ervoor kiezen om een nieuwe betalingswijze voor dit abonnement te gebruiken.

### <a name="how-do-i-make-payments"></a>Hoe kan ik betalingen doen?

Als u een creditcard instelt als betalingswijze, worden uw kosten na elke factureringsperiode automatisch in rekening gebracht bij uw creditcard. U hoeft hiervoor niets te doen.

Als u [betaalt via een factuur](billing-how-to-pay-by-invoice.md), maakt u het verschuldigde bedrag over naar de rekening die onder aan de factuur wordt vermeld.

### <a name="how-do-i-change-the-tax-id"></a>Hoe kan ik het btw-nummer wijzigen?

Als u een btw-nummer wilt toevoegen of bijwerken, werkt u uw profiel bij in het [Azure-accountcentrum](https://account.azure.com/Profile) en selecteert u vervolgens **Btw-registratie**. Dit btw-nummer wordt gebruikt voor de berekening van btw-vrijstelling en wordt vermeld op uw factuur.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Toegang tot een Microsoft-klantovereenkomst controleren
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen
- Krijg meer informatie over [Azure-reserveringen](billing-save-compute-costs-reservations.md) om te zien of ze geld kunnen besparen.
