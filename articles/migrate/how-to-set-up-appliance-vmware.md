---
title: Een Azure Migrate-toestel instellen voor VMware
description: Meer informatie over het instellen van een Azure Migrate-toestel om Vm's van VMware te beoordelen en te migreren.
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: 7a7d0007d2824abc781411f9529f9fa4ac89e55c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336796"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Een toestel instellen voor VMware VM's

In dit artikel wordt beschreven hoe u het Azure Migrate appliance instelt voor beoordeling met het [Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) tool en voor agentless migratie met behulp van het Azure [Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) tool.

Het [Azure Migrate-toestel](migrate-appliance.md) is een lichtgewicht toestel dat wordt gebruikt door Azure Migrate:Server Assessment en Server Migration om on-premises Vm's vMware te ontdekken, VM-metagegevens/prestatiegegevens naar Azure te verzenden en voor replicatie van VMware VM's tijdens agentloze migratie.

U het Azure Migrate-toestel instellen voor VMware VM-beoordeling met behulp van een OVA-sjabloon die u downloadt of met een PowerShell-installatiescript. In dit artikel wordt beschreven hoe u het toestel instelt met behulp van de OVA-sjabloon. Als u het toestel met het script wilt instellen, volgt u de instructies in [dit artikel.](deploy-appliance-script.md)


## <a name="appliance-deployment-ova"></a>Toestelimplementatie (OVA)

Ga als u het toestel instelt met een OVA-sjabloon:
- Download een OVA-sjabloonbestand en importeer het naar vCenter Server.
- Maak het toestel en controleer of het verbinding kan maken met Azure Migrate Server Assessment.
- Configureer het toestel voor de eerste keer en registreer het met het Azure Migrate-project.

## <a name="download-the-ova-template"></a>De OVA-sjabloon downloaden

1. Klik in **Migration Goals** > **Servers** > **Azure Migrate: Server assessment**op **Ontdekken**.
2. In **Discover machines** > **Worden uw machines gevirtualiseerd?**, klik op **Ja, met VMWare vSphere hypervisor**.
3. Klik op **Downloaden** om het .OVA-sjabloonbestand te downloaden.

  ![Selecties voor het downloaden van een OVA-bestand](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>Beveiliging verifiëren

Controleer of het OVA-bestand veilig is voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Voer de volgende opdracht uit om de hash voor de OVA te genereren:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gebruiksvoorbeeld: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Voor de nieuwste toestelversie moet de gegenereerde hash overeenkomen met deze [instellingen.](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware#verify-security)



## <a name="create-the-appliance-vm"></a>De toestel-VM maken

Importeer het gedownloade bestand en maak een VM.

1. Klik in de vSphere-clientconsole op > **OVF-sjabloon implementeren.** **File**
![Menuopdracht voor het implementeren van een OVF-sjabloon](./media/tutorial-assess-vmware/deploy-ovf.png)

2. Geef in de wizard OVF-sjabloon implementeren > **Bron**de locatie van het OVA-bestand op.
3. Geef in **Naam** en **locatie**een vriendelijke naam op voor de VM. Selecteer het voorraadobject waarin de VM wordt gehost.
5. Geef in **Host/Cluster**de host of het cluster op waarop de VM wordt uitgevoerd.
6. Geef **in Opslag**de opslagbestemming voor de VM op.
7. Geef in **Schijfindeling** het schijftype en de schijfgrootte op.
8. Geef in **Netwerktoewijzing**het netwerk op waarmee de VM verbinding maakt. Het netwerk heeft een internetverbinding nodig om metagegevens naar Azure Migrate Server Assessment te verzenden.
9. Controleer en bevestig de instellingen en klik op **Voltooien**.


### <a name="verify-appliance-access-to-azure"></a>De toegang tot het toestel tot Azure verifiëren

Controleer of de toestel-VM verbinding kan maken met [Azure-URL's](migrate-appliance.md#url-access).


## <a name="configure-the-appliance"></a>Het toestel configureren

Stel het apparaat voor de eerste keer in. Als u het toestel implementeert met een script in plaats van een OVA-sjabloon, zijn de eerste twee stappen in de procedure niet van toepassing.

1. Klik in de vSphere Client-console met de rechtermuisknop op de VM > **Console openen**.
2. Geef de taal, tijdzone en wachtwoord op voor het toestel.
3. Open een browser op elke machine die verbinding kan maken met de VM en open de URL van de toestelweb-app: **https://*toestelnaam of IP-adres*: 44368**.

   U de app ook openen vanaf het bureaublad van het toestel door op de snelkoppeling van de app te klikken.
4. Ga als volgt te werk in de web-app > **Vereiste voorwaarden instellen:**
    - **Licentie:** Accepteer de licentievoorwaarden en lees de informatie van derden.
    - **Connectiviteit**: De app controleert of de VM toegang tot internet heeft. Als de VM een proxy gebruikt:
        - Klik **op Proxy-instellingen**en geef het proxyadres http://ProxyIPAddress http://ProxyFQDNen de luisterpoort op in het formulier of .
        - Geef referenties op als de proxy verificatie nodig heeft.
        - Alleen HTTP-proxy wordt ondersteund.
    - **Tijdsynchronisatie**: De tijd wordt geverifieerd. De tijd op het apparaat moet synchroon lopen met de internettijd om de detectie goed te laten werken.
    - **Updates installeren:** Azure Migrate controleert of de nieuwste toestelupdates zijn geïnstalleerd.
    - **VDDK installeren:** Azure Migrate controleert of de VMWare vSphere Virtual Disk Development Kit (VDDK) is geïnstalleerd.
        - Azure Migrates gebruikt de VDDK om machines te repliceren tijdens de migratie naar Azure.
        - Download VDDK 6.7 van VMware en haal de gedownloade zip-inhoud naar de opgegeven locatie op het toestel.

## <a name="register-the-appliance-with-azure-migrate"></a>Het toestel registreren met Azure Migreren

1. Klik **op Aanmelden**. Als deze niet wordt weergegeven, controleert u of u de pop-upblokkering in de browser hebt uitgeschakeld.
2. Meld u op het nieuwe tabblad aan met uw Azure-referenties.
    - Meld u aan met uw gebruikersnaam en wachtwoord.
    - Aanmelden met een pincode wordt niet ondersteund.
3. Ga na het succesvol aanmelden terug naar de web-app.
2. Selecteer het abonnement waarin het Azure Migrate-project is gemaakt. Selecteer vervolgens het project.
3. Geef een naam op voor het toestel. De naam moet alfanumeriek zijn met 14 tekens of minder.
4. Klik **op Registreren**.


## <a name="start-continuous-discovery-by-providing-vcenter-server-and-vm-credential"></a>Continue detectie starten door vCenter Server- en VM-referenties op te geven

Het toestel moet verbinding maken met vCenter Server om de configuratie- en prestatiegegevens van de VM's te ontdekken.

### <a name="specify-vcenter-server-details"></a>vCenter Server-gegevens opgeven
1. Geef **in VCenter Server-gegevens opgeven**de naam (FQDN) of het IP-adres van de vCenterserver op. U de standaardpoort verlaten of een aangepaste poort opgeven waarop uw vCenter Server luistert.
2. Geef in **Gebruikersnaam** en **wachtwoord**de alleen-lezen accountreferenties op die het toestel zal gebruiken om VM's op de vCenter-server te detecteren. U de detectie beperken door de toegang tot het vCenter-account te beperken. [Meer informatie](set-discovery-scope.md).
3. Klik **op Verbinding valideren** om ervoor te zorgen dat het toestel verbinding kan maken met vCenter Server.

### <a name="specify-vm-credentials"></a>VM-referenties opgeven
Voor het ontdekken van toepassingen, rollen en functies en het visualiseren van afhankelijkheden van de VM's, u een VM-referentie opgeven die toegang heeft tot de Vm's van VMware. U één referentie voor Windows VM's en één referentie voor Linux VM's toevoegen. [Meer informatie](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) over de benodigde toegangsrechten.

> [!NOTE]
> Deze invoer is optioneel en is nodig om toepassingsdetectie en agentless afhankelijkheidsvisualisatie mogelijk te maken.

1. Klik in **Toepassingen en afhankelijkheden van VM's**ontdekken op **Referenties toevoegen**.
2. Selecteer het **besturingssysteem**.
3. Geef een vriendelijke naam op voor de referentie.
4. Geef **in Gebruikersnaam** en **Wachtwoord**een account op met ten minste gasttoegang op de VM's.
5. Klik op**toevoegen**.

Nadat u de vCenter Server- en VM-referenties hebt opgegeven (optioneel), klikt u op **Opslaan en start u detectie** om de on-premises omgeving te detecteren.

Het duurt ongeveer 15 minuten voordat metadata van ontdekte VM's in het portaal verschijnen. Detectie van geïnstalleerde toepassingen, rollen en functies duurt enige tijd, de duur is afhankelijk van het aantal VM's dat wordt gedetecteerd. Voor 500 VM's duurt het ongeveer 1 uur voordat de toepassingsvoorraad wordt weergegeven in de Azure Migrate-portal.

## <a name="next-steps"></a>Volgende stappen

Bekijk de tutorials voor [VMware-beoordeling](tutorial-assess-vmware.md) en [agentless migratie](tutorial-migrate-vmware.md).
