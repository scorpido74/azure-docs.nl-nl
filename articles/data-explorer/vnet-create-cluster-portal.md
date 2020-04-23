---
title: Een Azure Data Explorer-cluster maken & data base in uw virtuele netwerk
description: In dit artikel leert u hoe u een Azure Data Explorer-cluster maakt in uw virtuele netwerk.
author: orspod
ms.author: orspodek
ms.reviewer: basaba
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: 99c32122fc48aaea7428fa559d7289713849f34e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548906"
---
# <a name="create-an-azure-data-explorer-cluster-in-your-virtual-network"></a>Een Azure Data Explorer-cluster maken in uw virtuele netwerk

Azure Data Explorer ondersteunt de implementatie van een cluster in een subnet in uw virtuele netwerk (VNet). Met deze mogelijkheid kunt u het cluster privé benaderen vanuit uw virtuele Azure-netwerk of een on-premises toegang tot resources, zoals Event hub en Storage in uw virtuele netwerk, en het binnenkomende en uitgaande verkeer beperken.

## <a name="prerequisites"></a>Vereisten

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="create-network-security-group-nsg"></a>Een netwerk beveiligings groep maken (NSG)

[Netwerk beveiligings groepen (NSG)](/azure/virtual-network/security-overview) bieden de mogelijkheid om netwerk toegang binnen een VNet te beheren. Azure Data Explorer kan worden geopend met twee eind punten HTTPs (443) en TDS (1433). De volgende NSG-regels moeten worden geconfigureerd voor toegang tot deze eind punten voor beheer, bewaking en goede werking van uw cluster.

De netwerk beveiligings groep maken:

1. Selecteer de knop **+ een resource maken** in de linkerbovenhoek van de portal.
1. Zoeken naar *netwerk beveiligings groep*.
1. Onder **netwerk beveiligings groep**, onder aan het scherm, selecteert u **maken**.
1. Vul de volgende gegevens in het venster **netwerk beveiligings groep maken** in.

   ![Formulier NSG maken](media/vnet-create-cluster-portal/network-security-group.png)

    **Instelling** | **Voorgestelde waarde** | **Veld Beschrijving**
    |---|---|---|
    | Abonnement | Uw abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken voor uw cluster.|
    | Resourcegroep | Uw resourcegroep | Gebruik een bestaande resourcegroep of maak een nieuwe. |
    | Naam | AzureDataExplorerNsg | Kies een naam die uw netwerk beveiligings groep (NSG) identificeert in de resource groep.  |
    | Regio | *VS-West* | Selecteer de regio die het beste voldoet aan uw behoeften.
    | | | |

1. Selecteer **Bekijken + maken** om de gegevens van uw cluster te controleren, en **Maken** voor het inrichten van het cluster.

1. Nadat de implementatie is voltooid, selecteert u **Ga naar resource**.

    ![Ga naar resource](media/vnet-create-cluster-portal/notification-nsg.png)

1. Klik op het tabblad **regels voor binnenkomende beveiliging** op **+ toevoegen**.
1. Vul in het venster **binnenkomende beveiligings regel toevoegen** de volgende gegevens in.

    ![Formulier voor NSG binnenkomende regel maken](media/vnet-create-cluster-portal/nsg-inbound-rule.png)

    **Instelling** | **Voorgestelde waarde** 
    |---|---|
    | Bron | ServiceTag
    | Bron servicetag | AzureDataExplorerManagement
    | Poortbereiken van bron | *
    | Doel | VirtualNetwork
    | Poortbereiken van doel | *
    | Protocol | TCP
    | Bewerking | Toestaan
    | Prioriteit | 100
    | Naam | AllowAzureDataExplorerManagement
    | | |
    
1. Herhaal de vorige twee stappen voor alle binnenkomende en uitgaande afhankelijkheden volgens de [afhankelijkheden voor de VNet-implementatie](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment). Uitgaande regels kunnen ook worden vervangen door één regel voor het toestaan van *Internet* voor de poorten 443 en 80.
    
    De NSG-regels voor binnenkomende en uitgaande afhankelijkheden moeten er als volgt uitzien:

    ![NSG-regels](media/vnet-create-cluster-portal/nsg-rules.png)

## <a name="create-public-ip-addresses"></a>Open bare IP-adressen maken

De open bare IP-adressen van de query (Engine) maken:

1. Selecteer de knop **+ een resource maken** in de linkerbovenhoek van de portal.
1. Zoeken naar *netwerk beveiligings groep*.
1. Selecteer onder aan het scherm **openbaar IP-adres**de optie **maken**.
1. Voer in het deel venster **openbaar IP-adres maken** de volgende informatie in.
   
   ![Openbaar IP-formulier maken](media/vnet-create-cluster-portal/public-ip-blade.png)

    **Instelling** | **Voorgestelde waarde** | **Veld Beschrijving**
    |---|---|---|
    | IP-versie | IPv4 | Selecteer de IP-versie. Alleen IPv4 wordt ondersteund.|
    | Sku | Standard | Er is een **standaard** -URI-eind punt voor query (Engine) vereist. |
    | Naam | Engine-PIP | Kies een naam die uw open bare IP-adres identificeert in de resource groep.
    | Abonnement | Uw abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken voor uw open bare IP-adres.|
    | Resourcegroep | Uw resourcegroep | Gebruik een bestaande resourcegroep of maak een nieuwe. |
    | Locatie | *VS-West* | Selecteer de regio die het beste voldoet aan uw behoeften.
    | | | |

1. Selecteer **maken** om het open bare IP-adres te maken.

1. Volg dezelfde instructies om het open bare IP-adres voor opname (Gegevensbeheer) te maken en selecteer 
    * **SKU**: basis
    * **IP-adres toewijzing**: statisch

## <a name="create-virtual-network-and-subnet"></a>Virtual Network en subnet maken

Het virtuele netwerk en subnet maken:

1. Selecteer de knop **+ een resource maken** in de linkerbovenhoek van de portal.
1. Zoeken naar *Virtual Network*.
1. Onder **Virtual Network**, onder aan het scherm, selecteert u **maken**.
1. Voer in het venster **virtueel netwerk maken** de volgende informatie in.

   ![Een virtueel netwerk formulier maken](media/vnet-create-cluster-portal/vnet-blade.png)

    **Instelling** | **Voorgestelde waarde** | **Veld Beschrijving**
    |---|---|---|
    | Abonnement | Uw abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken voor uw cluster.|
    | Resourcegroep | Uw resourcegroep | Gebruik een bestaande resourcegroep of maak een nieuwe. |
    | Naam | AzureDataExplorerVnet | Kies een naam die uw virtuele netwerk in de resource groep aanduidt.
    | Regio | *VS-West* | Selecteer de regio die het beste voldoet aan uw behoeften.
    | | | |

    > [!NOTE]
    > Plan uw subnet-grootte op basis van de grootte van het [subnet in uw VNet](/azure/data-explorer/vnet-deployment#plan-subnet-size-in-your-vnet) voor werk belastingen voor productie.

1. Selecteer **Bekijken + maken** om de gegevens van uw cluster te controleren, en **Maken** voor het inrichten van het cluster.

1. Nadat de implementatie is voltooid, selecteert u **Ga naar resource**.
1. Ga naar de Blade **subnetten** en selecteer het **standaard** subnet.
    
    ![Blade subnetten](media/vnet-create-cluster-portal/subnets.png)

1. In het **standaard** -subnet-venster:
    1. Selecteer uw **netwerk beveiligings groep** in de vervolg keuzelijst. 
    1. Selecteer de naam van de netwerk beveiligings groep, in dit geval **AzureDataExplorerNsg**. 
    1. **Opslaan**

    ![Subnet configureren](media/vnet-create-cluster-portal/subnet-nsg.png)

## <a name="create-a-cluster"></a>Een cluster maken

Maak een Azure Data Explorer-cluster met een gedefinieerde set reken-en opslag resources in een Azure-resource groep zoals beschreven in [een cluster maken](create-cluster-database-portal.md#create-a-cluster).

1. Voordat u het maken van het cluster voltooit, selecteert u in het venster **een Azure Data Explorer-cluster maken** het tabblad **netwerk** om details van het virtuele netwerk op te geven met behulp van de resources die zijn gemaakt in de vorige tabbladen:

   ![Het formulier vnet maken](media/vnet-create-cluster-portal/create-cluster-form-vnet.png)

    **Instelling** | **Voorgestelde waarde** | **Veld Beschrijving**
    |---|---|---|
    | Abonnement | Uw abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken voor netwerk bronnen.|
    | Virtual Network | AzureDataExplorerVnet | Kies het virtuele netwerk dat u in de vorige stappen hebt gemaakt.
    | Subnet | standaardinstelling | Kies het subnet dat u in de vorige stappen hebt gemaakt.
    | Query uitvoeren op openbaar IP | Engine-PIP | Kies het open bare IP-adres van de query die u in de vorige stappen hebt gemaakt.
    | Gegevens opname openbaar IP-adres | DM-PIP | Kies het open bare opname-IP-adres dat u in de vorige stappen hebt gemaakt.
    | | | |

1. Selecteer **beoordeling + maken** om uw cluster te maken.
1. Nadat de implementatie is voltooid, selecteert u **Ga naar resource**.

Als u uw Azure Data Explorer-cluster wilt implementeren in uw virtuele netwerk, gebruikt u het [azure Data Explorer-cluster implementeren in uw VNet](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) -Azure Resource Manager sjabloon.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Azure Data Explorer implementeren in uw Virtual Network](vnet-create-cluster-portal.md)