---
title: Veelgestelde vragen over verkopersinzichten
description: Veelgestelde vragen over de functie Verkopersinzichten van de Cloud Partner Portal.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 011558baa43ee3db2803e9229d1d15df5158d668
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285381"
---
<a name="seller-insights-faq"></a>Veelgestelde vragen over verkopersinzichten
===================

In dit artikel vindt u richtlijnen voor algemene gebruikersprocedures binnen en vragen over verkopersstatistieken.


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>Definities zoeken voor de waarden in het gedownloade transactiebestand
------------------------------------------------------------------

De definities van de metrische waarden in het transactiebestand zijn te vinden in het artikel [Definities voor verkopersinzichten](./si-insights-definitions-v4.md).


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>Bekijk klantgegevens van transacties waarvoor ik ben betaald
-------------------------------------------------------------

Nadat u uw transacties hebt gedownload uit de uitbetalingsmodule, zoekt u de kolom met de **status Uitbetaling**en past u het filter toe om alleen de waarde 'Uitbetaald' weer te geven. De volgende kolommen worden weergegeven met de klantgegevens: **Bedrijfsnaam**, **e-mail van de klant,** **klantland,** **klantstatus**en **klantpostcode**.


<a name="calculate-my-open-accounts-receivable"></a>Mijn openstaande debiteuren berekenen
-------------------------------------

Nadat u uw transacties hebt gedownload van de uitbetalingsmodule, zoekt u de kolom met het label **Uitbetalingsstatus**en past u het filter toe om alleen de waarde 'Aanstaande uitbetaling' en 'Niet klaar voor uitbetaling' weer te geven. Som vervolgens het **kolommetje uitbetalingsbedrag (PC)** op .


<a name="calculate-revenue-by-customer-usage-period"></a>Inkomsten berekenen op basis van de gebruiksperiode van de klant
------------------------------------------

Nadat u uw transacties hebt gedownload uit de uitbetalingsmodule, zoekt u de kolom met het label **Transactiestatus**en filtert u de waarde 'Betaald'.   Voor elke vermelde transactie vertegenwoordigt de kolom met het label **Uitbetalingsbedrag (PC)** het bedrag dat u hebt betaald.  Als u de gebruiksperiode wilt schatten die aan de transactie is gekoppeld, gebruikt u de **kolom datum kosten**, een nauwe benadering van de laatste gebruiksdag voor de periode waarop de transactie van toepassing is.


<a name="calculate-your-bad-debt"></a>Bereken uw slechte schuld
---------------------

Nadat u uw transacties hebt gedownload uit de uitbetalingsmodule, zoekt u de kolom met de **status Definitieve verzameling**en past u het filter toe om alleen de waarde 'Afschrijven' weer te geven. Som vervolgens het **kolommetje uitbetalingsbedrag (PC)** op .


<a name="view-payout-or-customer-contact-information"></a>Uitbetalings- of klantcontactgegevens weergeven
-------------------------------------------

Meld u aan als gebruiker met de rol 'eigenaar' en niet de rol 'bijdrager'. Alleen de rol van de eigenaar ziet uitbetaling en klantgegevens. Meer informatie over gebruikersrollen vindt u in het artikel [Gebruikers beheren.](./cloud-partner-portal-manage-users.md)


<a name="calculate-my-advance-payouts"></a>Bereken mijn voorschot uitbetalingen
----------------------------

Nadat u uw transacties hebt gedownload uit de uitbetalingsmodule, zoekt u de kolom met het label **Transactietype**en past u het filter toe om alleen de waarde 'Kosten' weer te geven. Zoek vervolgens de kolom met het label **Definitieve verzamelingstatus**en pas het filter toe om alleen de waarde 'In uitvoering' weer te geven. Som ten slotte de kolom **Uitbetalingsbedrag (PC)** om alle voorschotten te berekenen die aan u zijn betaald voordat u van de klant wordt opgehaald.


<a name="calculate-customer-refunds"></a>Klantterugbetalingen berekenen
--------------------------

Nadat u uw transacties hebt gedownload uit de uitbetalingsmodule, zoekt u de kolom met de **status Definitieve verzameling**en past u het filter toe om alleen de waarde 'Terugbetaling' weer te geven. Som de kolom **Kostenbedrag (PC)** om alle terugbetalingen te berekenen die voor uw klanten zijn verwerkt.


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>Bepalen welke transacties een Microsoft Channel Partner betroffen
----------------------------------------------------------------

Alle transacties in het kolom **Azure-licentietype** die worden gefilterd om de waarden 'Enterprise via Reseller' en 'Cloud Solution Provider' weer te geven, hebben betrekking op een Microsoft Channel-partner. Voor meer informatie over de partner, vindt u hun **reseller naam** en **reseller e-mail** in de uitbetaling module downloaden en de klant module downloaden.


<a name="identify-trial-usage-and-trial-conversions"></a>Proefgebruik en proefconversies identificeren
------------------------------------------

Downloads van de bestel-, gebruiks- en uitbetalingsmodule bevatten nu **de einddatum van de proefperiode** om u te helpen begrijpen wanneer de proefperiode voor die specifieke bestelling is beëindigd, indien van toepassing. Als u proefgebruik en -orders wilt bekijken, zoekt u de kolom **SKU-factureringstype** in de downloads en past u het filter toe om alleen de waarde 'Proefproef' weer te geven. Als u proefconversies wilt bekijken, zoekt u de kolom **Einddatum van de proefversie** in de downloads en past u het filter alleen toe om orders weer te geven wanneer de einddatum van de **proefversie** de datum van vandaag is afgelopen en de kolom **Einddatum annuleren** leeg of later is dan de einddatum van **het proces**.


<a name="when-is-my-monthly-payout-calculated"></a>Wanneer wordt mijn maandelijkse uitbetaling berekend
------------------------------------

Uw uitbetalingen worden aan u uitgegeven door de 15e van elke maand voor alle bedragen klaar voor uitbetaling door de laatste kalenderdag van de voorafgaande maand. Op de derde dag van de maand berekent Microsoft het uitbetalingsbedrag van de vorige maand en werkt microsoft alle toepasselijke kostentransacties in uw download bij met 'Aanstaande uitbetaling' in de kolom **Uitbetalingsstatus.** Deze transacties blijven in die staat totdat het betalingsverzoek naar uw bankrekening wordt verzonden, op welk moment hun **uitbetalingsstatus** wordt bijgewerkt naar 'Uitbetaald' en de uitbetalingsdatum wordt bijgewerkt om de datum weer te geven waarop we het betalingsverzoek bij uw bank hebben ingediend.


<a name="calculate-customer-acquisition-and-loss"></a>Klantacquisitie en -verlies berekenen
---------------------------------------

U de datum zien waarop de klant een van uw aanbiedingen voor het eerst heeft gekocht door de kolom **Datum verworven** in de klantdownload te vinden. Op dezelfde manier u de datum zien waarop ze geen aanbieding meer hebben gepubliceerd door u door de kolom **Verloren datum** in de klantdownload te vinden.


<a name="finding-more-help"></a>Meer hulp vinden
-----------------

- [Definities van verkopersinzichten](./si-insights-definitions-v4.md) - Definities zoeken voor statistieken en gegevens

- [Aan de slag met verkopersstatistieken](./si-getting-started.md) - Inleiding tot de functie Verkopersstatistieken.

