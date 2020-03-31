---
title: VM's herstellen met de Azure-portal
description: Een virtuele Azure-machine herstellen vanaf een herstelpunt met behulp van de Azure-portal
ms.reviewer: geg
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 05ad6c4eab61ccf5bc0884da63ebda7ba76ac583
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273551"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Azure VM-gegevens herstellen in Azure-portal

In dit artikel wordt beschreven hoe u Azure VM-gegevens herstelt van de herstelpunten die zijn opgeslagen in de kluizen [van Azure Backup](backup-overview.md) Recovery Services.

## <a name="restore-options"></a>Herstelopties

Azure Backup biedt een aantal manieren om een VM te herstellen.

**Herstel, optie** | **Details**
--- | ---
**Een nieuwe VM maakt** | Maakt snel een basis-VM up and running vanaf een herstelpunt.<br/><br/> U een naam voor de VM opgeven, de brongroep en het virtuele netwerk (VNet) selecteren waarin deze wordt geplaatst en een opslagaccount opgeven voor de herstelde vm. De nieuwe vm moet worden gemaakt in dezelfde regio als de bron-VM.
**Schijf herstellen** | Hiermee herstelt u een VM-schijf, die vervolgens kan worden gebruikt om een nieuwe virtuele machine te maken.<br/><br/> Azure Backup biedt een sjabloon waarmee u een vm aanpassen en maken. <br/><br> Met de hersteltaak genereert u een sjabloon die u downloaden en gebruiken om aangepaste VM-instellingen op te geven en een vm te maken.<br/><br/> De schijven worden gekopieerd naar de resourcegroep die u opgeeft.<br/><br/> U de schijf ook koppelen aan een bestaande virtuele machine of een nieuwe virtuele machine maken met PowerShell.<br/><br/> Deze optie is handig als u de vm wilt aanpassen, configuratie-instellingen wilt toevoegen die er niet waren op het moment van back-up, of instellingen wilt toevoegen die moeten worden geconfigureerd met behulp van de sjabloon of PowerShell.
**Bestaande schijf vervangen** | U een schijf herstellen en deze gebruiken om een schijf op de bestaande vm te vervangen.<br/><br/> De huidige VM moet bestaan. Als deze optie is verwijderd, kan deze optie niet worden gebruikt.<br/><br/> Azure Backup maakt een momentopname van de bestaande VM voordat u de schijf vervangt en slaat deze op in de opgeeflocatie die u opgeeft. Bestaande schijven die met de VM zijn verbonden, worden vervangen door het geselecteerde herstelpunt.<br/><br/> De momentopname wordt gekopieerd naar de kluis en bewaard in overeenstemming met het bewaarbeleid. <br/><br/> Na de bewerking van de schijf vervangen, blijft de oorspronkelijke schijf behouden in de brongroep. U ervoor kiezen om de originele schijven handmatig te verwijderen als ze niet nodig zijn. <br/><br/>Bestaande bestaande vervangen wordt ondersteund voor niet-versleutelde beheerde VM's. Het wordt niet ondersteund voor onbeheerde schijven, [gegeneraliseerde VM's](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)of voor VM's [die zijn gemaakt met behulp van aangepaste afbeeldingen.](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/)<br/><br/> Als het herstelpunt meer of minder schijven heeft dan de huidige VM, wordt het aantal schijven in het herstelpunt alleen de VM-configuratie weergegeven.<br><br> Bestaande bestaande gegevens vervangen wordt niet ondersteund door gekoppelde resources (zoals [door de gebruiker toegewezen beheerde identiteit](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) of Key [Vault),](https://docs.microsoft.com/azure/key-vault/key-vault-overview)omdat de back-upclient-app geen machtigingen heeft voor deze bronnen tijdens het uitvoeren van het herstel.
**Regio kruisen (secundaire regio)** | Herstel van verschillende regio's kan worden gebruikt om Azure VM's te herstellen in het secundaire gebied, een [azure-gekoppelde regio.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)<br><br> U alle Azure VM's voor het geselecteerde herstelpunt herstellen als de back-up in de secundaire regio wordt uitgevoerd.<br><br> Deze functie is beschikbaar voor de onderstaande opties:<br> * [Een VM maken](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> * [Schijven herstellen](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> We ondersteunen momenteel de optie [Bestaande schijven vervangen](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks) niet.<br><br> Machtigingen<br> De herstelbewerking op secundair gebied kan worden uitgevoerd door back-upbeheerders en app-beheerders.

> [!NOTE]
> U ook specifieke bestanden en mappen herstellen op een Azure VM. [Meer informatie](backup-azure-restore-files-from-vm.md).
>
> Als u de [nieuwste versie](backup-instant-restore-capability.md) van Azure Backup voor Azure VM's uitvoert (instant restore genoemd), worden momentopnamen maximaal zeven dagen bewaard en u een VM herstellen van momentopnamen voordat de back-upgegevens naar de kluis worden verzonden. Als u een VM wilt herstellen van een back-up van de afgelopen zeven dagen, is deze sneller te herstellen vanaf de momentopname en niet vanuit de kluis.

## <a name="storage-accounts"></a>Opslagaccounts

Enkele details over opslagaccounts:

- **VM maken:** Wanneer u een nieuwe vm maakt, wordt de vm in het opgegeven opslagaccount geplaatst.
- **Schijf herstellen:** Wanneer u een schijf herstelt, wordt de schijf gekopieerd naar het opslagaccount dat u opgeeft. Met de hersteltaak genereert u een sjabloon die u downloaden en gebruiken om aangepaste VM-instellingen op te geven. Deze sjabloon wordt in het opgegeven opslagaccount geplaatst.
- **Schijf vervangen:** Wanneer u een schijf op een bestaande vm vervangt, maakt Azure Backup een momentopname van de bestaande vm voordat u de schijf vervangt. De momentopname wordt opgeslagen in de faseringslocatie (opslagaccount) die u opgeeft. Dit opslagaccount wordt gebruikt om de momentopname tijdelijk op te slaan tijdens het herstelproces en we raden u aan een nieuw account aan te maken om dit te doen, dat achteraf gemakkelijk kan worden verwijderd.
- **Locatie opslagaccount**: het opslagaccount moet zich in dezelfde regio als de kluis bevinden. Alleen deze accounts worden weergegeven. Als er geen opslagaccounts op de locatie zijn, moet u er een maken.
- **Opslagtype:** Blob-opslag wordt niet ondersteund.
- **Redundantie van opslag**: Zone redundante opslag (ZRS) wordt niet ondersteund. De replicatie- en redundantiegegevens voor het account worden tussen haakjes weergegeven na de accountnaam.
- **Premium opslag**:
  - Bij het herstellen van niet-premium VM's worden premium-opslagaccounts niet ondersteund.
  - Bij het herstellen van beheerde VM's worden premium-opslagaccounts die zijn geconfigureerd met netwerkregels, niet ondersteund.

## <a name="before-you-start"></a>Voordat u begint

Als u een VM wilt herstellen (een nieuwe vm maken), controleert u [permissions](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) of u over de juiste rbac-machtigingen (role-based access control) beschikt voor de bewerking VM herstellen.

Als u geen machtigingen hebt, u [een schijf herstellen](#restore-disks)en vervolgens nadat de schijf is hersteld, u de [sjabloon](#use-templates-to-customize-a-restored-vm) gebruiken die is gegenereerd als onderdeel van de herstelbewerking om een nieuwe vm te maken.

## <a name="select-a-restore-point"></a>Een herstelpunt selecteren

1. Klik in de kluis die is gekoppeld aan de VM die u wilt herstellen op **Back-upitems** > **Azure Virtual Machine**.
2. Klik op een virtuele machine. Standaard worden op het VM-dashboard herstelpunten van de afgelopen 30 dagen weergegeven. U herstelpunten ouder dan 30 dagen weergeven of filteren om herstelpunten te zoeken op basis van datums, tijdbereiken en verschillende typen momentopnameconsistentie.
3. Als u de vm wilt herstellen, klikt u op **VM herstellen**.

    ![Herstelpunt](./media/backup-azure-arm-restore-vms/restore-point.png)

4. Selecteer een herstelpunt dat u wilt gebruiken voor het herstel.

## <a name="choose-a-vm-restore-configuration"></a>Een configuratie voor vm-herstel kiezen

1. Selecteer **in Herstelconfiguratie**een hersteloptie:
    - **Nieuw maken**: gebruik deze optie als u een nieuwe virtuele machine wilt maken. U een vm maken met eenvoudige instellingen, een schijf herstellen en een aangepaste vm maken.
    - **Bestaande vervangen**: gebruik deze optie als u schijven op een bestaande virtuele machine wilt vervangen.

        ![Wizard Configuratie herstellen](./media/backup-azure-arm-restore-vms/restore-configuration.png)

2. Geef instellingen op voor de geselecteerde hersteloptie.

## <a name="create-a-vm"></a>Een virtuele machine maken

Als een van de [herstelopties](#restore-options)u snel een VM maken met basisinstellingen vanaf een herstelpunt.

1. Selecteer **in Herstelconfiguratie** > **Nieuw** > **hersteltype maken**de optie Een virtuele machine **maken**.
2. Geef in **De naam van**de virtuele machine een VM op die niet in het abonnement bestaat.
3. Selecteer in **resourcegroep**een bestaande resourcegroep voor de nieuwe virtuele machine of maak een nieuwe groep met een wereldwijd unieke naam. Als u een al bestaande naam toewijst, wijst Azure de groep dezelfde naam toe als de VM.
4. Selecteer **in virtueel netwerk**het VNet waarin de VM wordt geplaatst. Alle VNets die aan het abonnement zijn gekoppeld, worden weergegeven. Selecteer het subnet. Het eerste subnet is standaard geselecteerd.
5. Geef **in Opslaglocatie**het opslagaccount voor de VM op. [Meer informatie](#storage-accounts).

    ![Wizard Configuratie herstellen](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. Selecteer **OK**in **Herstelconfiguratie**. Klik in **Herstellen**op **Herstellen** om de herstelbewerking te activeren.

## <a name="restore-disks"></a>Schijven herstellen

Als een van de [herstelopties](#restore-options)u een schijf maken vanaf een herstelpunt. Vervolgens u met de schijf een van de volgende handelingen doen:

- Gebruik de sjabloon die wordt gegenereerd tijdens de herstelbewerking om instellingen aan te passen en vm-implementatie te activeren. U bewerkt de standaardsjablooninstellingen en verzendt de sjabloon voor vm-implementatie.
- [Herstelde schijven koppelen](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) aan een bestaande virtuele machine.
- [Maak een nieuwe vm](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks) van de herstelde schijven met PowerShell.

1. Selecteer **schijven herstellen**in **Herstelconfiguratie** > **Nieuw** > **hersteltype maken**.
2. Selecteer in **de groep Resource**een bestaande resourcegroep voor de herstelde schijven of maak een nieuwe groep met een wereldwijd unieke naam.
3. Geef **in opslagaccount**het account op waarop de VHD's moeten worden gekopieerd. [Meer informatie](#storage-accounts).

    ![Herstelconfiguratie voltooid](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. Selecteer **OK**in **Herstelconfiguratie**. Klik in **Herstellen**op **Herstellen** om de herstelbewerking te activeren.

Wanneer uw virtuele machine beheerde schijven gebruikt en u de optie **Virtuele machine maken** selecteert, maakt Azure Backup geen gebruik van het opgegeven opslagaccount. In het geval van **Schijven herstellen** en **Instant Restore**wordt het opslagaccount alleen gebruikt voor het opslaan van de sjabloon. Beheerde schijven worden gemaakt in de opgegeven resourcegroep.
Wanneer uw virtuele machine onbeheerde schijven gebruikt, worden deze als blobs hersteld naar het opslagaccount.

### <a name="use-templates-to-customize-a-restored-vm"></a>Sjablonen gebruiken om een herstelde virtuele machine aan te passen

Nadat de schijf is hersteld, gebruikt u de sjabloon die is gegenereerd als onderdeel van de herstelbewerking om een nieuwe vm aan te passen en te maken:

1. Open **Taakgegevens herstellen** voor de desbetreffende taak.

2. Selecteer **In Taakgegevens herstellen**de optie Sjabloon **implementeren** om sjabloonimplementatie te starten.

    ![Taakdrill-down herstellen](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

3. Als u de vm-instelling in de sjabloon wilt aanpassen, klikt u op **Sjabloon bewerken**. Als u meer aanpassingen wilt toevoegen, klikt u op **Parameters bewerken**.
    - [Meer informatie](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) over het implementeren van resources uit een aangepaste sjabloon.
    - [Meer informatie](../azure-resource-manager/templates/template-syntax.md) over het ontwerpen van sjablonen.

   ![Sjabloonimplementatie laden](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Voer de aangepaste waarden voor de VM in, accepteer de **algemene voorwaarden en** klik op **Kopen.**

   ![Sjabloonimplementatie verzenden](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>Bestaande schijven vervangen

Als een van de [herstelopties](#restore-options)u een bestaande VM-schijf vervangen door het geselecteerde herstelpunt. [Bekijk](#restore-options) alle herstelopties.

1. Klik **in Herstelconfiguratie**op **Bestaande vervangen**.
2. Selecteer **schijf/s vervangen**in **Type herstellen**. Dit is het herstelpunt dat wordt gebruikt ter vervanging van bestaande VM-schijven.
3. Geef in **Staging Location**op waar momentopnamen van de huidige beheerde schijven moeten worden opgeslagen tijdens het herstelproces. [Meer informatie](#storage-accounts).

   ![Configuratiewizard Bestaande vervangen](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="cross-region-restore"></a>Regio-herstel

Als een van de [herstelopties](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-options)u met Cross Region Restore (CRR) Azure VM's herstellen in een secundaire regio, een gekoppelde Azure-regio.

Als u tijdens de preview aan boord wilt gaan van de functie, leest u de [sectie Voor het begin](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-cross-region-restore).

Als u wilt weten of CRR is ingeschakeld, volgt u de instructies in [Cross Region Restore configureren](backup-create-rs-vault.md#configure-cross-region-restore)

### <a name="view-backup-items-in-secondary-region"></a>Back-upitems weergeven in secundair e-gebied

Als CRR is ingeschakeld, u de back-upitems in het secundaire gebied bekijken.

1. Ga vanuit de portal naar Vault**Backup-items** **van Recovery Services** > 
2. Klik **op Secundair gebied** om de items in het secundaire gebied weer te geven.

![Virtuele machines in secundaire regio](./media/backup-azure-arm-restore-vms/secbackedupitem.png)

![Secundair e-regio selecteren](./media/backup-azure-arm-restore-vms/backupitems-sec.png)

### <a name="restore-in-secondary-region"></a>Herstellen in secundair e-gebied

De gebruikerservaring van het secundaire gebied herstelt de gebruikerservaring als de primaire regio en herstelt de gebruikerservaring. Wanneer u details configureert in het beheer van de herstelconfiguratie om uw herstel te configureren, wordt u gevraagd alleen secundaire regioparameters op te geven.

![Kies VM om te herstellen](./media/backup-azure-arm-restore-vms/sec-restore.png)

![Herstelpunt selecteren](./media/backup-azure-arm-restore-vms/sec-rp.png)

![Configuratie herstellen](./media/backup-azure-arm-restore-vms/rest-config.png)

![Melding herstel herstellen in uitvoering activeren](./media/backup-azure-arm-restore-vms/restorenotifications.png)

- Als u een vm wilt herstellen en maken, [raadpleegt](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm)u Een vm maken .
- Als u wilt herstellen als schijf, [raadpleegt](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks)u Schijven herstellen .

>[!NOTE]
>Nadat het herstel is geactiveerd en in de fase van gegevensoverdracht, kan de hersteltaak niet worden geannuleerd.

### <a name="monitoring-secondary-region-restore-jobs"></a>Toezicht op secundaire regio herstel banen

1. Ga vanuit de portal naar **Recovery Services vault** > **Backup Jobs**
2. Klik **op Secundair gebied** om de items in het secundaire gebied weer te geven.

![Back-uptaken gefilterd](./media/backup-azure-arm-restore-vms/secbackupjobs.png)

## <a name="restore-vms-with-special-configurations"></a>VM's herstellen met speciale configuraties

Er zijn een aantal veelvoorkomende scenario's waarin u mogelijk VM's moet herstellen.

**Scenario** | **Richtlijnen**
--- | ---
**VM's herstellen met hybride gebruiksvoordeel** | Als een Windows-vm [hub-licenties (Hybrid Use Benefit)](../virtual-machines/windows/hybrid-use-benefit-licensing.md)gebruikt, herstelt u de schijven en maakt u een nieuwe virtuele machine met de meegeleverde sjabloon (met **licentietype** ingesteld op **Windows_Server)** of PowerShell.  Deze instelling kan ook worden toegepast na het maken van de VM.
**VM's herstellen tijdens een Azure-datacenterramp** | Als de kluis GRS gebruikt en het primaire datacenter voor de VM uitvalt, ondersteunt Azure Backup het herstellen van back-up VM's naar het gekoppelde datacenter. U selecteert een opslagaccount in het gekoppelde datacenter en herstelt als normaal. Azure Backup gebruikt de compute-service in het gekoppelde gebied om de herstelde VM te maken. [Meer informatie](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md) over de veerkracht van datacenters.
**VM voor één domeincontroller in één domein herstellen** | Herstel de VM net als elke andere VM. Opmerking:<br/><br/> Vanuit het perspectief van Active Directory is de Azure VM net als elke andere VM.<br/><br/> Directory Services Restore Mode (DSRM) is ook beschikbaar, zodat alle Active Directory-herstelscenario's levensvatbaar zijn. [Meer informatie](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps) over back-up- en hersteloverwegingen voor gevirtualiseerde domeincontrollers.
**Meerdere VM's voor domeincontroller in één domein herstellen** | Als andere domeincontrollers in hetzelfde domein via het netwerk kunnen worden bereikt, kan de domeincontroller worden hersteld zoals elke virtuele machine. Als dit de laatst overgebleven domeincontroller in het domein is of als er een herstel in een geïsoleerd netwerk wordt uitgevoerd, gebruikt u een [forestherstel](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Meerdere domeinen in één forest herstellen** | Wij raden een [bosherstel](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)aan.
**Bare-metal herstellen** | Het grote verschil tussen Azure VM's en on-premises hypervisors is dat er geen VM-console beschikbaar is in Azure. Een console is vereist voor bepaalde scenario's, zoals herstellen met behulp van een back-up van het back-uptype Back-up van het back-uptype Back-up (Bare-metal Recovery).A console is required for certain scenarios, such as recovering by using a bare-metal recovery (BMR)-type backup. Vm-herstel uit de kluis is echter een volledige vervanging voor BMR.
**VM's herstellen met speciale netwerkconfiguraties** | Speciale netwerkconfiguraties omvatten VM's met interne of externe taakverdeling, met behulp van meerdere NICS- of meerdere gereserveerde IP-adressen. U herstelt deze VM's met de [optie Schijf herstellen](#restore-disks). Met deze optie wordt een kopie van de VHD's naar het opgegeven opslagaccount gemaakt en u vervolgens een VM maken met een [interne](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/) of [externe](/azure/load-balancer/quickstart-create-standard-load-balancer-powershell) load balancer, [meerdere NICS](../virtual-machines/windows/multiple-nics.md)of [meerdere gereserveerde IP-adressen,](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md)in overeenstemming met uw configuratie.
**Network Security Group (NSG) op NIC/Subnet** | Azure VM-back-up ondersteunt back-up en herstel van NSG-informatie op vnet-, subnet- en NIC-niveau.
**Zone vastgemaakte VM's** | Azure Backup ondersteunt back-up en herstel van gezoneerde vastgemaakte VM's. [Meer informatie](https://azure.microsoft.com/global-infrastructure/availability-zones/)

## <a name="track-the-restore-operation"></a>De herstelbewerking bijhouden

Nadat u de herstelbewerking hebt geactiveerd, maakt de back-upservice een taak voor het bijhouden. Azure Backup geeft meldingen weer over de taak in de portal. Als ze niet zichtbaar zijn, selecteert u het **symbool Meldingen** en selecteert u **Alle taken weergeven** om de status Proces herstellen weer te geven.

![Geactiveerd herstellen](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 Volg het herstel als volgt:

1. Als u bewerkingen voor de taak wilt weergeven, klikt u op de hyperlink met meldingen. U ook in de kluis op **Back-uptaken**klikken en vervolgens op de relevante vm klikken.

    ![Lijst met VM's in een kluis](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

2. Als u de voortgang van het herstel wilt controleren, klikt u op een hersteltaak met de status **In-progress**. Hiermee wordt de voortgangsbalk weergegeven, waarin informatie wordt weergegeven over de herstelvoortgang:

    - **Geschatte hersteltijd**: geeft in eerste instantie de tijd die nodig is om de herstelbewerking te voltooien. Naarmate de bewerking vordert, vermindert de tijd en bereikt de nul wanneer de herstelbewerking is voltooid.
    - **Percentage herstel**. Hiermee wordt het percentage herstelbewerking weergegeven dat is uitgevoerd.
    - **Aantal overgeboekte bytes:** Als u herstelt door een nieuwe virtuele machine te maken, worden de bytes weergegeven die zijn overgedragen op het totale aantal bytes dat moet worden overgedragen.

## <a name="post-restore-steps"></a>Stappen na herstel

Er zijn een aantal dingen op te merken na het herstellen van een VM:

- Extensies die aanwezig zijn tijdens de back-upconfiguratie zijn geïnstalleerd, maar niet ingeschakeld. Als u een probleem ziet, installeert u de extensies opnieuw.
- Als de back-up VM een statisch IP-adres had, heeft de herstelde VM een dynamisch IP-adres om conflicten te voorkomen. U [een statisch IP-adres toevoegen aan de herstelde vm.](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkinterfaceipconfig?view=azps-3.5.0#description)
- Een herstelde vm heeft geen beschikbaarheidsset. Als u de optie Herstelschijf gebruikt, u [een beschikbaarheidsset opgeven](../virtual-machines/windows/tutorial-availability-sets.md) wanneer u een vm van de schijf maakt met de meegeleverde sjabloon of PowerShell.
- Als u een op cloud init gebaseerde Linux-distributie gebruikt, zoals Ubuntu, wordt het wachtwoord om veiligheidsredenen geblokkeerd na het herstel. Gebruik de VMAccess-extensie op de herstelde VM om het wachtwoord opnieuw in te [stellen.](../virtual-machines/linux/reset-password.md) We raden u aan SSH-sleutels te gebruiken voor deze distributies, zodat u het wachtwoord niet opnieuw hoeft in te stellen nadat het herstel is hersteld.
- Als u geen toegang hebt tot VM nadat deze is hersteld nadat deze is hersteld omdat vm de relatie met de domeincontroller heeft verbroken, voert u de onderstaande stappen uit om de VM weer te geven:
  - Os-schijf als gegevensschijf koppelen aan een herstelde virtuele machine.
  - Vm-agent handmatig installeren als Azure Agent niet reageert door deze [koppeling](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline)te volgen.
  - Seriële consoletoegang op VM inschakelen om opdrachtregeltoegang tot VM toe te staan

  ```cmd
    bcdedit /store <drive letter>:\boot\bcd /enum
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<<BOOT LOADER IDENTIFIER>>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

  - Wanneer de VM opnieuw is opgebouwd, gebruikt u Azure-portal om het lokale beheerdersaccount en het wachtwoord opnieuw in te stellen
  - Seriële consoletoegang en CMD gebruiken om VM uit domein te verwijderen

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```

- Zodra de VM is gedesjoind en opnieuw is gestart, u RDP met vm met lokale beheerdersreferenties met succes invoeren en opnieuw deelnemen aan VM terug naar het domein.

## <a name="backing-up-restored-vms"></a>Back-ups maken van herstelde VM's

- Als u een vm hebt hersteld naar dezelfde resourcegroep met dezelfde naam als de oorspronkelijk back-upvm, wordt de back-up na herstel voortgezet op de vm.
- Als u de VM hebt hersteld naar een andere resourcegroep of als u een andere naam hebt opgegeven voor de herstelde vm, moet u een back-up instellen voor de herstelde vm.

## <a name="next-steps"></a>Volgende stappen

- Als u problemen ondervindt tijdens het herstelproces, [controleert u](backup-azure-vms-troubleshoot.md#restore) veelvoorkomende problemen en fouten.
- Nadat de VM is hersteld, leert u meer over [het beheren van virtuele machines](backup-azure-manage-vms.md)
