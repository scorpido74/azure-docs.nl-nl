---
title: Een Azure Data Explorer-cluster & DB maken in uw virtuele netwerk
description: In dit artikel leert u hoe u een Azure Data Explorer-cluster maakt in uw virtuele netwerk.
author: orspod
ms.author: orspodek
ms.reviewer: basaba
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: 4dff471fa0f2194756409e01512ed223a1d46024
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241437"
---
# <a name="create-an-azure-data-explorer-cluster-in-your-virtual-network"></a>Een Azure Data Explorer-cluster maken in uw virtuele netwerk

Azure Data Explorer ondersteunt het implementeren van een cluster in een subnet in uw virtuele netwerk (VNet). Met deze mogelijkheid hebt u privé toegang tot het cluster vanuit uw virtuele Azure-netwerk of on-premise, toegangsbron zoals Event Hub en Storage in uw virtuele netwerk en beperkt u inkomend en uitgaand verkeer.

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* Meld u aan bij [Azure Portal](https://portal.azure.com/).

## <a name="create-network-security-group-nsg"></a>NsG (Network Security Group) maken

[Network Security Groups (NSG)](/azure/virtual-network/security-overview) bieden de mogelijkheid om de toegang tot het netwerk binnen een VNet te beheren. Azure Data Explorer is toegankelijk via twee eindpunten HTTP's (443) en TDS (1433). De volgende NSG-regels moeten zijn geconfigureerd om toegang te krijgen tot deze eindpunten voor beheer, bewaking en goede werking van uw cluster.

Ga als u de netwerkbeveiligingsgroep maken:

1. Selecteer de **knop + Een resource maken** in de linkerbovenhoek van de portal.
1. Zoeken naar *netwerkbeveiligingsgroep*.
1. Selecteer **onder Netwerkbeveiligingsgroep**onder aan het scherm de optie **Maken**.
1. Vul in het venster **netwerkbeveiligingsgroep maken** de volgende gegevens in.

   ![NSG-formulier maken](media/vnet-create-cluster-portal/network-security-group.png)

    **Instelling** | **Voorgestelde waarde** | **Veldbeschrijving**
    |---|---|---|
    | Abonnement | Uw abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken voor uw cluster.|
    | Resourcegroep | Uw resourcegroep | Gebruik een bestaande resourcegroep of maak een nieuwe. |
    | Name | AzureDataExplorerNsg | Kies een naam die uw Network Security Group (NSG) identificeert in de resourcegroep.  |
    | Regio | *West-VS* | Selecteer de regio die het beste voldoet aan uw behoeften.
    | | | |

1. Selecteer **Bekijken + maken** om de gegevens van uw cluster te controleren, en **Maken** voor het inrichten van het cluster.

1. Nadat de implementatie is voltooid, selecteert u **Ga naar resource**.

    ![Ga naar resource](media/vnet-create-cluster-portal/notification-nsg.png)

1. Selecteer op het tabblad **Binnenkomende beveiligingsregels** de optie **+Toevoegen**.
1. Vul in het venster **Binnenkomende beveiligingsregel** de volgende gegevens in.

    ![NsG-inkomende regelvorm maken](media/vnet-create-cluster-portal/nsg-inbound-rule.png)

    **Instelling** | **Voorgestelde waarde** 
    |---|---|
    | Bron | ServiceTag (ServiceTag)
    | Bronservicetag | AzureDataExplorerBeheer
    | Poortbereiken van bron | *
    | Doel | VirtualNetwork
    | Poortbereiken van doel | *
    | Protocol | TCP
    | Actie | Toestaan
    | Prioriteit | 100
    | Name | AllowAzureDataExplorerManagement
    | | |
    
1. Herhaal de vorige twee stappen voor alle inkomende en uitgaande afhankelijkheden op basis [van afhankelijkheden voor VNet-implementatie](/azure/data-explorer/vnet-deloyment#dependencies-for-vnet-deployment). Als alternatief kunnen uitgaande regels worden vervangen door enkele regel om *internet* toe te staan voor poorten 443 en 80.
    
    De NSG-regels voor inkomende en uitgaande afhankelijkheden moeten er als volgt uitzien:

    ![NSG-regels](media/vnet-create-cluster-portal/nsg-rules.png)

## <a name="create-public-ip-addresses"></a>Openbare IP-adressen maken

Ga als lid van het nieuwe adres (Engine):

1. Selecteer de **knop + Een resource maken** in de linkerbovenhoek van de portal.
1. Zoeken naar *netwerkbeveiligingsgroep*.
1. Selecteer **onder Openbaar IP-adres**onder aan het scherm de optie **Maken**.
1. Voer in het deelvenster **Openbaar IP-adres maken** de volgende gegevens in.
   
   ![Openbaar IP-formulier maken](media/vnet-create-cluster-portal/public-ip-blade.png)

    **Instelling** | **Voorgestelde waarde** | **Veldbeschrijving**
    |---|---|---|
    | IP-versie | IPv4 | Selecteer de IP-versie. We ondersteunen alleen IPv4.|
    | Sku | Standard | We hebben **standaard nodig** voor het URI-eindpunt (Engine) .We require Standard for query (Engine) URI endpoint. |
    | Name | motor-pip | Kies een naam die uw openbare IP-adres in de resourcegroep identificeert.
    | Abonnement | Uw abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken voor uw openbare IP.Select the Azure subscription that you want to use for your public IP.|
    | Resourcegroep | Uw resourcegroep | Gebruik een bestaande resourcegroep of maak een nieuwe. |
    | Locatie | *West-VS* | Selecteer de regio die het beste voldoet aan uw behoeften.
    | | | |

1. Selecteer **Maken** om het openbare IP-adres te maken.

1. Om het openbare IP-adres (Data Management) te maken, volgt u dezelfde instructies en selecteert u 
    * **Sku**: Basic
    * **IP-adrestoewijzing**: Statisch

## <a name="create-virtual-network-and-subnet"></a>Virtueel netwerk en subnet maken

Ga als het virtuele netwerk en het subnet:

1. Selecteer de **knop + Een resource maken** in de linkerbovenhoek van de portal.
1. Zoeken naar *virtueel netwerk*.
1. Selecteer **onder Virtueel netwerk**onder aan het scherm de optie **Maken**.
1. Voer in het venster **Virtueel netwerk maken** de volgende informatie in.

   ![Virtueel netwerkformulier maken](media/vnet-create-cluster-portal/vnet-blade.png)

    **Instelling** | **Voorgestelde waarde** | **Veldbeschrijving**
    |---|---|---|
    | Abonnement | Uw abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken voor uw cluster.|
    | Resourcegroep | Uw resourcegroep | Gebruik een bestaande resourcegroep of maak een nieuwe. |
    | Name | AzureDataExplorerVnet | Kies een naam die uw virtuele netwerk in de resourcegroep identificeert.
    | Regio | *West-VS* | Selecteer de regio die het beste voldoet aan uw behoeften.
    | | | |

    > [!NOTE]
    > Voor productieworkloads, plan uw subnetgrootte op [basis van plan subnetgrootte in uw VNet](/azure/data-explorer/vnet-deloyment#plan-subnet-size-in-your-vnet)

1. Selecteer **Bekijken + maken** om de gegevens van uw cluster te controleren, en **Maken** voor het inrichten van het cluster.

1. Nadat de implementatie is voltooid, selecteert u **Ga naar resource**.
1. Ga naar **Subnetten** blad en selecteer het **standaard** subnet.
    
    ![Subnetten blad](media/vnet-create-cluster-portal/subnets.png)

1. In **default** het standaardsubnetvenster:
    1. Selecteer uw **netwerkbeveiligingsgroep** in het vervolgkeuzemenu. 
    1. Selecteer de naam van uw netwerkbeveiligingsgroep in dit geval **AzureDataExplorerNsg**. 
    1. **Opslaan**

    ![Subnet configureren](media/vnet-create-cluster-portal/subnet-nsg.png)

## <a name="create-a-cluster"></a>Een cluster maken

Maak een Azure Data Explorer-cluster met een gedefinieerde set reken- en opslagbronnen in een Azure-brongroep zoals beschreven in [een cluster maken.](create-cluster-database-portal.md#create-a-cluster)

1. Voordat u het maken van een cluster wilt afronden, selecteert u in het **clustervenster Een Azure Data Explorer** maken het tabblad **Netwerk** om virtuele netwerkdetails op te geven met behulp van de bronnen die in de vorige tabbladen zijn gemaakt:

   ![Clustervnet-formulier maken](media/vnet-create-cluster-portal/create-cluster-form-vnet.png)

    **Instelling** | **Voorgestelde waarde** | **Veldbeschrijving**
    |---|---|---|
    | Abonnement | Uw abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken voor netwerkbronnen.|
    | Virtual Network | AzureDataExplorerVnet | Kies het virtuele netwerk dat in de vorige stappen is gemaakt.
    | Subnet | standaardinstelling | Kies het subnet dat in de vorige stappen is gemaakt.
    | Openbaar IP-query's | motor-pip | Kies het ip-adres van query's dat in de vorige stappen is gemaakt.
    | Gegevensopname Openbaar IP-adres | dm-pip dm-pip | Kies het inname-openbaar IP dat in de vorige stappen is gemaakt.
    | | | |

1. Selecteer **Controleren + maken** om uw cluster te maken.
1. Nadat de implementatie is voltooid, selecteert u **Ga naar resource**.

Als u uw Azure Data Explorer-cluster wilt implementeren in uw virtuele netwerk, gebruikt u het [cluster Azure Data Explorer implementeren in uw VNet](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) Azure Resource Manager-sjabloon.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Azure Data Explorer implementeren in uw virtuele netwerk](vnet-create-cluster-portal.md)