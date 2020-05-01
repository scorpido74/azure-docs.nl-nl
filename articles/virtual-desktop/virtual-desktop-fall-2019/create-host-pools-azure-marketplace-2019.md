---
title: Windows-hostgroep voor virtueel bureau blad Azure Marketplace-Azure
description: Een Windows-hostgroep voor virtueel bureau blad maken met behulp van Azure Marketplace.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 741bd94b290560bdc850cbf7bc24ec57104d8a66
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614352"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>Zelf studie: een hostgroep maken met behulp van Azure Marketplace

>[!IMPORTANT]
>Deze inhoud is van toepassing op de najaar 2019-release die geen ondersteuning biedt voor Azure Resource Manager virtueel-bureaublad objecten van Windows. Raadpleeg [dit artikel](../create-host-pools-azure-marketplace.md)als u probeert Azure Resource Manager virtuele Windows-bureaublad objecten te beheren die zijn geïntroduceerd in de lente 2020-update.

In deze zelf studie leert u hoe u een hostgroep maakt binnen een virtuele Windows-bureau blad-Tenant met behulp van een Microsoft Azure Marketplace aanbieding.

Hostgroepen zijn een verzameling van een of meer identieke virtuele machines in Windows-Tenant omgevingen voor virtueel bureau blad. Elke hostgroep kan een app-groep bevatten waarmee gebruikers kunnen communiceren, op dezelfde manier als op een fysiek bureau blad.

De taken in deze zelf studie zijn onder andere:

> [!div class="checklist"]
>
> * Maak een hostgroep in het virtuele bureau blad van Windows.
> * Maak een resource groep met virtuele machines in een Azure-abonnement.
> * Voeg de virtuele machines toe aan het Active Directory domein.
> * Registreer de Vm's met het virtuele bureau blad van Windows.

## <a name="prerequisites"></a>Vereisten

* Een Tenant in een virtueel bureau blad. In een vorige [zelf studie](tenant-setup-azure-active-directory.md) maakt u een Tenant.
* [Windows Power shell-module voor virtueel bureau blad](/powershell/windows-virtual-desktop/overview/).

Zodra u deze module hebt, voert u de volgende cmdlet uit om u aan te melden bij uw account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Voer de Azure Marketplace-aanbieding uit om een nieuwe hostgroep in te richten

De Azure Marketplace-aanbieding uitvoeren om een nieuwe hostgroep in te richten:

1. Selecteer in het menu van Azure Portal of op de **Startpagina** de optie **Een resource maken**.
1. Voer in het venster zoeken in Marketplace het **virtuele bureau blad van Windows** in.
1. Selecteer **virtueel bureau blad van Windows-een hostgroep inrichten**en selecteer vervolgens **maken**.

Volg daarna de instructies in de volgende sectie om de gegevens voor de juiste tabbladen in te voeren.

### <a name="basics"></a>Basisbeginselen

Ga als volgt te werk voor het tabblad **basis principes** :

1. Selecteer een **abonnement**.
1. Selecteer voor **resource groep**de optie **nieuwe maken** en geef een naam op voor de nieuwe resource groep.
1. Selecteer een **regio**.
1. Voer een naam in voor de hostgroep die uniek is binnen de Windows Virtual Desktop-Tenant.
1. Selecteer het **type bureau blad**. Als u **persoonlijk**selecteert, wordt elke gebruiker die verbinding maakt met deze hostgroep permanent toegewezen aan een virtuele machine.
1. Voer gebruikers in die zich kunnen aanmelden bij de virtuele Windows-desktop clients en toegang hebben tot een bureau blad. Gebruik een lijst met door komma's gescheiden waarden. Als u bijvoorbeeld wilt toewijzen `user1@contoso.com` en `user2@contoso.com` toegang wilt krijgen, voert u*`user1@contoso.com,user2@contoso.com`*
1. Voor de locatie van de **meta gegevens**van de service selecteert u dezelfde locatie als het virtuele netwerk dat verbinding heeft met de Active Directory-server.

   >[!IMPORTANT]
   >Als u een zuivere Azure Active Directory Domain Services (Azure AD DS) en Azure Active Directory-oplossing (Azure AD) gebruikt, moet u ervoor zorgen dat u uw hostgroep in dezelfde regio als uw Azure-AD DS implementeert om domein lidmaatschap en referentie fouten te voor komen.

1. Selecteer **volgende: virtuele machines configureren**.

### <a name="configure-virtual-machines"></a>Virtuele machines configureren

Voor het tabblad **virtuele machines configureren** :

1. Accepteer de standaard waarden of pas het aantal en de grootte van de virtuele machines aan.

    >[!NOTE]
    >Als de specifieke grootte van de virtuele machine die u zoekt niet wordt weer gegeven in de optie voor het selecteren van de grootte, komt dit omdat we deze nog niet hebben opgedaan met het Azure Marketplace-hulp programma. Als u een grootte wilt aanvragen, moet u een aanvraag maken of een bestaande aanvraag bijstemmen in het UserVoice-forum van het [virtuele Windows-bureau blad](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

1. Voer een voor voegsel voor de namen van de virtuele machines in. Als u bijvoorbeeld een *voor voegsel*opgeeft, worden de virtuele machines het **voor voegsel-0**, het **voor voegsel-1**, enzovoort genoemd.
1. Selecteer **volgende: instellingen van de virtuele machine**.

### <a name="virtual-machine-settings"></a>Instellingen voor virtuele machines

Voor het tabblad instellingen van de **virtuele machine** :

1. Voor **installatie kopie bron**selecteert u de bron en voert u de juiste informatie in voor het vinden ervan en hoe u deze kunt opslaan. Uw opties verschillen van Blob Storage, beheerde installatie kopie en galerie.

   Als u ervoor kiest geen Managed disks te gebruiken, selecteert u het opslag account dat het *. VHD* -bestand bevat.
1. Voer de user principal name en het wacht woord in. Dit account moet het domein account zijn dat wordt toegevoegd aan de virtuele machines in het Active Directory domein. Dezelfde gebruikers naam en hetzelfde wacht woord worden als een lokaal account op de virtuele machines gemaakt. U kunt deze lokale accounts later opnieuw instellen.

   >[!NOTE]
   > Als u uw virtuele machines lid maakt van een Azure AD DS-omgeving, moet u ervoor zorgen dat uw domein lid is van de [groep Aad DC-Administrators](../../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).
   >
   > Het account moet ook deel uitmaken van de Azure AD DS beheerde domein of Azure AD-Tenant. Accounts van externe mappen die zijn gekoppeld aan uw Azure AD-Tenant, kunnen niet correct worden geverifieerd tijdens het proces voor het samen voegen van het domein.

1. Selecteer het **virtuele netwerk** dat verbinding met de Active Directory server heeft en kies vervolgens een subnet om de virtuele machines te hosten.
1. Selecteer **volgende: informatie over virtueel Windows-bureau blad**.

### <a name="windows-virtual-desktop-tenant-information"></a>Windows-Tenant gegevens voor virtueel bureau blad

Voor het tabblad **Windows-Tenant gegevens voor virtuele bureau blad** :

1. Voer voor de naam van de **Windows-Tenant groep voor virtueel bureau blad**de naam in voor de Tenant groep die uw Tenant bevat. Zorg ervoor dat dit de standaard instelling is, tenzij u een specifieke naam voor de Tenant groep hebt opgegeven.
1. Voer de naam in van de Tenant waar u deze hostgroep gaat maken voor de **Windows-Tenant naam van het virtuele bureau blad**.
1. Geef het type referenties op dat u wilt gebruiken voor verificatie als de RDS-eigenaar van het Windows Virtual Desktop Tenant. Voer de UPN-of Service-Principal en een wacht woord in.

   Als u de [zelf studie service-principals en roltoewijzingen maken met Power shell](create-service-principal-role-powershell.md)hebt voltooid, selecteert u **Service-Principal**.

1. Voor de **Service-Principal**voert u voor de **Azure AD-Tenant-id**het Tenant beheerders account in voor de Azure AD-instantie die de service-principal bevat. Alleen service-principals met een wachtwoord referentie worden ondersteund.
1. Selecteer **volgende: controleren + maken**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>De installatie volt ooien en de virtuele machine maken

Controleer de installatie gegevens bij **controleren en maken**. Als u iets wilt wijzigen, gaat u terug en brengt u wijzigingen aan. Wanneer u klaar bent, selecteert u **maken** om uw hostgroep te implementeren.

Afhankelijk van het aantal virtuele machines dat u maakt, kan dit proces 30 minuten of langer duren.

>[!IMPORTANT]
> Als u uw virtuele Windows-desktop omgeving in azure wilt beveiligen, raden we u aan om de binnenkomende poort 3389 niet te openen op uw virtuele machines. Voor het virtuele bureau blad van Windows is geen open bare poort 3389 voor gebruikers nodig voor toegang tot de virtuele machines van de hostgroep.
>
> Als u poort 3389 moet openen voor het oplossen van problemen, raden we u aan Just-in-time-toegang te gebruiken. Zie [uw beheer poorten beveiligen met Just-in-time-toegang](../../security-center/security-center-just-in-time.md)voor meer informatie.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>Beschrijving Extra gebruikers toewijzen aan de bureaublad toepassings groep

Nadat Azure Marketplace klaar is met het maken van de groep, kunt u meer gebruikers toewijzen aan de groep bureau blad-toepassingen. Als u niet meer wilt toevoegen, kunt u deze sectie overs Laan.

Gebruikers toewijzen aan de bureaublad toepassings groep:

1. Open een PowerShell-venster.

1. Voer de volgende opdracht uit om u aan te melden bij de virtuele Windows-bureaublad omgeving:

   ```powershell
   Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
   ```

1. Gebruikers toevoegen aan de groep bureau blad-toepassing met behulp van deze opdracht:

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
   ```

   De UPN van de gebruiker moet overeenkomen met de identiteit van de gebruiker in azure AD *user1@contoso.com*, bijvoorbeeld. Als u meerdere gebruikers wilt toevoegen, voert u de opdracht uit voor elke gebruiker.

Gebruikers die u toevoegt aan de groep bureau blad-toepassing, kunnen zich aanmelden bij een virtueel Windows-bureau blad met ondersteunde Extern bureaublad-clients en een resource voor een sessie bureau blad bekijken.

Dit zijn de momenteel ondersteunde clients:

* [Extern bureaublad-client voor Windows 7 en Windows 10](../connect-windows-7-and-10.md)
* [Windows Virtual Desktop Web client](connect-web-2019.md)

## <a name="next-steps"></a>Volgende stappen

U hebt een hostgroep gemaakt en gebruikers toegewezen voor toegang tot het bureau blad. U kunt uw hostgroep vullen met RemoteApp-program ma's. Zie deze zelf studie voor meer informatie over het beheren van apps in Windows Virtual Desktop:

> [!div class="nextstepaction"]
> [Zelf studie app-groepen beheren](../manage-app-groups.md)
