---
title: Selfserviceopties voor inwisselen en retourneren van Azure-reserveringen
description: Meer informatie over hoe u Azure-reserveringen kunt inwisselen of retourneren.
author: yashesvi
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 393db5d2e14e047ade04e0b688582e272c6ca44f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77200432"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Selfserviceopties voor inwisselen en retourneren van Azure-reserveringen

Azure-reserveringen bieden flexibiliteit om te voldoen aan uw evoluerende behoeften. U kunt een reservering inwisselen voor een andere reservering van hetzelfde type. U kunt ook een restitutie (tot USD 50.000 per jaar) voor een reservering aanvragen als u deze niet meer nodig hebt. De maximumlimiet van de restitutie geldt voor alle reserveringen binnen het bereik van uw overeenkomst met Microsoft.

De selfserviceoptie voor inwisselen en annuleren is niet beschikbaar voor Enterprise Agreement-klanten van de Amerikaanse overheid. Andere abonnementstypen voor de Amerikaanse overheid, zoals Betalen per gebruik en CSP, worden wel ondersteund.

U moet eigenaarsrechten voor de reserveringsorder hebben om een bestaande reservering in te wisselen of er een restitutie voor te krijgen.

## <a name="exchange-an-existing-reserved-instance"></a>Een bestaande gereserveerde instantie inwisselen

U kunt uw reservering inwisselen met drie snelle stappen in [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Selecteer de reserveringen waarvoor u een restitutie wilt aanvragen en selecteer **Inwisselen**.  
    ![Voorbeeldafbeelding van reserveringen om restitutie voor aan te vragen](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png)
2. Selecteer het VM-product dat u wilt aanschaffen en geef een hoeveelheid op. Zorg ervoor dat het totaal van de nieuwe aankoop hoger is dan het restitutietotaal. [Bepaal de juiste grootte voordat u overgaat tot aanschaf](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    ![Voorbeeldafbeelding van het te kopen VM-product bij een inwisseling](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png)
3. Controleer en voltooi de transactie.  
    ![Voorbeeldafbeelding van het te kopen VM-product bij een inwisseling, waarbij de retournering wordt voltooid](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png)

Voor restitutie voor een reservering gaat u naar**Reserveringsgegevens** en selecteert u **Restitutie**.

## <a name="how-transactions-are-processed"></a>Hoe transacties worden verwerkt

Microsoft annuleert eerst de bestaande reservering en restitueert het aangepaste bedrag voor die reservering. Als er een inwisseling is, wordt de nieuwe aankoop verwerkt. Microsoft verwerkt restituties met behulp van een van de volgende methoden, afhankelijk van uw accounttype en betalingsmethode:

### <a name="enterprise-agreement-customers"></a>Enterprise Agreement-klanten

Geld wordt toegevoegd aan de financiële toezegging voor uitwisselingen en restituties als de oorspronkelijke aankoop hiermee is gedaan. Eventuele overschrijdingsfacturen sinds de oorspronkelijke aankopen worden opnieuw geopend en geëvalueerd om ervoor te zorgen dat de financiële toezegging wordt gebruikt. Als de financiële toezeggingstermijn die gebruikmaakt van de reservering niet langer actief is, wordt tegoed toegevoegd aan uw huidige financiële toezeggingsperiode van de Enterprise Agreement. Het tegoed is 90 dagen vanaf de terugbetalingsdatum geldig. Ongebruikt tegoed verloopt na 90 dagen.

Als de oorspronkelijke aankoop is gemaakt als een overschrijding, geeft Microsoft een creditnota uit.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Betalingen van betalen per gebruik-facturen en CSP-programma

De oorspronkelijke inkoopfactuur voor reserveringen wordt geannuleerd en er wordt een nieuwe factuur gemaakt voor de restitutie. Voor inwisselingen wordt in de nieuwe factuur de restitutie en de nieuwe aankoop weergegeven. Het restitutiebedrag wordt aangepast op basis van de aankoop. Als u alleen een reservering restitueert, blijft het bedrag naar rato bij Microsoft en wordt dit aangepast op basis van een toekomstige reserveringsaankoop.

### <a name="pay-as-you-go-credit-card-customers"></a>Betalen per gebruik-creditcardklanten

De oorspronkelijke factuur wordt geannuleerd en er wordt een nieuwe factuur gemaakt. Het geld wordt gerestitueerd naar de creditcard die is gebruikt voor de oorspronkelijke aankoop. Als u uw kaart hebt gewijzigd, neemt u [contact op met ondersteuning](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="cancel-exchange-and-refund-policies"></a>Beleidsregels voor annuleren, inwisselen en restituties

Azure heeft de volgende beleidsregels voor annuleringen, inwisselingen en restituties.

**Beleid voor inwisselen**

- U kunt meerdere bestaande reserveringen retourneren om een nieuwe reservering van hetzelfde type aan te schaffen. U kunt geen reserveringen van het ene type inwisselen voor een andere. U kunt bijvoorbeeld geen VM-reservering retourneren om een SQL-reservering aan te schaffen.
- Alleen eigenaars van reserveringen kunnen een inwisseling verwerken. [Meer informatie over het toevoegen of wijzigen van gebruikers die een reservering kunnen beheren](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Een inwisseling wordt verwerkt als een restitutie en een nieuwe aankoop: er worden verschillende transacties gemaakt voor de annulering en de nieuwe aankoop. Het reserveringsbedrag naar rato wordt gerestitueerd voor de reserveringen die u inruilt. De kosten voor de nieuwe aankoop worden volledig in rekening gebracht. Het reserveringsbedrag naar rato is de dagelijkse restwaarde naar rato van de reservering die wordt geretourneerd.
- U kunt reserveringen inwisselen of hier restitutie voor aanvragen zelfs als de Enterprise Agreement die is gebruikt om de reservering aan te schaffen is verlopen en is vernieuwd als een nieuwe overeenkomst.
- U kunt reserveringseigenschappen wijzigen zoals familie, serie, versie, SKU, regio, hoeveelheid en periode wijzigen met een inwisseling.
- Het nieuwe aankooptotaal moet gelijk zijn aan of groter zijn dan het geretourneerde bedrag.
- De nieuwe reservering die is gekocht als onderdeel van een inwisseling, heeft een nieuwe periode die start vanaf het moment van de inwisseling.
- Er is geen boete of jaarlijkse limieten voor inwisselingen.

**Restitutiebeleid**
- In de toekomst worden voor annuleringen mogelijk kosten voor vroege beëindiging in rekening gebracht van 12%. Momenteel wordt er geen boete berekend.
- Uw totale restitutiebedrag mag niet groter zijn dan USD 50.000 in een voortschrijdende periode van 12 maanden.
- Restituties worden berekend op basis van de laagste prijs, zijnde de aankoopprijs of de huidige prijs van de reservering, welke het laagste is.
- Alleen eigenaren van reserveringsorders kunnen een restitutie verwerken. [Meer informatie over het toevoegen of wijzigen van gebruikers die een reservering kunnen beheren](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Niet-Premium Storage inwisselen voor Premium Storage

U kunt een reservering die is gekocht voor een VM-grootte die geen ondersteuning biedt voor Premium Storage inwisselen voor een bijbehorende VM-grootte die deze ondersteuning wel biedt. Bijvoorbeeld een _F1_ voor een _F1s_. Als u de inwisseling wilt maken, gaat u naar Reserveringsdetails en selecteert u **Inwisselen**. De periode van de gereserveerde instantie wordt niet opnieuw ingesteld door de inwisseling en er wordt ook geen nieuwe transactie gemaakt.

## <a name="need-help-contact-us"></a>Hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure-reserveringen beheren](manage-reserved-vm-instance.md) voor meer informatie over het beheren van een reservering.
- Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:
    - [Wat zijn Azure-reserveringen?](save-compute-costs-reservations.md)
    - [Reserveringen beheren in Azure](manage-reserved-vm-instance.md)
    - [Begrijpen hoe de reserveringskorting wordt toegepast](../manage/understand-vm-reservation-charges.md)
    - [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](understand-reserved-instance-usage.md)
    - [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](understand-reserved-instance-usage-ea.md)
    - [Kosten van Windows-software zijn niet inbegrepen bij reserveringen](reserved-instance-windows-software-costs.md)
    - [Azure-reserveringen in CSP-programma (Cloud Solution Provider) van partnercentrum](/partner-center/azure-reservations)
