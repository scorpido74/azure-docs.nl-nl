---
title: Een Azure Migrate apparaat instellen voor VMware
description: Meer informatie over het instellen van een Azure Migrate apparaat om VMware-Vm's te beoordelen en te migreren.
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: c9a9f1567f984fc5770b47d3998610cb69643360
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88923607"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Een apparaat instellen voor VMware-Vm's

Volg dit artikel om het Azure Migrate-apparaat in te stellen voor evaluatie met het [Azure migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) tool en voor migratie zonder agent met behulp van het Azure migrate: hulp programma voor [server migratie](migrate-services-overview.md#azure-migrate-server-migration-tool) .

Het [Azure migrate apparaat](migrate-appliance.md) is een licht gewicht dat door Azure migrate wordt gebruikt: Server evaluatie en server migratie om on-premises virtuele VMware-machines te detecteren, VM-meta gegevens/prestatie gegevens naar Azure te verzenden en voor replicatie van virtuele VMware-machines tijdens migratie zonder agent.

U kunt het apparaat implementeren met een aantal methoden:

- Ingesteld op een virtuele VMware-machine met behulp van een gedownloade OVA-sjabloon. Dit is de methode die in dit artikel wordt beschreven.
- Ingesteld op een VMware-VM of fysieke machine met een PowerShell-installatiescript. [Deze methode](deploy-appliance-script.md) moet worden gebruikt als u geen virtuele machine kunt instellen met behulp van een eicellen-sjabloon of als u zich in azure Government bevindt.

Nadat u het apparaat hebt gemaakt, controleert u of er verbinding kan worden gemaakt met Azure Migrate-serverevaluatie, configureert u het voor de eerste keer en registreert u het bij het Azure Migrate-project.


## <a name="appliance-deployment-ova"></a>Implementatie van het apparaat (eicellen)

Als u het apparaat wilt instellen met behulp van een eicellen-sjabloon, doet u het volgende:
- Geef een naam op voor het apparaat en Genereer een Azure Migrate project sleutel in de portal
- Down load een bestand met een eicellen-sjabloon en importeer het naar vCenter Server.
- Maak het apparaat en controleer of het verbinding kan maken met Azure Migrate server beoordeling.
- Configureer het apparaat voor de eerste keer en registreer het met het Azure Migrate project met behulp van de Azure Migrate-project sleutel.

### <a name="generate-the-azure-migrate-project-key"></a>De Azure Migrate project sleutel genereren

1. In **Migratiedoelen** > **Servers** > **Azure Migrate: Serverevaluatie** selecteert u **Detecteren**.
2. In **Machines ontdekken** > **Zijn de machines gevirtualiseerd?** selecteert u **Ja, met VMware vSphere-hypervisor**.
3. In **1: Azure migrate project sleutel genereren**geeft u een naam op voor het Azure migrate apparaat dat u voor de detectie van virtuele VMware-machines gaat instellen. de naam moet alfanumeriek zijn met 14 tekens of minder.
1. Klik op **sleutel genereren** om te beginnen met het maken van de vereiste Azure-resources. Sluit de pagina computers detecteren niet af tijdens het maken van resources.
1. Nadat het maken van de Azure-resources is voltooid, wordt er een **Azure migrate project sleutel** gegenereerd.
1. Kopieer de sleutel, omdat u deze nodig hebt om de registratie van het apparaat tijdens de configuratie te volt ooien.

### <a name="download-the-ova-template"></a>De OVA-sjabloon downloaden
In **2: Azure migrate apparaat downloaden**, selecteert u de. EICELLEN bestand en klik op **downloaden**. 


   ![Selecties voor discover-computers](./media/tutorial-assess-vmware/servers-discover.png)


   ![Selecties voor sleutel genereren](./media/tutorial-assess-vmware/generate-key-vmware.png)

### <a name="verify-security"></a>Beveiliging controleren

Controleer of het bestand van de eicellen veilig is voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Voer de volgende opdracht uit om de hash voor de eicellen te genereren:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gebruiksvoorbeeld: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. De gegenereerde hash moet overeenkomen met deze [instellingen](./tutorial-assess-vmware.md#verify-security)voor de meest recente versie van het apparaat.



## <a name="create-the-appliance-vm"></a>Het VM-apparaat maken

Importeer het gedownloade bestand en maak een virtuele machine.

1. Klik in de vSphere-client console op **bestand**  >  **implementeren OVF sjabloon**.
![Menuopdracht voor het implementeren van een OVF-sjabloon](./media/tutorial-assess-vmware/deploy-ovf.png)

2. Geef in de wizard OVF-sjabloon implementeren > **Bron** de locatie van het OVA-bestand op.
3. Geef bij **Naam** en **Locatie** een beschrijvende naam op voor de virtuele machine. Selecteer het inventarisobject waarin de VM wordt gehost.
5. Geef bij **Host/Cluster** de host of het cluster op waarop de VM wordt uitgevoerd.
6. Geef bij **Opslag** de opslaglocatie voor de VM op.
7. Geef in **Schijfindeling** het schijftype en de schijfgrootte op.
8. Geef bij **netwerk toewijzing**het netwerk op waarmee de virtuele machine verbinding maakt. Het netwerk heeft Internet connectiviteit nodig om meta gegevens te verzenden naar Azure Migrate server-evaluatie.
9. Controleer en bevestig de instellingen en klik op **Voltooien**.


## <a name="verify-appliance-access-to-azure"></a>Apparaattoegang tot Azure controleren

Zorg ervoor dat de apparaat-VM verbinding kan maken met Azure-URL's voor [openbare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls)clouds.


### <a name="configure-the-appliance"></a>Het apparaat configureren

Het apparaat voor de eerste keer instellen.

> [!NOTE]
> Als u het apparaat instelt met behulp van een [PowerShell-script](deploy-appliance-script.md) in plaats van de gedownloade OVA, zijn de eerste twee stappen in deze procedure niet relevant.

1. Klik in de vSphere-clientconsole met de rechtermuisknop op de VM en selecteer **Console openen**.
2. Geef de taal, de tijdzone en een wachtwoord op voor het apparaat.
3. Open een browser op een computer die verbinding kan maken met de VM en open de URL van de web-app van het apparaat: **https://*apparaatnaam of IP-adres*: 44368**.

   U kunt de app ook openen vanaf het bureaublad van het apparaat door de snelkoppeling naar de app te selecteren.
1. Ga akkoord met de **licentie voorwaarden**en lees de informatie van derden.
1. Ga als volgt te werk in de web-app > **Vereisten instellen**:
   - **Connectiviteit**: de app controleert of de virtuele machine toegang heeft tot internet. Als de virtuele machine een proxy gebruikt:
     - Klik op **proxy instellen** om het proxy adres op te geven (in de vorm http://ProxyIPAddress of http://ProxyFQDN) en luister poort.
     - Geef referenties op als de proxy verificatie nodig heeft.
     - Alleen HTTP-proxy wordt ondersteund.
     - Als u proxy Details hebt toegevoegd of de proxy en/of de verificatie hebt uitgeschakeld, klikt u op **Opslaan** om de connectiviteits controle opnieuw te activeren.
   - **Tijdsynchronisatie**: de tijd op het apparaat moet zijn gesynchroniseerd met internettijd zodat detectie goed werkt.
   - **Updates installeren**: het apparaat zorgt ervoor dat de meest recente updates zijn geïnstalleerd. Nadat de controle is voltooid, kunt u op **apparaatdiensten weer geven** klikken om de status en versies van de onderdelen die op het apparaat worden uitgevoerd te bekijken.
   - **VDDK installeren**: het apparaat controleert of VMware vSphere Virtual Disk Development Kit (VDDK) is geïnstalleerd. Als dat niet het geval is, downloadt u VDDK 6,7 van VMware en extraheert u de gedownloade zip-inhoud naar de opgegeven locatie op het apparaat, zoals is opgegeven in de **installatie-instructies**.

     Azure Migrate-servermigratie gebruikt de VDDK om computers te repliceren tijdens migratie naar Azure. 
1. Als u wilt, kunt u de vereisten op elk gewenst moment tijdens de configuratie van het apparaat **opnieuw uitvoeren** om te controleren of het apparaat voldoet aan alle vereisten.

### <a name="register-the-appliance-with-azure-migrate"></a>Het apparaat registreren bij Azure Migrate

1. Plak de **Azure migrate project sleutel** die u hebt gekopieerd uit de portal. Als u de sleutel niet hebt, gaat u naar **Server Assessment> Discover> bestaande apparaten beheren**, selecteert u de naam van het apparaat dat u hebt ingevoerd op het moment van sleutel genereren en kopieert u de bijbehorende sleutel.
1. Klik op **Aanmelden**. Er wordt een Azure-aanmeldings prompt geopend in een nieuw browser tabblad. Als deze niet wordt weer gegeven, controleert u of de pop-upblokkering in de browser is uitgeschakeld.
1. Meld u op het nieuwe tabblad aan met de gebruikersnaam en het wachtwoord van Azure.
   
   Aanmelden met een pincode wordt niet ondersteund.
3. Nadat u zich hebt aangemeld, gaat u terug naar de web-app. 
4. Als het Azure-gebruikers account dat wordt gebruikt voor logboek registratie over de juiste [machtigingen](tutorial-prepare-vmware.md#prepare-azure) beschikt voor de Azure-resources die zijn gemaakt tijdens het genereren van sleutels, wordt de registratie van het apparaat gestart.
1. Nadat het apparaat is geregistreerd, kunt u de registratie details zien door te klikken op **Details weer geven**.


## <a name="start-continuous-discovery"></a>Continue detectie starten

Het apparaat moet verbinding maken met vCenter Server om de configuratie- en prestatiegegevens van de virtuele machines te detecteren.

1. In **stap 1: geef vCenter Server referenties**op en klik op **referenties toevoegen** om een beschrijvende naam voor de referenties op te geven, Voeg **gebruikers naam** en **wacht woord** toe voor het vCenter Server account dat het apparaat gebruikt voor het detecteren van vm's op het vCenter Server exemplaar.
    - U moet een account met de vereiste machtigingen hebben ingesteld in de [vorige zelfstudie](tutorial-prepare-vmware.md#set-up-permissions-for-assessment).
    - Als u het detectiebereik wilt beperken tot specifieke VMware-objecten (vCenter Server-datacenters, clusters, een map met clusters, hosts, een map met hosts of afzonderlijke VM's), raadpleegt u de instructies in [dit artikel](set-discovery-scope.md) om het account dat wordt gebruikt door Azure Migrate te begrenzen.
1. In **stap 2: geef vCenter Server Details**op, klik op **detectie bron toevoegen** om de beschrijvende naam voor referenties in de vervolg keuzelijst te selecteren, geeft u het **IP-adres/de FQDN** van de vCenter Server instantie op. U kunt de **poort** op de standaard waarde (443) laten staan of een aangepaste poort opgeven waarop vCenter Server luistert en klikt op **Opslaan**.
1. Wanneer u op Opslaan klikt, probeert het apparaat de verbinding met de vCenter Server te valideren met de referenties die zijn gegeven en wordt de **validatie status** in de tabel vergeleken met het vCenter Server IP-adres/de FQDN-naam.
1. U kunt de verbinding met vCenter Server op elk gewenst moment opnieuw **valideren** voordat de detectie wordt gestart.
1. In **stap 3: VM-referenties opgeven om geïnstalleerde toepassingen te detecteren en afhankelijkheids toewijzing zonder agent uit te voeren**, klikt u op **referenties toevoegen**en geeft u het besturings systeem op waarvoor de referenties zijn opgegeven, beschrijvende naam voor referenties en de **gebruikers naam** en het **wacht woord**. Klik vervolgens op **Opslaan**.

    - U kunt hier eventueel referenties toevoegen als u een account hebt gemaakt dat u wilt gebruiken voor de functie voor [toepassingsdetectie](how-to-discover-applications.md) of de [functie voor analyse van afhankelijkheden zonder agent](how-to-create-group-machine-dependencies-agentless.md).
    - Als u deze functies niet wilt gebruiken, kunt u op de schuif regelaar klikken om de stap over te slaan. U kunt de intentie op elk gewenst moment ongedaan maken.
    - Controleer de referenties die nodig zijn voor [toepassings detectie](migrate-support-matrix-vmware.md#application-discovery-requirements)of voor een [afhankelijkheids analyse zonder agent](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless).

5. Klik op **detectie starten**om de virtuele machine te detecteren. Nadat de detectie is gestart, kunt u de detectie status controleren op basis van het vCenter Server IP-adres/de FQDN in de tabel.

Detectie werkt als volgt:
- Het duurt ongeveer 15 minuten voordat gedetecteerde VM-metagegevens worden weergegeven in de portal.
- Detectie van geïnstalleerde toepassingen, functies en onderdelen kan enige tijd duren. De duur is afhankelijk van het aantal VM's dat wordt gedetecteerd. Voor 500 VM's duurt het ongeveer een uur voordat de inventaris van toepassingen wordt weergegeven in de Azure Migrate-portal.

## <a name="next-steps"></a>Volgende stappen

Bekijk de zelf studies voor [VMware-evaluatie](tutorial-assess-vmware.md) en migratie zonder [agent](tutorial-migrate-vmware.md).
