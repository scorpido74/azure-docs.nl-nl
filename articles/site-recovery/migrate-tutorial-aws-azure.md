---
title: AWS-VM's migreren naar Azure met de Azure Site Recovery-service | Microsoft Docs
description: In dit artikel wordt beschreven hoe u virtuele Windows-machines die worden uitgevoerd op Amazon Web Services (AWS) naar Azure migreert met behulp van Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 1533ceebcda15c45a71c04580c35432de4125ccd
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984987"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>AWS-VM’s (Amazon Web Services) migreren naar Azure

In deze zelfstudie leert u hoe u AWS-VM’s (Amazon Web Services) naar Azure migreert met behulp van Azure Site Recovery. Wanneer u AWS EC2-instanties migreert naar Azure, worden de VM’s behandeld als fysieke on-premises computers. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Vereisten verifiëren
> * Azure-resources voorbereiden
> * AWS EC2-instanties voorbereiden voor migratie
> * Een configuratieserver implementeren
> * Replicatie inschakelen voor VM's
> * De failover testen om te controleren of alles werkt
> * Een eenmalige failover uitvoeren naar Azure

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.


> [!NOTE]
    > U kunt nu de Azure Migrate-service gebruiken om AWS-exemplaren naar Azure te migreren. [Meer informatie](../migrate/tutorial-migrate-physical-virtual-machines.md).

## <a name="prerequisites"></a>Vereisten
- Zorg ervoor dat de VM’s die u wilt migreren, worden uitgevoerd met een ondersteunde versie van het besturingssysteem. Ondersteunde versies zijn onder andere: 
  - Windows Server 2016 
  - Windows Server 2012 R2
  - Windows Server 2012 
  - 64-bits versie van Windows Server 2008 R2 SP1 of later
  - Red Hat Enterprise Linux 6.4–6.10, 71 –7.6 (alleen gevirtualiseerde HVM-instanties) *(Instanties met RedHat PV-stuurprogramma’s worden niet ondersteund.)*
  - CentOS 6.4–6.10 7.1–7.6 (alleen gevirtualiseerde HVM-instanties)
 
- De Mobility-service moet worden geïnstalleerd op elke VM die u wilt repliceren. 

    > [!IMPORTANT]
    > Site Recovery installeert deze service automatisch wanneer u replicatie voor de VM inschakelt. Voor automatische installatie moet u op de EC2-instanties een account voorbereiden waarmee Site Recovery toegang kan krijgen tot de VM. U kunt een domein of lokale account gebruiken. 
    > - Voor Linux-VM’s moet het account een hoofdaccount zijn op de Linux-bronserver. 
    > - Voor Windows-accounts schakelt u als u geen domeinaccount gebruikt toegangsbeheer voor externe gebruikers uit op de lokale computer:
    >
    >      Voeg in het register onder **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** de DWORD-vermelding **LocalAccountTokenFilterPolicy** toe en stel de waarde in op **1**.

- Een afzonderlijke EC2-instantie die u kunt gebruiken als de Site Recovery-configuratieserver. Op deze instantie moet Windows Server 2012 R2 worden uitgevoerd.

## <a name="prepare-azure-resources"></a>Azure-resources voorbereiden

U moet een aantal resources gereed hebben in Azure voor gebruik op de gemigreerde EC2-instanties. Het gaat hier om een opslagaccount, een kluis en een virtueel netwerk.

### <a name="create-a-storage-account"></a>Create a storage account

Installatiekopieën van gerepliceerde machines worden bewaard in Azure Storage. Azure-VM's worden gemaakt vanuit de opslag wanneer u een failover van on-premises naar Azure uitvoert.

1. Selecteer in [Azure Portal](https://portal.azure.com), in het linkermenu, achtereenvolgens **Resource maken** > **Opslag** > **Opslagaccount**.
2. Voer een naam in voor het opslagaccount. In deze zelfstudies gebruiken we de naam**awsmigrated2017**. De naam moet:
    - Uniek zijn in Azure
    - Minimaal 3 en maximaal 24 tekens lang zijn
    - Alleen cijfers en kleine letters bevatten
3. Laat de standaardwaarden staan bij **Implementatiemodel**, **Soort account**, **Prestaties**, en **Veilige overdracht vereist**.
5. Selecteer bij **Replicatie** de standaardinstelling **RA-GRS**.
6. Selecteer het abonnement dat u voor deze zelfstudie wilt gebruiken.
7. Selecteer voor **Resourcegroep** de optie **Nieuwe maken**. In dit voorbeeld gebruiken we **migrationRG** als naam voor de resourcegroep.
8. Selecteer bij **Locatie** de optie **Europa - west**.
9. Selecteer **Maken** om het opslagaccount te maken.

### <a name="create-a-vault"></a>Een kluis maken

1. Selecteer in [Azure Portal](https://portal.azure.com) de optie **Alle services**. Zoek en selecteer **Recovery Services-kluizen**.
2. Selecteer **Toevoegen** op de pagina Azure Recovery Services-kluizen.
3. Voer bij **Naam** in: **myVault**.
4. Selecteer bij **Abonnement** het abonnement dat u wilt gebruiken.
4. Selecteer bij **Resourcegroep** de optie **Bestaande gebruiken** en selecteer vervolgens **migrationRG**.
5. Selecteer bij **Locatie** de optie **Europa - west**.
5. Selecteer **Aan dashboard vastmaken** voor snelle toegang tot de nieuwe kluis vanaf het dashboard.
7. Als u gereed bent, selecteert u **Maken**.

Ga naar **Dashboard** > **Alle resources** om de nieuwe kluis te zien. De nieuwe kluis wordt ook weergegeven op de hoofdpagina **Recovery Services-kluizen**.

### <a name="set-up-an-azure-network"></a>Een Azure-netwerk instellen

Wanneer Azure-VM’s zijn gemaakt na de migratie (failover), worden ze gekoppeld aan dit Azure-netwerk.

1. Selecteer in [Azure Portal](https://portal.azure.com) achtereenvolgens **Een resource maken** > **Netwerken** >
   **Virtueel netwerk**.
3. Voer bij **Naam** in **myMigrationNetwork**.
4. De standaard waarde voor de **adres ruimte** behouden (moet een waarde invoeren).
5. Selecteer bij **Abonnement** het abonnement dat u wilt gebruiken.
6. Selecteer bij **Resourcegroep** de optie **Bestaande gebruiken** en selecteer vervolgens **migrationRG**.
7. Selecteer bij **Locatie** de optie **Europa - west**.
8. Behoud onder **subnet**de standaard waarden voor **naam** en **IP-bereik (moet waarde invoeren)** .
9. Voeg instructies toe voor DDoS-beveiligings instellingen.
10. Laat de optie **Service-eindpunten** uitgeschakeld.
11. Instructies toevoegen voor Firewall instellingen.
12. Als u gereed bent, selecteert u **Maken**.

## <a name="prepare-the-infrastructure"></a>De infrastructuur voorbereiden

Selecteer op de pagina voor uw kluis in Azure Portal in de sectie **Aan de slag** de optie **Site Recovery**. Selecteer vervolgens **Infrastructuur voorbereiden**. Voltooi de volgende stappen.

### <a name="1-protection-goal"></a>1: Beveiligingsdoel

Selecteer op de pagina **Beveiligingsdoel** de volgende waarden:

|    |  |
|---------|-----------|
| Waar bevinden de machines zich? |Selecteer **On-premises**.|
| Waarnaartoe wilt u de machines repliceren? |Selecteer **Naar Azure**.|
| Voert u een migratie uit? | Selecteer **Ja**en schakel het selectie vakje naast de **underdstand in, maar ik wil door gaan met Azure site Recovery.**
| Zijn de machines gevirtualiseerd? |Selecteer **Niet gevirtualiseerd/overig**.|

Wanneer u gereed bent, selecteert u **OK** om naar de volgende sectie te gaan.

### <a name="2-select-deployment-planning"></a>2: Implementatieplanning selecteren

Selecteer in **Hebt u de implementatieplanning uitgevoerd?** de optie **Dat doe ik later**. Selecteer vervolgens **OK**.

### <a name="3-prepare-source"></a>3: Bron voorbereiden

Selecteer op de pagina **Bron voorbereiden** de optie **+ Configuratieserver**.

1. Gebruik een EC2-instantie waarop Windows Server 2012 R2 wordt uitgevoerd om een configuratieserver te maken en deze te registreren bij de recovery-kluis.
2. Configureer de proxy op de EC2-instantie-VM die u als de configuratieserver gebruikt, zodat deze toegang heeft tot de [service-URL's](site-recovery-support-matrix-to-azure.md).
3. Download [Geïntegreerde Setup van Microsoft Azure Site Recovery](https://aka.ms/unifiedinstaller_wus). U kunt dit downloaden naar de lokale computer en vervolgens kopiëren naar de VM die u gebruikt als de configuratieserver.
4. Selecteer de knop **Downloaden** om de kluisregistratiesleutel te downloaden. Kopieer het gedownloade bestand naar de VM die u gebruikt als de configuratieserver.
5. Klik op de VM met de rechtermuisknop op het installatieprogramma dat u hebt gedownload voor Geïntegreerde Setup van Microsoft Azure Site Recovery. Selecteer vervolgens **Als administrator uitvoeren**.

    1. Selecteer onder **Voordat u begint** de optie **De configuratieserver en processerver installeren**. Selecteer vervolgens **Volgende**.
    2. Selecteer in **Softwarelicentie van derden** de optie **Ik ga akkoord met de licentieovereenkomst van de derde partij**, en selecteer vervolgens **Volgende**.
    3. Selecteer in **Registratie** de optie **Bladeren** en ga naar de locatie waar u het registratiesleutelbestand voor de kluis hebt opgeslagen. Selecteer **Volgende**.
    4. Selecteer in **Internetinstellingen** de optie **Rechtstreeks verbinding maken met Azure Site Recovery zonder proxyserver**. Selecteer vervolgens **Volgende**.
    5. Op de pagina **Controle op vereisten** worden controles voor verschillende items uitgevoerd. Wanneer dit is voltooid, selecteert u **Volgende**.
    6. Geef in **MySQL-configuratie** de vereiste wachtwoorden op. Selecteer vervolgens **Volgende**.
    7. Selecteer in **Details van omgeving** de optie **Nee**. U hoeft VMware-machines niet te beveiligen. Selecteer vervolgens **Volgende**.
    8. Selecteer in **Installatielocatie** de optie **Volgende** om de standaardinstelling te accepteren.
    9. Selecteer in **Netwerkselectie** de optie **Volgende** om de standaardinstelling te accepteren.
    10. Selecteer in **Samenvatting** de optie **Installeren**.
    11. Bij **Voortgang van de installatie** ziet u informatie over hoe de installatie vordert. Wanneer dit is voltooid, selecteert u **Voltooien**. Er wordt een venster weergegeven met daarin een bericht over opnieuw opstarten. Selecteer **OK**. Vervolgens wordt een bericht weergegeven over de wachtwoordzin voor de configuratieserververbinding. Kopieer de wachtwoordzin naar het klembord en sla deze ergens veilig op.
6. Voer cspsconfigtool.exe uit op de VM om een of meer beheeraccounts op de configuratieserver te maken. Zorg ervoor dat de beheeraccounts beheerdersmachtigingen hebben voor de EC2-instanties die u wilt migreren.

Wanneer u klaar bent met het instellen van de configuratieserver, gaat u terug naar de portal en selecteert u de server die u zojuist hebt gemaakt als **configuratieserver**. Selecteer **OK** om naar 3: Doel voorbereiden te gaan.

### <a name="4-prepare-target"></a>4: Doel voorbereiden

In deze sectie voert u gegevens in over de resources die u eerder in deze zelfstudie hebt gemaakt in [Azure-resources voorbereiden](#prepare-azure-resources).

1. Selecteer bij **Abonnement** het Azure-abonnement dat u hebt gebruikt voor de zelfstudie [Azure voorbereiden](tutorial-prepare-azure.md).
2. Selecteer **Resource Manager** als het implementatiemodel.
3. Met Site Recovery wordt gecontroleerd of u een of meer compatibele Azure-opslagaccounts en -netwerken hebt. Als het goed is, zijn dit de resources die u eerder in deze zelfstudie hebt gemaakt in [Azure-resources voorbereiden](#prepare-azure-resources).
4. Wanneer u gereed bent, selecteert u **OK**.

### <a name="5-prepare-replication-settings"></a>5: Replicatie-instellingen voorbereiden

Voordat u replicatie kunt inschakelen, moet u replicatiebeleid maken.

1. Selecteer **maken en koppelen**.
2. Voer bij **Naam** in **myReplicationPolicy**.
3. Laat de rest van de standaardinstellingen ongewijzigd en selecteer **OK** om het beleid te maken. Het nieuwe beleid wordt automatisch gekoppeld aan de configuratieserver.

Wanneer u alle vijf de secties onder **Infrastructuur voorbereiden** hebt voltooid, selecteert u **OK**.

## <a name="enable-replication"></a>Replicatie inschakelen

Schakel replicatie in voor elke VM die u wilt migreren. Wanneer replicatie is ingeschakeld, wordt de Mobility-service automatisch geïnstalleerd met Site Recovery.

1. Ga naar de [Azure Portal](https://portal.azure.com).
1. Selecteer op de pagina voor uw kluis, onder **Aan de slag**, de optie **Site Recovery**.
2. Selecteer onder **Voor on-premises machines en Azure-VM's** de optie **Stap 1: Toepassing repliceren**. Voltooi de pagina’s van de wizard met de volgende informatie. Selecteer **OK** op elke pagina wanneer u gereed bent:
   - 1: Bron configureren

     |  |  |
     |-----|-----|
     | Bron: | Selecteer **On-premises**.|
     | Bronlocatie:| Voer de naam in van de EC2-instantie van de configuratieserver.|
     |Machinetype: | Selecteer **Fysieke machines**.|
     | Processerver: | Selecteer de configuratieserver in de vervolgkeuzelijst.|

   - 2: Doel configureren

     |  |  |
     |-----|-----|
     | Doel: | Laat de standaardinstelling staan.|
     | Abonnement: | Selecteer het Azure-abonnement dat u hebt gebruikt.|
     | Resourcegroep na failover:| Gebruik de resourcegroep die u hebt gemaakt in [Azure-resources voorbereiden](#prepare-azure-resources).|
     | Implementatiemodel na failover: | Selecteer **Resource Manager**.|
     | Opslagaccount: | Selecteer het opslagaccount dat u hebt gemaakt in [Azure-resources voorbereiden](#prepare-azure-resources).|
     | Azure-netwerk: | Selecteer **Configureer deze nu voor geselecteerde machines**.|
     | Azure-netwerk na failover: | Kies het netwerk dat u hebt gemaakt in [Azure-resources voorbereiden](#prepare-azure-resources).|
     | Subnet: | Selecteer de **standaardinstelling** in de vervolgkeuzelijst.|

   - 3: Fysieke machines selecteren

     Selecteer **Fysieke machine** en voer vervolgens de **Naam**, het **IP-adres** en het **Type besturingssysteem** in van de EC2-instantie die u wilt migreren. Selecteer **OK**.

   - 4: Eigenschappen configureren

     Selecteer het account dat u hebt gemaakt op de configuratieserver. Selecteer vervolgens **OK**.

   - 5: Replicatie-instellingen configureren

     Zorg ervoor dat het replicatiebeleid **myReplicationPolicy** is geselecteerd in de vervolgkeuzelijst. Selecteer vervolgens **OK**.

3. Wanneer de wizard is voltooid, selecteert u **Replicatie inschakelen**.

Als u de voortgang van de taak **Beveiliging inschakelen** wilt bijhouden, gaat u naar **Bewaking en rapporten** > **Taken** > **Site Recovery-taken**. Nadat de taak **Beveiliging voltooien** is uitgevoerd, is de machine klaar voor een mogelijke failover.        

Wanneer u replicatie voor een VM inschakelt, kan het 15 minuten of langer duren voordat wijzigingen zijn doorgevoerd en worden weergegeven in de portal.

## <a name="run-a-test-failover"></a>Een testfailover uitvoeren

Wanneer u een testfailover uitvoert, gebeurt het volgende:

- Er wordt een controle uitgevoerd om na te gaan of aan alle vereiste voorwaarden voor failover wordt voldaan.
- De gegevens worden verwerkt in de failover, zodat een Azure-VM kan worden gemaakt. Als u het meest recente herstelpunt selecteert, wordt een herstelpunt van de gegevens gemaakt.
- Er wordt een Azure-VM gemaakt met behulp van de gegevens die zijn verwerkt in de vorige stap.

Voer de testfailover uit in de portal:

1. Ga op de pagina voor uw kluis naar **Beveiligde items** > **Gerepliceerde Items**. Selecteer de VM en selecteer vervolgens **Testfailover**.
2. Selecteer een herstelpunt om voor de failover te gebruiken:
    - **Laatst verwerkt**: voert een failover van de VM uit naar het laatste herstelpunt dat is verwerkt met Site Recovery. Het tijdstempel wordt weergegeven. Met deze optie wordt er geen tijd besteed aan het verwerken van gegevens, zodat er sprake is van een lage RTO (Recovery Time Objective).
    - **Laatste toepassingsconsistente punt**: met deze optie wordt er een failover uitgevoerd van alle VM's naar het laatste toepassingsconsistente herstelpunt. Het tijdstempel wordt weergegeven.
    - **Aangepast**: selecteer een herstelpunt.

3. Selecteer in **Failover testen** het Azure-doelnetwerk waarmee de virtuele Azure-machines moeten worden verbonden nadat de failover heeft plaatsgevonden. Dit moet het netwerk zijn dat u hebt gemaakt in [Azure-resources voorbereiden](#prepare-azure-resources).
4. Selecteer **OK** om de failover te starten. Selecteer de VM om de eigenschappen ervan weer te geven als u de voortgang wilt volgen. Of u kunt de taak **Testfailover** selecteren op de pagina voor uw kluis. Selecteer hiervoor **Controle en rapporten** > **Taken** >  **Site Recovery-taken**.
5. Wanneer de failover is voltooid, wordt de replica-Azure-VM weergegeven in Azure Portal. Selecteer **Virtuele machines** om de VM te bekijken. Controleer of de VM de juiste grootte heeft, is verbonden met het juiste netwerk en actief is.
6. Nu moet u verbinding maken met de gerepliceerde virtuele machine in Azure.
7. Selecteer **Failovertest opschonen** in het herstelplan om de Azure-VM’s te verwijderen die zijn gemaakt tijdens de testfailover. Leg in **Notities** eventuele opmerkingen over de testfailover vast en sla deze op.

In sommige scenario’s is voor failover extra verwerking vereist. Het duurt 8 tot 10 minuten voordat de verwerking is voltooid.

## <a name="migrate-to-azure"></a>Migreren naar Azure

Voer een werkelijke failover voor de EC2-instanties uit om deze naar Azure-VM’s te migreren:

1. Selecteer in **Beveiligde items** > **Gerepliceerde items** de AWS-instanties. Selecteer vervolgens **Failover**.
2. Selecteer in **Failover** een **Herstelpunt** om een failover voor uit te voeren. Selecteer het meest recente herstelpunt en start de failover. U kunt de voortgang van de failover volgen op de pagina **Taken**.
1. Controleer of de VM wordt weergegeven in **Gerepliceerde items**.
2. Klik met de rechtermuisknop op elke VM, en selecteer vervolgens **Migratie voltooien**. Er gebeurt nu het volgende:

   - Hiermee wordt het migratieproces voltooid, de replicatie voor de AWS-VM gestopt en Site Recovery-facturering voor de virtuele machine gestopt.
   - Met deze stap worden de replicatiegegevens opgeschoond. De gemigreerde VM's worden niet verwijderd. 

     ![Migratie voltooien](./media/migrate-tutorial-aws-azure/complete-migration.png)

> [!WARNING]
> *Annuleer nooit een failover die wordt uitgevoerd*. De VM-replicatie wordt gestopt voordat de failover is gestart. Als u een failover annuleert die wordt uitgevoerd, wordt de failover gestopt, maar wordt de VM niet nogmaals gerepliceerd.  


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u AWS EC2-instanties kunt migreren naar Azure-VM's. Voor meer informatie over virtuele machines in Azure, gaat u verder met de zelfstudie voor virtuele Windows-machines.

> [!div class="nextstepaction"]
> [Zelfstudies over virtuele Windows-machines](../virtual-machines/windows/tutorial-manage-vm.md)
