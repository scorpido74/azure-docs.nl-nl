---
title: Een Azure Migrate apparaat instellen voor VMware
description: Meer informatie over het instellen van een Azure Migrate apparaat om VMware-Vm's te beoordelen en te migreren.
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: b32c6a9b703e4d341fe353d6b472ea7a18adadf3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81538253"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Een apparaat instellen voor VMware-Vm's

Volg dit artikel om het Azure Migrate-apparaat in te stellen voor evaluatie met het [Azure migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) tool en voor migratie zonder agent met behulp van het Azure migrate: hulp programma voor [server migratie](migrate-services-overview.md#azure-migrate-server-migration-tool) .

Het [Azure migrate apparaat](migrate-appliance.md) is een licht gewicht dat door Azure migrate wordt gebruikt: Server evaluatie en server migratie om on-premises virtuele VMware-machines te detecteren, VM-meta gegevens/prestatie gegevens naar Azure te verzenden en voor replicatie van virtuele VMware-machines tijdens migratie zonder agent.

U kunt het apparaat implementeren met een aantal methoden:

- Ingesteld op een virtuele VMware-machine met behulp van een gedownloade eicellen-sjabloon. Dit is de methode die in dit artikel wordt beschreven.
- Ingesteld op een VMware-VM of fysieke machine met een Power shell-installatie script. [Deze methode](deploy-appliance-script.md) moet worden gebruikt als u geen virtuele machine kunt instellen met behulp van een eicellen-sjabloon of als u zich in azure Government bevindt.

Nadat u het apparaat hebt gemaakt, controleert u of er verbinding kan worden gemaakt met Azure Migrate: Server evaluatie, het voor de eerste keer configureren en registreren bij het Azure Migrate-project.


## <a name="appliance-deployment-ova"></a>Implementatie van het apparaat (eicellen)

Als u het apparaat wilt instellen met behulp van een eicellen-sjabloon, doet u het volgende:
- Down load een bestand met een eicellen-sjabloon en importeer het naar vCenter Server.
- Maak het apparaat en controleer of het verbinding kan maken met Azure Migrate server beoordeling.
- Configureer het apparaat voor de eerste keer en registreer het bij het Azure Migrate-project.

## <a name="download-the-ova-template"></a>De eicellen-sjabloon downloaden

1. In **migratie doelen** > **servers** > **Azure migrate: Server evaluatie**, klikt u op **detecteren**.
2. **Zijn uw machines**in **Discover-computers** > gevirtualiseerd?, klikt u op **Ja, met VMware vSphere Hyper Visor**.
3. Klik op **Downloaden** om het .OVA-sjabloonbestand te downloaden.

  ![Selecties voor het downloaden van een bestand met een eicellen](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>Beveiliging controleren

Controleer of het bestand van de eicellen veilig is voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Voer de volgende opdracht uit om de hash voor de eicellen te genereren:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gebruiksvoorbeeld: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. De gegenereerde hash moet overeenkomen met deze [instellingen](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware#verify-security)voor de meest recente versie van het apparaat.



## <a name="create-the-appliance-vm"></a>De apparaat-VM maken

Importeer het gedownloade bestand en maak een virtuele machine.

1. Klik in de vSphere-client console op **bestand** > **implementeren OVF sjabloon**.
![Menu opdracht voor het implementeren van een OVF-sjabloon](./media/tutorial-assess-vmware/deploy-ovf.png)

2. Geef in de wizard OVF-sjabloon implementeren > **bron**de locatie op van het bestand van de eicellen.
3. Geef bij **naam** en **locatie**een beschrijvende naam op voor de virtuele machine. Selecteer het inventaris object waarin de VM wordt gehost.
5. Geef in **host/cluster**de host of het cluster op waarop de virtuele machine wordt uitgevoerd.
6. Geef in **opslag**de opslag bestemming voor de virtuele machine op.
7. Geef in **Schijfindeling** het schijftype en de schijfgrootte op.
8. Geef bij **netwerk toewijzing**het netwerk op waarmee de virtuele machine verbinding maakt. Het netwerk heeft Internet connectiviteit nodig om meta gegevens te verzenden naar Azure Migrate server-evaluatie.
9. Controleer en bevestig de instellingen en klik op **Voltooien**.


## <a name="verify-appliance-access-to-azure"></a>Toestel toegang tot Azure controleren

Zorg ervoor dat de virtuele machine van het apparaat verbinding kan maken met Azure-Url's voor [open bare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls) Clouds.


## <a name="configure-the-appliance"></a>Het apparaat configureren

Het apparaat voor de eerste keer instellen. Als u het apparaat implementeert met behulp van een script in plaats van een eicellen-sjabloon, zijn de eerste twee stappen in de procedure niet van toepassing.

1. Klik in de vSphere Client-console met de rechtermuisknop op de VM > **Console openen**.
2. Geef de taal, de tijd zone en het wacht woord op voor het apparaat.
3. Open een browser op een computer die verbinding kan maken met de virtuele machine en open de URL van de Web-App van het apparaat: **https://*-apparaatnaam of IP-adres*: 44368**.

   U kunt de app ook vanuit het toestel bureau blad openen door te klikken op de snelkoppeling naar de app.
4. Ga als volgt te werk in de web-app > vereisten in te **stellen**:
    - **Licentie**: accepteer de licentie voorwaarden en lees de informatie van derden.
    - **Connectiviteit**: de app controleert of de virtuele machine toegang heeft tot internet. Als de virtuele machine gebruikmaakt van een proxy:
        - Klik op **proxy-instellingen**en geef het proxy adres en de luister poort op in http://ProxyIPAddress het http://ProxyFQDNformulier of.
        - Geef referenties op als de proxy verificatie nodig heeft.
        - Alleen HTTP-proxy wordt ondersteund.
    - **Tijd synchronisatie**: tijd wordt gecontroleerd. De tijd op het apparaat moet zijn gesynchroniseerd met internet tijd zodat detectie goed werkt.
    - **Updates installeren**: Azure migrate controleert of de nieuwste updates van het apparaat zijn geïnstalleerd.
    - **Installeer VdDK**: Azure migrate controleert of de VMware VSphere Virtual Disk Development Kit (vddk) is geïnstalleerd.
        - Azure Migrates maakt gebruik van de VDDK om machines te repliceren tijdens de migratie naar Azure.
        - Down load VDDK 6,7 van VMware en extraheer de gedownloade zip-inhoud naar de opgegeven locatie op het apparaat.

## <a name="register-the-appliance-with-azure-migrate"></a>Het apparaat registreren bij Azure Migrate

1. Klik op **Aanmelden**. Als deze niet wordt weer gegeven, controleert u of de pop-upblokkering in de browser is uitgeschakeld.
2. Meld u aan met uw Azure-referenties op het tabblad Nieuw.
    - Meld u aan met uw gebruikers naam en wacht woord.
    - Aanmelden met een pincode wordt niet ondersteund.
3. Nadat u zich hebt aangemeld, gaat u terug naar de web-app.
2. Selecteer het abonnement waarin het Azure Migrate-project is gemaakt. Selecteer vervolgens het project.
3. Geef een naam op voor het apparaat. De naam moet alfanumeriek zijn met 14 tekens of minder.
4. Klik op **registreren**.


## <a name="start-continuous-discovery-by-providing-vcenter-server-and-vm-credential"></a>Continue detectie starten door vCenter Server en VM-referentie op te geven

Het apparaat moet verbinding maken met vCenter Server om de configuratie-en prestatie gegevens van de virtuele machines te detecteren.

### <a name="specify-vcenter-server-details"></a>vCenter Server-gegevens opgeven
1. Geef in **vCenter Server Details opgeven**de naam (FQDN) of het IP-adres van de vCenter Server op. U kunt de standaard poort verlaten of een aangepaste poort opgeven waarop uw vCenter Server luistert.
2. Geef bij **gebruikers naam** en **wacht woord**de alleen-lezen account referenties op die het apparaat gebruikt voor het detecteren van vm's op de vCenter-Server. U kunt de detectie bereiken door de toegang tot het vCenter-account te beperken. [Meer informatie](set-discovery-scope.md).
3. Klik op **verbinding valideren** om te controleren of het apparaat verbinding kan maken met vCenter Server.

### <a name="specify-vm-credentials"></a>VM-referenties opgeven
Voor de detectie van toepassingen, functies en onderdelen en het visualiseren van afhankelijkheden van de virtuele machines, kunt u een VM-referentie opgeven die toegang heeft tot de virtuele VMware-machines. U kunt één referentie toevoegen voor Windows-Vm's en één referentie voor Linux-Vm's. Meer [informatie](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) over de benodigde toegangs rechten.

> [!NOTE]
> Deze invoer is optioneel en is nodig voor het inschakelen van toepassings detectie en visualisatie van de afhankelijkheid van agents.

1. In **toepassingen en afhankelijkheden detecteren op vm's**, klikt u op **referenties toevoegen**.
2. Selecteer het **besturings systeem**.
3. Geef een beschrijvende naam op voor de referentie.
4. Geef in **gebruikers naam** en **wacht woord**een account op met ten minste gast toegang op de vm's.
5. Klik op **Add**.

Nadat u de vCenter Server-en VM-referenties (optioneel) hebt opgegeven, klikt u op **opslaan en detectie starten** om de detectie van de on-premises omgeving te starten.

Het duurt ongeveer 15 minuten voor de meta gegevens van gedetecteerde Vm's die in de portal worden weer gegeven. De detectie van geïnstalleerde toepassingen, functies en onderdelen vergt enige tijd, de duur is afhankelijk van het aantal Vm's dat wordt gedetecteerd. Voor 500 Vm's duurt het ongeveer 1 uur voordat de toepassings voorraad wordt weer gegeven in de Azure Migrate Portal.

## <a name="next-steps"></a>Volgende stappen

Bekijk de zelf studies voor [VMware-evaluatie](tutorial-assess-vmware.md) en migratie zonder [agent](tutorial-migrate-vmware.md).
