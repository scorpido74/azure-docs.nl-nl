---
title: 'Zelfstudie: Gegevens kopiëren naar Azure Data Box Heavy via de service voor gegevenskopiëren'
description: In deze zelfstudie leert u hoe u gegevens kopiëren naar uw Azure Data Box Heavy-apparaat via de gegevenskopieerservice
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 67547db53d2b9ce05838335ffcb5d789b77ecbbe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77560217"
---
# <a name="tutorial-use-the-data-copy-service-to-copy-data-into-azure-data-box-heavy-preview"></a>Zelfstudie: De gegevenskopieerservice gebruiken om gegevens naar Azure Data Box Heavy te kopiëren (voorbeeld)

In deze zelfstudie wordt beschreven hoe u gegevens opneemt met behulp van de gegevenskopieerservice, zonder gebruik te maken van een tussenliggende host. De datacopyservice wordt lokaal uitgevoerd op Azure Data Box Heavy, maakt verbinding met uw NAS-apparaat (Network Attached Storage) via SMB en kopieert gegevens naar Data Box Heavy.

De gegevenskopieerservice gebruiken:

- In NAS-omgevingen waar tussenliggende hosts mogelijk niet beschikbaar zijn.
- Met kleine bestanden waarvoor soms weken nodig zijn om gegevens op te nemen en te uploaden. Met de gegevenskopieerservice wordt de opname- en uploadtijd voor kleine bestanden aanzienlijk verbeterd.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gegevens kopiëren naar Data Box Heavy

## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

1. U hebt deze zelfstudie voltooid: [Azure Data Box Heavy instellen.](data-box-heavy-deploy-set-up.md)
2. U hebt de Data Box Heavy ontvangen en de orderstatus in de portal is **Geleverd**.
3. U hebt de referenties van het bron-NAS-apparaat waarmee u verbinding wilt maken om gegevens te kopiëren.
4. U bent verbonden met een netwerk met hoge snelheid. Voor de snelste kopieersnelheden kunnen twee verbindingen van 40 GbE (één per knooppunt) naast elkaar worden gebruikt. Als u niet beschikt over een 40-GbE-verbinding, is het advies dat u ten minste twee verbindingen van 10 GbE (één per knooppunt) gebruikt. 

## <a name="copy-data-to-data-box-heavy"></a>Gegevens kopiëren naar Data Box Heavy

Wanneer u bent verbonden met het NAS-apparaat, kunt u uw gegevens gaan kopiëren. Neem de volgende punten door voordat u gegevens gaat kopiëren:

- Controleer tijdens het kopiëren van gegevens of de gegevensgrootte overeenkomt met de groottelimieten die zijn beschreven in de [limieten voor Azure-opslag en Gegevensvak Zwaar](data-box-heavy-limits.md).
- Als gegevens die door Data Box Heavy worden geüpload, gelijktijdig worden geüpload door andere toepassingen buiten Data Box Heavy, kunnen fouten in uploadwerk en gegevensbeschadiging het gevolg zijn.
- Als de gegevens worden gewijzigd terwijl deze door de kopieerservice worden gelezen, is het mogelijk dat er fouten optreden of gegevens beschadigd raken.

U moet een taak maken als u gegevens wilt kopiëren met behulp van de gegevenskopieerservice:

1. Ga in de lokale webgebruikersinterface van uw databox zwaar apparaat naar**Kopieergegevens** **beheren.** > 
2. Selecteer op de pagina **Gegevens kopiëren** de optie **Maken**.

    ![Maken selecteren op de pagina Gegevens kopiëren](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. Voer in het dialoogvenster **Taak configureren en starten** de volgende velden in:
    
    |Veld                          |Waarde    |
    |-------------------------------|---------|
    |**Taaknaam**                       |Een unieke naam van maximaal 230 tekens voor de taak. De volgende tekens zijn niet toegestaan in de naam van de taak: \<, \>, \|, \?, \*, \\, \:, \/ en \\\.         |
    |**Bronlocatie**                |Geef het SMB-pad naar de gegevensbron op in de indeling: `\\<ServerIPAddress>\<ShareName>` of `\\<ServerName>\<ShareName>`.        |
    |**Gebruikersnaam**                       |Gebruikersnaam in de notatie `\\<DomainName><UserName>` voor toegang tot de gegevensbron. Als een lokale beheerder verbinding maakt, hebben ze expliciete beveiligingsmachtigingen nodig. Klik met de rechtermuisknop op de map, selecteer **Eigenschappen** en selecteer **Vervolgens Beveiliging**. Hiermee moet de lokale beheerder worden toegevoegd aan het tabblad **Beveiliging.**       |
    |**Wachtwoord**                       |Wachtwoord voor toegang tot de gegevensbron.           |
    |**Doelopslagaccount**    |Selecteer in de lijst het doelopslagaccount waarnaar u de gegevens wilt uploaden.         |
    |**Doeltype**       |Selecteer het doelopslagtype in de lijst: **Blob blokkeren,** **Paginablob**of **Azure-bestanden**.        |
    |**Doelcontainer/-share**    |Voer de naam in van de container of share waarnaar u gegevens wilt uploaden in uw doelopslagaccount. De naam kan een sharenaam of een containernaam zijn. Gebruik bijvoorbeeld `myshare` of `mycontainer`. U kunt de naam ook invoeren in de indeling `sharename\directory_name` of `containername\virtual_directory_name`.        |
    |**Bestanden kopiëren die overeenkomen met het patroon**    | U kunt het bestandsnaampatroon invoeren op de volgende twee manieren:<ul><li>**Jokerexpressies gebruiken:** Alleen `*` `?` en worden ondersteund in wildcard-expressies. De expressie `*.vhd` bijvoorbeeld komt overeen met alle bestanden die de extensie `.vhd` hebben. En zo komt `*.dl?` overeen met alle bestanden die de extensie `.dl` hebben of die beginnen met `.dl`, zoals `.dll`. En zo komt `*foo` overeen met alle bestanden waarvan de bestandsnaam eindigt op `foo`.<br>U kunt de expressie met jokertekens rechtstreeks invoeren in het veld. Standaard wordt de waarde die u in het veld invoert, beschouwd als een expressie met jokertekens.</li><li>**Gebruik reguliere expressies:** PoSIX-gebaseerde reguliere expressies worden ondersteund. De reguliere expressie `.*\.vhd` bijvoorbeeld komt overeen met alle bestanden die de extensie `.vhd` hebben. Voor reguliere expressies geeft u het `<pattern>` rechtstreeks op als `regex(<pattern>)`. Voor meer informatie over reguliere expressies gaat u naar [Regular expression language - a quick reference](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) (Reguliere expressies - een snelzoekgids).</li><ul>|
    |**Bestandsoptimalisatie**              |Als deze functie is ingeschakeld, worden bestanden die kleiner zijn dan 1 MB tijdens de opname verpakt. Dit verpakken versnelt het kopiëren van gegevens voor kleine bestanden. Het bespaart ook aanzienlijk veel tijd wanneer het aantal bestanden veel groter is dan het aantal directory's.        |
 
4. Selecteer **Starten**. De invoer wordt gevalideerd en als de validatie is geslaagd, wordt de taak gestart. Het kan enkele minuten duren voordat de taak start.

    ![Een taak starten vanuit het dialoogvenster De taak configureren en starten](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. Er wordt een taak gemaakt met de opgegeven instellingen. U kunt een taak onderbreken, hervatten, annuleren of opnieuw starten. Schakel het selectievakje in naast de taaknaam en selecteer de gewenste knop.

    ![Een taak beheren op de pagina Gegevens kopiëren](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - U kunt een taak onderbreken als deze tijdens piekuren invloed heeft op de resources van het NAS-apparaat:

        ![Een taak onderbreken op de pagina Gegevens kopiëren](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        U kunt de taak later, na de piekuren, hervatten:

        ![Een taak hervatten op de pagina Gegevens kopiëren](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - U kunt een taak op elk gewenst moment annuleren:

        ![Een taak annuleren op de pagina Gegevens kopiëren](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png)
        
        Wanneer u een taak annuleert, moet u dit bevestigen:

        ![Annulering van taak bevestigen](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        Als u besluit om een taak te annuleren, worden de gegevens die al zijn gekopieerd, niet verwijderd. Als u gegevens wilt verwijderen die u naar uw Data Box-apparaat hebt gekopieerd, moet u het apparaat opnieuw instellen.

        ![Een apparaat opnieuw instellen](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > Als u een taak annuleert of onderbreekt, is het mogelijk dat grote bestanden slechts gedeeltelijk zijn gekopieerd. Deze gedeeltelijk gekopieerde bestanden worden in dezelfde staat geüpload naar Azure. Wanneer u een taak annuleert of onderbreekt, moet u controleren of uw bestanden correct zijn gekopieerd. U kunt de bestanden valideren door de SMB-shares te bekijken of het BOM-bestand te downloaden.

    - U kunt een taak opnieuw starten als deze is mislukt vanwege een tijdelijke fout, zoals een netwerkfout. U kunt een taak echter niet opnieuw starten als deze een definitieve status heeft bereikt, zoals **Voltooid** of **Voltooid met fouten**. Taakfouten kunnen worden veroorzaakt door problemen met bestandsnamen of -grootten. Deze fouten worden vastgelegd in een logboek, maar de taak kan pas opnieuw worden gestart nadat deze is voltooid.

        ![Een mislukte taak opnieuw starten](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        Als er een fout optreedt en u de taak niet opnieuw kunt starten, downloadt u de foutenlogboeken en zoekt u de fout op in de logboekbestanden. Wanneer u het probleem hebt opgelost, maakt u een nieuwe taak om de bestanden te kopiëren. U kunt ook [de bestanden kopiëren via SMB](data-box-deploy-copy-data.md).
    
    - In deze release kunt u een taak niet verwijderen.
    
    - U kunt een onbeperkt aantal taken maken, maar u kunt maximaal 10 taken tegelijkertijd uitvoeren.
    - Als **Bestandsoptimalisatie** is ingeschakeld, worden kleine bestanden ingepakt bij opname om kopieerprestaties te verbeteren. In dergelijke gevallen wordt er een ingepakt bestand weergegeven (met een GUID als bestandsnaam). Verwijder dit bestand niet. Dit wordt uitgepakt tijdens het uploaden.

6. Terwijl de taak wordt uitgevoerd, ziet u het volgende op de pagina **Gegevens kopiëren**:

    - In de kolom **Status** ziet u de status van de kopieertaak. De status kan zijn:
        - **Wordt uitgevoerd**
        - **Mislukt**
        - **Geslaagd**
        - **Wordt onderbroken**
        - **Onderbroken**
        - **Wordt geannuleerd**
        - **Geannuleerd**
        - **Voltooid met fouten**
    - In de kolom **Bestanden** ziet u het aantal en de totale grootte van de bestanden die worden gekopieerd.
    - In de kolom **Verwerkt** ziet u het aantal en de totale grootte van de bestanden die worden verwerkt.
    - Selecteer in de kolom **Taakdetails** de optie **Weergeven** als u de taakdetails wilt bekijken.
    - Als er fouten optreden tijdens het kopieerproces, wat wordt weergegeven in de kolom **# fouten**, gaat u naar de kolom **Foutenlogboek** en downloadt u de foutenlogboeken om het probleem op te lossen.

Wacht tot de kopieertaak is voltooid. Omdat een aantal fouten alleen wordt vastgelegd op de pagina **Verbinding maken en kopiëren**, moet u wachten tot de kopieertaak zonder fouten is voltooid voordat u verder gaat met de volgende stap.

![Geen fouten op de pagina Verbinding maken en kopiëren](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

Om de gegevensintegriteit te garanderen wordt een controlesom inline berekend terwijl de gegevens worden gekopieerd. Selecteer na het kopiëren de optie **Dashboard weergeven** om de gebruikte ruimte en vrije ruimte op uw apparaat te controleren.
    
![Vrije en ongebruikte ruimte verifiëren op het dashboard](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

> [!IMPORTANT]
> Herhaal dezelfde instructies om gegevens naar het tweede knooppunt op Data Box Heavy te kopiëren.

Wanneer de kopieertaak is voltooid, kunt u **Voorbereiding voor verzending** selecteren.

>[!NOTE]
> **Voorbereiden op het verzenden** kan niet worden uitgevoerd terwijl kopieertaken aan de gang zijn.

## <a name="next-steps"></a>Volgende stappen

Ga naar de volgende zelfstudie om te leren hoe u uw Data Box Heavy-apparaat terug verzendt naar Microsoft.

> [!div class="nextstepaction"]
> [Uw Azure Data Box Heavy-apparaat verzenden naar Microsoft](./data-box-heavy-deploy-picked-up.md)

