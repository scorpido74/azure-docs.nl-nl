---
title: Vooruitbetalen voor met Azure toegewezen hosts om geld te besparen
description: Meer informatie over het kopen van gereserveerde instanties voor Azure-hosts om uw reken kosten op te slaan.
services: virtual-machines
author: yashar
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 57123abfe7f343a75d264d43afb88f9de1409e8a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78207743"
---
# <a name="save-costs-with-a-reserved-instance-of-azure-dedicated-hosts"></a>Bespaar kosten met een gereserveerd exemplaar van voor Azure toegewezen hosts

Wanneer u een gereserveerd exemplaar van voor Azure toegewezen hosts doorvoert, kunt u geld besparen. De reserverings korting wordt automatisch toegepast op het aantal actieve toegewezen hosts dat overeenkomt met het reserverings bereik en de kenmerken. U hoeft geen reserve ring aan een speciale host toe te wijzen om de kortingen te krijgen. Een gereserveerde instantie koopt alleen het reken onderdeel van uw gebruik en bevat software licentie kosten. Zie het [overzicht van de voor Azure toegewezen hosts voor virtuele machines](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts).

## <a name="determine-the-right-dedicated-host-sku-before-you-buy"></a>Bepaal de juiste specifieke host-SKU voordat u deze aanschaft


Voordat u een reserve ring koopt, moet u bepalen welke specifieke host u nodig hebt. Er wordt een SKU gedefinieerd voor een toegewezen host die de VM-reeksen en het type vertegenwoordigt. 

Ga eerst naar de ondersteunde grootten voor [virtuele Windows-machines](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) of [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) om de VM-serie te identificeren.

Controleer vervolgens of het wordt ondersteund op met Azure toegewezen hosts. De pagina prijs informatie voor [Azure dedicated hosts](https://aka.ms/ADHPricing) bevat de volledige lijst met gereserveerde hosts sku's, hun CPU-gegevens en diverse prijs opties (inclusief gereserveerde instanties).

Mogelijk vindt u verschillende Sku's die u de VM-serie ondersteunen (met verschillende typen). Identificeer de beste SKU door de capaciteit van de host te vergelijken (aantal Vcpu's). Houd er rekening mee dat u de reserve ring kunt Toep assen op meerdere toegewezen hosts Sku's die dezelfde VM-serie ondersteunen (bijvoorbeeld DSv3_Type1 en DSv3_Type2), maar niet in de verschillende VM-reeksen (zoals DSv3 en ESv3).



## <a name="purchase-restriction-considerations"></a>Overwegingen voor de aankoop beperking

Gereserveerde instanties zijn beschikbaar voor de meeste specifieke hostnamen, met enkele uitzonde ringen.

Er gelden geen reserve ring kortingen voor het volgende:

- **Clouds** -reserve ringen zijn niet beschikbaar voor aankopen in de regio's Duitsland en China.

- **Onvoldoende quotum** : voor een reserve ring die is toegewezen aan één abonnement, moet vCPU quotum beschikbaar zijn in het abonnement voor het nieuwe gereserveerde exemplaar. Als het doel abonnement bijvoorbeeld een quotum limiet van 10 Vcpu's voor DSv3-Series heeft, kunt u geen gereserveerde gereserveerde hosts die deze serie ondersteunen, kopen. De quotum controle voor reserve ringen omvat de Vm's en toegewezen hosts die al zijn geïmplementeerd in het abonnement. U kunt een  [aanvraag voor quotum verhoging maken](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)om dit probleem op te lossen.

- **Capaciteits beperkingen** : in zeldzame omstandigheden beperkt Azure de aanschaf van nieuwe reserve ringen voor subset van speciale host-sku's, vanwege een geringe capaciteit in een regio.

## <a name="buy-a-reservation"></a>Een reservering kopen

U kunt een gereserveerd exemplaar van een voor Azure toegewezen host-exemplaar kopen in de [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Betaal voor de reserve ring [vóór of met maandelijkse betalingen](https://docs.microsoft.com/azure/billing/billing-monthly-payments-reservations). Deze vereisten zijn van toepassing op het kopen van een gereserveerde, toegewezen host-instantie:

- U moet een rol van eigenaar zijn voor ten minste één EA-abonnement of een abonnement met een betalen naar gebruik-tarief.

- Voor EA-abonnementen moet de optie **gereserveerde instanties** toevoegen zijn ingeschakeld in de [EA-Portal](https://ea.azure.com/). Of, als deze instelling is uitgeschakeld, moet u een EA-beheerder van het abonnement zijn.

- Voor het programma Cloud Solution Provider (CSP) kunnen alleen de beheerders of verkoop medewerkers reserve ringen kopen.

Een exemplaar kopen:

1. Meld u aan bij de  [Azure-portal](https://portal.azure.com/).

2. Selecteer **alle services** \>- **reserve ringen**.

3. Selecteer **toevoegen** om een nieuwe reserve ring te kopen en klik vervolgens op **toegewezen hosts**.

4. Vul de verplichte velden in. Het uitvoeren van speciale hosts-instanties die overeenkomen met de kenmerken die u selecteert, komen in aanmerking voor de reserverings korting. Het werkelijke aantal toegewezen host-instanties dat de korting krijgt, is afhankelijk van het bereik en de geselecteerde hoeveelheid.

Als u een EA-overeenkomst hebt, kunt u de **optie meer toevoegen**gebruiken om snel extra instanties toe te voegen. De optie is niet beschikbaar voor andere typen abonnementen.

| **Veld**           | **Beschrijving**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Abonnement        | Het abonnement dat wordt gebruikt om te betalen voor de reserve ring. De betalings wijze voor het abonnement wordt in rekening gebracht voor de kosten voor de reserve ring. Het abonnements type moet een Enter prise Agreement zijn (nummer van de aanbieding: MS-AZR-0017P of MS-AZR-0148P) of een micro soft-klant overeenkomst of een afzonderlijk abonnement met betalen per gebruik-tarieven (aanbiedings nummers: MS-AZR-0003P of MS-AZR-0023P). De kosten worden afgetrokken van het saldo van de monetaire toezeg ging, indien beschikbaar, of in rekening gebracht als overschrijding. Voor een abonnement met betalen per gebruik-tarieven worden de kosten in rekening gebracht op basis van de credit card-of factuur betalings methode voor het abonnement. |
| Bereik               | Het bereik van de reserve ring kan betrekking hebben op één abonnement of meerdere abonnementen (gedeeld bereik). Als u het volgende selecteert:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| Regio              | De Azure-regio die wordt gedekt door de reserve ring.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Toegewezen grootte van host | De grootte van de toegewezen exemplaren van de host.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Termijn                | Eén jaar of drie jaar.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Aantal            | Het aantal exemplaren dat wordt aangeschaft binnen de reserve ring. De hoeveelheid is het aantal actieve toegewezen instanties dat de facturerings korting kan krijgen.                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

- **Bereik van één resource groep** : past de reserverings korting alleen toe op de overeenkomende resources in de geselecteerde resource groep.

- **Bereik van één abonnement** : past de reserverings korting toe op de overeenkomende resources in het geselecteerde abonnement.

- **Gedeeld bereik** : past de reserverings korting toe op overeenkomende resources in in aanmerking komende abonnementen in de facturerings context. Voor EA-klanten is de facturerings context de inschrijving. Voor afzonderlijke abonnementen met tarieven voor betalen per gebruik is het factureringsbereik alle in aanmerking komende abonnementen die zijn gemaakt door de accountbeheerder.

## <a name="usage-data-and-reservation-utilization"></a>Gebruiks gegevens en reserverings gebruik

Uw gebruiksgegevens kosten niets voor het deel waarvoor u een reserveringskorting verkrijgt. U kunt zien welke VM-instantie de reserverings korting voor elke reserve ring heeft ontvangen.

Zie het [gebruik van Azure-reserve ringen voor uw Enter prise-inschrijving](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) als u een EA-klant bent voor meer informatie over hoe reserverings kortingen worden weer gegeven in gebruiks gegevens. Zie [het gebruik van Azure-reserve ringen voor uw abonnement op basis van betalen per gebruik voor meer informatie](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)over een afzonderlijk abonnement.

## <a name="change-a-reservation-after-purchase"></a>Een reserve ring wijzigen na aankoop

Na aankoop kunt u de volgende typen wijzigingen aanbrengen in een reservering:

- Het bereik van de reservering bijwerken

- Flexibiliteit van de instantie grootte (indien van toepassing)

- Eigendom

U kunt ook een reserve ring in kleinere segmenten splitsen en al gesplitste reserve ringen samen voegen. Geen van de wijzigingen veroorzaakt een nieuwe commerciële trans actie of wijzigt de eind datum van de reserve ring.

U kunt de volgende typen wijzigingen na de aankoop niet rechtstreeks aanbrengen:

- Een bestaande reserverings regio

- SKU

- Aantal

- Duur

U kunt echter wel een reserve ring *uitwisselen* als u wijzigingen wilt aanbrengen.

## <a name="cancel-exchange-or-refund-reservations"></a>Annulering, omwisseling of terugbetaling van reserveringen

Annulering, ruiling of terugbetaling van reserveringen is mogelijk met bepaalde beperkingen. Zie [self-service-uitwisseling en terugbetalingen voor Azure Reservations](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund)voor meer informatie.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt,  [kunt u een ondersteuningsaanvraag maken](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Reservations beheren](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)voor meer informatie over het beheren van een reserve ring.

Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:

- [Wat zijn Azure Reservations?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)

- [Azure Dedicated Host gebruiken](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)

- [Prijzen van Dedicated Host](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Reserveringen beheren in Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)

- [Begrijpen hoe de reserveringskorting wordt toegepast](https://docs.microsoft.com/azure/billing/billing-understand-vm-reservation-charges)

- [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)

- [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)

- [Kosten van Windows-software zijn niet inbegrepen in reserveringen](https://docs.microsoft.com/azure/billing/billing-reserved-instance-windows-software-costs)

- [Azure-reserveringen in het CSP-programma (Cloud Solution Provider) van het Partnercentrum](https://docs.microsoft.com/partner-center/azure-reservations)


