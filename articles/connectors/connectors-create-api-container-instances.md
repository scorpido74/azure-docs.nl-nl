---
title: Azure Container Instances implementeren & beheren
description: Taken en werkstromen automatiseren die containerimplementaties in Azure Container Instances maken en beheren met Azure Logic Apps
services: logic-apps, container-instances
ms.service: logic-apps
ms.suite: integration
author: dlepow
ms.author: danlep
ms.manager: gwallace
ms.reviewer: estfan, macolso
ms.topic: article
tags: connectors
ms.date: 01/14/2020
ms.openlocfilehash: ecb1049d64197f2a60438df7eedfb244907f7327
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76046290"
---
# <a name="deploy-and-manage-azure-container-instances-by-using-azure-logic-apps"></a>Azure Container Instances implementeren en beheren met Azure Logic Apps

Met Azure Logic Apps en de Azure Container Instance-connector u geautomatiseerde taken en werkstromen instellen die [containergroepen](../container-instances/container-instances-container-groups.md)implementeren en beheren. De connector ContainerInstance ondersteunt de volgende acties:

* Een containergroep maken of verwijderen
* De eigenschappen van een containergroep oppakken
* Een lijst met containergroepen opmaken
* De logboeken van een containerinstantie oppakken

Gebruik deze acties in uw logische apps voor taken zoals het uitvoeren van een containerwerkbelasting als reactie op een Trigger van Logic Apps. U ook andere acties de uitvoer van acties voor containerinstantie laten gebruiken. 

Deze connector biedt alleen acties, dus om uw logische app te starten, gebruikt u een aparte trigger, zoals een **recidieftrigger** om een containerwerkbelasting volgens een regelmatig schema uit te voeren. U moet ook een implementatie van een containergroep activeren na een gebeurtenis, zoals de aankomst van een Outlook-e-mail. 

Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md) en het maken en beheren van [containerexemplaren](../container-instances/container-instances-quickstart.md)

* De logische app waar u toegang wilt krijgen tot uw containerexemplaren. Als u een actie wilt gebruiken, start u uw logische app met een andere trigger, bijvoorbeeld de **recidieftrigger.**

## <a name="add-a-container-instance-action"></a>Een actie Containerinstantie toevoegen

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open uw logische app in Logic App Designer, als deze nog niet is geopend.

1. Kies een pad: 

   * Kies Nieuwe **stap**onder de laatste stap waarin u een actie wilt toevoegen . 

     -of-

   * Tussen de stappen waar u een actie wilt toevoegen, beweegt u de aanwijzer over de pijl tussen de stappen. 
   Kies het plusteken (**+**) dat wordt weergegeven en selecteer Vervolgens Een actie **toevoegen**.

1. Voer in het zoekvak 'containerinstantie' in als filter. Selecteer onder de lijst acties de gewenste azure containerinstance-connectoractie.

1. Geef een naam op voor uw verbinding. 

1. Geef de benodigde details voor uw geselecteerde actie en ga verder met het bouwen van de workflow van uw logische app.

  Selecteer bijvoorbeeld **Containergroep maken** en voer de eigenschappen in voor een containergroep en een of meer containerexemplaren in de groep, zoals weergegeven in de volgende afbeelding (gedeeltelijke details):

  ![Containergroep maken](./media/connectors-create-api-container-instances/logic-apps-aci-connector.png)

## <a name="connector-reference"></a>Connector-verwijzing

Voor technische details over triggers, acties en limieten, die worden beschreven door de OpenAPI-beschrijving (voorheen Swagger) van de connector, raadpleegt u de [referentiepagina](/connectors/aci/) van de connector of de [YAML-verwijzing](../container-instances/container-instances-reference-yaml.md)voor de containergroep .

## <a name="next-steps"></a>Volgende stappen

* Bekijk een [voorbeeldlogica-app](https://github.com/Azure-Samples/aci-logicapps-integration) die een container uitvoert in Azure Container Instances om het sentiment van e-mail- of Twitter-tekst te analyseren

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)