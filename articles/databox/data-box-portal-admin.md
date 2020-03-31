---
title: Azure Data Box beheren, Azure Data Box Heavy via Azure portal | Microsoft Documenten
description: Beschrijft hoe u de Azure-portal gebruiken om uw Azure Data Box en Azure Data Box Heavy te beheren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/07/2019
ms.author: alkohli
ms.openlocfilehash: 581f95bd813445d2cc9bd83d91917ea83f0bf04f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259992"
---
# <a name="use-the-azure-portal-to-administer-your-azure-data-box-and-azure-data-box-heavy"></a>Gebruik de Azure-portal om uw Azure Data Box en Azure Data Box Heavy te beheren

Dit artikel is van toepassing op zowel Azure Data Box als Azure Data Box Heavy. In dit artikel worden enkele complexe werkstromen en beheertaken beschreven die kunnen worden uitgevoerd op het Azure Data Box-apparaat. U het Data Box-apparaat beheren via de Azure-portal of via de lokale webgebruikersinterface.

Dit artikel richt zich op de taken die u kunt uitvoeren met behulp van de Azure-portal. Gebruik de Azure-portal om orders te beheren, het gegevensbox-apparaat te beheren en de status van de bestelling bij te houden naarmate deze wordt voltooid.


## <a name="cancel-an-order"></a>Een order annuleren

Soms moet u een order om een bepaalde reden annuleren nadat u deze hebt geplaatst. U kunt de order alleen annuleren als deze nog niet is verwerkt. Zodra de bestelling is verwerkt en het Data Box-apparaat is voorbereid, is het niet mogelijk om de bestelling te annuleren.

Voer de volgende stappen uit om een order te annuleren.

1.  Ga naar **Overzicht > Annuleren**.

    ![Order annuleren 1](media/data-box-portal-admin/cancel-order1.png)

2.  Geef een reden op voor het annuleren van de order.  

    ![Order annuleren 2](media/data-box-portal-admin/cancel-order2.png)

3.  Zodra de order is geannuleerd, werkt de portal de status van de order bij naar **Geannuleerd**.

## <a name="clone-an-order"></a>Een order klonen

Klonen is handig in bepaalde situaties. Een voorbeeld: Een gebruiker heeft Data Box gebruikt om wat gegevens over te dragen. Naarmate er meer gegevens worden gegenereerd, is er behoefte aan een ander Data Box-apparaat om die gegevens over te zetten naar Azure. In dit geval kan dezelfde order gewoon worden gekloond.

Voer de volgende stappen uit om een order te klonen.

1.  Ga naar **Overzicht > Klonen**. 

    ![Order klonen 1](media/data-box-portal-admin/clone-order1.png)

2.  Alle details van de order blijven hetzelfde. De ordernaam is de oorspronkelijke ordernaam met *-Kloon* eraan toegevoegd. Schakel het selectievakje in om te bevestigen dat u de privacyinformatie hebt gelezen. Klik **op Maken**.

De kloon wordt binnen enkele minuten gemaakt en de portal wordt bijgewerkt met de nieuwe order.


## <a name="delete-order"></a>Order verwijderen

Wanneer de order is voltooid, wilt u deze wellicht verwijderen. De order bevat uw persoonlijke informatie, zoals naam, adres en contactgegevens. Deze persoonlijke informatie wordt verwijderd wanneer de order wordt verwijderd.

U kunt alleen orders verwijderen die zijn voltooid of geannuleerd. Voer de volgende stappen uit om een order te verwijderen.

1. Ga naar **Alle resources**. Zoek uw order.

2. Klik op de order die u wilt verwijderen en ga naar **Overzicht**. Klik in de opdrachtbalk op **Verwijderen**.

    ![Data Box-order verwijderen 1](media/data-box-portal-admin/delete-order1.png)

3. Voer de naam van de order in wanneer u wordt gevraagd de orderverwijdering te bevestigen. Klik **op Verwijderen**.

## <a name="download-shipping-label"></a>Verzendlabel downloaden

Als het E-ink-scherm van uw Data Box niet werkt en het verzendlabel voor retourzending niet weergeeft, kunt u het verzendlabel downloaden. Er is geen E-ink display op de Data Box Heavy en daarom is deze workflow niet van toepassing op Data Box Heavy.

Voer de volgende stappen uit om een verzendlabel te downloaden.

1.  Ga naar **Overzicht > Verzendlabel downloaden**. Deze optie is alleen beschikbaar nadat het apparaat is verzonden. 

    ![Verzendlabel downloaden](media/data-box-portal-admin/download-shipping-label.png)

2.  Hiermee downloadt u het volgende verzendlabel voor retourzending. Sla het etiket op en print het uit. Vouw en steek het etiket in de heldere hoes op het apparaat. Zorg dat het label zichtbaar is. Verwijder eventuele stickers van de vorige verzending die op het apparaat zitten.

    ![Voorbeeld van verzendlabel](media/data-box-portal-admin/example-shipping-label.png)

## <a name="edit-shipping-address"></a>Verzendadres bewerken

U moet het verzendadres wellicht bewerken nadat de order is geplaatst. Dit kan alleen als het apparaat nog niet is verzonden. Zodra het apparaat is verzonden, is deze optie niet meer beschikbaar.

Voer de volgende stappen uit om de order te bewerken.

1. Ga naar **Ordergegevens > Verzendadres bewerken**.

    ![Verzendadres bewerken 1](media/data-box-portal-admin/edit-shipping-address1.png)

2. Bewerk en valideer het verzendadres en sla de wijzigingen vervolgens op.

    ![Verzendadres bewerken 2](media/data-box-portal-admin/edit-shipping-address2.png)

## <a name="edit-notification-details"></a>Meldingsdetails bewerken

U moet wellicht wijzigen welke gebruikers de e-mails met de orderstatus ontvangen. Een voorbeeld: Een bepaalde gebruiker moet worden geïnformeerd wanneer het apparaat wordt afgeleverd of opgehaald. Een andere gebruiker moet mogelijk worden geïnformeerd wanneer de gegevenskopie is voltooid, zodat hij of zij kan controleren of de gegevens zich in het Azure-opslagaccount bevinden voordat deze van de bron wordt verwijderd. In deze gevallen kunt u de meldingsdetails bewerken.

Voer de volgende stappen uit om meldingsdetails te bewerken.

1. Ga naar **Ordergegevens > Meldingsdetails bewerken**.

    ![Meldingsdetails bewerken 1](media/data-box-portal-admin/edit-notification-details1.png)

2. U kunt de meldingsdetails nu bewerken en de wijzigingen vervolgens opslaan.
 
    ![Meldingsdetails bewerken 2](media/data-box-portal-admin/edit-notification-details2.png)


## <a name="download-order-history"></a>Ordergeschiedenis downloaden

Nadat de Data Box-order is voltooid, worden de gegevens op de schijven van het apparaat gewist. Wanneer het opschonen van het apparaat is voltooid, kunt u de ordergeschiedenis downloaden in de Azure-portal.

Voer de volgende stappen uit om de ordergeschiedenis te downloaden.

1. Ga in uw Data Box-order naar **Overzicht**. Controleer of de order is voltooid. Als de order is voltooid en het opschonen van het apparaat is voltooid, gaat u naar **Ordergegevens**. De optie **Ordergeschiedenis downloaden** is beschikbaar.

    ![Ordergeschiedenis downloaden](media/data-box-portal-admin/download-order-history-1.png)

2. Klik op **Ordergeschiedenis downloaden**. In de downloadgeschiedenis ziet u een record met logboeken voor tracering van de koerier. Er zullen twee sets van log die overeenkomen met de twee knooppunten op een Data Box Heavy. Als u naar de onderkant van dit logboek bladert, kunt u koppelingen zien naar:
    
   - **Logboeken kopiëren**: hier vindt u de lijst met bestanden met fouten tijdens het kopiëren van gegevens van de Data Box naar uw Azure Storage-account.
   - **Controlelogboeken** - bevatten informatie over het inschakelen en openen van shares in het gegevensvak wanneer deze zich buiten azure-datacenter bevinden.
   - **BOM-bestanden**: hier vindt u de lijst met bestanden (ook wel bekend als het bestandsmanifest) die u kunt downloaden tijdens **Voorbereiding voor verzending** en de bestandsnamen, grootte en de bestandscontrolesommen.

       ```
       -------------------------------
       Microsoft Data Box Order Report
       -------------------------------
       Name                                               : DataBoxTestOrder                              
       StartTime(UTC)                                     : 10/31/2018 8:49:23 AM +00:00                       
       DeviceType                                         : DataBox                                           
       -------------------
       Data Box Activities
       -------------------
       Time(UTC)                 | Activity                       | Status          | Description  
       
       10/31/2018 8:49:26 AM     | OrderCreated                   | Completed       |                                                   
       11/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |                                                   
       11/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 11/3/2018 1:36:43        PM at AMSTERDAM-NLD                                                                                
       11/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 11/4/2018        8:23:30 PM at AMSTERDAM-NLD                                                                        
       11/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time :          11/4/2018 11:43:34 PM at AMSTERDAM-NLD                                                               
       11/5/2018 1:38:20 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LEIPZIG-DEU. Local Time :        11/5/2018 1:38:20 AM at LEIPZIG-DEU                                                                
       11/5/2018 2:31:07 AM      | ShippingToCustomer             | InProgress      | Processed at LEIPZIG-DEU. Local Time : 11/5/2018          2:31:07 AM at LEIPZIG-DEU                                                                            
       11/5/2018 4:05:58 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LEIPZIG-DEU. Local Time :            11/5/2018 4:05:58 AM at LEIPZIG-DEU                                                                    
       11/5/2018 4:35:43 AM      | ShippingToCustomer             | InProgress      | Transferred through LUTON-GBR. Local Time :              11/5/2018 4:35:43 AM at LUTON-GBR                                                                         
       11/5/2018 4:52:15 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LUTON-GBR. Local Time :              11/5/2018 4:52:15 AM at LUTON-GBR                                                                        
       11/5/2018 5:47:58 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 10/5/2018 5:47:58 AM at LONDON-HEATHROW-GBR                                                
       11/5/2018 6:27:37 AM      | ShippingToCustomer             | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            11/5/2018 6:27:37 AM at LONDON-HEATHROW-GBR                                                            
       11/5/2018 6:39:40 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 11/5/2018 6:39:40 AM at LONDON-HEATHROW-GBR                                                    
       11/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in LAMBETH-GBR. Local        Time : 11/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
       11/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 11/5/2018            9:13:24 AM at LAMBETH-GBR                                                                               
       11/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 11/5/2018        12:03:04 PM at LAMBETH-GBR                                                                          
       1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25        PM at LAMBETH-GBR                                                                                       
       1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at LAMBETH-GBR. Local Time : 1/25/2019          8:03:55 PM at LAMBETH-GBR                                                                            
       1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in LAMBETH-GBR. Local Time :            1/25/2019 8:04:58 PM at LAMBETH-GBR                                                                    
       1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
       1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
       1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR                                                   
       1/26/2019 2:17:10 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility BRUSSELS-BEL. Local Time        : 1/26/2019 2:17:10 PM at BRUSSELS-BEL                                                              
       1/26/2019 2:31:57 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        2:31:57 PM at BRUSSELS-BEL                                                                          
       1/26/2019 3:37:53 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        3:37:53 PM at BRUSSELS-BEL                                                                          
       1/27/2019 11:01:45 AM     | ShippingToDataCenter           | InProgress      | Departed Facility in BRUSSELS-BEL. Local Time :          1/27/2019 11:01:45 AM at BRUSSELS-BEL                                                                 
       1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD.            Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
       1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019            9:07:57 AM at AMSTERDAM-NLD                                                                             
       1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019            1:35:56 PM at AMSTERDAM-NLD                                                                            
       1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019        2:57:48 PM at AMSTERDAM-NLD                                                                         
       1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |                                              
       1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully                  
       1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.                               
       1/29/2019 4:55:00 PM      | DataCopy                       | Started         |                                                 
       2/2/2019 7:07:34 PM       | DataCopy                       | Completed       | Copy Completed.                                   
       2/4/2019 7:47:32 PM       | SecureErase                    | Started         |                                                  
       2/4/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:DEVICESERIALNO has been sanitized          according to NIST 800-88 Rev 1.                                                                       

       ------------------
       Data Box Log Links
       ------------------

       Account Name         : Gus                                                       
       Copy Logs Path       : databoxcopylog/DataBoxTestOrder_CHC533180024_CopyLog_73a81b2d613547a28ecb7b1612fe93ca.xml
       Audit Logs Path      : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024
       BOM Files Path       : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024      
       ```
     U kunt vervolgens naar uw opslagaccount gaan en de logboeken met kopieerbewerkingen bekijken.

![Logboeken in opslagaccounts](media/data-box-portal-admin/logs-in-storage-acct-2.png)

U kunt ook de bewakingsketenlogboeken bekijken die de auditlogboeken en de BOM-bestanden bevatten.

![Logboeken in opslagaccounts](media/data-box-portal-admin/logs-in-storage-acct-1.png)

## <a name="view-order-status"></a>Orderstatus bekijken

Wanneer de apparaatstatus in de portal verandert, wordt u per e-mail op de hoogte gesteld.

|Orderstatus |Beschrijving |
|---------|---------|
|Besteld     | De order is geplaatst. <br>Als het apparaat beschikbaar is, identificeert Microsoft het apparaat dat moet worden verzonden en bereidt Microsoft het apparaat voor. <br> Als het apparaat niet onmiddellijk beschikbaar is, wordt de order verwerkt zodra het apparaat beschikbaar is. Het kan enkele dagen tot een paar maanden duren voordat de order is verwerkt. Als de order niet binnen 90 dagen kan worden voltooid, wordt deze geannuleerd en wordt u op de hoogte gesteld.         |
|Verwerkt     | De order is verwerkt. In overeenstemming met uw order wordt het apparaat in het datacenter voorbereid voor verzending.         |
|Verzonden     | De order is verzonden. Gebruik het volgnummer dat in uw order in de portal wordt weergegeven om de verzending bij te houden.        |
|Afgeleverd     | De verzending is afgeleverd op het adres dat in de order is opgegeven.        |
|Opgehaald     |Uw retourzending is opgehaald en gescand door de vervoerder.         |
|Ontvangen     | Uw apparaat is ontvangen en gescand in het Azure-datacenter. <br> Zodra de verzending is geïnspecteerd, wordt de apparaatupload gestart.      |
|Gegevens kopiëren     | Het kopiëren van gegevens is in voortgang. Houd de kopieervoortgang voor uw order bij in de Azure-portal. <br> Wacht totdat het kopiëren van gegevens is voltooid. |
|Voltooid       |De order is voltooid.<br> Verifieer dat uw gegevens zich in Azure bevinden voordat u de on-premises gegevens van servers verwijdert.         |
|Voltooid met fouten| Het kopiëren van gegevens is voltooid, maar er zijn fouten opgetreden tijdens het kopiëren. <br> Bekijk de logboeken met kopieerbewerkingen via het pad in de Azure-portal. Zie [voorbeelden van copy logs wanneer upload voltooid met fouten](https://docs.microsoft.com/azure/databox/data-box-logs#upload-completed-with-errors).   |
|Aangevuld met waarschuwingen| Gegevens kopie is voltooid, maar uw gegevens zijn gewijzigd. De gegevens hadden niet-kritieke blob- of bestandsnaamfouten die zijn opgelost door de bestands- of blobnamen te wijzigen. <br> Bekijk de logboeken met kopieerbewerkingen via het pad in de Azure-portal. Noteer de wijzigingen in uw gegevens. Zie [voorbeelden van copy logs wanneer upload voltooid met waarschuwingen](https://docs.microsoft.com/azure/databox/data-box-logs#upload-completed-with-warnings).   |
|Geannuleerd            |De order is geannuleerd. <br> U hebt de order zelf geannuleerd, of er is een fout opgetreden waardoor de service de order heeft geannuleerd. Als de order niet binnen 90 dagen kan worden voltooid, wordt deze ook geannuleerd en wordt u op de hoogte gesteld.     |
|Opruimen | De gegevens op het apparaat worden gewist. Het opruimen van het apparaat wordt als voltooid beschouwd zodra de ordergeschiedenis beschikbaar is om te downloaden in de Azure-portal.|



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [oplossen van problemen met gegevensboxen en gegevensboxen](data-box-troubleshoot.md).
