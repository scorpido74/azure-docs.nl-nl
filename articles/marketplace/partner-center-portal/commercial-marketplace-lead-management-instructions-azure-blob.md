---
title: Azure-tabel in het programma voor commerciële Marketplace | Azure Marketplace
description: Beheer van leads voor Azure-Blob configureren
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: evansma
ms.openlocfilehash: c67855422808f5ec4c81242edcece4e447f2f44f
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69902362"
---
# <a name="lead-management-instructions-for-azure-blob"></a>Instructies voor het beheer van potentiële klanten voor Azure Blob

>[!Caution]
>De optie Azure Blob voor het verwerken van leads van uw Marketplace-aanbieding is afgeschaft. Als u momenteel een aanbieding hebt gepubliceerd met lead management-configuratie voor Azure Blob, ontvangt u geen leads meer van de klant. Werk de configuratie van uw lead beheer bij naar een van de andere opties voor het beheer van leads. Meer informatie over de andere opties op de [landings pagina](./commercial-marketplace-get-customer-leads.md)voor het beheer van leads.

Als uw Customer Relationship Management-systeem (CRM) niet expliciet wordt ondersteund in het partner centrum voor het ontvangen van Azure Marketplace-en AppSource-leads, kunt u een Azure-Blob gebruiken om deze leads af te handelen. U kunt de gegevens vervolgens exporteren en importeren in uw CRM-systeem. De instructies in dit artikel geven u door het proces van het maken van een Azure Storage-account en een Azure-Blob onder dat account. Daarnaast kunt u een nieuwe stroom maken met behulp van Microsoft Flow om een e-mail bericht te verzenden wanneer uw aanbieding een lead ontvangt.


## <a name="how-to-configure-azure-blob"></a>Azure-Blob configureren

1. Als u geen Azure-account hebt, kunt u [een gratis proef account maken](https://azure.microsoft.com/pricing/free-trial/).
1. Nadat uw Azure-account actief is, meldt u zich aan bij de [Azure Portal](https://portal.azure.com).
1. Maak in de Azure Portal een opslag account met behulp van de volgende procedure.  
    1. Selecteer **+ een resource maken** in de menu balk links.  Het **nieuwe** deel venster (Blade) wordt aan de rechter kant weer gegeven.
    2. Selecteer **opslag** in het deel venster **Nieuw** .  Er wordt een **Aanbevolen** lijst aan de rechter kant weer gegeven.
    3. Selecteer het **opslag account** om te beginnen met het maken van het account.  Volg de instructies in het artikel [een opslag account maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Stappen voor het maken van een Azure Storage-account](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    Selecteer voor meer informatie over opslag accounts [Snelstartgids zelf studie](https://docs.microsoft.com/azure/storage/).  Zie [prijzen voor opslag](https://azure.microsoft.com/pricing/details/storage/)voor meer informatie over prijzen voor opslag.

4. Wacht tot uw opslag account is ingericht, een proces dat doorgaans een paar minuten in beslag neemt.  Ga vervolgens naar uw opslag account vanaf de **Start** pagina van de Azure portal door **alle resources weer geven** te selecteren of door **alle resources** te selecteren in de linker navigatie balk van de Azure Portal.

    ![Toegang tot uw Azure Storage-account](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. Selecteer in het deel venster opslag account de optie **toegangs sleutels** en kopieer de *verbindings reeks* waarde voor de sleutel. Sla deze waarde op als dit de waarde voor de *verbindings reeks voor het opslag account* is die u moet opgeven in de portal voor publiceren om leads voor uw Marketplace-aanbieding te ontvangen.

     Een voor beeld van een verbinding Sting is:

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Azure-opslag sleutel](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. Selecteer op de pagina opslag account deoptie blobs.

   ![Azure-opslag sleutel](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. Selecteer de knop **+ container** op de pagina blobs.

8. Voer een **naam** in voor de nieuwe container. De containernaam mag alleen kleine letters bevatten, moet beginnen met een letter of cijfer en mag alleen letters, cijfers en het streepje (-) bevatten. Zie [Naamgeving van en verwijzen naar containers, blobs en metagegevens](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) voor meer informatie over de namen van containers en blobs.

    Sla deze waarde op omdat dit de waarde voor de *container naam* is die u in de portal voor publiceren moet opgeven om leads voor uw Marketplace-aanbieding te ontvangen.

9. Stel het niveau van open bare toegang tot de container in als **privé (geen anonieme toegang)** .

10. Selecteer **OK** om de container te maken.

    ![Nieuwe container](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-blob"></a>Uw aanbieding configureren voor het verzenden van leads naar de Azure-Blob

Wanneer u klaar bent om de informatie over het beheer van leads voor uw aanbieding te configureren in de portal voor publiceren, volgt u de onderstaande stappen:

1. Navigeer naar de pagina voor het instellen van de **aanbieding** voor uw aanbieding.
2. Selecteer **verbinding maken** in het gedeelte Lead beheer.

    ![Connect-aanbieding](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-offer.png)

3. Selecteer in het pop-upvenster verbindings Details de optie **Azure Blob** voor de doel locatie van de lead.

    ![Details verbinden](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Geef de **container naam** en de **verbindings reeks voor het opslag account** op die u hebt gevolgd door deze instructies.

    * Container naam voor beeld:`marketplaceleadcontainer`
    * Voor beeld van verbindings reeks voor opslag account: `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net`![Verbindings Details](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Selecteer **Opslaan**.

    > [!NOTE]
    > U moet de configuratie van de rest van de aanbieding volt ooien en publiceren voordat u leads voor de aanbieding kunt ontvangen.


