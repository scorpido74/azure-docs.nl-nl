---
title: Aanbevelingen voor gegevens & opslag-Azure Security Center
description: Dit document bevat aanbevelingen in Azure Security Center waarmee u uw gegevens en Azure SQL-service kunt beveiligen en in overeenstemming bent met het beveiligings beleid.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2019
ms.author: memildin
ms.openlocfilehash: 74ed55e1d460495bfa8d3d4c00bd37bb7f05260e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75552862"
---
# <a name="protect-azure-data-and-storage-services"></a>Azure-gegevens-en opslag Services beveiligen
Wanneer Azure Security Center mogelijke beveiligings problemen identificeert, worden er aanbevelingen gemaakt die u door het proces van het configureren van de benodigde besturings elementen leiden om uw resources te beschermen en te beveiligen.

In dit artikel wordt de **pagina gegevens beveiliging** van de sectie resource beveiliging van Security Center beschreven.

Zie [aanbevelingen voor gegevens en opslag](recommendations-reference.md#recs-datastorage)voor een volledige lijst met aanbevelingen die u op deze pagina kunt zien.


## <a name="view-your-data-security-information"></a>Uw gegevens beveiligings gegevens weer geven

1. Klik in de sectie **beveiligings hygiëne voor bronnen** op **gegevens-en opslag resources**.

    ![Gegevens & storage-resources](./media/security-center-monitoring/click-data.png)

    De pagina **gegevens beveiliging** wordt geopend met aanbevelingen voor gegevens bronnen.

    [![Gegevensbronnen](./media/security-center-monitoring/sql-overview.png)](./media/security-center-monitoring/sql-overview.png#lightbox)

    Vanaf deze pagina kunt u het volgende doen:

    * Klik op het tabblad **overzicht** om alle aanbevelingen voor de gegevens bronnen weer te geven die u wilt herstellen. 
    * Klik op elk tabblad en Bekijk de aanbevelingen per resource type.

    > [!NOTE]
    > Zie [Azure Storage versleuteling voor Data-at-rest](../storage/common/storage-service-encryption.md)voor meer informatie over opslag versleuteling.


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Een aanbeveling voor een gegevens bron herstellen

1. Klik op een resource in een van de resource tabbladen. De pagina informatie wordt geopend met een lijst met de aanbevelingen die moeten worden hersteld.

    ![Resource gegevens](./media/security-center-monitoring/sql-recommendations.png)

2. Klik op een aanbeveling. De pagina aanbeveling wordt geopend en toont de herstels **tappen** voor het implementeren van de aanbeveling.

   ![Herstelstappen](./media/security-center-monitoring/remediate1.png)

3. Klik op **actie ondernemen**. De pagina Resource-instellingen wordt weer gegeven.

    ![Aanbeveling inschakelen](./media/security-center-monitoring/remediate2.png)

4. Volg de herstels **tappen** en klik op **Opslaan**.


## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over de aanbevelingen die van toepassing zijn op andere Azure-resource typen:

* [Volledige referentie lijst met aanbevelingen voor de beveiliging van Azure Security Center](recommendations-reference.md)
* [Uw machines en toepassingen beveiligen in Azure Security Center](security-center-virtual-machine-protection.md)
* [Protecting your network in Azure Security Center](security-center-network-recommendations.md) (Uw netwerk beveiligen in Azure Security Center)