---
title: Problemen oplossen met het Azure-reserveringstype dat niet beschikbaar is
description: Dit artikel helpt u bij het begrijpen en oplossen van problemen met gereserveerde instanties die worden weergegeven als niet beschikbaar in de Azure Portal.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/27/2020
ms.openlocfilehash: 8575d9d86d8e720122a295cf92fa571ef33d5b4c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92798187"
---
# <a name="troubleshoot-reservation-type-not-available"></a>Problemen oplossen met niet-beschikbare reserveringstypen

Dit artikel helpt u bij het begrijpen en oplossen van problemen met gereserveerde instanties die worden weergegeven als niet beschikbaar in de Azure Portal.

## <a name="symptoms"></a>Symptomen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) en navigeer naar **Reserveringen**.
2. Selecteer **+ Toevoegen** en selecteer vervolgens een product.
3. Selecteer het tabblad **Alle producten**.
4. Selecteer een product in de lijst met producten. U ziet mogelijk een van de volgende berichten:
    - `Product unavailable for the selected subscription or region. Contact support.`  
        :::image type="content" source="./media/troubleshoot-product-not-available/product-unavailable-message.png" alt-text="Voorbeeld van het bericht Product niet beschikbaar voor het geselecteerde abonnement of de regio" lightbox="./media/troubleshoot-product-not-available/product-unavailable-message.png" :::
    - `The selected subscription does not have enough core quota remaining to purchase this product. Request quota increase`  
        :::image type="content" source="./media/troubleshoot-product-not-available/not-enough-core-quota-message.png" alt-text="Voorbeeld van het bericht over onvoldoende kernquotum" lightbox="./media/troubleshoot-product-not-available/not-enough-core-quota-message.png" :::

## <a name="cause"></a>Oorzaak

Sommige reserveringen zijn niet beschikbaar voor aankoop omdat:

- Niet elk gereserveerde instantie-product is beschikbaar voor aankoop in elke regio
- Uw abonnement heeft een quotumbeperking

### <a name="cause-1"></a>Oorzaak 1

Niet alle gereserveerde instantie-producten zijn beschikbaar voor aankoop. Azure beslist of sommige producten zijn toegestaan op basis van de kosten voor het leveren van de monetaire korting aan klanten. In andere gevallen biedt Azure sommige producten niet aan vanwege capaciteitsomstandigheden in specifieke datacenters. Daarnaast is het mogelijk dat bepaalde Azure-productontwikkelingsgroepen sommige producten nu niet toelaten omdat ze een ouder product buiten gebruik willen stellen.

In andere gevallen plaatst Azure capaciteitsbeperkingen op verschillende producten op basis van de vraag naar die resources in sommige regio's. Een dergelijke beperking kan bijvoorbeeld worden gemaakt wanneer de vraag naar een bepaalde VM-grootte in een datacentrum opraakt. In dit voorbeeld kan Azure de capaciteit niet garanderen aan klanten die een reservering hebben gekocht voor die grootte in die regio. Tenslotte zijn er enkele producten die om verschillende redenen uniek zijn. Dergelijke producten worden alleen beschikbaar gesteld aan een kleine, geselecteerde groep klanten.

Waarom worden sommige reserveringen weergegeven in Azure Portal als zij niet beschikbaar zijn voor aankoop? Het antwoord is omdat gebruikers ondersteuningsaanvragen maken waarin zij melden dat zij hun gewenste producten niet kunnen vinden. Het ontwikkelteam van Azure-reservering heeft vastgesteld dat het tonen van alle producten met het bericht `Product unavailable` resulteert in minder ondersteuningsaanvragen, dan als deze producten niet worden weergegeven.

### <a name="cause-2"></a>Oorzaak 2

Uw abonnement heeft een quotumbeperking. Abonnementen hebben limieten voor het aantal CPU-kernen dat ze kunnen gebruiken. De limiet geldt voor sommige gereserveerde instantie-producten, met name voor virtuele machines. Azure wil er zeker van zijn dat mensen die een gereserveerde instantie aanschaffen, deze kunnen gebruiken. Azure voert een eenvoudige, oppervlakkige controle uit in de Azure Portal om ervoor te zorgen dat u voldoende vrije kerngeheugens hebt in uw abonnement om de VM te implementeren, en dat u voordeel heeft bij de aankoop van de reservering.

De controle waarmee u een bepaald product kunt toevoegen aan uw winkelwagen en een reservering kunt aanschaffen is eenvoudig. Azure evalueert het totale aantal CPU-kernen dat beschikbaar is voor uw abonnement en controleert of het aantal groter is dan het aantal kernen voor het geselecteerde item.

Azure controleert niet het quotum voor het bereik van **Gedeelde** gereserveerde instanties. Het voordeel van de gereserveerde instantie voor het gedeelde bereik is van toepassing op alle abonnementen in de inschrijving. U kunt in Azure niet bepalen of u voldoende gratis kernen hebt voor al uw abonnementen om de resource te implementeren. Ongeacht het quotum kunt u met Azure altijd een VM-grootte selecteren wanneer het geselecteerde bereik wordt gedeeld.

Daarnaast voert Azure geen quotumcontrole uit voor **Aanbevolen** aankopen. Aanbevelingen zijn gebaseerd op actief gebruik. Azure veronderstelt dat u voldoende kernen hebt voor het uitvoeren van een specifieke VM-grootte omdat u het gebruik dat vereist is voor het maken van de aanbeveling al hebt gegenereerd.

## <a name="solution"></a>Oplossing

Afhankelijk van het foutbericht dat u hebt ontvangen, gebruikt u een van de volgende oplossingen voor uw probleem.

### <a name="solution-1"></a>Oplossing 1

Als u een bericht _Product niet beschikbaar_ ziet, selecteert u de link **Contact opnemen met ondersteuning** in het foutbericht om het toevoegen van een uitzondering voor uw abonnement aan te vragen. Uitzonderingen worden niet altijd toegekend.

### <a name="solution-2"></a>Oplossing 2

Als u een bericht _Niet genoeg kernquotum_ ziet, kunt u het bereik wijzigen naar **Gedeeld**. Nadat u de reservering hebt gekocht, kunt u het reserveringsbereik wijzigen van **Gedeeld** naar **Eén**.

Of selecteer de link **Quotumverhoging aanvragen** in het foutbericht om extra CPU-kernquotum voor uw abonnement aan te vragen.

## <a name="next-steps"></a>Volgende stappen

- Zie [Bereikreserveringen](prepare-buy-reservation.md#scope-reservations) voor meer informatie over opties voor reserveringsbereik.