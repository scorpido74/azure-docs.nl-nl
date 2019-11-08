---
title: Tabblad virtual machine Sku's in de Cloud Partner-portal voor Azure Marketplace
description: Hierin wordt het tabblad Sku's beschreven dat wordt gebruikt voor het maken van een virtuele machine aanbieding in azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: 101f928e296d0b48b4e077676393772829aa76ab
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808972"
---
# <a name="virtual-machine-skus-tab"></a>Tabblad virtual machine Sku's

Op het tabblad **sku's** van de pagina **nieuwe aanbieding** kunt u een of meer sku's maken en deze koppelen aan uw nieuwe aanbieding.  Verschillende Sku's kunnen een oplossing onderscheiden van functie sets, VM-installatie kopieën, door Voer of schaal baarheid, facturerings modellen of een ander kenmerk.


## <a name="create-a-sku"></a>Een SKU maken

In eerste instantie heeft een nieuwe aanbieding geen gekoppelde Sku's, zodat u er een kunt maken door te klikken op **nieuwe SKU**.

![Nieuwe SKU-knop op het tabblad nieuwe aanbieding voor virtuele machines](./media/publishvm_005.png)

<br/>

Het dialoog venster **nieuwe SKU** wordt weer gegeven.  Voer de id in voor de nieuwe SKU en klik vervolgens op **OK**. (Zie hieronder voor id-naamgevings conventies.)  Op het tabblad **sku's** worden nu de velden weer gegeven die kunnen worden bewerkt.    Een toegevoegd sterretje (*) op de veld naam geeft aan dat deze vereist is.

<!-- TD: This tab has been updated, now has "Old Pricing" and "Simplified Currency Pricing" sections"! -->

![Tabblad SKU in het formulier nieuwe aanbieding voor virtuele machines](./media/publishvm_006.png)

In de volgende tabel worden het doel, de inhoud en de opmaak van deze velden beschreven.  Vereiste velden worden gedicteerd door een asterisk (*).

<!-- TD: I took a new screenshot, and the fields differ somewhat from description in the VM Pub Guide.  Needs review. -->

|  **Veld**       |     **Beschrijving**                                                          |
|  ---------       |     ---------------                                                          |
|  *SKU-instellingen*   |    |
| **SKU-ID\***       | Id voor deze SKU.  Deze naam mag Maxi maal 50 tekens bevatten, die bestaat uit kleine letters of streepjes (-), maar niet eindigen met een streepje.  Het kan niet worden gewijzigd nadat de aanbieding is gepubliceerd.  |
|  *SKU-gegevens*   |  |
| **Titel\***        | Beschrijvende naam voor de aanbieding voor weer gave in de Marketplace. Maximale lengte van 50 tekens. |
| **Overzicht\***      | Beknopt overzicht van de aanbieding voor weer gave in de Marketplace. Maximale lengte van 100 tekens. |
| **Beschrijving\***  | Beschrijvende tekst voor een gedetailleerde beschrijving van de aanbieding.  <!-- TD: max len/guidance? 3k characters -->  |
| **Deze SKU\* verbergen** | Hiermee wordt aangegeven of de SKU in de Marketplace op klanten moet worden weer gegeven.  Mogelijk wilt u de SKU verbergen als u alleen wilt dat deze alleen beschikbaar is via oplossings sjablonen en niet voor afzonderlijke aankopen.  Het kan ook handig zijn voor initiële tests of voor tijdelijke of seizoen aanbiedingen. |
| **Beschik baarheid van Cloud\*** | Bepaalt op welke Clouds de SKU beschikbaar moet zijn.  De standaard waarde is de open bare versie van Azure.  Microsoft Azure Government is een Cloud-community met gecontroleerde toegang voor de Amerikaanse federale, staats-, lokale of tribalee overheden en hun gecertificeerde partners.  Zie voor meer informatie over Government Cloud [Welkom bij Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome). |
| **Is dit een privé-SKU?\*** | Hiermee wordt aangegeven of de SKU privé of openbaar is. De standaard waarde is **Nee** (openbaar).  Zie [open bare en private sku's](../../cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md)voor meer informatie. |
| **Beschik baarheid van land/regio\*** | Bepaalt welke landen of regio's uw SKU beschikbaar is voor aankoop. Selecteer ten minste één regio/land. <!-- TD: Is this parameter an AMP visibility control or a contractual one, or both? --> |  
|  *Prijzen*   |  |
| **Licentie model\***| Gestandaardiseerd facturerings model dat moet worden gebruikt.  Als u **op gebruik gebaseerde maandelijkse gefactureerde SKU**selecteert, wordt er een sectie accordeon geopend waarmee u de details van per kern prijzen kunt opgeven en kunt u aangeven of u een gratis proef periode wilt aanbieden.  In deze sectie kunt u ook dit prijs schema exporteren en importeren in Excel. Zie [facturerings opties in azure Marketplace](../../billing-options-azure-marketplace.md)voor meer informatie. | 
|  *VM-installatie kopieën*   |  |
| **\* van familie van besturings systeem** | Hiermee wordt aangegeven of de oplossings-VM op Windows of Linux is gebaseerd. |
| **Type besturings systeem selecteren** | Specifieke leverancier of release van het opgegeven besturings systeem. |
| **Beschrijvende naam van het besturings systeem\*** | Naam van het besturings systeem die aan klanten moet worden weer gegeven.  |
| **Aanbevolen VM-grootten\*** | Hiermee kan Maxi maal zes aanbevolen VM-grootten worden geselecteerd uit een gestandaardiseerde lijst.  Deze lijst wordt door gegeven aan de Azure Portal en micro soft-markt plaatsen.  De eerste VM-grootte in deze lijst die geldig is (voor het abonnement van de klant, de regio, de zone enzovoort) is ingesteld als de standaard waarde voor de potentiële klant.  De gebruiker kan deze grootte wijzigen in elk compatibel met de oplossings installatie kopie. | 
| **Poorten openen**| Poorten voor open en protocol ter ondersteuning van de SKU.  Deze configuraties moeten overeenkomen met het virtuele netwerk dat u hebt geconfigureerd voor het netwerk van de oplossings-VM. Deze instellingen worden van kracht tijdens de implementatie van de virtuele machine. Poort instellingen kunnen echter worden gewijzigd nadat u een SKU hebt gepubliceerd. Zie [poorten openen voor een virtuele machine met de Azure Portal](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal)voor meer informatie. <br/>De volgende standaard netwerk toewijzingen worden toegevoegd aan alle Vm's. &emsp; Windows: 3389-> 3389 TCP, 5986-> 5986 TCP; &emsp; Linux: 22-> 22, TCP (SSH). |
| **Schijf versie**  | Gekoppelde oplossings-VM, opgegeven door het schijf versie nummer en de schijf-URL. De versie van de schijf moet een [semantische versie](https://semver.org/) -indeling hebben: `<major>.<minor>.<patch>`.  De URL is de URI van de gedeelde toegangs handtekening die is gemaakt voor de virtuele harde schijf van het besturings systeem.  Hoewel u per SKU Maxi maal acht schijf versies kunt toevoegen, wordt alleen het hoogste schijf nummer voor een SKU weer gegeven in azure Marketplace. De andere versies worden alleen weer gegeven via Api's.  <!--TD: Add more specific link to API --> <br/> In de sectie **nieuwe accordeon gegevens schijf** kunt u Maxi maal 15 gegevens schijven aan uw virtuele machine koppelen.  Zodra u een SKU met een bepaalde VM-versie en gekoppelde gegevens schijven publiceert, kan deze configuratie niet worden gewijzigd.  Als extra VM-versies worden toegevoegd aan de SKU, moeten ze ook hetzelfde aantal gegevens schijven ondersteunen. <br/> Als u de VM-installatie kopieën van Azure niet hebt gemaakt, kunt u dit veld later toevoegen.  Voor informatie over het maken van de gekoppelde VM-resource, zie de sectie [virtuele machines voor de VM maken](./cpp-create-technical-assets.md).  
|  |  |

<!-- TD: The CPP UX warning msg indicates that underscores are also supported in these SKU IDs. I suspect this might be true for other identifiers. --> 

<br/> Klik op **Opslaan** om uw voortgang op te slaan. Op het volgende tabblad kunt u opgeven of uw aanbod een [test station](./cpp-test-drive-tab.md)ondersteunt.


## <a name="additional-pricing-considerations"></a>Aanvullende prijs overwegingen

Het prijs model dat hierboven is beschreven, is een basis beschrijving.  De wijzigingen worden doorgevoerd en kunnen worden beïnvloed door lokaal of regionaal belasting Reglement en micro soft-prijs beleid. 

### <a name="new-core-sizes-added-on-722019"></a>Nieuwe kern grootten toegevoegd op 7/2/2019

VM-uitgevers zijn op 2 juli 2019 op de hoogte gebracht van de toevoeging van nieuwe prijzen voor nieuwe Azure virtual machine-grootten (op basis van het aantal kern geheugens).  De nieuwe prijzen gelden voor de belangrijkste grootten 10, 44, 48, 60, 120, 208 en 416.  Voor bestaande virtuele machines zijn de nieuwe prijzen voor deze kern geheugens automatisch berekend op basis van de huidige prijzen.  Uitgevers moeten tot 1 augustus 2019 de extra prijzen bekijken en de gewenste wijzigingen aanbrengen.  Na deze datum worden de automatisch berekende prijzen voor deze nieuwe kern groottes van kracht als u de uitgever nog niet opnieuw hebt gepubliceerd.


### <a name="simplified-currency-pricing"></a>Prijzen voor vereenvoudigd valuta

Vanaf september 1 2018 wordt een nieuwe sectie met de naam **vereenvoudigde valuta prijs** toegevoegd aan de portal. Micro soft stroomt de Azure Marketplace-onderneming door meer voorspel bare prijzen en verzamelingen van uw klanten in de hele wereld in te scha kelen. Deze stroom lijn omvat het verminderen van het aantal valuta's waarin we uw klanten factureren.  Zie [een bestaande VM-aanbieding bijwerken op Azure Marketplace](./cpp-update-existing-offer.md)voor meer informatie.


### <a name="additional-information-on-taxes-and-prices"></a>Aanvullende informatie over belastingen en prijzen

* Micro soft classificeert sommige landen/regio's als in het geding zijnde *landen*.  In dergelijke landen/regio's verzamelt micro soft belasting heffingen van klanten en betaalt vervolgens belastingen voor de overheid.  In andere landen/regio's zijn partners doorgaans verantwoordelijk voor het innen van de belasting van hun klanten en het betalen van belastingen aan de overheid. Als u ervoor kiest om te verkopen in de laatste landen/regio's, moet u de mogelijkheid hebben om lokale belastingen te berekenen en te betalen.  <!-- TD: Find a good reference on taxing policies. The best I found was in the UWP section: https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps -->
* Prijzen kunnen niet worden gewijzigd wanneer een aanbieding live gaat. U kunt echter nog steeds ondersteunde regio's toevoegen of verwijderen. 
* Micro soft brengt kosten in rekening voor het gebruik van Azure-VM'S voor de klant naast uw geplande SKU-kosten.
* Prijzen worden voor alle regio's in de lokale valuta ingesteld op de beschik bare valuta tarieven op het moment van de instelling van de prijzen.  <!-- TD: Meaning? - Offer created, published, other? -->
* Als u de prijs van elke regio afzonderlijk wilt instellen, exporteert u de prijs spread sheet, past u aangepaste prijzen toe en vervolgens importeren. 


## <a name="next-steps"></a>Volgende stappen

Optioneel kunt u de gegevens van het [test station](./cpp-test-drive-tab.md) opgeven als u deze functie ondersteunt. Als dat niet het geval is, kunt u [Marketplace](./cpp-marketplace-tab.md) -gegevens voor uw aanbieding opgeven.
