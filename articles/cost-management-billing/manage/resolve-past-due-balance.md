---
title: E-mail over verschuldigd bedrag van Azure
description: Hierin wordt beschreven hoe u een betaling doet als uw Azure-abonnement een verschuldigde betaling heeft
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 9548900fe627f774aca08c05a243d807bb5a699c
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282684"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>Verschuldigd bedrag betalen voor uw Azure-abonnement

Dit artikel is van toepassing op klanten die zich online met een creditcard hebben geregistreerd voor Azure en die een factureringsaccount hebben voor het Microsoft Online Services-programma. [Uw type factureringsrekening controleren](#check-the-type-of-your-account). Raadpleeg in plaats hiervan [Uw rekening voor Microsoft Azure betalen](../understand/pay-bill.md) als u een factureringsaccount voor het Microsoft Online Services-programma hebt.

Als uw betaling niet is ontvangen of als de betaling niet kan worden verwerkt, ontvangt u een e-mailbericht en ziet u een waarschuwing in de Azure-portal met de melding dat de einddatum voor de betaling van uw abonnement is verstreken. Als creditcard uw standaardbetalingswijze is, kan de [accountbeheerder](billing-subscription-transfer.md#whoisaa) de openstaande kosten betalen in de Azure-portal. Als u per factuur (cheque/overschrijving) betaalt, verzendt u uw betaling naar de locatie die onder aan de factuur wordt vermeld.

> [!IMPORTANT]
> * Als u meerdere abonnementen hebt die gebruikmaken van dezelfde creditcard en deze allemaal verschuldigde betalingen hebben, moet u het volledige openstaande bedrag in één keer betalen.
> * De creditcard die u gebruikt om de openstaande betalingen te betalen, wordt de nieuwe standaardbetalingswijze voor alle abonnementen die gebruikmaken van de betalingswijze van de mislukte betalingen.

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>Verschuldigd bedrag betalen in de Azure-portal

1. Meld u als accountbeheerder aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek naar **Cost Management en facturering**.
1. Selecteer het abonnement met de achterstallige betaling op de pagina **Overzicht**.
1. Klik op de pagina **Abonnementsoverzicht** op de rode banner bij de achterstallige betaling om het saldo te vereffenen.
    > [!NOTE]
    > Als u geen accountbeheerder bent, kunt u het saldo niet vereffenen.
1. Klik op de nieuwe pagina **Saldo vereffenen** de optie **Betalingswijze selecteren**.
    ![Schermafbeelding die de koppeling voor selecteren betalingswijze weergeeft](./media/resolve-past-due-balance/settle-balance-screen.png)

1. In de nieuwe blade aan de rechterkant selecteert u een creditcard in de vervolgkeuzelijst of voegt u een nieuwe creditcard toe door op de blauwe koppeling **Nieuwe betalingswijze toevoegen** te klikken. Deze creditcard wordt de actieve betalingswijze voor alle abonnementen die momenteel gebruikmaken van de betalingswijze van de mislukte betalingen.
     > [!NOTE]
     > * Het totale openstaande saldo weerspiegelt openstaande kosten in alle Microsoft-services die gebruikmaken van de betalingswijze van de mislukte betalingen.
     > * Als voor de geselecteerde betalingswijze ook openstaande betalingen zijn voor Microsoft-services, wordt dit weerspiegeld in het totale openstaande saldo. U moet deze openstaande kosten dan ook betalen.
1. Klik op **Betalen**.

## <a name="troubleshoot-declined-credit-card"></a>Problemen met een geweigerde creditcard oplossen

Als een betaling via uw creditcard door uw financiële instelling is geweigerd, neemt u contact op deze instelling om het probleem op te lossen. Controleer bij uw bank of aan de volgende voorwaarden is voldaan:
- Internationale transacties zijn ingeschakeld op de kaart.
- De kaart heeft voldoende kredietlimiet of financiële dekking om het saldo te vereffenen.
- Terugkerende betalingen zijn ingeschakeld op de kaart.

## <a name="not-getting-billing-email-notifications"></a>Ontvangt u geen e-mailmeldingen voor facturering?

Als u de accountbeheerder bent, [controleert u welk e-mailadres wordt gebruikt voor meldingen](change-azure-account-profile.md). U kunt het beste een e-mailadres gebruiken dat u regelmatig controleert. Als het e-mailadres klopt, controleert u de map met ongewenste e-mail.

## <a name="if-i-forget-to-pay-what-happens"></a>Wat gebeurt er als ik vergeet te betalen?

De service wordt geannuleerd en uw resources zijn niet meer beschikbaar. Uw Azure-gegevens worden 90 dagen nadat de service is beëindigd, verwijderd. Zie [Vertrouwenscentrum van Microsoft - Hoe we uw gegevens beheren](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409) voor meer informatie.

Als u weet dat uw betaling is verwerkt, maar uw abonnement nog steeds is uitgeschakeld, neemt u contact op met de [ondersteuning voor Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="check-the-type-of-your-account"></a>Controleer uw accounttype
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).
