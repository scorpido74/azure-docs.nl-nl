---
title: Azure Table in commercieel marktplaatsprogramma| Azure Marketplace
description: Leadbeheer configureren voor Azure Blob
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: dsindona
ms.openlocfilehash: 062252b007e22fcd2644c8b647fc0ecc2f5938cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285245"
---
# <a name="lead-management-instructions-for-azure-blob"></a>Instructies voor leadbeheer voor Azure Blob

>[!Caution]
>De Azure Blob-optie om leads van uw marktplaatsaanbieding te verwerken, is afgeschaft. Als u momenteel een aanbieding hebt gepubliceerd met leadbeheerconfiguratie voor Azure Blob, ontvangt u geen klantleads meer. Werk uw configuratie van het leadbeheer bij naar een van de andere opties voor leadbeheer. Meer informatie over de andere opties op de [bestemmingspagina voor leadbeheer."](./commercial-marketplace-get-customer-leads.md)

Als uw CRM-systeem (Customer Relationship Management) niet expliciet wordt ondersteund in het Partnercentrum voor het ontvangen van Azure Marketplace- en AppSource-leads, u een Azure Blob gebruiken om deze leads te verwerken. U er vervolgens voor kiezen om de gegevens te exporteren en te importeren in uw CRM-systeem. De instructies in dit artikel geven u het proces van het maken van een Azure Storage-account en een Azure Blob onder dat account. Bovendien u een nieuwe stroom maken met Microsoft Flow om een e-mailmelding te verzenden wanneer uw aanbieding een lead ontvangt.


## <a name="how-to-configure-azure-blob"></a>Azure Blob configureren

1. Als u geen Azure-account hebt, u [een gratis proefaccount maken.](https://azure.microsoft.com/pricing/free-trial/)
1. Nadat uw Azure-account actief is, meldt u zich aan bij de [Azure-portal.](https://portal.azure.com)
1. Maak in de Azure-portal een opslagaccount met de volgende procedure.  
    1. Selecteer **+Een resource maken** op de linkermenubalk.  Het **nieuwe** deelvenster (mes) wordt rechts weergegeven.
    2. Selecteer **Opslag** in het deelvenster **Nieuw.**  **Een lijst met aanbevolen** gegevens wordt rechts weergegeven.
    3. Selecteer het **opslagaccount** om het aanmaken van een account te starten.  Volg de instructies in het artikel [Een opslagaccount aanmaken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Stappen om een Azure-opslagaccount te maken](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    Selecteer [Snelstart-zelfstudie voor](https://docs.microsoft.com/azure/storage/)meer informatie over opslagaccounts .  Zie [opslagprijzen](https://azure.microsoft.com/pricing/details/storage/)voor meer informatie over opslagprijzen.

4. Wacht tot uw opslagaccount is ingericht, een proces dat doorgaans enkele minuten duurt.  Ga vervolgens naar uw opslagaccount vanaf de **startpagina** van de Azure-portal door **Alle resources weergeven** te selecteren of **alle bronnen** te selecteren op de linkernavigatiemenubalk van de Azure-portal.

    ![Toegang tot uw Azure-opslagaccount](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. Selecteer **access-sleutels** in het deelvenster Access en kopieer de waarde van de *verbindingstekenreeks* voor de sleutel. Sla deze waarde op, omdat dit de waarde van de *opslagaccountverbinding is* die u in de publicatieportal moet verstrekken om leads voor uw marktplaatsaanbieding te ontvangen.

     Een voorbeeld van een verbindingssteek is:

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Azure-opslagsleutel](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. Selecteer **Blobs**op de pagina met uw opslagaccount .

   ![Azure-opslagsleutel](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. Eenmaal op de blobs-pagina selecteert u de knop **+ Container.**

8. Typ een **naam** voor uw nieuwe container. De containernaam mag alleen kleine letters bevatten, moet beginnen met een letter of cijfer en mag alleen letters, cijfers en het streepje (-) bevatten. Zie [Containers, blobs en metagegevens benoemen en verwijzen](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)voor meer informatie over container- en blobnamen.

    Sla deze waarde op, omdat dit de *containernaamwaarde* is die u in de publicatieportal moet opgeven om leads voor uw marktplaatsaanbieding te ontvangen.

9. Stel het niveau van openbare toegang tot de container in als **Privé (geen anonieme toegang)**.

10. Selecteer **OK** om de container te maken.

    ![Nieuwe container](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-blob"></a>Uw aanbieding configureren om leads naar de Azure Blob te verzenden

Wanneer u klaar bent om de leadmanagementgegevens voor uw aanbieding in de publicatieportal te configureren, volgt u de volgende stappen:

1. Navigeer naar de pagina **Installatie van aanbieding** voor uw aanbieding.
2. Selecteer **Verbinding maken** onder de sectie Leadbeheer.

    ![Aanbieding verbinden](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-offer.png)

3. Selecteer **Azure Blob** voor de hoofdbestemming in het pop-upvenster Verbindingsdetails.

    ![Detail verbinden](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Geef de **tekenreeks Containernaam** en **Opslagaccountverbinding op die** u hebt gekregen door deze instructies te volgen.

    * Voorbeeld van containernaam:`marketplaceleadcontainer`
    * Voorbeeld van tekenreeks `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![opslagaccountverbinding: verbindingsdetail](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Selecteer **Opslaan**.

    > [!NOTE]
    > U moet de rest van de aanbieding voltooien en publiceren voordat u leads voor de aanbieding ontvangen.


