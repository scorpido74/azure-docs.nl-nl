---
title: Een Azure Migrate apparaat instellen voor VMware
description: Meer informatie over het instellen van een Azure Migrate apparaat om VMware-Vm's te beoordelen en te migreren.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/18/2019
ms.author: raynew
ms.openlocfilehash: 1489d29f854b02cf493493fe022c73dc050e2615
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185860"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Een apparaat instellen voor VMware-Vm's

In dit artikel wordt beschreven hoe u het Azure Migrate apparaat instelt als u virtuele VMware-machines evalueert met het hulp programma Azure Migrate server Assessment of virtuele VMware-machines naar Azure migreert met agentloze migratie met behulp van het hulp programma voor migratie van de Azure Migrate server.

Het VMware VM-apparaat is een licht gewicht apparaat dat wordt gebruikt door Azure Migrate server-evaluatie/-migratie om het volgende te doen:

- Ontdek on-premises virtuele VMware-machines.
- Meta gegevens en prestatie gegevens voor gedetecteerde Vm's verzenden naar Azure Migrate server evaluatie/migratie.

Meer [informatie](migrate-appliance.md) over het Azure migrate apparaat.


## <a name="appliance-deployment-steps"></a>Implementatie stappen voor het apparaat

Als u het apparaat wilt instellen, doet u het volgende:
- Down load een bestand met een eicellen-sjabloon en importeer het naar vCenter Server.
- Maak het apparaat en controleer of het verbinding kan maken met Azure Migrate server beoordeling.
- Configureer het apparaat voor de eerste keer en registreer het bij het Azure Migrate-project.

## <a name="download-the-ova-template"></a>De eicellen-sjabloon downloaden

1. In **migratie doelen** > **servers** > **Azure migrate: Server evaluatie**, klikt u op **ontdekken**.
2. In **Machines ontdekken** > **Zijn de machines gevirtualiseerd?** klikt u op **Ja, met VMWare vSphere-hypervisor**.
3. Klik op **Downloaden** om het .OVA-sjabloonbestand te downloaden.



### <a name="verify-security"></a>Beveiliging controleren

Controleer of het bestand van de eicellen veilig is voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Voer de volgende opdracht uit om de hash voor de eicellen te genereren:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gebruiksvoorbeeld: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. De gegenereerde hash moet overeenkomen met deze instellingen voor de meest recente versie van het apparaat.

  **Algoritme** | **Hash-waarde**
  --- | ---
  MD5 | c06ac2a2c0f870d3b274a0b7a73b78b1
  SHA256 | 4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c


## <a name="create-the-appliance-vm"></a>De apparaat-VM maken

Importeer het gedownloade bestand en maak een virtuele machine.

1. Klik in de Client vSphere-console op **Bestand** > **OVF-sjabloon implementeren**.
2. Geef in de wizard OVF-sjabloon implementeren > **bron**de locatie op van het bestand van de eicellen.
3. Geef bij **naam** en **locatie**een beschrijvende naam op voor de virtuele machine. Selecteer het inventaris object waarin de VM wordt gehost.
5. Geef in **host/cluster**de host of het cluster op waarop de virtuele machine wordt uitgevoerd.
6. Geef in **opslag**de opslag bestemming voor de virtuele machine op.
7. Geef in **Schijfindeling** het schijftype en de schijfgrootte op.
8. Geef bij **netwerk toewijzing**het netwerk op waarmee de virtuele machine verbinding maakt. Het netwerk heeft Internet connectiviteit nodig om meta gegevens te verzenden naar Azure Migrate server-evaluatie.
9. Controleer en bevestig de instellingen en klik op **Voltooien**.


### <a name="verify-appliance-access-to-azure"></a>Toestel toegang tot Azure controleren

Zorg ervoor dat de virtuele machine van het apparaat verbinding kan maken met [Azure-url's](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


## <a name="configure-the-appliance"></a>Het apparaat configureren

Het apparaat voor de eerste keer instellen.

1. Klik in de vSphere Client-console met de rechtermuisknop op de VM > **Console openen**.
2. Geef de taal, de tijd zone en het wacht woord op voor het apparaat.
3. Open een browser op een computer die verbinding kan maken met de virtuele machine en open de URL van de Web-App van het apparaat: **https:// *-apparaatnaam of IP-adres*: 44368**.

   U kunt de app ook vanuit het toestel bureau blad openen door te klikken op de snelkoppeling naar de app.
4. Ga als volgt te werk in de web-app > vereisten in te **stellen**:
    - **Licentie**: accepteer de licentie voorwaarden en lees de informatie van derden.
    - **Connectiviteit**: de app controleert of de virtuele machine toegang heeft tot internet. Als de virtuele machine gebruikmaakt van een proxy:
        - Klik op **proxy-instellingen**en geef het proxy adres en de luister poort op in de notatie http://ProxyIPAddress of http://ProxyFQDN.
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
2. Geef bij **gebruikers naam** en **wacht woord**de alleen-lezen account referenties op die het apparaat gebruikt voor het detecteren van vm's op de vCenter-Server. Zorg ervoor dat het account de [vereiste machtigingen voor detectie](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions)heeft. U kunt de detectie bereiken door de toegang tot het vCenter-account dienovereenkomstig te beperken. meer informatie over het detecteren van scopes [vindt u hier](tutorial-assess-vmware.md#scoping-discovery).
3. Klik op **verbinding valideren** om te controleren of het apparaat verbinding kan maken met vCenter Server.

### <a name="specify-vm-credentials"></a>VM-referenties opgeven
Voor de detectie van toepassingen, functies en onderdelen en het visualiseren van afhankelijkheden van de virtuele machines, kunt u een VM-referentie opgeven die toegang heeft tot de virtuele VMware-machines. U kunt één referentie toevoegen voor Windows-Vm's en één referentie voor Linux-Vm's. Meer [informatie](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-vcenter-server-permissions) over de benodigde toegangs rechten.

> [!NOTE]
> Deze invoer is optioneel en is nodig voor het inschakelen van toepassings detectie en visualisatie van de afhankelijkheid van agents.

1. In **toepassingen en afhankelijkheden detecteren op vm's**, klikt u op **referenties toevoegen**.
2. Selecteer het **besturings systeem**.
3. Geef een beschrijvende naam op voor de referentie.
4. Geef in **gebruikers naam** en **wacht woord**een account op met ten minste gast toegang op de vm's.
5. Klik op **Toevoegen**.

Nadat u de vCenter Server-en VM-referenties (optioneel) hebt opgegeven, klikt u op **opslaan en detectie starten** om de detectie van de on-premises omgeving te starten.

Het duurt ongeveer 15 minuten voor de meta gegevens van gedetecteerde Vm's die in de portal worden weer gegeven. De detectie van geïnstalleerde toepassingen, functies en onderdelen vergt enige tijd, de duur is afhankelijk van het aantal Vm's dat wordt gedetecteerd. Voor 500 Vm's duurt het ongeveer 1 uur voordat de toepassings voorraad wordt weer gegeven in de Azure Migrate Portal.

## <a name="next-steps"></a>Volgende stappen

Bekijk de zelf studies voor [VMware-evaluatie](tutorial-assess-vmware.md) en migratie zonder [agent](tutorial-migrate-vmware.md).
