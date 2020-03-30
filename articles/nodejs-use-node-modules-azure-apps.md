---
title: Werken met Node.js Modules
description: Meer informatie over het werken met Node.js-modules wanneer u Azure App Service of Cloud Services gebruikt.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70309280"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>Node.js-modules gebruiken met Azure-toepassingen
Dit document biedt richtlijnen voor het gebruik van Node.js-modules met toepassingen die op Azure worden gehost. Het biedt richtlijnen om ervoor te zorgen dat uw toepassing een specifieke versie van een module gebruikt en native modules met Azure gebruikt.

Als u al bekend bent met het gebruik van Node.js-modules, **package.json** en **npm-shrinkwrap.json-bestanden,** geeft de volgende informatie een korte samenvatting van wat in dit artikel wordt besproken:

* Azure App Service begrijpt **package.json-** en **npm-shrinkwrap.json-bestanden** en kan modules installeren op basis van vermeldingen in deze bestanden.

* Azure Cloud Services verwacht dat alle modules worden geïnstalleerd op de ontwikkelomgeving en dat de **nodemodulesmap\_** wordt opgenomen als onderdeel van het implementatiepakket. Het is mogelijk om ondersteuning in te schakelen voor het installeren van modules met behulp van **package.json-** of **npm-shrinkwrap.json-bestanden** op Cloud Services; Deze configuratie vereist echter aanpassing van de standaardscripts die worden gebruikt door Cloud Service-projecten. Zie [Azure Opstarttaak voor het uitvoeren van npm-installaties voor](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown) een voorbeeld van het configureren van deze omgeving om te voorkomen dat knooppuntmodules worden geïmplementeerd

> [!NOTE]
> Azure Virtual Machines worden niet besproken in dit artikel, omdat de implementatie-ervaring in een VM afhankelijk is van het besturingssysteem dat wordt gehost door de virtuele machine.
> 
> 

## <a name="nodejs-modules"></a>Node.js Modules
Modules zijn belastbare JavaScript-pakketten die specifieke functionaliteit voor uw toepassing bieden. Modules worden meestal geïnstalleerd met behulp van de **npm** command-line tool, maar sommige modules (zoals de http-module) worden geleverd als onderdeel van de kern Node.js pakket.

Wanneer modules worden geïnstalleerd, worden ze opgeslagen in de **node\_modules** directory aan de basis van uw toepassingsmapstructuur. Elke module binnen de **node\_modules** directory onderhoudt zijn eigen directory die alle modules die het afhankelijk is van bevat, en dit gedrag herhaalt zich voor elke module helemaal naar beneden de afhankelijkheidsketen. Deze omgeving maakt het mogelijk elke module geïnstalleerd om zijn eigen versie eisen voor de modules die het afhankelijk is van hebben, maar het kan resulteren in een vrij grote directory structuur.

Het implementeren van de **\_node modules** directory als onderdeel van uw toepassing verhoogt de grootte van de implementatie in vergelijking met het gebruik van een **package.json** of **npm-shrinkwrap.json** bestand; Het garandeert echter wel dat de versies van de bij de productie gebruikte modules dezelfde zijn als de modules die bij de ontwikkeling worden gebruikt.

### <a name="native-modules"></a>Native Modules
Terwijl de meeste modules zijn gewoon plain-text JavaScript-bestanden, sommige modules zijn platform-specifieke binaire beelden. Deze modules worden gecompileerd tijdens de installatie, meestal met behulp van Python en node-gyp. Aangezien Azure Cloud Services afhankelijk is van de **node-modulesmap\_** die als onderdeel van de toepassing wordt geïmplementeerd, moet elke native module die als onderdeel van de geïnstalleerde modules is opgenomen, in een cloudservice werken, zolang deze is geïnstalleerd en gecompileerd op een Windows-ontwikkelingssysteem.

Azure App Service ondersteunt niet alle native modules en kan mislukken bij het samenstellen van modules met specifieke vereisten. Terwijl sommige populaire modules zoals MongoDB optionele native afhankelijkheden hebben en prima werken zonder hen, zijn twee oplossingen succesvol gebleken met bijna alle native modules die vandaag beschikbaar zijn:

* Voer **npm-installatie uit** op een Windows-machine waarop alle vereisten van de native module zijn geïnstalleerd. Implementeer vervolgens de map met gemaakte **nodemodules\_** als onderdeel van de toepassing naar Azure App Service.

  * Controleer voordat u deze samenstelt of uw lokale Node.js-installatie een overeenkomende architectuur heeft en dat de versie zo dicht mogelijk bij de versie die in Azure wordt gebruikt (de huidige waarden kunnen worden gecontroleerd op runtime van properties **process.arch** en **process.version).**

* Azure App Service kan worden geconfigureerd om aangepaste bash- of shell-scripts uit te voeren tijdens de implementatie, zodat u aangepaste opdrachten uitvoeren en de manier waarop **npm-installaties** wordt uitgevoerd nauwkeurig configureren. Zie [Aangepaste website-implementatiescripts met Kudu](https://azure.microsoft.com/resources/videos/custom-web-site-deployment-scripts-with-kudu/)voor een video waarin wordt getoond hoe u die omgeving configureren.

### <a name="using-a-packagejson-file"></a>Een package.json-bestand gebruiken

Het **bestand package.json** is een manier om de afhankelijkheden op het hoogste niveau op te geven die uw toepassing nodig heeft, zodat het hostingplatform de afhankelijkheden kan installeren, in plaats van dat u de **nodemodulesmap\_** moet opnemen als onderdeel van de implementatie. Nadat de toepassing is geïmplementeerd, wordt de opdracht **npm-installatie** gebruikt om het **bestand package.json** te ontleden en alle vermelde afhankelijkheden te installeren.

Tijdens de ontwikkeling u de parameters **-opslaan,** **opslaan-dev**of **-opslaan-optionele** parameters gebruiken bij het installeren van modules om automatisch een vermelding voor de module toe te voegen aan uw **package.json-bestand.** Zie [npm-install](https://docs.npmjs.com/cli/install)voor meer informatie.

Een mogelijk probleem met het **bestand package.json** is dat het alleen de versie opgeeft voor afhankelijkheden op het hoogste niveau. Elke geïnstalleerde module kan al dan niet de versie van de modules opgeven waarvan het afhankelijk is, en dus is het mogelijk dat u een andere afhankelijkheidsketen krijgt dan de module die wordt gebruikt in de ontwikkeling.

> [!NOTE]
> Wanneer <b>u</b> wordt geïmplementeerd in Azure App Service, ziet u bij het publiceren van de toepassing met Git mogelijk een fout die vergelijkbaar is met het volgende voorbeeld als het publiceren van de toepassing met Git:
> 
> npm ERR! module-name@0.6.0installeren: 'node-gyp configure build'
> 
> npm ERR! 'cmd "/c" "node-gyp configure build"' mislukt met 1
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>Een npm-shrinkwrap.json-bestand gebruiken
Het **bestand npm-shrinkwrap.json** is een poging om de beperkingen voor moduleversievan het **package.json-bestand** aan te pakken. Hoewel het **bestand package.json** alleen versies bevat voor de modules op het hoogste niveau, bevat het **bestand npm-shrinkwrap.json** de versievereisten voor de volledige afhankelijkheidsketen van de module.

Wanneer uw toepassing klaar is voor productie, u de vereisten voor de versie vergrendelen en een **npm-shrinkwrap.json-bestand** maken met behulp van de opdracht **npm shrinkwrap.** Met deze opdracht worden de versies gebruikt die momenteel in de map **nodemodules\_** zijn geïnstalleerd en worden deze versies opgenomen in het **bestand npm-shrinkwrap.json.** Nadat de toepassing is geïmplementeerd in de hostingomgeving, wordt de **npm-installatieopdracht** gebruikt om het **npm-shrinkwrap.json-bestand** te ontleden en alle vermelde afhankelijkheden te installeren. Zie [npm-shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap)voor meer informatie.

> [!NOTE]
> Wanneer u wordt geïmplementeerd in Azure App Service, ziet u mogelijk een fout die vergelijkbaar is met het volgende voorbeeld wanneer u de toepassing publiceert met Git als uw <b>npm-shrinkwrap.json-bestand</b> verwijst naar een native module:
> 
> npm ERR! module-name@0.6.0installeren: 'node-gyp configure build'
> 
> npm ERR! 'cmd "/c" "node-gyp configure build"' mislukt met 1
> 
> 

## <a name="next-steps"></a>Volgende stappen
Nu u begrijpt hoe u Node.js-modules met Azure gebruiken, leest u hoe u [de Node.js-versie opgeeft,](https://github.com/squillace/staging/blob/master/articles/nodejs-specify-node-version-azure-apps.md) [een Web-app van Node.js en implementeert](app-service/app-service-web-get-started-nodejs.md)en [Hoe u de Azure Command-Line-interface voor Mac en Linux gebruiken.](https://azure.microsoft.com/blog/using-windows-azure-with-the-command-line-tools-for-mac-and-linux/)

Zie het [Node.js-ontwikkelaarscentrum](/azure/javascript/) voor meer informatie.

[specify the Node.js version]: nodejs-specify-node-version-azure-apps.md
[How to use the Azure Command-Line Interface for Mac and Linux]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo
