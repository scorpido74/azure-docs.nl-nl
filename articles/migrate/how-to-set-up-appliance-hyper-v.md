---
title: Een Azure Migrate apparaat instellen voor Hyper-V
description: Meer informatie over het instellen van een Azure Migrate apparaat om Hyper-V-Vm's te beoordelen en te migreren.
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: 21d88c4a2b2095fe677fe479bd7320f7a494db9e
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88929917"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Een apparaat instellen voor virtuele Hyper-V-machines

Volg dit artikel om het Azure Migrate-apparaat in te stellen voor de evaluatie van virtuele Hyper-V-machines met het [Azure migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) tool.

Het [Azure migrate apparaat](migrate-appliance.md)  is een licht gewicht dat door Azure migrate wordt gebruikt: Server evaluatie/migratie om on-premises virtuele Hyper-V-machines te detecteren en VM-meta gegevens/prestatie gegevens naar Azure te verzenden.

U kunt het apparaat implementeren met een aantal methoden:

- Instellen op een Hyper-V-VM met behulp van een gedownloade VHD. Dit is de methode die in dit artikel wordt beschreven.
- Ingesteld op een Hyper-V-VM of fysieke machine met een PowerShell-installatiescript. [Deze methode](deploy-appliance-script.md) moet worden gebruikt als u geen virtuele machine kunt instellen met behulp van een VHD of als u zich in azure Government bevindt.

Nadat u het apparaat hebt gemaakt, controleert u of er verbinding kan worden gemaakt met Azure Migrate-serverevaluatie, configureert u het voor de eerste keer en registreert u het bij het Azure Migrate-project.

## <a name="appliance-deployment-vhd"></a>Implementatie van het apparaat (VHD)

Het apparaat instellen met behulp van een VHD-sjabloon:

- Geef een naam op voor het apparaat en Genereer een Azure Migrate project sleutel in de portal.
- Down load een gecomprimeerde Hyper-V VHD vanuit het Azure Portal.
- Maak het apparaat en controleer of het verbinding kan maken met Azure Migrate server beoordeling.
- Configureer het apparaat voor de eerste keer en registreer het met het Azure Migrate project met behulp van de Azure Migrate-project sleutel.

### <a name="generate-the-azure-migrate-project-key"></a>De Azure Migrate project sleutel genereren

1. In **Migratiedoelen** > **Servers** > **Azure Migrate: Serverevaluatie** selecteert u **Detecteren**.
2. In **Machines ontdekken** > **Zijn de machines gevirtualiseerd?** selecteert u **Ja, met Hyper-V**.
3. In **1: Azure migrate project sleutel genereren**geeft u een naam op voor het Azure migrate apparaat dat u voor de detectie van Hyper-V-vm's wilt instellen. de naam moet alfanumeriek zijn met 14 tekens of minder.
1. Klik op **sleutel genereren** om te beginnen met het maken van de vereiste Azure-resources. Sluit de pagina computers detecteren niet af tijdens het maken van resources.
1. Nadat het maken van de Azure-resources is voltooid, wordt er een **Azure migrate project sleutel** gegenereerd.
1. Kopieer de sleutel, omdat u deze nodig hebt om de registratie van het apparaat tijdens de configuratie te volt ooien.

### <a name="download-the-vhd"></a>De VHD downloaden

In **2: Azure migrate apparaat downloaden**, selecteert u de. VHD-bestand en klik op **downloaden**. 

   ![Selecties voor discover-computers](./media/tutorial-assess-hyper-v/servers-discover.png)


   ![Selecties voor sleutel genereren](./media/tutorial-assess-hyper-v/generate-key-hyperv.png)


### <a name="verify-security"></a>Beveiliging controleren

Controleer of het zip-bestand veilig is voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Voer de volgende opdracht uit om de hash voor de VHD te genereren
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gebruiksvoorbeeld: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.vhd SHA256```
3.  Voor de toestel versie 2.19.11.12 moet de gegenereerde hash overeenkomen met deze [instellingen](./tutorial-assess-hyper-v.md#verify-security).




## <a name="create-the-appliance-vm"></a>Het VM-apparaat maken

Importeer het gedownloade bestand en maak de virtuele machine.

1. Pak het gecomprimeerde VHD-bestand uit in een map op de Hyper-V-host die als host fungeert voor de toestel-VM. Er worden drie mappen geëxtraheerd.
2. Open Hyper-V-beheer. Klik in **Acties** op **Virtuele machine importeren**.

    ![VHD implementeren](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. Klik in de wizard Virtuele machine importeren > **Voordat u begint** op **Volgende**.
3. In **map zoeken**geeft u de map op die de geëxtraheerde VHD bevat. Klik op **Volgende**.
1. Klik in **Virtuele machine selecteren** op **Volgende**.
2. Klik in **Importtype kiezen** op **De virtuele machine kopiëren (een nieuwe unieke id maken)** . Klik op **Volgende**.
3. Laat in **Bestemming kiezen** de standaardinstelling ongewijzigd. Klik op **Volgende**.
4. Laat in **Opslagmappen** de standaardinstelling ongewijzigd. Klik op **Volgende**.
5. Geef in **Netwerk kiezen** de virtuele switch op die door de virtuele machine wordt gebruikt. De switch heeft internetverbinding nodig om gegevens naar Azure te verzenden.
6. Controleer de instellingen in **Samenvatting**. Klik vervolgens op **Voltooien**.
7. Start de virtuele machine in Hyper-V-beheer > **Virtual Machines**.


### <a name="verify-appliance-access-to-azure"></a>Apparaattoegang tot Azure controleren

Zorg ervoor dat de apparaat-VM verbinding kan maken met Azure-URL's voor [openbare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls)clouds.

### <a name="configure-the-appliance"></a>Het apparaat configureren

Het apparaat voor de eerste keer instellen.

> [!NOTE]
> Als u het apparaat instelt met behulp van een [PowerShell-script](deploy-appliance-script.md) in plaats van de gedownloade VHD, zijn de eerste twee stappen in deze procedure niet relevant.

1. Klik in Hyper-V-beheer > **Virtual Machines** met de rechtermuisknop op de virtuele machine > **Verbinding maken**.
2. Geef de taal, de tijdzone en een wachtwoord op voor het apparaat.
3. Open een browser op een computer die verbinding kan maken met de VM en open de URL van de web-app van het apparaat: **https://*apparaatnaam of IP-adres*: 44368**.

   U kunt de app ook openen vanaf het bureaublad van het apparaat door te klikken op de snelkoppeling naar de app.
1. Ga akkoord met de **licentie voorwaarden**en lees de informatie van derden.
1. Ga als volgt te werk in de web-app > **Vereisten instellen**:
    - **Connectiviteit**: de app controleert of de virtuele machine toegang heeft tot internet. Als de virtuele machine een proxy gebruikt:
      - Klik op **proxy instellen** en geef het proxy adres op (in de vorm http://ProxyIPAddress of http://ProxyFQDN) en luister poort.
      - Geef referenties op als de proxy verificatie nodig heeft.
      - Alleen HTTP-proxy wordt ondersteund.
      - Als u proxy Details hebt toegevoegd of de proxy en/of de verificatie hebt uitgeschakeld, klikt u op **Opslaan** om de connectiviteits controle opnieuw te activeren.
    - **Tijdsynchronisatie**: Tijd is geverifieerd. De tijd op het apparaat moet zijn gesynchroniseerd met internettijd voor VM zodat detectie goed werkt.
    - **Updates installeren**: Azure migrate server beoordeling controleert of de meest recente updates zijn geïnstalleerd op het apparaat. Nadat de controle is voltooid, kunt u op **apparaatdiensten weer geven** klikken om de status en versies van de onderdelen die op het apparaat worden uitgevoerd te bekijken.

### <a name="register-the-appliance-with-azure-migrate"></a>Het apparaat registreren bij Azure Migrate

1. Plak de **Azure migrate project sleutel** die u hebt gekopieerd uit de portal. Als u de sleutel niet hebt, gaat u naar **Server Assessment> Discover> bestaande apparaten beheren**, selecteert u de naam van het apparaat dat u hebt ingevoerd op het moment van sleutel genereren en kopieert u de bijbehorende sleutel.
1. Klik op **Aanmelden**. Er wordt een Azure-aanmeldings prompt geopend in een nieuw browser tabblad. Als deze niet wordt weer gegeven, controleert u of de pop-upblokkering in de browser is uitgeschakeld.
1. Meld u op het nieuwe tabblad aan met de gebruikersnaam en het wachtwoord van Azure.
   
   Aanmelden met een pincode wordt niet ondersteund.
3. Nadat u zich hebt aangemeld, gaat u terug naar de web-app. 
4. Als het Azure-gebruikers account dat wordt gebruikt voor logboek registratie over de juiste [machtigingen](tutorial-prepare-hyper-v.md#prepare-azure) beschikt voor de Azure-resources die zijn gemaakt tijdens het genereren van sleutels, wordt de registratie van het apparaat gestart.
1. Nadat het apparaat is geregistreerd, kunt u de registratie details zien door te klikken op **Details weer geven**.



### <a name="delegate-credentials-for-smb-vhds"></a>Referenties voor SMB-VHD's delegeren

Als u VHD's uitvoert op SMB's, moet u de delegatie van referenties van het apparaat naar de Hyper-V-hosts inschakelen. Als u dit wilt doen vanaf het apparaat:

1. Voer deze opdracht uit op de apparaat-VM. HyperVHost1/HyperVHost2 zijn voorbeelden van hostnamen.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. U kunt dit ook doen in de editor voor Lokaal groepsbeleid op het apparaat:
    - Klik in **Lokaal computerbeleid** > **Computerconfiguratie** op **Administratieve sjablonen** > **Systeem** > **Delegatie van referenties**.
    - Dubbelklik op **Delegeren van nieuwe referenties toestaan** en selecteer **Ingeschakeld**.
    - Klik in **Opties** op **Weergeven** en voeg elke Hyper-V-host toe die u op de lijst wilt detecteren, waarbij u **wsman/** gebruikt als voorvoegsel.
    - Dubbel klik in het overdragen van  **referenties**op het **delegeren van nieuwe referenties toestaan met NTLM-Server verificatie**. Voeg weer elke Hyper-V-host toe die u op de lijst wilt detecteren, met **wsman/** als voorvoegsel.

## <a name="start-continuous-discovery"></a>Continue detectie starten

Maak vanaf het apparaat verbinding met Hyper-V-hosts of -clusters en start de VM-detectie.

1. In **stap 1: referenties opgeven voor hyper-v-host**, klikt u op **referenties toevoegen** om een beschrijvende naam op te geven voor referenties, **gebruikers naam** en **wacht woord** voor een Hyper-V-host/cluster toevoegen die door het apparaat wordt gebruikt voor het detecteren van vm's. Klik op **Opslaan**.
1. Als u meerdere referenties tegelijk wilt toevoegen, klikt u op **meer toevoegen** om de referenties op te slaan en toe te voegen. Er worden meerdere referenties ondersteund voor de detectie van Hyper-V-Vm's.
1. In **stap 2: Geef de gegevens van de hyper-v-host/het cluster**op en klik op **detectie bron toevoegen** om de hyper-v-host/het **IP-adres/FQDN** en de beschrijvende naam op te geven voor de referenties waarmee verbinding wordt gemaakt met de host/het cluster.
1. U kunt **één item tegelijk toevoegen** of **meerdere items** in één keer toevoegen. Er is ook een optie om de Hyper-V-host/cluster gegevens op te geven via CSV-bestand **importeren**.

    ![Selecties voor het toevoegen van de detectie bron](./media/tutorial-assess-hyper-v/add-discovery-source-hyperv.png)

    - Als u **één item toevoegen**kiest, moet u beschrijvende naam voor referenties en Hyper-V-host/cluster **-IP-adres/FQDN** opgeven en op **Opslaan**klikken.
    - Als u **meerdere items toevoegen** _(standaard geselecteerd)_ hebt gekozen, kunt u meerdere records tegelijk toevoegen door een **IP-adres/FQDN** voor de Hyper-V-host/het cluster op te geven met de beschrijvende naam voor de referenties in het tekstvak. **Controleer** de toegevoegde records en klik op **Opslaan**.
    - Als u **CSV importeren**kiest, kunt u een CSV-sjabloon bestand downloaden, het bestand vullen met de Hyper-V-host/het **IP-adres** van het cluster/FQDN en beschrijvende naam voor referenties. Vervolgens importeert u het bestand in het apparaat, **controleert** u de records in het bestand en klikt u op **Opslaan**.

1. Wanneer u op Opslaan klikt, probeert het apparaat de verbinding te valideren met de Hyper-V-hosts/clusters die zijn toegevoegd en wordt de **validatie status** in de tabel voor elke host/cluster weer gegeven.
    - Voor geslaagde hosts/clusters kunt u meer details weer geven door te klikken op het IP-adres/de FQDN.
    - Als de validatie voor een host mislukt, controleert u de fout door te klikken op **validatie is mislukt** in de kolom Status van de tabel. Los het probleem op en valideer het opnieuw.
    - Als u hosts of clusters wilt verwijderen, klikt u op **verwijderen**.
    - U kunt een specifieke host niet verwijderen uit een cluster. U kunt alleen het hele cluster verwijderen.
    - U kunt een cluster toevoegen, zelfs als er problemen zijn met specifieke hosts in het cluster.
1. U kunt de connectiviteit van hosts/clusters op elk gewenst moment opnieuw **valideren** voordat de detectie wordt gestart.
1. Klik op **detectie starten**om de VM-detectie van de gevalideerde hosts/clusters te vervolledigen. Nadat de detectie is gestart, kunt u de detectie status controleren op elke host/cluster in de tabel.

De detectie wordt gestart. Het duurt ongeveer 2 minuten per host voor de meta gegevens van gedetecteerde servers die worden weer gegeven in de Azure Portal.

## <a name="verify-vms-in-the-portal"></a>VM's verifiëren in de portal

Nadat de detectie is voltooid, kunt u controleren of de VM's worden weergegeven in de portal.

1. Open het Azure Migrate-dashboard.
2. Klik op de pagina **Azure Migrate - Servers** > **Azure Migrate: Serverevaluatie** op het pictogram dat het aantal voor **Gedetecteerde servers** weergeeft.


## <a name="next-steps"></a>Volgende stappen

Probeer de [Hyper-V-evaluatie](tutorial-assess-hyper-v.md) uit met Azure migrate server-evaluatie.
