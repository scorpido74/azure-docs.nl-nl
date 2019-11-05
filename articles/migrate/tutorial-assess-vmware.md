---
title: Virtuele VMware-machines evalueren voor migratie naar Azure met Azure Migrate
description: Hierin wordt beschreven hoe u on-premises virtuele VMware-machines kunt beoordelen voor migratie naar Azure met behulp van Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/11/2019
ms.author: hamusa
ms.openlocfilehash: 46bf756a729441bd3bc4b2b00aaa2c79fa06c0b8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521235"
---
# <a name="assess-vmware-vms-with-azure-migrate-server-assessment"></a>Virtuele VMware-machines beoordelen met Azure Migrate: Server evaluatie

In dit artikel leest u hoe u on-premises VMware-Vm's kunt beoordelen met behulp van het Azure Migrate: Server assessment tool.

[Azure migrate](migrate-services-overview.md) biedt een hub aan hulpprogram ma's waarmee u apps, infra structuur en werk belastingen op Microsoft Azure kunt detecteren, evalueren en migreren. De hub bevat Azure Migrate-hulpprogram ma's en ISV-aanbiedingen (Independent Software Vendor) van derden.



Deze zelf studie is de tweede in een serie die laat zien hoe u virtuele VMware-machines kunt beoordelen en migreren naar Azure. In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Stel een Azure Migrate project in.
> * Stel een Azure Migrate apparaat in dat on-premises wordt uitgevoerd om Vm's te beoordelen.
> * Start doorlopende detectie van on-premises Vm's. Het apparaat verzendt configuratie-en prestatie gegevens voor gedetecteerde Vm's naar Azure.
> * Gedetecteerde Vm's groeperen en evalueren van de VM-groep.
> * Bekijk de evaluatie.



> [!NOTE]
> In zelf studies ziet u het eenvoudigste installatiepad voor een scenario, zodat u snel een haalbaarheids test kunt instellen. Zelf studies gebruiken waar mogelijk standaard opties en worden niet alle mogelijke instellingen en paden weer gegeven. Raadpleeg de artikelen met procedures voor gedetailleerde instructies.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.


## <a name="prerequisites"></a>Vereisten

- [Voltooi](tutorial-prepare-vmware.md) de eerste zelf studie in deze serie. Als dat niet het geval is, werken de instructies in deze zelf studie niet.
- In de eerste zelf studie hebt u het volgende gedaan:
    - [Azure-machtigingen instellen](tutorial-prepare-vmware.md#prepare-azure) voor Azure Migrate.
    - [VMware voorbereiden](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment) voor evaluatie. VMware-instellingen moeten worden gecontroleerd en u moet beschikken over machtigingen voor het maken van een VMware-VM met een eicellen-sjabloon. U moet ook een account hebben ingesteld voor de detectie van VM'S. De vereiste poorten moeten beschikbaar zijn en u moet rekening houden met de Url's die nodig zijn voor toegang tot Azure.


## <a name="set-up-an-azure-migrate-project"></a>Een Azure Migrate project instellen

Ga als volgt te werk om een nieuw Azure Migrate-project op te zetten:

1. Zoek in de Azure-portal in **Alle services** naar **Azure Migrate**.
2. Onder **Services** selecteert u **Azure Migrate**.
3. In **Overzicht**, onder **Servers ontdekken, evalueren en migreren**, klikt u op **Servers evalueren en migreren**.

    ![Servers detecteren en beoordelen](./media/tutorial-assess-vmware/assess-migrate.png)

4. Klik in **Aan de slag** op **Hulpprogramma's toevoegen**.
5. Selecteer in **Project migreren** uw Azure-abonnement en maak een resourcegroep als u er nog geen hebt.     
6. Geef in **Project Details**de project naam en de geografie op waarin u het project wilt maken. Azië, Europa, UK en de Verenigde Staten worden ondersteund.

    - Het geografische gebied voor het project wordt alleen gebruikt om de metagegevens op te slaan die worden verzameld van on-premises virtuele machines.
    - Wanneer u een migratie uitvoert, kunt u elke gewenste doelregio selecteren.

    ![Een Azure Migrate-project maken](./media/tutorial-assess-vmware/migrate-project.png)


7. Klik op **Volgende**.
8. Selecteer in **hulp programma voor beoordeling selecteren**de optie **Azure migrate: Server evaluatie** > **volgende**.

    ![Een Azure Migrate-project maken](./media/tutorial-assess-vmware/assessment-tool.png)

9. In **Migratieprogramma selecteren** selecteert u **Het toevoegen van een migratieprogramma voorlopig overslaan** > **Volgende**.
10. Controleer in **Evalueren en hulpprogramma's toevoegen** de instellingen en klik op **Hulpmiddelen toevoegen**.
11. Wacht een paar minuten tot het Azure Migrate-project is geïmplementeerd. U wordt naar de projectpagina geleid. Als u het project niet ziet, kunt u het openen vanuit **Servers** in het Azure Migrate-dashboard.


## <a name="set-up-the-appliance-vm"></a>De toestel-VM instellen

Azure Migrate: Server bepaling voert een licht gewicht VMware-VM-apparaat uit.

- Dit apparaat voert VM-detectie uit en verzendt meta gegevens en prestatie gegevens van de virtuele machine naar Azure Migrate server beoordeling.
- Als u het apparaat wilt instellen, doet u het volgende:
    - Down load een bestand met een eicellen-sjabloon en importeer het naar vCenter Server.
    - Maak het apparaat en controleer of het verbinding kan maken met Azure Migrate server beoordeling.
    - Configureer het apparaat voor de eerste keer en registreer het bij het Azure Migrate-project.
- U kunt meerdere toestellen instellen voor één Azure Migrate project. Op alle apparaten wordt detectie van Maxi maal 35.000 Vm's ondersteund. Er kunnen Maxi maal 10.000 servers worden gedetecteerd per apparaat.

### <a name="download-the-ova-template"></a>De eicellen-sjabloon downloaden

1. In **migratie doelen** > **servers** > **Azure migrate: Server evaluatie**, klikt u op **ontdekken**.
2. In **Machines ontdekken** > **Zijn de machines gevirtualiseerd?** klikt u op **Ja, met VMWare vSphere-hypervisor**.
3. Klik op **Downloaden** om het .OVA-sjabloonbestand te downloaden.

    ![.OVA-bestand downloaden](./media/tutorial-assess-vmware/download-ova.png)


### <a name="verify-security"></a>Beveiliging controleren

Controleer of het bestand van de eicellen veilig is voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Gebruik de volgende opdracht om de hash voor het OVA-bestand te genereren:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gebruiksvoorbeeld: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Voor versie 2.19.07.30 moet de gegenereerde hash overeenkomen met deze waarden.

  **Algoritme** | **Hash-waarde**
  --- | ---
  MD5 | 27230f3b012187860281b912ee661709
  SHA256 | c0a5b5998b7f38ac6e57ea9a808ecc4295795e18f9ca99c367585068883f06e7


### <a name="create-the-appliance-vm"></a>De apparaat-VM maken

Importeer het gedownloade bestand en maak een virtuele machine.

1. Klik in de Client vSphere-console op **Bestand** > **OVF-sjabloon implementeren**.

    ![OVF implementeren](./media/tutorial-assess-vmware/deploy-ovf.png)

2. Geef in de wizard OVF-sjabloon implementeren > **bron**de locatie op van het bestand van de eicellen.
3. Geef bij **naam** en **locatie**een beschrijvende naam op voor de virtuele machine. Selecteer het inventaris object waarin de VM wordt gehost.
5. Geef in **host/cluster**de host of het cluster op waarop de virtuele machine wordt uitgevoerd.
6. Geef in **opslag**de opslag bestemming voor de virtuele machine op.
7. Geef in **Schijfindeling** het schijftype en de schijfgrootte op.
8. Geef bij **netwerk toewijzing**het netwerk op waarmee de virtuele machine verbinding maakt. Het netwerk heeft Internet connectiviteit nodig om meta gegevens te verzenden naar Azure Migrate server-evaluatie.
9. Controleer en bevestig de instellingen en klik op **Voltooien**.


### <a name="verify-appliance-access-to-azure"></a>Toestel toegang tot Azure controleren

Zorg ervoor dat de virtuele machine van het apparaat verbinding kan maken met [Azure-url's](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


### <a name="configure-the-appliance"></a>Het apparaat configureren

Stel het apparaat in met behulp van de volgende stappen.

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
    - **Tijd synchronisatie**: de tijd op het apparaat moet zijn gesynchroniseerd met internet tijd zodat de detectie goed werkt.
    - **Updates installeren**: het apparaat zorgt ervoor dat de meest recente updates worden geïnstalleerd.
    - **VdDK installeren**: het apparaat controleert of VMware VSphere Virtual Disk Development Kit (vddk) is geïnstalleerd.
        - Azure Migrate: Server migratie gebruikt de VDDK om computers te repliceren tijdens de migratie naar Azure.
        - Down load VDDK 6,7 van VMware en extraheer de gedownloade zip-inhoud naar de opgegeven locatie op het apparaat.

### <a name="register-the-appliance-with-azure-migrate"></a>Het apparaat registreren bij Azure Migrate

1. Klik op **Aanmelden**. Als deze niet wordt weer gegeven, controleert u of de pop-upblokkering in de browser is uitgeschakeld.
2. Meld u aan met uw Azure-referenties op het tabblad Nieuw.
    - Meld u aan met uw gebruikers naam en wacht woord.
    - Aanmelden met een pincode wordt niet ondersteund.
3. Nadat u zich hebt aangemeld, gaat u terug naar de web-app.
2. Selecteer het abonnement waarin het Azure Migrate project is gemaakt en selecteer vervolgens het project.
3. Geef een naam op voor het apparaat. De naam moet alfanumeriek zijn met 14 tekens of minder.
4. Klik op **registreren**.

## <a name="start-continuous-discovery"></a>Continue detectie starten

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

### <a name="scoping-discovery"></a>Detectie van bereik

Detectie kan worden beperkt door de toegang te beperken tot het vCenter-account dat wordt gebruikt voor detectie. U kunt het bereik instellen op vCenter Server Data Centers, clusters, map met clusters, hosts, de map hosts of afzonderlijke Vm's.

Als u het bereik wilt instellen, moet u de volgende stappen uitvoeren:
1.  Maak een vCenter-gebruikers account.
2.  Definieer een nieuwe rol met de vereiste bevoegdheden. (<em>vereist voor server migratie zonder agent</em>)
3.  Machtigingen toewijzen aan het gebruikers account op vCenter-objecten.

**Een vCenter-gebruikers account maken**
1.  Meld u aan bij de vSphere-webclient als de vCenter Server beheerder.
2.  Klik op **beheer** > **SSO-gebruikers en-groepen** > tabblad **gebruikers** .
3.  Klik op het pictogram **nieuwe gebruiker** .
4.  Vul de vereiste gegevens in om een nieuwe gebruiker te maken en klik op **OK**.

**Een nieuwe rol definiëren met vereiste bevoegdheden** (<em>vereist voor server migratie zonder agent</em>)
1.  Meld u aan bij de vSphere-webclient als vCenter Server beheerder.
2.  Blader naar **beheer** > **Role Manager**.
3.  Selecteer uw vCenter Server in de vervolg keuzelijst.
4.  Klik op actie voor het maken van een **rol** .
5.  Typ een naam voor de nieuwe rol. (zoals <em>Azure_Migrate</em>).
6.  Wijs deze [machtigingen](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-vcenter-server-permissions) toe aan de nieuw gedefinieerde rol.
7.  Klik op **OK**.

**Machtigingen toewijzen voor vCenter-objecten**

Er zijn twee methoden voor het toewijzen van machtigingen aan inventaris objecten in vCenter aan het vCenter-gebruikers account waaraan een rol is toegewezen.
- Voor Server evaluatie moet de rol **alleen-lezen** worden toegepast op het vCenter-gebruikers account voor alle bovenliggende objecten waar de vm's worden gehost die moeten worden gedetecteerd. Alle bovenliggende objecten-host, map van hosts, cluster, map met clusters in de hiërarchie tot aan het Data Center moeten worden opgenomen. Deze machtigingen moeten worden door gegeven aan onderliggende objecten in de hiërarchie.

    Net als bij server migratie moet een door de gebruiker gedefinieerde rol (met de naam <em>Azure _Migrate</em>) worden toegepast op het vCenter-gebruikers account voor alle bovenliggende objecten waar de virtuele machines worden [gehost die worden](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-vcenter-server-permissions) gemigreerd.

![Machtigingen toewijzen](./media/tutorial-assess-vmware/assign-perms.png)

- De alternatieve methode is het toewijzen van het gebruikers account en de rol op het datacenter niveau en het door geven van deze aan de onderliggende objecten. Geef vervolgens voor elk object (zoals Vm's) dat u niet wilt detecteren of migreren het account een **Access** -rol. Deze configuratie is omslachtig. Het biedt onbedoelde toegangs controles, omdat voor elk nieuw onderliggend object ook automatisch toegang wordt verleend die wordt overgenomen van de bovenliggende. Daarom wordt u aangeraden de eerste benadering te gebruiken.

> [!NOTE]
> Momenteel kan server evaluatie geen Vm's detecteren als het vCenter-account toegang heeft gekregen op het niveau van de vCenter-VM-map. Als u uw detectie op basis van VM-mappen wilt beperken, kunt u dit doen door ervoor te zorgen dat het vCenter-account alleen-lezen toegang heeft dat is toegewezen op een VM-niveau.  Hieronder vindt u instructies over hoe u dit kunt doen:
>
> 1. Wijs alleen-lezen machtigingen toe op alle virtuele machines in de VM-mappen die u wilt bereiken voor de detectie.
> 2. Ken alleen-lezen toegang toe aan alle bovenliggende objecten waarbij de Vm's worden gehost. Alle bovenliggende objecten-host, map van hosts, cluster, map met clusters-in de hiërarchie tot aan het Data Center moeten worden opgenomen. U hoeft de machtigingen niet door te geven aan alle onderliggende objecten.
> 3. Gebruik de referenties voor detectie Data Center als *verzamelings bereik*selecteren. De RBAC-instelling zorgt ervoor dat de bijbehorende vCenter-gebruiker alleen toegang heeft tot Tenant-specifieke Vm's.
>
> Houd er rekening mee dat de map van hosts en clusters wordt ondersteund.

### <a name="verify-vms-in-the-portal"></a>VM's verifiëren in de portal

Na de detectie kunt u controleren of de virtuele machines worden weer gegeven in de Azure Portal.

1. Open het Azure Migrate dash board.
2. Klik in **Azure migrate-Servers** > pagina **Azure migrate: Server beoordeling** op het pictogram met het aantal voor **gedetecteerde servers**.

## <a name="set-up-an-assessment"></a>Een evaluatie instellen

Er zijn twee soorten evaluaties die u kunt maken met behulp van Azure Migrate: Server Assessment.

**Evaluatie** | **Details** | **Gegevens**
--- | --- | ---
**Op basis van prestaties** | Evaluaties op basis van verzamelde prestatie gegevens | **Aanbevolen VM-grootte**: op basis van gegevens van CPU en geheugen gebruik.<br/><br/> **Aanbevolen schijf type (Standard of Premium Managed disk)** : op basis van de IOPS en door Voer van de on-premises schijven.
**Als on-premises** | Evaluaties op basis van on-premises grootte. | **Aanbevolen VM-grootte**: op basis van de on-PREMISes VM-grootte<br/><br> **Aanbevolen schijf type**: op basis van de instelling voor het opslag type die u voor de evaluatie selecteert.


### <a name="run-an-assessment"></a>Een evaluatie uitvoeren

Voer een evaluatie als volgt uit:

1. Bekijk de [Aanbevolen procedures](best-practices-assessment.md) voor het maken van evaluaties.
2. Klik op het tabblad **servers** in **Azure migrate: tegel server bepaling** op **evalueren**.

    ![Evalueren](./media/tutorial-assess-vmware/assess.png)

2. Geef in **servers beoordelen**een naam op voor de evaluatie.
3. Klik op **Alles weergeven** om de evaluatie-eigenschappen te controleren.

    ![Eigenschappen van beoordeling](./media/tutorial-assess-vmware/view-all.png)

3. In **een groep selecteren of maken**, selecteert u **nieuwe maken**en geeft u een groeps naam op. Een groep verzamelt een of meer Vm's samen voor evaluatie.
4. Selecteer op **computers toevoegen aan de groep**de optie vm's die u aan de groep wilt toevoegen.
5. Klik op **evaluatie maken** om de groep te maken en de evaluatie uit te voeren.

    ![Een evaluatie maken](./media/tutorial-assess-vmware/assessment-create.png)

6. Nadat de evaluatie is gemaakt, bekijkt u deze in **Servers** > **Azure migrate: Server evaluatie** > - **evaluaties**.
7. Klik op **Evaluatie exporteren** om deze te downloaden als een Excel-bestand.



## <a name="review-an-assessment"></a>Een evaluatie controleren

Een evaluatie beschrijft:

- **Azure-gereedheid**: of vm's geschikt zijn voor migratie naar Azure.
- **Schatting van maandelijkse kosten**: de geschatte maandelijkse reken-en opslag kosten voor het uitvoeren van de virtuele machines in Azure.
- **Schatting maandelijkse opslag kosten**: geschatte kosten voor schijf opslag na migratie.

### <a name="view-an-assessment"></a>Een evaluatie weer geven

1. In **migratie doelen** >  **servers**, klikt u op **evaluaties** in **Azure migrate: Server evaluatie**.
2. Klik in **beoordelingen**op een evaluatie om deze te openen.

    ![Evaluatie samenvatting](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure-gereedheid controleren

1. Controleer in **Azure Readiness**of de vm's gereed zijn voor migratie naar Azure.
2. Controleer de status van de virtuele machine:
    - **Gereed voor Azure**: Azure migrate RAADT een VM-grootte en schattingen voor de kosten aan voor vm's in de evaluatie.
    - **Klaar met voor waarden**: toont problemen en voorgestelde herbemiddeling.
    - **Niet gereed voor Azure**: toont problemen en voorgestelde herbemiddeling.
    - **Gereedheid onbekend**: wordt gebruikt wanneer Azure migrate de gereedheid niet kan beoordelen door problemen met de beschik baarheid van gegevens.

2. Klik op de status van een **Azure-gereedheid** . U kunt details van de VM-gereedheid bekijken en inzoomen op de details van de virtuele machine, met inbegrip van compute-, opslag-en netwerk instellingen.



### <a name="review-cost-details"></a>Details van kosten bekijken

In deze weer gave ziet u de geschatte berekenings-en opslag kosten voor het uitvoeren van Vm's in Azure.

1. Bekijk de maandelijkse reken-en opslag kosten. De kosten worden geaggregeerd voor alle virtuele machines in de geraamde groep.

    - Schattingen van kosten zijn gebaseerd op de grootte aanbevelingen voor een machine en de schijven en eigenschappen.
    - De geschatte maandelijkse kosten voor Compute en opslag worden weer gegeven.
    - De kosten raming is voor het uitvoeren van de on-premises Vm's als IaaS Vm's. Azure Migrate server-evaluatie houdt geen rekening met PaaS-of SaaS-kosten.

2. U kunt de maandelijkse schattingen voor de opslag kosten bekijken. In deze weer gave worden geaggregeerde opslag kosten voor de geschatte groep weer gegeven, gesplitst over verschillende typen opslag schijven.
3. U kunt inzoomen om de details van specifieke Vm's te bekijken.


### <a name="review-confidence-rating"></a>Betrouwbaarheidsclassificatie controleren

Wanneer u evaluaties op basis van prestaties uitvoert, wordt een betrouwbaarheids classificatie aan de evaluatie toegewezen.

![Betrouwbaarheidsclassificatie](./media/tutorial-assess-vmware/confidence-rating.png)

- Er wordt een classificatie van 1 ster (laagste) tot 5 sterren (hoogst) toegekend.
- De betrouwbaarheids classificatie helpt u bij het schatten van de betrouw baarheid van de grootte aanbevelingen van de evaluatie.
- De betrouwbaarheids classificatie is gebaseerd op de beschik baarheid van gegevens punten die nodig zijn om de evaluatie te berekenen.

De betrouwbaarheids classificaties voor een evaluatie zijn als volgt.

**Beschik baarheid van gegevens punt** | **Betrouwbaarheidsclassificatie**
--- | ---
0%-20% | 1 ster
21%-40% | 2 sterren
41%-60% | 3 sterren
61%-80% | 4 sterren
81%-100% | 5 sterren

Meer [informatie](best-practices-assessment.md#best-practices-for-confidence-ratings) over best practices voor betrouwbaarheids classificaties.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Een Azure Migrate apparaat instellen
> * Een evaluatie gemaakt en geëvalueerd

Ga door naar de derde zelf studie in de reeks om te leren hoe u virtuele VMware-machines naar Azure migreert met Azure Migrate server migratie.

> [!div class="nextstepaction"]
> [Virtuele VMware-machines migreren](./tutorial-migrate-vmware.md)
