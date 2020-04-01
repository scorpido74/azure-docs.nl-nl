---
title: Windows Virtual Desktop-hostpool Azure Marketplace - Azure
description: Een Windows Virtual Desktop-hostgroep maken met behulp van de Azure Marketplace.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d5165b160ffc196416052a56aaa0d93c05db56bc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238620"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>Zelfstudie: Een hostgroep maken met de Azure Marketplace

In deze zelfstudie leert u hoe u een hostgroep maakt in een Windows Virtual Desktop-tenant met behulp van een Microsoft Azure Marketplace-aanbieding.

Hostpools zijn een verzameling van een of meer identieke virtuele machines binnen Windows Virtual Desktop-tenantomgevingen. Elke hostgroep kan een appgroep bevatten waarmee gebruikers kunnen communiceren zoals op een fysiek bureaublad.

De taken in deze zelfstudie zijn:

> [!div class="checklist"]
>
> * Maak een hostgroep in Windows Virtual Desktop.
> * Maak een resourcegroep met VM's in een Azure-abonnement.
> * Word lid van de VM's naar het Active Directory-domein.
> * Registreer de VM's met Windows Virtual Desktop.

## <a name="prerequisites"></a>Vereisten

* Een tenant in Virtueel bureaublad. Een eerdere [zelfstudie](tenant-setup-azure-active-directory.md) maakt een tenant.
* [Windows Virtual Desktop PowerShell-module](/powershell/windows-virtual-desktop/overview/).

Zodra u deze module hebt, voert u de volgende cmdlet uit om u aan te melden bij uw account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [Azure Portal](https://portal.azure.com).

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Het Azure Marketplace-aanbod uitvoeren om een nieuwe hostgroep te voorzien

Ga als volgende voor een nieuwe hostgroep:

1. Selecteer in het menu van Azure Portal of op de **Startpagina** de optie **Een resource maken**.
1. Voer **Windows Virtueel bureaublad in** het zoekvenster Marketplace in.
1. Selecteer **Windows Virtueel bureaublad - Een hostgroep inrichten**en selecteer Vervolgens **Maken**.

Volg daarna de instructies in de volgende sectie om de informatie voor de juiste tabbladen in te voeren.

### <a name="basics"></a>Basisbeginselen

Dit is wat u doet voor het tabblad **Basisbeginselen:**

1. Selecteer een **abonnement**.
1. Selecteer **Voor resourcegroep** **Nieuw maken** en geef een naam op voor de nieuwe resourcegroep.
1. Selecteer een **regio**.
1. Voer een naam in voor de hostgroep die uniek is binnen de Windows Virtual Desktop-tenant.
1. Selecteer **Bureaubladtype**. Als u **Persoonlijk**selecteert, wordt elke gebruiker die verbinding maakt met deze hostgroep permanent toegewezen aan een virtuele machine.
1. Voer gebruikers in die zich kunnen aanmelden bij de Windows Virtual Desktop-clients en toegang krijgen tot een bureaublad. Gebruik een door komma's gescheiden lijst. Als u bijvoorbeeld wilt `user1@contoso.com` toewijzen `user2@contoso.com` en openen, voert u*`user1@contoso.com,user2@contoso.com`*
1. Selecteer **voor locatie metagegevens van Service**dezelfde locatie als het virtuele netwerk met verbinding met de Active Directory-server.

   >[!IMPORTANT]
   >Als u een pure Azure Active Directory Domain Services (Azure AD DS) en Azure Active Directory (Azure AD)-oplossing gebruikt, moet u uw hostgroep implementeren in dezelfde regio als uw Azure AD DS om domein-join- en referentiefouten te voorkomen.

1. Selecteer **Volgende: Virtuele machines configureren**.

### <a name="configure-virtual-machines"></a>Virtuele machines configureren

Ga als eerste naar het tabblad **Virtuele machines configureren:**

1. Accepteer de standaardinstellingen of pas het aantal en de grootte van de virtuele machines aan.

    >[!NOTE]
    >Als de specifieke virtuele machinegrootte die u zoekt niet wordt weergegeven in de groottekiezer, is dat omdat we deze nog niet hebben aangesloten bij de Azure Marketplace-tool. Als u een grootte wilt aanvragen, maakt u een verzoek of stemt u een bestaand verzoek in het [Windows Virtual Desktop UserVoice-forum.](https://windowsvirtualdesktop.uservoice.com/forums/921118-general)

1. Voer een voorvoegsel in voor de namen van de virtuele machines. Als u bijvoorbeeld *voorvoegsel*invoert, worden de virtuele machines **voorfix-0,** **voorvoegsel-1,** enzovoort genoemd.
1. Selecteer **Volgende: Instellingen voor virtuele machines**.

### <a name="virtual-machine-settings"></a>Instellingen voor virtuele machines

Ga als eerste naar het tabblad **Instellingen voor virtuele machines:**

1. Selecteer **bij Afbeeldingsbron**de bron en voer de juiste informatie in voor het vinden en opslaan ervan. Uw opties verschillen voor Blob-opslag, Beheerde afbeelding en Galerie.

   Als u ervoor kiest geen beheerde schijven te gebruiken, selecteert u het opslagaccount dat het *vhd-bestand* bevat.
1. Voer de gebruikersnaam en het wachtwoord in. Dit account moet het domeinaccount zijn dat de virtuele machines zal aansluiten bij het Active Directory-domein. Dezelfde gebruikersnaam en wachtwoord worden gemaakt op de virtuele machines als een lokaal account. U deze lokale accounts later opnieuw instellen.

   >[!NOTE]
   > Als u uw virtuele machines aansluit bij een Azure AD DS-omgeving, moet u ervoor zorgen dat uw domeinlid is van de [groep AAD DC-beheerders](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).
   >
   > Het account moet ook deel uitmaken van het door Azure AD DS beheerde domein of Azure AD-tenant. Accounts van externe mappen die zijn gekoppeld aan uw Azure AD-tenant, kunnen niet correct verifiëren tijdens het domeinjoinproces.

1. Selecteer het **virtuele netwerk** met verbinding met de Active Directory-server en kies vervolgens een subnet om de virtuele machines te hosten.
1. Selecteer **Volgende: Windows Virtual Desktop-gegevens**.

### <a name="windows-virtual-desktop-tenant-information"></a>Windows Virtual Desktop-tenantgegevens

Ga als verkenner naar het tabblad **Tenantgegevens van Windows Virtual Desktop:**

1. Voer voor de naam van de **tenantgroep**van Windows Virtual Desktop de naam in voor de tenantgroep die uw tenant bevat. Laat het als de standaard, tenzij u een specifieke tenantgroepnaam hebt opgegeven.
1. Voer voor **de tenantnaam van Windows Virtual Desktop**de naam in van de tenant waar u deze hostgroep maakt.
1. Geef het type referenties op dat u wilt gebruiken om te verifiëren als de RdS-eigenaar van de Windows Virtual Desktop-tenant. Voer de UPN- of Serviceprincipal en een wachtwoord in.

   Als u de [opdrachtvoor serviceen en roltoewijzingen maken met PowerShell-zelfstudie hebt](./create-service-principal-role-powershell.md)voltooid, selecteert u **Serviceprincipal**.

1. Voer voor **Serviceprincipal**voor **Azure AD-tenant-id**het tenantbeheeraccount in voor het Azure AD-exemplaar dat de serviceprincipal bevat. Alleen serviceprincipals met een wachtwoordreferentie worden ondersteund.
1. Selecteer **Volgende: Controleren + maken**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>De installatie voltooien en de virtuele machine maken

Bekijk in **Controleren en Maken**de installatiegegevens. Als je iets moet veranderen, ga dan terug en breng wijzigingen aan. Wanneer u klaar bent, selecteert u **Maken** om uw hostgroep te implementeren.

Afhankelijk van het aantal virtuele machines dat u maakt, kan dit proces 30 minuten of langer duren.

>[!IMPORTANT]
> Om uw Windows Virtual Desktop-omgeving in Azure te beveiligen, raden we u aan de binnenkomende poort 3389 niet te openen op uw virtuele machines. Voor Windows Virtual Desktop is geen open inkomende poort 3389 vereist voor gebruikers om toegang te krijgen tot de virtuele machines van de hostpool.
>
> Als u poort 3389 moet openen voor probleemoplossingsdoeleinden, raden we u aan just-in-time toegang te gebruiken. Zie [Uw beheerpoorten beveiligen met just-in-time toegang](../security-center/security-center-just-in-time.md)voor meer informatie.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(Optioneel) Extra gebruikers toewijzen aan de bureaubladtoepassingsgroep

Nadat Azure Marketplace is voltooid bij het maken van de groep, u meer gebruikers toewijzen aan de groep bureaubladtoepassingen. Als u niet meer wilt toevoegen, slaat u deze sectie over.

Ga als u gebruikers aande bureaubladtoepassingsgroep toe:

1. Open een PowerShell-venster.

1. Voer de volgende opdracht uit om u aan te melden bij de Windows Virtual Desktop-omgeving:

   ```powershell
   Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
   ```

1. Voeg gebruikers toe aan de bureaubladtoepassingsgroep met deze opdracht:

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
   ```

   Het UPN van de gebruiker moet bijvoorbeeld overeenkomen met de *user1@contoso.com*identiteit van de gebruiker in Azure AD. Als u meerdere gebruikers wilt toevoegen, voert u de opdracht voor elke gebruiker uit.

Gebruikers die u aan de bureaubladtoepassingsgroep toevoegt, kunnen zich aanmelden bij Windows Virtual Desktop met ondersteunde Extern bureaublad-clients en een bron voor een sessiebureaublad zien.

Hier zijn de huidige ondersteunde clients:

* [Extern bureaublad-client voor Windows 7 en Windows 10](connect-windows-7-and-10.md)
* [Windows Virtual Desktop-webclient](connect-web.md)

## <a name="next-steps"></a>Volgende stappen

U hebt een hostgroep gemaakt en gebruikers toegewezen om toegang te krijgen tot het bureaublad. U uw hostpool vullen met RemoteApp-programma's. Zie de les:

> [!div class="nextstepaction"]
> [Zelfstudie voor app-groepen beheren](./manage-app-groups.md)
