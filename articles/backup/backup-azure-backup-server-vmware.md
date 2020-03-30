---
title: Back-ups maken van VMware VM's met Azure Backup Server
description: In dit artikel leest u hoe u Azure Backup Server gebruikt om een back-up te maken van VMware VM's die worden uitgevoerd op een VMware vCenter/ESXi-server.
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: df85cba42118a2e814a4a1c8338f3927e4d75f36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273473"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Back-ups maken van VMware VM's met Azure Backup Server

In dit artikel wordt uitgelegd hoe u een back-up maakt van VMware VM's die worden uitgevoerd op VMware ESXi hosts/vCenter Server naar Azure met Azure Backup Server.

In dit artikel wordt uitgelegd hoe u:

- Stel een beveiligd kanaal in zodat Azure Backup Server via HTTPS met VMware-servers kan communiceren.
- Stel een VMware-account in dat Azure Backup Server gebruikt om toegang te krijgen tot de VMware-server.
- Voeg de accountreferenties toe aan Azure Backup.
- Voeg de vCenter- of ESXi-server toe aan Azure Backup Server.
- Stel een beveiligingsgroep in die de VMware-VM's bevat waarvan u een back-up wilt maken, back-upinstellingen opgeeft en de back-up plant.

## <a name="before-you-start"></a>Voordat u begint

- Controleer of u een versie van vCenter/ESXi gebruikt die wordt ondersteund voor back-up. Raadpleeg [hier](https://docs.microsoft.com/azure/backup/backup-mabs-protection-matrix)de ondersteuningsmatrix.
- Controleer of u Azure Backup Server hebt ingesteld. Als je dat nog niet hebt gedaan, [doe dat](backup-azure-microsoft-azure-backup.md) dan voordat je begint. U moet Azure Backup Server uitvoeren met de nieuwste updates.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Een beveiligde verbinding met de vCenter-server maken

Azure Backup Server communiceert standaard met VMware-servers via HTTPS. Als u de HTTPS-verbinding wilt instellen, downloadt u het CA-certificaat (VMware Certificate Authority) en importeert u het op de Azure Backup Server.

### <a name="before-you-begin"></a>Voordat u begint

- Als u GEEN HTTPS wilt gebruiken, u [https-certificaatvalidatie uitschakelen voor alle VMware-servers.](backup-azure-backup-server-vmware.md#disable-https-certificate-validation)
- U maakt meestal verbinding vanuit een browser op de Azure Backup Server-machine met de vCenter/ESXi-server met behulp van de vSphere-webclient. De eerste keer dat u dit doet, is de verbinding niet veilig en wordt het volgende weergegeven.
- Het is belangrijk om te begrijpen hoe Azure Backup Server back-ups verwerkt.
  - Als eerste stap maakt Azure Backup Server een back-up van gegevens naar lokale schijfopslag. Azure Backup Server maakt gebruik van een opslaggroep, een set schijven en volumes waarop Azure Backup Server schijfherstelpunten opslaat voor de beveiligde gegevens. De opslaggroep kan direct worden aangesloten op opslag (DAS), een fiberkanaal SAN of iSCSI-opslagapparaat of SAN. Het is belangrijk om ervoor te zorgen dat u voldoende opslagruimte hebt voor lokale back-ups van uw VMware VM-gegevens.
  - Azure Backup Server maakt vervolgens een back-up van de lokale schijfopslag naar Azure.
  - [Krijg hulp](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1807#figure-out-how-much-storage-space-you-need) om erachter te komen hoeveel opslagruimte je nodig hebt. De informatie is voor DPM, maar kan ook worden gebruikt voor Azure Backup Server.

### <a name="set-up-the-certificate"></a>Het certificaat instellen

Stel als volgt een beveiligd kanaal in:

1. Voer in de browser op Azure Backup Server de URL van de vSphere-webclient in. Als de aanmeldingspagina niet wordt weergegeven, controleert u de verbindings- en browserproxy-instellingen.

    ![vSphere-webclient](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. Klik op de aanmeldingspagina van vSphere Web Client op **Vertrouwde BASIS CA-certificaten downloaden**.

    ![Vertrouwd basis-CA-certificaat downloaden](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. Een bestand met de naam **download** wordt gedownload. Afhankelijk van uw browser ontvangt u een bericht waarin wordt gevraagd of u het bestand wilt openen of opslaan.

    ![CA-certificaat downloaden](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Sla het bestand op de Azure Backup Server-machine op met een .zip-extensie.

5. Klik met de rechtermuisknop op **download.zip** > **Extract All**. Het .zip-bestand haalt de inhoud ervan uit naar de **map certs,** die het:
   - Het hoofdcertificaatbestand met een extensie die begint met een genummerde reeks zoals .0 en .1.
   - Het CRL-bestand heeft een extensie die begint met een reeks als .r0 of .r1. Het CRL-bestand is gekoppeld aan een certificaat.

    ![Gedownloade certificaten](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

6. Klik in de map **certs** met de rechtermuisknop op het hoofdcertificaatbestand > **Hernoeming**.

    ![Naam van het hoofdcertificaat wijzigen](./media/backup-azure-backup-server-vmware/rename-cert.png)

7. Wijzig de extensie van het basiscertificaat in .crt en bevestig. Het bestandspictogram wordt gewijzigd in een bestand dat een hoofdcertificaat vertegenwoordigt.

8. Klik met de rechtermuisknop op het hoofdcertificaat en selecteer in het pop-upmenu **Certificaat installeren**.

9. Selecteer **in de wizard Certificaat importeren**de optie Lokale **machine** als bestemming voor het certificaat en klik op **Volgende**. Controleer of u wordt gevraagd of u wijzigingen in de computer wilt toestaan.

    ![Wizard Welkom](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

10. Selecteer op de pagina **Certificaatarchief** de optie **Alle certificaten in het volgende archief plaatsen**en klik vervolgens op **Bladeren** om het certificaatarchief te kiezen.

    ![Certificaatopslag](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

11. Selecteer **in Certificaatarchief selecteren**de optie Vertrouwde **basiscertificeringsinstanties** als de doelmap voor de certificaten en klik op **OK**.

    ![Map Certificaatbestemming](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

12. Controleer de map **in De wizard Certificaat importeren**voltooien en klik op **Voltooien**.

    ![Controleren of het certificaat zich in de juiste map bevindt](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

13. Nadat het importeren van het certificaat is bevestigd, meldt u zich aan bij de vCenter Server om te bevestigen dat uw verbinding veilig is.

### <a name="disable-https-certificate-validation"></a>HTTPS-certificaatvalidatie uitschakelen

Als u beveiligde grenzen binnen uw organisatie hebt en het HTTPS-protocol tussen VMware-servers en de Azure Backup Server-machine niet wilt gebruiken, schakelt u HTTPS als volgt uit:

1. Kopieer en plak de volgende tekst in een .txt-bestand.

```text
Windows Registry Editor Version 5.00
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
"IgnoreCertificateValidation"=dword:00000001
```

2. Sla het bestand op de Azure Backup Server-machine op met de naam **DisableSecureAuthentication.reg**.

3. Dubbelklik op het bestand om de registervermelding te activeren.

## <a name="create-a-vmware-role"></a>Een VMware-rol maken

De Azure Backup Server heeft een gebruikersaccount nodig met machtigingen voor toegang tot v-Center Server/ESXi-host. Maak een VMware-rol met specifieke bevoegdheden en koppel vervolgens een gebruikersaccount aan de rol.

1. Meld u aan bij de vCenter Server (of ESXi-host als u vCenter Server niet gebruikt).
2. Klik in het deelvenster **Navigator** op **Beheer**.

    ![Beheer](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. Klik **in Beheerrollen** > **Roles**op het pictogram Rol toevoegen (het + symbool).

    ![Rol toevoegen](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

4. Voer *back-upsrolrolinen*in De naam **Rol** > **van**maken . De rolnaam kan zijn wat je wilt, maar het moet herkenbaar zijn voor het doel van de rol.

5. Selecteer de bevoegdheden zoals samengevat in de onderstaande tabel en klik op **OK**.  De nieuwe rol wordt weergegeven in de lijst in het deelvenster **Rollen.**
   - Klik op het pictogram naast het bovenliggende label om de bovenliggende tekst uit te vouwen en de onderliggende rechten weer te geven.
   - Als u de VirtualMachine-bevoegdheden wilt selecteren, moet u verschillende niveaus naar de bovenliggende onderliggende hiërarchie gaan.
   - U hoeft niet alle onderliggende rechten binnen een bovenliggende bevoegdheid te selecteren.

    ![Hiërarchie van bovenliggende onderliggende bevoegdheden](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Functiemachtigingen

| **Bevoegdheden voor vCenter 6.5 en hoger gebruikersaccount**        | **Bevoegdheden voor vCenter 6.0-gebruikersaccount**               | **Bevoegdheden voor vCenter 5.5-gebruikersaccount** |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------- |
| Datastore.AllocateSpace                                      |                                                           |                                             |
| Datastore.Browse datastore                                   | Datastore.AllocateSpace                                   | Netwerk.Toewijzen                              |
| Datastore.Bestandsbewerkingen op laag niveau                          | Globale.Aangepaste kenmerken beheren                           | Datastore.AllocateSpace                     |
| Datastore-cluster. Een datatstore-cluster configureren             | Aangepast kenmerk globaal.Instellen                               | VirtualMachine.Config.ChangeTracking VirtualMachine.Config.ChangeTracking VirtualMachine.Config.ChangeTracking VirtualMachine        |
| Globale.Uitgeschakeldmethoden                                       | Host.Local operaties. Virtuele machine maken              | VirtualMachine.state.removeSnapshot VirtualMachine.State.RemoveSnapshot VirtualMachine.State.RemoveSnapshot VirtualMachine         |
| Globale.Enable-methoden                                        | Netwerk. Netwerk toewijzen                                   | VirtualMachine.State.CreateSnapshot VirtualMachine.State.CreateSnapshot VirtualMachine.State.CreateSnapshot VirtualMachine         |
| Global.Licenties                                              | Resource. Virtuele machine toewijzen aan resourcegroep         | VirtualMachine.Provisioning.DiskRandomRead VirtualMachine.Provisioning.DiskRandomRead VirtualMachine.Provisioning.DiskRandomRead VirtualMachine  |
| Global.Log-evenement                                             | Virtuele machine. Configuration.Nieuwe schijf toevoegen                | VirtualMachine.Interact.PowerOff VirtualMachine.Interact.PowerOff VirtualMachine.Interact.PowerOff VirtualMachine            |
| Globale.Aangepaste kenmerken beheren                              | Virtuele machine. Configuration.Advanced                    | VirtualMachine.Inventory.Create VirtualMachine.Inventory.Create VirtualMachine.Inventory.Create VirtualMachine             |
| Aangepast kenmerk globaal.Instellen                                  | Virtuele machine. Configuration.Disk change tracking        | VirtualMachine.Config.AddNewDisk            |
| Netwerk toewijzen.Netwerk toewijzen                                       | Virtuele machine. Configuration.Host USB-apparaat             | VirtualMachine.Config.HostUSBDevice         |
| Resource. Virtuele machine toewijzen aan resourcegroep            | Virtuele machine. Configuration.Query-niet-beheerde bestanden         | VirtualMachine.Config.AdvancedConfig        |
| Virtuele machine. Configuration.Nieuwe schijf toevoegen                   | Virtuele machine. Configuration.Swapfile plaatsing          | VirtualMachine.Config.SwapPlacement VirtualMachine.Config.SwapPlacement VirtualMachine.Config.SwapPlacement VirtualMachine         |
| Virtuele machine. Configuration.Advanced                       | Virtuele machine. Interaction.Power Off                     | Global.ManageCustomFields                   |
| Virtuele machine. Configuration.Disk change tracking           | Virtuele machine. Inventaris. Nieuwe maken                     |                                             |
| Virtuele machine. Configuration.Disk lease                     | Virtuele machine. Provisioning.Schijftoegang toestaan            |                                             |
| Virtuele machine. Configuration.Extend virtuele schijf            | Virtuele machine. Provisioning. Alleen-lezen schijftoegang toestaan |                                             |
| Virtuele machine. Wijzigingen van de bediening van gasten.Guest | Virtuele machine. Momentopnamebeheer. Momentopname maken       |                                             |
| Virtuele machine. Uitvoering van het Gast Operations.Guest Operation Program | Virtuele machine. Momentopnamebeheer. Momentopname verwijderen       |                                             |
| Virtuele machine. Gastoperations.Guest-bewerkingsquery's     |                                                           |                                             |
| Virtuele machine . Interactie. Apparaatverbinding              |                                                           |                                             |
| Virtuele machine . Interactie. Guest operating system management door VIX API |                                                           |                                             |
| Virtuele machine . Inventaris.Register                          |                                                           |                                             |
| Virtuele machine . Inventaris.verwijderen                            |                                                           |                                             |
| Virtuele machine . Provisioning.Schijftoegang toestaan              |                                                           |                                             |
| Virtuele machine . Provisioning.Alleen-lezen schijftoegang toestaan    |                                                           |                                             |
| Virtuele machine . Provisioning.Allow virtual machine download |                                                           |                                             |
| Virtuele machine . Momentopnamebeheer. Momentopname maken        |                                                           |                                             |
| Virtuele machine . Momentopnamebeheer. Momentopname verwijderen         |                                                           |                                             |
| Virtuele machine . Momentopnamebeheer. Terugkeren naar momentopname      |                                                           |                                             |
| vApp.Virtuele machine toevoegen                                     |                                                           |                                             |
| vApp.Resourcegroep toewijzen                                    |                                                           |                                             |
| vApp.Uitschrijven                                              |                                                           |                                             |

## <a name="create-a-vmware-account"></a>Een VMware-account maken

1. Klik in het deelvenster VCenter Server **Navigator** op **Gebruikers en groepen**. Als u geen vCenter Server gebruikt, maakt u het account op de juiste ESXi-host.

    ![Optie Gebruikers en groepen](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    Het deelvenster **VCenter-gebruikers en -groepen** wordt weergegeven.

2. Selecteer in het deelvenster **Gebruikers en groepen vCenter** het tabblad **Gebruikers** en klik vervolgens op het pictogram Gebruikers toevoegen (het + symbool).

    ![vCenter-gebruikers en -groepen, deelvenster](./media/backup-azure-backup-server-vmware/usersandgroups.png)

3. Voeg in het dialoogvenster **Nieuwe gebruiker** de gebruikersgegevens toe > **OK**. In deze procedure is de gebruikersnaam BackupAdmin.

    ![Dialoogvenster Nieuwe gebruiker](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

4. Als u het gebruikersaccount aan de rol wilt koppelen, klikt u in het deelvenster **Navigator** op **Globale machtigingen**. Selecteer **in** het deelvenster Globale machtigingen het tabblad **Beheren** en klik op het pictogram Toevoegen (het +-symbool).

    ![Deelvenster Globale machtigingen](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

5. Klik in **hoofdmap globale machtigingen - Machtigingen toevoegen**op **Toevoegen** om de gebruiker of groep te kiezen.

    ![Gebruiker of groep kiezen](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. Kies **BackupAdmin** > **Toevoegen**in **Gebruikers/groepen**selecteren . In **Gebruikers**wordt de *indeling domain\username* gebruikt voor het gebruikersaccount. Als u een ander domein wilt gebruiken, kiest u deze in de **lijst Domein.** Klik op **OK** om de geselecteerde gebruikers toe te voegen aan het dialoogvenster **Machtigingen toevoegen.**

    ![BackupAdmin-gebruiker toevoegen](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

7. Selecteer in **Toegewezen rol**in de vervolgkeuzelijst **BackupAdminRole** > **OK**.

    ![Gebruiker toewijzen aan rol](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

Op het tabblad **Beheren** in het deelvenster **Globale machtigingen** worden het nieuwe gebruikersaccount en de bijbehorende rol in de lijst weergegeven.

## <a name="add-the-account-on-azure-backup-server"></a>Het account toevoegen op Azure Backup Server

1. Open Azure Backup Server. Als u het pictogram op het bureaublad niet vinden, opent u Microsoft Azure Backup in de lijst met apps.

    ![Pictogram Azure Backup Server](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. Klik in de Azure Backup Server-console op >  **Beheerproductieservers** > **VMware beheren**. **Management**

    ![Azure Backup Server-console](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

3. Klik **in** het dialoogvenster Referenties beheren op **Toevoegen**.

    ![Dialoogvenster Referenties beheren van Azure Backup Server](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. Voer **in Credential toevoegen**een naam en een beschrijving in voor de nieuwe referentie en geef de gebruikersnaam en het wachtwoord op dat u hebt gedefinieerd op de VMware-server. De *naam, Contoso Vcenter-referentie* wordt gebruikt om de referentie in deze procedure te identificeren. Als de VMware-server en Azure Backup Server zich niet in hetzelfde domein bevinden, geeft u het domein in de gebruikersnaam op.

    ![Dialoogvenster Referenties toevoegen van Azure Backup Server](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Klik **op Toevoegen** om de nieuwe referentie toe te voegen.

    ![Dialoogvenster Referenties beheren van Azure Backup Server](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server"></a>De vCenter-server toevoegen

Voeg de vCenter Server toe aan Azure Backup Server.

1. Klik in de Azure Backup Server-console op > **Beheerproductieservers** > **Toevoegen**. **Management**

    ![Wizard Productieserver toevoegen openen](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

2. Selecteer **in de wizard** > Productieserver Toevoegen de pagina**Productieserver,** selecteer **VMware-servers**en klik op **Volgende**.

    ![Wizard Productieserver toevoegen](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. Geef in**Naam/IP-adres** **van computersserver**opgeven het FQDN- of IP-adres van de VMware-server op.   Als alle ESXi-servers door hetzelfde vCenter worden beheerd, geeft u de vCenter-naam op. Voeg anders de ESXi-host toe.

    ![VMware-server opgeven](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. Voer in **SSL-poort**de poort in die wordt gebruikt om te communiceren met de VMware-server. 443 is de standaardpoort, maar u deze wijzigen als uw VMware-server op een andere poort luistert.

5. Selecteer **in Credential opgeven**de referentie die u eerder hebt gemaakt.

    ![Referenties opgeven](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Klik **op Toevoegen** om de VMware-server toe te voegen aan de lijst met servers. Klik vervolgens op **Volgende**.

    ![VMWare-server en -referenties toevoegen](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Klik **op** de pagina Overzicht op **Toevoegen** om de VMware-server toe te voegen aan Azure Backup Server. De nieuwe server wordt onmiddellijk toegevoegd, er is geen agent nodig op de VMware-server.

    ![VMware-server toevoegen aan Azure Backup Server](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. Controleer de instellingen op de **pagina Voltooien.**

   ![Einddatumpagina](./media/backup-azure-backup-server-vmware/summary-screen.png)

Als u meerdere ESXi-hosts hebt die niet worden beheerd door vCenter-server of als u meerdere exemplaren van vCenter Server hebt, moet u de wizard opnieuw uitvoeren om de servers toe te voegen.

## <a name="configure-a-protection-group"></a>Een beveiligingsgroep configureren

VMware VM's toevoegen voor back-up. Beveiligingsgroepen verzamelen meerdere VM's en passen dezelfde instellingen voor gegevensbewaring en back-up toe op alle VM's in de groep.

1. Klik in de Azure Backup Server-console op **Beveiliging,**> **Nieuw**.

    ![De wizard Nieuwe beveiligingsgroep maken openen](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Klik op de welkomstpagina van de wizard **Nieuwe beveiligingsgroep** maken op **Volgende**.

    ![Dialoogvenster Nieuwe beveiligingsgroep maken](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Selecteer op de pagina **Type groep Beveiliging** selecteren de optie **Servers** en klik op **Volgende**. De pagina **Groepsleden selecteren** wordt weergegeven.

1. Selecteer **in Groepsleden selecteren**de VM's (of VM-mappen) waarvan u een back-up wilt maken. Klik vervolgens op **Volgende**.

    - Wanneer u een map of VM's of mappen in die map selecteert, worden ook een back-up geselecteerd. U mappen of VM's die u niet wilt back-ups uitvinken.
1. Als er al een back-up van een virtuele machine of map wordt gemaakt, u deze niet selecteren. Dit zorgt ervoor dat dubbele herstelpunten niet worden gemaakt voor een virtuele machine.

    ![Groepsleden selecteren](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Voer op de pagina **Methode gegevensbescherming** selecteren een naam in voor de beveiligingsgroep en beveiligingsinstellingen. Als u een back-up wilt maken met Azure, stelt u kortetermijnbeveiliging in **op Schijf** en schakelt u online beveiliging in. Klik vervolgens op **Volgende**.

    ![Methode voor gegevensbeveiliging selecteren](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Geef in **Doelen voor korte termijn**opgeven op hoe lang u een back-up van gegevens op schijf wilt houden.
   - Geef in **bewaarbereik**op hoeveel dagen schijfherstelpunten moeten worden bewaard.
   - Geef in **Synchronisatiefrequentie**op hoe vaak schijfherstelpunten worden genomen.
       - Als u geen back-upinterval wilt instellen, u **vlak voor een herstelpunt** controleren dat een back-up wordt uitgevoerd vlak voordat elk herstelpunt is gepland.
       - Korte termijn back-ups zijn volledige back-ups en niet incrementeel.
       - Klik **op Wijzigen** om de tijden/datums te wijzigen waarop back-ups op korte termijn plaatsvinden.

         ![Kortetermijndoelen opgeven](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. Controleer in **Schijftoewijzing controleren**of de schijfruimte voor de VM-back-ups is opgegeven. voor de VM's.

   - De aanbevolen schijftoewijzingen zijn gebaseerd op het opgegeven bewaarbereik, het type werkbelasting en de grootte van de beveiligde gegevens. Breng de vereiste wijzigingen aan en klik op **Volgende**.
   - **Gegevensgrootte:** Grootte van de gegevens in de beveiligingsgroep.
   - **Schijfruimte:** De aanbevolen hoeveelheid schijfruimte voor de beveiligingsgroep. Als u deze instelling wilt wijzigen, moet u de totale ruimte toewijzen die iets groter is dan het bedrag dat u schat dat elke gegevensbron groeit.
   - **Gegevens colocate:** Als u colocatie inschakelt, kunnen meerdere gegevensbronnen in de beveiliging toewijzen aan één replica- en herstelpuntvolume. Colocatie wordt niet ondersteund voor alle workloads.
   - **Automatisch groeien:** Als u deze instelling inschakelt, probeert Azure Backup Server de schijfgrootte met 25 procent te vergroten als gegevens in de beveiligde groep de eerste toewijzing ontgroeien.
   - **Details van de opslaggroep:** Hiermee wordt de status van de opslaggroep weergegeven, inclusief de totale en resterende schijfgrootte.

    ![Schijftoewijzing controleren](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. Geef op de pagina **Methode voor het maken van replica's** op hoe u de eerste back-up wilt maken en klik op **Volgende**.
   - De standaardinstelling is **automatisch via het netwerk** en **Nu**.
   - Als u de standaardinstelling gebruikt, raden we u aan een daluren op te geven. Kies **Later** en geef een dag en tijd op.
   - Voor grote hoeveelheden gegevens of minder optimale netwerkomstandigheden u overwegen de gegevens offline te repliceren met behulp van verwisselbare media.

    ![Methode voor het maken van replica's kiezen](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. Selecteer in **consistentiecontroleopties**hoe en wanneer u de consistentiecontroles wilt automatiseren. Klik vervolgens op **Volgende**.
      - U consistentiecontroles uitvoeren wanneer replicagegevens inconsistent worden of volgens een vast schema.
      - Als u geen automatische consistentiecontroles wilt configureren, u een handmatige controle uitvoeren. Klik hiervoor met de rechtermuisknop op de beveiligingsgroep > **Consistentiecontrole uitvoeren**.

1. Selecteer op de pagina **Gegevens voor onlinebeveiliging opgeven** de VM's of VM-mappen waarvan u een back-up wilt maken. U de leden afzonderlijk selecteren of op **Alles selecteren** om alle leden te kiezen. Klik vervolgens op **Volgende**.

    ![Onlinebeveiligingsgegevens opgeven](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Geef op de pagina **Online back-upplanning opgeven** op hoe vaak u een back-up wilt maken van gegevens van lokale opslag naar Azure.

    - Cloudherstelpunten voor de gegevens worden gegenereerd volgens het schema. Klik vervolgens op **Volgende**.
    - Nadat het herstelpunt is gegenereerd, wordt het overgebracht naar de kluis Recovery Services in Azure.

    ![Online back-upschema opgeven](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Geef op de pagina **Onlinebewaarbeleid opgeven** aan hoe lang u de herstelpunten wilt behouden die zijn gemaakt met de dagelijkse/wekelijkse/maandelijkse/jaarlijkse back-ups naar Azure. klik vervolgens op **Volgende**.

    - Er is geen tijdslimiet voor hoe lang u gegevens in Azure bewaren.
    - De enige limiet is dat u niet meer dan 9999 herstelpunten per beveiligde instantie hebben. In dit voorbeeld is de beveiligde instantie de VMware-server.

    ![Online bewaarbeleid opgeven](./media/backup-azure-backup-server-vmware/retention-policy.png)

1. Controleer **op** de pagina Overzicht de instellingen en klik op **Groep maken**.

    ![Lid van de beveiligingsgroep en een overzicht instellen](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6.7

Ga als volgt te werk om een back-up te maken van vSphere 6.7:

- TLS 1.2 inschakelen op DPM-server

>[!NOTE]
>VMWare 6.7 had tls ingeschakeld als communicatieprotocol.

- Stel de registersleutels als volgt in:

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

Controleer de [handleiding voor probleemoplossing voor Azure Backup Server voor](./backup-azure-mabs-troubleshoot.md)het oplossen van problemen bij het instellen van back-ups.
