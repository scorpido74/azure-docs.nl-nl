---
title: Back-ups maken van een SAP HANA Data Base naar Azure met Azure Backup | Microsoft Docs
description: In deze zelf studie wordt uitgelegd hoe u een back-up maakt van een SAP HANA Data Base naar Azure met de Azure Backup-service.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dacurwin
ms.openlocfilehash: a11d454feb965907f3bd4e994c0916eeb7236fa7
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034571"
---
# <a name="back-up-an-sap-hana-database-to-azure"></a>Back-up maken van een SAP HANA Data Base naar Azure

[Azure backup](backup-overview.md) ondersteunt de back-up van SAP Hana-data bases naar Azure.

> [!NOTE]
> Deze functie is momenteel beschikbaar als openbare preview-versie. Het is momenteel niet gereed voor productie en heeft geen gegarandeerde SLA. 

## <a name="scenario-support"></a>Scenario-ondersteuning

**Ondersteuning** | **Details**
--- | ---
**Ondersteunde geografische gebieden** | Australië-Zuid-Oost, Oost-Australië <br> Brazilië - zuid <br> Canada-centraal, Canada-oost <br> Zuid-Azië-oost, Azië-oost <br> VS-Oost, VS-Oost 2, West-Centraal VS, VS-West, VS-West 2, Noord-Centraal VS, centraal VS, Zuid-Centraal VS<br> India, midden, India Zuid <br> Japan (oost), Japan (west)<br> Korea Centraal, Korea Zuid <br> Europa - noord, Europa - west <br> UK-zuid, UK-west
**Ondersteunde VM-besturings systemen** | SLES 12 met SP2 of SP3.
**Ondersteunde HANA-versies** | Dit SDC op HANA 1. x, MDC op HANA 2. x < = SPS03

### <a name="current-limitations"></a>Huidige beperkingen

- U kunt alleen back-ups maken van SAP HANA-data bases die worden uitgevoerd op Azure-Vm's.
- U kunt SAP HANA back-up alleen configureren in de Azure Portal. De functie kan niet worden geconfigureerd met Power shell, CLI of de REST API.
- U kunt alleen back-ups maken van data bases in de scale-up-modus.
- U kunt om de 15 minuten een back-up maken van database Logboeken. Logboek back-ups gaan alleen naar de stroom nadat een volledige back-up voor de data base is voltooid.
- U kunt volledige en differentiële back-ups maken. Incrementele back-up wordt momenteel niet ondersteund.
- U kunt het back-upbeleid niet wijzigen nadat u het hebt toegepast voor SAP HANA back-ups. Als u een back-up met andere instellingen wilt maken, maakt u een nieuw beleid of wijst u een ander beleid toe.
  - Als u een nieuw beleid wilt maken, klikt u in de kluis op **beleids regels** > **back-upbeleid** >  **+ SAP Hana toevoegen** > **in azure VM**en geeft u beleids instellingen op.
  - Als u een ander beleid wilt toewijzen, klikt u in de eigenschappen van de virtuele machine waarop de data base wordt uitgevoerd op de huidige beleids naam. Vervolgens kunt u op de pagina **back-upbeleid** een ander beleid selecteren dat u wilt gebruiken voor de back-up.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u het volgende doet voordat u back-ups configureert:

1. Installeer op de virtuele machine waarop de SAP HANA-data base wordt uitgevoerd, het officiële micro soft [.net core Runtime 2,1](https://dotnet.microsoft.com/download/linux-package-manager/sles/runtime-current) -pakket. Houd rekening met het volgende:
    - U hebt alleen het **DotNet-runtime-2,1-** pakket nodig. U hebt **aspnetcore-runtime-2,1**niet nodig.
    - Als de VM geen Internet toegang heeft, Mirror of een offline cache voor dotnet-runtime-2,1 (en alle afhankelijke Rpm's) van de micro soft package feed die op de pagina is opgegeven.
    - Tijdens de installatie van het pakket wordt u mogelijk gevraagd om een optie op te geven. Als dit het geval is, geeft u **oplossing 2**op.

        ![Installatie optie pakket](./media/backup-azure-sap-hana-database/hana-package.png)

2. Installeer en schakel op de VM ODBC-stuur programmapakketten van het officiële SLES-pakket/medium in met behulp van Zypper, als volgt:

    ```unix
    sudo zypper update
    sudo zypper install unixODBC
    ```

3. Connectiviteit van de virtuele machine met Internet toestaan, zodat deze Azure kan bereiken, zoals beschreven in de [onderstaande](#set-up-network-connectivity)procedure.

4. Voer het script voor voorafgaande registratie uit in de virtuele machine waarin HANA is geïnstalleerd als een hoofd gebruiker. Het script is opgenomen [in de portal](#discover-the-databases) in de stroom en is vereist voor het instellen van de [juiste machtigingen](backup-azure-sap-hana-database-troubleshoot.md#setting-up-permissions).

### <a name="set-up-network-connectivity"></a>Netwerk connectiviteit instellen

Voor alle bewerkingen moet de SAP HANA VM verbinding hebben met open bare IP-adressen van Azure. VM-bewerkingen (database detectie, het configureren van back-ups, het plannen van back-ups, herstel punten, enzovoort) werken niet zonder connectiviteit. Verbinding maken door toegang te verlenen tot de IP-bereiken van het Azure-Data Center: 

- U kunt de [IP-](https://www.microsoft.com/download/details.aspx?id=41653) adresbereiken voor Azure-data centers downloaden en vervolgens toegang tot deze IP-adressen toestaan.
- Als u netwerk beveiligings groepen (Nsg's) gebruikt, kunt u de code van de Cloud- [service](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) gebruiken om alle open bare IP-adressen van Azure toe te staan. U kunt de [cmdlet Set-AzureNetworkSecurityRule](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) gebruiken om NSG-regels te wijzigen.

## <a name="onboard-to-the-public-preview"></a>Onboarding voor de open bare preview

Onboarding voor de open bare preview als volgt:

- Registreer uw abonnements-ID bij de provider van de Recovery Services-service in de portal door [dit artikel te volgen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal). 
- Voer deze cmdlet uit voor Power shell. Deze moet worden voltooid als ' geregistreerd '.

    ```powershell
    PS C:>  Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```



[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>De data bases ontdekken

1. Klik in de kluis in **aan**de slag op **back-up**. In **waar wordt uw workload uitgevoerd?** Selecteer **SAP Hana in azure VM**.
2. Klik op **detectie starten**. Hiermee initieert u de detectie van onbeveiligde virtuele Linux-machines in de kluis regio.

   - Na detectie worden onbeveiligde Vm's weer gegeven in de portal, vermeld op naam en resource groep.
   - Als een virtuele machine niet naar verwachting wordt vermeld, controleert u of er al een back-up is gemaakt in een kluis.
   - Meerdere virtuele machines kunnen dezelfde naam hebben, maar ze behoren tot verschillende resource groepen.

3. In **virtual machines selecteren**klikt u op de koppeling om het script te downloaden dat machtigingen biedt voor de Azure backup-service om toegang te krijgen tot de SAP Hana vm's voor database detectie
4. Voer het script uit op elke VM die als host fungeert voor SAP HANA-data bases waarvan u een back-up wilt maken.
5. Nadat u het script op de virtuele machines hebt uitgevoerd, selecteert u de virtuele machines in **virtual machines selecteren**. Klik vervolgens op **Discover db's**.
6. Azure Backup detecteert alle SAP HANA-data bases op de virtuele machine. Tijdens de detectie wordt de virtuele machine door Azure Backup geregistreerd bij de kluis en wordt een extensie op de VM geïnstalleerd. Er is geen agent geïnstalleerd op de data base.

    ![SAP HANA-data bases ontdekken](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Back-up configureren  

Schakel nu back-up in.

1. Klik in stap 2 op **back-up configureren**.
2. Selecteer in **items selecteren waarvan u een back**-up wilt maken de data bases die u wilt beveiligen > **OK**.
3. Kies in **back-upbeleid** > **back-upbeleid**, maak een nieuw back-upbeleid voor de data bases, in overeenstemming met de onderstaande instructies.
4. Nadat u het beleid hebt gemaakt, klikt u in het menu **back-up** op **back-up inschakelen**.
5. Volg de voortgang van de configuratie van de back-up in het gebied **meldingen** van de portal.

### <a name="create-a-backup-policy"></a>Maak een back-upbeleid

Een back-upbeleid definieert wanneer er back-ups worden gemaakt en hoe lang ze worden bewaard.

- Een beleid wordt gemaakt op kluisniveau.
- U kunt hetzelfde back-upbeleid gebruiken voor meerdere kluizen, maar u moet het back-upbeleid toepassen op elke kluis.

Geef de beleids instellingen als volgt op:

1. Geef bij **Beleidsnaam** een naam voor het nieuwe beleid op.
2. In **Beleid voor een volledige back-up** selecteert u een **back-upfrequentie** en kiest u **Dagelijks** of **Wekelijks**.
   - **Dagelijks**: Selecteer het uur en de tijd zone waarin de back-uptaak wordt gestart.
   
       - U moet een volledige back-up uitvoeren. U kunt deze optie niet uitschakelen.
       - Klik op **Volledige back-up** om het beleid te bekijken.
       - U kunt geen differentiële back-ups maken voor dagelijkse volledige back-ups.
       
   - **Wekelijkse**: Selecteer de dag van de week, het uur en de tijd zone waarin de back-uptaak wordt uitgevoerd.
3. Configureer in **Bewaar termijn**de Bewaar instellingen voor de volledige back-up.
    - Alle opties zijn standaard geselecteerd. Wis de limieten voor het Bewaar bereik die u niet wilt gebruiken en stel de beperkingen in die u doet.
    - De minimale Bewaar periode voor elk type back-up (volledig/differentieel/logboek) is zeven dagen.
    - Herstelpunten worden getagd voor retentie op basis van de bewaarperiode. Als u een dagelijkse volledige back-up selecteert, wordt slechts één volledige back-up per dag geactiveerd.
    - De back-up voor een specifieke dag wordt gelabeld en bewaard op basis van het wekelijkse Bewaar bereik en de instelling.
    - De maandelijkse en jaarlijkse bewaarperioden werken op soortgelijke wijze.

4. Klik in het menu **beleid voor volledige back-up** op **OK** om de instellingen te accepteren.
5. Selecteer **differentiële back-up** om een differentieel beleid toe te voegen.
6. In **Beleid voor een differentiële back-up** selecteert u **Inschakelen** om de frequentie- en bewaarinstellingen te openen.
    - U kunt maximaal één differentiële back-up per dag activeren.
    - Differentiële back-ups kunnen maximaal 180 dagen worden bewaard. Als dat voor u te kort is, moet u volledige back-ups gebruiken.

    > [!NOTE]
    > Incrementele back-ups worden momenteel niet ondersteund. 

7. Klik op **OK** om het beleid op te slaan en terug te gaan naar het hoofd menu van het **back-upbeleid** .
8. Selecteer **logboek back-up** om een transactionele logboek back-upbeleid toe te voegen.
    - Selecteer in **logboek back-up**de optie **inschakelen**.
    - Stel de besturings elementen voor de frequentie en de retentie in.

    > [!NOTE]
    > Logboek back-ups gaan alleen naar de stroom nadat een geslaagde volledige back-up is voltooid.

9. Klik op **OK** om het beleid op te slaan en terug te gaan naar het hoofd menu van het **back-upbeleid** .
10. Klik op **OK**nadat u klaar bent met het definiëren van het back-upbeleid.


## <a name="run-an-on-demand-backup"></a>Een back-up op aanvraag uitvoeren

Back-ups worden uitgevoerd volgens het beleids schema. U kunt als volgt een back-up op aanvraag uitvoeren:


1. Klik in het menu kluis op **back-** upitems.
2. Selecteer in **back-** upitems de virtuele machine waarop de SAP Hana-data base wordt uitgevoerd en klik vervolgens op **Nu back-up maken**.
3. In **Nu back-up**kunt u het besturings element kalender gebruiken om de laatste dag te selecteren dat het herstel punt moet worden bewaard. Klik vervolgens op **OK**.
4. De portal meldingen bewaken. U kunt de voortgang van de taak in het kluis dashboard controleren > **back-uptaken** > worden uitgevoerd. Afhankelijk van de grootte van de data base kan het maken van de eerste back-up enige tijd duren.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>SAP HANA Studio-back-up uitvoeren op een Data Base met Azure Backup ingeschakeld

Ga als volgt te werk als u een lokale back-up wilt maken (met behulp van HANA Studio) van een Data Base waarvan een back-up wordt gemaakt met Azure Backup:

1. Wacht totdat alle volledige of logboek back-ups voor de Data Base zijn voltooid. Controleer de status in SAP HANA Studio.
2. Schakel logboek back-ups uit en stel de back-catalogus in op het bestands systeem voor de relevante data base.
3. U doet dit door te dubbel klikken op **SystemDB** > -**configuratie** > **database** > **filter (logboek)** .
4. Stel **enable_auto_log_backup** in op **Nee**.
5. Stel **log_backup_using_backint** in op **False**.
6. Maak een ad-hoc volledige back-up van de data base.
7. Wacht tot de volledige back-up en catalogus back-up zijn voltooid.
8. De vorige instellingen herstellen voor Azure:
    - Stel **enable_auto_log_backup** in op **Ja**.
    - Stel **log_backup_using_backint** in op **True**.



## <a name="next-steps"></a>Volgende stappen

[Meer informatie over](backup-azure-sap-hana-database-troubleshoot.md) het oplossen van veelvoorkomende fouten bij het gebruik van SAP Hana back-up in azure vm's.
[Meer informatie over](backup-azure-arm-vms-prepare.md) het maken van back-ups van virtuele Azure-machines.
