---
title: Runtime-installatie van Azure-functies
description: De voorbeeldfunctie van Azure Functions installeren 2
author: apwestgarth
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 7ad748aa9a5b45af10121648a668344548484cf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74226723"
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>De voorbeeldvan Azure Functions Runtime Preview 2 installeren

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Als u het voorbeeld 2 van Azure Functions Runtime wilt installeren, voert u de volgende stappen uit:

1. Zorg ervoor dat uw machine aan de minimumeisen voldoet.
1. Download het [Installatieprogramma Voor runtime preview van Azure-functies](https://aka.ms/azafrv2).
1. Verwijder de Azure Functions Runtime preview 1.
1. Installeer de Azure Functions Runtime preview 2.
1. Voltooi de configuratie van de Azure Functions Runtime preview 2.
1. Uw eerste functie maken in Azure Functions Runtime Preview

## <a name="prerequisites"></a>Vereisten

Voordat u het voorbeeld van Azure Functions Runtime installeert, moet u over de volgende bronnen beschikken:

1. Een machine met Microsoft Windows Server 2016 of Microsoft Windows 10 Creators Update (Professional of Enterprise Edition).
1. Een SQL Server-exemplaar dat binnen uw netwerk wordt uitgevoerd.  Minimale editie vereist is SQL Server Express.

## <a name="uninstall-previous-version"></a>Vorige versie verwijderen

Als u eerder de voorbeeldversie azure functions Runtime hebt geïnstalleerd, moet u de installatie verwijderen voordat u de nieuwste versie installeert.  Verwijder de voorbeeldpreview Azure Functions Runtime door het programma te verwijderen in Programma's toevoegen/verwijderen in Windows.

## <a name="install-the-azure-functions-runtime-preview"></a>De Runtime Preview voor Azure Functions installeren

The Azure Functions Runtime Preview Installer guides you through the installation of the Azure Functions Runtime preview Management and Worker Roles.  Het is mogelijk om de rol Beheer en werknemer op dezelfde machine te installeren.  Als u echter meer functie-apps toevoegt, moet u meer werknemersrollen op extra machines implementeren om uw functies op meerdere werknemers te kunnen schalen.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>De rol Beheer en werknemer op dezelfde machine installeren

1. Voer het installatieprogramma Azure Functions Runtime Preview uit.

    ![Installatieprogramma voor runtime-voorbeeld azure-functies][1]

1. Klik op **Volgende**.
1. Zodra u de voorwaarden van de **EULA**hebt gelezen, **schakelt u het selectievakje** in om de voorwaarden te accepteren en klik op **Volgende** om verder te gaan.
1. Selecteer de rollen die u wilt installeren op deze **functiefunctie voor functiesbeheer** en/of **functierol functies** en klik op **Volgende**.

    ![Installatieprogramma voor azure Functions Runtime Preview - rolselectie][3]

    > [!NOTE]
    > U de **functie Functies werknemer op** veel andere machines installeren. Volg hiervoor deze instructies en selecteer alleen **Functiefunctie functie** in het installatieprogramma.

1. Klik **op Volgende** om de wizard **Runtime Setup voor Azure-functies** te starten met het installatieproces op uw machine.
1. Zodra de wizard Setup is voltooid, wordt het configuratiehulpprogramma **voor Azure Functions Runtime** gestart.

    ![Installatieprogramma voor runtime-voorbeeld azure-functies is voltooid][6]

    > [!NOTE]
    > Als u installeert op **Windows 10** en de **containerfunctie** niet eerder is ingeschakeld, wordt u met de **Azure Functions Runtime Setup** gevraagd om uw machine opnieuw op te starten om de installatie te voltooien.

## <a name="configure-the-azure-functions-runtime"></a>De runtime van Azure-functies configureren

Als u de runtime-installatie azure-functies wilt voltooien, moet u de configuratie voltooien.

1. Met het configuratiehulpprogramma **Voor runtime van Azure Functions** ziet u welke rollen op uw machine zijn geïnstalleerd.

    ![Configuratieprogramma voor azure-functies runtime-voorbeeld][7]

1. Klik op het tabblad **Database,** voer de verbindingsgegevens in voor uw SQL Server-exemplaar, inclusief het opgeven van een [databasehoofdsleutel](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine)en klik op **Toepassen**.  Connectiviteit met een SQL Server-exemplaar is vereist om de Runtime van Azure-functies een database te maken ter ondersteuning van de runtime.

    ![Azure Functions Runtime preview-databaseconfiguratie][8]

1. Klik op het tabblad **Referenties.**  Hier moet u twee nieuwe referenties maken voor gebruik met een bestandsshare voor het hosten van al uw functie-apps.  Geef **combinaties gebruikersnaam** en **wachtwoord** op voor de eigenaar van **de bestandsshare** en voor de gebruiker van **bestandsshare**klikt u op **Toepassen**.

    ![Azure Functions Runtime preview-referenties][9]

1. Klik op het tabblad **Bestandsdelen.**  Hier moet u de details van de locatie van de bestandsshare opgeven.  De bestandsshare kan voor u worden gemaakt of u een bestaande bestandsshare gebruiken en op **Toepassen klikken.**  Als u een nieuwe locatie voor bestandsshare selecteert, moet u een map opgeven voor gebruik door de runtime van Azure Functions.

    ![Bestandsshare van Azure Functions Runtime-voorbeeld][10]

1. Klik op het tabblad **IIS.**  Op dit tabblad worden de details weergegeven van de websites in IIS die het configuratiehulpprogramma voor runtime-functies azure-functies maakt.  U hier een aangepaste DNS-naam opgeven voor de Azure Functions Runtime preview-portal.  Klik **op Toepassen** om te voltooien.

    ![Azure Functions Runtime preview IIS][11]

1. Klik op het tabblad **Services.**  Op dit tabblad ziet u de status van de services in uw configuratiehulpprogramma voor Runtime van Azure Functions.  Als de **Azure Functions Host Activation Service** niet wordt uitgevoerd na de eerste configuratie, klikt u op Service **starten**.

    ![Configuratie van de Runtime-voorvertoning van Azure Functions is voltooid][12]

1. Blader naar de **Runtime Portal voor Azure-functies** als `https://<machinename>.<domain>/`.

    ![Voorbeeldportal voor Runtime van Azure Functions][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Uw eerste functie maken in de runtime-voorvertoning van Azure Functions

Uw eerste functie maken in de runtime-voorvertoning van Azure Functions

1. Blader bijvoorbeeld `https://<machinename>.<domain>` `https://mycomputer.mydomain.com`naar de **Runtime Portal voor Azure-functies** .

1. U wordt gevraagd om u aan te **melden,** als u wordt geïmplementeerd in een domein uw gebruikersnaam en wachtwoord van uw domeinaccount te gebruiken, anders gebruikt u uw gebruikersnaam en wachtwoord voor uw lokale account om in te loggen op de portal.

    ![Inloggen voor Azure Functions Runtime preview-portal][14]

1. Als u functie-apps wilt maken, moet u een abonnement maken.  Klik in de linkerbovenhoek van de **+** portal op de optie naast de abonnementen.

    ![Azure Functions Runtime preview portal abonnementen][15]

1. Kies **DefaultPlan,** voer een naam voor uw abonnement in en klik op **Maken**.

    ![Abonnement en naam van azure Functions Runtime Preview-portal][16]

1. Al uw functie-apps staan in het linkerdeelvenster van de portal.  Als u een nieuwe functie-app wilt maken, selecteert u de kop **Functie-apps** en klikt u op de **+** optie.

1. Voer een naam in voor uw functie-app, selecteer het juiste abonnement, kies welke versie van de Azure Functions-runtime waarmee u wilt **programmeren** en klik op Maken

    ![Nieuwe functie-app Azure Functions Runtime preview-portal][17]

1. De nieuwe functie-app wordt weergegeven in het linkerdeelvenster van de portal.  Selecteer Functies en klik boven aan het middelste deelvenster in de portal op **Nieuwe functie.**

    ![Voorbeeldsjablonen voor Azure Functions Runtime-voorbeeld][18]

1. Selecteer de functie Timertrigger, plaats in de juiste flyout `*/5 * * * * *` naam van uw functie en wijzig de planning in (deze cron-expressie zorgt ervoor dat uw timerfunctie elke vijf seconden wordt uitgevoerd) en klik op **Maken**

    ![Azure Functions Runtime preview nieuwe timerfunctie configuratie][19]

1. Uw functie is nu gemaakt.  U het uitvoeringslogboek van uw functie-app bekijken door het **logboekvenster** onderaan de portal uit te breiden.

    ![Uitvoer van de functie Runtime preview van Azure Functions][20]

<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-Progress.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer6-InstallComplete.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[13]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png
[14]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Login.png
[15]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions.png
[16]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions1.png
[17]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewFunctionApp.png
[18]: ./media/functions-runtime-install/AzureFunctionsRuntime_v1FunctionsTemplates.png
[19]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewTimerFunction.png
[20]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_RunningV2Function.png
