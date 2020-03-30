---
title: Zelfstudie om een runtime van Azure-SSIS-integratie te configureren om lid te worden van een virtueel netwerk
description: Meer informatie over het deelnemen aan een runtime van Azure-SSIS-integratie naar een virtueel Azure-netwerk.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 1/10/2020
ms.openlocfilehash: 065610a9de4898d012cef8a16849c09a81f0774c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841094"
---
# <a name="configure-an-azure-sql-server-integration-services-ssis-integration-runtime-ir-to-join-a-virtual-network"></a>Een SSIS-integratierun (Azure-SQL Server Integration Services) (IR) configureren om lid te worden van een virtueel netwerk

Deze zelfstudie bevat basisstappen voor het gebruik van de Azure-portal om een SSIS-runtime (Azure-SQL Server Integration Services) (SSIS) te configureren om lid te worden van een virtueel netwerk.

De stappen omvatten:

- Een virtueel netwerk configureren.
- Sluit u aan bij de Azure-SSIS IR in een virtueel netwerk vanuit de Azure Data Factory-portal.

## <a name="prerequisites"></a>Vereisten

- **Runtime azure-SSIS-integratie**. Als u geen runtime van Azure-SSIS-integratie hebt, moet u voor aanvang [een runtime voor Azure-SSIS-integratie inAzure-SSIS-integratie](tutorial-deploy-ssis-packages-azure.md) inrichten.

- **Toestemming van de gebruiker**. De gebruiker die azure-SSIS IR maakt, moet de [roltoewijzing](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal#list-role-assignments-for-a-user-at-a-scope) ten minste op azure data factory-bron hebben met een van de onderstaande opties:

    - Gebruik de ingebouwde rol netwerkbijdrager. Deze rol wordt geleverd met de _Microsoft.Network\* /_ toestemming, die een veel groter bereik dan nodig heeft.
    - Maak een aangepaste rol die alleen de benodigde _Microsoft.Network/virtualNetworks/\*/join/action-toestemming_ bevat. Als u ook uw eigen openbare IP-adressen voor Azure-SSIS IR wilt toevoegen terwijl u deze wilt samenvoegen tot een virtueel Azure Resource Manager-netwerk, u ook _Microsoft.Network/publicIPAddresses/*/join/action-machtigingen_ in de rol opnemen.

- **Virtueel netwerk**.

    - Als u geen virtueel netwerk hebt, [maakt u een virtueel netwerk met de Azure-portal.](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

    - Zorg ervoor dat de brongroep van het virtuele netwerk bepaalde Azure-netwerkbronnen kan maken en verwijderen.
    
        De Azure-SSIS IR moet bepaalde netwerkbronnen maken onder dezelfde brongroep als het virtuele netwerk. Deze bronnen omvatten:
        - Een Azure load balancer, met de naam * \<Guid>-azurebatch-cloudserviceloadbalancer*
        - Een netwerkbeveiligingsgroep met de\<naam * Guid>-azurebatch-cloudservicenetworksecuritygroup
        - Een Openbaar IP-adres van Azure, met de naam -azurebatch-cloudservicepublicip
    
        Deze bronnen worden gemaakt wanneer uw Azure-SSIS IR wordt gestart. Ze worden verwijderd wanneer uw Azure-SSIS IR stopt. Als u wilt voorkomen dat uw Azure-SSIS IR wordt geblokkeerd, u deze netwerkbronnen niet opnieuw gebruiken in uw andere bronnen.

    - Zorg ervoor dat u geen [resourcevergrendeling](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) hebt op de brongroep/het abonnement waartoe het virtuele netwerk behoort. Als u een vergrendelbericht/verwijdervergrendeling voor lezen configureert, mislukt en stopt u uw Azure-SSIS IR of reageert deze niet meer.

    - Zorg ervoor dat u geen Azure-beleid hebt dat voorkomt dat de volgende resources worden gemaakt onder de brongroep/-abonnement waartoe het virtuele netwerk behoort:
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

- Onderstaande **scenario's voor netwerkconfiguratie** komen niet aan bod in deze zelfstudie:
    - Als u uw eigen openbare IP-adressen meeneemt voor de Azure-SSIS IR.
    - Als u uw eigen DNS-server (Domain Name System) gebruikt.
    - Als u een netwerkbeveiligingsgroep (NSG) op het subnet gebruikt.
    - Als u Azure ExpressRoute of een door de gebruiker gedefinieerde route (UDR) gebruikt.
    - Als u aangepaste Azure-SSIS IR gebruikt.
    
    Voor meer informatie, controleer [de virtuele netwerkconfiguratie](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration).

## <a name="configure-a-virtual-network"></a>Een virtueel netwerk configureren

Gebruik de Azure-portal om een virtueel netwerk te configureren voordat u probeert er een Azure-SSIS IR bij aan te voegen.

1. Microsoft Edge of Google Chrome starten. Momenteel ondersteunen alleen deze webbrowsers de Gebruikersinterface van Data Factory.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **Meer services**. Filter voor en selecteer **Virtuele netwerken**.

1. Filter voor en selecteer uw virtuele netwerk in de lijst.

1. Selecteer **eigenschappen**op de pagina **Virtueel netwerk** .

1. Selecteer de kopieerknop voor **RESOURCE-id** om de bron-id voor het virtuele netwerk naar het klembord te kopiëren. Sla de id op vanaf het klembord in OneNote of een bestand.

1. Selecteer **Subnetten**in het linkermenu .

    - Controleer of het subnet dat u selecteert voldoende beschikbare adresruimte heeft voor het Azure-SSIS IR om te gebruiken. Laat beschikbare IP-adressen voor ten minste twee keer het IR-knooppuntnummer. Azure reserveert een aantal IP-adressen binnen elk subnet. Deze adressen kunnen niet worden gebruikt. De eerste en laatste IP-adressen van de subnetten zijn gereserveerd voor protocolconformiteit en er worden nog drie adressen gebruikt voor Azure-services. Zie [Zijn er beperkingen voor het gebruik van IP-adressen binnen deze subnetten?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
    - Selecteer het GatewaySubnet niet om een Azure-SSIS IR te implementeren. Het is speciaal voor virtuele netwerkgateways.
    - Gebruik geen subnet dat uitsluitend wordt bezet door andere Azure-services (bijvoorbeeld SQL Database-beheerde instantie, App Service, enzovoort).

1. Controleer of de Azure Batch-provider is geregistreerd in het Azure-abonnement met het virtuele netwerk. Of registreer de Azure Batch-provider. Als u al een Azure Batch-account in uw abonnement hebt, wordt uw abonnement geregistreerd voor Azure Batch. (Als u azure-SSIS IR maakt in de portal Gegevensfabriek, wordt de Azure Batch-provider automatisch voor u geregistreerd.)

   1. Selecteer In de Azure-portal in het linkermenu de optie **Abonnementen**.

   1. Selecteer uw abonnement.

   1. Selecteer aan de linkerkant **Resourceproviders**en bevestig dat **Microsoft.Batch** een geregistreerde provider is.

   ![Bevestiging van de status "Geregistreerd"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Als u **Microsoft.Batch** niet in de lijst ziet, maakt [u een leeg Azure Batch-account](../batch/batch-account-create-portal.md) in uw abonnement om deze te registreren. U het later verwijderen.

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Word lid van azure-SSIS IR naar een virtueel netwerk

Nadat u uw azure resource manager virtueel netwerk of klassiek virtueel netwerk hebt geconfigureerd, u de Azure-SSIS IR aansluiten bij het virtuele netwerk:

1. Microsoft Edge of Google Chrome starten. Momenteel ondersteunen alleen deze webbrowsers de Gebruikersinterface van Data Factory.

1. Selecteer **gegevensfabrieken**in de [Azure-portal](https://portal.azure.com)in het linkermenu . Als u **gegevensfabrieken** niet in het menu ziet, selecteert u **Meer services**en selecteert u vervolgens in de sectie INTELLIGENTIE **+ ANALYTICS** **gegevensfabrieken**.

   ![Lijst van gegevensfabrieken](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Selecteer uw gegevensfabriek met de Azure-SSIS IR in de lijst. U ziet de startpagina van uw gegevensfabriek. Selecteer de tegel **Auteur & Monitor.** U ziet de gebruikersinterface van de gegevensfabriek op een apart tabblad.

   ![Startpagina van de gegevensfactory](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Schakel in de gebruikersinterface van de gegevensfabriek over naar het tabblad **Bewerken,** selecteer **Verbindingen**en schakel over naar het tabblad **Runtimes voor integratie.**

   ![Tabblad 'Inburgeringslooptijden'](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Als uw Azure-SSIS IR wordt uitgevoerd, selecteert u in de lijst **Integratieruntimes** in de kolom **Acties** de knop **Stoppen** voor uw Azure-SSIS IR. U uw Azure-SSIS IR pas bewerken als u deze stopt.

   ![Stop de IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Selecteer in de lijst **Programma's voor integratie** in de kolom **Acties** de knop **Bewerken** voor uw Azure-SSIS IR.

   ![De runtime voor integratie bewerken](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Ga in het installatiepaneel voor de instelling van de integratieruntime door de secties **Algemene instellingen** en **SQL-instellingen** door de knop **Volgende te** selecteren.

1. Ga als een **opdeandere sectie Geavanceerde instellingen:**
   1. Schakel **het Selectievakje Een VNet selecteren voor uw Runtime Azure-SSIS-integratie om lid te worden, ADF in staat te stellen bepaalde netwerkbronnen te maken en optioneel het selectievakje Eigen statische openbare IP-adressen mee** te nemen.

   1. Selecteer **bij Abonnement**het Azure-abonnement met uw virtuele netwerk.

   1. Voor **Locatie**is dezelfde locatie van uw intredetijd voor integratie geselecteerd.

   1. Selecteer **bij Type**het type van uw virtuele netwerk: klassiek of Azure Resource Manager. We raden u aan een virtueel Azure Resource Manager-netwerk te selecteren, omdat klassieke virtuele netwerken binnenkort worden afgeschaft.

   1. Selecteer voor **VNet-naam**de naam van uw virtuele netwerk. Het moet dezelfde zijn die wordt gebruikt voor uw Azure SQL Database-server met eindpunten voor virtuele netwerkservices of beheerde instantie met privéeindpunt om SSISDB te hosten. Of het moet dezelfde zijn die is aangesloten op uw on-premises netwerk. Anders kan het elk virtueel netwerk zijn om uw eigen statische openbare IP-adressen voor Azure-SSIS IR mee te nemen.

   1. Selecteer **bij Subnet Name**de naam van subnet voor uw virtuele netwerk. Het moet dezelfde zijn die wordt gebruikt voor uw Azure SQL Database-server met eindpunten voor virtuele netwerkservice om SSISDB te hosten. Of het moet een ander subnet zijn dan het subnet dat wordt gebruikt voor uw beheerde instantie met privéeindpunt om SSISDB te hosten. Anders kan het elk subnet zijn om uw eigen statische openbare IP-adressen voor Azure-SSIS IR mee te nemen.

   1. Selecteer **VNet-validatie**. Als de validatie is geslaagd, selecteert u **Doorgaan**.

   ![Geavanceerde instellingen met een virtueel netwerk](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. Bekijk **in** de sectie Overzicht alle instellingen voor uw Azure-SSIS IR. Selecteer vervolgens **Bijwerken**.

1. Start uw Azure-SSIS IR door de knop **Start** te selecteren in de kolom **Acties** voor uw Azure-SSIS IR. Het duurt ongeveer 20 tot 30 minuten om de Azure-SSIS IR te starten die lid wordt van een virtueel netwerk.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het samenvoegen van Azure-SSIS IR naar een virtueel netwerk](join-azure-ssis-integration-runtime-virtual-network.md).
