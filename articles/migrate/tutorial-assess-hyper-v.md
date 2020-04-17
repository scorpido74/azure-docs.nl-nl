---
title: Hyper-V VM's beoordelen op migratie naar Azure met Azure Migrate | Microsoft Documenten
description: Beschrijft hoe u on-premises Hyper-V VM's beoordelen op migratie naar Azure met Azure Migrate Server Assessment.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: c627902268af3a91e172223c1741dd24ea21fa92
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535448"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>Hyper-V VM's beoordelen met Azure Migrate Server Assessment

In dit artikel ziet u hoe u on-premises Hyper-V VM's beoordelen met behulp van het hulpprogramma [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool)


Deze zelfstudie is de tweede in een reeks die laat zien hoe u Hyper-V VM's beoordelen en migreren naar Azure. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Stel een Azure Migrate-project in.
> * Een Azure Migrate-toestel instellen en registreren.
> * Start continue ontdekking van on-premises VM's.
> * Groep ontdekte VM's en beoordeel de groep.
> * Bekijk de beoordeling.

> [!NOTE]
> In zelfstudies ziet u het eenvoudigste implementatiepad voor een scenario, zodat u snel een proof-of-concept instellen. Tutorials maken gebruik van standaardopties waar mogelijk, en niet alle mogelijke instellingen en paden weer te geven. Bekijk de artikelen How-to voor gedetailleerde instructies.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/pricing/free-trial/) voordat u begint.


## <a name="prerequisites"></a>Vereisten

- [Voltooi](tutorial-prepare-hyper-v.md) de eerste tutorial in deze serie. Als u dat niet doet, werken de instructies in deze zelfstudie niet.
- Hier is wat je had moeten doen in de eerste tutorial:
    - [Bereid Azure voor](tutorial-prepare-hyper-v.md#prepare-azure) om met Azure Migrate te werken.
    - [Bereid Hyper-V hosts](tutorial-prepare-hyper-v.md#prepare-hyper-v-for-assessment) en VM's beoordeling voor.
    - [Controleer](tutorial-prepare-hyper-v.md#prepare-for-appliance-deployment) wat u nodig hebt om het Azure Migrate-toestel te implementeren voor hyper-v-beoordeling.

## <a name="set-up-an-azure-migrate-project"></a>Een Azure Migrate-project instellen

1. Zoek in de Azure-portal in **Alle services** naar **Azure Migrate**.
2. Selecteer **Azure Migreren**in de zoekresultaten .
3. In **Overzicht**, onder **Servers ontdekken, evalueren en migreren**, klikt u op **Servers evalueren en migreren**.

    ![Servers ontdekken en beoordelen](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. Klik in **Aan de slag** op **Hulpprogramma's toevoegen**.
5. Selecteer op het tabblad **Project migreren** uw Azure-abonnement en maak een brongroep als u er geen hebt.
6. Geef in **Projectdetails**de projectnaam op en het gebied waarin u het project wilt maken. Bekijk ondersteunde regio's voor [publieke](migrate-support-matrix.md#supported-geographies-public-cloud) en [overheidswolken.](migrate-support-matrix.md#supported-geographies-azure-government)

    - De projectregio wordt alleen gebruikt om de metagegevens op te slaan die zijn verzameld bij on-premises VM's.
    - U een ander Azure-doelgebied selecteren wanneer u de VM's migreert. Alle Azure-regio's worden ondersteund voor migratiedoel.

    ![Een Azure-migratieproject maken](./media/tutorial-assess-hyper-v/migrate-project.png)

7. Klik op **Volgende**.
8. Selecteer Azure **Migrate: Server Assessment** > **Next**in **Het beoordelingshulpprogramma selecteren.**

    ![Een Azure-migratieproject maken](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. In **Migratieprogramma selecteren** selecteert u **Het toevoegen van een migratieprogramma voorlopig overslaan** > **Volgende**.
10. Bekijk in **Hulpmiddelen voor controleren + toevoegen**de instellingen en klik op Gereedschappen **toevoegen**.
11. Wacht een paar minuten tot het Azure Migrate-project is geïmplementeerd. U wordt naar de projectpagina geleid. Als u het project niet ziet, kunt u het openen vanuit **Servers** in het Azure Migrate-dashboard.

## <a name="set-up-the-azure-migrate-appliance"></a>Het Azure Migrate-toestel instellen


Azure Migrate:Server Assessment maakt gebruik van een lichtgewicht Azure Migrate-toestel. Het toestel voert VM-detectie uit en verzendt VM-metagegevens en prestatiegegevens naar Azure Migrate. Het apparaat kan op verschillende manieren worden ingesteld.

- Stel in op een Hyper-V VM met behulp van een gedownloade Hyper-V VHD. Dit is de methode die in deze zelfstudie wordt gebruikt.
- Instellen op een Hyper-V VM of fysieke machine met een PowerShell-installatiescript. [Deze methode](deploy-appliance-script.md) moet worden gebruikt als u een VM niet instellen met de VHD of als u zich in Azure Government bevindt.

Nadat u het toestel hebt gemaakt, controleert u of het verbinding kan maken met Azure Migrate:Server Assessment, configureer het voor de eerste keer en registreer u het met het Azure Migrate-project.

### <a name="download-the-vhd"></a>Download de VHD

Download de VHD-sjabloon met rits voor het apparaat.

1. Klik in **Migration Goals** > **Servers** > **Azure Migrate: Server assessment**op **Ontdekken**.
2. In **Discover machines** > **Worden uw machines gevirtualiseerd?**, klik op **Ja, met Hyper-V**.
3. Klik **op Downloaden** om het VHD-bestand te downloaden.

    ![VM downloaden](./media/tutorial-assess-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Beveiliging verifiëren

Controleer of het zip-bestand veilig is, voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.

2. Voer de volgende PowerShell-opdracht uit om de hash voor het ZIP-bestand te genereren
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Gebruiksvoorbeeld: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v1.19.06.27.zip -Algorithm SHA256```

3.  Voor toestelversie 2.19.07.30 moet de gegenereerde hash overeenkomen met deze instellingen.

  **Algoritme** | **Hash-waarde**
  --- | ---
  MD5 | 29a7531f32bcf69f32d964fa5ae950bc
  SHA256 | 37b3f27bc4f475872e355f04fcb8f38606c84534c117d1609f2d1244569b31

### <a name="create-the-appliance-vm"></a>De toestel-VM maken

Importeer het gedownloade bestand en maak de VM.

1. Na het downloaden van het gezipte VHD-bestand naar de Hyper-V-host waarop de toestel-VM zal worden geplaatst, haalt u het zip-bestand eruit.
    - Op de uitgepakte locatie wordt het bestand uitgepakt in een map met de naam **AzureMigrateAppliance_VersionNumber**.
    - Deze map bevat een submap, ook wel **AzureMigrateAppliance_VersionNumber**genoemd.
    - Deze submap bevat nog drie submappen - **Snapshots,** **Virtual Hard Disks**en Virtual **Machines**.

2. Open Hyper-V-beheer. Klik **in Acties**op Virtuele machine **importeren**.

    ![VHD implementeren](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. Klik in de wizard Virtuele machine importeren > **Voordat u begint**op **Volgende**.
3. Selecteer in **Map Zoeken**de map **Virtuele machines.** Klik vervolgens op **Volgende**.
1. Klik in **Virtuele machine selecteren**op **Volgende**.
2. Klik in **Type importeren kiezen**op De virtuele machine kopiëren **(een nieuwe unieke ID maken).** Klik vervolgens op **Volgende**.
3. Verlaat **in Bestemming kiezen**de standaardinstelling. Klik op **Volgende**.
4. Laat **in Opslagmappen**de standaardinstelling achter. Klik op **Volgende**.
5. Geef **in Netwerk kiezen**de virtuele switch op die de virtuele switch zal gebruiken. De switch heeft internetverbinding nodig om gegevens naar Azure te verzenden. [Meer informatie](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines) over het maken van een virtuele switch.
6. **Bekijk in het kort**de instellingen. Klik vervolgens op **Voltooien**.
7. Start in Hyper-V Manager > **Virtual Machines**de VM.


## <a name="verify-appliance-access-to-azure"></a>De toegang tot het toestel tot Azure verifiëren

Zorg ervoor dat de toestel-VM verbinding kan maken met Azure-URL's voor [openbare](migrate-appliance.md#public-cloud-urls) en [overheidsclouds.](migrate-appliance.md#government-cloud-urls)

### <a name="configure-the-appliance"></a>Het toestel configureren

Stel het apparaat voor de eerste keer in.

> [!NOTE]
> Als u het toestel instelt met een [PowerShell-script](deploy-appliance-script.md) in plaats van het gedownloade VHD, zijn de eerste twee stappen in deze procedure niet relevant.

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

Als u VHD's uitvoert op het MKB, moet u de overdracht van referenties van het toestel naar de Hyper-V-hosts inschakelen. Dit vereist het volgende:

- U stelt elke host in staat om als gemachtigde voor het toestel op te treden. Als je de tutorials op volgorde hebt gevolgd, deed je dit in de vorige zelfstudie, toen je Hyper-V voorbereidde op beoordeling en migratie. U moet CredSSP handmatig voor de [hosts](tutorial-prepare-hyper-v.md#enable-credssp-on-hosts)hebben ingesteld of door een script uit te [voeren](tutorial-prepare-hyper-v.md#prepare-with-a-script) dat dit doet.
- Schakel CredSSP-delegatie in, zodat het Azure Migrate-toestel als client kan fungeren en referenties kan delegeren aan een host.

Schakel het toestel als volgt in:

#### <a name="option-1"></a>Mogelijkheid 1

Voer deze opdracht uit op de toestel-VM. HyperVHost1/HyperVHost2 zijn voorbeeldhostnamen.

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
```

Voorbeeld: ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>Optie 2

U dit ook doen in de lokale groepsbeleidseditor op het toestel:

1. Klik in **computerconfiguratie lokaal** > **System** > **computerbeleid**op Overdracht systeemreferenties **voor systeemreferenties** > beheren .**Credentials Delegation**
2. Dubbelklik op **Het delegeren van nieuwe referenties toestaan**en selecteer **Ingeschakeld**.
3. Klik **in Opties**op **Weergeven**en voeg elke Hyper-V-host die u wilt ontdekken toe aan de lijst, met **wsman/** als voorvoegsel.
4. Dubbelklik vervolgens in **Credentials Delegation**op **Het delegeren van nieuwe referenties toestaan met NTLM-serververificatie**. Nogmaals, voeg elke Hyper-V-host die je wilt ontdekken toe aan de lijst, met **wsman/** als voorvoegsel.

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

Dit begint te ontdekken. Het duurt ongeveer 1,5 minuten per host voordat metagegevens van gedetecteerde servers worden weergegeven in de Azure-portal.

### <a name="verify-vms-in-the-portal"></a>VM's verifiëren in de portal

Nadat detectie is voltooid, u controleren of de VM's in de portal worden weergegeven.

1. Open het Azure Migrate-dashboard.
2. Klik in **Azure Migrate - Servers** > **Azure Migrate: Server Assessment** pagina op het pictogram dat het aantal voor **gedetecteerde servers**weergeeft.

## <a name="set-up-an-assessment"></a>Een beoordeling instellen

Er zijn twee typen beoordelingen die u uitvoeren met Azure Migrate Server Assessment.

**Beoordeling** | **Details** | **Gegevens**
--- | --- | ---
**Op prestaties gebaseerd** | Beoordelingen op basis van verzamelde prestatiegegevens | **Aanbevolen VM-grootte:** gebaseerd op cpu- en geheugengebruiksgegevens.<br/><br/> **Aanbevolen schijftype (standaard of premium beheerde schijf):** op basis van de IOPS en doorvoer van de on-premises schijven.
**Als on-premises** | Beoordelingen op basis van on-premises dimensionering. | **Aanbevolen VM-grootte:** op basis van de on-premises VM-grootte<br/><br> **Aanbevolen schijftype:** op basis van de instelling voor het opslagtype die u voor de beoordeling selecteert.



### <a name="run-an-assessment"></a>Een evaluatie uitvoeren

Voer een beoordeling als volgt uit:

1. Bekijk de [aanbevolen procedures](best-practices-assessment.md) voor het maken van beoordelingen.
2. Klik in **servers** > **Azure Migrate: Serverbeoordeling**op **Beoordelen**.

    ![Evalueren](./media/tutorial-assess-hyper-v/assess.png)

3. Geef **in Servers beoordelen**een naam op voor de beoordeling.
4. Klik op **Alles weergeven** om de evaluatie-eigenschappen te controleren.

    ![Beoordelingseigenschappen](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. Selecteer in **Een groep selecteren of maken**de optie Nieuw **maken**en geef een groepsnaam op. Een groep verzamelt een of meer VM's voor beoordeling.
4. Selecteer vm's die u aan de groep wilt toevoegen in **Machines toevoegen.**
5. Klik **op Beoordeling maken** om de groep te maken en voer de beoordeling uit.

    ![Een evaluatie maken](./media/tutorial-assess-hyper-v/assessment-create.png)

6. Nadat de beoordeling is gemaakt, bekijkt u deze in **Servers** > **Azure Migrate: Server Assessment**.
7. Klik op **Evaluatie exporteren** om deze te downloaden als een Excel-bestand.


## <a name="review-an-assessment"></a>Een beoordeling beoordelen

Een beoordeling beschrijft:

- **Azure-gereedheid**: of VM's geschikt zijn voor migratie naar Azure.
- **Schatting van de maandelijkse kosten:** de geschatte maandelijkse reken- en opslagkosten voor het uitvoeren van de VM's in Azure.
- **Schatting van de maandelijkse opslagkosten**: Geschatte kosten voor schijfopslag na migratie.


### <a name="view-an-assessment"></a>Bekijk een beoordeling

1. Klik in **Migratiedoelen** >  **Servers** > **Azure Migreren: Serverbeoordeling**, klik op **Beoordelingen**.
2. Klik in **Assessments**op een beoordeling om deze te openen.

    ![Evaluatiesamenvatting](./media/tutorial-assess-hyper-v/assessment-summary.png)


### <a name="review-azure-readiness"></a>Azure-gereedheid controleren

1. Controleer in **Azure-gereedheid**of VM's klaar zijn voor migratie naar Azure.
2. Controleer de VM-status:
    - **Klaar voor Azure:** Azure Migrate beveelt in de beoordeling een VM-grootte en kostenramingen voor VM's aan.
    - **Klaar met voorwaarden**: Toont problemen en voorgestelde herstel.
    - **Niet klaar voor Azure:** toont problemen en voorgestelde herstel.
    - **Gereedheid onbekend**: wordt gebruikt wanneer Azure Migrate de gereedheid niet kan beoordelen vanwege problemen met de beschikbaarheid van gegevens.

2. Klik op een **Azure-gereedheidsstatus.** U de gereedheidsgegevens van vm's bekijken en inzoomen op VM-details, waaronder rekenkracht, opslag en netwerkinstellingen.

### <a name="review-cost-details"></a>Kostendetails bekijken

In deze weergave worden de geschatte reken- en opslagkosten weergegeven voor het uitvoeren van VM's in Azure.

1. Bekijk de maandelijkse reken- en opslagkosten. De kosten worden geaggregeerd voor alle VM's in de beoordeelde groep.

    - Kostenramingen zijn gebaseerd op de grootteaanbevelingen voor een machine en de schijven en eigenschappen ervan.
    - Geschatte maandelijkse kosten voor rekenkracht en opslag worden weergegeven.
    - De kostenschatting is voor het uitvoeren van de on-premises VM's als IaaS VM's. Azure Migrate Server Assessment houdt geen rekening met paas- of SaaS-kosten.

2. U schattingen van de maandelijkse opslagkosten bekijken. Deze weergave toont geaggregeerde opslagkosten voor de beoordeelde groep, verdeeld over verschillende soorten opslagschijven.
3. U inzoomen om details voor specifieke VM's te bekijken.


### <a name="review-confidence-rating"></a>Betrouwbaarheidsclassificatie controleren

Wanneer u op prestaties gebaseerde beoordelingen uitvoert, wordt een betrouwbaarheidsbeoordeling toegewezen aan de beoordeling.

![Betrouwbaarheidsclassificatie](./media/tutorial-assess-hyper-v/confidence-rating.png)

- Een beoordeling van 1-sterren (laagste) tot 5-sterren (hoogste) wordt toegekend.
- Met de betrouwbaarheidsclassificatie u de betrouwbaarheid inschatten van de aanbevelingen die door de beoordeling worden gegeven.
- De betrouwbaarheidsbeoordeling is gebaseerd op de beschikbaarheid van gegevenspunten die nodig zijn om de beoordeling te berekenen.

Vertrouwensbeoordelingen voor een beoordeling zijn als volgt.

**Beschikbaarheid van gegevenspunten** | **Betrouwbaarheidsclassificatie**
--- | ---
0%-20% | 1 ster
21%-40% | 2 sterren
41%-60% | 3 sterren
61%-80% | 4 sterren
81%-100% | 5 sterren

[Meer informatie](best-practices-assessment.md#best-practices-for-confidence-ratings) over aanbevolen procedures voor vertrouwensbeoordelingen.





## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Een Azure Migrate-toestel instellen
> * Een beoordeling maken en herzien

Ga verder naar de derde zelfstudie in de reeks, voor meer informatie over het migreren van Hyper-V VM's naar Azure met Azure Migrate Server Migration.

> [!div class="nextstepaction"]
> [Hyper-V-VM's migreren](./tutorial-migrate-hyper-v.md)
