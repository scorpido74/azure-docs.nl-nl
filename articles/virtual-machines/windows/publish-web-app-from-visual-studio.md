---
title: Een web-app publiceren naar een Azure VM vanuit Visual Studio
description: Een ASP.NET webtoepassing publiceren naar een Azure Virtual Machine vanuit Visual Studio
services: virtual-machines-windows
author: ghogen
manager: jillfra
tags: azure-service-management
ms.assetid: 70267837-3629-41e0-bb58-2167ac4932b3
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: 6e5db9b4e46019aa386057d51d956ff11d90f498
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71970865"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>Een web-app van ASP.NET publiceren naar een Azure VM vanuit Visual Studio

In dit document wordt beschreven hoe u een ASP.NET-webtoepassing publiceert naar een virtuele Azure-machine (VM) met behulp van de publicatiefunctie **Microsoft Azure Virtual Machines** in Visual Studio 2019.  

## <a name="prerequisites"></a>Vereisten
Om Visual Studio te gebruiken om een ASP.NET project te publiceren naar een Azure VM, moet de VM correct zijn ingesteld.

- Machine moet zijn geconfigureerd om een ASP.NET webtoepassing uit te voeren en WebDeploy te laten installeren.

- De VM moet een DNS-naam hebben geconfigureerd. Zie [Een volledig gekwalificeerde domeinnaam maken in de Azure-portal voor een Windows-vm voor](portal-create-fqdn.md)meer informatie.

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>Uw ASP.NET web-app publiceren naar de Azure VM met Visual Studio
In de volgende sectie wordt beschreven hoe u een bestaande ASP.NET-webtoepassing publiceert naar een virtuele Azure-machine.

1. Open uw web-app-oplossing in Visual Studio 2019.
2. Klik met de rechtermuisknop op het project in Solution Explorer en kies **Publiceren...**
3. Gebruik de pijl rechts van de pagina om door de publicatieopties te bladeren totdat u **Microsoft Azure Virtual Machines vindt.**  

   ![Pagina publiceren - Pijl-rechts]

4. Selecteer het pictogram **Microsoft Azure Virtual Machines** en selecteer **Publiceren**.

   ![Pagina publiceren - Pictogram Microsoft Azure Virtual Machine]

5. Kies het juiste account (met Een Azure-abonnement dat is verbonden met uw virtuele machine).  
   - Als u bent aangemeld bij Visual Studio, wordt de accountlijst gevuld met al uw geverifieerde accounts.  
   - Als u niet bent aangemeld of als het account dat u nodig hebt niet wordt vermeld, kiest u 'Een account toevoegen...'. en volg de aanwijzingen om in te loggen.  
   ![Azure-accountkiezer]  

6. Selecteer de juiste VM in de lijst met bestaande virtuele machines.

   > [!Note]
   > Het vullen van deze lijst kan enige tijd duren.

   ![Azure VM-kiezer]

7. Klik op OK om te beginnen met publiceren.

8. Wanneer u om referenties wordt gevraagd, geeft u de gebruikersnaam en het wachtwoord van een gebruikersaccount op de doel-VM die is geconfigureerd met publicatierechten. Deze referenties zijn meestal de gebruikersnaam en het wachtwoord van de beheerder die worden gebruikt bij het maken van de VM.  

   ![WebDeploy-aanmelding]

9. Accepteer het beveiligingscertificaat.

   ![Certificaatfout]

10. Bekijk het venster Uitvoer om de voortgang van de publicatiebewerking te controleren.

    ![Uitvoervenster]

11. Als publiceren succesvol is, wordt een browser gestart om de URL van de nieuw gepubliceerde site te openen.

**Succes!**

U hebt uw web-app nu met succes gepubliceerd naar een virtuele Azure-machine.

## <a name="publish-page-options"></a>Paginaopties publiceren

Nadat de wizard Publiceren is voltooid, wordt de pagina Publiceren goed in het document geopend met het nieuwe publicatieprofiel geselecteerd.

### <a name="re-publish"></a>Opnieuw publiceren

Als u updates voor uw webtoepassing wilt publiceren, selecteert u de knop **Publiceren** op de pagina Publiceren.  
- Voer de gebruikersnaam en het wachtwoord in als u daarom wordt gevraagd.  
- Publiceren begint onmiddellijk.

![Pagina publiceren - Knop Publiceren]

### <a name="modify-publish-profile-settings"></a>Instellingen voor publicatieprofiel wijzigen

Als u de instellingen voor het publicatieprofiel wilt weergeven en wijzigen, selecteert u **Instellingen...**.  

![Pagina publiceren - knop Instellingen]

Uw instellingen moeten er ongeveer zo uitzien:  

![Publicatie-instellingen - pagina Verbinding]

#### <a name="save-user-name-and-password"></a>Gebruikersnaam en wachtwoord opslaan
- Vermijd het verstrekken van verificatie-informatie elke keer dat u publiceert. Vul hiervoor de velden **Gebruikersnaam** en **Wachtwoord** in en selecteer het vak **Wachtwoord opslaan.**
- Gebruik de knop **Verbindings valideren** om te bevestigen dat u de juiste gegevens hebt ingevoerd.

#### <a name="deploy-to-clean-web-server"></a>Implementeren op schone webserver

- Als u ervoor wilt zorgen dat de webserver na elke upload een schone kopie van de webtoepassing heeft en dat er geen **Settings** andere bestanden meer over zijn van een eerdere implementatie, u het selectievakje **Extra bestanden verwijderen op het** tabblad Bestemming controleren.

- Waarschuwing: Als u met deze instelling publiceert, worden alle bestanden die op de webserver aanwezig zijn (wwwroot-map) verwijderd. Zorg ervoor dat u de status van de machine weet voordat u publiceert met deze optie ingeschakeld. 

![Publicatie-instellingen - pagina Instellingen]

## <a name="next-steps"></a>Volgende stappen

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>CI/CD instellen voor geautomatiseerde implementatie naar Azure VM

Zie [Implementeren naar een Virtuele Windows-machine als](https://docs.microsoft.com/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups)u een pijplijn voor continue levering met Azure Pipelines wilt instellen.

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[Pagina publiceren - Pijl-rechts]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[Pagina publiceren - Pictogram Microsoft Azure Virtual Machine]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Azure-accountkiezer]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Azure VM-kiezer]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[WebDeploy-aanmelding]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[Certificaatfout]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[Uitvoervenster]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[Pagina publiceren - Knop Publiceren]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[Pagina publiceren - knop Instellingen]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[Publicatie-instellingen - pagina Verbinding]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[Publicatie-instellingen - pagina Instellingen]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png
