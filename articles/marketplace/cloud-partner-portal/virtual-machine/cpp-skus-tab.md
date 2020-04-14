---
title: Tabblad Virtuele machine-SKU's in de Cloud Partner Portal voor Azure Marketplace
description: Beschrijft het tabblad SKU's dat wordt gebruikt bij het maken van een virtuele machineaanbieding in de Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: a8dbadf959107e733a94216c8a01495e6ebf5039
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273117"
---
# <a name="virtual-machine-skus-tab"></a>Tabblad Virtuele machine SKU's

> [!IMPORTANT]
> Vanaf 13 april 2020 beginnen we met het verplaatsen van het beheer van uw Azure Virtual Machine-aanbiedingen naar partnercentrum. Na de migratie maak en beheer je je aanbiedingen in partnercentrum. Volg de instructies in [Een Azure Virtual Machine-aanbieding maken](https://aka.ms/CreateAzureVMoffer) om uw gemigreerde aanbiedingen te beheren.

Op het tabblad **SKU's** van de pagina **Nieuwe aanbieding** u een of meer SKU's maken en deze koppelen aan uw nieuwe aanbieding.  Verschillende SKU's kunnen een oplossing onderscheiden op functiesets, VM-afbeeldingstypen, doorvoer of schaalbaarheid, factureringsmodellen of een ander kenmerk.


## <a name="create-a-sku"></a>Een SKU maken

In eerste instantie heeft een nieuwe aanbieding geen bijbehorende SKU's, dus u maakt er een door op **Nieuwe SKU**te klikken.

![Nieuwe SKU-knop op het tabblad Nieuwe aanbieding voor virtuele machines](./media/publishvm_005.png)

<br/>

Het dialoogvenster **Nieuwe SKU** wordt weergegeven.  Voer de id voor de nieuwe SKU in en klik op **OK**. (Zie hieronder voor id-naamgevingsconventies.)  Op het tabblad **SKU's** worden nu de velden weergegeven die beschikbaar zijn voor bewerking.    Een toegevoegd sterretje (*) op de veldnaam geeft aan dat dit vereist is.

<!-- TD: This tab has been updated, now has "Old Pricing" and "Simplified Currency Pricing" sections"! -->

![Tabblad SKU op het formulier Nieuwe aanbieding voor virtuele machines](./media/publishvm_006.png)

In de volgende tabel worden het doel, de inhoud en de opmaak van deze velden beschreven.  Vereiste velden worden aangeklaagd door een sterretje (*).

<!-- TD: I took a new screenshot, and the fields differ somewhat from description in the VM Pub Guide.  Needs review. -->

|  **Veld**       |     **Beschrijving**                                                          |
|  ---------       |     ---------------                                                          |
|  *SKU-instellingen*   |    |
| **SKU-id\***       | Id voor deze SKU.  Deze naam heeft een maximum van 50 tekens, bestaande uit alfanumerieke letters of streepjes (-), maar kan niet eindigen met een streepje.  Het kan niet worden gewijzigd nadat de aanbieding is gepubliceerd.  |
|  *SKU-gegevens*   |  |
| **Titel\***        | Vriendelijke naam voor het aanbod voor weergave in de markt. Maximale lengte van 50 tekens. |
| **Samenvatting\***      | Beknopte beschrijving van het aanbod voor weergave in de markt. Maximale lengte van 100 tekens. |
| **Beschrijving\***  | Beschrijvingstekst die een meer gedetailleerde uitleg van het aanbod geeft.  <!-- TD: max len/guidance? 3k characters -->  |
| **Verberg deze SKU\*** | Geeft aan of de SKU zichtbaar moet zijn in de markt voor klanten.  U de SKU verbergen als u deze alleen beschikbaar wilt hebben via oplossingssjablonen en niet voor afzonderlijke aankopen.  Het kan ook nuttig zijn voor de eerste tests of voor tijdelijke of seizoensgebonden aanbiedingen. |
| **Beschikbaarheid in de cloud\*** | Hiermee bepaalt u op welke wolken de SKU beschikbaar moet zijn.  De standaardversie is de openbare versie van Azure.  Microsoft Azure Government is een cloud die door de overheid wordt beheerd met gecontroleerde toegang voor de Amerikaanse federale, staats-, lokale of tribale overheden en hun gecertificeerde partners.  Zie Welkom bij Azure [Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)voor meer informatie over de overheidscloud. |
| **Is dit een private SKU?\*** | Geeft aan of de SKU privé of openbaar is. De standaardinstelling is **Nee** (openbaar).  Zie [openbare en particuliere SKU's voor](../../cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md)meer informatie. |
| **Beschikbaarheid van land/regio\*** | Hiermee bepaalt u welke landen of wereldregio's uw SKU kan worden gekocht. Selecteer ten minste één regio/land. <!-- TD: Is this parameter an AMP visibility control or a contractual one, or both? --> |  
|  *Prijzen*   |  |
| **Licentiemodel\***| Gestandaardiseerd factureringsmodel om te gebruiken.  Als u **op gebruik gebaseerde maandelijks gefactureerde SKU**selecteert, wordt er een accordeonsectie geopend waarmee u details van de prijzen per kern opgeven en of u een gratis proefperiode wilt aanbieden.  Met deze sectie u dit prijsschema ook exporteren en importeren naar Excel. Zie [Factureringsopties in de Azure Marketplace](../../billing-options-azure-marketplace.md)voor meer informatie. | 
|  *VM-installatiekopieën*   |  |
| **Besturingssysteemfamilie\*** | Geeft aan of de oplossing VM windows- of Linux-gebaseerd is. |
| **Type besturingssysteem selecteren** | Specifieke leverancier of release van het opgegeven besturingssysteem. |
| **Os vriendelijke naam\*** | Naam van het besturingssysteem die aan klanten moet worden weergegeven.  |
| **Aanbevolen VM-formaten\*** | Hiermee u maximaal zes aanbevolen VM-formaten selecteren uit een gestandaardiseerde lijst.  Deze lijst wordt doorgegeven aan de Azure-portal- en Microsoft-marktplaatsen.  De eerste VM-grootte in deze lijst die geldig is (voor dat klantabonnement, regio, zone, enz.) is ingesteld als de standaardwaarde voor die potentiële klant.  De gebruiker kan deze grootte wijzigen in elke compatibele oplossingsafbeelding. | 
| **Open poorten**| Poorten te openen en protocol ter ondersteuning van de SKU.  Deze configuraties moeten overeenkomen met het virtuele netwerk dat u hebt geconfigureerd voor het netwerk van de oplossingvm. Deze instellingen worden van kracht tijdens vm-implementatie. Poortinstellingen kunnen echter worden gewijzigd nadat u een SKU hebt gepubliceerd. Zie voor meer informatie [Het openen van poorten op een virtuele machine met Azure Portal](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal). <br/>De volgende standaardnetwerktoewijzingen worden toegevoegd aan alle VM's. &emsp;Windows: 3389 -> 3389 TCP, 5986 -> 5986 TCP; &emsp; Linux: 22 -> 22, TCP (SSH). |
| **Schijfversie**  | Gekoppelde oplossing VM, opgegeven op schijfversienummer en schijf-URL. De schijfversie moet in [semantische versieindeling](https://semver.org/) zijn: `<major>.<minor>.<patch>`.  De URL is de gedeelde toegangshandtekening URI die is gemaakt voor het besturingssysteem VHD.  Hoewel u maximaal acht schijfversies per SKU toevoegen, wordt alleen het hoogste schijfversienummer voor een SKU weergegeven in Azure Marketplace. De andere versies zijn alleen zichtbaar via API's.  <!--TD: Add more specific link to API --> <br/> Met de sectie **Nieuwe gegevensschijfaccordeon** u maximaal 15 gegevensschijven aan uw vm koppelen.  Zodra u een SKU met een bepaalde VM-versie en bijbehorende gegevensschijven publiceert, kan deze configuratie niet meer worden gewijzigd.  Als er extra VM-versies aan de SKU worden toegevoegd, moeten ze ook hetzelfde aantal gegevensschijven ondersteunen. <br/> Als u uw VM-afbeelding(en) in Azure niet hebt gemaakt, u dit veld later bijwerken toevoegen.  Zie de sectie [VM-technische elementen maken](./cpp-create-technical-assets.md)voor informatie over het maken van de gekoppelde VM-bron.  
|  |  |

<!-- TD: The CPP UX warning msg indicates that underscores are also supported in these SKU IDs. I suspect this might be true for other identifiers. --> 

<br/> Klik **op Opslaan** om uw voortgang op te slaan. In het volgende tabblad geeft u aan of uw aanbieding [teststation](./cpp-test-drive-tab.md)ondersteunt.


## <a name="additional-pricing-considerations"></a>Aanvullende prijsoverwegingen

Het hierboven beschreven prijsmodel is een basisbeschrijving.  Het ondergaat veranderingen en kan worden beïnvloed door lokale of regionale belastingvoorschriften en het prijsbeleid van Microsoft. 

### <a name="new-core-sizes-added-on-722019"></a>Nieuwe kernmaten toegevoegd op 7/2/2019

VM-uitgevers werden op 2 juli 2019 op de hoogte gebracht van de toevoeging van nieuwe prijzen voor nieuwe Azure-virtuele machineformaten (op basis van het aantal cores).  De nieuwe prijzen zijn voor de kernmaten 10, 44, 48, 60, 120, 208 en 416.  Voor bestaande VM-aanbiedingen werden nieuwe prijzen voor deze kernen-maten automatisch berekend op basis van de huidige prijzen.  Uitgevers hebben tot 1 augustus 2019 de tijd om de extra prijzen te bekijken en eventuele gewenste wijzigingen door te voeren.  Na deze datum, indien deze nog niet opnieuw is gepubliceerd door de uitgever, worden de automatisch berekende prijzen voor deze nieuwe kerngroottes van kracht.


### <a name="simplified-currency-pricing"></a>Vereenvoudigde valutaprijzen

Vanaf 1 september 2018 wordt een nieuwe sectie met de naam **Simplified Currency Pricing** toegevoegd aan het portaal. Microsoft stroomlijnt het Azure Marketplace-bedrijf door meer voorspelbare prijzen en verzamelingen van uw klanten over de hele wereld mogelijk te maken. Deze stroomlijning omvat het verminderen van het aantal valuta's waarin we uw klanten factureren.  Zie [Een bestaande VM-aanbieding bijwerken op Azure Marketplace](./cpp-update-existing-offer.md)voor meer informatie.


### <a name="additional-information-on-taxes-and-prices"></a>Aanvullende informatie over belastingen en prijzen

* Microsoft classificert sommige landen/regio's als *belastinguitgestoten landen*.  In dergelijke landen/regio's int Microsoft belastingen van klanten en betaalt vervolgens (remits) belastingen aan de overheid.  In andere landen/regio's zijn partners doorgaans verantwoordelijk voor het innen van belastingen van hun klanten en het betalen van belastingen aan de overheid. Als u ervoor kiest om te verkopen in de laatste landen / regio's, moet u de mogelijkheid hebben om te berekenen en lokale belastingen te betalen.  <!-- TD: Find a good reference on taxing policies. The best I found was in the UWP section: https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps -->
* Prijzen zijn niet veranderlijk zodra een aanbieding live gaat. U echter nog steeds ondersteunde regio's toevoegen of verwijderen. 
* Microsoft brengt de standaard Azure VM-gebruikskosten in rekening naast uw geplande SKU-kosten.
* Prijzen worden vastgesteld voor alle regio's in lokale valuta op de beschikbare wisselkoersen op het moment van het vaststellen van de prijzen.  <!-- TD: Meaning? - Offer created, published, other? -->
* Als u de prijs van elke regio afzonderlijk wilt instellen, exporteert u de prijsspreadsheet, past u aangepaste prijzen toe en importeert u. 


## <a name="next-steps"></a>Volgende stappen

Optioneel geeft u [testritgegevens](./cpp-test-drive-tab.md) op als u deze functie ondersteunt; anders verstrekt u [marktplaatsgegevens](./cpp-marketplace-tab.md) voor uw aanbieding.
