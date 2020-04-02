---
title: VMware-VM's evalueren voor migratie naar Azure
description: Beschrijft hoe u on-premises VM's voor VMware-gegevens beoordelen op migratie naar Azure met Azure Migrate Server Assessment.
ms.topic: tutorial
ms.date: 03/23/2019
ms.openlocfilehash: 944b7c12a353a29a172576974261eece63ebf668
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548747"
---
# <a name="assess-vmware-vms-by-using-azure-migrate-server-assessment"></a>VMware VM's beoordelen met Azure Migrate Server Assessment

In dit artikel ziet u hoe u on-premises Virtuele VMware-machines (VM's) beoordelen met behulp van het hulpprogramma [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool)


Deze zelfstudie is de tweede in een reeks die laat zien hoe u VMware VM's beoordelen en migreren naar Azure. In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Stel een Azure Migrate-project in.
> * Stel een Azure Migrate-toestel in dat on-premises wordt uitgevoerd om VM's te beoordelen.
> * Start continue ontdekking van on-premises VM's. Het toestel stuurt configuratie- en prestatiegegevens voor gedetecteerde VM's naar Azure.
> * Groep ontdekt VM's en beoordeel de VM-groep.
> * Bekijk de beoordeling.

> [!NOTE]
> In zelfstudies ziet u het eenvoudigste implementatiepad voor een scenario, zodat u snel een proof-of-concept instellen. Tutorials maken gebruik van standaardopties waar mogelijk, en niet alle mogelijke instellingen en paden weer te geven. Bekijk voor gedetailleerde instructies de how-to-artikelen.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/pricing/free-trial/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

- [Voltooi de eerste tutorial](tutorial-prepare-vmware.md) in deze serie. Als u dat niet doet, werken de instructies in deze zelfstudie niet.
- Hier is wat je had moeten doen in de eerste tutorial:
    - [Bereid Azure voor](tutorial-prepare-vmware.md#prepare-azure) om met Azure Migrate te werken.
    - [VMware voorbereiden op beoordeling](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment) voor beoordeling. Dit omvat het controleren van VMware-instellingen, het instellen van een account dat Azure Migrate kan gebruiken om toegang te krijgen tot vCenter Server.
    - [Controleer](tutorial-prepare-vmware.md#verify-appliance-settings-for-assessment) wat u nodig hebt om het Azure Migrate-toestel te implementeren voor VMware-beoordeling.

## <a name="set-up-an-azure-migrate-project"></a>Een Azure Migrate-project instellen

Stel als volgt een nieuw Azure Migrate-project in:

1. Zoek in de Azure-portal in **Alle services** naar **Azure Migrate**.
1. Onder **Services** selecteert u **Azure Migrate**.
1. Selecteer in **Overzicht**onder **Servers ontdekken, beoordelen en migreren**de optie Servers beoordelen en **migreren**.

   ![Knop om servers te beoordelen en te migreren](./media/tutorial-assess-vmware/assess-migrate.png)

1. Selecteer **Gereedschappen toevoegen**in Aan **de slag**.
1. Selecteer in **Project migreren** uw Azure-abonnement en maak een resourcegroep als u er nog geen hebt.     
1. Geef in **Projectdetails**de projectnaam en de geografie op waarin u het project wilt maken. Azië, Europa, het Verenigd Koninkrijk en de Verenigde Staten worden ondersteund.

   Het geografische gebied voor het project wordt alleen gebruikt om de metagegevens op te slaan die worden verzameld van on-premises virtuele machines. Wanneer u een migratie uitvoert, kunt u elke gewenste doelregio selecteren.

   ![Vakken voor projectnaam en regio](./media/tutorial-assess-vmware/migrate-project.png)

1. Selecteer **Volgende**.
1. Selecteer Azure **Migrate: Server Assessment** > **Next**in **Het beoordelingshulpprogramma selecteren.**

   ![Selectie voor het hulpprogramma Serverbeoordeling](./media/tutorial-assess-vmware/assessment-tool.png)

1. In **Migratieprogramma selecteren** selecteert u **Het toevoegen van een migratieprogramma voorlopig overslaan** > **Volgende**.
1. Bekijk in **Controle + hulpprogramma's de**instellingen en selecteer Gereedschappen **toevoegen**.
1. Wacht een paar minuten tot het Azure Migrate-project is geïmplementeerd. U wordt naar de projectpagina geleid. Als u het project niet ziet, kunt u het openen vanuit **Servers** in het Azure Migrate-dashboard.

## <a name="set-up-the-azure-migrate-appliance"></a>Het Azure Migrate-toestel instellen

Azure Migrate:Server Assessment maakt gebruik van een lichtgewicht Azure Migrate-toestel. Het toestel voert VM-detectie uit en verzendt VM-metagegevens en prestatiegegevens naar Azure Migrate.
- Het toestel kan worden ingesteld op een VMware VM met behulp van een gedownloade OVA-sjabloon. U het apparaat ook instellen op een VM of fysieke machine met een PowerShell-installatiescript.
- In deze zelfstudie wordt de OVA-sjabloon gebruikt. Bekijk [dit artikel](deploy-appliance-script.md) als u het toestel wilt instellen met een script.

Nadat u het toestel hebt gemaakt, controleert u of het verbinding kan maken met Azure Migrate:Server Assessment, configureer het voor de eerste keer en registreer u het met het Azure Migrate-project.



### <a name="download-the-ova-template"></a>De OVA-sjabloon downloaden

1. Selecteer in **Migratiedoelenservers** > **Servers** > **Azure Migreren: Serverbeoordeling**, selecteer **Ontdekken**.
1. In **Discover machines** > Worden uw machines **Yes, with VMWare vSphere hypervisor****gevirtualiseerd?**
1. Selecteer **Downloaden** om het OVA-sjabloonbestand te downloaden.

   ![Selecties voor het downloaden van een OVA-bestand](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>Beveiliging verifiëren

Controleer of het OVA-bestand veilig is voordat u het implementeert:

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
1. Voer de volgende opdracht uit om de hash voor het OVA-bestand te genereren:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Gebruiksvoorbeeld: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

Voor versie 2.19.07.30 moet de gegenereerde hash overeenkomen met deze waarden:

**Algoritme** | **Hash-waarde**
--- | ---
MD5 | c06ac2a2c0f870d3b274a0b7a73b78b1
SHA256 | 4ce4faa3a78189a09a26bfa5b817c7afcf5b5555eb46999c2fad9d2ebc808540c

### <a name="create-the-appliance-vm"></a>De toestel-VM maken

Importeer het gedownloade bestand en maak een VM:

1. Selecteer > **OVF-sjabloon**bestand **File**implementeren in de vSphere-clientconsole .

   ![Menuopdracht voor het implementeren van een OVF-sjabloon](./media/tutorial-assess-vmware/deploy-ovf.png)

1. Geef in de wizard OVF-sjabloon implementeren > **Bron**de locatie van het OVA-bestand op.
1. Geef in **Naam** en **locatie**een vriendelijke naam op voor de VM. Selecteer het voorraadobject waarin de VM wordt gehost.
1. Geef in **Host/Cluster**de host of het cluster op waarop de VM wordt uitgevoerd.
1. Geef **in Opslag**de opslagbestemming voor de VM op.
1. Geef in **Schijfindeling** het schijftype en de schijfgrootte op.
1. Geef in **Netwerktoewijzing**het netwerk op waarmee de VM verbinding maakt. Het netwerk heeft een internetverbinding nodig om metagegevens naar Azure Migrate Server Assessment te verzenden.
1. Controleer en bevestig de instellingen en selecteer **Voltooien.**

### <a name="verify-appliance-access-to-azure"></a>De toegang tot het toestel tot Azure verifiëren

Controleer of de toestel-VM verbinding kan maken met [Azure-URL's](migrate-appliance.md#url-access).

### <a name="configure-the-appliance"></a>Het toestel configureren

Stel het apparaat voor de eerste keer in.

> [!NOTE]
> Als u het toestel instelt met een [PowerShell-script](deploy-appliance-script.md) in plaats van de gedownloade OVA, zijn de eerste twee stappen in deze procedure niet relevant.

1. Klik in de vSphere-clientconsole met de rechtermuisknop op de vm en selecteer **Console openen**.
1. Geef de taal, tijdzone en wachtwoord op voor het toestel.
1. Open een browser op elke machine die verbinding kan maken met de VM en open de URL van de toestelweb-app: **https://*toestelnaam of IP-adres*: 44368**.

   U de app ook openen vanaf het bureaublad van het toestel door de snelkoppeling van de app te selecteren.
1. Ga als volgt te werk in de web-app > **Vereiste voorwaarden instellen:**
   - **Licentie:** Accepteer de licentievoorwaarden en lees de informatie van derden.
   - **Connectiviteit**: De app controleert of de VM toegang tot internet heeft. Als de VM een proxy gebruikt:
     - Selecteer **Proxy-instellingen**en geef het proxyadres http://ProxyIPAddress en http://ProxyFQDNde luisterpoort op in het formulier of .
     - Geef referenties op als de proxy verificatie nodig heeft.
     - Houd er rekening mee dat alleen HTTP-proxy wordt ondersteund.
   - **Tijdsynchronisatie**: de tijd op het toestel moet synchroon lopen met de internettijd om de detectie goed te laten werken.
   - **Updates installeren**: Het toestel zorgt ervoor dat de nieuwste updates worden geïnstalleerd.
   - **VDDK installeren**: Het toestel controleert of VMWare vSphere Virtual Disk Development Kit (VDDK) is geïnstalleerd. Als deze niet is geïnstalleerd, downloadt u VDDK 6.7 van VMware en haalt u de gedownloade zip-inhoud naar de opgegeven locatie in het toestel.

     Azure Migrate Server Migration gebruikt de VDDK om machines te repliceren tijdens de migratie naar Azure.       

### <a name="register-the-appliance-with-azure-migrate"></a>Het toestel registreren met Azure Migreren

1. Selecteer **Aanmelden**. Als deze niet wordt weergegeven, controleert u of u de pop-upblokkering in de browser hebt uitgeschakeld.
1. Meld u op het nieuwe tabblad aan met uw Azure-gebruikersnaam en -wachtwoord.
   
   Aanmelden met een pincode wordt niet ondersteund.
1. Nadat u zich hebt aangemeld, gaat u terug naar de web-app.
1. Selecteer het abonnement waarin het Azure Migrate-project is gemaakt en selecteer vervolgens het project.
1. Geef een naam op voor het toestel. De naam moet alfanumeriek zijn met 14 tekens of minder.
1. Selecteer **Registreren**.


## <a name="start-continuous-discovery"></a>Continue detectie starten

Het toestel moet verbinding maken met vCenter Server om de configuratie- en prestatiegegevens van de VM's te ontdekken.

### <a name="specify-vcenter-server-details"></a>vCenter Server-gegevens opgeven
1. Geef **in VCenter Server-gegevens opgeven**de naam (FQDN) of het IP-adres van het vCenter-server-exemplaar op. U de standaardpoort verlaten of een aangepaste poort opgeven waarop vCenter Server luistert.
2. Geef in **Gebruikersnaam** en **wachtwoord**de vCenter Server-accountreferenties op die het toestel zal gebruiken om VM's te detecteren in het vCenter Server-exemplaar. 

    - U had in de [vorige zelfstudie](tutorial-prepare-vmware.md#set-up-an-account-for-assessment)een account met de vereiste machtigingen moeten instellen.
    - Als u detectie wilt uitvoeren naar specifieke VMware-objecten (vCenter Server-datacenters, clusters, een map met clusters, hosts, een map met hosts of afzonderlijke VM's.), controleert u de instructies in [dit artikel](set-discovery-scope.md) om het account dat wordt gebruikt door Azure Migrate te beperken.

3. Selecteer **Verbinding valideren** om ervoor te zorgen dat het toestel verbinding kan maken met vCenter Server.
4. Klik in **Toepassingen en afhankelijkheden van VM's**ontdekken, klik optioneel op **Referenties toevoegen**en geef het besturingssysteem op waarvoor de referenties relevant zijn en de gebruikersnaam en het wachtwoord van de referenties. Klik vervolgens op **Toevoegen**..

    - U voegt hier optioneel referenties toe als u een account hebt gemaakt dat u wilt gebruiken voor de [functie voor het ontdekken van](how-to-discover-applications.md)toepassingen of de functie voor [afhankelijkheidsanalyse zonder agent](how-to-create-group-machine-dependencies-agentless.md).
    - Als u deze functies niet gebruikt, u deze instelling overslaan.
    - Controleer de referenties die nodig zijn voor [app-detectie](migrate-support-matrix-vmware.md#application-discovery)of voor [agentloze analyse](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements).

5. **Opslaan en beginnen met detectie**, om vm-detectie te starten.

Discovery werkt als volgt:
- Het duurt ongeveer 15 minuten voordat gedetecteerde VM-metagegevens in de portal worden weergegeven.
- Het ontdekken van geïnstalleerde toepassingen, rollen en functies neemt enige tijd in beslag. De duur is afhankelijk van het aantal VM's dat wordt gedetecteerd. Voor 500 VM's duurt het ongeveer een uur voordat de toepassingsvoorraad wordt weergegeven in de Azure Migrate-portal.

### <a name="verify-vms-in-the-portal"></a>VM's verifiëren in de portal

Na detectie u controleren of de VM's worden weergegeven in de Azure-portal:

1. Open het Azure Migrate-dashboard.
1. Selecteer in **Azure Migrate - Servers** > **Azure Migrate: Server Assessment**het pictogram met het aantal **gedetecteerde servers.**

## <a name="set-up-an-assessment"></a>Een beoordeling instellen

U twee typen beoordelingen maken met Azure Migrate Server Assessment:

**Beoordeling** | **Details** | **Gegevens**
--- | --- | ---
**Op prestaties gebaseerd** | Beoordelingen op basis van verzamelde prestatiegegevens | **Aanbevolen VM-grootte:** gebaseerd op cpu- en geheugengebruiksgegevens.<br/><br/> **Aanbevolen schijftype (standaard of premium beheerde schijf):** op basis van de IOPS en doorvoer van de on-premises schijven.
**Als on-premises** | Beoordelingen op basis van on-premises dimensionering | **Aanbevolen VM-grootte:** op basis van de on-premises VM-grootte.<br/><br> **Aanbevolen schijftype:** op basis van de instelling voor het opslagtype die u voor de beoordeling selecteert.

## <a name="run-an-assessment"></a>Een evaluatie uitvoeren

Voer een beoordeling als volgt uit:

1. Bekijk de [aanbevolen procedures](best-practices-assessment.md) voor het maken van beoordelingen.
1. Selecteer op het tabblad **Servers** in de tegel **Azure Migreren: Serverbeoordeling** de optie **Beoordelen**.

   ![Locatie van de knop Beoordelen](./media/tutorial-assess-vmware/assess.png)

1. Geef **in Servers beoordelen**een naam op voor de beoordeling.
1. Selecteer **Alles weergeven**en controleer vervolgens de beoordelingseigenschappen.

   ![Beoordelingseigenschappen](./media/tutorial-assess-vmware/view-all.png)

1. Selecteer in **Een groep selecteren of maken**de optie Nieuw **maken**en geef een groepsnaam op. Een groep verzamelt een of meer VM's voor beoordeling.
1. Selecteer vm's die u aan de groep wilt toevoegen in **Machines toevoegen.**
1. Selecteer **Beoordeling maken** om de groep te maken en de beoordeling uit te voeren.

   ![Servers beoordelen](./media/tutorial-assess-vmware/assessment-create.png)

1. Nadat de beoordeling is gemaakt, bekijkt u deze in **Servers** > **Azure Migrate: Server Assessment** > **Assessments**.
1. Selecteer **Beoordeling exporteren** om het als Excel-bestand te downloaden.

## <a name="review-an-assessment"></a>Een beoordeling beoordelen

Een beoordeling beschrijft:

- **Azure-gereedheid**: of VM's geschikt zijn voor migratie naar Azure.
- **Schatting van de maandelijkse kosten:** de geschatte maandelijkse reken- en opslagkosten voor het uitvoeren van de VM's in Azure.
- **Schatting van de maandelijkse opslagkosten**: Geschatte kosten voor schijfopslag na migratie.

Ga als het gaat om een beoordeling:

1. Selecteer In **Servers voor migratiedoelen** > **Servers**de optie **Beoordelingen** in **Azure Migreren: Serverbeoordeling**.
1. Selecteer in **Assessments**een beoordeling om deze te openen.

   ![Evaluatiesamenvatting](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure-gereedheid controleren

1. Controleer in **Azure-gereedheid**of VM's klaar zijn voor migratie naar Azure.
1. Controleer de VM-status:
    - **Klaar voor Azure:** wordt gebruikt wanneer Azure Migrate een VM-grootte en kostenramingen voor VM's in de beoordeling aanbeveelt.
    - **Klaar met voorwaarden**: Toont problemen en voorgestelde herstel.
    - **Niet klaar voor Azure:** toont problemen en voorgestelde herstel.
    - **Gereedheid onbekend**: wordt gebruikt wanneer Azure Migrate de gereedheid niet kan beoordelen vanwege problemen met de beschikbaarheid van gegevens.

1. Selecteer een **Azure-gereedheidsstatus.** U de details van de gereedheid van de VM weergeven. U ook inzoomen om VM-details te bekijken, waaronder rekenkracht, opslag en netwerkinstellingen.

### <a name="review-cost-details"></a>Kostendetails bekijken

Het beoordelingsoverzicht toont de geschatte reken- en opslagkosten van het uitvoeren van VM's in Azure. De kosten worden geaggregeerd voor alle VM's in de beoordeelde groep. U inzoomen om kostendetails voor specifieke VM's te bekijken.

> [!NOTE]
> Kostenramingen zijn gebaseerd op de grootteaanbevelingen voor een machine, de schijven en de eigenschappen ervan. Schattingen zijn voor het uitvoeren van de on-premises VM's als IaaS VM's. Azure Migrate Server Assessment houdt geen rekening met paas- of SaaS-kosten.

De geaggregeerde opslagkosten voor de beoordeelde groep worden verdeeld over verschillende soorten opslagschijven. 

### <a name="review-confidence-rating"></a>Betrouwbaarheidsclassificatie controleren

Azure Migrate Server Assessment kent een betrouwbaarheidsclassificatie toe aan een prestatiegebaseerde beoordeling, van 1 ster (laagste) tot 5 sterren (hoogste).

![Betrouwbaarheidsclassificatie](./media/tutorial-assess-vmware/confidence-rating.png)

Met de betrouwbaarheidsclassificatie u de betrouwbaarheid van de aanbevelingen voor de grootte van de beoordeling schatten. De beoordeling is gebaseerd op de beschikbaarheid van gegevenspunten die nodig zijn om de beoordeling te berekenen:

**Beschikbaarheid van gegevenspunten** | **Betrouwbaarheidsclassificatie**
--- | ---
0%-20% | 1 ster
21%-40% | 2 sterren
41%-60% | 3 sterren
61%-80% | 4 sterren
81%-100% | 5 sterren

[Meer informatie over aanbevolen procedures](best-practices-assessment.md#best-practices-for-confidence-ratings) voor vertrouwensbeoordelingen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie stelt u een Azure Migrate-toestel in. U hebt ook een beoordeling gemaakt en beoordeeld.

Ga door naar de derde zelfstudie in de reeks voor meer informatie over het migreren van VMware VM's naar Azure met Azure Migrate Server Migration:

> [!div class="nextstepaction"]
> [Virtuele VMware-machines migreren](./tutorial-migrate-vmware.md)
