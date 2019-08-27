---
title: Verbinding maken met SMTP vanuit Azure Logic Apps | Microsoft Docs
description: Taken en werk stromen automatiseren waarmee e-mail via uw SMTP-account (Simple Mail Transfer Protocol) wordt verzonden met behulp van Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 1649f197d4dbd88e2b485ab32f254a2d09696a84
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050741"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>E-mail verzenden vanuit uw SMTP-account met Azure Logic Apps

Met Azure Logic Apps en de Simple Mail Transfer Protocol (SMTP)-connector kunt u geautomatiseerde taken en werk stromen maken waarmee e-mail berichten worden verzonden vanuit uw SMTP-account. U kunt ook andere acties uitvoeren met de uitvoer van SMTP-acties. Nadat uw SMTP bijvoorbeeld een e-mail heeft verzonden, kunt u uw team in de toegestane vertraging melden met de uitzonderings connector. Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Uw SMTP-account en gebruikers referenties

  Met uw referenties wordt uw logische app geautoriseerd om een verbinding te maken en toegang te krijgen tot uw SMTP-account.

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waartoe u toegang wilt krijgen tot uw SMTP-account. Als u een SMTP-actie wilt gebruiken, start u uw logische app met een trigger, zoals een Sales Force-trigger, als u een Sales Force-account hebt.

  U kunt bijvoorbeeld uw logische app starten met de trigger **Wanneer een record wordt gemaakt** Sales Force. 
  Deze trigger wordt geactiveerd elke keer dat een nieuwe record, zoals een lead, wordt gemaakt in Sales Force. 
  U kunt deze trigger vervolgens volgen met de actie SMTP- **e-mail verzenden** . Op die manier verzendt de logische app een e-mail van uw SMTP-account over de nieuwe record wanneer de nieuwe record wordt gemaakt.

## <a name="connect-to-smtp"></a>Verbinding maken met SMTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open de logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Kies **nieuwe stap**onder de laatste stap waarin u een SMTP-actie wilt toevoegen. 

   Als u een actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen de stappen. 
   Kies het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Voer in het zoekvak ' SMTP ' in als uw filter. Selecteer in de lijst acties de gewenste actie.

1. Wanneer u hierom wordt gevraagd, geeft u de volgende verbindings gegevens op:

   | Eigenschap | Vereist | Description |
   |----------|----------|-------------|
   | **Verbindingsnaam** | Ja | Een naam voor de verbinding met de SMTP-server | 
   | **SMTP-server adres** | Ja | Het adres voor de SMTP-server | 
   | **Gebruikers naam** | Ja | Uw gebruikers naam voor uw SMTP-account | 
   | **Wachtwoord** | Ja | Uw wacht woord voor uw SMTP-account | 
   | **SMTP-server poort** | Nee | Een specifieke poort op de SMTP-server die u wilt gebruiken | 
   | **SSL inschakelen?** | Nee | SSL-versleuteling inschakelen of uitschakelen. | 
   |||| 

1. Geef de benodigde gegevens op voor de geselecteerde actie. 

1. Sla de logische app op of ga door met het bouwen van de werk stroom van uw logische app.

## <a name="connector-reference"></a>Connector-verwijzing

Raadpleeg de [referentie pagina](/connectors/smtpconnector/)van de connector voor technische informatie over triggers, acties en limieten die worden beschreven in de beschrijving van de OpenAPI (voorheen Swagger) van de connector.

## <a name="get-support"></a>Ondersteuning krijgen

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps](../connectors/apis-list.md) -connectors