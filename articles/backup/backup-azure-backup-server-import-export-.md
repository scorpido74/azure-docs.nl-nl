---
title: Offline back-ups voor DPM en Azure Backup Server
description: Met Azure Backup u gegevens uit het netwerk verzenden met behulp van de Azure Import/Export-service. In dit artikel wordt de offline back-upwerkstroom voor DPM en Azure Backup Server uitgelegd.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 080b0bc53b2058bd186e90f354b8f5bcda510414
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197045"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>Offline back-upwerkstroom voor DPM en Azure Backup Server

Azure Backup heeft verschillende ingebouwde efficiëntieverbeteringen die netwerk- en opslagkosten besparen tijdens de eerste volledige back-ups van gegevens naar Azure. Initiële volledige back-ups brengen doorgaans grote hoeveelheden gegevens over en vereisen meer netwerkbandbreedte in vergelijking met latere back-ups die alleen de delta's/incrementele gegevens overbrengen. Azure Backup comprimeert de eerste back-ups. Tijdens het proces van offline zaaien kan Azure Backup schijven gebruiken om de gecomprimeerde initiële back-upgegevens offline naar Azure te uploaden.

Het offline-seedingproces van Azure Backup is nauw geïntegreerd met de [Azure Import/Export-service.](../storage/common/storage-import-export-service.md) U deze service gebruiken om gegevens over te dragen aan Azure met behulp van schijven. Als u terabytes (TB's) hebt van de eerste back-upgegevens die moeten worden overgedragen via een netwerk met hoge latentie en lage bandbreedte, u de offline-seeding-workflow gebruiken om de eerste back-upkopie op een of meer harde schijven naar een Azure-datacenter te verzenden. In dit artikel vindt u een overzicht en verdere stappen die deze werkstroom voor System Center Data Protection Manager (DPM) en Microsoft Azure Backup Server (MABS) voltooien.

> [!NOTE]
> Het proces van offline back-up voor de Microsoft Azure Recovery Services (MARS) Agent onderscheidt zich van DPM en MABS. Zie [Offline back-upwerkstroom in Azure Backup](backup-azure-backup-import-export.md)voor informatie over het gebruik van offline back-ups met de MARS-agent. Offline back-upwordt niet ondersteund voor back-ups van systeemstatus die worden uitgevoerd met de Azure Backup Agent.
>

## <a name="overview"></a>Overzicht

Met de offline-seeding-mogelijkheden van Azure Backup en de Azure Import/Export-service is het eenvoudig om de gegevens offline naar Azure te uploaden met behulp van schijven. Het offline back-upproces omvat de volgende stappen:

> [!div class="checklist"]
>
> * De back-upgegevens worden naar een faseringslocatie geschreven in plaats van via het netwerk te worden verzonden.
> * De gegevens op de faseringslocatie worden vervolgens naar een of meer SATA-schijven geschreven met behulp van het *AzureOfflineBackupDiskPrep-hulpprogramma.*
> * Een Azure-importtaak wordt automatisch gemaakt door het hulpprogramma.
> * De SATA-stations worden vervolgens verzonden naar het dichtstbijzijnde Azure-datacenter.
> * Nadat de upload van de back-upgegevens naar Azure is voltooid, kopieert Azure Backup de back-upgegevens naar de back-upkluis en worden de incrementele back-ups gepland.

## <a name="supported-configurations"></a>Ondersteunde configuraties

Offline back-up wordt ondersteund voor alle implementatiemodellen van Azure Backup die back-ups maken van gegevens van on-premises naar de Microsoft-cloud. Deze modellen omvatten:

> [!div class="checklist"]
>
> * Back-up van bestanden en mappen met de MARS-agent of de Azure Backup Agent.
> * Back-up van alle workloads en bestanden met DPM.
> * Back-up van alle workloads en bestanden met MABS.

## <a name="prerequisites"></a>Vereisten

Controleer of aan de volgende vereisten is voldaan voordat u de offline back-upworkflow start:

* Er is [een kluis van Recovery Services](backup-azure-recovery-services-vault-overview.md) gemaakt. Als u er een wilt maken, volgt u de stappen in [Een kluis van Herstelservices maken](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Een Azure Backup Agent of MABS of DPM is geïnstalleerd op Windows Server of een Windows-client, indien van toepassing, en de computer is geregistreerd bij de kluis Recovery Services. Controleer of alleen de [nieuwste versie van Azure Backup](https://go.microsoft.com/fwlink/?linkid=229525) wordt gebruikt.
* [Download het bestand met Azure-publicatie-instellingen](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) op de computer van waaruit u een back-up van uw gegevens wilt maken. Het abonnement van waaruit u het publicatie-instellingenbestand downloadt, kan afwijken van het abonnement dat de kluis Recovery Services bevat. Als uw abonnement zich op soevereine Azure-clouds bevindt, gebruikt u de volgende koppelingen om het bestand met Azure-publicatie-instellingen te downloaden.

    | Soevereine wolkenregio | Bestandskoppeling voor Azure-publicatie-instellingen |
    | --- | --- |
    | Verenigde Staten | [Koppeling](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | China | [Koppeling](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Er is een Azure-opslagaccount met het implementatiemodel Resource Manager gemaakt in het abonnement waarvan u het publicatie-instellingenbestand hebt gedownload.

  ![Een opslagaccount maken met de ontwikkeling van Resource Manager](./media/backup-azure-backup-import-export/storage-account-resource-manager.png)

* Er wordt een faseringslocatie gemaakt, die een netwerkshare of een extra schijf op de computer kan zijn, intern of extern, met voldoende schijfruimte om uw eerste kopie vast te houden. Als u bijvoorbeeld een back-up wilt maken van een bestandsserver van 500 GB, moet u ervoor zorgen dat het faseringsgebied ten minste 500 GB bedraagt. (Een kleinere hoeveelheid wordt gebruikt als gevolg van compressie.)
* Voor schijven die naar Azure worden verzonden, moet u ervoor zorgen dat alleen interne harde schijven van 2,5-inch SSD of 2,5-inch of 3,5-inch SATA II/III worden gebruikt. U harde schijven tot 10 TB gebruiken. Controleer de [servicedocumentatie voor Azure Import/Export](../storage/common/storage-import-export-requirements.md#supported-hardware) voor de nieuwste set schijven die de service ondersteunt.
* De SATA-stations moeten zijn aangesloten op een computer (aangeduid als een *kopieercomputer)* van waaruit de kopie van back-upgegevens van de halteplaats naar de SATA-stations wordt uitgevoerd. Controleer of BitLocker is ingeschakeld op de kopieercomputer.

## <a name="prepare-the-server-for-the-offline-backup-process"></a>De server voorbereiden op het offline back-upproces

>[!NOTE]
> Als u de vermelde hulpprogramma's, zoals *AzureOfflineBackupCertGen.exe,* niet vinden in AskAzureBackupTeam@microsoft.com uw installatie van de MARS-agent, schrijft u naar om toegang tot deze hulpprogramma's te krijgen.

* Open een opdrachtprompt met verhoogde bevoegdheid op de server en voer de volgende opdracht uit:

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    Het hulpprogramma maakt een Azure Offline Backup Active Directory Application als deze niet bestaat.

    Als er al een toepassing bestaat, vraagt deze uitvoerbare u om het certificaat handmatig te uploaden naar de toepassing in de tenant. Volg de stappen in [deze sectie](#manually-upload-an-offline-backup-certificate) om het certificaat handmatig naar de toepassing te uploaden.

* Het hulpprogramma *AzureOfflineBackup.exe* genereert een *Bestand OfflineApplicationParams.xml.* Kopieer dit bestand naar de server met MABS of DPM.
* Installeer de [nieuwste MARS-agent](https://aka.ms/azurebackup_agent) op het DPM-exemplaar of de Azure Backup-server.
* Registreer de server op Azure.
* Voer de volgende opdracht uit:

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname configured with Azure Data Box>
    ```

* Met de vorige `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml`opdracht wordt het bestand gemaakt.

## <a name="manually-upload-an-offline-backup-certificate"></a>Een offline back-upcertificaat handmatig uploaden

Volg deze stappen om het offline back-upcertificaat handmatig te uploaden naar een eerder gemaakte Azure Active Directory-toepassing die is bedoeld voor offline back-up.

1. Meld u aan bij Azure Portal.
1. Ga naar **Azure Active Directory** > **App-registraties**.
1. Zoek op het tabblad **Bezeten toepassingen** een `AzureOfflineBackup _<Azure User Id`toepassing met de weergavenaamindeling .

    ![Toepassing zoeken op tabblad Eigen toepassingen](./media/backup-azure-backup-import-export/owned-applications.png)

1. Selecteer de toepassing. Ga **onder Beheren** in het linkerdeelvenster naar Certificaten & **geheimen**.
1. Controleer op reeds bestaande certificaten of openbare sleutels. Als die er zijn, u de toepassing veilig verwijderen door de knop **Verwijderen** te selecteren op de **pagina Overzicht** van de toepassing. Vervolgens u de stappen opnieuw proberen om de server voor te [bereiden op het offline back-upproces](#prepare-the-server-for-the-offline-backup-process) en de volgende stappen overslaan. Anders blijft u deze stappen volgen vanaf de DPM-instantie of Azure Backup-server waar u offline back-up wilt configureren.
1. Selecteer het tabblad **Persoonlijke toepassing Computercertificaat beheren.** > **Personal** `CB_AzureADCertforOfflineSeeding_<ResourceId>`Zoek naar het certificaat met de naam .
1. Selecteer het certificaat, klik met de rechtermuisknop op **Alle taken**en selecteer **Vervolgens Exporteren**zonder privésleutel in de .cer-indeling.
1. Ga naar de Azure offline back-uptoepassing in de Azure-portal.
1. Selecteer **Certificaten beheren** > **& geheimen** > **Uploadcertificaat**. Upload het certificaat dat in de vorige stap is geëxporteerd.

    ![Het certificaat uploaden](./media/backup-azure-backup-import-export/upload-certificate.png)

1. Open het register op de server door **regedit** in het run-venster in te voeren.
1. Ga naar de *registerinvoercomputer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider*.
1. Klik met de rechtermuisknop op **CloudBackupProvider**en `AzureADAppCertThumbprint_<Azure User Id>`voeg een nieuwe tekenreekswaarde toe met de naam .

    >[!NOTE]
    > Ga als volgt te werk om de Azure-gebruikers-id te zoeken:
    >
    >* Voer de `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as appears in the portal”` opdracht uit vanuit de PowerShell met Azure verbonden.
    >* Ga naar het `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;`registerpad .

1. Klik met de rechtermuisknop op de tekenreeks die in de vorige stap is toegevoegd en selecteer **Wijzigen**. Geef in de waarde de duimafdruk op van het certificaat dat u in stap 7 hebt geëxporteerd. Selecteer vervolgens **OK**.
1. Dubbelklik op het certificaat om de waarde van de duimafdruk te krijgen. Selecteer het tabblad **Details** en schuif omlaag totdat u het veld duimafdruk ziet. Selecteer **Duimafdruk**en kopieer de waarde.

    ![Waarde kopiëren van het veld duimafdruk](./media/backup-azure-backup-import-export/thumbprint-field.png)

1. Ga verder naar de sectie [Werkstroom](#workflow) om verder te gaan met het offline back-upproces.

## <a name="workflow"></a>Werkstroom

Met de informatie in deze sectie u de offline back-upwerkstroom voltooien, zodat uw gegevens kunnen worden geleverd aan een Azure-datacenter en kunnen worden geüpload naar Azure Storage. Als u vragen hebt over de importservice of enig aspect van het proces, [raadpleegt](../storage/common/storage-import-export-service.md) u de documentatie van het overzicht van de service importeren waarnaar eerder wordt verwezen.

### <a name="initiate-offline-backup"></a>Offline back-up starten

1. Wanneer u een back-up plant, ziet u de volgende pagina in Windows Server, een Windows-client of DPM.

    ![Pagina Importeren](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Hier is de bijbehorende pagina in DPM. <br/>
    
    ![Importpagina DPM en Azure Backup Server](./media/backup-azure-backup-import-export/dpmoffline.png)

    De vakken die u invult zijn:

   * **Staging Locatie:** de tijdelijke opslaglocatie waarnaar de eerste back-up kopie is geschreven. De faseringslocatie bevindt zich mogelijk op een netwerkshare of een lokale computer. Als de kopieercomputer en de broncomputer verschillend zijn, geeft u het volledige netwerkpad van de faseringslocatie op.
   * **Azure-publicatie-instellingen:** het lokale pad naar het publicatie-instellingenbestand.
   * **Naam azure importtaak:** de unieke naam waarmee de Azure Import/Export-service en Azure Backup de overdracht bijhouden van gegevens die op schijven naar Azure worden verzonden.
   * **Azure Subscription ID**: De Azure-abonnements-ID voor het abonnement van waaruit u het Azure-publicatie-instellingenbestand hebt gedownload.
   * **Azure Storage-account:** de naam van het opslagaccount in het Azure-abonnement dat is gekoppeld aan het Azure-publicatie-instellingenbestand.
   * **Azure Storage Container:** de naam van de blob met doelopslag in het Azure-opslagaccount waar de back-upgegevens worden geïmporteerd.

   Sla de **tijdelijke locatie en** de azure import **jobname-gegevens** op die u hebt opgegeven. Het is nodig om de schijven voor te bereiden.

1. Voltooi de werkstroom. Als u de offline back-upwilt starten, selecteert u **Nu back-up** maken op de beheerconsole azure backup agent. De eerste back-up wordt naar het faseringsgebied geschreven als onderdeel van deze stap.

    ![Back-up nu](./media/backup-azure-backup-import-export/backupnow.png)

    Als u de bijbehorende werkstroom in DPM of Azure Backup Server wilt voltooien, klikt u met de rechtermuisknop op de **beveiligingsgroep**. Selecteer de optie **Herstelpunt maken.** Selecteer vervolgens de optie **Onlinebeveiliging.**

    ![DPM en MABS maken nu een back-up](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Nadat de bewerking is voltooid, is de faseringslocatie klaar voor de voorbereiding van de schijf.

    ![Voortgang van back-ups](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>SATA-stations voorbereiden en verzenden naar Azure

Het *AzureOfflineBackupDiskPrep-hulpprogramma* wordt gebruikt om de SATA-schijven voor te bereiden die naar het dichtstbijzijnde Azure-datacenter worden verzonden. Dit hulpprogramma is beschikbaar in de installatiemap van de Recovery Services Agent in het volgende pad:

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. Ga naar de map en kopieer de map *AzureOfflineBackupDiskPrep* naar een kopieercomputer waarop de sata-stations zijn aangesloten. Zorg ervoor dat:

   * De kopieercomputer heeft toegang tot de faseringslocatie voor de offlinezaaiende werkstroom met behulp van hetzelfde netwerkpad dat in de werkstroom is opgegeven in de sectie Offline back-up initiëren.
   * BitLocker is ingeschakeld op de kopieercomputer.
   * De kopieercomputer heeft toegang tot de Azure-portal. Indien nodig kan de kopieercomputer hetzelfde zijn als de broncomputer.

     > [!IMPORTANT]
     > Als de broncomputer een virtuele machine is, is het verplicht om een andere fysieke server of clientmachine als kopieercomputer te gebruiken.

1. Open een opdrachtprompt met verhoogde bevoegdheid op de kopieercomputer met de hulpprogrammamap *AzureOfflineBackupDiskPrep* als de huidige map. Voer de volgende opdracht uit:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parameter | Beschrijving |
    | --- | --- |
    | s:&lt;*Locatiepad met fasering*&gt; |Deze verplichte invoer wordt gebruikt om het pad naar de faseringslocatie te geven die u in de werkstroom hebt ingevoerd in de sectie Offline back-up initiëren. |
    | p:&lt;*Path to PublishSettingsFile*&gt; |Deze optionele invoer wordt gebruikt om het pad te bieden naar het Azure-publicatie-instellingenbestand dat u hebt ingevoerd in de werkstroom in de sectie Offline back-up starten. |

    > [!NOTE]
    > De &lt;waarde Path to&gt; AzurePublishSettingFile is verplicht wanneer de kopieercomputer en de broncomputer verschillend zijn.
    >
    >

    Wanneer u de opdracht uitvoert, vraagt het hulpprogramma de selectie van de Azure-importtaak aan die overeenkomt met de stations die moeten worden voorbereid. Als slechts één importtaak is gekoppeld aan de opgegeven faseringslocatie, ziet u een pagina als deze.

    ![Invoer van azure-schijfvoorbereidingsgereedschappen](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>

1. Voer de stationsletter in zonder de slepende dubbele punt voor de gemonteerde schijf die u wilt voorbereiden op overdracht naar Azure. Geef desgevraagd een bevestiging voor de opmaak van het station.

    Het hulpprogramma begint vervolgens de schijf voor te bereiden en de back-upgegevens te kopiëren. Mogelijk moet u extra schijven toevoegen wanneer het hulpprogramma daarom vraagt als de meegeleverde schijf niet voldoende ruimte heeft voor de back-upgegevens. <br/>

    Nadat het hulpprogramma is voltooid, worden een of meer schijven die u hebt geleverd, voorbereid voor verzending naar Azure. Er wordt ook een importtaak gemaakt met de naam die u tijdens de werkstroom hebt opgegeven in de sectie Offline back-up initiëren, ook in Azure. Ten slotte geeft het hulpprogramma het verzendadres weer naar het Azure-datacenter waar de schijven moeten worden verzonden.

    ![Azure-schijfvoorbereiding voltooid](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

1. Aan het einde van de opdrachtuitvoering ziet u ook de optie om verzendgegevens bij te werken.

    ![Optie Verzendgegevens bijwerken](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

1. U de gegevens meteen invoeren. De tool begeleidt u door het proces dat een reeks ingangen omvat. Als u geen informatie hebt zoals het trackingnummer of andere details met betrekking tot verzending, u de sessie beëindigen. De stappen om de verzendgegevens later bij te werken, worden in dit artikel gegeven.

1. Verzend de schijven naar het adres dat het gereedschap heeft verstrekt. Bewaar het volgnummer voor toekomstige referentie.

   > [!IMPORTANT]
   > Geen twee Azure-importtaken kunnen hetzelfde trackingnummer hebben. Zorg ervoor dat schijven die zijn opgesteld door het hulpprogramma onder één Azure-importtaak samen worden verzonden in één pakket en dat er één uniek trackingnummer voor het pakket is. Combineer geen schijven die zijn voorbereid als onderdeel van verschillende Azure-importtaken in één pakket.

1. Wanneer u de trackingnummergegevens hebt, gaat u naar de broncomputer, die wacht op het voltooien van de importtaak. Voer de volgende opdracht uit in een opdrachtprompt met de hulpprogrammamap *AzureOfflineBackupDiskPrep* als de huidige map.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   U optioneel de volgende opdracht uitvoeren vanaf een andere computer, zoals de kopieercomputer, met de hulpprogrammamap *AzureOfflineBackupDiskPrep* als de huidige map.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parameter | Beschrijving |
    | --- | --- |
    | U: | Deze verplichte invoer wordt gebruikt om verzendgegevens bij te werken voor een Azure-importtaak. |
    | s:&lt;*Locatiepad met fasering*&gt; | Deze verplichte invoer wordt gebruikt wanneer de opdracht niet wordt uitgevoerd op de broncomputer. Het wordt gebruikt om het pad naar de faseringslocatie te bieden die u hebt ingevoerd in de werkstroom in de sectie Offline back-up initiëren. |
    | p:&lt;*Path to PublishSettingsFile*&gt; | Deze verplichte invoer wordt gebruikt wanneer de opdracht niet wordt uitgevoerd op de broncomputer. Het wordt gebruikt om het pad naar het Azure-publicatie-instellingenbestand te bieden dat u hebt ingevoerd in de werkstroom in de sectie Offline back-up initiëren. |

    Het hulpprogramma detecteert automatisch de importtaak waarop de broncomputer wacht of de importtaken die zijn gekoppeld aan de faseringslocatie wanneer de opdracht op een andere computer wordt uitgevoerd. Het biedt dan de mogelijkheid om verzendinformatie bij te werken via een reeks ingangen.

    ![Verzendgegevens invoeren](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

1. Nadat alle ingangen zijn verstrekt, bekijk de details zorgvuldig en bega de verzendinformatie die u verstrekt door het invoeren van **ja**.

    ![Verzendgegevens bekijken](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

1. Nadat de verzendgegevens zijn bijgewerkt, biedt het hulpprogramma een lokale locatie waar de verzendgegevens die u hebt ingevoerd, worden opgeslagen.

    ![Verzendgegevens opslaan](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT]
   > Zorg ervoor dat de stations het Azure-datacenter bereiken binnen twee weken na het verstrekken van de verzendgegevens met behulp van het *AzureOfflineBackupDiskPrep-hulpprogramma.* Als u dit niet doet, kunnen de schijven niet worden verwerkt. 

Nadat u de vorige stappen hebt voltooid, is het Azure-datacenter klaar om de stations te ontvangen en ze verder te verwerken om de back-upgegevens van de stations over te zetten naar het klassieke Azure-opslagaccount dat u hebt gemaakt.

### <a name="time-to-process-the-drives"></a>Tijd om de schijven te verwerken

De hoeveelheid tijd die nodig is om een Azure-importtaak te verwerken, varieert. De procestijd is afhankelijk van factoren zoals verzendtijd, taaktype, type en grootte van de gegevens die worden gekopieerd en de grootte van de geleverde schijven. De Azure Import/Export-service heeft geen SLA. Nadat schijven zijn ontvangen, streeft de service ernaar om de back-upgegevenskopie naar uw Azure-opslagaccount binnen 7 tot 10 dagen te voltooien. In de volgende sectie wordt beschreven hoe u de status van de Azure-importtaak controleren.

### <a name="monitor-azure-import-job-status"></a>Status van Azure-importtaak controleren

Terwijl uw schijven onderweg zijn of in het Azure-datacenter om naar het opslagaccount te worden gekopieerd, toont de Azure Backup Agent of DPM of de MABS-console op de broncomputer de volgende taakstatus voor uw geplande back-ups:

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Ga als u de status van de importtaak controleren:

1. Open een opdrachtprompt met verhoogde bevoegdheid op de broncomputer en voer de volgende opdracht uit:

     `AzureOfflineBackupDiskPrep.exe u:`

1. De uitvoer geeft de huidige status van de importtaak weer.

    ![Status van importtaak controleren](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Zie [De status van Azure Import/Export-taken weergeven](../storage/common/storage-import-export-view-drive-status.md)voor meer informatie over de verschillende statussen van de azure-importtaak.

### <a name="finish-the-workflow"></a>De werkstroom voltooien

Nadat de importtaak is voltooid, zijn de eerste back-upgegevens beschikbaar in uw opslagaccount. Op het moment van de volgende geplande back-up kopieert Azure Backup de inhoud van de gegevens van het opslagaccount naar de kluis Van Herstelservices.

   ![Gegevens kopiëren naar de kluis Recovery Services](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Op het moment van de volgende geplande back-up voert Azure Backup incrementele back-up uit via de eerste back-upkopie.

## <a name="next-steps"></a>Volgende stappen

* Zie [De service Microsoft Azure Import/Export gebruiken om gegevens over te dragen naar Blob-opslag voor](../storage/common/storage-import-export-service.md)vragen over de azure import/exportservice.
