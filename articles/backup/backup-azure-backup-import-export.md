---
title: Offline back-ups maken met de import/export-service
description: Meer informatie over hoe u met behulp van Azure Backup gegevens via het netwerk kunt verzenden met de Azure import/export-service. In dit artikel wordt uitgelegd hoe u de offline seeding van de eerste back-upgegevens maakt met behulp van de Azure import-export service.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 331d5528c8f124f4d43142ff7be4daa3169b0381
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173291"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Werkstroom voor offline back-ups maken in Azure Backup

Azure Backup heeft verschillende ingebouwde efficiency waarmee netwerk-en opslag kosten worden bespaard tijdens de eerste volledige back-ups van gegevens naar Azure. Bij eerste volledige back-ups worden grote hoeveel heden gegevens overgebracht en is er meer netwerk bandbreedte nodig in vergelijking met de volgende back-ups die alleen de Deltas/toenames overdragen. Dankzij het proces van offline seeding kan Azure Backup schijven gebruiken om de offline back-upgegevens te uploaden naar Azure.

De Azure Backup offline-seeding proces is nauw geïntegreerd met de [Azure import/export-service](../storage/common/storage-import-export-service.md) waarmee u de eerste back-upgegevens naar Azure kunt overdragen met behulp van schijven. Als u een TBs hebt van de eerste back-upgegevens die moeten worden overgedragen via een netwerk met een hoge latentie en een lage band breedte, kunt u de werk stroom voor offline seeding gebruiken om de eerste back-up op een of meer harde schijven naar een Azure-Data Center te verzenden. De volgende afbeelding bevat een overzicht van de stappen in de werk stroom.

  ![overzicht van werk stroom proces voor offline import](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Het offline back-upproces bestaat uit de volgende stappen:

1. In plaats van de back-upgegevens via het netwerk te verzenden, schrijft u de back-upgegevens naar een faserings locatie.
2. Gebruik het hulp programma AzureOfflineBackupDiskPrep om de gegevens op de faserings locatie te schrijven naar een of meer SATA-schijven.
3. Als onderdeel van de voorbereidende werkzaamheden maakt het hulp programma AzureOfflineBackupDiskPrep een Azure import-taak. Verzend de SATA-schijven naar het dichtstbijzijnde Azure-Data Center en Raadpleeg de import taak om de activiteiten te verbinden.
4. In het Azure-Data Center worden de gegevens op de schijven gekopieerd naar een Azure-opslag account.
5. Azure Backup kopieert de back-upgegevens van het opslag account naar de Recovery Services kluis en worden incrementele back-ups gepland.

## <a name="supported-configurations"></a>Ondersteunde configuraties

De volgende Azure Backup-functies of-workloads ondersteunen het gebruik van offline back-ups.

> [!div class="checklist"]
>
> * Back-ups van bestanden en mappen met de Microsoft Azure Recovery Services-agent (MARS), ook wel de Azure Backup-Agent genoemd.
> * Back-ups van alle werk belastingen en bestanden met System Center Data Protection Manager (SC DPM)
> * Back-ups maken van alle werk belastingen en bestanden met Microsoft Azure Backup Server
 
   > [!NOTE]
   > Offline back-ups worden niet ondersteund voor systeem status back-ups die zijn uitgevoerd met behulp van de Azure Backup-Agent.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Vereisten

  > [!NOTE]
  > De volgende vereisten en werk stroom zijn alleen van toepassing op offline back-ups van bestanden en mappen met behulp van de [nieuwste Mars-agent](https://aka.ms/azurebackup_agent). Raadpleeg [dit artikel](backup-azure-backup-server-import-export-.md)als u offline back-ups wilt uitvoeren voor workloads met System Center DPM of Azure backup server.

Voordat u de werk stroom voor offline back-ups initieert, moet u de volgende vereisten volt ooien:

* Maak een [Recovery Services kluis](backup-azure-recovery-services-vault-overview.md). Raadpleeg de stappen in [dit artikel](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault) om een kluis te maken
* Zorg ervoor dat alleen de [meest recente versie van de Azure backup-agent](https://aka.ms/azurebackup_agent) is geïnstalleerd op de Windows Server/Windows-client, indien van toepassing en de computer is geregistreerd bij de Recovery Services kluis.
* Azure PowerShell 3.7.0 is vereist op de computer met Azure Backup Agent. Het is raadzaam om [de 3.7.0-versie van Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)te downloaden en te installeren.
* Controleer op de computer met Azure Backup Agent of micro soft Edge of Internet Explorer 11 is geïnstalleerd en of Java script is ingeschakeld.
* Maak een Azure Storage-account in hetzelfde abonnement als de Recovery Services kluis.
* Zorg ervoor dat u over de [benodigde machtigingen](../active-directory/develop/howto-create-service-principal-portal.md) beschikt om de Azure Active Directory-toepassing te maken. De werk stroom offline back-up maakt een Azure Active Directory-toepassing in het abonnement dat is gekoppeld aan het Azure Storage-account. Het doel van de toepassing is om Azure Backup te voorzien van beveiligde en scoped toegang tot de Azure-import Service, die is vereist voor de werk stroom voor offline back-ups.
* Registreer de resource provider micro soft. ImportExport bij het abonnement met het Azure Storage-account. De resource provider registreren:
    1. Klik in het hoofd menu op **abonnementen**.
    2. Als u bent geabonneerd op meerdere abonnementen, selecteert u het abonnement dat u gebruikt voor de offline back-up. Als u slechts één abonnement gebruikt, wordt uw abonnement weer gegeven.
    3. Klik in het menu abonnement op **resource providers** om de lijst met providers weer te geven.
    4. Schuif omlaag in de lijst met providers naar micro soft. ImportExport. Als de status NotRegistered is, klikt u op **registreren**.
    de resource provider ![registreren](./media/backup-azure-backup-import-export/registerimportexport.png)
* Een faserings locatie, die een netwerk share kan zijn of een extra station op de computer, intern of extern, met voldoende schijf ruimte om uw eerste kopie te bewaren, wordt gemaakt. Als u bijvoorbeeld een back-up probeert te maken van een bestands server van 500 GB, moet u ervoor zorgen dat de tijdelijke ruimte ten minste 500 GB is. (Er wordt een kleiner bedrag gebruikt vanwege compressie.)
* Gebruik bij het verzenden van schijven naar Azure alleen 2,5-SSD-of 2,5-inch of 3,5-inch SATA II/III interne harde schijven. U kunt harde schijven van Maxi maal 10 TB gebruiken. Raadpleeg de [documentatie van de Azure import/export-service](../storage/common/storage-import-export-requirements.md#supported-hardware) voor de meest recente set stations die door de service worden ondersteund.
* De SATA-schijven moeten zijn verbonden met een computer (waarnaar wordt verwezen als een *Kopieer computer*) van waaruit het kopiëren van back-upgegevens van de *faserings locatie* naar de SATA-schijven is voltooid. Zorg ervoor dat BitLocker is ingeschakeld op de *Kopieer computer*.

## <a name="workflow"></a>Werkstroom

In deze sectie wordt de werk stroom voor offline back-ups beschreven, zodat uw gegevens kunnen worden geleverd aan een Azure-Data Center en naar Azure Storage worden geüpload. Als u vragen hebt over de import service of een wille keurig aspect van het proces, raadpleegt u de documentatie over het importeren van de [service](../storage/common/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Offline back-up initiëren

1. Wanneer u een back-up plant op de MARS-agent, wordt het volgende scherm weer gegeven.

    ![Scherm importeren](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

   De beschrijving van de invoer is als volgt:

    * **Faserings locatie**: de tijdelijke opslag locatie waarnaar de eerste back-up wordt geschreven. De faserings locatie kan zich op een netwerk share of een lokale computer bezien. Als de computer en bron computer verschillend zijn, raden we u aan het volledige netwerkpad van de faserings locatie op te geven.
    * **Azure Resource Manager Storage-account**: de naam van het Resource Manager-type opslag account (algemeen gebruik v1 of algemeen gebruik v2) in een Azure-abonnement.
    * **Azure storage container**: de naam van de doel-Storage-Blob in het Azure Storage account waar de back-upgegevens worden geïmporteerd voordat deze naar de Recovery Services kluis worden gekopieerd.
    * **Azure-abonnements-id**: de id voor het Azure-abonnement waarin het Azure Storage-account is gemaakt.
    * **Azure import-taak naam**: de unieke naam waarmee de Azure import-service en Azure backup de overdracht van gegevens die op schijven zijn verzonden, volgen naar Azure.
  
   Geef de invoer op het scherm op en klik op **volgende**. Sla de opgegeven *faserings locatie* en de naam van de *Azure import-taak*op, omdat deze informatie vereist is om de schijven voor te bereiden.

2. Meld u aan bij uw Azure-abonnement wanneer u hierom wordt gevraagd. U moet zich aanmelden zodat Azure Backup de Azure Active Directory-toepassing kunt maken en de vereiste machtigingen geeft voor toegang tot de Azure import-service.

    ![Nu back-up maken](./media/backup-azure-backup-import-export/azurelogin.png)

3. Voltooi de werk stroom en klik in de Azure Backup Agent-console op **Nu back**-up maken.

    ![Nu back-up maken](./media/backup-azure-backup-import-export/backupnow.png)

4. Klik op de pagina Bevestiging van de wizard op **back-up**. De eerste back-up wordt naar het faserings gebied geschreven als onderdeel van de installatie.

   ![Bevestig dat u nu een back-up wilt maken](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Nadat de bewerking is voltooid, kan de faserings locatie worden gebruikt voor het voorbereiden van de schijf.

   ![Nu back-up maken](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>SATA-schijven voorbereiden en verzenden naar Azure

Het hulp programma *AzureOfflineBackupDiskPrep* bereidt de SATA-stations voor die naar het dichtstbijzijnde Azure-Data Center worden verzonden. Dit hulp programma is beschikbaar in de installatiemap van de Azure Backup Agent (in het volgende pad):

    *\Microsoft Azure Recovery Services Agent\Utils\\*

1. Ga naar de map en kopieer de **AzureOfflineBackupDiskPrep** -map naar een andere computer waarop de SATA-schijven zijn aangesloten. Zorg ervoor dat op de computer met de aangesloten SATA-schijven:

   * De Kopieer computer heeft toegang tot de faserings locatie voor de werk stroom voor offline seeding met behulp van hetzelfde netwerkpad dat is gegeven in de werk stroom **offline back-up initiëren** .
   * BitLocker is ingeschakeld op de Kopieer computer.
   * Azure PowerShell 3.7.0 is geïnstalleerd.
   * De meest recente compatibele browsers (micro soft Edge of Internet Explorer 11) zijn geïnstalleerd en Java script is ingeschakeld.
   * De Kopieer computer heeft toegang tot de Azure Portal. Als dat nodig is, kan de Kopieer computer gelijk zijn aan die van de bron computer.

     > [!IMPORTANT]
     > Als de bron computer een virtuele machine is, moet de Kopieer computer een andere fysieke server of client computer van de bron computer zijn.

2. Open een opdracht prompt met verhoogde bevoegdheid op de computer kopiëren met de map *AzureOfflineBackupDiskPrep* Utility als de huidige map en voer de volgende opdracht uit:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parameter | Beschrijving |
    | --- | --- |
    | s:&lt;*pad voor faserings locatie*&gt; |Verplichte invoer die wordt gebruikt om het pad naar de faserings locatie op te geven die u hebt ingevoerd in de werk stroom **offline back-up initiëren** . |
    | p:&lt;*pad naar PublishSettingsFile*&gt; |Optionele invoer die wordt gebruikt om het pad naar het **Azure Publish Settings** -bestand op te geven dat u hebt ingevoerd in de werk stroom **offline back-up initiëren** . |

    Wanneer u de opdracht uitvoert, vraagt het hulp programma de selectie van de Azure import-taak aan die overeenkomt met de stations die moeten worden voor bereid. Als er slechts één import taak is gekoppeld aan de gegeven faserings locatie, ziet u een scherm zoals de volgende.

    ![Invoer van hulp programma voor Azure-schijf voorbereiding](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

3. Voer de stationsletter zonder de afsluitende dubbele punt in voor de gekoppelde schijf die u wilt voorbereiden voor overdracht naar Azure.
4. Geef een bevestiging voor de Format teren van het station op wanneer u hierom wordt gevraagd.
5. U wordt gevraagd om u aan te melden bij uw Azure-abonnement. Geef uw referenties op.

    ![Invoer van hulp programma voor Azure-schijf voorbereiding](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Het hulp programma begint vervolgens met het voorbereiden van de schijf en het kopiëren van de back-upgegevens. Mogelijk moet u extra schijven toevoegen wanneer u hierom wordt gevraagd, in het geval dat de schijf niet voldoende ruimte heeft voor de back-upgegevens. <br/>

    Aan het einde van de geslaagde uitvoering van het hulp programma biedt de opdracht prompt drie stukjes informatie:
   1. Een of meer schijven die u hebt geleverd, worden voor bereid voor verzen ding naar Azure.
   2. U ontvangt een bevestiging dat uw import taak is gemaakt. De import taak maakt gebruik van de naam die u hebt ingevoerd.
   3. Het hulp programma geeft het verzend adres weer voor het Azure-Data Center.

      ![Voorbereiden van Azure-schijf is voltooid](./media/backup-azure-backup-import-export/console2.png)<br/>

6. Aan het einde van de uitvoering van de opdracht kunt u de verzend gegevens bijwerken.

7. Verzend de schijven naar het adres dat het hulp programma heeft geleverd en behoud het tracking nummer voor toekomstige referentie.

   > [!IMPORTANT]
   > Er kunnen niet twee Azure-import taken hetzelfde tracking nummer hebben. Zorg ervoor dat de stations die worden voor bereid met het hulp programma onder één Azure import-taak samen worden verzonden in één pakket en dat er één uniek Volg nummer voor het pakket is. Combi neer geen stations die zijn voor bereid als onderdeel van afzonderlijke Azure-import taken in één pakket.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Verzend gegevens bijwerken in de Azure import-taak

Met de volgende procedure worden de verzend gegevens van de Azure import-taak bijgewerkt. Deze informatie omvat onder meer informatie over:

* de naam van de vervoerder die de schijven aan Azure levert
* Verzend gegevens retour neren voor uw schijven

1. Meld u aan bij uw Azure-abonnement.
2. Klik in het hoofd menu op **alle services** en typ importeren in het dialoog venster alle services. Wanneer u **import/export-taken**ziet, klikt u erop.
    ![het invoeren van verzend gegevens](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    De lijst met het menu **import/export-taken** wordt geopend en de lijst met alle import/export-taken in het geselecteerde abonnement wordt weer gegeven.

3. Als u meerdere abonnementen hebt, selecteert u het abonnement dat is gebruikt voor het importeren van de back-upgegevens. Selecteer vervolgens de zojuist gemaakte import taak om de details ervan te openen.

    ![Verzend gegevens controleren](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

4. Klik in het menu instellingen voor de import taak op **Verzend gegevens beheren** en voer de retour verzendings gegevens in.

    ![Verzend gegevens opslaan](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

5. Wanneer u het tracking nummer van uw vervoerder hebt, klikt u op de banner op de pagina overzicht van Azure import-taak en voert u de volgende gegevens in:

   > [!IMPORTANT]
   > Zorg dat de informatie voor de vervoerder en het traceringsnummer binnen twee weken van het maken van de Azure-importtaak zijn bijgewerkt. Als u deze gegevens niet binnen twee weken controleert, kan dit ertoe leiden dat de taak wordt verwijderd en dat de stations niet worden verwerkt.

   ![Verzend gegevens opslaan](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Verzend gegevens opslaan](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Tijd voor het verwerken van de stations

De hoeveelheid tijd die nodig is voor het verwerken van een Azure import-taak is afhankelijk van verschillende factoren, zoals de verzend tijd, het taak type, het type en de grootte van de gegevens die worden gekopieerd en de grootte van de geleverde schijven. De Azure import/export-service heeft geen SLA, maar nadat de schijven zijn ontvangen, streeft de service naar het volt ooien van de back-upgegevens naar uw Azure Storage-account in 7 tot tien dagen.

### <a name="monitoring-azure-import-job-status"></a>De status van de Azure import-taak controleren

U kunt de status van uw import taak controleren vanuit de Azure Portal door te navigeren naar de pagina **import/export-taken** en uw taak te selecteren. Zie het artikel [Storage import export service](../storage/common/storage-import-export-service.md) voor meer informatie over de status van de import taken.

### <a name="complete-the-workflow"></a>De werk stroom volt ooien

Nadat de import taak is voltooid, zijn de eerste back-upgegevens beschikbaar in uw opslag account. Op het moment van de volgende geplande back-up Azure Backup kopieert de inhoud van de gegevens uit het opslag account naar de Recovery Services kluis, zoals hieronder wordt weer gegeven:

   ![Gegevens kopiëren naar Recovery Services kluis](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Op het moment van de volgende geplande back-up voert Azure Backup een incrementele back-up uit.

### <a name="cleaning-up-resources"></a>Resources opschonen

Zodra de eerste back-up is voltooid, kunt u de gegevens die zijn geïmporteerd in de Azure Storage container en de back-upgegevens op de Faserings locatie veilig verwijderen.

## <a name="next-steps"></a>Volgende stappen

* Voor vragen over de Azure import/export-werk stroom raadpleegt u [de Microsoft Azure import/export-service gebruiken om gegevens over te dragen naar de Blob-opslag](../storage/common/storage-import-export-service.md).
* Raadpleeg de sectie offline back-up van de Azure Backup [Veelgestelde vragen](backup-azure-backup-faq.md) voor vragen over de werk stroom.
