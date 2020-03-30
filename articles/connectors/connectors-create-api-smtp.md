---
title: Verbinding maken met SMTP vanuit Azure Logic Apps
description: Taken en werkstromen automatiseren die e-mail verzenden via uw SMTP-account (Simple Mail Transfer Protocol) met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 60acd128495176cd0a90418c61edf53bdcd88e5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77647582"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>E-mail verzenden vanaf uw SMTP-account met Azure Logic Apps

Met Azure Logic Apps en de SMTP-connector (Simple Mail Transfer Protocol) u geautomatiseerde taken en werkstromen maken die e-mail verzenden vanuit uw SMTP-account. U ook andere acties de uitvoer van SMTP-acties laten gebruiken. Nadat uw SMTP bijvoorbeeld een e-mail heeft gestuurd, u uw team in Slack op de hoogte stellen met de Slack-connector. Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Uw SMTP-account en gebruikersreferenties

  Uw referenties geven toestemming voor uw logische app om een verbinding te maken en toegang te krijgen tot uw SMTP-account.

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u toegang wilt hebben tot uw SMTP-account. Als u een SMTP-actie wilt gebruiken, start u uw logica-app met een trigger, zoals een Salesforce-trigger, als u een Salesforce-account hebt.

  U uw logische app bijvoorbeeld starten met de **Salesforce-trigger wanneer een record is gemaakt.** 
  Deze trigger vuurt elke keer dat een nieuwe record, zoals een lead, wordt gemaakt in Salesforce. 
  U deze trigger vervolgens volgen met de actie SMTP **Send Email.** Op die manier stuurt uw logische app een e-mail van uw SMTP-account over de nieuwe record wanneer de nieuwe record wordt gemaakt.

## <a name="connect-to-smtp"></a>Verbinding maken met SMTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open uw logische app in Logic App Designer, als deze nog niet is geopend.

1. Kies Onder de laatste stap waarin u een SMTP-actie wilt toevoegen, de optie **Nieuwe stap**. 

   Als u een actie tussen de stappen wilt toevoegen, verplaatst u de aanwijzer over de pijl tussen de stappen. 
   Kies het plusteken (**+**) dat wordt weergegeven en selecteer Vervolgens Een actie **toevoegen**.

1. Voer in het zoekvak 'smtp' in als filter. Selecteer onder de lijst met acties de gewenste actie.

1. Geef desgevraagd de som van deze verbindingsgegevens op:

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Verbindingsnaam** | Ja | Een naam voor de verbinding met uw SMTP-server | 
   | **SMTP-serveradres** | Ja | Het adres voor uw SMTP-server | 
   | **Gebruikersnaam** | Ja | Uw gebruikersnaam voor uw SMTP-account | 
   | **Wachtwoord** | Ja | Uw wachtwoord voor uw SMTP-account | 
   | **SMTP-serverpoort** | Nee | Een specifieke poort op uw SMTP-server die u wilt gebruiken | 
   | **SSL inschakelen?** | Nee | SSL-versleuteling in- of uitschakelen. | 
   |||| 

1. Geef de benodigde details op voor uw geselecteerde actie. 

1. Sla uw logische app op of bouw de werkstroom van uw logische app verder.

## <a name="connector-reference"></a>Connector-verwijzing

Zie de [referentiepagina van](https://docs.microsoft.com/connectors/smtpconnector/)de connector voor meer technische details over deze connector, zoals triggers, acties en limieten zoals beschreven in het Swagger-bestand van de connector.

> [!NOTE]
> Voor logische apps in een [integratieserviceomgeving (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)gebruikt de ISE-versie met HET LABEL ISE in plaats daarvan de [ISE-berichtlimieten.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)