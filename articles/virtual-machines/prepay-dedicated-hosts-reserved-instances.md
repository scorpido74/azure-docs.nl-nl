---
title: Vooraf betalen voor Azure Dedicated Hosts om geld te besparen
description: Meer informatie over het kopen van Gereserveerde Azure-hosts om te besparen op uw rekenkosten.
services: virtual-machines
author: yashar
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 57123abfe7f343a75d264d43afb88f9de1409e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78207743"
---
# <a name="save-costs-with-a-reserved-instance-of-azure-dedicated-hosts"></a>Kosten besparen met een gereserveerdexemplaar van Azure Dedicated Hosts

Wanneer u zich verbindt met een gereserveerdexemplaar van Azure Dedicated Hosts, u geld besparen. De reserveringskorting wordt automatisch toegepast op het aantal lopende speciale hosts dat overeenkomt met het reserveringsbereik en de kenmerken. U hoeft geen reservering toe te wijzen aan een speciale verhuurder om de kortingen te krijgen. Een gereserveerde instantieaankoop dekt alleen het rekengedeelte van uw gebruik en omvat softwarelicentiekosten. Zie het [overzicht van Azure Dedicated Hosts voor virtuele machines.](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)

## <a name="determine-the-right-dedicated-host-sku-before-you-buy"></a>Bepaal de juiste dedicated host SKU voordat je koopt


Voordat u een reservering koopt, moet u bepalen welke speciale host u nodig hebt. Een SKU is gedefinieerd voor een speciale host die de VM-serie en het type vertegenwoordigt. 

Begin met het doornemen van de ondersteunde formaten voor [Windows virtuele machine](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) of [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) om de VM-serie te identificeren.

Controleer vervolgens of het wordt ondersteund op Azure Dedicated Hosts. [De prijspagina van Azure Dedicated Hosts](https://aka.ms/ADHPricing) bevat de volledige lijst met speciale hosts SKU's, hun CPU-informatie en verschillende prijsopties (inclusief gereserveerde exemplaren).

Mogelijk worden er verschillende SKU's gevonden die u VM-reeksen ondersteunen (met verschillende typen). Identificeer de beste SKU door de capaciteit van de host te vergelijken (aantal vCPU's). Houd er rekening mee dat u reserveren op meerdere dedicated hosts SKU's die dezelfde VM-serie ondersteunen (bijvoorbeeld DSv3_Type1 en DSv3_Type2), maar niet in verschillende VM-series (zoals DSv3 en ESv3).



## <a name="purchase-restriction-considerations"></a>Overwegingen voor aankoopbeperking

Gereserveerde exemplaren zijn beschikbaar voor de meeste speciale hostformaten, met enkele uitzonderingen.

Reserveringskortingen gelden niet voor het volgende:

- **Clouds** - Reserveringen zijn niet beschikbaar voor aankoop in Duitsland of China regio's.

- **Onvoldoende quotum** - Een reservering die is beperkt tot één abonnement, moet vCPU-quotum beschikbaar hebben in het abonnement voor het nieuwe gereserveerde exemplaar. Als het doelabonnement bijvoorbeeld een quotumlimiet van 10 vCPU's voor DSv3-serie heeft, u geen reserveringsdedicated hosts kopen die deze serie ondersteunen. De quotacontrole voor reserveringen omvat de VM's en speciale hosts die al in het abonnement zijn geïmplementeerd. U [aanvragen voor quotaverhoging maken](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) om dit probleem op te lossen.

- **Capaciteitsbeperkingen** - In zeldzame omstandigheden beperkt Azure de aankoop van nieuwe reserveringen voor subset van dedicated host SKU's vanwege de lage capaciteit in een regio.

## <a name="buy-a-reservation"></a>Een reservering kopen

U een gereserveerdexemplaar van een Azure Dedicated Host-exemplaar kopen in de [Azure-portal.](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)

Betaal voor de reservering [vooraf of met maandelijkse betalingen](https://docs.microsoft.com/azure/billing/billing-monthly-payments-reservations). Deze vereisten zijn van toepassing op het kopen van een gereserveerde dedicated host-instantie:

- Je moet in een eigenaarrol zitten voor ten minste één EA-abonnement of een abonnement met een betalen per gebruik-tarief.

- Voor EA-abonnementen moet de optie **Gereserveerde instanties** toevoegen zijn ingeschakeld in de [EA-portal.](https://ea.azure.com/) Of, als deze instelling is uitgeschakeld, moet u een EA-beheerder van het abonnement zijn.

- Voor het CSP-programma (Cloud Solution Provider) kunnen alleen de beheerders of verkoopagenten reserveringen kopen.

Een exemplaar kopen:

1. Meld u aan bij de  [Azure-portal](https://portal.azure.com/).

2. Selecteer **Alle services** \> **Reserveringen**.

3. Selecteer **Toevoegen** om een nieuwe reservering te kopen en klik op **Dedicated Hosts**.

4. Vul de verplichte velden in. Het uitvoeren van dedicated hosts-exemplaren die overeenkomen met de kenmerken die u selecteert, komen in aanmerking om de reserveringskorting te krijgen. Het werkelijke aantal van uw Dedicated Host-exemplaren dat de korting krijgt, is afhankelijk van het geselecteerde bereik en het geselecteerde aantal.

Als u een EA-overeenkomst hebt, u de **optie** Meer toevoegen gebruiken om snel extra exemplaren toe te voegen. De optie is niet beschikbaar voor andere abonnementstypen.

| **Veld**           | **Beschrijving**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Abonnement        | Het abonnement werd gebruikt om te betalen voor de reservering. De betalingsmethode op het abonnement wordt in rekening gebracht de kosten voor de reservering. Het abonnementstype moet een ondernemingsovereenkomst zijn (aanbiedingsnummers: MS-AZR-0017P of MS-AZR-0148P) of Microsoft Customer Agreement of een individueel abonnement met pay-as-you-go-tarieven (aanbiedingsnummers: MS-AZR-0003P of MS-AZR-0023P). De kosten worden in mindering gebracht op het monetaire vastleggingssaldo, indien beschikbaar, of in rekening gebracht als overschrijding. Voor een abonnement met betalen per gebruik worden de kosten in rekening gebracht op de creditcard- of factuurbetalingsmethode van het abonnement. |
| Bereik               | Het bereik van de reservering kan betrekking hebben op één abonnement of meerdere abonnementen (gedeeld bereik). Als u:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| Regio              | De Azure-regio die onder de reservering valt.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Speciale hostgrootte | De grootte van de exemplaren Dedicated Host.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Termijn                | Een jaar of drie jaar.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Aantal            | Het aantal exemplaren dat binnen de reservering wordt gekocht. Het aantal beschikbare hostexemplaren dat de factureringskorting kan krijgen, is het aantal hostexemplaren dat wordt uitgevoerd.                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

- **Bereik van één resourcegroep** : hiermee past u alleen de reserveringskorting toe op de overeenkomende resources in de geselecteerde resourcegroep.

- **Eén abonnementsbereik** : past de reserveringskorting toe op de overeenkomende resources in het geselecteerde abonnement.

- **Gedeeld bereik** : past de reserveringskorting toe op het afstemmen van resources op in aanmerking komende abonnementen die zich in de factureringscontext bevinden. Voor EA-klanten is de factureringscontext de inschrijving. Voor afzonderlijke abonnementen met tarieven voor betalen per gebruik is het factureringsbereik alle in aanmerking komende abonnementen die zijn gemaakt door de accountbeheerder.

## <a name="usage-data-and-reservation-utilization"></a>Gebruiksgegevens en reserveringsgebruik

Uw gebruiksgegevens kosten niets voor het deel waarvoor u een reserveringskorting verkrijgt. U zien welke VM-instantie de reserveringskorting voor elke reservering heeft ontvangen.

Zie [Azure-reserveringsgebruik voor uw Enterprise-inschrijving](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) begrijpen als u een EA-klant bent voor meer informatie over hoe reserveringskortingen worden weergegeven in gebruiksgegevens. Zie [Azure-reserveringsgebruik voor uw abonnement voor betalen per gebruik begrijpen](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)als u een individueel abonnement hebt.

## <a name="change-a-reservation-after-purchase"></a>Een reservering wijzigen na aankoop

Na aankoop kunt u de volgende typen wijzigingen aanbrengen in een reservering:

- Het bereik van de reservering bijwerken

- Flexibiliteit bij instantiegrootte (indien van toepassing)

- Eigendom

U een reservering ook splitsen in kleinere segmenten en al gesplitste reserveringen samenvoegen. Geen van de wijzigingen veroorzaakt een nieuwe commerciële transactie of wijzigt de einddatum van de reservering.

U de volgende typen wijzigingen na aankoop niet rechtstreeks aanbrengen:

- De regio van een bestaande reservering

- SKU

- Aantal

- Duur

U echter wel een reservering *wisselen* als u wijzigingen wilt aanbrengen.

## <a name="cancel-exchange-or-refund-reservations"></a>Annulering, omwisseling of terugbetaling van reserveringen

Annulering, ruiling of terugbetaling van reserveringen is mogelijk met bepaalde beperkingen. Zie [Selfservice-uitwisselingen en terugbetalingen voor Azure-reserveringen voor](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund)meer informatie.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [maakt u een ondersteuningsverzoek.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Volgende stappen

Zie [Azure-reserveringen beheren](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)voor meer informatie over het beheren van een reservering.

Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:

- [Wat zijn Azure-reserveringen?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)

- [Azure Dedicated Hosts gebruiken](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)

- [Speciale verhuurdersprijzen](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Reserveringen beheren in Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)

- [Begrijpen hoe de reserveringskorting wordt toegepast](https://docs.microsoft.com/azure/billing/billing-understand-vm-reservation-charges)

- [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)

- [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)

- [Windows-softwarekosten niet inbegrepen bij reserveringen](https://docs.microsoft.com/azure/billing/billing-reserved-instance-windows-software-costs)

- [Azure-reserveringen in het CSP-programma (Cloud Solution Provider) van het Partnercentrum](https://docs.microsoft.com/partner-center/azure-reservations)


