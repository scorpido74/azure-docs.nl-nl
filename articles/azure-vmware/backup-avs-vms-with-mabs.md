---
title: Back-ups maken van Azure VMware-oplossing (AVS) Vm's met Microsoft Azure Backup Server
description: Configureer uw Azure VMware Solution-omgeving (AVS) voor het maken van back-ups van virtuele machines met behulp van Microsoft Azure Backup Server.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 16941753d614bbfacbc63e4ae518986901153662
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84612759"
---
# <a name="back-up-avs-vms-with-microsoft-azure-backup-server"></a>Back-up maken van AVS-Vm's met Microsoft Azure Backup Server

In dit artikel gaan we de procedures volgen voor het maken van een back-up van virtuele VMware-machines die worden uitgevoerd op de Azure VMware-oplossing (AVS) met behulp van Azure Backup Server. Voordat u begint, moet u ervoor zorgen dat u de [Microsoft Azure backup-server instellen voor AVS](set-up-mabs-for-avs.md)grondig doorloopt. 

Vervolgens worden alle benodigde procedures door lopen voor het volgende:

> [!div class="checklist"] 
> * Stel een beveiligd kanaal zodanig in dat Azure Backup Server kan communiceren met VMware-servers via HTTPS. 
> * Voeg de account referenties toe aan Azure Backup Server. 
> * Voeg de vCenter toe aan Azure Backup Server. 
> * Stel een beveiligings groep in die de VMware-Vm's bevat waarvan u een back-up wilt maken, geef back-upinstellingen op en plan de back-up.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Een beveiligde verbinding met de vCenter Server maken

Azure Backup Server communiceert standaard met VMware-servers via HTTPS. Als u de HTTPS-verbinding wilt instellen, downloadt u het CA-certificaat (VMware Certificate Authority) en importeert u het op de Azure Backup Server.

### <a name="set-up-the-certificate"></a>Het certificaat instellen

1. Voer in de browser, op de Azure Backup Server, de URL van de vSphere-webclient in.

   > [!NOTE] 
   > Als de pagina aan de slag met VMware niet wordt weer gegeven, controleert u de instellingen voor de verbinding en browser proxy en probeert u het opnieuw.

1. Klik op de pagina aan de slag met VMware op **vertrouwde basis-CA-certificaten downloaden**.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/vsphere-web-client.png" alt-text="vSphere-webclient":::

1. Sla het bestand **down load. zip** op de Azure backup server machine op en pak de inhoud vervolgens uit naar de map **certs** , die de volgende bevat:

   - Basis certificaat bestand met een extensie die begint met een genummerde reeks, zoals 0 en 1.

   - Het CRL-bestand heeft een extensie die begint met een Sequence zoals. R0 of. R1.

1. Klik in de map **certificaten** met de rechter muisknop op het basis certificaat bestand en selecteer **naam wijzigen** om de extensie te wijzigen in **. CRT**.

   Het bestands pictogram wordt gewijzigd in een bestand dat een basis certificaat vertegenwoordigt.

1. Klik met de rechter muisknop op het basis certificaat en selecteer **certificaat installeren**.

1. Selecteer in de **wizard Certificaat importeren**de optie **lokale computer** als doel voor het certificaat en klik op **volgende**.

   ![Welkom bij de wizard](../backup/media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

   > [!NOTE] 
   > Als u daarom wordt gevraagd, bevestigt u dat u wijzigingen aan de computer wilt toestaan.    

1. Selecteer **alle certificaten in het onderstaande archief opslaan**en klik op **Bladeren** om het certificaat archief te kiezen.

   ![Certificaat opslag](../backup/media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

1. Selecteer **vertrouwde basis certificerings instanties** als doelmap en klik op **OK**.

1. Controleer de instellingen en klik op **volt ooien** om het certificaat te importeren.

   ![Controleren of het certificaat zich in de juiste map bevindt](../backup/media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

1. Nadat het importeren van het certificaat is bevestigd, meldt u zich aan bij de vCenter Server om te bevestigen dat uw verbinding is beveiligd.

### <a name="enable-tls-12-on-azure-backup-server"></a>TLS 1,2 inschakelen op Azure Backup Server

Voor VMWare 6,7 werd TLS ingeschakeld als communicatie protocol. 

1. Kopieer de volgende register instellingen en plak deze in Klad blok en sla het bestand op als TLS. REG zonder de extensie. txt.

   ```text
   
   Windows Registry Editor Version 5.00
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   ```

1. Klik met de rechter muisknop op de TLS. REG-bestand en selecteer **samen voegen** of **openen** om de instellingen toe te voegen aan het REGI ster.

## <a name="add-the-provisioning-ip-address-for-avs-esxi-hosts-on-azure-backup-server"></a>Voeg het IP-adres van de inrichting toe voor AVS ESXi-hosts op Azure Backup Server

Tijdens de preview-versie wordt de ESX-host niet door AVS omgezet van de virtuele machine die in het virtuele netwerk is geïmplementeerd. U moet aanvullende stappen uitvoeren om de vermelding van een hostbestand toe te voegen aan de virtuele machine van Azure Backup Server.

### <a name="identify-ip-address-for-esxi-hosts"></a>IP-adres voor ESXi-hosts identificeren

1. Open de browser en meld u aan bij de vCenter-Url's. 

   > [!TIP]
   > U kunt de Url's vinden in het artikel [verbinding maken met de lokale vCenter van uw](tutorial-access-private-cloud.md#connect-to-the-local-vcenter-of-your-private-cloud) privécloud.

1. Selecteer in de vSphere-client het cluster dat u wilt inschakelen voor back-up.

   :::image type="content" source="media/avs-backup/vsphere-client-select-host.png" alt-text="VSphere-client-host selecteren":::

1. Selecteer **Configure**  >  **netwerk**  >  **VMKernel adapters**configureren, en klik onder de lijst met apparaten op de netwerk adapter waarvoor de **inrichtings** functie is ingeschakeld en noteer het **IP-adres** en de ESXi-hostnaam.

   :::image type="content" source="media/avs-backup/vmkernel-adapters-provisioning-enabled.png" alt-text="VMKernel-adapters-ingeschakelde apparaten inrichten":::

1. Herhaal de vorige stap voor elke ESXi-host onder elk cluster dat u wilt gebruiken voor het inschakelen van back-ups.

### <a name="update-the-host-file-on-the-azure-backup-server"></a>Het hostbestand op de Azure Backup Server bijwerken

1. Open **Klad blok** als Administrator.

2. Selecteer **File > open** en zoek naar c:\Windows\System32\Drivers\etc\hosts.

3. Voeg de vermelding voor elke ESXi-host toe, samen met het IP-adres dat u in de bovenstaande sectie hebt geïdentificeerd.

4. Sla de wijzigingen op en sluit Klad blok.

## <a name="add-the-account-on-azure-backup-server"></a>Het account toevoegen op Azure Backup Server


1. Open Azure backup server en selecteer in de Azure backup server-console **beheer**  >  **productie servers**  >  **Manage VMware**.

   ![Azure Backup Server-console](../backup/media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

1. Klik in het dialoog venster **referenties beheren** op **toevoegen**.

   ![Het dialoog venster referenties Azure Backup Server beheren](../backup/media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

1. Voer bij **referentie toevoegen**een naam en een beschrijving in voor de nieuwe referentie en geef de gebruikers naam en het wacht woord op die u hebt gedefinieerd op de VMware-Server.

   > [!NOTE] 
   > Als de VMware-Server en Azure Backup Server zich niet in hetzelfde domein bevinden, geeft u het domein op in de gebruikers naam.

   ![Dialoog venster Azure Backup Server referentie toevoegen](../backup/media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

1. Klik op **toevoegen** om de nieuwe referentie toe te voegen.

   ![Het dialoog venster referenties Azure Backup Server beheren](../backup/media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Voeg de vCenter Server toe aan Azure Backup Server

1. Klik in de Azure backup server-console op **beheer**  >  **productie servers**  >  **toevoegen**.

   ![Wizard voor het toevoegen van productie servers](../backup/media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

1. Selecteer **VMware-servers** en klik op **volgende**.

   ![Wizard voor het toevoegen van productie servers](../backup/media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

1. Geef het IP-adres van de vCenter op.

   ![VMware-Server opgeven](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

1. Voer bij **SSL-poort**de poort in die wordt gebruikt om te communiceren met de vCenter.

   > [!TIP] 
   > Poort 443 is de standaard poort, maar u kunt deze wijzigen als uw vCenter luistert op een andere poort.

1. Selecteer in **referenties opgeven**de referentie die u hebt gemaakt in de vorige sectie.

1. Klik op **toevoegen** om de vCenter toe te voegen aan de lijst servers en klik op **volgende**.

   ![VMWare-Server en-referentie toevoegen](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

1. Klik op de pagina **samen vatting** op **toevoegen** om de vCenter toe te voegen aan Azure backup server.

   De nieuwe server wordt direct toegevoegd. vCenter heeft geen agent nodig.

   ![VMware-Server toevoegen aan Azure Backup Server](../backup/media/backup-azure-backup-server-vmware/tasks-screen.png)

1. Controleer de instellingen op de pagina **volt ooien** en klik vervolgens op **sluiten**.

   ![Pagina volt ooien](../backup/media/backup-azure-backup-server-vmware/summary-screen.png)

   De vCenter-Server wordt weer gegeven onder de productie server met het type VMware-Server en de status van de agent als *OK*. Als de agent status *onbekend*is, klikt u op **vernieuwen**.

## <a name="configure-a-protection-group"></a>Een beveiligings groep configureren

Beveiligings groepen verzamelen meerdere Vm's en passen dezelfde gegevens retentie en back-upinstellingen toe op alle virtuele machines in de groep.

1. Klik in de Azure backup server-console op **beveiliging**  >  **Nieuw**.

   ![Open de wizard nieuwe beveiligings groep maken](../backup/media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Klik op de welkomst pagina van de wizard **nieuwe beveiligings groep maken** op **volgende**.

   ![Dialoog venster Wizard nieuwe beveiligings groep maken](../backup/media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Selecteer op de pagina **type beveiligings groep selecteren** de optie **servers** en klik vervolgens op **volgende**. De pagina **groeps leden selecteren** wordt weer gegeven.

1. In **groeps leden selecteren**selecteert u de virtuele machines (of VM-mappen) waarvan u een back-up wilt maken. Klik op **Volgende**.

   > [!NOTE]
   > Wanneer u een map of Vm's of mappen in die map selecteert, worden ook voor back-up geselecteerd. U kunt de selectie van mappen of Vm's die u niet wilt maken, uitschakelen. Als er al een back-up van een virtuele machine of map wordt gemaakt, kunt u deze niet selecteren, waardoor er geen dubbele herstel punten worden gemaakt voor een virtuele machine.

   ![Groeps leden selecteren](../backup/media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Voer op de pagina **methode voor gegevens beveiliging selecteren** een naam in voor de beveiligings groep en de beveiligings instellingen. 

1. Stel de beveiliging op de korte termijn in op **schijf**, schakel online beveiliging in en klik vervolgens op **volgende**.

   ![Methode voor gegevensbeveiliging selecteren](../backup/media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Geef op hoelang u een back-up van de gegevens wilt maken op schijf.

   - **Bewaar** termijn: aantal dagen dat herstel punten voor de schijf worden bewaard.
   - **Snelle volledige back-up** : hoe vaak schijf herstel punten worden gemaakt. Klik op **wijzigen**om de tijden of datums te wijzigen wanneer back-ups op korte termijn worden uitgevoerd.

   :::image type="content" source="media/avs-backup/new-protection-group-specify-short-term-goals.png" alt-text="Geef uw doelen voor de korte termijn voor beveiliging op basis van schijven op":::

1. Controleer in **schijf toewijzing controleren**de schijf ruimte die is vereist voor de back-ups van de virtuele machine.

   - De aanbevolen schijf toewijzingen zijn gebaseerd op de Bewaar termijn die u hebt opgegeven, het type werk belasting en de grootte van de beveiligde gegevens. Breng de gewenste wijzigingen aan en klik op **volgende**.

   - **Gegevens grootte:** Grootte van de gegevens in de beveiligings groep.

   - **Schijf ruimte:** Aanbevolen hoeveelheid schijf ruimte voor de beveiligings groep. Als u deze instelling wilt wijzigen, moet u de totale ruimte toewijzen die iets groter is dan de hoeveelheid waarmee u de gegevens bronnen wilt schatten.

   - **Details van opslag groep:** Hier wordt de status van de opslag groep weer gegeven, met inbegrip van de totale en resterende schijf grootte.

   :::image type="content" source="media/avs-backup/review-disk-allocation.png" alt-text="De toegewezen schijf ruimte in de opslag groep controleren":::

   > [!NOTE]
   > In sommige scenario's is de gerapporteerde gegevens grootte hoger dan de werkelijke VM-grootte. We zijn op de hoogte van het probleem en kunnen momenteel worden onderzocht.

1. Geef op de pagina **methode voor maken van replica selecteren** op hoe u de eerste back-up wilt maken en klik op **volgende**.

   - De standaard instelling is **automatisch via het netwerk** en **nu**.   Als u de standaard waarde gebruikt, raden we u aan een rustige tijd op te geven. Kies **later** en geef een dag en tijd op.

   - Voor grote hoeveel heden gegevens of minder dan optimale netwerk omstandigheden kunt u overwegen om de gegevens offline te repliceren met behulp van Verwissel bare media.

   ![Methode voor maken van replica selecteren](../backup/media/backup-azure-backup-server-vmware/replica-creation.png)

1. Selecteer in **Opties voor consistentie controle**hoe en wanneer de consistentie controles moeten worden geautomatiseerd en klik op **volgende**.

   - U kunt consistentie controles uitvoeren wanneer de replica gegevens inconsistent worden, of volgens een ingesteld schema.

   - Als u geen automatische consistentie controles wilt configureren, kunt u een hand matige controle uitvoeren door met de rechter muisknop op de beveiligings groep te klikken **consistentie controle uitvoeren**.

1. Selecteer op de pagina **online beveiligings gegevens opgeven** de VM'S of VM-mappen waarvan u een back-up wilt maken en klik vervolgens op **volgende**. 

   > [!TIP]
   > U kunt de leden afzonderlijk selecteren of op **Alles selecteren** klikken om alle leden te kiezen.

   ![Gegevens voor online beveiliging opgeven](../backup/media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Op de pagina **online back-upschema opgeven** geeft u op hoe vaak u een back-up wilt maken van gegevens van de lokale opslag naar Azure en klikt u op **volgende**. 

   - Cloud herstel punten voor de gegevens die volgens het schema worden gegenereerd. 

   - Wanneer het herstel punt wordt gegenereerd, wordt het overgedragen naar de Recovery Services kluis in Azure.

   ![Online back-upschema opgeven](../backup/media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Geef op de pagina **online retentie beleid opgeven** op hoe lang u de herstel punten wilt bewaren die worden gemaakt op basis van de dagelijkse/wekelijkse/maandelijkse/jaarlijkse back-ups naar Azure en klik vervolgens op **volgende**.

   - Er is geen tijds limiet voor hoe lang u gegevens in azure kunt blijven gebruiken.

   - De enige limiet is dat u niet meer dan 9999 herstel punten per beveiligd exemplaar kunt hebben. In dit voor beeld is het beveiligde exemplaar de VMware-Server.

   ![Online Bewaar beleid opgeven](../backup/media/backup-azure-backup-server-vmware/retention-policy.png)

1. Controleer de instellingen op de pagina **samen vatting** en klik vervolgens op **groep maken**.

   ![Lid van beveiligings groep en samen vatting van instellingen](../backup/media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="monitor-with-azure-backup-server-console"></a>Bewaken met Azure Backup Server-console

Zodra u de beveiligings groep configureert om een back-up te maken van de AVS-Vm's, kunt u de status van de back-uptaak en waarschuwing controleren met behulp van de Azure Backup Server-console. Dit is wat u kunt bewaken.

- Op het tabblad waarschuwingen in het deel venster bewaking kunt u fouten, waarschuwingen en algemene informatie bewaken voor een beveiligings groep, voor een specifieke beveiligde computer of op basis van de ernst van het bericht. U kunt actieve en inactieve waarschuwingen weergeven en e-mailmeldingen instellen

- Op het tabblad taken in het deel venster bewaking kunt u taken weer geven die door Azure Backup Server worden gestart voor een specifieke beveiligde gegevens bron of beveiligings groep. U kunt de voortgang van de taak volgen of resources die worden gebruikt door taken controleren.

- In het taak gebied **beveiliging** kunt u de status van volumes en shares in de beveiligings groep controleren en configuratie-instellingen, zoals herstel instellingen, schijf toewijzing en het back-upschema controleren.

- In het taak gebied **beheer** kunt u het tabblad **schijven, online**en **agents** weer geven om de status van de schijven in de opslag groep te controleren, te registreren bij Azure en de status van de DPM-agent te implementeren.

:::image type="content" source="media/avs-backup/monitor-backup-jobs-in-mabs.png" alt-text="De status van back-uptaken in Azure Backup Server bewaken":::

## <a name="restore-vmware-virtual-machines"></a>Virtuele VMware-machines herstellen

In de Azure Backup Server Administrator-console kunt u op twee manieren herstel bare gegevens vinden: zoeken of bladeren. Wanneer u gegevens herstelt, is het mogelijk dat u de gegevens of een virtuele machine niet op dezelfde locatie wilt herstellen. Daarom ondersteunt Azure Backup Server drie herstel opties voor VMware VM-back-ups:

- **Herstel van de oorspronkelijke locatie (herstellen)** : gebruik herstellen om een beveiligde VM naar de oorspronkelijke locatie te herstellen. U kunt een virtuele machine alleen op de oorspronkelijke locatie herstellen als er geen schijven zijn toegevoegd of verwijderd sinds de back-up is gemaakt. Als er schijven zijn toegevoegd of verwijderd, moet u alternatieve locatie herstel gebruiken.

- **Alternatieve locatie herstel (ALR)** : wanneer de oorspronkelijke VM ontbreekt of als u de oorspronkelijke VM niet wilt verstoren, herstelt u de virtuele machine naar een andere locatie. Als u een virtuele machine naar een alternatieve locatie wilt herstellen, moet u de locatie van een ESXi-host, resource groep, map en de opslag-Data Store en het pad opgeven. Azure Backup Server voegt '-hersteld ' toe aan de naam van de virtuele machine om te voor komen dat de herstelde VM wordt onderscheiden van de oorspronkelijke VM.

- **Herstel van afzonderlijke bestands locaties (ILR)** : als de beveiligde virtuele machine een Windows Server-VM is, kunnen afzonderlijke bestanden/mappen in de virtuele machine worden hersteld met behulp van de ILR-mogelijkheid van Azure backup server. Zie de procedure verderop in dit artikel voor meer informatie over het herstellen van afzonderlijke bestanden. Het herstellen van een afzonderlijk bestand van een virtuele machine is alleen beschikbaar voor Windows-VM'S en schijf herstel punten.

### <a name="restore-a-recovery-point"></a>Herstel punt herstellen

1. Klik in de Azure Backup Server Administrator-console op herstel weergave. 

1. Gebruik het Blader venster, blader of filter om de virtuele machine te vinden die u wilt herstellen. Wanneer u een virtuele machine of map selecteert, worden in het deel venster herstel punten voor de beschik bare herstel punten weer gegeven.

   ![Beschik bare herstel punten](../backup/media/restore-azure-backup-server-vmware/recovery-points.png)

1. In het veld **herstel punten voor** kunt u de kalender en vervolg keuzemenu's gebruiken om een datum te selecteren waarop een herstel punt is gemaakt. De gemarkeerde kalender datums bevatten beschik bare herstel punten. U kunt ook met de rechter muisknop op de virtuele machine klikken en **alle herstel punten weer geven** selecteren en vervolgens het herstel punt selecteren in de lijst.

   > [!NOTE] 
   > Voor beveiliging op de korte termijn selecteert u een herstel punt op basis van een schijf voor sneller herstel. Nadat herstel punten voor de korte termijn zijn verlopen, ziet u alleen online herstel punten om te herstellen.

1. Voordat u herstelt vanaf een online herstel punt, moet u ervoor zorgen dat de faserings locatie voldoende beschik bare ruimte bevat voor de volledige niet-gecomprimeerde grootte van de virtuele machine die u wilt herstellen. De faserings locatie kan worden weer gegeven/gewijzigd door de **wizard abonnements instellingen configureren**uit te voeren.

   :::image type="content" source="media/avs-backup/mabs-recovery-folder-settings.png" alt-text="Instellingen voor de Recovery-map Azure Backup Server":::

1. Klik op **herstellen** om de **wizard herstellen**te openen.

   ![Wizard herstel, herstel selectie controleren](../backup/media/restore-azure-backup-server-vmware/recovery-wizard.png)

1. Klik op **volgende** om verder te gaan naar het scherm **herstel opties opgeven** en klik nogmaals op **volgende** om naar het scherm **herstel type selecteren** te gaan. 

   > [!NOTE]
   > VMware-workloads bieden geen ondersteuning voor het inschakelen van netwerk bandbreedte regeling.

1. Kies op de pagina **type herstel bewerking selecteren** of u wilt herstellen naar het oorspronkelijke exemplaar of een nieuwe locatie en klik vervolgens op **volgende**.

   - Als u **herstellen naar oorspronkelijk exemplaar**kiest, hoeft u geen keuzes meer te maken in de wizard. De gegevens voor de oorspronkelijke instantie worden gebruikt.

   - Als u **herstellen als virtuele machine op een wille keurige host**kiest, geeft u op het scherm **doel opgeven** de gegevens op voor de **ESXi-host, de resource groep**, de map en het **pad**.

   ![Herstel type selecteren](../backup/media/restore-azure-backup-server-vmware/recovery-type.png)

1. Controleer uw instellingen op de pagina **samen vatting** en klik op **herstellen** om het herstel proces te starten. 

   Het scherm herstel status toont de voortgang van de herstel bewerking.

### <a name="restore-an-individual-file-from-a-vm"></a>Een afzonderlijk bestand herstellen vanaf een virtuele machine

U kunt afzonderlijke bestanden herstellen vanaf een herstel punt van een beveiligde virtuele machine. Deze functie is alleen beschikbaar voor virtuele machines met Windows Server. Het herstellen van afzonderlijke bestanden is vergelijkbaar met het herstellen van de volledige virtuele machine, behalve dat u naar de VMDK bladert en de gewenste bestanden zoekt voordat u het herstel proces start. 

> [!NOTE]
> Het herstellen van een afzonderlijk bestand van een virtuele machine is alleen beschikbaar voor Windows-VM'S en schijf herstel punten.

1. Klik in de Azure Backup Server Administrator-console op **herstel** weergave.

1. Blader in het **Blader** venster en filter om de virtuele machine te vinden die u wilt herstellen. Wanneer u een virtuele machine of map selecteert, worden in het deel venster herstel punten voor de beschik bare herstel punten weer gegeven.

   ![Beschik bare herstel punten](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

1. In het deel venster **herstel punten voor** kunt u de kalender gebruiken om de datum te selecteren die de gewenste herstel punten bevat. Afhankelijk van hoe het back-upbeleid is geconfigureerd, kunnen de datums meer dan één herstel punt hebben. 

1. Nadat u de dag hebt geselecteerd waarop het herstel punt is gemaakt, moet u ervoor zorgen dat u de juiste **herstel tijd**hebt gekozen. 

   > [!NOTE]
   > Als de geselecteerde datum meerdere herstel punten heeft, kiest u het herstel punt door deze te selecteren in de vervolg keuzelijst herstel tijd. 

   Nadat u het herstel punt hebt gekozen, wordt de lijst met herstel bare items weer gegeven in het deel venster **pad** .  

1. Als u de bestanden wilt vinden die u wilt herstellen, dubbelklikt u in het deel venster **pad** op het item in de kolom herstel **bare items** om het te openen en selecteert u het bestand of de mappen die u wilt herstellen. Als u meerdere items wilt selecteren, houdt u de **CTRL** -toets ingedrukt terwijl u elk item selecteert. In het deel venster **pad** kunt u zoeken naar de lijst met bestanden of mappen die worden weer gegeven in de kolom **herstelbaar item** .
    
   > [!NOTE]
   > In de **onderstaande Zoek lijst** worden niet naar submappen gezocht. Dubbel klik op de map om in submappen te zoeken. Gebruik de knop **omhoog** om van een onderliggende map naar de bovenliggende map te gaan. U kunt meerdere items selecteren (bestanden en mappen), maar ze moeten zich in dezelfde bovenliggende map bevinden. U kunt geen items uit meerdere mappen in dezelfde herstel taak herstellen.

   ![Selectie voor herstel controleren](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

1. Wanneer u de item (s) voor herstel hebt geselecteerd, klikt u in het lint van de Administrator-console op **herstellen** om de **wizard herstellen**te openen. In de herstel wizard ziet u in het scherm **herstel selectie controleren** de geselecteerde items die moeten worden hersteld.

1. Voer op het scherm **herstel opties opgeven** een van de volgende handelingen uit:

   - Klik op **wijzigen** om de netwerk bandbreedte regeling in te scha kelen.  In het dialoog venster gashendel selecteert u **beperking van netwerk bandbreedte gebruik inschakelen** om deze in te scha kelen. Wanneer deze functie is ingeschakeld, configureert u de **instellingen** en het **werk schema**.
    
   - Klik op **volgende** om netwerk beperking uitgeschakeld te laten.

1. Klik in het scherm **herstel type selecteren** op **volgende**. U kunt uw bestanden of mappen alleen herstellen naar een netwerkmap.

1. Klik op het scherm **doel opgeven** op **Bladeren** om een netwerk locatie te zoeken voor uw bestanden of mappen. Azure Backup Server maakt een map waarin alle herstelde items worden gekopieerd. De mapnaam heeft het voor voegsel, MABS_day-maand-jaar. Wanneer u een locatie voor de herstelde bestanden of map selecteert, worden de gegevens voor die locatie (bestemming, doelpad en beschik bare ruimte) weer gegeven.

   ![Geef de locatie op voor het herstellen van bestanden](../backup/media/restore-azure-backup-server-vmware/specify-destination.png)

1. Kies op het scherm **herstel opties opgeven** welke beveiligings instelling u wilt Toep assen. U kunt ervoor kiezen om de beperking van het netwerk bandbreedte gebruik te wijzigen, maar de beperking is standaard uitgeschakeld. Ook **San-herstel** en- **meldingen** zijn niet ingeschakeld.

1. Controleer uw instellingen op het scherm **samen vatting** en klik op **herstellen** om het herstel proces te starten. Het scherm **herstel status** toont de voortgang van de herstel bewerking.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de hand leiding voor probleem oplossing voor Azure Backup Server voor het oplossen van problemen met het instellen van back-ups.



> [!div class="nextstepaction"]
> [Gids voor probleem oplossing voor Azure Backup Server](../backup/backup-azure-mabs-troubleshoot.md)
