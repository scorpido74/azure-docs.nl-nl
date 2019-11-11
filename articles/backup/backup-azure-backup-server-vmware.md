---
title: Back-ups maken van VMware-Vm's met Azure Backup Server
description: In dit artikel leert u hoe u Azure Backup Server kunt gebruiken om een back-up te maken van virtuele VMware-machines die op een VMware vCenter/ESXi-server worden uitgevoerd.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: dacurwin
ms.openlocfilehash: df41907ee10b54ab3bfaeb548e085617f7d79084
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903235"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Back-ups maken van VMware-Vm's met Azure Backup Server

In dit artikel wordt uitgelegd hoe u een back-up maakt van virtuele VMware-machines die worden uitgevoerd op VMware ESXi hosts/vCenter Server naar Azure met Azure Backup Server.

In dit artikel wordt uitgelegd hoe u:

- Stel een beveiligd kanaal zodanig in dat Azure Backup Server kan communiceren met VMware-servers via HTTPS.
- Stel een VMware-account in dat Azure Backup Server gebruikt voor toegang tot de VMware-Server.
- Voeg de account referenties toe aan Azure Backup.
- Voeg de vCenter-of ESXi-server toe aan Azure Backup Server.
- Stel een beveiligings groep in die de VMware-Vm's bevat waarvan u een back-up wilt maken, geef back-upinstellingen op en plan de back-up.

## <a name="before-you-start"></a>Voordat u begint

- Controleer of u een versie van vCenter-ESXi gebruikt die wordt ondersteund voor back-up-versies 6,5, 6,0 en 5,5.
- Zorg ervoor dat u Azure Backup Server hebt ingesteld. Als u dat nog [niet hebt gedaan, moet u dat doen](backup-azure-microsoft-azure-backup.md) voordat u begint. U moet Azure Backup Server uitvoeren met de nieuwste updates.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Een beveiligde verbinding met de vCenter Server maken

Azure Backup Server communiceert standaard met VMware-servers via HTTPS. Als u de HTTPS-verbinding wilt instellen, downloadt u het CA-certificaat (VMware Certificate Authority) en importeert u het op de Azure Backup Server.

### <a name="before-you-begin"></a>Voordat u begint

- Als u HTTPS niet wilt gebruiken, kunt u [HTTPS-certificaat validatie uitschakelen voor alle VMware-servers](backup-azure-backup-server-vmware.md#disable-https-certificate-validation).
- U maakt doorgaans vanuit een browser op de Azure Backup Server machine een verbinding met de vCenter/ESXi-server met behulp van de vSphere-webclient. De eerste keer dat u dit doet, is de verbinding niet beveiligd en ziet u het volgende.
- Het is belang rijk om te begrijpen hoe Azure Backup Server back-ups verwerkt.
  - Als eerste stap Azure Backup Server back-ups maken van gegevens naar de lokale schijf opslag. Azure Backup Server gebruikt een opslag groep, een set schijven en volumes waarop Azure Backup Server schijf herstel punten voor de beveiligde gegevens opslaat. De opslag groep kan direct Attached Storage (DAS), een Fibre Channel-SAN of een iSCSI-opslag apparaat of SAN zijn. Het is belang rijk om ervoor te zorgen dat u voldoende opslag ruimte hebt voor lokale back-ups van uw VMware VM-gegevens.
  - Azure Backup Server vervolgens een back-up van de lokale schijf opslag naar Azure.
  - [Krijg hulp](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1807#figure-out-how-much-storage-space-you-need) bij het bepalen van de hoeveelheid opslag ruimte die u nodig hebt. De informatie is voor DPM, maar kan ook worden gebruikt voor Azure Backup Server.

### <a name="set-up-the-certificate"></a>Het certificaat instellen

Stel als volgt een beveiligd kanaal in:

1. Voer in de browser op Azure Backup Server de URL van de vSphere-webclient in. Als de aanmeldings pagina niet wordt weer gegeven, controleert u de instellingen voor de verbinding en browser proxy.

    ![vSphere-webclient](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. Klik op de aanmeldings pagina van de vSphere-WebClient op **vertrouwde basis-CA-certificaten downloaden**.

    ![Vertrouwd basis-CA-certificaat downloaden](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. Er wordt een bestand met de naam **down load** gedownload. Afhankelijk van uw browser krijgt u een bericht waarin u wordt gevraagd of u het bestand wilt openen of opslaan.

    ![CA-certificaat downloaden](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Sla het bestand op de Azure Backup Server machine op met de extensie. zip.

5. Klik met de rechter muisknop op **down load. zip** > **Alles uitpakken**. Het zip-bestand extraheert de inhoud naar de map **certs** , die de volgende bevat:
   - Het basis certificaat bestand met een extensie die begint met een genummerde reeks, zoals 0 en 1.
   - Het CRL-bestand heeft een extensie die begint met een Sequence zoals. R0 of. R1. Het CRL-bestand is gekoppeld aan een certificaat.

    ![Gedownloade certificaten](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

6. Klik in de map **certificaten** met de rechter muisknop op het basis certificaat bestand > **naam wijzigen**.

    ![Naam van basis certificaat wijzigen](./media/backup-azure-backup-server-vmware/rename-cert.png)

7. Wijzig de extensie van het basis certificaat in. CRT en bevestig dit. Het bestands pictogram wordt gewijzigd in een bestand dat een basis certificaat vertegenwoordigt.

8. Klik met de rechter muisknop op het basis certificaat en selecteer **certificaat installeren**in het pop-upmenu.

9. Selecteer in de **wizard Certificaat importeren**de optie **lokale computer** als doel voor het certificaat en klik vervolgens op **volgende**. Bevestig of u wordt gevraagd of u wijzigingen op de computer wilt toestaan.

    ![Welkom bij de wizard](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

10. Selecteer op de pagina **certificaat archief** **alle certificaten in het onderstaande archief opslaan**en klik vervolgens op **Bladeren** om het certificaat archief te kiezen.

    ![Certificaat opslag](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

11. Selecteer in **certificaat archief selecteren**de optie **vertrouwde basis certificerings instanties** als doelmap voor de certificaten en klik vervolgens op **OK**.

    ![Doelmap van certificaat](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

12. Controleer in de **wizard Certificaat importeren**de map en klik vervolgens op **volt ooien**.

    ![Controleren of het certificaat zich in de juiste map bevindt](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

13. Nadat het importeren van het certificaat is bevestigd, meldt u zich aan bij de vCenter Server om te bevestigen dat uw verbinding is beveiligd.

### <a name="disable-https-certificate-validation"></a>HTTPS-certificaat validatie uitschakelen

Als u binnen uw organisatie beveiligde grenzen hebt en u het HTTPS-protocol niet wilt gebruiken tussen VMware-servers en de Azure Backup Server machine, schakelt u HTTPS als volgt uit:

1. Kopieer en plak de volgende tekst in een txt-bestand.

       ```text
      Windows Registry editor versie 5,00 [HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare] "IgnoreCertificateValidation" = dword: 00000001
       ```

2. Sla het bestand op de Azure Backup Server computer op met de naam **DisableSecureAuthentication. reg**.

3. Dubbel klik op het bestand om de register vermelding te activeren.

## <a name="create-a-vmware-role"></a>Een VMware-rol maken

De Azure Backup Server moet een gebruikers account met machtigingen hebben voor toegang tot de ESXi-host van de v-Center-Server. Maak een VMware-rol met specifieke bevoegdheden en koppel vervolgens een gebruikers account aan de rol.

1. Meld u aan bij de vCenter Server (of ESXi-host als u geen gebruik maakt van vCenter Server).
2. Klik in het deel venster **Navigator** op **beheer**.

    ![Beheer](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. Klik in **beheer** > **rollen**op het pictogram rol toevoegen (het plus teken).

    ![Rol toevoegen](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

4. Voer *BackupAdminRole*in **rol maken** > **naam**in. De rolnaam kan wille keurig zijn, maar deze moet herkenbaar zijn voor het doel van de functie.

5. Selecteer in de onderstaande tabel de bevoegdheden die u wilt samenvatten en klik vervolgens op **OK**.  De nieuwe rol wordt weer gegeven in de lijst in het deel venster **rollen** .
   - Klik op het pictogram naast het bovenliggende label om het bovenliggende item uit te vouwen en de onderliggende bevoegdheden weer te geven.
   - Als u de VirtualMachine-bevoegdheden wilt selecteren, moet u verschillende niveaus naar de bovenliggende onderliggende hiërarchie gaan.
   - U hoeft niet alle onderliggende bevoegdheden binnen een bovenliggende bevoegdheid te selecteren.

    ![Hiërarchie bovenliggende onderliggende bevoegdheden](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Rolmachtigingen

**6.5/6.0** | **5,5**
--- | ---
Data Store. AllocateSpace | Data Store. AllocateSpace
Global. ManageCustomFields | Global. ManageCustomFields
Global. SetCustomField |
Host. local. CreateVM | Netwerk. assign
Netwerk. assign |
Resource. AssignVMToPool |
VirtualMachine. config. AddNewDisk  | VirtualMachine. config. AddNewDisk
VirtualMachine. config. AdvancedConfig| VirtualMachine. config. AdvancedConfig
VirtualMachine. config. change tracking| VirtualMachine. config. change tracking
VirtualMachine. config. HostUSBDevice |
VirtualMachine. config. QueryUnownedFiles |
VirtualMachine. config. SwapPlacement| VirtualMachine. config. SwapPlacement
VirtualMachine. interactie. uitgeschakeld| VirtualMachine. interactie. uitgeschakeld
VirtualMachine. Inventory. Create| VirtualMachine. Inventory. Create
VirtualMachine. provisioning. DiskRandomAccess |
VirtualMachine. provisioning. DiskRandomRead | VirtualMachine. provisioning. DiskRandomRead
VirtualMachine. State. CreateSnapshot | VirtualMachine. State. CreateSnapshot
VirtualMachine. State. RemoveSnapshot | VirtualMachine. State. RemoveSnapshot

## <a name="create-a-vmware-account"></a>Een VMware-account maken

1. Klik in vCenter Server **Navigator** venster op **gebruikers en groepen**. Als u vCenter Server niet gebruikt, maakt u het account op de juiste ESXi-host.

    ![Optie gebruikers en groepen](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    Het deel venster **vCenter-gebruikers en groepen** wordt weer gegeven.

2. Selecteer in het deel venster **vCenter-gebruikers en-groepen** het tabblad **gebruikers** en klik vervolgens op het pictogram gebruikers toevoegen (het plus teken).

    ![het deel venster vCenter-gebruikers en-groepen](./media/backup-azure-backup-server-vmware/usersandgroups.png)

3. Voeg in het dialoog venster **nieuwe gebruiker** de gebruikers gegevens > **OK**toe. In deze procedure is de gebruikers naam BackupAdmin.

    ![Het dialoog venster nieuwe gebruiker](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

4. Als u het gebruikers account wilt koppelen aan de rol, klikt u in het deel venster **Navigator** op **Global permissions**. Selecteer in het deel venster **algemene machtigingen** het tabblad **beheren** en klik vervolgens op het pictogram toevoegen (het plus teken).

    ![Paneel voor algemene machtigingen](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

5. Klik in **algemene machtiging hoofdmap-toevoegen machtiging**op **toevoegen** om de gebruiker of groep te kiezen.

    ![Gebruiker of groep selecteren](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. Kies in **gebruikers/groepen selecteren**de optie **BackupAdmin** > **toevoegen**. In **gebruikers**wordt de notatie *domein\gebruikersnaam* gebruikt voor het gebruikers account. Als u een ander domein wilt gebruiken, kiest u het in de lijst **domein** . Klik op **OK** om de geselecteerde gebruikers toe te voegen aan het dialoog venster **machtiging toevoegen** .

    ![BackupAdmin-gebruiker toevoegen](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

7. Selecteer in **toegewezen rol**in de vervolg keuzelijst de optie **BackupAdminRole** > **OK**.

    ![Gebruiker toewijzen aan rol](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

Op het tabblad **beheren** in het deel venster **algemene machtigingen** worden het nieuwe gebruikers account en de bijbehorende rol weer gegeven in de lijst.

## <a name="add-the-account-on-azure-backup-server"></a>Het account toevoegen op Azure Backup Server

1. Open Azure Backup Server. Als u het pictogram niet op het bureau blad kunt vinden, opent u Microsoft Azure Backup in de lijst met apps.

    ![Azure Backup Server pictogram](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. Klik in de Azure Backup Server-console op **beheer** >  **productie servers** > **VMware beheren**.

    ![Azure Backup Server-console](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

3. Klik in het dialoog venster **referenties beheren** op **toevoegen**.

    ![Het dialoog venster referenties Azure Backup Server beheren](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. Voer bij **referentie toevoegen**een naam en een beschrijving in voor de nieuwe referentie en geef de gebruikers naam en het wacht woord op die u hebt gedefinieerd op de VMware-Server. De naam *Contoso vCenter-referentie* wordt gebruikt om de referentie in deze procedure te identificeren. Als de VMware-Server en Azure Backup Server zich niet in hetzelfde domein bevinden, geeft u het domein op in de gebruikers naam.

    ![Dialoog venster Azure Backup Server referentie toevoegen](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Klik op **toevoegen** om de nieuwe referentie toe te voegen.

    ![Het dialoog venster referenties Azure Backup Server beheren](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server"></a>Voeg de vCenter Server toe

Voeg de vCenter Server toe aan Azure Backup Server.

1. Klik in de Azure Backup Server-console op **beheer** > **productie servers** > **toevoegen**.

    ![Wizard voor het toevoegen van productie servers](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

2. Selecteer in de wizard voor het toevoegen van de **productie server** > pagina **type productie server selecteren** de optie **VMware-servers**en klik vervolgens op **volgende**.

    ![Wizard voor het toevoegen van productie servers](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. Geef in **computers selecteren**  **Server naam/IP-adres**de FQDN of het IP-adres van de VMware-Server op. Als alle ESXi-servers worden beheerd door dezelfde vCenter, geeft u de vCenter-naam op. Voeg anders de ESXi-host toe.

    ![VMware-Server opgeven](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. Voer bij **SSL-poort**de poort in die wordt gebruikt om te communiceren met de VMware-Server. 443 is de standaard poort, maar u kunt deze wijzigen als uw VMware-Server luistert op een andere poort.

5. Selecteer in **referenties opgeven**de referentie die u eerder hebt gemaakt.

    ![Referentie opgeven](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Klik op **toevoegen** om de VMware-Server toe te voegen aan de lijst met servers. Klik op **Volgende**.

    ![VMWare-Server en-referentie toevoegen](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Klik op de pagina **samen vatting** op **toevoegen** om de VMware-Server toe te voegen aan Azure backup server. De nieuwe server wordt onmiddellijk toegevoegd, maar er is geen agent nodig op de VMware-Server.

    ![VMware-Server toevoegen aan Azure Backup Server](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. Controleer de instellingen op de pagina **volt ooien** .

   ![Pagina volt ooien](./media/backup-azure-backup-server-vmware/summary-screen.png)

Als u meerdere ESXi-hosts hebt die niet worden beheerd door de vCenter-Server of als u meerdere exemplaren van vCenter Server hebt, moet u de wizard opnieuw uitvoeren om de servers toe te voegen.

## <a name="configure-a-protection-group"></a>Een beveiligings groep configureren

VMware-Vm's toevoegen voor back-up. Beveiligings groepen verzamelen meerdere Vm's en passen dezelfde gegevens retentie en back-upinstellingen toe op alle virtuele machines in de groep.

1. Klik in de Azure Backup Server-console op **beveiliging**> **Nieuw**.

    ![Open de wizard nieuwe beveiligings groep maken](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Klik op de welkomst pagina van de wizard **nieuwe beveiligings groep maken** op **volgende**.

    ![Dialoog venster Wizard nieuwe beveiligings groep maken](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Selecteer op de pagina **type beveiligings groep selecteren** de optie **servers** en klik vervolgens op **volgende**. De pagina **groeps leden selecteren** wordt weer gegeven.

1. In **groeps leden selecteren**selecteert u de virtuele machines (of VM-mappen) waarvan u een back-up wilt maken. Klik op **Volgende**.

    - Wanneer u een map selecteert, worden er ook Vm's of mappen in die map geselecteerd voor back-up. U kunt de selectie van mappen of Vm's die u niet wilt maken, uitschakelen.
1. Als er al een back-up van een virtuele machine of map wordt gemaakt, kunt u deze niet selecteren. Dit zorgt ervoor dat er geen dubbele herstel punten worden gemaakt voor een virtuele machine.

    ![Groeps leden selecteren](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Voer op de pagina **methode voor gegevens beveiliging selecteren** een naam in voor de beveiligings groep en beveiligings instellingen. Als u een back-up wilt maken naar Azure, stelt u kortetermijnbeveiliging op **schijf** in en schakelt u online beveiliging in. Klik op **Volgende**.

    ![Methode voor gegevens beveiliging selecteren](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Geef in **doel stellingen voor de korte termijn**op hoe lang u een back-up van de gegevens wilt laten maken op schijf.
   - Geef in **Bewaar termijn**op hoeveel dagen schijf herstel punten moeten worden bewaard.
   - Geef bij **synchronisatie frequentie**op hoe vaak schijf herstel punten worden gemaakt.
       - Als u geen back-upinterval wilt instellen, kunt u **net vóór een herstel punt** controleren of een back-up wordt uitgevoerd vlak voordat elk herstel punt is gepland.
       - Back-ups op korte termijn zijn volledige back-ups en niet incrementeel.
       - Klik op **wijzigen** om de tijden of datums te wijzigen wanneer back-ups op korte termijn plaatsvinden.

         ![Doelen voor de korte termijn opgeven](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. Controleer in **schijf toewijzing controleren**de schijf ruimte die is vereist voor de back-ups van de virtuele machine. voor de Vm's.

   - De aanbevolen schijf toewijzingen zijn gebaseerd op de Bewaar termijn die u hebt opgegeven, het type werk belasting en de grootte van de beveiligde gegevens. Breng de gewenste wijzigingen aan en klik op **volgende**.
   - **Gegevens grootte:** Grootte van de gegevens in de beveiligings groep.
   - **Schijf ruimte:** De aanbevolen hoeveelheid schijf ruimte voor de beveiligings groep. Als u deze instelling wilt wijzigen, moet u de totale ruimte toewijzen die iets groter is dan de hoeveelheid waarmee u de gegevens bronnen wilt schatten.
   - **Gegevens opzoeken:** Als u co-locatie inschakelt, kunnen meerdere gegevens bronnen in de beveiliging worden toegewezen aan één replica-en herstel punt volume. De co-locatie wordt niet ondersteund voor alle werk belastingen.
   - **Automatisch verg Roten:** Als u deze instelling inschakelt en de gegevens in de beveiligde groep groter worden dan de initiële toewijzing, Azure Backup Server probeert de schijf grootte met 25 procent te verhogen.
   - **Details van opslag groep:** Hier wordt de status van de opslag groep weer gegeven, met inbegrip van de totale en resterende schijf grootte.

    ![Schijf toewijzing controleren](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. Geef in de pagina **methode voor maken van replica selecteren** op hoe u de eerste back-up wilt maken en klik vervolgens op **volgende**.
   - De standaard instelling is **automatisch via het netwerk** en **nu**.
   - Als u de standaard waarde gebruikt, raden we u aan een rustige tijd op te geven. Kies **later** en geef een dag en tijd op.
   - Voor grote hoeveel heden gegevens of minder dan optimale netwerk omstandigheden kunt u overwegen om de gegevens offline te repliceren met behulp van Verwissel bare media.

    ![Methode voor maken van replica selecteren](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. Selecteer in **Opties voor consistentie controle**hoe en wanneer de consistentie controles moeten worden geautomatiseerd. Klik op **Volgende**.
      - U kunt consistentie controles uitvoeren wanneer de replica gegevens inconsistent worden, of volgens een ingesteld schema.
      - Als u geen automatische consistentie controles wilt configureren, kunt u een hand matige controle uitvoeren. U doet dit door met de rechter muisknop op de beveiligings groep te klikken > **consistentie controle uit te voeren**.

1. Selecteer op de pagina **gegevens voor online beveiliging opgeven** de vm's of de mappen van de virtuele machine waarvan u een back-up wilt maken. U kunt de leden afzonderlijk selecteren of op **Alles selecteren** klikken om alle leden te kiezen. Klik op **Volgende**.

    ![Gegevens voor online beveiliging opgeven](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Op de pagina **online back-upschema opgeven** geeft u op hoe vaak u een back-up wilt maken van gegevens van de lokale opslag naar Azure.

    - De Cloud herstel punten voor de gegevens worden gegenereerd volgens het schema. Klik op **Volgende**.
    - Nadat het herstel punt is gegenereerd, wordt het overgedragen naar de Recovery Services kluis in Azure.

    ![Online back-upschema opgeven](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Geef op de pagina **online retentie beleid opgeven** op hoe lang u de herstel punten wilt bewaren die zijn gemaakt op basis van de dagelijkse/wekelijkse/maandelijkse/jaarlijkse back-ups naar Azure. Klik vervolgens op **volgende**.

    - Er is geen tijds limiet voor hoe lang u gegevens in azure kunt blijven gebruiken.
    - De enige limiet is dat u niet meer dan 9999 herstel punten per beveiligd exemplaar kunt hebben. In dit voor beeld is het beveiligde exemplaar de VMware-Server.

    ![Online Bewaar beleid opgeven](./media/backup-azure-backup-server-vmware/retention-policy.png)

1. Controleer de instellingen op de pagina **samen vatting** en klik vervolgens op **groep maken**.

    ![Lid van beveiligings groep en samen vatting van instellingen](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6,7

Ga als volgt te werk om een back-up te maken van vSphere 6,7:

- TLS 1,2 inschakelen op de DPM-server
  >[!Note]
  >VMWare 6,7 heeft TLS ingeschakeld als communicatie protocol.

- Stel de register sleutels als volgt in:

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

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [hand leiding voor probleem oplossing voor Azure backup server](./backup-azure-mabs-troubleshoot.md)voor het oplossen van problemen met het instellen van back-ups.
