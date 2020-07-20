---
title: Zelfstudie voor het terugsturen van Azure Data Box in exportbestelling | Microsoft Docs
description: Lees hoe u uw Azure Data Box naar Microsoft verzendt nadat de exportbestelling is voltooid
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 7023d29bcb559f4edf11b374b9bfb959e968f626
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86208765"
---
# <a name="tutorial-return-azure-data-box-preview"></a>Zelfstudie: Azure Data Box retourneren (preview)


In deze zelfstudie wordt beschreven hoe u uw Azure Data Box retourneert en hoe de gegevens worden gewist zodra het apparaat is ontvangen door het Azure-datacenter.

In deze zelfstudie vindt u informatie over onderwerpen als:

> [!div class="checklist"]
> * Vereisten
> * Voorbereiding voor verzending
> * De Data Box naar Microsoft verzenden
> * Gegevens verwijderen uit de Data Box

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

- U hebt de [Zelfstudie: Gegevens kopiëren uit Azure Data Box](data-box-deploy-export-copy-data.md) voltooid. 
- Kopieertaken zijn voltooid. Voorbereiding voor verzending kan niet worden uitgevoerd als er nog kopieertaken worden uitgevoerd.

## <a name="prepare-to-ship"></a>Voorbereiding voor verzending

[!INCLUDE [data-box-export-prepare-to-ship](../../includes/data-box-export-prepare-to-ship.md)]

De volgende stappen zijn afhankelijk van de locatie waarvandaan u het apparaat retourneert.

## <a name="ship-data-box-back"></a>De Data Box terugsturen

Zorg ervoor dat het kopiëren van gegevens van het apparaat is voltooid en dat de stappen in **Voorbereiding voor verzending** zijn uitgevoerd. De procedure is afhankelijk van de regio waaruit u het apparaat verzendt.

## <a name="in-us-canada-europe"></a>[In de VS, Canada, Europa](#tab/in-us-canada-europe)

Voer de volgende stappen uit als u het apparaat vanuit de VS, Canada of Europa retourneert.

1. Zorg ervoor dat het apparaat uit staat en de kabels zijn verwijderd. 
2. Rol het netsnoer op en plaats het veilig aan de achterzijde van het apparaat.
3. Zorg ervoor dat het verzendlabel wordt weergegeven op het E-ink-scherm en plan met de vervoerder een tijdstip waarop het kan worden opgehaald. Neem contact op met de Microsoft-ondersteuning als het label beschadigd is of ontbreekt of niet wordt weergegeven op het e-inkscherm. Als het ondersteuningsteam dit aanraadt, gaat u in de Azure-portal naar **Overzicht > Verzendlabel downloaden**. Download het verzendlabel en bevestig dat op het apparaat. 
4. Als u het apparaat retourneert, spreekt u met UPS een tijdstip af waarop het kan worden opgehaald. Ophalen van apparaat plannen:

    - Bel met UPS (gratis land-/regiospecifiek nummer).
    - Vermeld in uw gesprek het trackingnummer voor de retournering, zoals weergegeven op het E-ink-display of het afgedrukte label.
    - Als het trackingnummer niet wordt vermeld, moet u bij het afhalen door UPS extra kosten betalen.

    In plaats van het ophalen te plannen, kunt u de Data Box ook zelf naar de dichtstbijzijnde inleverlocatie brengen.
4. Nadat de Data Box door de vervoerder is opgehaald en gescand, verandert de orderstatus in de portal in **Opgehaald**. Er wordt ook een tracerings-id weergegeven.


## <a name="in-australia"></a>[In Australië](#tab/in-australia)

Voor Azure-datacenters in Australië geldt een extra beveiligingsmaatregel. Alle inkomende verzendingen moeten vooraf worden gemeld. Voer de volgende stappen uit voor verzenden vanuit Australië.


1. Bewaar de oorspronkelijke doos om het apparaat terug te sturen.
2. Zorg ervoor dat het kopiëren van gegevens naar het apparaat is voltooid en dat de stappen in **Voorbereiding voor verzending** zijn uitgevoerd.
3. Schakel het apparaat uit en verwijder de kabels.
4. Rol het meegeleverde netsnoer op en plaats het in de opberglade aan de achterkant van het apparaat.
5. Boek via de [DHL-koppeling](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference)  online een afspraak voor de retourzending.

## <a name="in-japan"></a>[In Japan](#tab/in-japan) 

1. Bewaar de oorspronkelijke doos om het apparaat terug te sturen.
2. Schakel het apparaat uit en verwijder de kabels.
3. Rol het meegeleverde netsnoer op en plaats het in de opberglade aan de achterkant van het apparaat.
4. Schrijf als gegevens van de afzender uw bedrijfsnaam en adresgegevens op de vrachtbrief.
5. Stuur een e-mail naar Quantium Solutions met behulp van de volgende e-mailsjabloon.

    - Als er geen vrachtbrief van Japan Post Chakubarai is meegeleverd of ontbreekt, vermeldt u dit in het e-mailbericht. Quantium Solutions Japan zal Japan Post verzoeken om de vrachtbrief mee te nemen als het pakket wordt opgehaald.
    - Als u meerdere orders hebt, moet u een e-mail verzenden om afzonderlijke pakketten te laten ophalen.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box｜Job name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
    ```

3. U ontvangt per e-mail een bevestiging van Quantium Solutions nadat u het ophalen hebt afgesproken. De e-mailbevestiging bevat ook informatie over de Chakubarai-vrachtbrief.

Zo nodig kunt u contact opnemen met de ondersteuning van Quantium Solutions (in het Japans) via: 

- E-mail：Customerservice.JP@quantiumsolutions.com 
- Telefoon: 03-5755-0150 


## <a name="in-singapore"></a>[In Singapore](#tab/in-singapore) 

1. Bewaar de oorspronkelijke doos om het apparaat terug te sturen.
2. Noteer het volgnummer (weergegeven als referentienummer op de pagina Voorbereiden voor verzending van de lokale web-UI van Data Box). Dit is beschikbaar nadat de stap voor het voorbereiden van de verzending is voltooid. Download het verzendlabel vanaf deze pagina en plak het op de doos. 
3. Schakel het apparaat uit en verwijder de kabels.
4. Rol het meegeleverde netsnoer op en plaats het in de opberglade aan de achterkant van het apparaat. 
5. Stuur een e-mail naar SingPost Customer Service met behulp van de volgende e-mailsjabloon met het volgnummer.

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pick-up - OrderName 
    Body: 
        1. Requestor name  
        2. Requestor contact number
        3. Requestor collection address
        4. Preferred collection date
    ```

   > [!NOTE]
   > Voor boekingsaanvragen op een werkdag:
   > - Vóór 15.00 uur is de ophaaltijd op de volgende werkdag tussen 09.00 en 13.00.
   > - Na 15.00 uur is de ophaaltijd op de volgende werkdag tussen 14.00 en 18.00.  


## <a name="self-managed"></a>[Zelfbeheerd](#tab/in-selfmanaged) 

Als u Data Box gebruikt in Japan, Singapore, Korea en West-Europa en de bezorgoptie Zelfbeheerde verzending hebt geselecteerd tijdens een bestelling, volgt u deze instructies. 

1. Noteer de autorisatiecode die wordt weergegeven op de pagina Voorbereiden voor verzending van de lokale web-UI van Data Box nadat deze stap is voltooid.
2. Schakel het apparaat uit en verwijder de kabels. Rol het meegeleverde netsnoer op en plaats het in de opberglade aan de achterkant van het apparaat.
3. Wanneer u klaar bent om het apparaat terug te sturen, stuurt u een e-mail naar het Azure Data Box-team met de sjabloon hieronder.
    
    ```
    To: adbops@microsoft.com 
    Subject: Request for Azure Data Box drop-off for order: ‘orderName’ 
    Body: 
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```
---

## <a name="erasure-of-data-from-data-box"></a>Gegevens verwijderen uit de Data Box
 
Zodra het apparaat het Azure-datacenter heeft bereikt, worden de gegevens door de Data Box van de schijven gewist volgens de [richtlijnen van NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u meer geleerd over onderwerpen zoals:

> [!div class="checklist"]
> * Vereisten
> * Voorbereiding voor verzending
> * De Data Box naar Microsoft verzenden
> * Gegevens verwijderen uit de Data Box

Ga naar het volgende artikel om te lezen hoe u uw Data Box beheert.

> [!div class="nextstepaction"]
> [Data Box beheren via de Azure-portal](./data-box-portal-admin.md)


