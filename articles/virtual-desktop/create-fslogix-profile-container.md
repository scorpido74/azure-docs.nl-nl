---
title: FSLogix-profielcontainers NetApp Windows Virtual Desktop - Azure
description: Een FSLogix-profielcontainer maken met Azure NetApp-bestanden in Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 149fb5818ee360c7333997655ea9eb8d7ded346c
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270890"
---
# <a name="create-an-fslogix-profile-container-for-a-host-pool-using-azure-netapp-files"></a>Een FSLogix-profielcontainer maken voor een hostgroep met Azure NetApp-bestanden

We raden u aan fslogix-profielcontainers te gebruiken als gebruikersprofieloplossing voor de [Windows Virtual Desktop-service.](overview.md) FSLogix-profielcontainers slaan een volledig gebruikersprofiel op in één container en zijn ontworpen om profielen te roamen in niet-permanente externe computeromgevingen zoals Windows Virtual Desktop. Wanneer u zich aanmeldt, wordt de container dynamisch gekoppeld aan de computeromgeving met behulp van een lokaal ondersteunde virtuele harde schijf (VHD) en Hyper-V virtuele harde schijf (VHDX). Deze geavanceerde filter-driver technologieën zorgen ervoor dat het gebruikersprofiel onmiddellijk beschikbaar is en in het systeem wordt weergegeven, precies zoals een lokaal gebruikersprofiel. Zie [FSLogix-profielcontainers en Azure-bestanden](fslogix-containers-azure-files.md)voor meer informatie over FSLogix-profielcontainers.

U FSLogix-profielcontainers maken met [Azure NetApp Files](https://azure.microsoft.com/services/netapp/), een gebruiksvriendelijke Azure-native platformservice waarmee klanten snel en betrouwbaar mkb-volumes van bedrijfsniveau kunnen inrichten voor hun Windows Virtual Desktop-omgevingen. Zie [Wat is Azure NetApp-bestanden voor](../azure-netapp-files/azure-netapp-files-introduction.md) meer informatie over Azure NetApp-bestanden?

In deze handleiding ziet u hoe u een Azure NetApp Files-account instelt en FSLogix-profielcontainers maakt in Windows Virtual Desktop.

In dit artikel wordt ervan uitgegaan dat u al [hostpools](create-host-pools-azure-marketplace.md) hebt ingesteld en gegroepeerd in een of meer tenants in uw Windows Virtual Desktop-omgeving. Zie [Een tenant maken in Windows Virtual Desktop](tenant-setup-azure-active-directory.md) en onze blogpost van de Tech [Community](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054)voor meer informatie over het instellen van tenants.

De instructies in deze handleiding zijn specifiek voor Windows Virtual Desktop-gebruikers. Als u op zoek bent naar meer algemene richtlijnen voor het instellen van Azure NetApp-bestanden en het maken van FSLogix-profielcontainers buiten Windows Virtual Desktop, raadpleegt u de [Azure NetApp-bestanden instellen en maakt u snel een NFS-volume.](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md)

>[!NOTE]
>Dit artikel heeft geen betrekking op aanbevolen procedures voor het beveiligen van toegang tot het delen van Azure NetApp-bestanden.

>[!NOTE]
>Zie Opslagopties voor [FSLogix-profielcontainers](store-fslogix-profile.md)als u op zoek bent naar vergelijkingsmateriaal over de verschillende opslagopties voor FSLogix-profiel .

## <a name="prerequisites"></a>Vereisten

Voordat u een FSLogix-profielcontainer voor een hostgroep maken, moet u het:

- Windows Virtual Desktop instellen en configureren
- Een Windows Virtual Desktop-hostgroep inrichten
- [Uw Azure NetApp Files-abonnement inschakelen](../azure-netapp-files/azure-netapp-files-register.md)

## <a name="set-up-your-azure-netapp-files-account"></a>Uw Azure NetApp Files-account instellen

Om aan de slag te gaan, moet u een Azure NetApp Files-account instellen.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Controleer of uw account machtigingen voor inzender of beheerders heeft.

2. Selecteer het **Azure Cloud Shell-pictogram** rechts van de zoekbalk om Azure Cloud Shell te openen.

3. Zodra Azure Cloud Shell is geopend, selecteert u **PowerShell**.

4. Als dit de eerste keer is dat u Azure Cloud Shell gebruikt, maakt u een opslagaccount in hetzelfde abonnement dat u uw Azure NetApp Files en Windows Virtual Desktop bewaart.

   ![Het opslagaccountvenster met de knop Opslag maken onder aan het venster gemarkeerd in het rood.](media/create-storage-button.png)

5. Zodra Azure Cloud Shell is geladen, voert u de volgende twee cmdlets uit.

   ```azurecli
   az account set --subscription <subscriptionID>
   ```

   ```azurecli
   az provider register --namespace Microsoft.NetApp --wait
   ```

6. Selecteer alle **services**aan de linkerkant van het venster . Voer **Azure NetApp-bestanden** in het zoekvak dat boven aan het menu wordt weergegeven.

   ![Een schermafbeelding van een gebruiker die 'Azure NetApp-bestanden' invoert in het zoekvak Alle services. De zoekresultaten tonen de Azure NetApp Files-bron.](media/azure-netapp-files-search-box.png)


7. Selecteer **Azure NetApp-bestanden** in de zoekresultaten en selecteer **Vervolgens Maken**.

8. Selecteer de knop **Add**.
9. Wanneer het tabblad **Nieuwe NetApp-account** wordt geopend, voert u de volgende waarden in:

    - Voer **voor Naam**de naam van uw NetApp-account in.
    - Selecteer **bij Abonnement**het abonnement voor het opslagaccount dat u in stap 4 hebt ingesteld in het vervolgkeuzemenu.
    - Selecteer **voor resourcegroep**een bestaande resourcegroep in het vervolgkeuzemenu of maak een nieuwe groep door **Nieuw maken te**selecteren .
    - Selecteer **bij Locatie**de regio voor uw NetApp-account in het vervolgkeuzemenu. Deze regio moet dezelfde regio zijn als de VM's van uw sessiehost.

   >[!NOTE]
   >Azure NetApp Files biedt momenteel geen ondersteuning voor het verhogen van een volume in verschillende regio's.

10. Wanneer u klaar bent, selecteert u **Maken** om uw NetApp-account te maken.

## <a name="create-a-capacity-pool"></a>Een capaciteitspool maken

Maak vervolgens een nieuwe capaciteitsgroep: 

1. Ga naar het menu Azure NetApp Files en selecteer uw nieuwe account.
2. Selecteer in het accountmenu **Capaciteitsgroepen** onder Opslagservice.
3. Selecteer **Groep toevoegen**.
4. Wanneer het tabblad **Nieuwe capaciteitsgroep** wordt geopend, voert u de volgende waarden in:

    - Voer voor **Naam**een naam in voor de nieuwe capaciteitsgroep.
    - Selecteer **voor Serviceniveau**de gewenste waarde in het vervolgkeuzemenu. We raden **Premium** aan voor de meeste omgevingen.
       >[!NOTE]
       >De Premium-instelling biedt de minimale doorvoer die beschikbaar is voor een Premium Service-niveau, namelijk 256 MBps. Mogelijk moet u deze doorvoer aanpassen voor een productieomgeving. De uiteindelijke doorvoer is gebaseerd op de relatie die wordt beschreven in [doorvoerlimieten](../azure-netapp-files/azure-netapp-files-service-levels.md).
    - Voer voor **Grootte (TiB)** de capaciteitspoolgrootte in die het beste bij uw behoeften past. De minimale grootte is 4 TiB.

5. Klik op **OK** wanneer u klaar bent.

## <a name="join-an-active-directory-connection"></a>Lid worden van een Active Directory-verbinding

Daarna moet u lid worden van een Active Directory-verbinding.

1. Selecteer **Active Directory-verbindingen** in het menu aan de linkerkant van de pagina en selecteer vervolgens de knop **Deelnemen** om de pagina Deelnemen aan Active **Directory te** openen.

   ![Een schermafbeelding van het menu Active Directory-verbindingen deelnemen.](media/active-directory-connections-menu.png)

2. Voer de volgende waarden in op de pagina **Deelnemen aan Active Directory** om lid te worden van een verbinding:

    - Voer **voor Primaire DNS**het IP-adres van de DNS-server in uw omgeving in die de domeinnaam kan oplossen.
    - Voer voor **Domein**uw volledig gekwalificeerde domeinnaam (FQDN) in.
    - Voer voor **voorvoegsel SMB Server (Computer Account)** de tekenreeks in die u aan de naam van het computeraccount wilt toevoegen.
    - Voer **voor Gebruikersnaam**de naam van het account in met machtigingen om lid te worden van het domein.
    - Voer **voor wachtwoord**het wachtwoord van het account in.

## <a name="create-a-new-volume"></a>Een nieuw volume maken

Vervolgens moet u een nieuw volume maken.

1. Selecteer **Volumes**en selecteer **Volume toevoegen**.

2. Wanneer het tabblad **Een volume maken** wordt geopend, voert u de volgende waarden in:

    - Voer **voor de naam Volume**een naam in voor het nieuwe volume.
    - Selecteer **voor capaciteitspool**de capaciteitspool die u zojuist hebt gemaakt in het vervolgkeuzemenu.
    - Voer **voor Quota (GiB)** de volumegrootte in die geschikt is voor uw omgeving.
    - Selecteer **voor virtueel netwerk**een bestaand virtueel netwerk met verbinding met de domeincontroller in het vervolgkeuzemenu.
    - Selecteer **onder Subnet**De optie **Nieuw maken**. Houd er rekening mee dat dit subnet wordt gedelegeerd aan Azure NetApp-bestanden.

3.  Selecteer **Volgende: \> Protocol** om het tabblad Protocol te openen en uw parameters voor volumetoegang te configureren.

## <a name="configure-volume-access-parameters"></a>Parameters voor volumetoegang configureren

Nadat u het volume hebt gemaakt, configureert u de parameters voor volumetoegang.

1.  Selecteer **SMB** als protocoltype.
2.  Selecteer onder Configuratie in het vervolgkeuzemenu **Active Directory** dezelfde map die u oorspronkelijk hebt verbonden in Lid worden bij Een [Active Directory-verbinding](create-fslogix-profile-container.md#join-an-active-directory-connection). Houd er rekening mee dat er een limiet is van één Active Directory per abonnement.
3.  Voer in het tekstvak **Naam delen** de naam in van het aandeel dat wordt gebruikt door de groep sessiehost en de gebruikers ervan.

4.  Selecteer **Controleren + maken** onder aan de pagina. Hiermee wordt de validatiepagina geopend. Nadat uw volume is gevalideerd, selecteert u **Maken**.

5.  Op dit moment zal het nieuwe volume beginnen te implementeren. Zodra de implementatie is voltooid, u het aandeel Azure NetApp-bestanden gebruiken.

6.  Als u het bergpad wilt zien, selecteert u **Ga naar resource** en zoekt u deze op het tabblad Overzicht.

    ![Een screenshot van het scherm Overzicht met een rode pijl die naar het bergpad wijst.](media/overview-mount-path.png)

## <a name="configure-fslogix-on-session-host-virtual-machines-vms"></a>FSLogix configureren op virtuele vm's voor sessiehost

Deze sectie is gebaseerd op [Een profielcontainer maken voor een hostgroep met behulp van een bestandsshare.](create-host-pools-user-profile.md)

1. [Download het FSLogix-zip-bestand .zip](https://go.microsoft.com/fwlink/?linkid=2084562&clcid=0x409) terwijl u nog op afstand bent in de vm van sessiehost.

2. Rits het gedownloade bestand uit.

3. Ga in het bestand naar **x64** > **Releases** en voer **FSLogixAppsSetup.exe uit.** Het installatiemenu wordt geopend.

4.  Als u een productcode hebt, voert u deze in het tekstvak Productcode in.

5. Schakel het selectievakje naast **Ik ga akkoord met de licentievoorwaarden**.

6. Selecteer **Installeren**.

7. Navigeer naar **\\C:\\Programmabestanden\\FSLogix Apps** om de installatie van de agent te bevestigen.

8. Voer **RegEdit** in het menu Start uit als beheerder.

9. Navigeer naar **computerHKEY_LOCAL_MACHINE\\\\software\\FSLogix**.

10. Een sleutel met de naam **Profielen maken**.

11.  Maak een waarde met de naam **Ingeschakeld** met een **REG_DWORD** typen ingesteld op een gegevenswaarde van **1**.

12. Maak een waarde met de naam **VHDLocations** met een **multi-string-type** en stel de gegevenswaarde in op de URI voor het aandeel Azure NetApp-bestanden.

13. Maak een waarde met de naam **DeleteLocalProfileWhenVHDShouldApply** met een DWORD-waarde van 1 om problemen met bestaande lokale profielen te voorkomen voordat u zich aanmeldt.

     >[!WARNING]
     >Wees voorzichtig bij het maken van de waarde DeleteLocalProfileWhenVHDShouldApply. Wanneer het FSLogix-profielensysteem bepaalt dat een gebruiker een FSLogix-profiel moet hebben, maar er al een lokaal profiel bestaat, verwijdert Profile Container het lokale profiel permanent. De gebruiker wordt dan aangemeld met het nieuwe FSLogix profiel.

## <a name="assign-users-to-session-host"></a>Gebruikers toewijzen aan sessiehost

1. Open **PowerShell ISE** als beheerder en meld u aan bij Windows Virtual Desktop.

2. Voer de volgende cmdlets uit:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell
   # (Optional) Install-Module Microsoft.RdInfra.RdPowershell
   $brokerurl = "https://rdbroker.wvd.microsoft.com"
   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

3. Wanneer u om referenties wordt gevraagd, voert u de referenties voor de gebruiker in met de rollen tenantmaker of RDS-bijdrager in- of rds-bijdrager op de Windows Virtual Desktop-tenant.

4. Voer de volgende cmdlets uit om een gebruiker toe te wijzen aan een groep Extern bureaublad:

   ```powershell
   $wvdTenant = "<your-wvd-tenant>"
   $hostPool = "<wvd-pool>"
   $appGroup = "Desktop Application Group"
   $user = "<user-principal>"
   Add-RdsAppGroupUser $wvdTenant $hostPool $appGroup $user
   ```

## <a name="make-sure-users-can-access-the-azure-netapp-file-share"></a>Zorg ervoor dat gebruikers toegang hebben tot het Azure NetApp-bestandsaandeel

1. Open uw internetbrowser <https://rdweb.wvd.microsoft.com/webclient/index.html>en ga naar .

2. Meld u aan met de referenties van een gebruiker die is toegewezen aan de groep Extern bureaublad.

3. Zodra u de gebruikerssessie hebt ingesteld, meldt u zich aan bij de Azure-portal met een beheerdersaccount.

4. Open **Azure NetApp-bestanden,** selecteer uw Azure NetApp-bestandenaccount en selecteer **Vervolgens Volumes**. Zodra het menu Volumes wordt geopend, selecteert u het bijbehorende volume.

   ![Een schermafbeelding van het NetApp-account dat u eerder in de Azure-portal hebt ingesteld met de knop Volumes geselecteerd.](media/netapp-account.png)

5. Ga naar het tabblad **Overzicht** en controleer of de FSLogix-profielcontainer ruimte gebruikt.

6. Maak rechtstreeks verbinding met een VM-onderdeel van de hostgroep met Extern bureaublad en open de **Verkenner.** Navigeer vervolgens naar het **bergpad** (in het \\ \\volgende\\voorbeeld wordt het bergpad anf-SMB-3863.gt1107.onmicrosoft.com anf-VOL).

   Binnen deze map moet er een profiel VHD (of VHDX) zoals in het volgende voorbeeld.

   ![Een screenshot van de inhoud van de map in het bergpad. Binnen is een enkel VHD-bestand met de naam "Profile_ssbb."](media/mount-path-folder.png)

## <a name="next-steps"></a>Volgende stappen

U FSLogix-profielcontainers gebruiken om een gebruikersprofielaandeel in te stellen. Zie [Een profielcontainer maken voor een hostgroep met een bestandsshare](create-host-pools-user-profile.md)voor meer informatie over het maken van gebruikersprofielshares met uw nieuwe containers.
