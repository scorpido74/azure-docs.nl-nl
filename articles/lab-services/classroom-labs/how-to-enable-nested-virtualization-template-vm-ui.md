---
title: Geneste virtualisatie inschakelen op een sjabloon-VM in Azure Lab Services (UI) | Microsoft Documenten
description: Meer informatie over het maken van een sjabloon-vm met meerdere VM's erin.  Met andere woorden, schakel geneste virtualisatie in op een sjabloon-VM in Azure Lab Services.
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 10f8d2760474631fbcabac4d66139aedf4c7560c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503073"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-manually"></a>Geneste virtualisatie inschakelen op een virtuele sjabloonmachine in Azure Lab Services handmatig

Geneste virtualisatie stelt u in staat om een multi-VM-omgeving te maken in de virtuele machine van een lab. Als u de sjabloon publiceert, krijgt elke gebruiker in het lab een virtuele machine die is ingesteld met meerdere VM's erin.  Zie [Geneste virtualisatie inschakelen op een virtuele sjabloon in Azure Lab Services](how-to-enable-nested-virtualization-template-vm.md)voor meer informatie over geneste virtualisatie en Azure Lab Services.

In dit artikel wordt besproken hoe u geneste virtualisatie instelt op een sjabloonmachine in Azure Lab Services met behulp van Windows-rollen en -hulpprogramma's rechtstreeks.  Er zijn een paar dingen nodig om een klasse in staat te stellen geneste virtualisatie te gebruiken.  In de onderstaande stappen wordt beschreven hoe u handmatig een labservices-machinesjabloon instellen met Hyper-V.  Stappen zijn bedoeld voor Windows Server 2016 of Windows Server 2019.  

>[!IMPORTANT]
>Selecteer **Groot (geneste virtualisatie)** of **Medium (geneste virtualisatie)** voor de grootte van de virtuele machine bij het maken van het lab.  Geneste virtualisatie werkt anders niet.  

## <a name="enable-hyper-v-role"></a>Hyper-V-rol inschakelen

In de volgende stappen worden acties beschreven die nodig zijn om Hyper-V op Windows Server in te schakelen met Serverbeheer.  Zodra de installatie succesvol is, is Hyper-V-manager beschikbaar om virtuele clientmachines toe te voegen, aan te passen en te verwijderen.

1. Klik in **Serverbeheer**op de dashboardpagina op **Rollen en onderdelen toevoegen**.
2. Klik op de pagina **Voordat u begint** op **Volgende**.
3. Bewaar op de pagina **Installatietype selecteren** de standaardselectie van installatie op basis van rollen of functies en klik vervolgens op **Volgende**.
4. Selecteer op de pagina **Doelserver selecteren** de optie Een server selecteren in de servergroep.   De huidige server wordt al geselecteerd.  Klik op Volgende.
5. Op de **Serverfuncties selecteren** pagina **Hyper-V**.  
6. De pop-up van de **wizard Rollen en functies toevoegen** wordt weergegeven.  Selecteer **Beheergereedschappen opnemen (indien van toepassing)**.  Klik op de knop **Onderdelen toevoegen.**
7. Klik op de pagina **Serverfuncties selecteren** op **Volgende**.
8. Klik **op**de pagina Onderdelen selecteren op **Volgende**.
9. Klik op de pagina **Hyper-V** op **Volgende**.
10. Accepteer op de pagina **Virtuele switches maken** de standaardinstellingen en klik op **Volgende**.
11. Accepteer op de pagina **Virtual Machine Migration** de standaardinstellingen en klik op **Volgende**.
12. Accepteer op de pagina **Standaardopslag** de standaardinstellingen en klik op **Volgende**.
13. Selecteer op de pagina **Installatieselecties bevestigen** de optie **De doelserver automatisch opnieuw starten indien nodig**.
14. Klik op **Ja**wanneer de **wizard Rollen en onderdelen toevoegen** wordt weergegeven.
15. Klik **op Installeren**.
16. Wacht tot de **voortgangspagina Installatie** aangeeft dat de functie Hyper-V is voltooid.  De machine kan in het midden van de installatie opnieuw worden opgestart.
17. Klik op **Sluiten**.

## <a name="enable-dhcp-role"></a>DHCP-rol inschakelen

Elke Virtuele Hyper-V-client die is gemaakt, heeft een IP-adres in het NAT-netwerk nodig.  We maken het NAT-netwerk later.  Een manier om IP-adressen toe te wijzen is het instellen van de host, in dit geval de sjabloon voor de virtuele machine van het lab, als een DHCP-server.  Hieronder vindt u de stappen die nodig zijn om de DHCP-rol in te schakelen.

1. Klik in **Serverbeheer**op de **dashboardpagina** op **Rollen en onderdelen toevoegen**.
2. Klik op de pagina **Voordat u begint** op **Volgende**.
3. Op de **installatietype selecteren** pagina **installatie op basis van rollen of op basis van functie** en klik vervolgens op **volgende**.
4. Selecteer op de pagina **Doelserver selecteren** de huidige server in de servergroep en klik op **Volgende**.
5. Selecteer **DHCP Server**op de pagina **Serverrollen selecteren** .  
6. De pop-up van de **wizard Rollen en functies toevoegen** wordt weergegeven.  Selecteer **Beheergereedschappen opnemen (indien van toepassing)**.  Klik op **functies toevoegen**.

    >[!NOTE]
    >Mogelijk ziet u een validatiefout waarin staat dat er geen statische IP-adressen zijn gevonden.  Deze waarschuwing kan worden genegeerd voor ons scenario.

7. Klik op de pagina **Serverfuncties selecteren** op **Volgende**.
8. Klik **op** de pagina Onderdelen selecteren op **Volgende**.
9. Klik op de pagina **DHCP Server** op **Volgende**.
10. Klik op de pagina **Installatieselecties bevestigen** op **Installeren**.
11. Wacht tot de **pagina Voortgang van** de installatie aangeeft dat de DHCP-rol is voltooid.
12. Klik op Sluiten.

## <a name="enable-routing-and-remote-access-role"></a>Rol Routering en rastoegang inschakelen

1. Klik in **Serverbeheer**op de **dashboardpagina** op **Rollen en onderdelen toevoegen**.
2. Klik op de pagina **Voordat u begint** op **Volgende**.
3. Op de **installatietype selecteren** pagina **installatie op basis van rollen of op basis van functie** en klik vervolgens op **volgende**.
4. Selecteer op de pagina **Doelserver selecteren** de huidige server in de servergroep en klik op **Volgende**.
5. Selecteer **rastoegang**op de pagina **Serverrollen selecteren** . Klik op **OK**.
6. Klik **op** de pagina Onderdelen selecteren op **Volgende**.
7. Klik op de pagina **Rastoegang** op **Volgende**.
8. Selecteer Op de pagina **Role Services** de optie **Routering**.
9. De pop-up van de **wizard Rollen en functies toevoegen** wordt weergegeven.  Selecteer **Beheergereedschappen opnemen (indien van toepassing)**.  Klik op **functies toevoegen**.
10. Klik op **Volgende**.
11. Klik op de pagina **Webserverfunctie (IIS)** op **Volgende**.
12. Klik op de pagina **Functieservices selecteren** op **Volgende**.
13. Klik op de pagina **Installatieselecties bevestigen** op **Installeren**.
14. Wacht tot de **voortgangspagina Installatie** aangeeft dat de functie Rastoegang is voltooid.  
15. Klik op **Sluiten**.

## <a name="create-virtual-nat-network"></a>Virtueel NAT-netwerk maken

Nu alle benodigde rollen zijn geïnstalleerd, is het tijd om het NAT-netwerk te maken.  Het creatieproces omvat het maken van een switch en het NAT-netwerk zelf.  Een NAT-netwerk (network address translation) kent een openbaar IP-adres toe aan een groep VM's op een privénetwerk om connectiviteit met het internet mogelijk te maken.  In ons geval zal de groep van particuliere VM's de geneste VM's zijn.  Met het NAT-netwerk kunnen de geneste VM's met elkaar communiceren. Een switch is een netwerkapparaat dat het ontvangen en routeren van verkeer in een netwerk afhandelt.

### <a name="create-a-new-virtual-switch"></a>Een nieuwe virtuele switch maken

1. Open **Hyper-V Manager** vanuit Windows-beheerprogramma's.
2. Selecteer de huidige server in het navigatiemenu aan de linkerkant.
3. Klik **op Virtuele Schakelbeheer...** in het menu **Acties** aan de rechterkant van de **Hyper-V Manager**.
4. Selecteer **in** de pop-up Virtual Switch Manager de optie **Intern** voor het type switch dat u wilt maken.  Klik **op Virtuele switch maken**.
5. Voor de nieuw gemaakte virtuele schakelaar, stel de naam op iets onvergetelijks.  Voor dit voorbeeld gebruiken we 'LabServicesSwitch'.  Klik op **OK**.
6. Er wordt een nieuwe netwerkadapter gemaakt.  De naam zal vergelijkbaar zijn met 'vEthernet (LabServicesSwitch)'.  Als u het **Configuratiescherm wilt**openen, klikt u op **Netwerk en internet**en klikt u op **Netwerkstatus en taken weergeven**.  Klik aan de linkerkant op **Adapterinstellingen wijzigen**.

### <a name="create-a-nat-network"></a>Een NAT-netwerk maken

1. Open het hulpprogramma **Routering en Rastoegang** vanuit Windows-systeembeheer.
2. Selecteer de lokale server op de linkernavigatiepagina.
3. Kies **Actie** -> **Voorstel en Schakel routering en rastoegang in.**
4. Klik op **Volgende**wanneer **de wizard Routering en rastoegangsserver setup** wordt weergegeven.
5. Selecteer **op** de pagina Configuratie de **NAT-configuratie (Network address translation).**  Klik op **Volgende**.

    >[!WARNING]
    >Kies niet voor de optie 'Virtual private network (VPN) access en NAT'.

6. Kies op de pagina **NAT Internetverbinding** 'Ethernet'.  Kies niet voor de 'vEthernet (LabServicesSwitch)' verbinding die we hebben gemaakt in Hyper-V Manager. Klik op **Volgende**.
7. Klik op **Voltooien** op de laatste pagina van de wizard.
8. Wanneer het dialoogvenster **De service starten** wordt weergegeven, klikt u op Service **starten**.
9. Wacht tot de service is gestart.

## <a name="update-network-adapter-settings"></a>Instellingen voor netwerkadapters bijwerken

De netwerkadapter wordt gekoppeld aan het IP-adres dat wordt gebruikt voor het standaard-gateway-IP-adres voor het nat-netwerk dat eerder is gemaakt.  In dit voorbeeld maken we een IP-adres van 192.168.0.1 met een subnetmasker van 255.255.255.0.  We zullen gebruik maken van de virtuele schakelaar eerder gemaakt.

1. Open het **Configuratiescherm**, klik op **Netwerk en internet**, klik op **Netwerkstatus en taken weergeven**.
2. Klik aan de linkerkant op **Adapterinstellingen wijzigen**.  
3. Dubbelklik in het venster **Netwerkverbindingen** op 'vEthernet (LabServicesSwitch)' om het dialoogvenster **statusdetails van vEthernet (LabServicesSwitch)** weer te geven.
4. Klik op de knop **Eigenschappen.**
5. Selecteer het item **Internet Protocol Versie 4 (TCP/IPv4)** en klik op de knop **Eigenschappen.**
6. Selecteer in het dialoogvenster Eigenschappen van **Internet Protocol Versie 4 (TCP/IPv4)** de optie Het volgende **IP-adres gebruiken**.  Voer voor het ip-adres 192.168.0.1 in. Voer voor het subnetmasker 255.255.255.0 in.  Laat de standaardgateway leeg.  Laat de DNS-servers ook leeg.

    >[!NOTE]
    > Ons assortiment voor ons NAT-netwerk zal, in CIDR-notatie, 192.168.0.0/24 zijn.  Hierdoor ontstaat een reeks bruikbare IP-adressen van 192.168.0.1 tot 192.168.0.254.  Volgens de conventie hebben gateways het eerste IP-adres in een subnetbereik.

7. Klik op OK.

## <a name="create-dhcp-scope"></a>DHCP-bereik maken

De volgende stappen zijn instructies om DHCP-bereik toe te voegen.  In dit artikel is ons NAT-netwerk 192.168.0.0/24 in CIDR-notatie.  Hierdoor ontstaat een reeks bruikbare IP-adressen van 192.168.0.1 tot 192.168.0.254.  Het gemaakte bereik moet zich in dat bereik van bruikbare adressen bevinden, met uitzondering van het IP-adres dat eerder is gemaakt.

1. Open **Systeembeheer en** open het **DHCP-beheerhulpprogramma.**
2. Vouw in het **DHCP-gereedschap** het knooppunt voor de huidige server uit en selecteer **IPv4**.
3. Kies In het menu Actie de optie **Nieuwe scope...**
4. Wanneer de **wizard Nieuw bereik** wordt weergegeven, klikt u op **Volgende** op **de** welkomstpagina.
5. Voer op de pagina **Scope Name** 'LabServicesDhcpScope' of iets anders dat memorabel is voor de naam in.  Klik op **Volgende**.
6. Voer op de pagina **IP-adresbereik** de volgende waarden in.

    - 192.168.0.100 voor het ip-adres start
    - 192.168.0.200 voor het ip-adres aan het einde
    - 24 voor de lengte
    - 255.255.255.0 voor het Subnetmasker

7. Klik op **Volgende**.
8. Klik op de pagina **Uitsluitingen en Vertraging toevoegen** op **Volgende**.
9. Klik op de pagina **Huurduur** op **Volgende**.
10. Selecteer op de pagina **DHCP-opties configureren** de optie **Ja, ik wil deze opties nu configureren.** Klik op **Volgende**.
11. Op de **router (standaardgateway)**
12. Voeg 192.168.0.1 toe, indien nog niet gedaan. Klik op **Volgende**.
13. Voeg op de pagina **Domain Name and DNS Servers** 168.63.129.16 toe als IP-adres van de DNS-server, als dit nog niet is gedaan.  168.63.129.16 is het IP-adres voor een statische DNS-server in Azure. Klik op **Volgende**.
14. Klik op de pagina **WINS-servers** op **Volgende**.
15. Eén pagina **Bereik activeren,** **selecteer Ja, ik wil dit bereik nu activeren.**  Klik op **Volgende**.
16. Klik op de wizard **Nieuw bereik voltooien** op **Voltooien**.

## <a name="conclusion"></a>Conclusie

Nu is uw sjabloonmachine klaar om virtuele Hyper-V-machines te maken.   Zie [Een virtuele machine maken in Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) voor instructies over het maken van virtuele hyperv-virtuele machines.  Zie ook het [Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/) om beschikbare besturingssystemen en software te bekijken.

## <a name="next-steps"></a>Volgende stappen

De volgende stappen zijn gebruikelijk voor het opzetten van een lab.

- [Gebruikers toevoegen](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quotum instellen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Een schema instellen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Koppelingen naar e-mailregistratie naar studenten](how-to-configure-student-usage.md#send-invitations-to-users)