---
title: Aanbevelingen voor & opslag - Azure Security Center
description: Dit document richt aanbevelingen in Azure Security Center waarmee u uw gegevens en Azure SQL-service beschermen en in overeenstemming blijft met het beveiligingsbeleid.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552862"
---
# <a name="protect-azure-data-and-storage-services"></a>Azure-gegevens- en opslagservices beveiligen
Wanneer Azure Security Center potentiële beveiligingsproblemen identificeert, worden er aanbevelingen gemaakt die u begeleiden bij het configureren van de benodigde besturingselementen om uw resources te verharden en te beschermen.

In dit artikel wordt de **pagina Gegevensbeveiliging** van het gedeelte resourcebeveiliging van Security Center uitgelegd.

Zie [Aanbevelingen voor gegevens en opslag](recommendations-reference.md#recs-datastorage)voor een volledige lijst met aanbevelingen die u op deze pagina zien.


## <a name="view-your-data-security-information"></a>Uw gegevensbeveiligingsgegevens weergeven

1. Klik in de sectie **Resourcebeveiligingshygiëne** op **Gegevens en opslagbronnen**.

    ![Gegevens & opslagbronnen](./media/security-center-monitoring/click-data.png)

    De pagina **Gegevensbeveiliging** wordt geopend met aanbevelingen voor gegevensbronnen.

    [![Gegevensbronnen](./media/security-center-monitoring/sql-overview.png)](./media/security-center-monitoring/sql-overview.png#lightbox)

    Vanaf deze pagina kunt u het volgende doen:

    * Klik op het tabblad **Overzicht** met alle aanbevelingen voor gegevensbronnen die moeten worden gesaneerd. 
    * Klik op elk tabblad en bekijk de aanbevelingen op resourcetype.

    > [!NOTE]
    > Zie [Azure Storage-versleuteling voor gegevens in rust](../storage/common/storage-service-encryption.md)voor meer informatie over opslagversleuteling.


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Een aanbeveling voor een gegevensbron herstellen

1. Klik op een van de tabbladen resource op een resource. De informatiepagina wordt geopend met de aanbevelingen die moeten worden gesaneerd.

    ![Brongegevens](./media/security-center-monitoring/sql-recommendations.png)

2. Klik op een aanbeveling. De pagina Aanbeveling wordt geopend en geeft de **herstelstappen weer** om de aanbeveling uit te voeren.

   ![Herstelstappen](./media/security-center-monitoring/remediate1.png)

3. Klik **op Actie uitvoeren**. De pagina resourceinstellingen wordt weergegeven.

    ![Aanbeveling inschakelen](./media/security-center-monitoring/remediate2.png)

4. Volg de **stappen Voor herstel** en klik op **Opslaan**.


## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over aanbevelingen die van toepassing zijn op andere Azure-brontypen:

* [Volledige referentielijst met beveiligingsaanbevelingen van Azure Security Center](recommendations-reference.md)
* [Uw machines en toepassingen beveiligen in Azure Security Center](security-center-virtual-machine-protection.md)
* [Protecting your network in Azure Security Center](security-center-network-recommendations.md) (Uw netwerk beveiligen in Azure Security Center)