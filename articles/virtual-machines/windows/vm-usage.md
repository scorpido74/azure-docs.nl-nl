---
title: Inzicht in het gebruik van virtuele azure-machines
description: Informatie over het gebruik van virtuele machines
services: virtual-machines
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: ''
tags: azure-virtual-machine
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 12/04/2017
ms.author: memccror
ms.openlocfilehash: 2aa175d97787d82aae062a95ed519f35ff65816b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982265"
---
# <a name="understanding-azure-virtual-machine-usage"></a>Inzicht in het gebruik van virtuele azure-machines
Door uw Azure-gebruiksgegevens te analyseren, kunnen krachtige verbruiksinzichten worden verkregen : inzichten die een beter kostenbeheer en -toewijzing in uw hele organisatie mogelijk kunnen maken. Dit document biedt een diepe duik in uw Azure Compute-verbruiksgegevens. Ga voor meer informatie over algemeen Azure-gebruik naar [Inzicht in uw factuur.](../../cost-management-billing/understand/review-individual-bill.md)

## <a name="download-your-usage-details"></a>Uw gebruiksgegevens downloaden
Download om te beginnen [uw gebruiksgegevens.](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md) De onderstaande tabel bevat de definitie- en voorbeeldwaarden van het gebruik voor virtuele machines die zijn geïmplementeerd via Azure Resource Manager. Dit document bevat geen gedetailleerde informatie voor VM's die via ons klassieke model worden geïmplementeerd.


| Velden             | Betekenis                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Voorbeeldwaarden                                                                                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Gebruiksdatum         | De datum waarop de resource is gebruikt.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |  "11/23/2017"                                                                                                                                                                                                                                                                                                                                                     |
| Id van de meter           | Hiermee wordt de service op het hoogste niveau aangegeven waartoe dit gebruik behoort.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | "Virtuele machines"                                                                                                                                                                                                                                                                                                                                               |
| De subcategorie van de meter | De id van de gefactureerde meter. <ul><li>Voor gebruik van rekenuren is er een meter voor elke VM-grootte + OS (Windows, niet-Windows) + regio.</li><li>Voor Premium-softwaregebruik is er een meter voor elk softwaretype. De meeste premium software afbeeldingen hebben verschillende meters voor elke kerngrootte. Ga voor meer informatie naar de [pagina Compute Pricing.](https://azure.microsoft.com/pricing/details/virtual-machines/)</li></ul>                                                                                                                                                                                                                                                                                                                                         | "2005544f-659d-49c9-9094-8e0aea1be3a5"                                                                                                                                                                                                                                                                                                                           |
| De naam van de meter         | Dit is specifiek voor elke service in Azure. Voor compute is het altijd "Compute Hours".                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | "Rekenuren"                                                                                                                                                                                                                                                                                                                                                  |
| De regio van de meter       | De datacenterlocatie voor bepaalde services waarbij de prijs is gebaseerd op de datacenterlocatie.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |  "JA Oost"                                                                                                                                                                                                                                                                                                                                                       |
| Eenheid               | Hiermee wordt de eenheid geïdentificeerd waarin de service wordt gefactureerd. Rekenresources worden per uur gefactureerd.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | "Uren"                                                                                                                                                                                                                                                                                                                                                          |
| Verbruikt           | De hoeveelheid resource die voor die dag is verbruikt. Voor Compute factureren we voor elke minuut die de VM gedurende een bepaald uur heeft uitgevoerd (tot 6 decimalen nauwkeurigheid).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |    "1", "0.5"                                                                                                                                                                                                                                                                                                                                                    |
| Resourcelocatie  | Hiermee wordt het datacenter geïdentificeerd waarop de resource wordt uitgevoerd.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | "JA Oost"                                                                                                                                                                                                                                                                                                                                                        |
| Verbruikte service   | De Azure-platformservice die u hebt gebruikt.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | "Microsoft.Compute"                                                                                                                                                                                                                                                                                                                                              |
| Resourcegroep     | De resourcegroep waarin de geïmplementeerde resource wordt uitgevoerd. Zie overzicht van Azure Resource Manager voor meer [informatie.](../../azure-resource-manager/management/overview.md)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |    "MyRG"                                                                                                                                                                                                                                                                                                                                                        |
| Exemplaar-id        | De id voor de resource. De id bevat de naam die u voor de resource opgeeft wanneer deze wordt gemaakt. Voor VM's bevat de instantie-id de SubscriptionId, ResourceGroupName en VMName (of schaalsetnaam voor schaalsetgebruik).                                                                                                                                                                                                                                                                                                                                                                                                                    | "/abonnementen/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/ resourceGroepen/MyRG/providers/Microsoft.Compute/virtualMachines/MyVM1"<br><br>of<br><br>"/abonnementen/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/ resourceGroepen/MyRG/providers/Microsoft.Compute/virtualMachineScaleSets/MyVMSS1"                                                                                           |
| Tags               | Tag die u aan de resource toewijst. Tags gebruiken om factureringsrecords te groeperen. Meer informatie over het [taggen van uw virtuele machines.](tag.md) Dit is alleen beschikbaar voor Vm's voor Resource Manager.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | "{"myDepartment":"RD","myUser":"myName"}"                                                                                                                                                                                                                                                                                                                        |
| Aanvullende informatie    | Servicespecifieke metagegevens. Voor VM's vullen we het volgende in het aanvullende infoveld: <ul><li>Image Type- specifieke afbeelding die u hebt uitgevoerd. Hieronder vindt u de volledige lijst met ondersteunde tekenreeksen onder Afbeeldingstypen.</li><li>Servicetype: de grootte die u hebt geïmplementeerd.</li><li>VMName: naam van uw VM. Dit wordt alleen ingevuld voor vm's met een schaalset. Als u de VM-naam nodig hebt voor vm's met een schaalset, u dat vinden in de bovenstaande tekenreeks Instantie-id.</li><li>UsageType: hiermee wordt het type gebruik opgegeven dat wordt aangegeven.<ul><li>ComputeHR is het Compute Hour-gebruik voor de onderliggende VM, zoals Standard_D1_v2.</li><li>ComputeHR_SW is de premium software kosten als de VM is met behulp van premium software, zoals Microsoft R Server.</li></ul></li></ul>    | Virtuele machines {"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR"}<br><br>Virtuele machineschaal sets {"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"myVM1", "UsageType":"ComputeHR"}<br><br>Premium Software {"ImageType":"","""ServiceType":"Standard_DS1_v2","VMName":"",", "UsageType":"ComputeHR_SW"} |

## <a name="image-type"></a>Type installatiekopie
Voor sommige afbeeldingen in de Azure-galerie wordt het afbeeldingstype ingevuld in het veld Aanvullende info. Dit stelt gebruikers in staat om te begrijpen en bijhouden wat ze hebben ingezet op hun Virtual Machine. De waarden die in dit veld worden ingevuld op basis van de afbeelding die u hebt geïmplementeerd, zijn de volgende:
  - BitRock BitRock 
  - Canonical 
  - FreeBSD 
  - Logica openen 
  - Oracle 
  - SLES voor SAP 
  - SQL Server 14 Preview op Windows Server 2012 R2 Preview 
  - SUSE
  - SUSE Premium
  - StorSimple Cloud Appliance 
  - Red Hat
  - Red Hat voor SAP Business Applications     
  - Red Hat voor SAP HANA 
  - ByOL van Windows-client 
  - Windows Server BYOL 
  - Voorbeeld van Windows Server 

## <a name="service-type"></a>Servicetype
Het veld servicetype in het veld Aanvullende info komt overeen met de exacte VM-grootte die u hebt geïmplementeerd. Premium opslag VM's (SSD-gebaseerde) en niet-premium opslag VM's (HDD-gebaseerde) zijn geprijsd hetzelfde. Als u een SSD-grootte implementeert, zoals Standard\_DS2\_v2, ziet\_u\_de niet-SSD-grootte ('Standard D2 v2 VM') in de kolom Metersubcategorie en de SSD-grootte ('Standaard\_DS2\_v2') in het veld Aanvullende info.

## <a name="region-names"></a>Regionamen
De regionaam die in het veld Resourcelocatie is ingevuld in de gebruiksgegevens, is afhankelijk van de regionaam die wordt gebruikt in Azure Resource Manager. Hier is een toewijzing tussen de regiowaarden:

|    **Naam resourcebeheerregio**       |    **Resourcelocatie in gebruiksgegevens**    |
|--------------------------|------------------------------------------|
|    Australië-oost         |    AU - oost                               |
|    australiasoutheast    |    Australië - zuidoost                          |
|    brazilsouth           |    BR - zuid                              |
|    CanadaCentraal         |    CA - centraal                            |
|    CanadaOost            |    CA - oost                               |
|    Centraal-India          |    IN - centraal                            |
|    centralus             |    VS - centraal                            |
|    Chinaeast Chinaeast             |    China East                            |
|    chinanoord            |    China - noord                           |
|    eastasia              |    Azië - oost                             |
|    eastus                |    VS - oost                               |
|    eastus2               |    VS - oost 2                             |
|    DuitslandCentraal        |    DE Centraal                            |
|    DuitslandNoordoost      |    DE Noordoost                          |
|    japaneast             |    JA - oost                               |
|    japanwest             |    JA - west                               |
|    KoreaCentraal          |    KR - centraal                            |
|    KoreaZuid            |    KR - zuid                              |
|    northcentralus northcentralus        |    VS - noord-centraal                      |
|    northeurope           |    Europa - noord                          |
|    southcentralus        |    VS - zuid-centraal                      |
|    southeastasia         |    Azië - zuidoost                        |
|    Zuid-India            |    IN - zuid                              |
|    UKNorth               |    Het Noorden van de V.S.                              |
|    uksouth               |    Verenigd Koninkrijk Zuid                              |
|    UKZuid2              |    VK - zuid 2                            |
|    ukwest                |    Verenigd Koninkrijk West                               |
|    USDodCentraal          |    US DoD Central                        |
|    USDodEast             |    US DoD East                           |
|    USGovArizona USGovArizona          |    USGov Arizona                         |
|    usgoviowa             |    USGov Iowa                            |
|    USGovTexas USGovTexas            |    USGov - Texas                           |
|    usgovvirginia         |    USGov - Virginia                        |
|    US - west-centraal         |    US - west-centraal                       |
|    westeurope            |    Europa -west                           |
|    WestIndia WestIndia             |    IN - west                               |
|    westus                |    VS - west                               |
|    westus2               |    US - west 2                             |


## <a name="virtual-machine-usage-faq"></a>Veelgestelde vragen over virtueel machinegebruik
### <a name="what-resources-are-charged-when-deploying-a-vm"></a>Welke resources worden in rekening gebracht bij het implementeren van een VM?    
VM's krijgen kosten voor de VM zelf, alle premium software die op de VM wordt uitgevoerd, de opslagaccount\beheerde schijf die is gekoppeld aan de VM en de netwerkbandbreedteoverdrachten van de VM.
### <a name="how-can-i-tell-if-a-vm-is-using-azure-hybrid-benefit-in-the-usage-csv"></a>Hoe weet ik of een VM Azure Hybrid Benefit gebruikt in het gebruiks-CSV?
Als u implementeert met het [Azure Hybrid Benefit,](https://azure.microsoft.com/pricing/hybrid-benefit/)worden het VM-tarief voor niet-Windows in rekening gebracht omdat u uw eigen licentie naar de cloud brengt. In uw factuur u onderscheid maken welke Resource Manager VM's\_Azure Hybrid Benefit\_uitvoeren omdat ze 'Windows Server BYOL' of 'Windows Client BYOL' in de kolom ImageType hebben.
### <a name="how-are-basic-vs-standard-vm-types-differentiated-in-the-usage-csv"></a>Hoe worden Basis versus standaard VM-typen gedifferentieerd in het gebruik csv?
Zowel Basic als Standard A-serie VM's worden aangeboden. Als u een basisvm implementeert in de subcategorie Meter, heeft deze de tekenreeks 'Basic'. Als u een VM met standaarda-reeksen implementeert, wordt de VM-grootte weergegeven als 'A1 VM', omdat Standard de standaardis. Zie de [prijspagina](https://azure.microsoft.com/pricing/details/virtual-machines/)voor meer informatie over de verschillen tussen Basic en Standard.
### <a name="what-are-extrasmall-small-medium-large-and-extralarge-sizes"></a>Wat zijn extrakleine, kleine, middelgrote, grote en extragrote maten?
ExtraSmall - ExtraLarge zijn de\_legacy namen\_voor Standard A0 – Standard A4. In klassieke VM-gebruiksrecords wordt deze conventie mogelijk gebruikt als u deze formaten hebt geïmplementeerd.
### <a name="what-is-the-difference-between-meter-region-and-resource-location"></a>Wat is het verschil tussen metergebied en resourcelocatie?
Het metergebied is gekoppeld aan de meter. Voor sommige Azure-services die één prijs voor alle regio's gebruiken, kan het veld Metergebied leeg zijn. Aangezien VM's echter speciale prijzen per regio voor virtuele machines hebben, wordt dit veld bevolkt. Op dezelfde manier is de resourcelocatie voor virtuele machines de locatie waar de VM wordt geïmplementeerd. Het Azure-gebied in beide velden is hetzelfde, hoewel ze mogelijk een andere tekenreeksconventie voor de regionaam hebben.
### <a name="why-is-the-imagetype-value-blank-in-the-additional-info-field"></a>Waarom is de waarde ImageType leeg in het veld Aanvullende info?
Het veld ImageType wordt alleen ingevuld voor een subset van afbeeldingen. Als u een van de bovenstaande afbeeldingen niet hebt geïmplementeerd, is het ImageType leeg.
### <a name="why-is-the-vmname-blank-in-the-additional-info"></a>Waarom is de VMName leeg in de aanvullende informatie?
De VMName wordt alleen ingevuld in het veld Aanvullende info voor VM's in een schaalset. Het veld InstanceID bevat de VM-naam voor vm's voor niet-schaalset-VM's.
### <a name="what-does-computehr-mean-in-the-usagetype-field-in-the-additional-info"></a>Wat betekent ComputeHR in het veld UsageType in de aanvullende informatie?
ComputeHR staat voor Compute Hour, dat de gebruiksgebeurtenis voor de onderliggende infrastructuurkosten vertegenwoordigt. Als het UsageType\_ComputeHR SW is, vertegenwoordigt de gebruiksgebeurtenis de premium-softwarekosten voor de VM.
### <a name="how-do-i-know-if-i-am-charged-for-premium-software"></a>Hoe weet ik of er kosten in rekening worden gebracht voor premium software?
Wanneer u onderzoekt welke VM Image het beste bij uw behoeften past, moet u de [Azure Marketplace bekijken.](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute) Het beeld heeft de snelheid van het softwareplan. Als u "Gratis" voor het tarief ziet, zijn er geen extra kosten voor de software. 
### <a name="what-is-the-difference-between-microsoftclassiccompute-and-microsoftcompute-in-the-consumed-service"></a>Wat is het verschil tussen Microsoft.ClassicCompute en Microsoft.Compute in de verbruikte service?
Microsoft.ClassicCompute vertegenwoordigt klassieke resources die zijn geïmplementeerd via Azure Service Manager. Als u implementeert via Resource Manager, wordt Microsoft.Compute ingevuld in de verbruikte service. Meer informatie over de [Azure Deployment-modellen](../../azure-resource-manager/management/deployment-models.md).
### <a name="why-is-the-instanceid-field-blank-for-my-virtual-machine-usage"></a>Waarom is het veld InstanceID leeg voor het gebruik van mijn virtuele machine?
Als u via het klassieke implementatiemodel implementeert, is de InstanceID-tekenreeks niet beschikbaar.
### <a name="why-are-the-tags-for-my-vms-not-flowing-to-the-usage-details"></a>Waarom stromen de tags voor mijn VM's niet naar de gebruiksgegevens?
Tags stromen alleen naar u de gebruiksCSV voor Resource Manager VM's alleen. Klassieke resourcetags zijn niet beschikbaar in de gebruiksgegevens.
### <a name="how-can-the-consumed-quantity-be-more-than-24-hours-one-day"></a>Hoe kan de verbruikte hoeveelheid meer dan 24 uur per dag zijn?
In het Klassieke model wordt facturering voor resources samengevoegd op cloudserviceniveau. Als u meer dan één vm in een cloudservice hebt die dezelfde factureringsmeter gebruikt, wordt uw gebruik samengevoegd. VM's die via Resource Manager worden geïmplementeerd, worden gefactureerd op VM-niveau, dus deze aggregatie is niet van toepassing.
### <a name="why-is-pricing-not-available-for-dsfsgsls-sizes-on-the-pricing-page"></a>Waarom zijn er geen prijzen beschikbaar voor DS/FS/GS/LS-formaten op de prijspagina?
Vm's met een premium opslaggeschikt worden gefactureerd tegen hetzelfde tarief als vm's die geschikt zijn voor niet-premium opslag. Alleen uw opslagkosten verschillen. Ga naar de [pagina opslagprijzen](https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Zie [Uw factuur voor Microsoft Azure begrijpen voor](../../cost-management-billing/understand/review-individual-bill.md) meer informatie over uw gebruiksgegevens.

