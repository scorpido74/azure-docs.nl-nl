---
title: B2B-bedrijfsintegratie
description: Meer informatie over het bouwen van geautomatiseerde B2B-workflows voor bedrijfsintegratie met Azure Logic Apps en Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: overview
ms.date: 08/01/2019
ms.openlocfilehash: 9356cecb22672ae15505d14b5ac73d5baeef09e0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77191376"
---
# <a name="b2b-enterprise-integration-solutions-with-azure-logic-apps-and-enterprise-integration-pack"></a>B2B-oplossingen voor bedrijfsintegratie met Azure Logic Apps en Enterprise Integration Pack

Voor B2B-oplossingen (business-to-business) en naadloze communicatie tussen organisaties u geautomatiseerde schaalbare enterprise-integratieworkflows bouwen met behulp van het Enterprise Integration Pack (EIP) met [Azure Logic Apps.](../logic-apps/logic-apps-overview.md) Hoewel organisaties verschillende protocollen en formaten gebruiken, kunnen ze berichten elektronisch uitwisselen. De EIP transformeert verschillende formaten in een indeling die de systemen van uw organisatie kunnen verwerken en ondersteunt industriestandaard protocollen, waaronder [AS2,](../logic-apps/logic-apps-enterprise-integration-as2.md) [X12](logic-apps-enterprise-integration-x12.md)en [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md). U ook de beveiliging van berichten verbeteren door zowel versleuteling als digitale handtekeningen te gebruiken. Het EIP ondersteunt deze [enterprise integration connectors](../connectors/apis-list.md#integration-account-connectors) en deze industriestandaarden:

* Elektronische gegevensuitwisseling (EDI)
* Integratie van bedrijfstoepassingen (EAI)

Als u bekend bent met Microsoft BizTalk Server of Azure BizTalk Services, volgt het EIP vergelijkbare concepten, waardoor de functies eenvoudig te gebruiken zijn. Een groot verschil is echter dat het EIP architectonisch is gebaseerd op "integratieaccounts" om de opslag en het beheer van artefacten die worden gebruikt in B2B-communicatie te vereenvoudigen. Deze accounts zijn cloudgebaseerde containers die al uw artefacten opslaan, zoals partners, overeenkomsten, schema's, kaarten en certificaten. 

## <a name="why-use-the-enterprise-integration-pack"></a>Waarom het Enterprise Integration Pack gebruiken?

* Met de EIP u al uw artefacten op één plek opslaan: uw integratieaccount.

* U B2B-workflows bouwen en integreren met SaaS-apps (Software-as-Service) van derden met behulp van Azure Logic Apps en connectors.

* U aangepaste code maken voor uw logische apps met Azure-functies.

## <a name="how-do-i-get-started"></a>Hoe ga ik aan de slag?

Voordat u beginnen met het bouwen van B2B-logische app-werkstromen met het EIP, hebt u deze items nodig:

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een [integratieaccount](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) met de artefacten die u wilt gebruiken

* Als u kaarten en schema's wilt maken, u de [Microsoft Azure Logic Apps Enterprise Integration Tools voor Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas) en Visual Studio 2015 gebruiken.

Nadat u een integratieaccount hebt gemaakt en uw artefacten hebt toegevoegd, u beginnen met het bouwen van B2B-werkstromen met deze artefacten door een logische app te maken in de Azure-portal. Als u nieuw bent in logische apps, probeert u [een basislogische app te maken.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Als u met deze artefacten wilt werken, moet u ervoor zorgen dat u eerst uw integratieaccount koppelt aan uw logica-app. Daarna heeft uw logische app toegang tot uw integratieaccount. U ook logische apps maken, beheren en implementeren met Visual Studio of [PowerShell.](https://docs.microsoft.com/powershell/module/az.logicapp)

Dit zijn de stappen op hoog niveau om aan de slag te gaan met het bouwen van B2B-logische apps:

![Voorwaarden voor het maken van B2B-logische apps](./media/logic-apps-enterprise-integration-overview/overview.png)  

## <a name="try-now"></a>Nu uitproberen

[Een volledig operationele voorbeeldlogica-app implementeren die AS2-berichten verzendt en ontvangt](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive)

## <a name="next-steps"></a>Volgende stappen

* [Handelspartners maken](logic-apps-enterprise-integration-partners.md)
* [Afspraken maken](../logic-apps/logic-apps-enterprise-integration-agreements.md)
* [Schema's toevoegen](logic-apps-enterprise-integration-schemas.md)
* [Kaarten toevoegen](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Migreren vanuit BizTalk Services](../logic-apps/logic-apps-move-from-mabs.md)
