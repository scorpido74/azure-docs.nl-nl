---
title: 'Snelstart: Een auditrapport dowloaden met behulp van de Azure Portal | Microsoft Docs'
description: Meer informatie over het downloaden van een auditrapport met behulp van de Azure Portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4de121ea-f4aa-4c8a-aae4-700c2c5e97a2
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6cbea49fe39c92c8a2fc50e501cb4ef5cff74b1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "68989677"
---
# <a name="quickstart-download-an-audit-report-using-the-azure-portal"></a>Snelstart: Een auditrapport downloaden met behulp van de Azure Portal

In deze quickstart leert u hoe u een CSV-bestand van de controlelogboeken voor uw tenant van de afgelopen 24 uur downloaden. U maximaal 250.000 records downloaden van de Azure-portal. De records zijn gesorteerd op de meest recente dus standaard, krijg je de meest recente 250.000 records. 

## <a name="prerequisites"></a>Vereisten

U hebt de volgende zaken nodig:

* Een Azure Active Directory-tenant. 
* Een gebruiker met de rol van **Beveiligingsadministrator**, **Beveiligingslezer** of **Globale administrator** voor de tenant. Bovendien kan elke gebruiker in de tenant toegang krijgen tot hun eigen auditlogboeken.

## <a name="quickstart-download-an-audit-report"></a>Snelstart: Een auditrapport downloaden

1. Navigeer naar de [Azure-portal](https://portal.azure.com).
2. Selecteer **Azure Active Directory** in het linker navigatiedeelvenster en gebruik de knop **Schakelen tussen mappen** om uw actieve map te selecteren.
3. Selecteer in het dashboard, **Azure Active Directory** en selecteer vervolgens **Auditlogboeken**. 
4. Kies **de afgelopen 24 uur** in de filter vervolgkeuzelijst **Datumbereik** en selecteer **Toepassen** om de auditlogboeken voor de afgelopen 24 uur weer te geven. 
5. Selecteer de knop **Downloaden,** selecteer **CSV** als bestandsindeling en geef een bestandsnaam op om een CSV-bestand te downloaden dat de gefilterde records bevat. 

![Rapportage](./media/quickstart-download-audit-report/download-audit-logs.png)

## <a name="next-steps"></a>Volgende stappen

* [Aanmeldactiviteitenrapporten in Azure Active Directory Portal](concept-sign-ins.md)
* [Azure Active Directory-rapportretentie](reference-reports-data-retention.md)
* [Azure Active Directory reporting latencies](reference-reports-latencies.md) (Rapportagevertraging in Azure Active Directory)
