---
title: Kosten optimaliseren voor Blob-opslag met gereserveerde capaciteit - Azure Storage
description: Meer informatie over de aankoop van gereserveerde capaciteit voor Azure Storage om kosten te besparen op blokblob- en Azure Data Lake Storage Gen2-resources.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: e73686629de8481f6a37e5bfafc9b723206b4853
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351034"
---
# <a name="optimize-costs-for-blob-storage-with-reserved-capacity"></a>Kosten optimaliseren voor blob-opslag met gereserveerde capaciteit

U geld besparen op opslagkosten voor blob-gegevens met gereserveerde capaciteit voor Azure Storage. De gereserveerde capaciteit voor Azure Storage biedt u een korting op de capaciteit voor blokblobs en voor Azure Data Lake Storage Gen2-gegevens in standaardopslagaccounts wanneer u een reservering voor een reservering van een jaar of drie jaar verbindt. Een reservering biedt een vaste hoeveelheid opslagcapaciteit voor de looptijd van de reservering.

De gereserveerde capaciteit voor Azure Storage kan uw capaciteitskosten voor blokblobs en Azure Data Lake Storage Gen2-gegevens aanzienlijk verlagen. De gerealiseerde kostenbesparingen zijn afhankelijk van de duur van uw reservering, de totale capaciteit die u wilt reserveren en de toegangslaag en het type redundantie die u hebt gekozen voor uw opslagaccount. Gereserveerde capaciteit biedt een factureringskorting en heeft geen invloed op de status van uw Azure Storage-bronnen.

Zie [Prijzen voor blok-blobs](https://azure.microsoft.com/pricing/details/storage/blobs/) en [Prijzen voor Azure Data Lake Storage Gen 2](https://azure.microsoft.com/pricing/details/storage/data-lake/) voor meer informatie over Azure Storage-reserveringsprijzen.

## <a name="reservation-terms-for-azure-storage"></a>Reserveringsvoorwaarden voor Azure Storage

In de volgende secties worden de voorwaarden van een Azure Storage-reservering beschreven.

### <a name="reservation-capacity"></a>Reserveringscapaciteit

U gereserveerde azure-opslagcapaciteit aanschaffen in eenheden van 100 TB en 1 PB per maand voor een periode van één of drie jaar.

### <a name="reservation-scope"></a>Reserveringsbereik

Gereserveerde azure-opslagcapaciteit is beschikbaar voor één abonnement of voor meerdere abonnementen (gedeelde scope). Wanneer de reserveringskorting wordt toegepast op één abonnement, wordt de reserveringskorting alleen toegepast op het geselecteerde abonnement. Wanneer de reserveringskorting wordt beperkt tot meerdere abonnementen, wordt deze gedeeld over deze abonnementen binnen de factureringscontext van de klant.

Wanneer u gereserveerde capaciteit voor Azure Storage aanschaft, u uw reservering gebruiken voor zowel blokblob- als Azure Data Lake Storage Gen2-gegevens. Er wordt een reservering toegepast op uw gebruik binnen het gekochte bereik en kan niet worden beperkt tot een specifiek opslagaccount, container of object binnen het abonnement. Een reservering kan niet worden verdeeld over meerdere abonnementen.

Een Azure Storage-reservering dekt alleen de hoeveelheid gegevens die is opgeslagen in een abonnement of gedeelde brongroep. Vroege verwijdering, bewerkingen, bandbreedte en kosten voor gegevensoverdracht zijn niet inbegrepen in de reservering. Zodra u een reservering koopt, worden de capaciteitskosten die overeenkomen met de reserveringskenmerken in rekening gebracht tegen de kortingstarieven in plaats van tegen de pay-as-you-go-tarieven. Zie [Wat zijn Azure-reserveringen?](/azure/billing/billing-save-compute-costs-reservations)

### <a name="supported-account-types-tiers-and-redundancy-options"></a>Ondersteunde accounttypen, lagen en redundantieopties

De gereserveerde capaciteit voor Azure Storage is beschikbaar voor resources in standaardopslagaccounts, waaronder v2-opslagaccounts voor algemene doeleinden.

Alle toegangsniveaus (hot, cool en archief) worden ondersteund voor reserveringen. Zie [Azure Blob-opslag: hot, cool en archieftoegangslagen voor](storage-blob-storage-tiers.md)meer informatie over toegangslagen.

Alle soorten redundantie worden ondersteund voor reserveringen. Zie [Azure Storage redundantie](../common/storage-redundancy.md)voor meer informatie over redundantieopties.

> [!NOTE]
> Gereserveerde azure-opslagcapaciteit is niet beschikbaar voor premium-opslagaccounts, GPv1-opslagaccounts voor algemene doeleinden, Azure Data Lake Storage Gen1, paginablobs, Azure Queue-opslag, Azure Table-opslag of Azure-bestanden.  

### <a name="security-requirements-for-purchase"></a>Beveiligingsvereisten voor aankoop

Ga als bedoeld als gevolg van de aankoop van gereserveerde capaciteit:

- U moet in de **rol Eigenaar** zijn voor ten minste één Enterprise- of individueel abonnement met betalen per gebruik.
- Voor Enterprise-abonnementen moet **Gereserveerde exemplaren** toevoegen zijn ingeschakeld in de EA-portal. Of als die instelling is uitgeschakeld, moet u een EA-beheerder op het abonnement zijn.
- Voor het CSP-programma (Cloud Solution Provider) kunnen alleen beheerdersof verkoopagenten gereserveerde azure klodderopslag kopen.

## <a name="determine-required-capacity-before-purchase"></a>Bepaal de vereiste capaciteit vóór aankoop

Wanneer u een Azure Storage-reservering koopt, moet u de optie regio, toegangsniveau en redundantie voor de reservering kiezen. Uw reservering is alleen geldig voor gegevens die zijn opgeslagen in die regio, toegangslaag en redundantieniveau. Stel dat u een reservering koopt voor gegevens in US West voor de hot tier met behulp van zoneredundante opslag (ZRS). U niet dezelfde reservering gebruiken voor gegevens in HET Oosten van de VS, gegevens in de archieflaag of gegevens in georedundante opslag (GRS). U echter wel een andere reservering kopen voor uw extra behoeften.  

Reserveringen zijn vandaag beschikbaar voor 100 TB of 1 PB blokken, met hogere kortingen voor 1 PB blokken. Wanneer u een reservering koopt in de Azure-portal, kan Microsoft u aanbevelingen geven op basis van uw vorige gebruik om te bepalen welke reservering u moet kopen.

## <a name="purchase-azure-storage-reserved-capacity"></a>Gereserveerde azure-opslagcapaciteit aanschaffen

U gereserveerde azure-opslagcapaciteit aanschaffen via de [Azure-portal.](https://portal.azure.com) Betaal vooraf of per maand voor de reservering. Zie [Azure-reserveringen kopen met vooraf of maandelijkse betalingen](/azure/billing/billing-monthly-payments-reservations)voor meer informatie over het kopen met maandelijkse betalingen.

Zie Inzicht in [de korting op de gereserveerde capaciteit](../../cost-management-billing/reservations/understand-storage-charges.md)voor Azure Storage voor hulp bij het identificeren van de reserveringsvoorwaarden die geschikt zijn voor uw scenario.

Volg de volgende stappen om gereserveerde capaciteit aan te schaffen:

1. Navigeer naar het deelvenster [Reserveringen kopen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) in de Azure-portal.  
1. Selecteer **Azure Blob Storage** om een nieuwe reservering te kopen.  
1. Vul de vereiste velden in zoals beschreven in de volgende tabel:

    ![Schermafbeelding van de aankoop van gereserveerde capaciteit](media/storage-blob-reserved-capacity/select-reserved-capacity.png)

   |Veld  |Beschrijving  |
   |---------|---------|
   |**Scope**   |  Geeft aan hoeveel abonnementen het factureringsvoordeel kunnen gebruiken dat aan de reservering is gekoppeld. Het bepaalt ook hoe de reservering wordt toegepast op specifieke abonnementen. <br/><br/> Als u **Gedeeld**selecteert, wordt de reserveringskorting toegepast op Azure Storage-capaciteit in een abonnement binnen uw factureringscontext. De factureringscontext is gebaseerd op de manier waarop u zich hebt aangemeld voor Azure. Voor zakelijke klanten is het gedeelde bereik de inschrijving en omvat het alle abonnementen binnen de inschrijving. Voor betalen per gebruik-klanten omvat het gedeelde bereik alle afzonderlijke abonnementen met betalen per gebruik-tarieven die door de accountbeheerder zijn gemaakt.  <br/><br/>  Als u **Eén abonnement selecteert,** wordt de reserveringskorting toegepast op Azure Storage-capaciteit in het geselecteerde abonnement. <br/><br/> Als u **Één resourcegroep**selecteert, wordt de reserveringskorting toegepast op azure-opslagcapaciteit in het geselecteerde abonnement en de geselecteerde resourcegroep binnen dat abonnement. <br/><br/> U het reserveringsbereik wijzigen nadat u de reservering hebt gekocht.  |
   |**Abonnement**  | Het abonnement dat wordt gebruikt om te betalen voor de Azure Storage-reservering. De betaalmethode op het geselecteerde abonnement wordt gebruikt bij het in rekening brengen van de kosten. Het abonnement moet een van de volgende typen zijn: <br/><br/>  Enterprise Agreement (aanbiedingsnummers: MS-AZR-0017P of MS-AZR-0148P): Voor een Enterprise-abonnement worden de kosten in mindering gebracht op het monetaire toezeggingssaldo van de inschrijving of als overschrijding in rekening gebracht. <br/><br/> Individueel abonnement met betalen per gebruik -tarieven (aanbiedingsnummers: MS-AZR-0003P of MS-AZR-0023P): Voor een individueel abonnement met betalen per gebruik worden de kosten in rekening gebracht op de creditcard- of factuurbetaalmethode op het abonnement.    |
   | **Regio** | De regio waar de reservering van kracht is. |
   | **Toegangslaag** | De toegangslaag waarvoor de reservering van kracht is. Opties zijn *Hot,* *Cool*of *Archive*. Zie [Azure Blob-opslag: hot, cool en archieftoegangslagen voor](storage-blob-storage-tiers.md)meer informatie over toegangslagen. |
   | **Redundantie** | De redundantieoptie voor de reservering. Opties zijn *LRS,* *ZRS,* *GRS*en *RA-GZRS*. Zie [Azure Storage redundantie](../common/storage-redundancy.md)voor meer informatie over redundantieopties. |
   | **Factureringsfrequentie** | Geeft aan hoe vaak de rekening wordt gefactureerd voor de reservering. Opties zijn *maandelijks* of *vooraf*. |
   | **Grootte** | De regio waar de reservering van kracht is. |
   |**Termijn**  | Een jaar of drie jaar.   |

1. Nadat u de parameters voor uw reservering hebt geselecteerd, worden de kosten in de Azure-portal weergegeven. De portal toont ook het kortingspercentage ten opzichte van pay-as-you-go facturering.

1. Bekijk in het deelvenster **Reserveringen kopen** de totale kosten van de reservering. U ook een naam opgeven voor de reservering.

    ![Schermafbeelding van het kopen van een reservering](media/storage-blob-reserved-capacity/purchase-reservations.png)

Nadat u een reservering hebt gekocht, wordt deze automatisch toegepast op bestaande Azure Storage Block-opslagblokkeringen of Azure Data Lake Storage Gen2-resources die overeenkomen met de voorwaarden van de reservering. Als u nog geen Azure Storage-bronnen hebt gemaakt, is de reservering van toepassing wanneer u een resource maakt die overeenkomt met de voorwaarden van de reservering. In beide gevallen begint de looptijd van de reservering onmiddellijk na een succesvolle aankoop.

## <a name="exchange-or-refund-a-reservation"></a>Een reservering omruilen of terugbetalen

U een reservering inwisselen of terugbetalen, met bepaalde beperkingen. Deze beperkingen worden beschreven in de volgende secties.

Als u een reservering wilt ruilen of terugbetalen, navigeert u naar de reserveringsgegevens in de Azure-portal. Selecteer **Ruilen** of **Terugbetaling**en volg de instructies om een ondersteuningsverzoek in te dienen. Wanneer het verzoek is verwerkt, stuurt Microsoft u een e-mail om de voltooiing van het verzoek te bevestigen.

Zie [Selfservice-uitwisselingen en terugbetalingen voor Azure-reserveringen voor](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)meer informatie over azure-reserveringen.

### <a name="exchange-a-reservation"></a>Een reservering ruilen

Het uitwisselen van een reservering stelt u in staat om een prorata restitutie te ontvangen op basis van het ongebruikte gedeelte van de reservering. U de terugbetaling vervolgens toepassen op de aankoopprijs van een nieuwe Azure Storage-reservering.

Er is geen limiet aan het aantal uitwisselingen dat u maken. Bovendien zijn er geen kosten verbonden aan een uitwisseling. De nieuwe reservering die u koopt, moet even veel of meer waard zijn dan het geschatte tegoed van de oorspronkelijke reservering. Een Azure Storage-reservering kan alleen worden ingewisseld voor een andere Azure Storage-reservering en niet voor een reservering voor een andere Azure-service.

### <a name="refund-a-reservation"></a>Een reservering terugbetalen

U op elk gewenst moment een Azure Storage-reservering annuleren. Wanneer u annuleert, ontvangt u een prorata-terugbetaling op basis van de resterende looptijd van de reservering, minus een vergoeding voor vervroegde beëindiging van 12 procent. De maximale restitutie per jaar is $ 50.000.

Als u een reservering annuleert, wordt de reservering onmiddellijk beëindigd en wordt de resterende maanden teruggekeerd naar Microsoft. Het resterende proratasaldo, minus de vergoeding, wordt terugbetaald aan uw oorspronkelijke aankoopvorm.

## <a name="expiration-of-a-reservation"></a>Het verstrijken van een reservering

Wanneer een reservering verloopt, wordt de azure-opslagcapaciteit die u onder die reservering gebruikt, gefactureerd tegen het tarief voor betalen per gebruik. Reserveringen worden niet automatisch vernieuwd.

U ontvangt 30 dagen voor het verstrijken van de reservering een e-mailmelding en opnieuw op de vervaldatum. Als u wilt blijven profiteren van de kostenbesparingen die een reservering biedt, verlengt u deze niet later dan de vervaldatum.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [maakt u een ondersteuningsverzoek.](https://go.microsoft.com/fwlink/?linkid=2083458)

## <a name="next-steps"></a>Volgende stappen

- [Wat zijn Azure-reserveringen?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Leer hoe de reserveringskorting wordt toegepast op Azure Storage](../../cost-management-billing/reservations/understand-storage-charges.md)
