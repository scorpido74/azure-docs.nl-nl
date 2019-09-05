---
title: Werken met node. js-modules
description: Meer informatie over het werken met node. js-modules bij het gebruik van Azure App Service of Cloud Services.
services: ''
documentationcenter: nodejs
author: rloutlaw
manager: rloutlaw
editor: ''
ms.assetid: c0e6cd3d-932d-433e-b72d-e513e23b4eb6
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: routlaw
ms.openlocfilehash: 61be6bcd957a4e81147d5ef472b8f850e5605e41
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309280"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>Node.js-modules gebruiken met Azure-toepassingen
Dit document bevat richt lijnen voor het gebruik van node. js-modules met toepassingen die worden gehost op Azure. Het biedt richt lijnen om ervoor te zorgen dat uw toepassing gebruikmaakt van een specifieke versie van een module en gebruikmaakt van systeem eigen modules met Azure.

Als u al bekend bent met het gebruik van node. js-modules, **package. json** en **NPM-Shrinkwrap. json-** bestanden, biedt de volgende informatie een snelle samen vatting van wat in dit artikel wordt besproken:

* Azure App Service begrijpt **package. json** en **NPM-Shrinkwrap. json** files en kan modules installeren op basis van vermeldingen in deze bestanden.

* Azure Cloud Services verwacht dat alle modules moeten worden geïnstalleerd in de ontwikkel omgeving en dat de map met **knooppunt\_modules** moet worden opgenomen als onderdeel van het implementatie pakket. Het is mogelijk ondersteuning voor het installeren van modules in te scha kelen met behulp van **package. json** **-of NPM-Shrinkwrap. json-** bestanden op Cloud Services; deze configuratie vereist echter aanpassing van de standaard scripts die worden gebruikt door Cloud service-projecten. Voor een voor beeld van het configureren van deze omgeving raadpleegt [u Azure-opstart taak om de NPM-installatie uit te voeren om te voor komen dat knooppunt modules worden geïmplementeerd](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [!NOTE]
> Azure Virtual Machines komen niet aan de orde in dit artikel, omdat de implementatie-ervaring in een VM afhankelijk is van het besturings systeem dat wordt gehost door de virtuele machine.
> 
> 

## <a name="nodejs-modules"></a>Node. js-modules
Modules zijn laad bare java script-pakketten die specifieke functionaliteit bieden voor uw toepassing. Modules worden doorgaans geïnstalleerd met behulp van het **NPM** -opdracht regel programma, maar sommige modules (zoals de HTTP-module) worden gegeven als onderdeel van het kern node. js-pakket.

Als er modules zijn geïnstalleerd, worden ze opgeslagen in de map **\_knoop punt modules** in de hoofdmap van de mapstructuur van uw toepassing. Elke module in de directory met **knooppunt\_modules** onderhoudt een eigen map die modules bevat waarvan deze afhankelijk is, en dit gedrag wordt herhaald voor elke module, net als bij de afhankelijkheids keten. In deze omgeving kan elke module die is geïnstalleerd met eigen versie vereisten voor de modules waarvan deze afhankelijk is, echter een grote mappen structuur hebben.

Als u de map met **knooppunt\_modules** implementeert als onderdeel van uw toepassing, neemt de grootte van de implementatie toe als deze vergeleken is met het gebruik van een **package. json** **-of NPM-Shrinkwrap. json-** bestand. het garandeert echter wel dat de versies van de modules die worden gebruikt voor de productie zijn hetzelfde als de modules die worden gebruikt voor ontwikkeling.

### <a name="native-modules"></a>Systeem eigen modules
Hoewel de meeste modules gewoon java script-bestanden met tekst zonder opmaak zijn, zijn sommige modules platformspecifieke binaire installatie kopieën. Deze modules worden tijdens de installatie gecompileerd, meestal met behulp van python en node-Gyp. Omdat Azure Cloud Services afhankelijk is van de map **node\_modules** die wordt geïmplementeerd als onderdeel van de toepassing, moet elke systeem eigen module die deel uitmaakt van de geïnstalleerde modules, werken in een Cloud service zolang deze is geïnstalleerd en gecompileerd op een Windows-ontwikkel systeem.

Azure App Service biedt geen ondersteuning voor alle systeem eigen modules en kan mislukken bij het compileren van modules met specifieke vereisten. Hoewel sommige populaire modules zoals MongoDB optionele systeem eigen afhankelijkheden hebben en zonder hen goed werken, zijn twee tijdelijke oplossingen bewezen met vrijwel alle systeem eigen modules die momenteel beschikbaar zijn:

* Voer de **installatie van NPM** uit op een Windows-computer waarop alle vereiste onderdelen van de systeem eigen module zijn geïnstalleerd. Implementeer vervolgens de map gemaakte **knooppunt\_modules** als onderdeel van de toepassing die u wilt Azure app service.

  * Controleer voordat u het compileert of uw lokale node. js-installatie overeenkomt met de architectuur en de versie zo dicht mogelijk bij het systeem dat in azure wordt gebruikt (de huidige waarden kunnen worden gecontroleerd tijdens runtime vanuit het eigenschappen **proces. Arch** en **process. version)** ).

* Azure App Service kunnen worden geconfigureerd voor het uitvoeren van aangepaste bash of shell scripts tijdens de implementatie, zodat u aangepaste opdrachten kunt uitvoeren en de manier waarop **NPM-installatie** wordt uitgevoerd, nauw keurig moet configureren. Zie [Custom website Deployment scripts with kudu](https://azure.microsoft.com/resources/videos/custom-web-site-deployment-scripts-with-kudu/)(Engelstalig) voor een video over het configureren van die omgeving.

### <a name="using-a-packagejson-file"></a>Een package. JSON-bestand gebruiken

Het bestand **package. json** is een manier om de hoogste niveau afhankelijkheden op te geven die uw toepassing vereist, zodat het hosting platform de afhankelijkheden kan installeren, in plaats van dat u de map met **\_knooppunt modules** moet opnemen als onderdeel van de inhoudsdistributiepad. Nadat de toepassing is geïmplementeerd, wordt de **NPM-installatie** opdracht gebruikt voor het parseren van het **package. json** -bestand en het installeren van alle afhankelijkheden die in de lijst staan.

Tijdens de ontwikkeling kunt u de para meters **--Opslaan**, **--Save-dev**of **---Save-optional** gebruiken bij het installeren van modules om een vermelding voor de module toe te voegen aan uw **pakket. json** -bestand automatisch. Zie [NPM-install (](https://docs.npmjs.com/cli/install)Engelstalig) voor meer informatie.

Een mogelijk probleem met het bestand **package. json** is dat alleen de versie van afhankelijkheden op het hoogste niveau wordt opgegeven. Elke module die is geïnstalleerd, kan de versie van de modules waarvan deze afhankelijk is, mogelijk niet opgeven. het is dus mogelijk dat u een andere afhankelijkheids keten hebt dan die in ontwikkeling.

> [!NOTE]
> Als uw <b>package. json</b> -bestand naar een systeem eigen module wordt geAzure app service distribueerd, ziet u mogelijk een fout die vergelijkbaar is met het volgende voor beeld bij het publiceren van de toepassing met git:
> 
> NPM fout. module-name@0.6.0installeren: ' node-Gyp-build configureren '
> 
> NPM fout. cmd "/c" "node-Gyp configureren build" "is mislukt met 1
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>Een NPM-Shrinkwrap. JSON-bestand gebruiken
Het bestand **NPM-Shrinkwrap. json** is een poging om de beperkingen van de module versie van het bestand **package. json** op te lossen. Hoewel het bestand **package. json** alleen versies bevat voor de modules op het hoogste niveau, bevat het bestand **NPM-Shrinkwrap. json** de versie vereisten voor de volledige module-afhankelijkheids keten.

Wanneer uw toepassing gereed is voor productie, kunt u de versie vereisten vergren delen en een **NPM-Shrinkwrap. json-** bestand maken met behulp van de **NPM Shrinkwrap** -opdracht. Met deze opdracht worden de versies gebruikt die momenteel zijn geïnstalleerd in de map **node\_modules** en worden deze versies vastgelegd in het bestand **NPM-Shrinkwrap. json** . Nadat de toepassing is geïmplementeerd in de hostomgeving, wordt de **NPM-installatie** opdracht gebruikt voor het parseren van het bestand **NPM-Shrinkwrap. json** en om alle afhankelijkheden te installeren. Zie [NPM-Shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap)voor meer informatie.

> [!NOTE]
> Als uw <b>NPM-Shrinkwrap. json-</b> bestand naar een systeem eigen module wordt gedistribueerd in azure app service, ziet u mogelijk een fout die vergelijkbaar is met het volgende voor beeld bij het publiceren van de toepassing met git:
> 
> NPM fout. module-name@0.6.0installeren: ' node-Gyp-build configureren '
> 
> NPM fout. cmd "/c" "node-Gyp configureren build" "is mislukt met 1
> 
> 

## <a name="next-steps"></a>Volgende stappen
Nu u begrijpt hoe u node. js-modules gebruikt met Azure, leert u hoe u [de node. js-versie kunt opgeven](https://github.com/squillace/staging/blob/master/articles/nodejs-specify-node-version-azure-apps.md), [een node. js-Web-App kunt maken en implementeren](app-service/app-service-web-get-started-nodejs.md)en [hoe u de Azure-opdracht regel interface voor Mac en Linux gebruikt](https://azure.microsoft.com/blog/using-windows-azure-with-the-command-line-tools-for-mac-and-linux/).

Zie het [Node.js Developer Center](/azure/javascript/) voor meer informatie.

[specify the Node.js version]: nodejs-specify-node-version-azure-apps.md
[How to use the Azure Command-Line Interface for Mac and Linux]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo
