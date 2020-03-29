---
title: Een afbeeldingsfabriek uitvoeren vanuit Azure DevOps in Azure DevTest Labs
description: Dit artikel behandelt alle voorbereidingen die nodig zijn om de afbeeldingsfabriek van Azure DevOps (voorheen Visual Studio Team Services) uit te voeren.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: bb67f765684c77ed5f8527226bef578e450579e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76758679"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Een fabrieksinstallatiekopie uitvoeren vanuit Azure DevOps
Dit artikel behandelt alle voorbereidingen die nodig zijn om de afbeeldingsfabriek van Azure DevOps (voorheen Visual Studio Team Services) uit te voeren.

> [!NOTE]
> Elke orchestration motor zal werken! Azure DevOps is niet verplicht. De afbeeldingsfabriek wordt uitgevoerd met Azure PowerShell-scripts, zodat deze handmatig kan worden uitgevoerd, met Behulp van Windows Task Scheduler, andere CI/CD-systemen, enzovoort.

## <a name="create-a-lab-for-the-image-factory"></a>Een lab maken voor de afbeeldingsfabriek
De eerste stap bij het opzetten van de imagefabriek is het maken van een lab in Azure DevTest Labs. Dit lab is het beeld fabriek lab waar we de virtuele machines te maken en op maat gemaakte beelden op te slaan. Dit lab wordt beschouwd als onderdeel van het totale beeldfabriekproces. Zodra u een lab hebt gemaakt, moet u de naam opslaan, omdat u deze later nodig hebt.

## <a name="scripts-and-templates"></a>Scripts en sjablonen
De volgende stap in het adopteren van de imagefactory voor uw team is om te begrijpen wat er beschikbaar is. De scripts en sjablonen van de afbeeldingsfabriek zijn openbaar beschikbaar in [de DevTest Labs GitHub Repo.](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory) Hier is een overzicht van de stukken:

- Beeld Fabriek. Het is de hoofdmap.
    - Configuratie. De ingangen naar de beeldfabriek
        - GoldenImages. Deze map bevat JSON-bestanden die de definities van aangepaste afbeeldingen vertegenwoordigen.
        - Labs.json. Bestand waarin teams zich aanmelden om specifieke aangepaste afbeeldingen te ontvangen.
- Scripts. De motor voor de beeldfabriek.

De artikelen in deze sectie geven meer details over deze scripts en sjablonen.

## <a name="create-an-azure-devops-team-project"></a>Een Azure DevOps-teamproject maken
Met Azure DevOps u de broncode opslaan en de Azure PowerShell op één plaats uitvoeren. U terugkerende uitvoeringen plannen om afbeeldingen up-to-date te houden. Er zijn goede faciliteiten voor het loggen van de resultaten om eventuele problemen te diagnosticeren.  Het gebruik van Azure DevOps is echter geen vereiste, u elk harnas/engine gebruiken die verbinding kan maken met Azure en Azure PowerShell kan uitvoeren.

Als u een bestaand DevOps-account of project hebt dat u in plaats daarvan wilt gebruiken, slaat u deze stap over.

Maak een gratis account aan in Azure DevOps om aan de slag te gaan. Bezoek https://www.visualstudio.com/ en selecteer **Gratis aan de slag** onder Azure **DevOps** (voorheen VSTS). Je moet een unieke accountnaam kiezen en ervoor zorgen dat je ervoor kiest om code te beheren met Git. Sla de URL op naar uw teamproject zodra dit is gemaakt. Hier is een `https://<accountname>.visualstudio.com/MyFirstProject`voorbeeld URL: .

## <a name="check-in-the-image-factory-to-git"></a>Check in de afbeelding fabriek naar Git
Alle PowerShell, sjablonen en configuratie voor de afbeeldingsfabriek bevinden zich in de [openbare DevTest Labs GitHub repo.](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory) De snelste manier om de code in uw nieuwe teamproject te krijgen, is door een opslagplaats te importeren. Dit trekt in de hele DevTest Labs repository (dus je krijgt extra documenten, en monsters).

1. Ga naar het Azure DevOps-project dat u in de vorige stap hebt gemaakt (URL lijkt op **\//\<https: accountnaam>.visualstudio.com/MyFirstProject**).
2. Selecteer **Een opslagplaats importeren**.
3. Voer de **kloon-URL** in voor de `https://github.com/Azure/azure-devtestlab`DevTest Labs Repo: .
4. Selecteer **Importeren**.

    ![Git repo importeren](./media/set-up-devops-lab/import-git-repo.png)

Als u besluit om alleen in te checken wat er precies nodig is (de afbeelding fabriek bestanden), volg de stappen [hier](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) om de Git repo kloon en duw alleen de bestanden in de **scripts / ImageFactory** directory.

## <a name="create-a-build-and-connect-to-azure"></a>Een build maken en verbinding maken met Azure
Op dit moment worden de bronbestanden opgeslagen in een Git repo in Azure DevOps. U moet nu een pijplijn instellen om de Azure PowerShell uit te voeren. Er zijn veel opties om deze stappen te doen. In dit artikel gebruikt u builddefinitie voor eenvoud, maar het werkt met DevOps Build, DevOps Release (enkele of meerdere omgevingen), andere uitvoeringsengines zoals Windows Task Scheduler of een ander harnas dat Azure PowerShell kan uitvoeren.

> [!NOTE]
> Een belangrijk punt om in gedachten te houden dat sommige van de PowerShell-bestanden lang duren om uit te voeren wanneer er veel (10+) aangepaste afbeeldingen te maken. Gratis gehoste DevOps Build/ Release-agents hebben een time-out van 30 min, dus je de gratis gehoste agent niet gebruiken zodra je veel afbeeldingen begint te bouwen. Deze time-out uitdaging is van toepassing op welk harnas u ook besluit te gebruiken, het is goed om vooraf te controleren of u de typische time-outs voor lang lopende Azure PowerShell-scripts verlengen. In het geval van Azure DevOps u betaalde gehoste agents gebruiken of uw eigen build-agent gebruiken.

1. Als u wilt beginnen, selecteert u **Bouwen instellen** op de startpagina van uw DevOps-project:

    ![Knop Installatiebuild](./media/set-up-devops-lab/setup-build-button.png)
2. Geef een **naam** op voor de build (bijvoorbeeld: Afbeeldingen bouwen en leveren aan DevTest Labs).
3. Selecteer een **lege** builddefinitie en selecteer **Toepassen** om uw build te maken.
4. In dit stadium u **Gehost** kiezen voor de buildagent.
5. Sla de builddefinitie **op.**

    ![Definitie van de build](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>De buildvariabelen configureren
Als u de opdrachtregelparameters wilt vereenvoudigen, voert u de sleutelwaarden in die de afbeeldingsfabriek naar een reeks bouwvariabelen leiden. Selecteer het tabblad **Variabelen** en u ziet een lijst met verschillende standaardvariabelen. Hier vindt u de lijst met variabelen die u wilt invoeren in Azure DevOps:


| Naam variabele | Waarde | Opmerkingen |
| ------------- | ----- | ----- |
| ConfiguratieLocatie | /Scripts/ImageFactory/Configuratie | Dit is het volledige pad in de opslagplaats naar de map **Configuratie.** Als u de bovenstaande repo hebt geïmporteerd, is de waarde aan de linkerkant juist. Anders wordt bijgewerkt om naar de configuratielocatie te wijzen. |
| DevTestLabName | MyImageFactory | De naam van het lab in Azure DevTest Labs gebruikt als de fabriek om beelden te produceren. Als je er geen hebt, maak er dan een. Zorg ervoor dat het lab zich in hetzelfde abonnement bevindt waartoe het eindpunt van de service toegang heeft. |
| ImageRetentie | 1 | Het aantal afbeeldingen dat u van elk type wilt opslaan. Standaardwaarde instellen op 1. |
| MachinePassword | ******* | Het ingebouwde beheerdersaccountwachtwoord voor de virtuele machines. Dit is een tijdelijk account, dus zorg ervoor dat het veilig is. Selecteer het kleine vergrendelingspictogram aan de rechterkant om ervoor te zorgen dat het een veilige tekenreeks is. |
| MachineUserName | ImageFactoryUser | De ingebouwde admin account gebruikersnaam voor de virtuele machines. Dit is een tijdelijk account. |
| Standaardtime-outminuten | 30 | De time-out moeten we wachten op reguliere Azure-bewerkingen. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | De id van het abonnement waar het lab bestaat en waar toe het eindpunt van de service toegang heeft. |
| VMSize (VMSize) | Standard_A3 | De grootte van de virtuele machine die moet worden gebruikt voor de stap **Maken.** De vm's die zijn gemaakt, zijn van voorbijgaande aard. De grootte moet degene zijn die is [ingeschakeld voor het lab.](devtest-lab-set-lab-policy.md) Controleer of er voldoende [quotum voor abonnementskernen is.](../azure-resource-manager/management/azure-subscription-service-limits.md)

![Variabelen maken](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Verbinding maken met Azure
De volgende stap is het opzetten van service principal. Dit is een identiteit in Azure Active Directory waarmee de devOps-buildagent namens de gebruiker in Azure kan werken. Als u het wilt instellen, begint u met het toevoegen van de eerste Azure PowerShell Build Step.

1. Selecteer **Taak toevoegen**.
2. Zoeken naar **Azure PowerShell**.
3. Zodra u deze hebt gevonden, selecteert u **Toevoegen** om de taak aan de build toe te voegen. Wanneer u dit doet, ziet u de taak aan de linkerkant worden weergegeven als toegevoegd.

![PowerShell-stap instellen](./media/set-up-devops-lab/set-up-powershell-step.png)

De snelste manier om een serviceprincipal in te stellen, is door Azure DevOps dit voor ons te laten doen.

1. Selecteer de **taak die** u zojuist hebt toegevoegd.
2. Kies Azure Resource **Manager**voor **Azure Connection Type**.
3. Selecteer de koppeling **Beheren** om de serviceprincipal in te stellen.

Zie dit [blogbericht](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) voor meer informatie. Wanneer u de koppeling **Beheren** selecteert, landt u op de juiste plaats in DevOps (tweede schermafbeelding in het blogbericht) om de verbinding met Azure in te stellen. Kies azure **resource manager serviceeindpunt** bij het instellen.

## <a name="complete-the-build-task"></a>De bouwtaak voltooien
Als u de buildtaak selecteert, ziet u alle details in het rechterdeelvenster die moeten worden ingevuld.

1. Geef eerst de naam van de buildtaak: **Virtuele machines maken**.
2. Kies de **serviceprincipal** die u hebt gemaakt door **Azure Resource Manager** te kiezen
3. Kies het **serviceeindpunt**.
4. Selecteer **voor Scriptpad** **... (ellips)** aan de rechterkant.
5. Navigeer naar **MakeGoldenImageVMs.ps1** script.
6. Scriptparameters moeten er als volgt uitzien:`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![De builddefinitie voltooien](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>Wachtrij de build
Laten we controleren of u alles correct hebt ingesteld door een nieuwe build in de rij te zetten. Terwijl de build wordt uitgevoerd, schakelt u over naar de [Azure-portal](https://portal.azure.com) en selecteert u op **Alle virtuele machines** in uw imagefactorylab om te bevestigen dat alles correct werkt. Je zou moeten zien dat er drie virtuele machines worden gemaakt in het lab.

![VM's in het lab](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>Volgende stappen
De eerste stap in het instellen van de afbeeldingsfabriek op basis van Azure DevTest Labs is voltooid. In het volgende artikel in de serie, krijg je die VM's gegeneraliseerd en opgeslagen in aangepaste afbeeldingen. Dan heb je ze verdeeld naar al je andere laboratoria. Zie het volgende artikel in de serie: [Sla aangepaste afbeeldingen op en distribueer naar meerdere labs.](image-factory-save-distribute-custom-images.md)
