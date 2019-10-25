---
title: De configuratie server voor VMware-nood herstel implementeren met Azure Site Recovery | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een configuratie server implementeert voor VMware-nood herstel met Azure Site Recovery
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/15/2019
ms.author: ramamill
ms.openlocfilehash: f5fe49130742d116775b75f17c726b56150c574f
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792356"
---
# <a name="deploy-a-configuration-server"></a>Een configuratieserver implementeren

U implementeert een on-premises configuratie server wanneer u [Azure site Recovery](site-recovery-overview.md) gebruikt voor herstel na nood gevallen van virtuele VMware-machines en fysieke servers naar Azure. De configuratie server coördineert de communicatie tussen on-premises VMware en Azure. Het beheert ook de gegevens replicatie. Dit artikel begeleidt u stapsgewijs door de stappen die nodig zijn voor het implementeren van de configuratie server wanneer u virtuele VMware-machines naar Azure repliceert. [Volg dit artikel](physical-azure-set-up-source.md) als u een configuratie server wilt instellen voor de replicatie van de fysieke server.

> [!TIP]
> [Hier](vmware-azure-architecture.md)vindt u meer informatie over de functie van de configuratie server als onderdeel van de Azure site Recovery architectuur.

## <a name="deployment-of-configuration-server-through-ova-template"></a>Implementatie van configuratie server via eicellen-sjabloon

Configuratie server moet worden ingesteld als een Maxi maal beschik bare VMware-VM met bepaalde minimale vereisten voor hardware en grootte. Voor een handige en eenvoudige implementatie biedt Site Recovery een download bare eicellen-sjabloon (open Virtualization Application) voor het instellen van de configuratie server die voldoet aan alle voorgeschreven vereisten die hieronder worden vermeld.

## <a name="prerequisites"></a>Vereisten

De minimale hardwarevereisten voor een configuratie server worden in de volgende secties samenvatten.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Machtigings vereisten voor Azure Active Directory

U hebt een gebruiker met **een van de volgende** machtigingen ingesteld in AAD (Azure Active Directory) om de configuratie server te registreren bij Azure site Recovery Services.

1. De gebruiker moet de rol ' toepassings ontwikkelaar ' hebben om een toepassing te maken.
    - Meld u aan bij Azure Portal om te verifiëren</br>
    - Ga naar Azure Active Directory >-rollen en-beheerders</br>
    - Controleer of de rol ' toepassings ontwikkelaar ' is toegewezen aan de gebruiker. Als dat niet het geval is, gebruik dan een gebruiker met deze machtiging of neem contact op met [de beheerder om de machtiging in te scha kelen](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal#assign-roles).
    
2. Als de rol ' toepassings ontwikkelaar ' niet kan worden toegewezen, moet u ervoor zorgen dat de vlag ' gebruiker kan toepassing registreren ' is ingesteld op True als de gebruiker een identiteit wil maken. Om bovenstaande machtigingen in te scha kelen,
    - Meld u aan bij Azure Portal
    - Ga naar Azure Active Directory > gebruikers instellingen
    - Onder * * App-registraties "kunnen gebruikers toepassingen registreren" worden gekozen als "ja".

      ![AAD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> Active Directory Federation Services (ADFS) wordt **niet ondersteund**. Gebruik een account dat wordt beheerd via [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis).

## <a name="download-the-template"></a>De sjabloon downloaden

1. Ga in de kluis naar **Infrastructuur voorbereiden** > **Bron**.
2. Selecteer **+Configuratieserver** in **Bron voorbereiden**.
3. Controleer in **Server toevoegen** of **Configuratieserver voor VMware** wordt weergegeven in **Servertype**.
4. Down load de sjabloon voor open Virtualization Application (eicellen) voor de configuratie server.

   > [!TIP]
   >U kunt de nieuwste versie van de configuratie Server sjabloon ook rechtstreeks downloaden vanuit het [micro soft Download centrum](https://aka.ms/asrconfigurationserver).

> [!NOTE]
> De licentie voor de eicellen-sjabloon is een evaluatie licentie die gedurende 180 dagen geldig is. Na deze periode moet de klant de Windows activeren met een aangeschafte licentie.

## <a name="import-the-template-in-vmware"></a>De sjabloon in VMware importeren

1. Meld u bij de VMware vCenter-server of vSphere ESXi-host met behulp van de VMWare vSphere-client.
2. Selecteer in het menu **Bestand** de optie **OVF-sjabloon implementeren** om de wizard voor het implementeren van OVF-sjablonen te starten.

     ![OVF-sjabloon](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. In **bron selecteren voert u**de locatie van de gedownloade OVF in.
4. Selecteer **volgende**onder **Details weer geven**.
5. Accepteer de standaard instellingen in **naam en map selecteren** en **configuratie selecteren**.
6. Selecteer in **Opslag selecteren** **Thick Provision Eager Zeroed** in **Indeling virtuele schijf selecteren**. Gebruik van thin provisioning optie kan de prestaties van de configuratie server beïnvloeden.
7. Accepteer de standaardinstellingen in de rest van de wizardpagina’s.
8. In **Klaar om te voltooien**:

    * Selecteer **Inschakelen na de implementatie** > **Voltooien** om de VM in te stellen met de standaardinstellingen.

    * Als u een extra netwerk interface wilt toevoegen, schakelt u het selectie vakje **na de implementatie**uit en selecteert u vervolgens **volt ooien**. Standaard wordt de configuratieserversjabloon met één NIC geïmplementeerd. Na de implementatie kunt u meer NIC’s toevoegen.

> [!IMPORTANT]
> Wijzig geen resource configuraties (geheugen/kernen/CPU-beperking), Wijzig/verwijder geïnstalleerde services of bestanden op de configuratie server na de implementatie. Dit heeft gevolgen voor de registratie van de configuratie server met Azure-Services en de prestaties van de configuratie server.

## <a name="add-an-additional-adapter"></a>Een extra adapter toevoegen

> [!NOTE]
> Er zijn twee Nic's vereist als u van plan bent om de IP-adressen van de bron machines in de failover te bewaren en later een failback naar on-premises uit te laten gaan. Er wordt één NIC verbonden met de bron machines en de andere NIC wordt gebruikt voor Azure-connectiviteit.

Als u een extra NIC aan de configuratie server wilt toevoegen, voegt u deze toe voordat u de server in de kluis registreert. Het toevoegen van extra adapters wordt niet ondersteund na registratie.

1. Klik in de vSphere Client-inventaris met de rechtermuisknop op de VM en selecteer **Instellingen bewerken**.
2. Selecteer **Toevoegen** > **Ethernet-adapter** bij **Hardware**. Selecteer vervolgens **Volgende**.
3. Selecteer een adaptertype en een netwerk.
4. Als u de virtuele NIC wilt verbinden wanneer de VM is ingeschakeld, selecteert u **verbinden bij inschakelen**. Selecteer vervolgens **volgende** > **volt ooien** > **OK**.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>De configuratie server registreren bij Azure Site Recovery Services

1. Schakel de VM in vanuit de VMWare vSphere Client-console.
2. De VM wordt opgestart in een Windows Server 2016-installatie-ervaring. Accepteer de gebruiksrechtovereenkomst en voer een Administrator-wachtwoord in.
3. Meld u nadat de installatie is voltooid bij de virtuele machine aan als de administrator.
4. De eerste keer dat u zich aanmeldt, binnen een paar seconden wordt het configuratie hulpprogramma voor de Azure Site Recovery gestart.
5. Voer een naam in die wordt gebruikt voor het registreren van de configuratieserver bij Site Recovery. Selecteer vervolgens **Volgende**.
6. Het hulpprogramma controleert of de VM verbinding kan maken met Azure. Nadat de verbinding tot stand is gebracht, selecteert u **Aanmelden** om u aan te melden bij uw Azure-abonnement.</br>
    a. De referenties moeten toegang hebben tot de kluis waarin u de configuratieserver wilt registreren.</br>
    b. Zorg ervoor dat het gekozen gebruikers account machtigingen heeft voor het maken van een toepassing in Azure. Als u de vereiste machtigingen wilt inschakelen, volgt u de richt lijnen die [hier](#azure-active-directory-permission-requirements)worden gegeven.
7. Het hulpprogramma voert enkele configuratietaken uit en start opnieuw op.
8. Meld u opnieuw aan bij de machine. De wizard Configuratie Server beheer wordt in een paar seconden **automatisch** gestart.

### <a name="configure-settings"></a>Instellingen configureren

1. Selecteer in de wizard Configuratie Server beheer de optie **connectiviteit instellen**. Selecteer in de vervolg keuzelijsten eerst de NIC die de ingebouwde proces server gebruikt voor detectie en push-installatie van Mobility service op bron machines, en selecteer vervolgens de NIC die door de configuratie server wordt gebruikt voor connectiviteit met Azure. Selecteer vervolgens **Opslaan**. U kunt deze instelling niet wijzigen nadat deze is geconfigureerd. Het wordt ten zeerste aangeraden het IP-adres van een configuratie server niet te wijzigen. Zorg ervoor dat IP-adressen die zijn toegewezen aan de configuratie server statisch IP-adres zijn en geen DHCP-IP.
2. Meld u in **Recovery Services kluis selecteren**aan Microsoft Azure met de referenties die worden gebruikt in **stap 6** van '[Configuratie server registreren bij Azure site Recovery Services](#register-the-configuration-server-with-azure-site-recovery-services)'.
3. Nadat u zich hebt aangemeld, selecteert u uw Azure-abonnement en de relevante resource groep en kluis.

    > [!NOTE]
    > Zodra het REGI ster is geregistreerd, is er geen flexibiliteit om de Recovery Services-kluis te wijzigen.
    > Het wijzigen van de Recovery Services-kluis vereist een ontkoppeling van de configuratie server van de huidige kluis en de replicatie van alle beveiligde virtuele machines onder de configuratie server is gestopt. [Meer](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) informatie.

4. In **Software van derden installeren**

    |Scenario   |Stappen om te volgen  |
    |---------|---------|
    |Kan ik & MySQL hand matig installeren?     |  Ja. Down load MySQL-toepassing & plaats deze in de map **C:\Temp\ASRSetup**en installeer hand matig. Wanneer u nu akkoord gaat met de voor waarden > klikt u op **downloaden en installeren**, blijkt dat de portal *al is geïnstalleerd*. U kunt door gaan met de volgende stap.       |
    |Kan ik voor komen dat MySQL online worden gedownload?     |   Ja. Plaats uw MySQL-installatie toepassing in de map **C:\Temp\ASRSetup**. Ga akkoord met de voor waarden > Klik op **downloaden en installeren**. in de portal wordt het installatie programma gebruikt dat door u is toegevoegd en de toepassing wordt geïnstalleerd. U kunt door gaan met de volgende stap na de installatie.    |
    |Ik wil graag downloaden & MySQL installeren via Azure Site Recovery     |  Ga akkoord met de gebruiksrecht overeenkomst & klik op **downloaden en installeren**. U kunt vervolgens door gaan met de volgende stap na de installatie.       |

5. In **De configuratie van het apparaat valideren** worden de vereisten gecontroleerd voordat u doorgaat.
6. In **vCenter Server vSphere/ESXi-server configureren** voert u de FQDN of het IP-adres van de vCenter-server, of vSphere-host, in waar de VM's die u wilt repliceren zich bevinden. Voer de poort in waarop de server luistert. Voer een beschrijvende naam in voor de VMware-server in de kluis.
7. Voer referenties in die door de configuratieserver moeten worden gebruikt voor verbinding met de VMware-server. Site Recovery gebruikt deze referenties voor het automatisch detecteren van VMware-VM’s die beschikbaar zijn voor replicatie. Selecteer **toevoegen**en vervolgens **door gaan**. De referenties die u hier opgeeft, worden lokaal opgeslagen.
8. Voer in **referenties voor virtuele machine configureren**de gebruikers naam en het wacht woord van de virtuele machines in om de Mobility-service automatisch te installeren tijdens de replicatie. Voor **Windows** -computers heeft het account lokale beheerders rechten nodig voor de computers die u wilt repliceren. Voor **Linux**geeft u Details voor het hoofd account op.
9. Selecteer **Configuratie voltooien** om de registratie te voltooien.
10. Nadat de registratie is voltooid, opent u Azure Portal, controleert u of de configuratie server en de VMware-Server worden vermeld op **Recovery Services kluis** >   > **site Recovery infra structuur** **beheren** > **configuratie Servers**.

## <a name="upgrade-the-configuration-server"></a>De configuratie Server upgraden

Volg deze [stappen](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)om de configuratie server bij te werken naar de meest recente versie. Ga naar [service-update beheer](service-updates-how-to.md)voor gedetailleerde instructies voor het upgraden van alle site Recovery onderdelen.

## <a name="manage-the-configuration-server"></a>De configuratieserver beheren

Zorg ervoor dat het IP-adres van de configuratie server niet verandert nadat de configuratie server is geregistreerd in een kluis om onderbrekingen van de replicatie te voor komen. [Hier](vmware-azure-manage-configuration-server.md)vindt u meer informatie over algemene beheer taken voor configuratie servers.

## <a name="faq"></a>Veelgestelde vragen

1. Hoe lang is de licentie die is ingevoerd op de configuratie server die via OVF is geïmplementeerd, geldig? Wat gebeurt er als ik de licentie niet opnieuw Activeer?

    De licentie voor de eicellen-sjabloon is een evaluatie licentie die gedurende 180 dagen geldig is. Voordat het verloopt, moet u de licentie activeren. Anders kan de configuratie server worden afgesloten en kan dit leiden tot replicatie activiteiten. Raadpleeg het artikel over het beheren van de [Configuratie Server licentie](vmware-azure-manage-configuration-server.md#update-windows-license)voor meer informatie.

2. Kan ik de virtuele machine, waarbij de configuratie server is geïnstalleerd, voor verschillende doel einden gebruiken?

    **Nee**, we raden u aan de virtuele machine te gebruiken voor uitsluitend het doel van de configuratie server. Zorg ervoor dat u alle specificaties volgt die worden vermeld in [vereisten](#prerequisites) voor efficiënt beheer van herstel na nood gevallen.
3. Kan ik de kluis die al is geregistreerd op de configuratie server wijzigen met een nieuwe kluis?

    **Nee**, zodra een kluis is geregistreerd bij de configuratie server, kan deze niet meer worden gewijzigd.
4. Kan ik dezelfde configuratie server gebruiken voor het beveiligen van fysieke en virtuele machines?

    **Ja**, dezelfde configuratie server kan worden gebruikt voor het repliceren van fysieke en virtuele machines. De fysieke machine kan echter alleen worden teruggezet naar een VMware-VM.
5. Wat is het doel van een configuratie server en waar wordt deze gebruikt?

    Raadpleeg de [replicatie architectuur van VMware naar Azure](vmware-azure-architecture.md) voor meer informatie over de configuratie server en de functionaliteit ervan.
6. Waar vind ik de nieuwste versie van de configuratie server?

    Zie [de configuratie Server upgraden](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)voor stappen om de configuratie server via de portal bij te werken. Meer informatie over het upgraden van alle Site Recovery onderdelen vindt u [hier](https://aka.ms/asr_how_to_upgrade).
7. Waar kan ik de wachtwoordzin voor de configuratie server downloaden?

    Raadpleeg [dit artikel](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) voor het downloaden van de wachtwoordzin.
8. Kan ik de wachtwoordzin wijzigen?

    **Nee**, u wordt **ten zeerste aangeraden de wachtwoordzin van de configuratie server niet te wijzigen** . Wijziging in de wachtwoordzin verbreekt de replicatie van beveiligde machines en leidt tot een kritieke status.
9. Waar kan ik een kluis registratie sleutel downloaden?

    In de **Recovery Services kluis** **beheert**u  > -**site Recovery infra structuur** > **configuratie servers**. In servers selecteert u **registratie sleutel downloaden** om het bestand met kluis referenties te downloaden.
10. Kan ik een bestaande configuratie server klonen en gebruiken voor replicatie-indeling?

    **Nee**, het gebruik van een gekloond configuratie Server onderdeel wordt niet ondersteund. De kloon van de scale-out proces server is ook een niet-ondersteund scenario. Het klonen van Site Recovery onderdelen heeft invloed op voortdurende replicaties.

11. Kan ik het IP-adres van de configuratie server wijzigen?

    **Nee**, het wordt ten zeerste aanbevolen het IP-adres van een configuratie server niet te wijzigen. Zorg ervoor dat alle IP-adressen die zijn toegewezen aan de configuratie server statische IP-adressen zijn en geen DHCP Ip's.
12. Kan ik een configuratie server instellen op Azure?

    Het is raadzaam om de configuratie server in te stellen op een on-premises omgeving met een directe line-of-Insight-versie en om latentie van gegevens overdracht te minimaliseren. U kunt geplande back-ups maken van configuratie server voor [failback-doel einden](vmware-azure-manage-configuration-server.md#failback-requirements).

Raadpleeg de [documentatie over veelgestelde vragen over Configuration Server](vmware-azure-common-questions.md#configuration-server) voor meer informatie over de configuratie server.

## <a name="troubleshoot-deployment-issues"></a>Oplossen van implementatieproblemen

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Volgende stappen

Stel herstel na nood gevallen van [virtuele VMware-machines](vmware-azure-tutorial.md) in op Azure.
