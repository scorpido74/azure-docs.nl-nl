---
title: Een hostgroep voor virtuele Windows-Bureau bladen maken met behulp van Azure Marketplace-Azure
description: Een hostgroep voor virtuele Windows-Bureau bladen maken met behulp van Azure Marketplace.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: 32628296c5d43a11371b486abc426da2e243d0a7
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70138231"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>Zelfstudie: Een hostpool maken met behulp van Azure Marketplace

Hostgroepen zijn een verzameling van een of meer identieke virtuele machines in Windows Virtual Desktop-Preview-Tenant omgevingen. Elke hostgroep kan een app-groep bevatten waarmee gebruikers kunnen communiceren, op dezelfde manier als op een fysiek bureau blad.

In deze zelf studie wordt beschreven hoe u een hostgroep maakt in een virtuele Windows-bureau blad-Tenant met behulp van een Microsoft Azure Marketplace aanbieding. De taken omvatten:

> [!div class="checklist"]
> * Maak een hostgroep in het virtuele bureau blad van Windows.
> * Maak een resource groep met virtuele machines in een Azure-abonnement.
> * Voeg de virtuele machines toe aan het Active Directory domein.
> * Registreer de Vm's met het virtuele bureau blad van Windows.

Voordat u begint, moet u [de Power shell-module voor virtueel bureau blad van Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) voor gebruik in uw Power shell-sessie downloaden en importeren als u dat nog niet hebt gedaan.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [Azure Portal](https://portal.azure.com).

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Voer de Azure Marketplace-aanbieding uit om een nieuwe hostgroep in te richten

De Azure Marketplace-aanbieding uitvoeren om een nieuwe hostgroep in te richten:

1. Selecteer **+** of **+ een resource maken**.
2. Voer in het venster zoeken in Marketplace het **virtuele bureau blad van Windows** in.
3. Selecteer **virtueel bureau blad van Windows-een hostgroep inrichten**en selecteer vervolgens **maken**.

Volg de instructies om de gegevens voor de juiste blades in te voeren.

### <a name="basics"></a>Basics

Ga als volgt te werk voor de Blade **basis beginselen** :

1. Voer een naam in voor de hostgroep die uniek is binnen de Windows Virtual Desktop-Tenant.
2. Selecteer de juiste optie voor een persoonlijk bureau blad. Als u **Ja**selecteert, wordt elke gebruiker die verbinding maakt met deze hostgroep permanent toegewezen aan een virtuele machine.
3. Voer een door komma's gescheiden lijst in van gebruikers die zich kunnen aanmelden bij de virtueel-bureaubladclient van Windows en toegang hebben tot een bureau blad nadat de Azure Marketplace-aanbieding is voltooid. Als u bijvoorbeeld wilt user1@contoso.com toewijzen en user2@contoso.com toegang wilt krijgen, voert u 'user1@contoso.com,user2@contoso.com' in.
4. Selecteer **nieuwe maken** en geef een naam op voor de nieuwe resource groep.
5. Voor **locatie**selecteert u dezelfde locatie als het virtuele netwerk dat verbinding heeft met de Active Directory-server.
6. Selecteer **OK**.

### <a name="configure-virtual-machines"></a>Virtuele machines configureren

Voor de Blade **virtuele machines configureren** :

1. Accepteer de standaard waarden of pas het aantal en de grootte van de virtuele machines aan.
2. Voer een voor voegsel voor de namen van de virtuele machines in. Als u bijvoorbeeld de naam "voor voegsel" opgeeft, worden de virtuele machines "voor voegsel-0," "voor voegsel-1," genoemd.
3. Selecteer **OK**.

### <a name="virtual-machine-settings"></a>Instellingen voor virtuele machines

Voor de Blade instellingen van de **virtuele machine** :

>[!NOTE]
> Als u uw Vm's lid maakt van een Azure Active Directory Domain Services-omgeving (Azure AD DS), moet u ervoor zorgen dat uw domein deelname ook lid is van de [groep Aad DC](../active-directory-domain-services/tutorial-create-instance.md#configure-an-administrative-group)-Administrators.

1. Voor **installatie kopie bron**selecteert u de bron en voert u de juiste informatie in voor het vinden ervan en hoe u deze kunt opslaan. Als u ervoor kiest geen Managed disks te gebruiken, selecteert u het opslag account dat het. VHD-bestand bevat.
2. Voer de user principal name en het wacht woord in voor het domein account dat wordt toegevoegd aan de virtuele machines in het Active Directory domein. Dezelfde gebruikers naam en hetzelfde wacht woord worden als een lokaal account op de virtuele machines gemaakt. U kunt deze lokale accounts later opnieuw instellen.
3. Selecteer het virtuele netwerk dat verbinding met de Active Directory server heeft en kies vervolgens een subnet om de virtuele machines te hosten.
4. Selecteer **OK**.

### <a name="windows-virtual-desktop-preview-tenant-information"></a>Tenant gegevens voor Windows-voor beeld van Virtual Desktop

Voor de Blade **Tenant gegevens van Windows virtueel bureau blad** :

1. Voer voor de naam van de **Windows-Tenant groep voor virtueel bureau blad**de naam in voor de Tenant groep die uw Tenant bevat. Zorg ervoor dat dit de standaard instelling is, tenzij u een specifieke naam voor de Tenant groep hebt opgegeven.
2. Voer de naam in van de Tenant waar u deze hostgroep gaat maken voor de **Windows-Tenant naam van het virtuele bureau blad**.
3. Geef het type referenties op dat u wilt gebruiken voor verificatie als de RDS-eigenaar van het Windows Virtual Desktop Tenant. Als u de [zelf studie service-principals en roltoewijzingen maken met Power shell](./create-service-principal-role-powershell.md)hebt voltooid, selecteert u **Service-Principal**. Wanneer de **Azure AD-Tenant-id** wordt weer gegeven, voert u de id in voor het Azure Active Directory exemplaar dat de service-principal bevat.
4. Voer de referenties in voor het Tenant beheerders account. Alleen service-principals met een wachtwoord referentie worden ondersteund.
5. Selecteer **OK**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>De installatie volt ooien en de virtuele machine maken

Voor de laatste twee blades:

1. Controleer de installatie gegevens op de Blade **samen vatting** . Als u iets wilt wijzigen, gaat u terug naar de juiste Blade en brengt u de wijzigingen aan voordat u doorgaat. Als de gegevens er rechts uitzien, selecteert u **OK**.
2. Bekijk op de Blade **kopen** de aanvullende informatie over uw aankoop op de Azure Marketplace.
3. Selecteer **maken** om uw hostgroep te implementeren.

Afhankelijk van het aantal Vm's dat u maakt, kan dit proces 30 minuten of langer duren.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>Beschrijving Extra gebruikers toewijzen aan de bureaublad toepassings groep

Nadat de Azure Marketplace-aanbieding is voltooid, kunt u meer gebruikers toewijzen aan de groep desktop toepassingen voordat u begint met het testen van de volledige sessie Desk tops op uw virtuele machines. Als u standaard gebruikers al hebt toegevoegd in de Azure Marketplace-aanbieding en niet meer wilt toevoegen, kunt u deze sectie overs Laan.

Als u gebruikers wilt toewijzen aan de groep bureau blad-toepassing, moet u eerst een Power shell-venster openen. Daarna moet u de volgende twee cmdlets invoeren.

Voer de volgende cmdlet uit om u aan te melden bij de virtuele Windows-bureaublad omgeving:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Gebruikers toevoegen aan de groep bureau blad-toepassing met behulp van deze cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

De UPN van de gebruiker moet overeenkomen met de identiteit van de gebruiker in Azure Active Directory user1@contoso.com(bijvoorbeeld). Als u meerdere gebruikers wilt toevoegen, moet u deze cmdlet voor elke gebruiker uitvoeren.

Nadat u deze stappen hebt voltooid, kunnen gebruikers die zijn toegevoegd aan de groep bureau blad, zich aanmelden bij een virtueel bureau blad van Windows met ondersteunde Extern bureaublad clients en een resource voor een sessie bureau blad bekijken.

Dit zijn de momenteel ondersteunde clients:

- [Extern bureaublad-client voor Windows 7 en Windows 10](connect-windows-7-and-10.md)
- [Windows Virtual Desktop Web client](connect-web.md)

>[!IMPORTANT]
>Voor het beveiligen van uw virtuele bureau blad-omgeving in azure, raden we u aan om de binnenkomende poort 3389 niet te openen op uw virtuele machines. Voor het virtuele bureau blad van Windows is geen open bare poort 3389 voor gebruikers nodig om toegang te krijgen tot de virtuele machines van de hostgroep. Als u poort 3389 moet openen voor het oplossen van problemen, raden we u aan [just-in-time-VM-toegang](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)te gebruiken.

## <a name="next-steps"></a>Volgende stappen

Nu u een hostgroep hebt gemaakt en toegewezen gebruikers toegang hebt tot het bureau blad, kunt u uw hostgroep vullen met RemoteApp-program ma's. Zie deze zelf studie voor meer informatie over het beheren van apps in Windows Virtual Desktop:

> [!div class="nextstepaction"]
> [Zelf studie app-groepen beheren](./manage-app-groups.md)
