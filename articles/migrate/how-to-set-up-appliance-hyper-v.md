---
title: Een Azure Migrate-toestel instellen voor Hyper-V
description: Meer informatie over het instellen van een Azure Migrate-toestel om Hyper-V VM's te beoordelen en te migreren.
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: 77c13a3a8c87d116bd0863324d28669185c53c84
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538287"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Een apparaat instellen voor Hyper V VM's

Volg dit artikel om het Azure Migrate appliance in te stellen voor de beoordeling van Hyper-V VM's met het Azure [Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) tool.

Het [Azure Migrate-toestel](migrate-appliance.md) is een lichtgewicht toestel dat wordt gebruikt door Azure Migrate:Server Assessment/Migration om on-premises Hyper-V VM's te detecteren en VM-metagegevens/prestatiegegevens naar Azure te verzenden.

U het toestel op een aantal manieren implementeren:

- Instellen op een Hyper-V VM met behulp van een gedownloade VHD. Dit is de methode beschreven in dit artikel.
- Instellen op een Hyper-V VM of fysieke machine met een PowerShell-installatiescript. [Deze methode](deploy-appliance-script.md) moet worden gebruikt als u een VM niet instellen met een VHD of als u zich in Azure Government bevindt.

Nadat u het toestel hebt gemaakt, controleert u of het verbinding kan maken met Azure Migrate:Server Assessment, configureer het voor de eerste keer en registreer u het met het Azure Migrate-project.

## <a name="appliance-deployment-vhd"></a>Toestelimplementatie (VHD)

Ga als lid van het toestel met een VHD-sjabloon:

- Download een gecomprimeerde Hyper-V VHD van de Azure-portal.
- Maak het toestel en controleer of het verbinding kan maken met Azure Migrate Server Assessment.
- Configureer het toestel voor de eerste keer en registreer het met het Azure Migrate-project.

## <a name="download-the-vhd"></a>Download de VHD

Download de VHD-sjabloon met rits voor het apparaat.

1. Klik in **Migration Goals** > **Servers** > **Azure Migrate: Server assessment**op **Ontdekken**.
2. In **Discover machines** > **Worden uw machines gevirtualiseerd?**, klik op **Ja, met Hyper-V**.
3. Klik **op Downloaden** om het VHD-bestand te downloaden.

    ![VM downloaden](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Beveiliging verifiëren

Controleer of het zip-bestand veilig is, voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Voer de volgende opdracht uit om de hash voor de VHD te genereren
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gebruiksvoorbeeld: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.vhd SHA256```
3.  Voor toestelversie 2.19.11.12 moet de gegenereerde hash overeenkomen met deze [instellingen](https://docs.microsoft.com/azure/migrate/tutorial-assess-hyper-v#verify-security).




## <a name="create-the-appliance-vm"></a>De toestel-VM maken

Importeer het gedownloade bestand en maak de VM.

1. Haal het vhd-bestand met rits uit naar een map op de Hyper-V-host die de VM van het toestel host. Drie mappen worden geëxtraheerd.
2. Open Hyper-V-beheer. Klik **in Acties**op Virtuele machine **importeren**.

    ![VHD implementeren](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. Klik in de wizard Virtuele machine importeren > **Voordat u begint**op **Volgende**.
3. Geef in **Map zoeken**de map op die de uitgepakte VHD bevat. Klik vervolgens op **Volgende**.
1. Klik in **Virtuele machine selecteren**op **Volgende**.
2. Klik in **Type importeren kiezen**op De virtuele machine kopiëren **(een nieuwe unieke ID maken).** Klik vervolgens op **Volgende**.
3. Verlaat **in Bestemming kiezen**de standaardinstelling. Klik op **Volgende**.
4. Laat **in Opslagmappen**de standaardinstelling achter. Klik op **Volgende**.
5. Geef **in Netwerk kiezen**de virtuele switch op die de virtuele switch zal gebruiken. De switch heeft internetverbinding nodig om gegevens naar Azure te verzenden.
6. **Bekijk in het kort**de instellingen. Klik vervolgens op **Voltooien**.
7. Start in Hyper-V Manager > **Virtual Machines**de VM.


### <a name="verify-appliance-access-to-azure"></a>De toegang tot het toestel tot Azure verifiëren

Zorg ervoor dat de toestel-VM verbinding kan maken met Azure-URL's voor [openbare](migrate-appliance.md#public-cloud-urls) en [overheidsclouds.](migrate-appliance.md#government-cloud-urls)

## <a name="configure-the-appliance"></a>Het toestel configureren

Stel het apparaat voor de eerste keer in. Als u het toestel implementeert met een script in plaats van een VHD, zijn de eerste twee stappen in de procedure niet van toepassing.

1. Klik in Hyper-V Manager > **virtuele machines**met de rechtermuisknop op de VM > **Connect**.
2. Geef de taal, tijdzone en wachtwoord op voor het toestel.
3. Open een browser op elke machine die verbinding kan maken met de VM en open de URL van de toestelweb-app: **https://*toestelnaam of IP-adres*: 44368**.

   U de app ook openen vanaf het bureaublad van het toestel door op de snelkoppeling van de app te klikken.
1. Ga als volgt te werk in de web-app > **Vereiste voorwaarden instellen:**
    - **Licentie:** Accepteer de licentievoorwaarden en lees de informatie van derden.
    - **Connectiviteit**: De app controleert of de VM toegang tot internet heeft. Als de VM een proxy gebruikt:
        - Klik **op Proxy-instellingen**en geef het proxyadres http://ProxyIPAddress http://ProxyFQDNen de luisterpoort op in het formulier of .
        - Geef referenties op als de proxy verificatie nodig heeft.
        - Alleen HTTP-proxy wordt ondersteund.
    - **Tijdsynchronisatie**: De tijd wordt geverifieerd. De tijd op het toestel moet synchroon lopen met de internettijd om vm-detectie goed te laten werken.
    - **Updates installeren:** Azure Migrate Server Assessment controleert of het toestel de nieuwste updates heeft geïnstalleerd.

### <a name="register-the-appliance-with-azure-migrate"></a>Het toestel registreren met Azure Migreren

1. Klik **op Aanmelden**. Als deze niet wordt weergegeven, controleert u of u de pop-upblokkering in de browser hebt uitgeschakeld.
2. Meld u op het nieuwe tabblad aan met uw Azure-referenties.
    - Meld u aan met uw gebruikersnaam en wachtwoord.
    - Aanmelden met een pincode wordt niet ondersteund.
3. Ga na het succesvol aanmelden terug naar de web-app.
4. Selecteer het abonnement waarin het Azure Migrate-project is gemaakt. Selecteer vervolgens het project.
5. Geef een naam op voor het toestel. De naam moet alfanumeriek zijn met 14 tekens of minder.
6. Klik **op Registreren**.


### <a name="delegate-credentials-for-smb-vhds"></a>Referenties voor SMB VHD's delegeren

Als u VHD's uitvoert op het MKB, moet u de overdracht van referenties van het toestel naar de Hyper-V-hosts inschakelen. Ga als volgt te werk om dit vanaf het apparaat te doen:

1. Voer deze opdracht uit op de toestel-VM. HyperVHost1/HyperVHost2 zijn voorbeeldhostnamen.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. U dit ook doen in de lokale groepsbeleidseditor op het toestel:
    - Klik in **computerconfiguratie lokaal** > **System** > **computerbeleid**op Overdracht systeemreferenties **voor systeemreferenties** > beheren .**Credentials Delegation**
    - Dubbelklik op **Het delegeren van nieuwe referenties toestaan**en selecteer **Ingeschakeld**.
    - Klik **in Opties**op **Weergeven**en voeg elke Hyper-V-host die u wilt ontdekken toe aan de lijst, met **wsman/** als voorvoegsel.
    - Dubbelklik in **Credentials Delegation**op **Het delegeren van nieuwe referenties toestaan met NTLM-serververificatie**. Nogmaals, voeg elke Hyper-V-host die je wilt ontdekken toe aan de lijst, met **wsman/** als voorvoegsel.

## <a name="start-continuous-discovery"></a>Continue detectie starten

Maak verbinding van het toestel met Hyper-V-hosts of clusters en start VM-detectie.

1. Geef **in Gebruikersnaam** en **wachtwoord**de accountgegevens op die het toestel zal gebruiken om VM's te ontdekken. Geef een vriendelijke naam op voor de referenties en klik op **Details opslaan**.
2. Klik **op Host toevoegen**en geef details van hyper-v-host/cluster op.
3. Klik op **Valideren**. Na validatie wordt het aantal VM's weergegeven dat op elke host/cluster kan worden gedetecteerd.
    - Als de validatie mislukt voor een host, controleert u de fout door boven het pictogram in de kolom **Status** te zweven. Los problemen op en valideer opnieuw.
    - Als u hosts of clusters wilt verwijderen, selecteert u > **verwijderen**.
    - U een specifieke host niet uit een cluster verwijderen. U alleen het hele cluster verwijderen.
    - U een cluster toevoegen, zelfs als er problemen zijn met specifieke hosts in het cluster.
4. Klik na validatie op **Opslaan en start detectie** om het detectieproces te starten.

Dit begint te ontdekken. Het duurt ongeveer 15 minuten voordat metagegevens van gedetecteerde VM's worden weergegeven in de Azure-portal.

## <a name="verify-vms-in-the-portal"></a>VM's verifiëren in de portal

Nadat detectie is voltooid, u controleren of de VM's in de portal worden weergegeven.

1. Open het Azure Migrate-dashboard.
2. Klik in **Azure Migrate - Servers** > **Azure Migrate: Server Assessment** pagina op het pictogram dat het aantal voor **gedetecteerde servers**weergeeft.


## <a name="next-steps"></a>Volgende stappen

Probeer [Hyper-V-beoordeling](tutorial-assess-hyper-v.md) uit met Azure Migrate Server Assessment.
