---
title: Gegevensbescherming in Azure Stream Analytics
description: In dit artikel wordt uitgelegd hoe u uw privégegevens versleutelt die worden gebruikt door een Azure Stream Analytics-taak.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 1b3bdad0125b5bddbba20c8d807924fc3ea87e32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299393"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Gegevensbescherming in Azure Stream Analytics 

Azure Stream Analytics is een volledig beheerde platform-as-a-service waarmee u realtime analysepijplijnen bouwen. Al het zware werk, zoals clusterinrichting, het schalen van knooppunten om uw gebruik aan te passen en het beheren van interne controlepunten, worden achter de schermen beheerd.

## <a name="encrypt-your-data"></a>Uw gegevens versleutelen

Stream Analytics maakt automatisch gebruik van de beste versleutelingsstandaarden in de hele infrastructuur om uw gegevens te versleutelen en te beveiligen. U streamanalytics eenvoudig vertrouwen om al uw gegevens veilig op te slaan, zodat u zich geen zorgen hoeft te maken over het beheer van de infrastructuur.

Als u door de klant beheerde sleutels (CMK) wilt gebruiken om uw gegevens te versleutelen, u uw eigen opslagaccount (algemene toepassing V1 of V2) gebruiken om alle persoonlijke gegevenselementen op te slaan die vereist zijn door de runtime van Stream Analytics. Uw opslagaccount kan naar behoefte worden versleuteld. Geen van uw persoonlijke gegevensassets wordt permanent opgeslagen door de Stream Analytics-infrastructuur. 

Deze instelling moet zijn geconfigureerd op het moment dat stream Analytics-banen worden gemaakt en kan niet worden gewijzigd gedurende de hele levenscyclus van de taak. Het wordt afgeraden om opslag die door uw Stream Analytics wordt gebruikt, te wijzigen of te verwijderen. Als u uw opslagaccount verwijdert, verwijdert u permanent alle persoonlijke gegevensassets, waardoor uw taak mislukt. 

Het bijwerken of roteren van sleutels naar uw opslagaccount is niet mogelijk via de Stream Analytics-portal. U de sleutels bijwerken met behulp van de REST API's.


## <a name="configure-storage-account-for-private-data"></a>Opslagaccount configureren voor privégegevens 

Gebruik de volgende stappen om uw opslagaccount te configureren voor persoonlijke gegevens. Deze configuratie is gemaakt vanuit je Stream Analytics-taak, niet vanuit je opslagaccount.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Selecteer in de linkerbovenhoek van Azure Portal **Een resource maken**. 

1. Selecteer de **taak**  **Analytics** >Stream Analytics in de resultatenlijst. 

1. Vul de vacaturepagina Stream Analytics in met de nodige details, zoals naam, regio en schaal. 

1. Schakel het selectievakje in met de tekst *Veilig alle persoonlijke gegevenselementen die deze taak nodig heeft in mijn Opslagaccount*.

1. Selecteer een opslagaccount in uw abonnement. Houd er rekening mee dat deze instelling niet kan worden gewijzigd gedurende de gehele levenscyclus van de taak. 

   ![Instellingen voor accountvoor privégegevensopslag](./media/data-protection/storage-account-create.png)

## <a name="private-data-assets-that-are-stored"></a>Privégegevens die zijn opgeslagen

Alle privégegevens die door Stream Analytics moeten worden bewaard, worden opgeslagen in uw opslagaccount. Voorbeelden van private data assets zijn: 

* Query's die u hebt geschreven en de bijbehorende configuraties  

* Door de gebruiker gedefinieerde functies 

* Controlepunten die nodig zijn voor de runtime van Stream Analytics

* Momentopnamen van referentiegegevens 

Verbindingsgegevens van uw resources, die worden gebruikt door uw Stream Analytics-taak, worden ook opgeslagen. Versleutel uw opslagaccount om al uw gegevens te beveiligen. 

Om u te helpen voldoen aan uw nalevingsverplichtingen in elke gereguleerde industrie of omgeving, u meer lezen over [het nalevingsaanbod van Microsoft.](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) 

## <a name="next-steps"></a>Volgende stappen

* [Een Azure Storage-account maken](../storage/common/storage-account-create.md)
* [Inzicht in invoer voor Azure Stream Analytics](stream-analytics-add-inputs.md)
* [Controlepunt- en replayconcepten in Azure Stream Analytics-taken](stream-analytics-concepts-checkpoint-replay.md)
* [Referentiegegevens gebruiken voor opzoekingen in Stream Analytics](stream-analytics-use-reference-data.md)
