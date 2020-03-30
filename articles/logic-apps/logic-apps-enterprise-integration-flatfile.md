---
title: Platte bestanden coderen of decoderen
description: Platte bestanden coderen of decoderen voor bedrijfsintegratie met Azure Logic Apps en Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 2d6182ba01507c2fb361628e01bb52e1ea821f44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152649"
---
# <a name="encode-or-decode-flat-files-with-azure-logic-apps-and-enterprise-integration-pack"></a>Platte bestanden coderen of decoderen met Azure Logic Apps en Enterprise Integration Pack

Mogelijk wilt u XML-inhoud coderen voordat u deze naar een zakelijke partner verzendt in een B2B-scenario (business-to-business). In een logische app u hiervoor de platte bestandscoderingsconnector gebruiken. De logische app die u maakt, kan de XML-inhoud uit verschillende bronnen halen, waaronder van een HTTP-aanvraagtrigger, van een andere toepassing of zelfs van een van de vele [connectors.](../connectors/apis-list.md) Zie de [documentatie van logische apps](logic-apps-overview.md "Meer informatie over Logic-apps")voor meer informatie over logische apps.  

## <a name="create-the-flat-file-encoding-connector"></a>De platte verbindingsconnector voor bestandscodering maken
Voer deze stappen uit om een platte bestandscoderingsconnector toe te voegen aan uw logische app.

1. Maak een logische app en [koppel deze aan uw integratieaccount.](logic-apps-enterprise-integration-accounts.md "Meer informatie over het koppelen van een integratieaccount aan een Logic-app") Dit account bevat het schema dat u gebruikt om de XML-gegevens te coderen.  

1. Voeg in de Logic App Designer de **trigger Wanneer een HTTP-aanvraag wordt ontvangen** toe aan uw logische app.

1. Voeg als volgt de actie voor het coderen van platte bestanden toe:

   a. Selecteer het **plusteken.**

   b. Selecteer de koppeling **Een actie toevoegen** (wordt weergegeven nadat u het plusteken hebt geselecteerd).

   c. Voer in het zoekvak *Plat* in om alle acties te filteren op de acties die u wilt gebruiken.

   d. Selecteer de optie **Platte bestandscodering** in de lijst.   

      ![Schermafbeelding van de optie Platte bestandscodering](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   

1. Selecteer in het dialoogvenster **Platte bestandscodering** het tekstvak **Inhoud.**  

   ![Schermafbeelding van het tekstvak Inhoud](media/logic-apps-enterprise-integration-flatfile/flatfile-3.png)  

1. Selecteer de hoofdtag als de inhoud die u wilt coderen. De hoofdtag vult het inhoudsveld.     

   ![Schermafbeelding van hoofdtekst](media/logic-apps-enterprise-integration-flatfile/flatfile-4.png)  

1. Selecteer het keuzelijstje **Schemanaam** en kies het schema dat u wilt gebruiken om de invoerinhoud te coderen.    

   ![Schermafbeelding van het keuzelijstvak Schemanaam](media/logic-apps-enterprise-integration-flatfile/flatfile-5.png)  

1. Sla uw werk op.

   ![Schermafbeelding van pictogram Opslaan](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)  

Op dit moment bent u klaar met het instellen van uw platte bestandscoderingsconnector. In een echte toepassing u de gecodeerde gegevens opslaan in een line-of-business-toepassing, zoals Salesforce. Of u die gecodeerde gegevens naar een handelspartner sturen. U eenvoudig een actie toevoegen om de uitvoer van de coderingsactie naar Salesforce of naar uw handelspartner te verzenden door een van de andere meegeleverde connectors te gebruiken.

U uw connector nu testen door een aanvraag in te dienen bij het HTTP-eindpunt en de XML-inhoud op te nemen in de hoofdtekst van de aanvraag.  

## <a name="create-the-flat-file-decoding-connector"></a>De stekkerconnector voor het decoderen van platte bestanden maken

> [!NOTE]
> Als u deze stappen wilt uitvoeren, moet u een schemabestand hebben dat al is geüpload naar uw integratieaccount.

1. Voeg in de Logic App Designer de **trigger Wanneer een HTTP-aanvraag wordt ontvangen** toe aan uw logische app.

1. Voeg als volgt de actie voor het decoderen van platte bestanden toe:

   a. Selecteer het **plusteken.**

   b. Selecteer de koppeling **Een actie toevoegen** (wordt weergegeven nadat u het plusteken hebt geselecteerd).

   c. Voer in het zoekvak *Plat* in om alle acties te filteren op de acties die u wilt gebruiken.

   d. Selecteer de optie **Flat File Decoding** in de lijst.   

      ![Schermafbeelding van de optie Flat File Decoding](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   

1. Selecteer het **besturingselement Inhoud.** Dit levert een lijst op met de inhoud uit eerdere stappen die u gebruiken als de inhoud om te decoderen. Merk op dat de *instantie* van de binnenkomende HTTP-aanvraag beschikbaar is om te worden gebruikt als de inhoud om te decoderen. U de inhoud ook invoeren om rechtstreeks te decoderen in het **inhoudsbesturingselement.**     

1. Selecteer de *hoofdteksttag.* Let op: de hoofdtag bevindt zich nu in het **besturingselement Inhoud.**

1. Selecteer de naam van het schema dat u wilt gebruiken om de inhoud te decoderen. In de volgende schermafbeelding ziet u dat *OrderFile* de geselecteerde schemanaam is. Deze schemanaam was eerder geüpload naar het integratieaccount.

   ![Schermafbeelding van het dialoogvenster Decodering van platte bestanden](media/logic-apps-enterprise-integration-flatfile/flatfile-decode-1.png) 

1. Sla uw werk op.  

   ![Schermafbeelding van pictogram Opslaan](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)    

Op dit moment bent u klaar met het instellen van uw flat file decoding connector. In een echte toepassing u de gedecodeerde gegevens opslaan in een line-of-business-toepassing zoals Salesforce. U eenvoudig een actie toevoegen om de uitvoer van de decoderingsactie naar Salesforce te verzenden.

U uw connector nu testen door een aanvraag in te dienen bij het HTTP-eindpunt en de XML-inhoud op te nemen die u wilt decoderen in de hoofdtekst van de aanvraag.  

## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over het Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Meer informatie over enterprise integration pack").  

