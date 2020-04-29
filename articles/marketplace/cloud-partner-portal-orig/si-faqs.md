---
title: Veelgestelde vragen over verkopers inzichten
description: Veelgestelde vragen over de functie voor de verkoop inzichten van de Cloud Partner-portal.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 011558baa43ee3db2803e9229d1d15df5158d668
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80285381"
---
<a name="seller-insights-faq"></a>Veelgestelde vragen over verkopers inzichten
===================

In dit artikel vindt u richt lijnen voor algemene gebruikers procedures in en vragen over de verkoop inzichten.


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>Definities voor de waarden in het gedownloade transactie bestand zoeken
------------------------------------------------------------------

De definities van de metrische waarden in het transactie bestand vindt u in het artikel [verkoper Insights-definities](./si-insights-definitions-v4.md).


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>Bekijk klant gegevens van trans acties waarvoor ik betaald ben
-------------------------------------------------------------

Nadat u uw trans acties hebt gedownload vanuit de uitzonderings module, zoekt u de kolom met de **status uitbetaling**en past u het filter toe op alleen de waarde ' betaald uit ' weer te geven. De volgende kolommen worden weer gegeven met de klant Details: **Bedrijfs naam**, **klant-e-mail**, **klant land**, **klant status**en **Post code**van de klant.


<a name="calculate-my-open-accounts-receivable"></a>Mijn openstaande debiteuren te berekenen
-------------------------------------

Nadat u uw trans acties hebt gedownload vanuit de uitzonderings module, zoekt u de kolom met de **status uitbetaling**en past u het filter toe zodat alleen de waarde ' aanstaande uitbetaling ' en ' niet gereed voor betaling ' worden weer gegeven. Vervolgens wordt de kolom met het label **uitbetaling (PC)** opgeteld.


<a name="calculate-revenue-by-customer-usage-period"></a>Opbrengst berekenen op basis van gebruiks periode van klant
------------------------------------------

Nadat u uw trans acties hebt gedownload vanuit de uitbetalings module, zoekt u de kolom met de naam **transactie status**en filtert u de waarde betaald.   Voor elke trans actie die wordt vermeld, vertegenwoordigt de kolom met het label **uitbetaling (PC)** de hoeveelheid die u hebt betaald.  Als u de gebruiks periode wilt schatten die aan de trans actie is gekoppeld, gebruikt u de kolom **kosten datum**, een nauw keurige benadering van de laatste dag van gebruik voor de periode waarop de trans actie van toepassing is.


<a name="calculate-your-bad-debt"></a>Uw slechte schulden berekenen
---------------------

Nadat u uw trans acties hebt gedownload vanuit de uitzonderings module, zoekt u de kolom met de **status definitieve verzameling**en past u het filter toe zodat alleen de waarde ' afschrijven ' wordt weer gegeven. Vervolgens wordt de kolom met het label **uitbetaling (PC)** opgeteld.


<a name="view-payout-or-customer-contact-information"></a>Contact gegevens van de uitbetaling of klant weer geven
-------------------------------------------

Meld u aan als een gebruiker met de rol ' eigenaar ' en niet de rol ' Inzender '. Alleen de rol van eigenaar krijgt de uitbetalings-en klant gegevens te zien. Meer informatie over gebruikers rollen vindt u in het artikel [gebruikers beheren](./cloud-partner-portal-manage-users.md).


<a name="calculate-my-advance-payouts"></a>Mijn voor schot uitbetalingen berekenen
----------------------------

Nadat u uw trans acties hebt gedownload vanuit de uitzonderings module, zoekt u de kolom met het label **transactie type**en past u het filter toe zodat alleen de waarde ' kosten ' wordt weer gegeven. Zoek vervolgens de kolom met de naam **definitieve verzameling**en pas het filter toe om alleen de waarde ' wordt uitgevoerd ' weer te geven. Ten slotte moet u de kolom **uitbetalings bedrag (PC)** opsommen om alle voor uitbetalingen te berekenen die u vóór het verzamelen van de klant hebt betaald.


<a name="calculate-customer-refunds"></a>Klant restituties berekenen
--------------------------

Nadat u uw trans acties hebt gedownload vanuit de uitzonderings module, zoekt u de kolom met de **status definitieve verzameling**en past u het filter toe om alleen de waarde "restitutie" weer te geven. De kolom **kosten bedrag (PC)** opsommen om alle verwerkte restituties voor uw klanten te berekenen.


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>Bepalen welke trans acties betrokken zijn bij een micro soft Channel-partner
----------------------------------------------------------------

Alle trans acties in de kolom **Azure-licentie type** die worden gefilterd om de waarden ' Enter prise through Reseller ' en ' Cloud solution provider ' te laten zien, zijn een micro soft Channel-partner. Voor meer informatie over de partner kunt u de **naam** en het **e-mail adres** van de wederverkoper vinden in de module uitgaand downloaden en de module klant downloaden.


<a name="identify-trial-usage-and-trial-conversions"></a>Proef gebruik en evaluatie versies identificeren
------------------------------------------

Down loads van de module volg orde, gebruik en uitbetalingen bevatten nu de **eind datum van de proef versie** om u te helpen begrijpen wanneer de proef periode voor die specifieke order is beëindigd, indien van toepassing. Als u het gebruik van de proef versie en de orders wilt bekijken, gaat u naar de kolom **SKU-factuur type** in de down loads en past u het filter toe om alleen de waarde ' trial ' weer te geven. Als u proef conversies wilt bekijken, zoekt u de kolom **proef einddatum** op in de down loads en past u het filter toe om alleen orders weer te geven wanneer de **eind datum van de proef periode** van vandaag is en de kolom **annulerings** datum leeg of later is dan de **eind datum van de proef versie**.


<a name="when-is-my-monthly-payout-calculated"></a>Wanneer is mijn maandelijkse betaling berekend
------------------------------------

Uw uitbetalingen worden aan u verleend door de 15e van elke maand voor alle bedragen die gereed zijn voor betaling op de laatste dag van de vorige maand. Op de derde dag van de maand berekent micro soft de uitbetalings hoeveelheid van de vorige maand en worden alle toepasselijke kosten transacties in uw down load bijgewerkt met ' aanstaande uitbetaling ' in de kolom **uitbetalings status** . Deze trans acties blijven in die staat totdat de betalings aanvraag wordt verzonden naar uw bank rekening, op voor waarde dat de **status** van de betaling wordt bijgewerkt naar ' betaald ', en de ' uitbetalings datum ' wordt bijgewerkt om de datum weer te geven waarop we de betalings aanvraag naar uw bank hebben verzonden.


<a name="calculate-customer-acquisition-and-loss"></a>Aankoop en verlies van klanten berekenen
---------------------------------------

U kunt de datum weer geven waarop de klant voor het eerst een van uw aanbiedingen heeft gekocht door de kolom **aanschaf datum** te zoeken in het downloaden van de klant. Op dezelfde manier kunt u de datum zien waarna deze geen aanbieding meer heeft gepubliceerd door u door de kolom **date verloren** te vinden in de down load van de klant.


<a name="finding-more-help"></a>Meer hulp zoeken
-----------------

- [Insights-definities voor verkopers](./si-insights-definitions-v4.md) -Zoek definities voor metrieken en gegevens

- Aan de [slag met de verkoop inzichten](./si-getting-started.md) : Inleiding tot de functie voor de verkoop inzichten.

