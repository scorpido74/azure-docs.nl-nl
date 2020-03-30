---
title: AWS CloudTrail verbinden met Azure Sentinel | Microsoft Documenten
description: Meer informatie over het verbinden van AWS CloudTrail-gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 5cbef1f31ea7088d4fab4888f5630af1b765a910
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588651"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>Azure Sentinel verbinden met AWS CloudTrail

Gebruik de AWS-connector om al uw AWS CloudTrail-gebeurtenissen naar Azure Sentinel te streamen. In dit verbindingsproces wordt de toegang voor Azure Sentinel gemachtigd tot uw AWS-bronlogboeken, waardoor een vertrouwensrelatie ontstaat tussen AWS CloudTrail en Azure Sentinel. Dit wordt bereikt op AWS door een rol te maken die Azure Sentinel toestemming geeft om toegang te krijgen tot uw AWS-logboeken.

## <a name="prerequisites"></a>Vereisten

U moet schrijftoestemming hebben op de Azure Sentinel-werkruimte.

> [!NOTE]
> Azure Sentinel verzamelt CloudTrail-gebeurtenissen uit alle regio's. Het wordt aanbevolen dat u geen gebeurtenissen van de ene regio naar de andere streamt.

## <a name="connect-aws"></a>Verbinding maken met AWS 


1. Selecteer in Azure Sentinel **Gegevensconnectors** en selecteer vervolgens de **Amazon Web Services-regel** in de tabel en klik in het deelvenster AWS rechts op **Connectorpagina openen**.

1. Volg de instructies onder **Configuratie** met de volgende stappen.
 
1.  Selecteer **IAM**in uw Amazon Web Services-console onder **Beveiliging, Identiteit & Compliance**.

    ![AWS1 (AWS1)](./media/connect-aws/aws-1.png)

1.  Kies **Rollen** en selecteer **Rol maken**.

    ![AWS2 (AWS2)](./media/connect-aws/aws-2.png)

1.  Kies **een ander AWS-account.** Voer in het veld **Account-id** de **Microsoft-account-id** (**123412341234**) in die u vinden op de pagina met AWS-connectoren in de Azure Sentinel-portal.

    ![AWS3 (AWS3)](./media/connect-aws/aws-3.png)

1.  Controleer of **Externe id vereist** is geselecteerd en voer vervolgens de externe id (Workspace ID) in die kan worden gevonden op de pagina met AWS-connector in de Azure Sentinel-portal.

    ![AWS4 (AWS4)](./media/connect-aws/aws-4.png)

1.  Selecteer **onder Machtigingen bijvoegen het** beleid voor **AWSCloudTrailReadOnlyAccess**.

    ![AWS5 (AWS5)](./media/connect-aws/aws-5.png)

1.  Voer een tag in (optioneel).

    ![AWS6](./media/connect-aws/aws-6.png)

1.  Voer vervolgens een **rolnaam** in en selecteer de knop **Rol maken.**

    ![AWS7](./media/connect-aws/aws-7.png)

1.  Kies in de lijst Rollen de rol die u hebt gemaakt.

    ![AWS8 (AWS8)](./media/connect-aws/aws-8.png)

1.  Kopieer de **rol ARN**. Plak deze in de Azure Sentinel-portal in het scherm Amazon Web Services-connector in het **veld Rol om toe te voegen** en klik op **Toevoegen**.

    ![AWS9 (AWS9)](./media/connect-aws/aws-9.png)

1. Als u het relevante schema wilt gebruiken in Log Analytics voor AWS-gebeurtenissen, zoekt u naar **AWSCloudTrail**.



## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u AWS CloudTrail verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te controleren.

