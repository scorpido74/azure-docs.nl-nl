---
title: Back-ups maken van virtuele Azure-machines in een Recovery Services kluis met Azure Backup
description: Hierin wordt beschreven hoe u back-ups maakt van virtuele Azure-machines in een Recovery Services kluis met behulp van de Azure Backup
service: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: dacurwin
ms.openlocfilehash: 2ef8e7e77481c0df6e85545d16c3859949184d2f
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968541"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Back-ups maken van virtuele Azure-machines in een Recovery Services kluis

In dit artikel wordt beschreven hoe u back-ups maakt van virtuele Azure-machines in een Recovery Services kluis, met behulp van de [Azure backup](backup-overview.md) -service.

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * Virtuele Azure-machines voorbereiden.
> * Maak een kluis.
> * Virtuele machines detecteren en een back-upbeleid configureren.
> * Schakel back-ups voor virtuele Azure-machines in.
> * De eerste back-up uitvoeren.

> [!NOTE]
> In dit artikel wordt beschreven hoe u een kluis instelt en Vm's selecteert waarvan u een back-up wilt maken. Het is handig als u een back-up wilt maken van meerdere Vm's. U kunt ook rechtstreeks vanuit de VM-instellingen [een back-up maken van één Azure-VM](backup-azure-vms-first-look-arm.md) .

## <a name="before-you-start"></a>Voordat u begint

* [Bekijk](backup-architecture.md#architecture-direct-backup-of-azure-vms) de Azure VM-back-uparchitectuur.
* [Meer informatie over](backup-azure-vms-introduction.md) Back-ups van Azure-VM'S en de uitbrei ding van back-ups.
* [Raadpleeg de ondersteunings matrix](backup-support-matrix-iaas.md) voordat u een back-up configureert.

Daarnaast zijn er een aantal dingen die u in bepaalde omstandigheden mogelijk moet doen:

* **Installeer de VM-agent op de VM**: Azure backup maakt back-ups van virtuele Azure-machines door een uitbrei ding te installeren in de Azure VM-agent die op de computer wordt uitgevoerd. Als uw virtuele machine is gemaakt op basis van een installatie kopie van Azure Marketplace, wordt de agent geïnstalleerd en uitgevoerd. Als u een aangepaste VM maakt of een on-premises machine migreert, moet u [de agent mogelijk hand matig installeren](#install-the-vm-agent).
* **Uitgaande toegang expliciet toestaan**: over het algemeen hoeft u geen expliciete netwerk toegang voor een virtuele Azure-machine toe te staan, zodat deze kan communiceren met Azure backup. Sommige Vm's kunnen echter verbindings problemen ondervinden, waarbij de **ExtensionSnapshotFailedNoNetwork** -fout wordt weer gegeven wanneer wordt geprobeerd verbinding te maken. Als dit het geval is, moet u [uitgaande toegang expliciet toestaan](#explicitly-allow-outbound-access), zodat de Azure backup extensie kan communiceren met open bare IP-adressen van Azure voor back-upverkeer.

## <a name="create-a-vault"></a>Een kluis maken

 Een kluis slaat back-ups en herstel punten op die gedurende een periode zijn gemaakt en slaat back-upbeleid op dat is gekoppeld aan back-upcomputers. Maak als volgt een kluis:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Typ **Recovery Services**in zoeken. Klik onder **Services**op **Recovery Services kluizen**.

     ![Recovery Services kluizen zoeken](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

3. Klik in **Recovery Services menu kluizen** op **+ toevoegen**.

     ![Een Recovery Services-kluis maken, stap 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

4. Typ in **Recovery Services kluis**een beschrijvende naam om de kluis aan te duiden.
    * De naam moet uniek zijn voor het Azure-abonnement.
    * Dit kan twee tot 50 tekens bevatten.
    * De naam moet beginnen met een letter en mag alleen letters, cijfers en afbreek streepjes bevatten.
5. Selecteer het Azure-abonnement, de resource groep en de geografische regio waarin de kluis moet worden gemaakt. Klik vervolgens op **Maken**.
    * Het kan even duren voordat de kluis is gemaakt.
    * Bewaak de status meldingen in de rechter bovenhoek van de portal.

Nadat de kluis is gemaakt, wordt deze weer gegeven in de lijst Recovery Services kluizen. Als uw kluis niet wordt weer geven, selecteert u **vernieuwen**.

![Lijst met back-upkluizen](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

> [!NOTE]
> Azure Backup service maakt een afzonderlijke resource groep (met uitzonde ring van de resource groep van de VM) voor het opslaan van de moment opname, met de naamgevings indeling **AzureBackupRG_geography_number** (voor beeld: AzureBackupRG_northeurope_1). De gegevens in deze resource groep worden bewaard gedurende de duur in dagen zoals opgegeven in de sectie *onmiddellijke herstel momentopname bewaren* van het back-upbeleid van Azure virtual machine.  Het Toep assen van een vergren deling op deze resource groep kan leiden tot back-upfouten.<br>
Deze resource groep moet ook worden uitgesloten van de beperkingen van namen/Tags als een beperkings beleid het maken van resource punt verzamelingen in dat geval voor back-upfouten blokkeert.

### <a name="modify-storage-replication"></a>Opslag replicatie wijzigen

Standaard gebruiken kluizen de [geo-redundante opslag (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Als de kluis uw primaire back-upmechanisme is, raden we u aan GRS te gebruiken.
* U kunt [lokaal redundante opslag (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) gebruiken voor een goedkopere optie.

Wijzig het type opslag replicatie als volgt:

1. Klik in de nieuwe kluis op **Eigenschappen** in de sectie **instellingen** .
2. In **Eigenschappen**, onder **back-upconfiguratie**, klikt u op **bijwerken**.
3. Selecteer het type opslag replicatie en klik op **Opslaan**.

      ![De opslagconfiguratie voor nieuwe kluis instellen](./media/backup-try-azure-backup-in-10-mins/full-blade.png)

> [!NOTE]
   > U kunt het type opslag replicatie niet wijzigen nadat de kluis is ingesteld en back-upitems bevat. Als u dit wilt doen, moet u de kluis opnieuw maken.

## <a name="apply-a-backup-policy"></a>Een back-upbeleid Toep assen

Configureer een back-upbeleid voor de kluis.

1. Klik in de kluis op **+ back-up** in de sectie **overzicht** .

   ![Knop back-up](./media/backup-azure-arm-vms-prepare/backup-button.png)

2. In **back-updoel** > **waar wordt uw workload uitgevoerd?** Selecteer **Azure**. In **waarvan wilt u een back-up maken?** Selecteer de **virtuele machine** >  **OK**. Hiermee wordt de VM-extensie in de kluis geregistreerd.

   ![Deel Vensters voor back-up en back-up](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. Selecteer in **back-upbeleid**het beleid dat u aan de kluis wilt koppelen.
    * Het standaard beleid maakt eenmaal per dag een back-up van de VM. De dagelijkse back-ups worden 30 dagen bewaard. Moment opnamen voor direct herstel worden twee dagen bewaard.
    * Als u het standaard beleid niet wilt gebruiken, selecteert u **nieuwe maken**en maakt u een aangepast beleid zoals beschreven in de volgende procedure.

      ![Standaard back-upbeleid](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. Selecteer in **virtuele machines selecteren**de vm's waarvan u een back-up wilt maken met behulp van het beleid. Klik vervolgens op **OK**.

   * De geselecteerde Vm's worden gevalideerd.
   * U kunt alleen Vm's in dezelfde regio als de kluis selecteren.
   * Er kan alleen een back-up van virtuele machines worden gemaakt in één kluis.

     ![Het deel venster virtuele machines selecteren](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. Klik in **back-up**op **back-up inschakelen**. Hiermee wordt het beleid geïmplementeerd voor de kluis en de virtuele machines en wordt de back-upextensie geïnstalleerd op de VM-agent die wordt uitgevoerd op de virtuele machine van Azure.

     ![Knop back-up inschakelen](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Na het inschakelen van back-up:

* De back-upservice installeert de back-upextensie, ongeacht of de virtuele machine wordt uitgevoerd.
* Een eerste back-up wordt uitgevoerd in overeenstemming met uw back-upschema.
* Houd bij het uitvoeren van back-ups rekening met het volgende:
  * Een virtuele machine die wordt uitgevoerd, heeft de grootste kans om een toepassings consistent herstel punt vast te leggen.
  * Maar zelfs als de VM is uitgeschakeld, wordt er een back-up van gemaakt. Een dergelijke virtuele machine wordt ook wel een offline-VM genoemd. In dit geval wordt het herstel punt vastlopen consistent.

### <a name="create-a-custom-policy"></a>Aangepast beleid maken

Als u hebt geselecteerd voor het maken van een nieuw back-upbeleid, vult u de beleids instellingen in.

1. Geef in **beleids naam**een beschrijvende naam op.
2. Geef in **back-upschema**op wanneer back-ups moeten worden gemaakt. U kunt dagelijks of wekelijks back-ups maken voor Azure-Vm's.
3. Geef in **direct terugzetten**op hoe lang u moment opnamen lokaal wilt behouden voor direct terugzetten.
    * Wanneer u een back-up van VM-schijven herstelt, worden deze vanuit het netwerk gekopieerd naar de opslag locatie voor herstel. Met direct terugzetten kunt u lokaal opgeslagen moment opnamen gebruiken die zijn gemaakt tijdens een back-uptaak, zonder te wachten tot back-upgegevens worden overgebracht naar de kluis.
    * U kunt moment opnamen voor direct terugzetten tussen een en vijf dagen bewaren. Twee dagen is de standaard instelling.
4. Geef in **Bewaar termijn**op hoe lang u uw dagelijkse of wekelijkse back-uppunten wilt houden.
5. Geef bij het **bewaren van maandelijks back-uppunt**op of u een maandelijkse back-up van uw dagelijkse of wekelijkse back-ups wilt behouden.
6. Klik op **OK** om het beleid op te slaan.

    ![Nieuw back-upbeleid](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Azure Backup biedt geen ondersteuning voor automatische aanpassing van de klok voor zomer-en winter wijzigingen voor back-ups van Azure-VM'S. Wanneer er wijzigingen optreden, moet u het back-upbeleid hand matig aanpassen.

## <a name="trigger-the-initial-backup"></a>De eerste back-up activeren

De eerste back-up wordt uitgevoerd volgens de planning, maar u kunt deze als volgt direct uitvoeren:

1. Klik in het menu kluis op **Back-upitems**.
2. Klik in **Back-upitems**op **virtuele machine van Azure**.
3. Klik in de lijst **Back-upitems** op het weglatings teken (...).
4. Klik op **Nu back-up maken**.
5. In **Nu back-up**kunt u het besturings element kalender gebruiken om de laatste dag te selecteren dat het herstel punt moet worden bewaard. Klik vervolgens op **OK**.
6. De portal meldingen bewaken. U kunt de voortgang van de taak in het kluis dashboard controleren > **back-uptaken** > worden **uitgevoerd**. Afhankelijk van de grootte van de virtuele machine kan het maken van de eerste back-up even duren.

## <a name="verify-backup-job-status"></a>Status van back-uptaak controleren

De details van de back-uptaak voor elke VM-back-up bestaan uit twee fasen: de **momentopname** fase, gevolgd door de fase **gegevens overdragen naar de kluis** .<br/>
De momentopname fase garandeert de beschik baarheid van een herstel punt dat is opgeslagen samen met de schijven voor **onmiddellijke herstel** bewerkingen en is Maxi maal vijf dagen beschikbaar, afhankelijk van de retentie van de moment opname die door de gebruiker is geconfigureerd. Bij het overdragen van gegevens naar de kluis wordt een herstel punt in de kluis gemaakt voor lange termijn retentie. Het overdragen van gegevens naar de kluis begint alleen wanneer de fase van de moment opname is voltooid.

  ![Status van back-uptaak](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

Er worden twee **subtaken** uitgevoerd op de back-end, een voor front-end back-uptaak die kan worden gecontroleerd op de Blade Details van de **back-uptaak** , zoals hieronder wordt vermeld:

  ![Status van back-uptaak](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

De **overdracht van gegevens naar de kluis** fase kan meerdere dagen duren, afhankelijk van de grootte van de schijven, het verloop per schijf en verschillende andere factoren.

De taak status kan variëren, afhankelijk van de volgende scenario's:

**Snapshot** | **Gegevens overdragen aan de kluis** | **Taak status**
--- | --- | ---
Voltooid | Wordt uitgevoerd | Wordt uitgevoerd
Voltooid | Genegeerd | Voltooid
Voltooid | Voltooid | Voltooid
Voltooid | Mislukt | Voltooid met waarschuwing
Mislukt | Mislukt | Mislukt

Met deze mogelijkheid kunnen twee back-ups voor dezelfde virtuele machine parallel worden uitgevoerd, maar in beide fase (moment opname, gegevens overdragen naar de kluis) kan slechts één subtaak worden uitgevoerd. In scenario's werd een back-uptaak uitgevoerd waardoor de back-up van de volgende dag niet kan worden vermeden met deze ontkoppelings functionaliteit. De back-ups van de volgende dag kunnen een moment opname hebben voltooid terwijl **gegevens worden overgedragen naar de kluis die** wordt overgeslagen als de back-uptaak van een eerdere dag wordt uitgevoerd.
Met het incrementele herstel punt dat in de kluis is gemaakt, worden alle verloop van het laatste herstel punt dat in de kluis is gemaakt, vastgelegd. Er is geen kosten gevolgen voor de gebruiker.

## <a name="optional-steps-install-agentallow-outbound"></a>Optionele stappen (installatie agent/uitgaand toestaan)

### <a name="install-the-vm-agent"></a>De VM-agent installeren

Azure Backup maakt back-ups van virtuele Azure-machines door een uitbrei ding te installeren in de Azure VM-agent die op de computer wordt uitgevoerd. Als uw virtuele machine is gemaakt op basis van een installatie kopie van Azure Marketplace, wordt de agent geïnstalleerd en uitgevoerd. Als u een aangepaste VM maakt of een on-premises machine migreert, moet u de agent mogelijk hand matig installeren, zoals in de tabel wordt samenvatten.

**VM** | **Details**
--- | ---
**Windows** | 1. [down load en installeer](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) het MSI-bestand van de agent.<br/><br/> 2. Installeer met beheerders machtigingen op de computer.<br/><br/> 3. Controleer de installatie. Klik in *C:\WindowsAzure\Packages* op de virtuele machine met de rechter muisknop op **WaAppAgent. exe** > **Eigenschappen**. Op het tabblad **Details** moet de **product versie** 2.6.1198.718 of hoger zijn.<br/><br/> Als u de agent bijwerkt, moet u ervoor zorgen dat er geen back-upbewerkingen worden uitgevoerd en [installeert u de agent opnieuw](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Installeer met behulp van een RPM-of een DEB-pakket vanuit de pakket opslagplaats van uw distributie. Dit is de aanbevolen methode voor het installeren en upgraden van de Azure Linux-agent. Alle [gewaarmerkte distributie providers](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integreren het Azure Linux-agent pakket in hun installatie kopieën en opslag plaatsen. De agent is beschikbaar op [github](https://github.com/Azure/WALinuxAgent), maar we raden niet aan om te installeren.<br/><br/> Als u de agent bijwerkt, zorg er dan voor dat er geen back-upbewerkingen worden uitgevoerd en werk de binaire bestanden bij.

### <a name="explicitly-allow-outbound-access"></a>Uitgaande toegang expliciet toestaan

De back-upextensie die op de virtuele machine wordt uitgevoerd, heeft uitgaande toegang tot open bare IP-adressen van Azure nodig.

* Over het algemeen hoeft u geen expliciete netwerk toegang voor een Azure-VM toe te staan, zodat deze kan communiceren met Azure Backup.
* Als u problemen ondervindt met het verbinden van Vm's, of als u de fout **ExtensionSnapshotFailedNoNetwork** ziet wanneer u probeert verbinding te maken, moet u de toegang expliciet toestaan, zodat de back-upextensie kan communiceren met open bare IP-adressen van Azure voor back-up vervoer. De volgende tabel bevat een overzicht van de toegangs methoden.

**Optie** | **Actie** | **Details**
--- | --- | ---
**NSG-regels instellen** | Sta de [IP-adresbereiken van Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653)toe.<br/><br/> In plaats van elk adres bereik toe te staan en te beheren, kunt u een regel toevoegen waarmee u toegang krijgt tot de Azure Backup-service met behulp [van een servicetag](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure). | Meer [informatie](../virtual-network/security-overview.md#service-tags) over service tags.<br/><br/> Met services Tags wordt het toegangs beheer vereenvoudigd en worden er geen extra kosten in rekening gebracht.
**Een proxy implementeren** | Implementeer een HTTP-proxy server voor route ring van verkeer. | Biedt toegang tot het hele Azure en niet alleen opslag.<br/><br/> Gedetailleerde controle over de opslag-Url's is toegestaan.<br/><br/> Eén punt van Internet toegang voor Vm's.<br/><br/> Aanvullende kosten voor proxy.
**Azure Firewall instellen** | Verkeer via de Azure Firewall op de virtuele machine toestaan met behulp van een FQDN-code voor de Azure Backup-Service | Eenvoudig te gebruiken als u Azure Firewall hebt ingesteld in een VNet-subnet.<br/><br/> U kunt geen eigen FQDN-Tags maken of FQDN-namen in een label wijzigen.<br/><br/> Als uw Azure-Vm's beheerde schijven hebben, moet u mogelijk een extra poort (8443) openen op de firewalls.

#### <a name="establish-network-connectivity"></a>Netwerkverbinding tot stand brengen

Verbinding maken met NSG, op basis van een proxy of via de firewall

##### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Een NSG-regel instellen om uitgaande toegang tot Azure toe te staan

Als een NSG de toegang tot de virtuele machine beheert, moet u uitgaande toegang tot de vereiste bereiken en poorten toestaan voor de back-upopslag.

1. Selecteer in de VM-eigenschappen > **netwerk**de optie **uitgaande poort regel toevoegen**.
2. In **uitgaande beveiligings regel toevoegen**selecteert u **Geavanceerd**.
3. Selecteer in **bron** **VirtualNetwork**.
4. Voer in het bereik van de **bron poort**een asterisk (*) in om uitgaande toegang vanaf een wille keurige poort toe te staan.
5. Selecteer in **doel**de optie **service label**. Selecteer **opslag. regio**in de lijst. De regio is de locatie waar de kluis en de virtuele machines zich bevinden waarvan u een back-up wilt maken.
6. Selecteer in **doel**poortbereiken de poort.
    * VM met onbeheerde schijven met niet-versleuteld opslag account: 80
    * VM met onbeheerde schijven met een versleuteld opslag account: 443 (standaard instelling)
    * VM met beheerde schijven: 8443.
7. Selecteer in **protocol** **TCP**.
8. Geef bij **prioriteit**een prioriteits waarde op die kleiner is dan een hogere regels voor weigeren.

   Als u een regel hebt waarmee de toegang wordt geweigerd, moet de nieuwe regel voor toestaan hoger zijn. Als u bijvoorbeeld een **Deny_All** -regel hebt ingesteld op prioriteit 1000, moet uw nieuwe regel worden ingesteld op minder dan 1000.
9. Geef een naam en beschrijving op voor de regel en selecteer **OK**.

U kunt de NSG-regel Toep assen op meerdere virtuele machines om uitgaande toegang toe te staan. In deze video wordt u begeleid bij het proces.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]

##### <a name="route-backup-traffic-through-a-proxy"></a>Back-upverkeer via een proxy routeren

U kunt back-upverkeer via een proxy routeren en vervolgens de proxy toegang geven tot de vereiste Azure-bereiken. Configureer de proxy-VM om het volgende toe te staan:

* De Azure-VM moet alle HTTP-verkeer routeren dat is gebonden voor het open bare Internet via de proxy.
* De proxy moet binnenkomend verkeer van Vm's in het betreffende virtuele netwerk toestaan.
* De NSG **NSF-Lockdown** vereist een regel waarmee uitgaand Internet verkeer van de proxy-VM wordt toegestaan.

###### <a name="set-up-the-proxy"></a>De proxy instellen

Als u geen systeem account proxy hebt, stelt u een van de volgende opties in:

1. Down load [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Voer **PsExec. exe-i-s cmd. exe** uit om de opdracht prompt onder een systeem account uit te voeren.
3. Voer de browser uit in de systeem context. Gebruik bijvoorbeeld **%ProgramFiles%\Internet Explorer\iexplore.exe** voor Internet Explorer.  
4. Definieer de proxy-instellingen.
   * Op Linux-machines:
     * Voeg deze regel toe aan het **/etc/Environment** -bestand:
       * **http_proxy = http:\//proxy IP-adres: Proxy poort**
     * Voeg deze regels toe aan het **/etc/waagent.conf** -bestand:
         * **Http proxy. host = IP-adres van proxy**
         * **Http proxy. Port = Proxy poort**
   * Op Windows-computers, in de browser instellingen, geeft u op dat er een proxy moet worden gebruikt. Als u momenteel een proxy gebruikt voor een gebruikers account, kunt u dit script gebruiken om de instelling toe te passen op het niveau van het systeem account.

       ```powershell
      $obj = Get-ItemProperty -Path Registry::"HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
      $obj = Get-ItemProperty -Path Registry::"HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

       ```

###### <a name="allow-incoming-connections-on-the-proxy"></a>Binnenkomende verbindingen op de proxy toestaan

Binnenkomende verbindingen in de proxy-instellingen toestaan.

1. Open in Windows Firewall **Windows Firewall met geavanceerde beveiliging**.
2. Klik met de rechter muisknop op **regels voor binnenkomende verbindingen** > **nieuwe regel**.
3. Selecteer in **regel type**de optie **aangepaste** > **volgende**.
4. Selecteer in **programma** **alle Program ma's** > **volgende**.
5. In **protocollen en poorten**:
   * Stel het type in op **TCP**.
   * **Lokale poorten** instellen op **specifieke poorten**.
   * Stel **externe poort** in op **alle poorten**.

6. Voltooi de wizard en geef een naam op voor de regel.

###### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>Een uitzonderings regel toevoegen aan de NSG voor de proxy

Op de NSG **NSF-Lockdown**is verkeer toegestaan van elke poort op 10.0.0.5 naar een Internet adres op poort 80 (http) of 443 (https).

Het volgende Power shell-script biedt een voor beeld voor het toestaan van verkeer.
In plaats van uitgaande berichten voor alle open bare Internet adressen toe te staan, kunt u een IP-adres bereik (`-DestinationPortRange`) opgeven of het label Storage. Region-service gebruiken.

```powershell
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

##### <a name="allow-firewall-access-with-an-fqdn-tag"></a>Firewall toegang met een FQDN-label toestaan

U kunt Azure Firewall instellen om uitgaande toegang voor netwerk verkeer toe te staan voor Azure Backup.

* [Meer informatie over](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) het implementeren van Azure firewall.
* [Meer informatie over](https://docs.microsoft.com/azure/firewall/fqdn-tags) FQDN-Tags.

## <a name="next-steps"></a>Volgende stappen

* Los eventuele problemen met [Azure VM-agents](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) of [back-ups van Azure VM](backup-azure-vms-troubleshoot.md)op.
* [Herstellen](backup-azure-arm-restore-vms.md) Azure-Vm's.
