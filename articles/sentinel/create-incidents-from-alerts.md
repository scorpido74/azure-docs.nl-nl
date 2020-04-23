---
title: Incidenten maken op gebruik van waarschuwingen in Azure Sentinel | Microsoft Documenten
description: Meer informatie over het maken van incidenten op gebruik maken van waarschuwingen in Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 1593b96ae8412632120e8977635a4193996ca88d
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025115"
---
# <a name="automatically-create-incidents-from-microsoft-security-alerts"></a>Automatisch incidenten maken vanuit microsoft-beveiligingswaarschuwingen

Waarschuwingen die worden geactiveerd in microsoft-beveiligingsoplossingen die zijn verbonden met Azure Sentinel, zoals Microsoft Cloud App Security en Azure Advanced Threat Protection, leiden niet automatisch tot incidenten in Azure Sentinel. Wanneer u een Microsoft-oplossing koppelt aan Azure Sentinel, wordt elke waarschuwing die in die service wordt gegenereerd standaard opgeslagen als ruwe gegevens in Azure Sentinel, in de tabel Beveiligingswaarschuwing in uw Azure Sentinel-werkruimte. U die gegevens vervolgens gebruiken zoals alle andere ruwe gegevens die u met Sentinel verbindt.

U Azure Sentinel eenvoudig configureren om automatisch incidenten te maken telkens wanneer een waarschuwing wordt geactiveerd in een verbonden Microsoft-beveiligingsoplossing door de instructies in dit artikel te volgen.

## <a name="prerequisites"></a>Vereisten
U moet [microsoft-beveiligingsoplossingen verbinden](connect-data-sources.md#data-connection-methods) om het maken van incidenten mogelijk te maken via waarschuwingen van beveiligingsservices.

## <a name="using-microsoft-security-incident-creation-analytic-rules"></a>Analytische regels voor het maken van incidenten met Microsoft Security gebruiken

Gebruik de ingebouwde regels die beschikbaar zijn in Azure Sentinel om te kiezen welke verbonden Microsoft-beveiligingsoplossingen azure sentinel-incidenten automatisch in realtime moeten maken. U de regels ook bewerken om meer specifieke opties te definiëren voor het filteren van welke waarschuwingen die door de Microsoft-beveiligingsoplossing worden gegenereerd, incidenten in Azure Sentinel moeten veroorzaken. U er bijvoorbeeld voor kiezen om Azure Sentinel-incidenten automatisch alleen te maken op basis van azure security center-waarschuwingen met hoge ernst.

1. Selecteer **Analytics**in de Azure-portal onder Azure Sentinel .

1. Selecteer het tabblad **Regelsjablonen** om alle ingebouwde analytische regels weer te geven.

    ![Regelsjablonen](media/incidents-from-alerts/rule-templates.png)

1. Kies de **microsoft-regelsjabloon voor beveiligingsanalyse** die u wilt gebruiken en klik op **Regel maken**.

    ![Beveiligingsanalyseregel](media/incidents-from-alerts/security-analytics-rule.png)

1. U de regeldetails wijzigen en ervoor kiezen om de waarschuwingen te filteren die incidenten veroorzaken op basis van de ernst van de waarschuwing of op tekst in de naam van de waarschuwing.  
      
    Als u bijvoorbeeld **Azure Security Center** kiest in het veld **Beveiligingsservice van Microsoft** en **Hoog** kiest in het veld Filter **op ernst,** worden alleen waarschuwingen voor Azure Security Center met hoge ernst automatisch gemaakt in Azure Sentinel.  

    ![Regelwizard maken](media/incidents-from-alerts/create-rule-wizard.png)

1. U ook een nieuwe **Microsoft-beveiligingsregel** maken die waarschuwingen van verschillende Microsoft-beveiligingsservices filtert door te klikken op **+Microsoft-regel voor** **het maken van incidenten**maken en selecteren.

    ![Regel voor het maken van incidenten](media/incidents-from-alerts/incident-creation-rule.png)

  U meer dan één analytische regel voor Microsoft Security maken per **microsoft-beveiligingsservicetype.** **Microsoft Security** Hierdoor worden geen dubbele incidenten gemaakt, omdat elke regel als filter wordt gebruikt. Zelfs als een waarschuwing overeenkomt met meer dan één analytische regel **van Microsoft Security,** wordt slechts één Azure Sentinel-incident veroorzaakt.

## <a name="enable-incident-generation-automatically-during-connection"></a>Incidentgeneratie automatisch inschakelen tijdens verbinding
 Wanneer u een Microsoft-beveiligingsoplossing aanverbindt, u selecteren of u wilt dat de waarschuwingen van de beveiligingsoplossing automatisch incidenten genereren in Azure Sentinel.

1. Verbind een gegevensbron voor microsoft-beveiligingsoplossingen. 

   ![Beveiligingsincidenten genereren](media/incidents-from-alerts/generate-security-incidents.png)

1. Selecteer **onder Incidenten maken** **inschakelen** om de standaardanalytische regel in te schakelen die incidenten automatisch maakt op uit waarschuwingen die zijn gegenereerd in de verbonden beveiligingsservice. U deze regel vervolgens bewerken onder **Analytics** en vervolgens **Actieve regels**.

## <a name="next-steps"></a>Volgende stappen

- Om aan de slag te gaan met Azure Sentinel, hebt u een abonnement op Microsoft Azure nodig. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/free/).
- Meer informatie over het [gebruik van uw gegevens naar Azure Sentinel](quickstart-onboard.md)en inzicht krijgen in uw gegevens en potentiële [bedreigingen.](quickstart-get-visibility.md)
