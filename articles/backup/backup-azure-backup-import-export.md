---
title: Offline back-up van Seed met de Azure Import/Export-service
description: Meer informatie over hoe u Azure Backup gebruiken om gegevens uit het netwerk te verzenden met behulp van de Azure Import/Export-service. In dit artikel wordt het offline zaaien van de oorspronkelijke back-upgegevens uitgelegd met behulp van de Azure Import/Export-service.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 1359616e074f36a1324a418d5b2c889076ced52d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206755"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Offline back-upwerkstroom in Azure Backup

Azure Backup heeft verschillende ingebouwde efficiëntieverbeteringen die netwerk- en opslagkosten besparen tijdens de eerste volledige back-ups van gegevens naar Azure. Initiële volledige back-ups brengen doorgaans grote hoeveelheden gegevens over en vereisen meer netwerkbandbreedte in vergelijking met latere back-ups die alleen de delta's/incrementele gegevens overbrengen. Tijdens het proces van offline zaaien kan Azure Backup schijven gebruiken om de offline back-upgegevens naar Azure te uploaden.

Het offline seeding-proces voor Azure Backup is nauw geïntegreerd met de [Azure Import/Export-service.](../storage/common/storage-import-export-service.md) U deze service gebruiken om de eerste back-upgegevens over te zetten naar Azure met behulp van schijven. Als u terabytes (TB's) hebt van de eerste back-upgegevens die moeten worden overgedragen via een netwerk met hoge latentie en lage bandbreedte, u de offline-seeding-workflow gebruiken om de eerste back-upkopie op een of meer harde schijven naar een Azure-datacenter te verzenden. De volgende afbeelding geeft een overzicht van de stappen in de werkstroom.

  ![Overzicht van offline importworkflowproces](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Het offline back-upproces omvat de volgende stappen:

1. In plaats van de back-upgegevens via het netwerk te verzenden, schrijft u de back-upgegevens naar een faseringslocatie.
1. Gebruik het *azureOfflineBackupDiskPrep-hulpprogramma* om de gegevens op de faseringslocatie naar een of meer SATA-schijven te schrijven.
1. Als onderdeel van het voorbereidende werk maakt het *AzureOfflineBackupDiskPrep-hulpprogramma* een Azure-importtaak. Verzend de SATA-stations naar het dichtstbijzijnde Azure-datacenter en verwijs naar de importtaak om de activiteiten met elkaar te verbinden.
1. In het Azure-datacenter worden de gegevens op de schijven gekopieerd naar een Azure-opslagaccount.
1. Azure Backup kopieert de back-upgegevens van het opslagaccount naar de kluis Recovery Services en incrementele back-ups zijn gepland.

## <a name="supported-configurations"></a>Ondersteunde configuraties

De volgende Azure Backup-functies of -workloads ondersteunen het gebruik van offline back-up voor:

> [!div class="checklist"]
>
> * Back-up van bestanden en mappen met de Microsoft Azure Recovery Services (MARS)-agent, ook wel Azure Backup Agent genoemd.
> * Back-up van alle workloads en bestanden met System Center Data Protection Manager (DPM).
> * Back-up van alle workloads en bestanden met Microsoft Azure Backup Server.
 
   > [!NOTE]
   > Offline back-upwordt niet ondersteund voor back-ups van systeemstatus die worden uitgevoerd met de Azure Backup Agent.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Vereisten

  > [!NOTE]
  > De volgende vereisten en werkstroom zijn alleen van toepassing op offline back-ups van bestanden en mappen met behulp van de [nieuwste Azure Recovery Services Agent.](https://aka.ms/azurebackup_agent) Zie [Offline back-ups voor DPM en](backup-azure-backup-server-import-export-.md)Azure Backup Server voor het uitvoeren van offline back-ups voor workloads met Behulp van System Center DPM of Azure Backup Server.

Voer de volgende vereisten in voordat u de offline back-upworkflow start:

* Een [kluis van Recovery Services maken.](backup-azure-recovery-services-vault-overview.md) Als u een kluis wilt maken, volgt u de stappen in [Een kluis voor Herstelservices maken.](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Controleer of alleen de [nieuwste versie van de Azure Backup Agent](https://aka.ms/azurebackup_agent) is geïnstalleerd op de Windows Server- of Windows-client, indien van toepassing, en dat de computer is geregistreerd bij de kluis Herstelservices.
* Azure PowerShell 3.7.0 is vereist op de computer waarop de Azure Backup Agent wordt uitgevoerd. Download en [installeer de 3.7.0-versie van Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* Controleer op de computer waarop de Azure Backup Agent wordt uitgevoerd, of Microsoft Edge of Internet Explorer 11 is geïnstalleerd en JavaScript is ingeschakeld.
* Maak een Azure-opslagaccount in hetzelfde abonnement als de kluis Recovery Services.
* Zorg ervoor dat u over de [benodigde machtigingen beschikt](../active-directory/develop/howto-create-service-principal-portal.md) om de Azure Active Directory-toepassing te maken. De offline back-upwerkstroom maakt een Azure Active Directory-toepassing in het abonnement dat is gekoppeld aan het Azure-opslagaccount. Het doel van de toepassing is om Azure Backup te voorzien van veilige en scoped toegang tot de Azure Import/Export-service, die vereist is voor de offline back-upworkflow.
* Registreer de *Microsoft.ImportExport-bronprovider* met het abonnement dat het Azure-opslagaccount bevat. Ga als u de resourceprovider registreren:
    1. Selecteer **Abonnementen**in het hoofdmenu .
    1. Als u bent geabonneerd op meerdere abonnementen, selecteert u het abonnement dat u wilt gebruiken voor de offline back-up. Als u slechts één abonnement gebruikt, wordt uw abonnement weergegeven.
    1. Selecteer **resourceproviders** in het abonnementsmenu om de lijst met providers weer te geven.
    1. Scrol in de lijst met providers naar *Microsoft.ImportExport*. Als de **status** **niet is geregistreerd,** selecteert u **Register**.

        ![De resourceprovider registreren](./media/backup-azure-backup-import-export/registerimportexport.png)

* Er wordt een faseringslocatie gemaakt, die een netwerkshare of een extra schijf op de computer kan zijn, intern of extern, met voldoende schijfruimte om uw eerste kopie vast te houden. Als u bijvoorbeeld een back-up wilt maken van een bestandsserver van 500 GB, moet u ervoor zorgen dat het faseringsgebied ten minste 500 GB bedraagt. (Een kleinere hoeveelheid wordt gebruikt als gevolg van compressie.)
* Wanneer u schijven naar Azure verzendt, gebruikt u alleen interne harde schijven van 2,5-inch SSD of 2,5-inch of 3,5-inch SATA II/III. U harde schijven tot 10 TB gebruiken. Controleer de [servicedocumentatie voor Azure Import/Export](../storage/common/storage-import-export-requirements.md#supported-hardware) voor de nieuwste set schijven die de service ondersteunt.
* De SATA-stations moeten zijn aangesloten op een computer (aangeduid als een *kopieercomputer)* van waaruit de kopie van back-upgegevens van de halteplaats naar de SATA-stations wordt uitgevoerd. Controleer of BitLocker is ingeschakeld op de kopieercomputer.

## <a name="workflow"></a>Werkstroom

In deze sectie wordt de offline back-upwerkstroom beschreven, zodat uw gegevens kunnen worden geleverd aan een Azure-datacenter en kunnen worden geüpload naar Azure Storage. Als u vragen hebt over de importservice of enig aspect van het proces, raadpleegt u de [overzichtsdocumentatie voor Azure Import/Export.](../storage/common/storage-import-export-service.md)

## <a name="initiate-offline-backup"></a>Offline back-up starten

1. Wanneer u een back-up plant op de Recovery Services Agent, ziet u deze pagina.

    ![Pagina Importeren](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

1. Selecteer de optie **Overdracht met mijn eigen schijven**.

    > [!NOTE]
    > Gebruik de optie Azure Data Box om de eerste back-upgegevens offline over te zetten. Deze optie bespaart de moeite die nodig is om uw eigen Azure-compatibele schijven aan te schaffen. Het levert Microsoft-eigen, veilige en tamperproof Azure Data Box-apparaten waar naar back-upgegevens rechtstreeks kunnen worden geschreven door de Recovery Services Agent.

1. Selecteer **Volgende**en vul de vakken zorgvuldig in.

    ![Voer uw schijfgegevens in](./media/backup-azure-backup-import-export/your-disk-details.png)

   De vakken die u invult zijn:

    * **Staging Locatie:** de tijdelijke opslaglocatie waarnaar de eerste back-up kopie is geschreven. De faseringslocatie bevindt zich mogelijk op een netwerkshare of een lokale computer. Als de kopieercomputer en de broncomputer verschillend zijn, geeft u het volledige netwerkpad van de faseringslocatie op.
    * **Opslagaccount Azure Resource Manager:** de naam van het resourcebeheer-opslagaccount (v1 voor algemeen gebruik of v2 voor algemeen gebruik) in een Azure-abonnement.
    * **Azure Storage Container:** de naam van de blob met doelopslag in het Azure-opslagaccount waar de back-upgegevens worden geïmporteerd voordat ze worden gekopieerd naar de kluis Recovery Services.
    * **Azure Subscription ID**: De id voor het Azure-abonnement waarbij het Azure-opslagaccount wordt gemaakt.
    * **Naam azure importtaak:** de unieke naam waarmee de Azure Import/Export-service en Azure Backup de overdracht bijhouden van gegevens die op schijven naar Azure worden verzonden.
  
   Nadat u de vakken hebt ingevuld, selecteert u **Volgende**. Sla de **faseringslocatie** en de azure **import jobname-gegevens** op. Het is nodig om de schijven voor te bereiden.

1. Meld u aan bij uw Azure-abonnement wanneer u daarom wordt gevraagd. U moet zich aanmelden zodat Azure Backup de Azure Active Directory-toepassing kan maken. Voer de vereiste machtigingen in voor toegang tot de Azure Import/Export-service.

    ![Aanmeldingspagina voor Azure-abonnementen](./media/backup-azure-backup-import-export/azure-login.png)

1. Voltooi de werkstroom. Selecteer **nu back-up maken**op de Azure Backup Agent-console .

    ![Maak nu een back-up](./media/backup-azure-backup-import-export/backupnow.png)

1. Selecteer op de pagina **Bevestiging** van de wizard de optie **Back-up maken**. De eerste back-up wordt naar het faseringsgebied geschreven als onderdeel van de installatie.

   ![Bevestig dat je nu een back-up maken](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Nadat de bewerking is voltooid, is de faseringslocatie klaar voor de voorbereiding van de schijf.

   ![Wizard Back-up maken, pagina](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>SATA-stations voorbereiden en verzenden naar Azure

Het *AzureOfflineBackupDiskPrep-hulpprogramma* bereidt de SATA-schijven voor die naar het dichtstbijzijnde Azure-datacenter worden verzonden. Dit hulpprogramma is beschikbaar in de installatiemap azure backup agent in het volgende pad:

```*\Microsoft Azure Recovery Services Agent\Utils\\*```

1. Ga naar de map en kopieer de map *AzureOfflineBackupDiskPrep* naar een andere computer waar de SATA-stations zijn aangesloten. Zorg er op de computer met de aangesloten SATA-stations voor dat:

   * De kopieercomputer heeft toegang tot de faseringslocatie voor de offlinezaaiende werkstroom met behulp van hetzelfde netwerkpad dat in de werkstroom is opgegeven in de sectie Offline back-up initiëren.
   * BitLocker is ingeschakeld op de kopieercomputer.
   * Azure PowerShell 3.7.0 is geïnstalleerd.
   * De nieuwste compatibele browsers (Microsoft Edge of Internet Explorer 11) zijn geïnstalleerd en JavaScript is ingeschakeld.
   * De kopieercomputer heeft toegang tot de Azure-portal. Indien nodig kan de kopieercomputer hetzelfde zijn als de broncomputer.

     > [!IMPORTANT]
     > Als de broncomputer een virtuele machine is, moet de kopieercomputer een andere fysieke server of clientmachine zijn dan de broncomputer.

1. Open een opdrachtprompt met verhoogde bevoegdheid op de kopieercomputer met de hulpprogrammamap *AzureOfflineBackupDiskPrep* als de huidige map. Voer de volgende opdracht uit:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parameter | Beschrijving |
    | --- | --- |
    | s:&lt;*Locatiepad met fasering*&gt; |Deze verplichte invoer wordt gebruikt om het pad naar de faseringslocatie te geven die u in de werkstroom hebt ingevoerd in de sectie Offline back-up initiëren. |
    | p:&lt;*Path to PublishSettingsFile*&gt; |Deze optionele invoer wordt gebruikt om het pad te bieden naar het Azure-publicatie-instellingenbestand dat u hebt ingevoerd in de werkstroom in de sectie Offline back-up starten. |

    Wanneer u de opdracht uitvoert, vraagt het hulpprogramma de selectie van de Azure-importtaak aan die overeenkomt met de stations die moeten worden voorbereid. Als slechts één importtaak is gekoppeld aan de opgegeven faseringslocatie, ziet u een pagina als deze.

    ![Invoer van azure-schijfvoorbereidingsgereedschappen](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

1. Voer de stationsletter in zonder de slepende dubbele punt voor de gemonteerde schijf die u wilt voorbereiden op overdracht naar Azure.
1. Geef de opmaak van het station op wanneer daarom wordt gevraagd een bevestiging.
1. U wordt gevraagd zich aan te melden bij uw Azure-abonnement. Voer uw referenties in.

    ![Aanmelding voor Azure-abonnementen](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Het hulpprogramma begint vervolgens de schijf voor te bereiden en de back-upgegevens te kopiëren. Mogelijk moet u extra schijven toevoegen wanneer het hulpprogramma daarom vraagt als de meegeleverde schijf niet voldoende ruimte heeft voor de back-upgegevens. <br/>

    Aan het einde van de succesvolle uitvoering van het gereedschap biedt de opdrachtprompt drie stukjes informatie:

   1. Een of meer schijven die u hebt geleverd, zijn voorbereid voor verzending naar Azure.
   1. U ontvangt een bevestiging dat uw importtaak is gemaakt. De importtaak gebruikt de door u opgegeven naam.
   1. De tool geeft het verzendadres voor het Azure-datacenter weer.

      ![Azure-schijfvoorbereiding voltooid](./media/backup-azure-backup-import-export/console2.png)<br/>

1. Aan het einde van de opdrachtuitvoering u de verzendgegevens bijwerken.

1. Verzend de schijven naar het adres dat het gereedschap heeft verstrekt. Bewaar het volgnummer voor toekomstige referentie.

   > [!IMPORTANT]
   > Geen twee Azure-importtaken kunnen hetzelfde trackingnummer hebben. Zorg ervoor dat schijven die zijn opgesteld door het hulpprogramma onder één Azure-importtaak samen worden verzonden in één pakket en dat er één uniek trackingnummer voor het pakket is. Combineer geen schijven die zijn voorbereid als onderdeel van afzonderlijke Azure-importtaken in één pakket.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Verzendgegevens bijwerken voor de azure-importtaak

Met de volgende procedure worden de verzendgegevens van de Azure-importtaak bijgewerkt. Deze informatie bevat details over:

* De naam van de provider die de schijven aan Azure levert.
* Verzendgegevens voor uw schijven retourneren.

1. Meld u aan bij uw Azure-abonnement.
1. Selecteer **Alle services**in het hoofdmenu. Voer in het dialoogvenster **Alle services** **importeren**in. Wanneer u **Taken importeren/exporteren**ziet, selecteert u deze.

    ![Verzendgegevens invoeren](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Het menu **Taken importeren/exporteren** wordt geopend en de lijst met alle import/exporttaken in het geselecteerde abonnement wordt weergegeven.

1. Als u meerdere abonnementen hebt, selecteert u het abonnement dat wordt gebruikt om de back-upgegevens te importeren. Selecteer vervolgens de nieuw gemaakte importtaak om de details te openen.

    ![Verzendgegevens bekijken](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

1. Selecteer **verzendgegevens beheren**in het menu **Instellingen** voor de taak importeren . Voer de verzendgegevens van de retour in.

    ![Verzendgegevens opslaan](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

1. Wanneer u het volgnummer van uw verzender hebt, selecteert u de banner op de pagina Overzicht van de azure-importtaak en voert u de volgende gegevens in.

   > [!IMPORTANT]
   > Zorg dat de informatie voor de vervoerder en het traceringsnummer binnen twee weken van het maken van de Azure-importtaak zijn bijgewerkt. Als u deze informatie niet binnen twee weken verifieert, kan de taak worden verwijderd en worden stations niet verwerkt.

   ![Waarschuwing voor het bijwerken van informatie](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Gegevens van de vervoerder en volgnummer](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Tijd om de schijven te verwerken

De hoeveelheid tijd die nodig is om een Azure-importtaak te verwerken, varieert. De procestijd is gebaseerd op factoren zoals verzendtijd, taaktype, type en grootte van de gegevens die worden gekopieerd en de grootte van de geleverde schijven. De Azure Import/Export-service heeft geen SLA. Nadat schijven zijn ontvangen, streeft de service ernaar om de back-upgegevenskopie naar uw Azure-opslagaccount binnen 7 tot 10 dagen in te vullen.

### <a name="monitor-azure-import-job-status"></a>Status van Azure-importtaak controleren

U de status van uw importtaak controleren vanuit de Azure-portal. Ga naar de pagina **Taken importeren/exporteren** en selecteer uw taak. Zie [Wat is de azure import/exportservice?](../storage/common/storage-import-export-service.md).

### <a name="finish-the-workflow"></a>De werkstroom voltooien

Nadat de importtaak is voltooid, zijn de eerste back-upgegevens beschikbaar in uw opslagaccount. Op het moment van de volgende geplande back-up kopieert Azure Backup de inhoud van de gegevens van het opslagaccount naar de kluis Van Herstelservices.

   ![Gegevens kopiëren naar de kluis Recovery Services](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Op het moment van de volgende geplande back-up voert Azure Backup een incrementele back-up uit.

### <a name="clean-up-resources"></a>Resources opschonen

Nadat de eerste back-up is voltooid, u de gegevens die zijn geïmporteerd naar de Azure Storage-container en de back-upgegevens op de faseringslocatie veilig verwijderen.

## <a name="next-steps"></a>Volgende stappen

* Zie [De service Microsoft Azure Import/Export gebruiken om gegevens over te dragen naar Blob-opslag voor](../storage/common/storage-import-export-service.md)vragen over de azure import/exportservice.
