---
title: Virtuele Hyper-V-machines evalueren voor migratie naar Azure met Azure Migrate | Microsoft Docs
description: Hierin wordt beschreven hoe u on-premises virtuele Hyper-V-machines kunt beoordelen voor migratie naar Azure met behulp van Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 08/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: b93d9ee65850749e79714b632584f1977ca88c81
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639989"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>Virtuele Hyper-V-machines beoordelen met Azure Migrate server-evaluatie

Dit artikel laat u zien hoe u on-premises virtuele Hyper-V-machines kunt beoordelen met behulp van de Azure Migrate: Hulp programma Server Assessment.

[Azure migrate](migrate-services-overview.md) biedt een hub aan hulpprogram ma's waarmee u apps, infra structuur en werk belastingen op Microsoft Azure kunt detecteren, evalueren en migreren. De hub bevat Azure Migrate-hulpprogram ma's en ISV-aanbiedingen (Independent Software Vendor) van derden.



Deze zelf studie is de tweede in een serie die laat zien hoe u virtuele Hyper-V-machines kunt beoordelen en migreren naar Azure. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Stel een Azure Migrate project in.
> * Een Azure Migrate apparaat instellen en registreren.
> * Start doorlopende detectie van on-premises Vm's.
> * Gedetecteerde Vm's groeperen en de groep beoordelen.
> * Bekijk de evaluatie.

> [!NOTE]
> In zelf studies ziet u het eenvoudigste installatiepad voor een scenario, zodat u snel een haalbaarheids test kunt instellen. Zelf studies gebruiken waar mogelijk standaard opties en worden niet alle mogelijke instellingen en paden weer gegeven. Raadpleeg de artikelen met procedures voor gedetailleerde instructies.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.


## <a name="prerequisites"></a>Vereisten

- [Voltooi](tutorial-prepare-hyper-v.md) de eerste zelf studie in deze serie. Als dat niet het geval is, werken de instructies in deze zelf studie niet.
- In de eerste zelf studie hebt u het volgende gedaan:
    - [Azure-machtigingen instellen](tutorial-prepare-hyper-v.md#prepare-azure) voor Azure Migrate.
    - [Bereid Hyper-V-](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-assessment) clusters,-hosts en-vm's voor op evaluatie.

## <a name="set-up-an-azure-migrate-project"></a>Een Azure Migrate project instellen

1. Zoek in het Azure Portal **alle services**> naar **Azure migrate**.
2. Selecteer in de zoek resultaten **Azure migrate**.
3. Klik in **overzicht**onder **servers detecteren, evalueren en migreren**op **servers beoordelen en migreren**.

    ![Servers detecteren en beoordelen](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. Klik in **aan**de slag op **hulp middelen toevoegen**.
5. Selecteer uw Azure-abonnement op het tabblad **project migreren** en maak een resource groep als u er nog geen hebt.
6. Geef in **Project Details**de project naam en de regio op waarin u het project wilt maken.


    ![Een Azure Migrate-project maken](./media/tutorial-assess-hyper-v/migrate-project.png)

    U kunt in deze regio's een Azure Migrate-project maken.

    **Geografie** | **Regio**
    --- | ---
    Azië  | Azië - zuidoost
    Europa | Europa - noord of Europa - west
    Verenigd Koninkrijk |  UK-zuid of UK-west
    Verenigde Staten | VS-Oost, VS-West 2 of West-Centraal

    - De project regio wordt alleen gebruikt om de meta gegevens op te slaan die zijn verzameld van on-premises Vm's.
    - U kunt een andere Azure-doel regio selecteren wanneer u de virtuele machines migreert. Alle Azure-regio's worden ondersteund voor het migratie doel.

7. Klik op **Volgende**.
8. Selecteer in **hulp programma voor beoordeling selecteren**de optie **Azure migrate: Server evaluatie** > **volgende**.

    ![Een Azure Migrate-project maken](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. Selecteer in **hulp programma voor migratie selecteren** **de optie overs Laan een migratie hulpmiddel toevoegen voor nu** > **volgende**.
10. Controleer de instellingen in **hulp middelen voor beoordeling en toevoegen**en klik op **hulp middelen toevoegen**.
11. Wacht enkele minuten totdat het Azure Migrate project is geïmplementeerd. U wordt naar de project-pagina geleid. Als u het project niet ziet, kunt u het openen vanaf **servers** in het dash board van Azure Migrate.




## <a name="set-up-the-appliance-vm"></a>De toestel-VM instellen

Azure Migrate server-evaluatie voert een licht gewicht Hyper-V-VM-apparaat uit.

- Dit apparaat voert VM-detectie uit en verzendt meta gegevens en prestatie gegevens van de virtuele machine naar Azure Migrate: Server-evaluatie.
- Als u het apparaat wilt instellen, doet u het volgende:
    - Down load een gecomprimeerde Hyper-V VHD vanuit het Azure Portal.
    - Maak het apparaat en controleer of het verbinding kan maken met Azure Migrate server beoordeling.
    - Configureer het apparaat voor de eerste keer en registreer het bij het Azure Migrate-project.

### <a name="download-the-vhd"></a>De VHD downloaden

Down load de sjabloon voor de gezipte VHD voor het apparaat.

1. In **migratie doelen** > **servers** > Azuremigrate **: Server evaluatie**, klikt u op **ontdekken**.
2. **Zijn uw machines**in **Discover-computers** > gevirtualiseerde? Klik op **Ja, met Hyper-V**.
3. Klik op **downloaden** om het VHD-bestand te downloaden.

    ![Virtuele machine downloaden](./media/tutorial-assess-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Beveiliging controleren

Controleer of het gecomprimeerde bestand is beveiligd, voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.

2. Voer de volgende Power shell-opdracht uit om de hash voor het ZIP-bestand te genereren
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Gebruiksvoorbeeld: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v1.19.06.27.zip -Algorithm SHA256```

3.  Voor de toestel versie 2.19.07.30 moet de gegenereerde hash overeenkomen met deze instellingen.

  **Algoritme** | **Hash-waarde**
  --- | ---
  MD5 | d0a68e76ea24ba4e4a494c0dab95e90e
  SHA256 | 0551221d2a9de75c352c201ccc88f7f10e87e5df1ecda42bfd4ec6c8defc57c0



### <a name="create-the-appliance-vm"></a>De apparaat-VM maken

Importeer het gedownloade bestand en maak de virtuele machine.

1. Na het downloaden van het gecomprimeerde VHD-bestand naar de Hyper-V-host waarop de apparaat-VM wordt geplaatst, pakt u het zip-bestand uit.
    - In de geëxtraheerde locatie wordt het bestand uitgepakt naar een map met de naam **AzureMigrateAppliance_VersionNumber**.
    - Deze map bevat een submap, ook wel **AzureMigrateAppliance_VersionNumber**genoemd.
    - Deze submap bevat drie verdere submappen: **moment opnamen**, **virtuele harde schijven**en **virtual machines**.

2. Open Hyper-V-beheer. Klik in **acties**op **virtuele machine importeren**.

    ![VHD implementeren](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. Klik in de wizard virtuele machine importeren > **voordat u begint**op **volgende**.
3. Selecteer in **map zoeken**de map **virtual machines** . Klik op **Volgende**.
1. Klik in **virtuele machine selecteren**op **volgende**.
2. In **import type kiezen**klikt u op **de virtuele machine kopiëren (een nieuwe unieke id maken)** . Klik op **Volgende**.
3. Laat in **doel kiezen**de standaard instelling ongewijzigd. Klik op **Volgende**.
4. In **opslag mappen**, behoud de standaard instelling. Klik op **Volgende**.
5. Geef in **netwerk kiezen**de virtuele switch op die door de VM moet worden gebruikt. De switch heeft Internet connectiviteit nodig om gegevens naar Azure te verzenden.
6. Controleer de instellingen in **samen vatting**. Klik vervolgens op **volt ooien**.
7. Start de virtuele machine in Hyper-V-beheer > **virtual machines**.


### <a name="verify-appliance-access-to-azure"></a>Toestel toegang tot Azure controleren

Zorg ervoor dat de virtuele machine van het apparaat verbinding kan maken met [Azure-url's](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access).

### <a name="configure-the-appliance"></a>Het apparaat configureren

Het apparaat voor de eerste keer instellen.

1. Klik in Hyper-V-beheer > **virtual machines**met de rechter muisknop op de virtuele machine > **verbinding maken**.
2. Geef de taal, de tijd zone en het wacht woord op voor het apparaat.
3. Open een browser op een computer die verbinding kan maken met de virtuele machine en open de URL van de web-app **van het apparaat:*naam of IP-adres van https://-apparaat*: 44368**.

   U kunt de app ook vanuit het toestel bureau blad openen door te klikken op de snelkoppeling naar de app.
1. Ga als volgt te werk in de web-app > vereisten in te **stellen**:
    - **Licentie**: Accepteer de licentievoorwaarden en lees de informatie van derden.
    - **Connectiviteit**: De app controleert of de virtuele machine toegang heeft tot internet. Als de virtuele machine gebruikmaakt van een proxy:
      - Klik op **proxy-instellingen**en geef het proxy adres en de luister poort op in http://ProxyIPAddress het http://ProxyFQDN formulier of.
      - Geef referenties op als de proxy verificatie nodig heeft.
      - Alleen HTTP-proxy wordt ondersteund.
    - **Tijd synchronisatie**: Tijd is geverifieerd. De tijd op het apparaat moet zijn gesynchroniseerd met internet tijd zodat de machine detectie goed werkt.
    - **Updates installeren**: Azure Migrate server beoordeling controleert of de meest recente updates zijn geïnstalleerd op het apparaat.

### <a name="register-the-appliance-with-azure-migrate"></a>Het apparaat registreren bij Azure Migrate

1. Klik op **Aanmelden**. Als deze niet wordt weer gegeven, controleert u of de pop-upblokkering in de browser is uitgeschakeld.
2. Meld u aan met uw Azure-referenties op het tabblad Nieuw.
    - Meld u aan met uw gebruikers naam en wacht woord.
    - Aanmelden met een pincode wordt niet ondersteund.
3. Nadat u zich hebt aangemeld, gaat u terug naar de web-app.
4. Selecteer het abonnement waarin het Azure Migrate-project is gemaakt. Selecteer vervolgens het project.
5. Geef een naam op voor het apparaat. De naam moet alfanumeriek zijn met 14 tekens of minder.
6. Klik op **registreren**.


### <a name="delegate-credentials-for-smb-vhds"></a>Referenties voor SMB-Vhd's delegeren

Als u virtuele harde schijven uitvoert op Smb's, moet u de overdracht van referenties van het apparaat naar de Hyper-V-hosts inschakelen. Hiervoor moet u het volgende doen:

- U schakelt elke host in als gemachtigde voor het apparaat. U moet dit doen in de vorige zelf studie, wanneer u Hyper-V voor de evaluatie en migratie hebt voor bereid. U moet CredSSP [hand matig](tutorial-prepare-hyper-v.md#enable-credssp-on-hosts)instellen voor de hosts of door [het configuratie script van de vereisten voor Hyper-V](tutorial-prepare-hyper-v.md#hyper-v-prerequisites-configuration-script)uit te voeren.
- Schakel CredSSP-delegering in, zodat het Azure Migrate apparaat kan fungeren als de client, en de referenties voor een host delegeren.

Schakel op het apparaat als volgt in:

#### <a name="option-1"></a>Optie 1

Voer de volgende opdracht uit op de apparaat-VM. HyperVHost1/HyperVHost2 zijn voor beelden van hostnamen.

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
```

Voorbeeld: ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>Optie 2

U kunt dit ook doen in het Lokale groepsbeleidsobjecteditor op het apparaat:

1. Klik in de**computer configuratie**van het **lokale computer beleid** > op **Beheersjablonen** > delegering van**systeem** > **referenties**.
2. Dubbel klik op delegeren van **nieuwe referenties toestaan**en selecteer **ingeschakeld**.
3. Klik in **Opties**op **weer geven**en voeg elke Hyper-V-host die u wilt detecteren, toe aan de lijst met **wsman/** als voor voegsel.
4. Dubbel klik vervolgens bij het delegeren van **referenties**op toestaan van het delegeren **van nieuwe referenties met NTLM-Server verificatie**. Voeg nogmaals elke Hyper-V-host die u wilt detecteren, toe aan de lijst met **wsman/** als voor voegsel.

## <a name="start-continuous-discovery"></a>Continue detectie starten

Maak verbinding van het apparaat met Hyper-V-hosts of-clusters en start de VM-detectie.

1. Geef bij **gebruikers naam** en **wacht woord**de account referenties op die door het apparaat worden gebruikt voor het detecteren van vm's. Geef een beschrijvende naam op voor de referenties en klik op **Details opslaan**.
2. Klik op **host toevoegen**en geef de gegevens van de Hyper-V-host/het cluster op.
3. Klik op **valideren**. Na validatie wordt het aantal Vm's weer gegeven dat op elke host/het cluster kan worden gedetecteerd.
    - Als de validatie voor een host mislukt, controleert u de fout door de muis aanwijzer boven het pictogram in de kolom **status** te bewegen. Los problemen op en valideer opnieuw.
    - Als u hosts of clusters wilt verwijderen, selecteert u > **verwijderen**.
    - U kunt een specifieke host niet verwijderen uit een cluster. U kunt alleen het hele cluster verwijderen.
    - U kunt een cluster toevoegen, zelfs als er problemen zijn met specifieke hosts in het cluster.
4. Klik na validatie op **opslaan en start de detectie** om het detectie proces te starten.

De detectie wordt gestart. Het duurt ongeveer 15 minuten voor de meta gegevens van gedetecteerde Vm's die in de Azure Portal worden weer gegeven.

### <a name="verify-vms-in-the-portal"></a>VM's verifiëren in de portal

Nadat de detectie is voltooid, kunt u controleren of de virtuele machines in de portal worden weer gegeven.

1. Open het Azure Migrate dash board.
2. In **Azure migrate-servers** > **Azure migrate: Pagina Server** evaluatie klikt u op het pictogram dat het aantal voor **gedetecteerde servers**weergeeft.

## <a name="set-up-an-assessment"></a>Een evaluatie instellen

Er zijn twee soorten evaluaties die u kunt uitvoeren met behulp van Azure Migrate server-evaluatie.

**Onderzoek** | **Details** | **Gegevens**
--- | --- | ---
**Op basis van prestaties** | Evaluaties op basis van verzamelde prestatie gegevens | **Aanbevolen VM-grootte**: Op basis van gegevens van het CPU-en geheugen gebruik.<br/><br/> **Aanbevolen schijf type (Standard of Premium Managed disk)** : Op basis van de IOPS en door Voer van de on-premises schijven.
**Als on-premises** | Evaluaties op basis van on-premises grootte. | **Aanbevolen VM-grootte**: Op basis van de on-premises VM-grootte<br/><br> **Aanbevolen schijf type**: Op basis van de instelling voor het opslag type die u voor de evaluatie selecteert.



### <a name="run-an-assessment"></a>Een evaluatie uitvoeren

Voer een evaluatie als volgt uit:

1. Bekijk de [Aanbevolen procedures](best-practices-assessment.md) voor het maken van evaluaties.
2. In **servers** > Azuremigrate: **Server evaluatie**, klikt u op **evalueren**.

    ![Evalueren](./media/tutorial-assess-hyper-v/assess.png)

3. Geef in **servers beoordelen**een naam op voor de evaluatie.
4. Klik op **Alles weergeven** om de evaluatie-eigenschappen te controleren.

    ![Beoordelingseigenschappen](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. In **een groep selecteren of maken**selecteert u **nieuwe maken** en geeft u een groeps naam op. Een groep verzamelt een of meer Vm's samen voor evaluatie.
4. Selecteer op **computers toevoegen aan de groep**de optie vm's die u aan de groep wilt toevoegen.
5. Klik op **evaluatie maken** om de groep te maken en de evaluatie uit te voeren.

    ![Een evaluatie maken](./media/tutorial-assess-hyper-v/assessment-create.png)

6. Nadat de evaluatie is gemaakt, bekijkt u deze in **servers** > **Azure migrate: Server evaluatie**.
7. Klik op **Evaluatie exporteren** om deze te downloaden als een Excel-bestand.


## <a name="review-an-assessment"></a>Een evaluatie controleren

Een evaluatie beschrijft:

- **Azure**-gereedheid: Of Vm's geschikt zijn voor migratie naar Azure.
- **Schatting maandelijkse kosten**: De geschatte maandelijkse reken-en opslag kosten voor het uitvoeren van de virtuele machines in Azure.
- **Schatting maandelijkse opslag kosten**: Geschatte kosten voor schijf opslag na migratie.


### <a name="view-an-assessment"></a>Een evaluatie weer geven

1. In **migratie doelen** >  **servers** > Azuremigrate **: Server evaluatie**, kliktu op evaluaties.
2. Klik in **beoordelingen**op een evaluatie om deze te openen.

    ![Evaluatie-overzicht](./media/tutorial-assess-hyper-v/assessment-summary.png)


### <a name="review-azure-readiness"></a>Azure-gereedheid controleren

1. Controleer in **Azure Readiness**of de vm's gereed zijn voor migratie naar Azure.
2. Controleer de status van de virtuele machine:
    - **Gereed voor Azure**: Azure Migrate raadt een VM-grootte en schattingen voor de kosten aan voor Vm's in de evaluatie.
    - **Klaar met voor waarden**: Geeft problemen en voorgestelde herbemiddeling weer.
    - **Niet gereed voor Azure**: Geeft problemen en voorgestelde herbemiddeling weer.
    - **Gereedheid onbekend**: Wordt gebruikt wanneer Azure Migrate de gereedheid niet kan beoordelen door problemen met de beschik baarheid van gegevens.

2. Klik op de status van een **Azure** -gereedheid. U kunt details van de VM-gereedheid bekijken en inzoomen op de details van de virtuele machine, met inbegrip van compute-, opslag-en netwerk instellingen.

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

![Betrouwbaarheidswaardering](./media/tutorial-assess-hyper-v/confidence-rating.png)

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

Ga door naar de derde zelf studie in de reeks om te leren hoe u virtuele Hyper-V-machines naar Azure migreert met Azure Migrate server migratie.

> [!div class="nextstepaction"]
> [Virtuele Hyper-V-machines migreren](./tutorial-migrate-hyper-v.md)
