---
title: Technische elementen maken voor een virtuele machineaanbieding voor de Azure Marketplace
description: Hier wordt uitgelegd hoe u de technische elementen voor een virtuele machineaanbieding in de Azure Marketplace maakt.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: dsindona
ms.openlocfilehash: a62af1d8d751d36150c236280077cde8f6547385
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273967"
---
# <a name="create-technical-assets-for-a-virtual-machine-offer"></a>Technische assets maken voor een virtuele machineaanbieding

> [!IMPORTANT]
> Vanaf 13 april 2020 beginnen we met het verplaatsen van het beheer van uw Azure Virtual Machine-aanbiedingen naar Partner Center. Na de migratie maak en beheer je je aanbiedingen in partnercentrum. Volg de instructies in [Uw technische assets van Azure Virtual Machine maken](https://aka.ms/AzureVMTechAsset) om uw gemigreerde aanbiedingen te beheren.

In deze sectie u de technische elementen voor een virtuele machine (VM) voor de Azure Marketplace maken en configureren.  Een VM bevat twee componenten: de oplossing virtuele harde schijf (VHD) en optionele gekoppelde gegevensschijven.  

- *Virtuele harde schijven (VHD's),* die het besturingssysteem en uw oplossing bevatten, die u implementeert met uw Azure Marketplace-aanbieding. Het proces van de voorbereiding van de VHD verschilt afhankelijk van of het een Linux-gebaseerde, Windows-gebaseerde, of een op maat gebaseerde VM.
- *Gegevensschijven* vertegenwoordigen speciale, permanente opslag voor een virtuele machine. Gebruik *not* de oplossing VHD (bijvoorbeeld `C:` het station) niet om permanente informatie op te slaan.

Een VM-afbeelding bevat één besturingssysteemschijf en nul of meer gegevensschijven. Per schijf is één VHD nodig. Zelfs lege gegevensschijven vereisen een VHD worden gemaakt.
U moet het VM-besturingssysteem, de VM-grootte, poorten die u wilt openen en maximaal 15 gekoppelde gegevensschijven configureren.

> [!TIP] 
> Ongeacht welk besturingssysteem u gebruikt, voegt u alleen het minimum aantal gegevensschijven toe dat de SKU nodig heeft. Klanten kunnen geen schijven verwijderen die deel uitmaken van een afbeelding op het moment van implementatie, maar ze kunnen altijd schijven toevoegen tijdens of na implementatie. 

> [!IMPORTANT]
> *Wijzig het aantal schijven niet in een nieuwe afbeeldingsversie.* Als u gegevensschijven in de afbeelding opnieuw moet configureren, definieert u een nieuwe SKU. Als u een nieuwe afbeeldingsversie publiceert met verschillende schijftellingen, kan de nieuwe implementatie worden afgebroken op basis van de nieuwe afbeeldingsversie in het geval van automatisch schalen, automatische implementaties van oplossingen via Azure Resource Manager-sjablonen en andere scenario's.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="fundamental-technical-knowledge"></a>Fundamentele technische kennis

Het ontwerpen, bouwen en testen van deze assets kost tijd en vereist technische kennis van zowel het Azure-platform als de technologieën die worden gebruikt om het aanbod te bouwen. Naast uw oplossingsdomein moet uw engineeringteam kennis hebben van de volgende Microsoft-technologieën: 
-    Basiskennis van [Azure Services](https://azure.microsoft.com/services/) 
-    [Azure-toepassingen ontwerpen en ontwerpen](https://azure.microsoft.com/solutions/architecture/)
-    Werkkennis van [Azure Virtual Machines,](https://azure.microsoft.com/services/virtual-machines/) [Azure Storage](https://azure.microsoft.com/services/?filter=storage) en Azure [Networking](https://azure.microsoft.com/services/?filter=networking)
-    Werkkennis van [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-    Werkkennis van [JSON](https://www.json.org/)


## <a name="suggested-tools"></a>Voorgestelde hulpprogramma's 

Kies een of beide van de volgende scriptomgevingen om VHD's en VM's te beheren:
-    [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-    [Azure-CLI](https://docs.microsoft.com/cli/azure)

Daarnaast raden we aan om de volgende tools toe te voegen aan je ontwikkelomgeving: 

-    [Azure Opslagverkenner](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-    [Visual Studio Code](https://code.visualstudio.com/)
    *    Extensie: [Azure Resource Manager-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *    Uitbreiding: [Verfraaien](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *    Uitbreiding: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

We raden ook aan om de beschikbare hulpprogramma's op de pagina [Azure Developer Tools](https://azure.microsoft.com/tools/) te bekijken en, als u Visual Studio gebruikt, de Visual Studio [Marketplace](https://marketplace.visualstudio.com/).


## <a name="next-steps"></a>Volgende stappen

De volgende artikelen in deze sectie lopen u door de stappen van het maken en registreren van deze VM-elementen:

1. [Maak een virtuele harde schijf die compatibel](./cpp-create-vhd.md) is met Azure en geeft aan hoe u een VHD op Basis van Linux of Windows maakt die compatibel is met Azure.  Het bevat best practices, zoals het dimensioneren, patchen en voorbereiden van de VM voor het uploaden.

2. [Maak verbinding met de virtuele machine](./cpp-connect-vm.md) legt uit hoe u op afstand verbinding maken met uw nieuw gemaakte virtuele machine en zich erop aanmelden.  In dit artikel wordt ook uitgelegd hoe u de VM stoppen om te besparen op gebruikskosten.

3. [Configureer de virtuele machine](./cpp-configure-vm.md) legt uit hoe u de juiste VHD-grootte kiest, uw afbeelding generaliseert, recente updates (patches) toepast en aangepaste configuraties plant.

4. [Implementeer een virtuele machine vanaf een virtuele harde schijf](./cpp-deploy-vm-vhd.md) legt uit hoe u een VM registreert vanaf een Door Azure geïmplementeerde VHD.  Het bevat de benodigde hulpprogramma's en hoe u deze gebruiken om een VM-afbeelding van de gebruiker te maken en deze vervolgens te implementeren in Azure met behulp van de [Microsoft Azure-portal](https://ms.portal.azure.com/) of PowerShell-scripts. 

5. [In een afbeelding van een virtuele machine certificeren](./cpp-certify-vm.md) wordt uitgelegd hoe u een VM-afbeelding voor Azure Marketplace-certificering testen en verzenden. Hierin wordt uitgelegd waar u het *hulpprogramma voor certificeringstest voor Azure Certified* krijgen en hoe u deze tool gebruiken om uw VM-afbeelding te certificeren. 

6. [Download SAS URI](./cpp-get-sas-uri.md) legt uit hoe u de uri voor gedeelde toegangshandtekeningen (SAS) voor uw VM-afbeelding(en) krijgen.
 
Als ondersteunend artikel bevat [url-problemen met algemene gedeelde toegangshandtekeningen](./cpp-common-sas-url-issues.md) een aantal veelvoorkomende problemen die u tegenkomen met Behulp van SAS-URL's en de bijbehorende mogelijke oplossingen.

Nadat u al deze stappen hebt voltooid, bent u klaar om [uw VM-aanbieding](./cpp-publish-offer.md) te publiceren naar de Azure Marketplace.
