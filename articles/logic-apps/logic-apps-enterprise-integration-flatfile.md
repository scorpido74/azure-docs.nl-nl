---
title: Platte bestanden coderen of decoderen
description: Versleutelen of decoderen van platte bestanden voor Enter prise Integration met Azure Logic Apps en Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 2d6182ba01507c2fb361628e01bb52e1ea821f44
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152649"
---
# <a name="encode-or-decode-flat-files-with-azure-logic-apps-and-enterprise-integration-pack"></a>Versleutelen of decoderen van platte bestanden met Azure Logic Apps en Enterprise Integration Pack

U kunt XML-inhoud coderen voordat u deze naar een zakelijke partner verzendt in een Business-to-business-scenario (B2B). In een logische app kunt u de connector voor platte bestands codering gebruiken om dit te doen. De logische app die u maakt, kan de XML-inhoud ophalen uit verschillende bronnen, waaronder van een HTTP-aanvraag trigger, vanuit een andere toepassing of zelfs van een van de vele [connectors](../connectors/apis-list.md). Zie de [documentatie van Logic apps](logic-apps-overview.md "Meer informatie over Logic apps")voor meer informatie over Logic apps.  

## <a name="create-the-flat-file-encoding-connector"></a>De plat file encoding-connector maken
Volg deze stappen om een plat file encoding-connector toe te voegen aan uw logische app.

1. Maak een logische app en [koppel deze aan uw integratie account](logic-apps-enterprise-integration-accounts.md "Meer informatie over het koppelen van een integratie account aan een logische app"). Dit account bevat het schema dat u gaat gebruiken voor het coderen van de XML-gegevens.  

1. Voeg in de ontwerp functie voor logische apps de trigger **Wanneer een HTTP-aanvraag is ontvangen** , toe aan uw logische app.

1. Voeg de actie voor het coderen van platte bestanden als volgt toe:

   a. Selecteer het **plus** teken.

   b. Selecteer de koppeling **actie toevoegen** (wordt weer gegeven nadat u het plus teken hebt geselecteerd).

   c. Voer in het zoekvak *plat* in om alle acties te filteren die u wilt gebruiken.

   d. Selecteer de optie voor **platte bestands codering** in de lijst.   

      ![Scherm afbeelding van de optie voor het coderen van een plat bestand](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   

1. Selecteer in het dialoog venster **platte bestands codering** het tekstvak **inhoud** .  

   ![Scherm afbeelding van het tekstvak voor inhoud](media/logic-apps-enterprise-integration-flatfile/flatfile-3.png)  

1. Selecteer de code Body als de inhoud die u wilt coderen. Met het label body wordt het inhouds veld ingevuld.     

   ![Scherm afbeelding van hoofd code](media/logic-apps-enterprise-integration-flatfile/flatfile-4.png)  

1. Selecteer de keuze lijst **schema naam** en kies het schema dat u wilt gebruiken voor het coderen van de invoer inhoud.    

   ![Scherm opname van de keuze lijst schema naam](media/logic-apps-enterprise-integration-flatfile/flatfile-5.png)  

1. Sla uw werk op.

   ![Scherm afbeelding van pictogram opslaan](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)  

U bent nu klaar met het instellen van uw plat file encoding-connector. In een echte wereld toepassing kunt u de versleutelde gegevens in een line-of-Business-toepassing opslaan, zoals Sales Force. Of u kunt die gecodeerde gegevens verzenden naar een handels partner. U kunt eenvoudig een actie toevoegen om de uitvoer van de coderings actie te verzenden naar Sales Force of naar uw handels partner, door gebruik te maken van een van de andere connectors.

U kunt de connector nu testen door een aanvraag naar het HTTP-eind punt te verzenden en de XML-inhoud in de hoofd tekst van de aanvraag op te nemen.  

## <a name="create-the-flat-file-decoding-connector"></a>De gedecodeerde connector voor platte bestanden maken

> [!NOTE]
> Als u deze stappen wilt uitvoeren, moet er al een schema bestand zijn geüpload naar het integratie account.

1. Voeg in de ontwerp functie voor logische apps de trigger **Wanneer een HTTP-aanvraag is ontvangen** , toe aan uw logische app.

1. Voeg als volgt de actie voor het decoderen van platte bestanden toe:

   a. Selecteer het **plus** teken.

   b. Selecteer de koppeling **actie toevoegen** (wordt weer gegeven nadat u het plus teken hebt geselecteerd).

   c. Voer in het zoekvak *plat* in om alle acties te filteren die u wilt gebruiken.

   d. Selecteer de optie voor het **decoderen van plat bestand** in de lijst.   

      ![Scherm afbeelding van de optie voor het decoderen van een plat bestand](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   

1. Selecteer het **inhouds** besturings element. Dit resulteert in een lijst met de inhoud van eerdere stappen die u kunt gebruiken als de inhoud die u wilt decoderen. U ziet dat de *hoofd tekst* van de binnenkomende HTTP-aanvraag kan worden gebruikt als de inhoud die moet worden ontsleuteld. U kunt ook de inhoud invoeren die rechtstreeks naar het **inhouds** besturings element moet worden gedecodeerd.     

1. Selecteer de tag *hoofd tekst* . U ziet dat het label body nu in het **inhouds** besturings element staat.

1. Selecteer de naam van het schema dat u wilt gebruiken om de inhoud te decoderen. In de volgende scherm afbeelding ziet u dat *OrderFile* de geselecteerde schema naam is. Deze schema naam is eerder geüpload naar het integratie account.

   ![Scherm opname van het dialoog venster voor het decoderen van een plat bestand](media/logic-apps-enterprise-integration-flatfile/flatfile-decode-1.png) 

1. Sla uw werk op.  

   ![Scherm afbeelding van pictogram opslaan](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)    

U bent nu klaar met het instellen van de coderings connector voor platte bestanden. In een echte wereld toepassing wilt u mogelijk de gedecodeerde gegevens opslaan in een line-of-Business-toepassing, zoals Sales Force. U kunt eenvoudig een actie toevoegen om de uitvoer van de decodeer actie naar Sales Force te verzenden.

U kunt de connector nu testen door een aanvraag naar het HTTP-eind punt te verzenden en de XML-inhoud die u wilt decoderen, op te nemen in de hoofd tekst van de aanvraag.  

## <a name="next-steps"></a>Volgende stappen
* Meer [informatie over de Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Meer informatie over Enterprise Integration Pack").  

