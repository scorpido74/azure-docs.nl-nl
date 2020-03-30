---
title: Azure-woordenlijst - Azure-woordenboek | Microsoft Documenten
description: Gebruik de Azure-woordenlijst om de cloudterminologie op het Azure-platform te begrijpen. Deze korte Azure-woordenboek bevat definities voor algemene cloudtermen voor Azure.
keywords: Azure-woordenboek, cloudterminologie, Azure-woordenlijst, terminologiedefinities, cloudtermen
services: na
documentationcenter: na
author: MonicaRush
manager: jhubbard
editor: ''
ms.assetid: d7ac12f7-24b5-4bcd-9e4d-3d76fbd8d297
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: monicar
ms.openlocfilehash: 7c80ba6b15cec08da9bcefea243493a824ef66bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276476"
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Microsoft Azure-woordenlijst: een woordenboek met cloudterminologie op het Azure-platform

De Woordenlijst van Microsoft Azure is een kort woordenboek met cloudterminologie voor het Azure-platform. Zie ook:

* [Microsoft Azure en Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) - Definities van Azure-services en hun AWS-tegenhangers.<!-- I propose to link to https://azure.microsoft.com/services/ instead of this -->
* [Termen voor cloudcomputing](https://azure.microsoft.com/overview/cloud-computing-dictionary/) - Algemene termen voor cloudin de industrie.

## <a name="account"></a>account
Een account dat wordt gebruikt om toegang te krijgen tot en een Azure-abonnement te beheren. Het wordt vaak een Azure-account genoemd, hoewel een account een van deze kan zijn: een bestaand werk-, school- of persoonlijk Microsoft-account of een Office 365-gebruikersnaam en -wachtwoord. U ook een account maken om een Azure-abonnement te beheren wanneer u zich aanmeldt voor de [gratis proefversie.](https://azure.microsoft.com)  
Zie [Aanmelden voor een Azure-abonnement met uw Office 365-account](cost-management-billing/manage/office-365-account-for-azure-subscription.md) en accounts waarmee u zich [aanmelden.](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

## <a name="api-app"></a>API-app
Een andere naam voor [app-service-app](#app-service-app).

## <a name="app-service-app"></a>App Service-app
De rekenbronnen die [Azure App Service](app-service/overview.md) biedt voor het hosten van een website of webtoepassing, web-API of [back-end van mobiele apps.](app-service-mobile/app-service-mobile-value-prop.md) App Service-apps worden ook wel *App Services,* *web-apps,* *API-apps*en *mobiele apps*genoemd.

## <a name="availability-set"></a>beschikbaarheidsset
Een verzameling virtuele machines die samen worden beheerd om redundantie en betrouwbaarheid van toepassingen te bieden. Het gebruik van een beschikbaarheidsset zorgt ervoor dat tijdens een gepland of ongepland onderhoudsevenement ten minste één virtuele machine beschikbaar is.  
Zie [De beschikbaarheid van virtuele Windows-machines beheren](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) en de beschikbaarheid van virtuele [Linux-machines beheren](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="azure-classic-deployment-model"></a><a name="classic-model"></a>Azure-klassiek implementatiemodel
Een van de twee [implementatiemodellen](resource-manager-deployment-model.md) die worden gebruikt om resources in Azure te implementeren (het nieuwe model is Azure Resource Manager). Sommige Azure-services ondersteunen alleen het implementatiemodel van Resource Manager, sommige ondersteunen alleen het klassieke implementatiemodel en sommige ondersteunen beide. De documentatie voor elke Azure-service geeft aan welk model(en) ze ondersteunen.

## <a name="azure-command-line-interface-cli"></a><a name="cli"></a>Azure command-line interface (CLI)
Een command-line interface die kan worden gebruikt om Azure-services te beheren vanuit Windows, macOS en Linux.  Sommige services of servicefuncties kunnen alleen worden beheerd via PowerShell of de CLI. Zie [Azure CLI](/cli/azure)

## <a name="azure-powershell"></a><a name="powershell"></a>Azure PowerShell
Een command-line interface voor het beheren van Azure-services via een opdrachtregel van Windows-pc's. Sommige services of servicefuncties kunnen alleen worden beheerd via PowerShell of de CLI.
Bekijk [hoe u Azure PowerShell installeert en configureert](/powershell/azure/overview)

## <a name="azure-resource-manager-deployment-model"></a><a name="arm-model"></a>Azure Resource Manager-implementatiemodel
Een van de twee [implementatiemodellen](resource-manager-deployment-model.md) die worden gebruikt om resources in Microsoft Azure te implementeren (het andere is het klassieke implementatiemodel). Sommige Azure-services ondersteunen alleen het implementatiemodel van Resource Manager, sommige ondersteunen alleen het klassieke implementatiemodel en sommige ondersteunen beide. De documentatie voor elke Azure-service geeft aan welk model(en) ze ondersteunen.

## <a name="fault-domain"></a>foutdomein
Het verzamelen van virtuele machines in een beschikbaarheidsset die mogelijk tegelijkertijd kan mislukken. Een voorbeeld is een groep machines in een rack die een gemeenschappelijke stroombron en netwerkswitch delen. In Azure worden de virtuele machines in een beschikbaarheidsset automatisch gescheiden over meerdere foutdomeinen.  
Zie [De beschikbaarheid van virtuele Windows-machines beheren](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) of De beschikbaarheid van virtuele [Linux-machines beheren](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

## <a name="geo"></a>Geo
Een gedefinieerde grens voor gegevensresidentie die doorgaans twee of meer regio's bevat. De grenzen kunnen binnen of buiten de landsgrenzen liggen en worden beïnvloed door fiscale regelgeving. Elke geo heeft ten minste één regio. Voorbeelden van geo's zijn Azië-Pacific en Japan. Ook wel *aardrijkskunde*genoemd.  
Zie [Azure-regio's](best-practices-availability-paired-regions.md)

## <a name="geo-replication"></a>geo-replicatie
Het proces waarbij inhoud, zoals blobs, tabellen en wachtrijen in een regionaal paar, automatisch worden gerepliceerd.  
Zie [Actieve georeplicatie voor Azure SQL-database](sql-database/sql-database-geo-replication-overview.md)
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>installatiekopie
Een bestand dat het besturingssysteem en de toepassingsconfiguratie bevat die kunnen worden gebruikt om een willekeurig aantal virtuele machines te maken. In Azure zijn er twee soorten afbeeldingen: VM-afbeelding en OS-afbeelding. Een VM-afbeelding bevat een besturingssysteem en alle schijven die aan een virtuele machine zijn gekoppeld wanneer de afbeelding wordt gemaakt. Een OS-afbeelding bevat alleen een algemeen besturingssysteem zonder configuratie van gegevensschijven.  
Bekijk [Navigeren en selecteer Windows-afbeeldingen voor virtuele machines in Azure met PowerShell of de CLI](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="limits"></a>Grenzen
Het aantal resources dat kan worden gemaakt of de prestatiebenchmark die kan worden bereikt. Limieten worden meestal gekoppeld aan abonnementen, services en aanbiedingen.  
Zie [Azure-abonnements- en servicelimieten, quota en beperkingen](azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="load-balancer"></a>load balancer
Een bron die binnenkomend verkeer verdeelt over computers in een netwerk. In Azure distribueert een load balancer verkeer naar virtuele machines die zijn gedefinieerd in een load-balancer-set. Een [load balancer](load-balancer/load-balancer-overview.md) kan internet-facing, of het kan intern zijn.  

## <a name="mobile-app"></a>mobiele app
Een andere naam voor [App Service App](#app-service-app).

## <a name="offer"></a>offer
De prijzen, credits en bijbehorende voorwaarden die van toepassing zijn op een Azure-abonnement.  
Bekijk de [pagina Met azure-aanbiedingsgegevens](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>portal
De beveiligde webportal die wordt gebruikt voor het implementeren en beheren van Azure-services.

## <a name="region"></a>regio
Een gebied binnen een geo dat de landsgrenzen niet overschrijdt en een of meer datacenters bevat. Prijzen, regionale diensten en aanbiedingstypen worden op regioniveau blootgesteld. Een regio wordt meestal gekoppeld aan een andere regio, die kan oplopen tot enkele honderden mijlen afstand. Regionale paren kunnen worden gebruikt als een mechanisme voor noodherstel en scenario's met hoge beschikbaarheid. Ook wel *locatie*genoemd.  
Zie [Azure-regio's](best-practices-availability-paired-regions.md)

## <a name="resource"></a>resource
Een item dat deel uitmaakt van uw Azure-oplossing. Met elke Azure-service u verschillende typen resources implementeren, zoals databases of virtuele machines.   
Overzicht [van Azure Resource Manager](azure-resource-manager/management/overview.md)

## <a name="resource-group"></a>resourcegroep
Een container in Resource manager met gerelateerde resources voor een toepassing. De resourcegroep kan alle resources voor een toepassing bevatten, of alleen de resources die logisch zijn gegroepeerd. U kunt bepalen hoe resources worden toegewezen aan resourcegroepen op basis van wat voor uw organisatie het meest zinvol is.  
Overzicht [van Azure Resource Manager](azure-resource-manager/management/overview.md)

## <a name="resource-manager-template"></a><a name="arm-template"></a>Sjabloon ResourceManager
Een JSON-bestand dat een of meer Azure-resources declaratief definieert en dat afhankelijkheden tussen de geïmplementeerde resources definieert. De sjabloon kan worden gebruikt om de resources consistent en herhaaldelijk te implementeren.  
Zie [Sjablonen voor ontwerpen van Azure Resource Manager](resource-group-authoring-templates.md)

## <a name="resource-provider"></a>resourceprovider
Een service die de resources levert die u implementeren en beheren via Resource Manager. Elke resourceprovider biedt bewerkingen voor het werken met de resources die zijn geïmplementeerd. Resourceproviders zijn toegankelijk via de Azure-portal, Azure PowerShell en verschillende programmeer-SDK's.  
Overzicht [van Azure Resource Manager](azure-resource-manager/management/overview.md)

## <a name="role"></a>role
Een middel voor het beheren van toegang die kan worden toegewezen aan gebruikers, groepen en services. Rollen kunnen acties uitvoeren zoals Azure-resources maken, beheren en lezen.  
Zie [RBAC: Ingebouwde rollen](role-based-access-control/built-in-roles.md)

## <a name="service-level-agreement-sla"></a><a name="sla"></a>serviceniveauovereenkomst (SLA)
De overeenkomst waarin de toezeggingen van Microsoft voor uptime en connectiviteit worden beschreven. Elke Azure-service heeft een specifieke SLA.  
[Serviceniveauovereenkomsten bekijken](https://azure.microsoft.com/support/legal/sla/)

## <a name="shared-access-signature-sas"></a><a name="sas"></a>handtekening voor gedeelde toegang (SAS)
Een handtekening waarmee u beperkte toegang tot een resource verlenen, zonder uw accountsleutel bloot te geven. Azure Storage gebruikt bijvoorbeeld [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) om clienttoegang te verlenen tot objecten zoals blobs. [IoT Hub gebruikt SAS](iot-hub/iot-hub-devguide-security.md#security-tokens) om apparaten toestemming te geven om telemetrie te verzenden.

## <a name="storage-account"></a>opslagaccount
Een account dat u toegang geeft tot de Azure Blob-, Queue-, Table- en File-services in Azure Storage. De naam van het opslagaccount definieert de unieke naamruimte voor Azure Storage-gegevensobjecten.  
[Zie Over Azure-opslagaccounts](storage/common/storage-create-storage-account.md)

## <a name="subscription"></a>abonnement
Een overeenkomst van een klant met Microsoft waarmee ze Azure-services kunnen verkrijgen. De abonnementsprijzen en bijbehorende voorwaarden worden bepaald door de aanbieding die voor het abonnement is gekozen.
Bekijk [microsoft online-abonnementsovereenkomst](https://azure.microsoft.com/support/legal/subscription-agreement/) en [hoe Azure-abonnementen zijn gekoppeld aan Azure Active Directory](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

## <a name="tag"></a>tag
Een indexeringsterm waarmee u resources categoriseren op basis van uw vereisten voor beheer of facturering. Wanneer u een complexe verzameling resources hebt, u tags gebruiken om deze elementen te visualiseren op de manier die het meest zinvol is. U kunt bijvoorbeeld resources taggen die een vergelijkbare rol hebben in uw organisatie of bij dezelfde afdeling horen.  
Zie [Labels gebruiken om uw Azure-bronnen te ordenen](resource-group-using-tags.md)

## <a name="update-domain"></a>domein bijwerken
Het verzamelen van virtuele machines in een beschikbaarheidsset die tegelijkertijd wordt bijgewerkt. Virtuele machines in hetzelfde updatedomein worden opnieuw gestart tijdens gepland onderhoud. Azure start nooit meer dan één updatedomein tegelijk opnieuw op. Ook wel aangeduid als een upgrade domein.  
Zie [De beschikbaarheid van virtuele Windows-machines beheren](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) en de beschikbaarheid van virtuele [Linux-machines beheren](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-machine"></a><a name="vm"></a>virtuele machine
De software-implementatie van een fysieke computer die een besturingssysteem draait. Meerdere virtuele machines kunnen tegelijkertijd op dezelfde hardware worden uitgevoerd. In Azure zijn virtuele machines beschikbaar in verschillende formaten.  
Documentatie [voor virtuele machines bekijken](https://azure.microsoft.com/documentation/services/virtual-machines/)

## <a name="virtual-machine-extension"></a><a name="vm-extension"></a>extensie virtuele machine
Een bron die gedragingen of functies implementeert die andere programma's helpen werken of de mogelijkheid bieden om te communiceren met een draaiende computer. U bijvoorbeeld de VM Access-extensie gebruiken om rastoegangswaarden op een virtuele Azure-machine opnieuw in te stellen of te wijzigen.
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
[Zie Over virtuele machine-extensies en -functies (Windows)](virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) of Over virtuele [machine-extensies en -functies (Linux)](virtual-machines/linux/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-network"></a><a name="vnet"></a>virtueel netwerk
Een netwerk dat connectiviteit biedt tussen uw Azure-resources die is geïsoleerd van alle andere Azure-tenants. Met een [Azure VPN Gateway](vpn-gateway/vpn-gateway-about-vpngateways.md) u verbindingen tot stand brengen tussen virtuele netwerken en tussen een virtueel netwerk en een on-premises netwerk. U de IP-adresblokken, DNS-instellingen, beveiligingsbeleid en routetabellen binnen een virtueel netwerk volledig beheren.  
Bekijk [het overzicht van virtueel netwerk](virtual-network/virtual-networks-overview.md)  

## <a name="web-app"></a>Web-app
Een andere naam voor [App Service App](#app-service-app).

## <a name="see-also"></a>Zie ook

* [Aan de slag met Azure](https://azure.microsoft.com/get-started/)
* [Cloudresourcecentrum](https://azure.microsoft.com/resources/)  
* [Azure voor uw bedrijfstoepassing](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Azure in uw datacenter](https://azure.microsoft.com/overview/business-apps-on-azure/)

