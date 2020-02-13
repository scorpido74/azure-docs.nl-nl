---
title: B2B-bedrijfsintegratie
description: Meer informatie over het bouwen van geautomatiseerde B2B-werk stromen voor bedrijfs integratie met behulp van Azure Logic Apps en Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: overview
ms.date: 08/01/2019
ms.openlocfilehash: 9356cecb22672ae15505d14b5ac73d5baeef09e0
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191376"
---
# <a name="b2b-enterprise-integration-solutions-with-azure-logic-apps-and-enterprise-integration-pack"></a>B2B-oplossingen voor bedrijfs integratie met Azure Logic Apps en Enterprise Integration Pack

Voor Business-to-Business (B2B)-oplossingen en naadloze communicatie tussen organisaties kunt u geautomatiseerde, schaal bare Enter prise Integration-werk stromen maken met behulp van de Enterprise Integration Pack (EIP) met [Azure Logic apps](../logic-apps/logic-apps-overview.md). Hoewel organisaties verschillende protocollen en indelingen gebruiken, kunnen ze berichten elektronisch uitwisselen. De EIP transformeert verschillende indelingen naar een indeling die de systemen van uw organisatie kunnen verwerken en ondersteunt industrie standaard protocollen, waaronder [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md)en [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md). U kunt ook de beveiliging van berichten verbeteren door versleuteling en digitale hand tekeningen te gebruiken. De EIP ondersteunt deze [Enter prise Integration-connectors](../connectors/apis-list.md#integration-account-connectors) en deze industrie standaarden:

* Electronic Data Interchange (EDI)
* Enterprise Application Integration (EAI)

Als u bekend bent met micro soft BizTalk Server of Azure BizTalk Services, volgt de EIP vergelijk bare concepten, waardoor de functies gemakkelijk te gebruiken zijn. Maar een belang rijk verschil is dat de EIP op architectuur is gebaseerd op integratie accounts om de opslag en het beheer van artefacten die worden gebruikt in B2B-communicatie te vereenvoudigen. Deze accounts zijn Cloud containers waarmee al uw artefacten worden opgeslagen, zoals partners, overeenkomsten, schema's, kaarten en certificaten. 

## <a name="why-use-the-enterprise-integration-pack"></a>Waarom het Enterprise Integration Pack gebruiken?

* Met de EIP kunt u al uw artefacten opslaan op één plek-uw integratie account.

* U kunt B2B-werk stromen bouwen en integreren met software-as-service (SaaS)-apps van derden, on-premises apps en aangepaste apps met behulp van Azure Logic Apps en connectors.

* U kunt aangepaste code voor uw Logic apps maken met Azure functions.

## <a name="how-do-i-get-started"></a>Hoe ga ik aan de slag?

Voordat u werk stromen voor B2B Logic apps kunt bouwen met de EIP, hebt u de volgende items nodig:

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een [integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) met de artefacten die u wilt gebruiken

* Als u kaarten en schema's wilt maken, kunt u de [Microsoft Azure Logic Apps bedrijfsintegratie-Hulpprogram ma's voor Visual studio 2015 2,0](https://aka.ms/vsmapsandschemas) en Visual Studio 2015 gebruiken.

Nadat u een integratie account hebt gemaakt en uw artefacten hebt toegevoegd, kunt u beginnen met het bouwen van B2B-werk stromen met deze artefacten door een logische app te maken in de Azure Portal. Als u geen ervaring hebt met Logic apps, kunt u [een eenvoudige logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md). Als u met deze artefacten wilt werken, moet u ervoor zorgen dat u uw integratie account eerst koppelt aan uw logische app. Daarna kan uw logische app toegang krijgen tot uw integratie account. U kunt ook logische apps maken, beheren en implementeren met behulp van Visual Studio of [Power shell](https://docs.microsoft.com/powershell/module/az.logicapp).

Dit zijn de stappen op hoog niveau om te beginnen met het bouwen van B2B Logic apps:

![Vereisten voor het maken van B2B Logic apps](./media/logic-apps-enterprise-integration-overview/overview.png)  

## <a name="try-now"></a>Nu uitproberen

[Een volledig operationele logische voorbeeld toepassing implementeren die AS2 berichten verzendt en ontvangt](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive)

## <a name="next-steps"></a>Volgende stappen

* [Handels partners maken](logic-apps-enterprise-integration-partners.md)
* [Overeenkomsten maken](../logic-apps/logic-apps-enterprise-integration-agreements.md)
* [Schema's toevoegen](logic-apps-enterprise-integration-schemas.md)
* [Kaarten toevoegen](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Migreren vanaf BizTalk Services](../logic-apps/logic-apps-move-from-mabs.md)
