---
title: Hyper-V-VM's beoordelen voor migratie naar Azure met Azure Migrate | Microsoft Docs
description: Hier wordt beschreven hoe u met behulp van Azure Migrate-serverevaluatie on-premises Hyper-V-VM's kunt evalueren voor migratie naar Azure.
ms.topic: tutorial
ms.date: 06/03/2020
ms.custom: mvc
ms.openlocfilehash: 4c95916bf5f92f8a82b9dfae50aa311891857e7a
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2020
ms.locfileid: "86114241"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>Hyper-V-VM's beoordelen met Azure Migrate-serverevaluatie

In dit artikel leest u hoe u on-premises Hyper-V-VM's kunt beoordelen met behulp van het hulpprogramma [Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool).


Deze zelfstudie is de tweede in een reeks die laat zien hoe u Hyper-V-VM's kunt evalueren en migreren naar Azure. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure Migrate-project instellen.
> * Azure Migrate-apparaat instellen en registreren.
> * Continue detectie van on-premises VM's starten.
> * Gedetecteerde VM's groeperen en de groep evalueren.
> * De evaluatie controleren.

> [!NOTE]
> In zelfstudies ziet u het eenvoudigste implementatiepad voor een scenario, zodat u snel een haalbaarheidstest kunt instellen. Waar mogelijk maken zelfstudies gebruik van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven. Raadpleeg de artikelen met procedures voor gedetailleerde instructies.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.


## <a name="prerequisites"></a>Vereisten

- [Voltooi](tutorial-prepare-hyper-v.md) de eerste zelfstudie in de serie. Als u dit niet doet, werken de instructies in deze zelfstudie niet.
- In de eerste zelfstudie hebt u het volgende gedaan:
    - [Azure voorbereiden](tutorial-prepare-hyper-v.md#prepare-azure) om te werken met Azure Migrate.
    - Beoordeling van [Hyper-V](tutorial-prepare-hyper-v.md#prepare-for-assessment)-hosts en -VM's voorbereiden.
    - [Controleren](tutorial-prepare-hyper-v.md#prepare-for-appliance-deployment) wat u nodig hebt om het Azure Migrate-apparaat voor Hyper-V-evaluatie te implementeren.

## <a name="set-up-an-azure-migrate-project"></a>Een Azure Migrate-project instellen

1. Zoek in de Azure-portal in **Alle services** naar **Azure Migrate**.
2. Selecteer **Azure Migrate** in de lijst met zoekresultaten.
3. In **Overzicht**, onder **Servers ontdekken, evalueren en migreren**, klikt u op **Servers evalueren en migreren**.

    ![Servers detecteren en evalueren](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. Klik in **Aan de slag** op **Hulpprogramma's toevoegen**.
5. Selecteer op het tabblad **Project migreren** uw Azure-abonnement en maak een resourcegroep als u er nog geen hebt.
6. Geef in **Projectdetails** de projectnaam en de regio op waarin u het project wilt maken. Bekijk ondersteunde geografische regio's voor [openbare](migrate-support-matrix.md#supported-geographies-public-cloud) clouds en [overheidsclouds](migrate-support-matrix.md#supported-geographies-azure-government).

    - De projectregio wordt alleen gebruikt om de metagegevens op te slaan die worden verzameld van on-premises virtuele machines.
    - U kunt een andere Azure-doelregio selecteren wanneer u de virtuele machines migreert. Alle Azure-regio's worden ondersteund voor het migratiedoel.

    ![Een Azure Migrate-project maken](./media/tutorial-assess-hyper-v/migrate-project.png)

7. Klik op **Volgende**.
8. In **Evaluatieprogramma selecteren** selecteert u **Azure Migrate: Serverevaluatie** > **Volgende**.

    ![Een Azure Migrate-project maken](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. In **Migratieprogramma selecteren** selecteert u **Het toevoegen van een migratieprogramma voorlopig overslaan** > **Volgende**.
10. Controleer in **Evalueren en hulpprogramma's toevoegen** de instellingen en klik op **Hulpmiddelen toevoegen**.
11. Wacht een paar minuten tot het Azure Migrate-project is geïmplementeerd. U wordt naar de projectpagina geleid. Als u het project niet ziet, kunt u het openen vanuit **Servers** in het Azure Migrate-dashboard.

## <a name="set-up-the-azure-migrate-appliance"></a>Het Azure Migrate-apparaat instellen


Azure Migrate-serverevaluatie maakt gebruik van een lichtgewicht Azure Migrate-apparaat. Het apparaat voert VM-detectie uit en verzendt metagegevens en prestatiegegevens van de virtuele machine naar Azure Migrate. Het apparaat kan op verschillende manieren worden ingesteld.

- Ingesteld op een Hyper-V-VM met behulp van een gedownloade Hyper-V-VHD. Dit is de methode die in deze zelfstudie wordt gebruikt.
- Ingesteld op een Hyper-V-VM of fysieke machine met een PowerShell-installatiescript. [Deze methode](deploy-appliance-script.md) moet worden gebruikt als u een virtuele machine niet kunt instellen met behulp de VHD of als u zich in Azure Government bevindt.

Nadat u het apparaat hebt gemaakt, controleert u of er verbinding kan worden gemaakt met Azure Migrate-serverevaluatie, configureert u het voor de eerste keer en registreert u het bij het Azure Migrate-project.

### <a name="download-the-vhd"></a>De VHD downloaden

Download het zip-bestand met de VHD-sjabloon voor het apparaat.

1. In **Migratiedoelen** > **Servers** > **Azure Migrate: Serverevaluatie** klikt u op **Ontdekken**.
2. In **Machines ontdekken** > **Zijn de machines gevirtualiseerd?** klikt u op **Ja, met Hyper-V**.
3. Klik op **Downloaden** om het VHD-bestand te downloaden.

    ![VM downloaden](./media/tutorial-assess-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Beveiliging controleren

Controleer of het zip-bestand veilig is voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.

2. Gebruik de volgende PowerShell-opdracht om de hash voor het zip-bestand te genereren
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Gebruiksvoorbeeld: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v1.19.06.27.zip -Algorithm SHA256```

3.  Controleer de meest recente versie van het apparaat en de hashwaarden:

    - Voor de openbare Azure-cloud:

        **Scenario** | **Downloaden** | **SHA256**
        --- | --- | ---
        Hyper-V (8,93 GB) | [Nieuwste versie](https://aka.ms/migrate/appliance/hyperv) |  572be425ea0aca69a9aa8658c950bc319b2bdbeb93b440577264500091c846a1

    - Voor Azure Government:

        **Scenario*** | **Downloaden** | **SHA256**
        --- | --- | ---
        Hyper-V (63,1 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2120200&clcid=0x409) |  2c5e73a1e5525d4fae468934408e43ab55ff397b7da200b92121972e683f9aa3


### <a name="create-the-appliance-vm"></a>Het VM-apparaat maken

Importeer het gedownloade bestand en maak de virtuele machine.

1. Na het downloaden van het gecomprimeerde VHD-bestand naar de Hyper-V-host waarop de apparaat-VM wordt geplaatst, pakt u het zip-bestand uit.
    - Op de uitgepakte locatie wordt het bestand uitgepakt naar een map met de naam **AzureMigrateAppliance_VersionNumber**.
    - Deze map bevat een submap, ook wel **AzureMigrateAppliance_VersionNumber** genoemd.
    - Deze submap bevat weer drie submappen: **Momentopnamen**, **Virtuele harde schijven** en **Virtuele machines**.

2. Open Hyper-V-beheer. Klik in **Acties** op **Virtuele machine importeren**.

    ![VHD implementeren](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. Klik in de wizard Virtuele machine importeren > **Voordat u begint** op **Volgende**.
3. Selecteer in **Map zoeken** de map **Virtual Machines**. Klik op **Volgende**.
1. Klik in **Virtuele machine selecteren** op **Volgende**.
2. Klik in **Importtype kiezen** op **De virtuele machine kopiëren (een nieuwe unieke id maken)** . Klik op **Volgende**.
3. Laat in **Bestemming kiezen** de standaardinstelling ongewijzigd. Klik op **Volgende**.
4. Laat in **Opslagmappen** de standaardinstelling ongewijzigd. Klik op **Volgende**.
5. Geef in **Netwerk kiezen** de virtuele switch op die door de virtuele machine wordt gebruikt. De switch heeft internetverbinding nodig om gegevens naar Azure te verzenden. [Lees meer](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines) over het maken van een virtuele switch.
6. Controleer de instellingen in **Samenvatting**. Klik vervolgens op **Voltooien**.
7. Start de virtuele machine in Hyper-V-beheer > **Virtual Machines**.


## <a name="verify-appliance-access-to-azure"></a>Apparaattoegang tot Azure controleren

Zorg ervoor dat de apparaat-VM verbinding kan maken met Azure-URL's voor [openbare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls)clouds.

### <a name="configure-the-appliance"></a>Het apparaat configureren

Het apparaat voor de eerste keer instellen.

> [!NOTE]
> Als u het apparaat instelt met behulp van een [PowerShell-script](deploy-appliance-script.md) in plaats van de gedownloade VHD, zijn de eerste twee stappen in deze procedure niet relevant.

1. Klik in Hyper-V-beheer > **Virtual Machines** met de rechtermuisknop op de virtuele machine > **Verbinding maken**.
2. Geef de taal, de tijdzone en een wachtwoord op voor het apparaat.
3. Open een browser op een computer die verbinding kan maken met de VM en open de URL van de web-app van het apparaat: **https://*apparaatnaam of IP-adres*: 44368**.

   U kunt de app ook openen vanaf het bureaublad van het apparaat door te klikken op de snelkoppeling naar de app.
1. Ga als volgt te werk in de web-app > **Vereisten instellen**:
    - **Licentie**: Accepteer de licentievoorwaarden en lees de informatie van derden.
    - **Connectiviteit**: de app controleert of de virtuele machine toegang heeft tot internet. Als de virtuele machine een proxy gebruikt:
      - Klik op **Proxyinstellingen** en geef het proxyadres en de controlepoort op in het formulier http://ProxyIPAddress of http://ProxyFQDN.
      - Geef referenties op als de proxy verificatie nodig heeft.
      - Alleen HTTP-proxy wordt ondersteund.
    - **Tijdsynchronisatie**: Tijd is geverifieerd. De tijd op het apparaat moet zijn gesynchroniseerd met internettijd voor VM zodat detectie goed werkt.
    - **Updates installeren**: Azure Migrate-serverevaluatie controleert of de meest recente updates zijn geïnstalleerd op het apparaat.

### <a name="register-the-appliance-with-azure-migrate"></a>Het apparaat registreren bij Azure Migrate

1. Klik op **Aanmelden**. Als dit niet wordt weergegeven, controleert u of de pop-upblokkering in de browser is uitgeschakeld.
2. Meld u aan met uw Azure-referenties op het nieuwe tabblad.
    - Meld u aan met uw gebruikersnaam en wachtwoord.
    - Aanmelden met een pincode wordt niet ondersteund.
3. Nadat u zich hebt aangemeld, gaat u terug naar de web-app.
4. Selecteer het abonnement waarin het Azure Migrate-project is gemaakt. Selecteer vervolgens het project.
5. Geef een naam op voor het apparaat. De naam moet alfanumeriek zijn met 14 tekens of minder.
6. Klik op **Registreren**.


### <a name="delegate-credentials-for-smb-vhds"></a>Referenties voor SMB-VHD's delegeren

Als u VHD's uitvoert op SMB's, moet u de delegatie van referenties van het apparaat naar de Hyper-V-hosts inschakelen. Als u dit wilt doen, schakelt u elke host in als gemachtigde voor het apparaat. Als u de zelfstudies in volgorde hebt gevolgd, hebt u dit gedaan in de vorige zelfstudie, toen u Hyper-V voor de evaluatie en migratie hebt voorbereid. U moet CredSSP ofwel [handmatig](tutorial-prepare-hyper-v.md#enable-credssp-to-delegate-credentials) voor de hosts hebben ingesteld, of door [een script uit te voeren](tutorial-prepare-hyper-v.md#run-the-script) dat dit doet.

Schakel dit als volgt in op het apparaat:

#### <a name="option-1"></a>Optie 1

Voer deze opdracht uit op de apparaat-VM. HyperVHost1/HyperVHost2 zijn voorbeelden van hostnamen.

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
```

Voorbeeld: ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>Optie 2

U kunt dit ook doen in de editor voor Lokaal groepsbeleid op het apparaat:

1. Klik in **Lokaal computerbeleid** > **Computerconfiguratie** op **Administratieve sjablonen** > **Systeem** > **Delegatie van referenties**.
2. Dubbelklik op **Delegeren van nieuwe referenties toestaan** en selecteer **Ingeschakeld**.
3. Klik in **Opties** op **Weergeven** en voeg elke Hyper-V-host toe die u op de lijst wilt detecteren, waarbij u **wsman/** gebruikt als voorvoegsel.
4. Dubbelklik vervolgens in **Delegatie van referenties** op **Delegeren van nieuwe referenties toestaan met NTLM-serververificatie**. Voeg weer elke Hyper-V-host toe die u op de lijst wilt detecteren, met **wsman/** als voorvoegsel.

## <a name="start-continuous-discovery"></a>Continue detectie starten

Maak vanaf het apparaat verbinding met Hyper-V-hosts of -clusters en start de VM-detectie.

1. Geef in **Gebruikersnaam** en **Wachtwoord** de accountreferenties op die door het apparaat worden gebruikt om VM's te detecteren. Geef een beschrijvende naam op voor de referenties en klik op **Details opslaan**.
2. Klik op **Host toevoegen** en geef de gegevens van de Hyper-V-host/het cluster.
3. Klik op **Valideren**. Na validatie wordt het aantal VM's weergegeven dat op elke host/elk cluster kan worden gedetecteerd.
    - Als de validatie mislukt voor een host, raadpleegt u de fout door te klikken op het pictogram in de kolom **Status**. Los problemen op en valideer opnieuw.
    - Als u hosts of clusters wilt verwijderen, selecteert u > **Verwijderen**.
    - U kunt een specifieke host niet verwijderen uit een cluster. U kunt alleen het hele cluster verwijderen.
    - U kunt een cluster toevoegen, zelfs als er problemen zijn met specifieke hosts in het cluster.
4. Klik na validatie op **Opslaan en detectie starten** om het detectieproces te starten.

De detectie wordt gestart. Het duurt ongeveer 1,5 minuten per host voordat de metagegevens van de gedetecteerde servers worden weergegeven in de Azure-portal.

### <a name="verify-vms-in-the-portal"></a>VM's verifiëren in de portal

Nadat de detectie is voltooid, kunt u controleren of de VM's worden weergegeven in de portal.

1. Open het Azure Migrate-dashboard.
2. Klik op de pagina **Azure Migrate - Servers** > **Azure Migrate: Serverevaluatie** op het pictogram dat het aantal voor **Gedetecteerde servers** weergeeft.

## <a name="set-up-an-assessment"></a>Een evaluatie instellen

U kunt twee soorten evaluaties uitvoeren met behulp van Azure Migrate-serverevaluatie.

**Evaluatie** | **Details** | **Gegevens**
--- | --- | ---
**Op basis van prestaties** | Evaluaties op basis van verzamelde prestatiegegevens | **Aanbevolen VM-grootte**: Gebaseerd op verbruiksgegevens voor CPU en geheugen.<br/><br/> **Aanbevolen schijftype (standaard of premium beheerde schijf)** : Op basis van de IOPS en doorvoer van de on-premises schijven.
**Zoals on-premises** | Evaluaties op basis van on-premises grootte aanpassen. | **Aanbevolen VM-grootte**: Op basis van de on-premises VM-grootte<br/><br> **Aanbevolen schijftype**: Op basis van de instelling voor het opslagtype die u voor de evaluatie selecteert.



### <a name="run-an-assessment"></a>Een evaluatie uitvoeren

Voer als volgt een evaluatie uit:

1. Bekijk de [best practices](best-practices-assessment.md) voor het maken van evaluaties.
2. In **Servers** > **Azure Migrate: Serverevaluatie** klikt u op **Evalueren**.

    ![Evalueren](./media/tutorial-assess-hyper-v/assess.png)

3. Geef bij **Servers evalueren** een naam op voor de evaluatie.
4. Klik op **Alles weergeven** om de evaluatie-eigenschappen te controleren.

    ![Evaluatie-eigenschappen](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. In **Een groep selecteren of maken** selecteert u **Nieuwe maken** en geeft u een groepsnaam op. Een groep verzamelt een of meer VM's voor evaluatie.
4. Selecteer in **Machines toevoegen aan de groep** de VM's die aan de groep moeten worden toegevoegd.
5. Klik op **Evaluatie maken** om de groep te maken en de evaluatie uit te voeren.

    ![Een evaluatie maken](./media/tutorial-assess-hyper-v/assessment-create.png)

6. Nadat de evaluatie is gemaakt, kunt u deze bekijken in **Servers** > **Azure Migrate: Server-evaluatieserver**.
7. Klik op **Evaluatie exporteren** om deze te downloaden als een Excel-bestand.


## <a name="review-an-assessment"></a>Een evaluatie beoordelen

Een evaluatie beschrijft het volgende:

- **Azure-gereedheid**: Hiermee wordt aangegeven of VM's geschikt zijn voor migratie naar Azure.
- **Schatting maandelijkse kosten**: De geschatte maandelijkse reken- en opslagkosten voor het uitvoeren van de VM's in Azure.
- **Schatting maandelijkse opslagkosten**: Geschatte kosten voor schijfopslag na migratie.


### <a name="view-an-assessment"></a>Een evaluatie weergeven

1. In **Migratiedoelen** >  **Servers** > **Azure Migrate: Serverevaluatie** klikt u op **Evaluaties**.
2. Klik in **Evaluaties** op een evaluatie om deze te openen.

    ![Evaluatie-overzicht](./media/tutorial-assess-hyper-v/assessment-summary.png)


### <a name="review-azure-readiness"></a>Azure-gereedheid beoordelen

1. Controleer in **Azure-gereedheid** of de VM's gereed zijn voor migratie naar Azure.
2. Controleer de VM-status:
    - **Gereed voor Azure**: Azure Migrate raadt een VM-grootte en schattingen voor de kosten aan voor VM's in de evaluatie.
    - **Gereed met voorwaarden**: Geeft problemen en voorgesteld herstel weer.
    - **Niet gereed voor Azure**: Geeft problemen en voorgesteld herstel weer.
    - **Gereedheid onbekend**: Wordt gebruikt wanneer Azure Migrate de gereedheid niet kan evalueren door problemen met de beschikbaarheid van gegevens.

2. Klik op een **Azure-gereedheid**-status. U kunt details van de VM-gereedheid weergeven en inzoomen op de details van de VM, inclusief berekenings-, opslag- en netwerkinstellingen.

### <a name="review-cost-details"></a>Gedetailleerde kosten beoordelen

Deze weergave toont de geschatte berekenings- en opslagkosten om virtuele machines in Azure uit te voeren.

1. Controleer de maandelijkse berekenings- en opslagkosten. De kosten worden geaggregeerd voor alle VM's in de geëvalueerde groep.

    - Schattingen van kosten zijn gebaseerd op de grootte-aanbevelingen voor een machine en de schijven en eigenschappen.
    - De geschatte maandelijkse kosten voor berekening en opslag worden weergegeven.
    - De schatting van de kosten is voor het uitvoeren van de on-premises VM's als IaaS-VM's. Azure Migrate Serverevaluatie houdt geen rekening met PaaS-of SaaS-kosten.

2. U kunt de schatting van de maandelijkse opslagkosten controleren. In deze weergave worden geaggregeerde opslagkosten voor de geëvalueerde groep weergegeven, gesplitst over verschillende typen opslagschijven.
3. U kunt inzoomen om de details voor specifieke VM's te bekijken.


### <a name="review-confidence-rating"></a>Betrouwbaarheidsclassificatie controleren

Wanneer u evaluaties op basis van prestaties uitvoert, wordt er een betrouwbaarheidsclassificatie aan de evaluatie toegewezen.

![Betrouwbaarheidsclassificatie](./media/tutorial-assess-hyper-v/confidence-rating.png)

- Er wordt een classificatie van 1 ster (laagst) tot 5 sterren (hoogst) toegekend.
- De betrouwbaarheidsclassificatie helpt u om de betrouwbaarheid in te schatten van de aanbevelingen voor de grootte die de evaluatie geeft.
- De betrouwbaarheidsclassificatie is op basis van de beschikbaarheid van de gegevenspunten die nodig zijn om de evaluatie te berekenen.

De betrouwbaarheidsclassificatie voor een evaluatie zijn als volgt.

**Beschikbaarheid van gegevenspunten** | **Betrouwbaarheidsclassificatie**
--- | ---
0%-20% | 1 ster
21%-40% | 2 sterren
41%-60% | 3 sterren
61%-80% | 4 sterren
81%-100% | 5 sterren

[Meer informatie](best-practices-assessment.md#best-practices-for-confidence-ratings) over de aanbevolen procedures voor betrouwbaarheidsclassificaties.





## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Een Azure Migrate-apparaat ingesteld
> * Een evaluatie gemaakt en gecontroleerd

Ga door naar de derde zelfstudie in deze reeks om te leren hoe u Hyper-V-VM's migreert naar Azure met Azure Migrate Server Migration.

> [!div class="nextstepaction"]
> [Hyper-V-VM's migreren](./tutorial-migrate-hyper-v.md)
