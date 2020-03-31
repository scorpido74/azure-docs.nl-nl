---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02-18-2020
ms.openlocfilehash: aebe8f05bd1a3607da6d1153c87490891fc6b299
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471449"
---
Wanneer u een gereserveerdvm-exemplaar koopt, u ervoor kiezen om bijvoorbeeld de flexibiliteit van de grootte of de capaciteitsprioriteit te optimaliseren. Zie [De optimalisatieinstelling voor gereserveerde VM-exemplaren wijzigen voor](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances)meer informatie over het instellen of wijzigen van de optimalisatie-instelling voor gereserveerde VM-exemplaren.

Met een gereserveerde virtuele machine-instantie die is geoptimaliseerd voor bijvoorbeeld grootteflexibiliteit, kan de reservering die u koopt van toepassing zijn op de virtuele machines (VM's) in dezelfde flexibiliteitsgroep voor instantiegrootte. Als u bijvoorbeeld een reservering koopt voor een VM-grootte die wordt vermeld in de DSv2-serie, zoals Standard_DS5_v2, kan de reserveringskorting van toepassing zijn op de andere vier maten die in dezelfde flexibiliteitsgroep voor instantiegrootte worden vermeld:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

Maar die reserveringskorting is niet van toepassing op VM's-formaten die worden vermeld in verschillende flexibiliteitsgroepen voor instantiegrootte, zoals SKU's in DSv2-serie hoog geheugen: Standard_DS11_v2, Standard_DS12_v2, enzovoort.

Binnen de flexibiliteitsgroep voor instantiegrootte is het aantal VM's waarop de reserveringskorting van toepassing is, afhankelijk van de VM-grootte die u kiest wanneer u een reservering koopt. Het hangt ook af van de grootte van de VM's die u hebt uitgevoerd. De kolom ratio vergelijkt de relatieve voetafdruk voor elke VM-grootte in die flexibiliteitsgroep voor instantiegrootte. Gebruik de verhoudingswaarde om te berekenen hoe de reserveringskorting van toepassing is op de VM's die u hebt uitgevoerd.

## <a name="examples"></a>Voorbeelden

In de volgende voorbeelden worden de groottes en verhoudingen in de tabel met de DSv2-reeksen gebruikt.

U koopt een gereserveerde VM-instantie met de grootte Standard_DS4_v2 waarbij de verhouding of relatieve voetafdruk ten opzichte van de andere groottes in die reeks 8 is.

- Scenario 1: Voer acht VM's van Standard_DS1_v2 formaat uit met een verhouding van 1. Uw reserveringskorting is van toepassing op alle acht vm's.
- Scenario 2: Voer twee VM's van Standard_DS2_v2 formaat uit met een verhouding van 2 per stuk. Voer ook een vm van Standard_DS3_v2 formaat uit met een verhouding van 4. De totale voetafdruk is 2+2+4=8. Dus uw reserveringskorting geldt voor alle drie deze VM's.
- Scenario 3: Voer een Standard_DS5_v2 uit met een verhouding van 16. Uw reserveringskorting is van toepassing op de helft van de rekenkosten van die VM.

In de volgende secties wordt weergegeven welke groottes zich in de reeksgroep van dezelfde grootte bevinden wanneer u een gereserveerdvm-exemplaar koopt dat is geoptimaliseerd voor bijvoorbeeld grootteflexibiliteit.

## <a name="instance-size-flexibility-ratio-for-vms"></a>Flexibiliteitsverhouding voor vm's 

CSV hieronder heeft de instantiegrootteflexibiliteitsgroepen, ArmSkuName en de verhoudingen.  

[Flexibiliteitsratio's voor instantiegrootte](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

We houden de URL van het bestand en het schema vast, zodat u dit bestand programmatisch gebruiken. De gegevens zullen binnenkort ook beschikbaar zijn via API.
