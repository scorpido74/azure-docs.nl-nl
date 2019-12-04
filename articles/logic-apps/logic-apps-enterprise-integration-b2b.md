---
title: B2B-ondernemings integraties maken
description: B2B-gegevens in Azure Logic Apps ontvangen met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 39966b8171296a8608b9436485f7682d114c8410
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793092"
---
# <a name="receive-b2b-data-with-azure-logic-apps-and-enterprise-integration-pack"></a>B2B-gegevens ontvangen met Azure Logic Apps en Enterprise Integration Pack

Nadat u een integratie account met partners en overeenkomsten hebt gemaakt, bent u klaar om een B2B-werk stroom (Business to Business) te maken voor uw logische app met de [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Vereisten

Als u de AS2-en X12-acties wilt gebruiken, moet u een Bedrijfsintegratie-account hebben. Meer informatie [over het maken van een bedrijfsintegratie-account](../logic-apps/logic-apps-enterprise-integration-accounts.md).

## <a name="create-a-logic-app-with-b2b-connectors"></a>Een logische app maken met B2B-connectors

Volg deze stappen voor het maken van een B2B Logic-app die gebruikmaakt van de AS2-en X12-acties om gegevens van een handels partner te ontvangen:

1. Maak een logische app en [koppel vervolgens uw app aan uw integratie account](../logic-apps/logic-apps-enterprise-integration-accounts.md).

2. Een **aanvraag toevoegen: wanneer een HTTP-aanvraag wordt ontvangen, wordt** de trigger geactiveerd voor uw logische app.

    ![](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)

3. Selecteer **een actie toevoegen**om de actie **decoderen AS2** toe te voegen.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-2.png)

4. Als u alle acties wilt filteren op de gewenste actie, voert u het woord **AS2** in het zoekvak in.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-5.png)

5. Selecteer de actie **AS2-AS2-bericht decoderen** .

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-6.png)

6. Voeg de **hoofd tekst** toe die u wilt gebruiken als invoer. 
   Selecteer in dit voor beeld de hoofd tekst van de HTTP-aanvraag die de logische app activeert. Of voer een expressie in die de kopteksten in het veld **headers** typt:

    @triggerOutputs() [' headers ']

7. Voeg de vereiste **headers** voor AS2 toe, die u in de HTTP-aanvraag headers kunt vinden. 
   In dit voor beeld selecteert u de headers van de HTTP-aanvraag die de logische app activeren.

8. Voeg nu de actie X12-bericht decoderen toe. Selecteer **een actie toevoegen**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-9.png)

9. Als u alle acties wilt filteren op de gewenste actie, voert u het woord **X12** in het zoekvak in.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-10.png)

10. Selecteer de actie **X12-X12-bericht decoderen** .

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-as2message.png)

11. Nu moet u de invoer voor deze actie opgeven. 
    Deze invoer is de uitvoer van de vorige AS2-actie.

    De werkelijke bericht inhoud bevindt zich in een JSON-object en is base64-gecodeerd, dus u moet een expressie opgeven als invoer. 
    Voer de volgende expressie in het **bericht X12 plat file om het invoer veld te DEcoderen** :
    
    @base64ToString(Body (' Decode_AS2_message ')? [' AS2Message']? [' Content '])

    Voeg nu stappen toe voor het decoderen van de X12-gegevens die zijn ontvangen van de handels partner en uitvoer items in een JSON-object. 
    Als u de partner wilt informeren dat de gegevens zijn ontvangen, kunt u een antwoord terugsturen met de AS2 (MDN) in een HTTP-antwoord actie.

12. Kies **een actie toevoegen**om de **reactie** actie toe te voegen.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-14.png)

13. Als u alle gewenste acties wilt filteren, voert u het **antwoord** van het woord in het zoekvak in.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-15.png)

14. Selecteer de **reactie** actie.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-16.png)

15. Om toegang te krijgen tot de MDN van de uitvoer van de **X12 bericht actie decoderen** , stelt u het veld antwoord **tekst** in op deze expressie:

    @base64ToString(Body (' Decode_AS2_message ')? [' OutgoingMdn']? [' Content '])

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-17.png)  

16. Sla uw werk op.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-5.png)  

U bent nu klaar met het instellen van uw B2B Logic-app. In een echte wereld toepassing wilt u mogelijk de gedecodeerde X12-gegevens opslaan in een LOB-app (line-of-Business) of een gegevens archief. Als u uw eigen LOB-Apps wilt verbinden en deze Api's wilt gebruiken in uw logische app, kunt u verdere acties toevoegen of aangepaste Api's schrijven.

## <a name="features-and-use-cases"></a>Functies en use cases

* Met de acties voor decoderen en coderen van AS2 en X12 kunt u gegevens uitwisselen tussen handels partners door gebruik te maken van industrie standaard protocollen in Logic apps.
* Voor het uitwisselen van gegevens met handels partners kunt u AS2 en X12 gebruiken met of zonder elkaar.
* De B2B-acties helpen u bij het eenvoudig maken van partners en overeenkomsten in uw integratie account en deze te gebruiken in een logische app.
* Wanneer u uw logische app met andere acties uitbreidt, kunt u gegevens verzenden en ontvangen tussen andere apps en services, zoals Sales Force.

## <a name="learn-more"></a>Meer informatie
[Meer informatie over de Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
