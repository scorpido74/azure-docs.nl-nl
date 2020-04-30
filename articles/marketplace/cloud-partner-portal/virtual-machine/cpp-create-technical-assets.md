---
title: Technische activa maken voor een aanbieding van een virtuele machine voor Azure Marketplace
description: Hierin wordt uitgelegd hoe u de technische activa voor een aanbieding van een virtuele machine maakt in de Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: dsindona
ms.openlocfilehash: c6ca017e18a83f0745140cba15d9c894e5a21c32
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82148138"
---
# <a name="create-technical-assets-for-a-virtual-machine-offer"></a>Technische activa maken voor een aanbieding van een virtuele machine

> [!IMPORTANT]
> Vanaf 13 april 2020 begint het beheer van uw Azure Virtual Machine-aanbiedingen naar het partner centrum. Na de migratie maakt en beheert u uw aanbiedingen in partner centrum. Volg de instructies in de [technische assets van uw virtuele Azure-machines maken](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) om uw gemigreerde aanbiedingen te beheren.

In deze sectie vindt u instructies voor het maken en configureren van de technische assets voor een virtuele machine (VM)-aanbieding voor Azure Marketplace.  Een VM bevat twee onderdelen: de oplossing virtuele harde schijf (VHD) en optionele gekoppelde gegevens schijven.  

- *Virtuele harde schijven (vhd's)* met het besturings systeem en uw oplossing, die u kunt implementeren met uw Azure Marketplace-aanbieding. Het proces van het voorbereiden van de VHD verschilt, afhankelijk van het feit of het een op Linux gebaseerde, op Windows gebaseerde of een op een maatwerk gebaseerde VM is.
- *Gegevens schijven* vertegenwoordigen toegewezen, permanente opslag voor een virtuele machine. Gebruik *niet* de oplossings-VHD (bijvoorbeeld het `C:` station) om permanente gegevens op te slaan.

Een VM-installatie kopie bevat één besturingssysteem schijf en nul of meer gegevens schijven. Per schijf is één VHD vereist. Voor zelfs lege gegevens schijven moet een VHD worden gemaakt.
U moet het VM-besturings systeem, de VM-grootte, de te openen poorten en Maxi maal 15 gekoppelde gegevens schijven configureren.

> [!TIP] 
> Ongeacht welk besturings systeem u gebruikt, voegt u alleen het minimum aantal gegevens schijven toe dat nodig is voor de SKU. Klanten kunnen geen schijven verwijderen die deel uitmaken van een installatie kopie op het moment van de implementatie, maar ze kunnen altijd schijven toevoegen tijdens of na de implementatie. 

> [!IMPORTANT]
> *Wijzig het aantal schijven in een nieuwe installatie kopie versie niet.* Als u de gegevens schijven in de installatie kopie opnieuw moet configureren, definieert u een nieuwe SKU. Als u een nieuwe versie van een installatie kopie met verschillende schijf aantallen publiceert, kan de nieuwe implementatie worden verkleind op basis van de nieuwe versie van de installatie kopie in gevallen van automatische schaling, automatische implementatie van oplossingen via Azure Resource Manager sjablonen en andere scenario's.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="fundamental-technical-knowledge"></a>Fundamentele technische kennis

Het ontwerpen, bouwen en testen van deze assets vergt tijd en vereist technische kennis van het Azure-platform en de technologieën die worden gebruikt om de aanbieding te bouwen. Naast uw oplossings domein moet uw technische team kennis hebben van de volgende micro soft-technologieën: 
-    Basis informatie over [Azure-Services](https://azure.microsoft.com/services/) 
-    Azure- [toepassingen ontwerpen en ontwikkelen](https://azure.microsoft.com/solutions/architecture/)
-    Praktische kennis van [Azure virtual machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage) en [Azure-netwerken](https://azure.microsoft.com/services/?filter=networking)
-    Werk ervaring van [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-    Praktische kennis van [JSON](https://www.json.org/)


## <a name="suggested-tools"></a>Aanbevolen hulpprogram ma's 

Kies een of beide van de volgende script omgevingen om Vhd's en Vm's te beheren:
-    [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-    [Azure-CLI](https://docs.microsoft.com/cli/azure)

Daarnaast raden wij aan de volgende hulpprogram ma's toe te voegen aan uw ontwikkel omgeving: 

-    [Azure Opslagverkenner](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-    [Visual Studio Code](https://code.visualstudio.com/)
    *    Extensie: [Azure Resource Manager-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *    Extensie: [beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *    Extensie: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

We raden u ook aan om de beschik bare hulpprogram ma's op de [Azure Ontwikkelhulpprogramma's](https://azure.microsoft.com/tools/) -pagina te bekijken en als u Visual Studio, de [Visual Studio Marketplace](https://marketplace.visualstudio.com/), gebruikt.


## <a name="next-steps"></a>Volgende stappen

In de volgende artikelen in deze sectie worden de stappen beschreven voor het maken en registreren van deze VM-assets:

1. [Een virtuele harde schijf met Azure-compatibel maken](./cpp-create-vhd.md) hierin wordt uitgelegd hoe u een op Linux of Windows gebaseerde VHD maakt die compatibel is met Azure.  Het bevat aanbevolen procedures, zoals het aanpassen van de grootte, het herstellen van patches en het voorbereiden van de virtuele machine voor het uploaden.

2. [Verbinding maken met de virtuele machine](./cpp-connect-vm.md) hier wordt uitgelegd hoe u op afstand verbinding maakt met uw ZOJUIST gemaakte VM en zich aanmeldt.  In dit artikel wordt ook uitgelegd hoe u kunt voor komen dat de virtuele machine wordt bespaard op gebruiks kosten.

3. [Configureer de virtuele machine](./cpp-configure-vm.md) wordt uitgelegd hoe u de juiste VHD-grootte kiest, uw installatie kopie generaliseert, recente updates (patches) toepast en aangepaste configuraties plant.

4. [Een virtuele machine implementeren vanaf een virtuele harde schijf](./cpp-deploy-vm-vhd.md) wordt uitgelegd hoe u een VM kunt registreren vanuit een door Azure geïmplementeerde VHD.  De lijst bevat de vereiste hulpprogram ma's en hoe u deze kunt gebruiken om een VM-installatie kopie van een gebruiker te maken en deze vervolgens te implementeren in azure met behulp van de [Microsoft Azure-Portal](https://ms.portal.azure.com/) -of Power shell-scripts. 

5. [Een installatie kopie van een virtuele machine certificeren](./cpp-certify-vm.md) hierin wordt uitgelegd hoe u een VM-installatie kopie kunt testen en verzenden voor Azure Marketplace-certificering. Hierin wordt uitgelegd waar u het *certificerings test hulpprogramma voor het hulp programma voor Azure Certified* kunt ophalen en hoe u dit hulp programma kunt gebruiken om uw VM-installatie kopie te certificeren. 

6. Met [SAS URI ophalen](./cpp-get-sas-uri.md) wordt uitgelegd hoe u de SAS-URI (Shared Access Signature) voor uw VM-installatie kopie (n) kunt ophalen.
 
Als ondersteunend artikel worden veelvoorkomende problemen met de [URL van gedeelde Access-hand tekeningen](./cpp-common-sas-url-issues.md) vermeld in een aantal veelvoorkomende fouten die kunnen optreden met SAS uri's en de bijbehorende mogelijke oplossingen.

Nadat u al deze stappen hebt voltooid, kunt u [uw VM-aanbieding publiceren](./cpp-publish-offer.md) op de Azure Marketplace.
