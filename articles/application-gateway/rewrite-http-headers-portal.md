---
title: HTTP-aanvraag- en antwoordkoppen opnieuw schrijven in portal - Azure Application Gateway
description: Meer informatie over het gebruik van de Azure-portal om een Azure Application Gateway te configureren om de HTTP-headers te herschrijven in de aanvragen en antwoorden die via de gateway worden doorgegeven
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: b90736b3ed1c1f69488fde4a386cf215d751c362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74012864"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>HTTP-aanvraag- en antwoordkoppen opnieuw schrijven met Azure Application Gateway - Azure-portal

In dit artikel wordt beschreven hoe u de Azure-portal gebruiken om een [V2 SKU-instantie van de Toepassingsgateway](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) te configureren om de HTTP-headers in aanvragen en antwoorden te herschrijven.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

U moet een Instantie Van de Toepassinggateway v2 SKU hebben om de stappen in dit artikel te voltooien. Het herschrijven van kopteksten wordt niet ondersteund in de v1 SKU. Als u de v2 SKU niet hebt, maakt u een [Instantie Application Gateway v2 SKU](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) voordat u begint.

## <a name="create-required-objects"></a>Vereiste objecten maken

Als u http-headerherschrijven wilt configureren, moet u deze stappen uitvoeren.

1. Maak de objecten die nodig zijn voor het herschrijven van HTTP-koppen:

   - **Actie herschrijven:** wordt gebruikt om de koptekstvelden voor aanvragen en aanvragen op te geven die u wilt herschrijven en de nieuwe waarde voor de kopteksten. U een of meer herschrijfvoorwaarden koppelen aan een herschrijfactie.

   - **Voorwaarde herschrijven**: Een optionele configuratie. Voorwaarden herschrijven evalueert de inhoud van HTTP(S)-verzoeken en -antwoorden. De herschrijfactie vindt plaats als het HTTP(S)-verzoek of -antwoord overeenkomt met de herschrijfvoorwaarde.

     Als u meer dan één voorwaarde aan een actie koppelt, vindt de actie alleen plaats wanneer aan alle voorwaarden is voldaan. Met andere woorden, de bewerking is een logische EN operatie.

   - **Regel herschrijven**: Bevat meerdere herschrijfactie / herschrijven van conditiecombinaties.

   - **Regelvolgorde:** hiermee u bepalen in welke volgorde de herschrijfregels worden uitgevoerd. Deze configuratie is handig wanneer u meerdere herschrijfregels in een herschrijfset hebt. Een herschrijfregel met een lagere regelvolgordewaarde wordt eerst uitgevoerd. Als u dezelfde regelvolgordewaarde toewijst aan twee herschrijfregels, is de uitvoeringsvolgorde niet-deterministisch.

   - **Set herschrijven:** bevat meerdere herschrijfregels die worden gekoppeld aan een regel voor het routeren van aanvragen.

2. Voeg de herschrijfset toe aan een routeringsregel. De herschrijfconfiguratie is via de routeringsregel gekoppeld aan de bronlistener. Wanneer u een basisrouteringsregel gebruikt, wordt de configuratie van de koptekst herschrijven gekoppeld aan een bronlistener en is een globale koptekst herschrijven. Wanneer u een routeringsregel op basis van paden gebruikt, wordt de configuratie van de koptekst herschrijven gedefinieerd op de URL-padkaart. In dat geval is het alleen van toepassing op het specifieke padgebied van een site.

U meerdere HTTP-koptekstsets opnieuw schrijven en elke herschrijfset toepassen op meerdere listeners. U echter slechts één herschrijvenset toepassen op een specifieke listener.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com/) met uw Azure-account.

## <a name="configure-header-rewrite"></a>Header opnieuw schrijven configureren

In dit voorbeeld wijzigen we een omleidings-URL door de locatiekoptekst in het HTTP-antwoord te herschrijven dat door een back-endtoepassing wordt verzonden.

1. Selecteer **Alle bronnen**en selecteer vervolgens de toepassingsgateway.

2. Selecteer **Opnieuw schrijven** in het linkerdeelvenster.

3. Selecteer **Set herschrijven:**

   ![Herschrijfset toevoegen](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Geef een naam op voor de herschrijfset en koppel deze aan een routeringsregel:

   - Voer de naam in voor de herschrijfset in het vak **Naam.**
   - Selecteer een of meer regels die worden vermeld in de lijst **Gekoppelde routeringsregels.** U alleen regels selecteren die niet zijn gekoppeld aan andere herschrijfsets. De regels die al zijn gekoppeld aan andere herschrijfsets worden gedimd.
   - Selecteer **Volgende**.
   
     ![Naam en koppeling toevoegen](media/rewrite-http-headers-portal/name-and-association.png)

5. Een herschrijfregel maken:

   - Selecteer **Regel herschrijven toevoegen**.

     ![Herschrijfregel toevoegen](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - Voer een naam in voor de regel voor herschrijven in het vak **Regelnaam herschrijven.** Voer een getal in het vak **Regelreeks in.**

     ![Regelnaam herschrijven toevoegen](media/rewrite-http-headers-portal/rule-name.png)

6. In dit voorbeeld herschrijven we de locatiekoptekst alleen wanneer deze een verwijzing bevat naar azurewebsites.net. Voeg hiervoor een voorwaarde toe om te evalueren of de locatiekoptekst in het antwoord azurewebsites.net bevat:

   - Selecteer **Voorwaarde toevoegen** en selecteer het vak met de instructies **Als** om het uit te vouwen.

     ![Een voorwaarde toevoegen](media/rewrite-http-headers-portal/add-condition.png)

   - Selecteer **http-koptekst**in de lijst **Type variabele om de** lijst aan te vinken .

   - Selecteer **Antwoord**in de lijst **Kopteksttypen** .

   - Omdat we in dit voorbeeld de locatiekoptekst evalueren, een algemene koptekst, selecteert u **Algemene koptekst** onder **Koptekstnaam**.

   - Selecteer **Locatie**in de lijst **Algemene koptekst** .

   - Selecteer **Onder Hoofdlettergevoelig**de optie **Nee**.

   - Selecteer **gelijk (=)** in **de** operatorlijst.

   - Voer een normaal expressiepatroon in. In dit voorbeeld gebruiken we `(https?):\/\/.*azurewebsites\.net(.*)$`het patroon .

   - Selecteer **OK**.

     ![Een voorwaarde Als configureren](media/rewrite-http-headers-portal/condition.png)

7. Voeg een actie toe om de locatiekoptekst te herschrijven:

   - Selecteer in de lijst **Actietype** de optie **Instellen**.

   - Selecteer **Antwoord**in de lijst **Kopteksttypen** .

   - Selecteer Algemene **koptekst**onder **Koptekstnaam**.

   - Selecteer **Locatie**in de lijst **Algemene koptekst** .

   - Voer de kopwaarde in. In dit voorbeeld gebruiken `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` we als kopwaarde. Deze waarde vervangt *azurewebsites.net* door *contoso.com* in de locatiekoptekst.

   - Selecteer **OK**.

     ![Een actie toevoegen](media/rewrite-http-headers-portal/action.png)

8. Selecteer **Maken** om de herschrijfset te maken:

   ![Selecteer Maken](media/rewrite-http-headers-portal/create.png)

9. De weergave Set opnieuw schrijven wordt geopend. Controleer of de gemaakte herschrijfset zich bevindt in de lijst met herschrijfsets:

   ![Setweergave opnieuw schrijven](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Volgende stappen

Zie [veelvoorkomende scenario's voor het herschrijven van kopteksten voor](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)meer informatie over het instellen van een aantal veelvoorkomende use cases.