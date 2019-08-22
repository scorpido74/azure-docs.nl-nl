---
title: Probleemoplossing - QnAMaker
titleSuffix: Azure Cognitive Services
description: QnAMaker bestaat uit onderdelen die worden gehost in Azure-account van de gebruiker. Foutopsporing kan vereisen dat gebruikers wijzigingen aanbrengen in de QnAMaker-Azure-resources of QnAMaker-ondersteuningsteam bieden met aanvullende informatie over de installatie.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/20/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b58a08c4cfa97356be4064456b2e9ec5cceccfbc
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876340"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>Tips voor probleemoplossing voor de ondersteuning van de QnA Maker-service en runtime

QnAMaker omvat resources die worden gehost in het Azure-abonnement van de gebruiker. Voor fout opsporing kunnen gebruikers hun Azure QnAMaker-resources bewerken of het ondersteunings team van QnAMaker voorzien van aanvullende informatie over de installatie.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Over het verkrijgen van de meest recente updates voor QnAMaker-runtime

De QnAMaker-runtime maakt deel uit van de Azure App Service geïmplementeerd bij het [maken van een QnAMaker-service](./set-up-qnamaker-service-azure.md) in azure Portal. Updates worden periodiek gesteld in de runtime. QnA Maker App Service bevindt zich in de modus automatisch bijwerken na de release van de site-uitbrei ding van apr 2019 (versie 5 +). Dit is al zo ontworpen dat er tijdens upgrades geen uitval tijd in rekening wordt gehouden. 

U kunt uw huidige versie controleren op https://www.qnamaker.ai/UserSettings. Als uw versie ouder is dan versie 5. x, moet u de App Service opnieuw opstarten om de meest recente updates toe te passen.

1. Ga naar uw QnAMaker-service (resourcegroep) in de [Azure-portal](https://portal.azure.com)

    ![QnAMaker-Azure-resourcegroep](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Klik op de Service-App en opent u de sectie overzicht

     ![QnAMaker App Service](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Start de App-service. Het zou binnen een paar seconden voltooid. Houd er rekening mee dat alle afhankelijke toepassingen of botsingen die gebruikmaken van deze QnAMaker-service, niet beschikbaar zijn voor eind gebruikers tijdens deze herstart periode.

    ![QnAMaker appservice-opnieuw opstarten](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>Over het verkrijgen van de hostnaam van de service QnAMaker
Hostnaam van de QnAMaker-service is handig voor de foutopsporing als u contact op met ondersteuning voor QnAMaker of UserVoice. De hostnaam is een URL in dit formulier: https:// *{hostname}* . azurewebsites.net.
    
1. Ga naar uw QnAMaker-service (resourcegroep) in de [Azure-portal](https://portal.azure.com)

    ![QnAMaker-Azure-resourcegroep in Azure portal](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selecteer de App Service die aan de QnA Maker resource is gekoppeld. Normaal gesp roken zijn de namen hetzelfde.

     ![Selecteer QnAMaker App Service](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. De URL van de hostnaam is beschikbaar in de sectie overzicht

    ![QnAMaker-hostnaam](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Knowledge Base-vragen verbeteren met actief leren](./improve-knowledge-base.md)
