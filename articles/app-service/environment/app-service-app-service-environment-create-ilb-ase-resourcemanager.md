---
title: ILB ASE v1 maken
description: Maak een App Service-omgeving met een interne load balancer (ILB ASE). Dit document is alleen bedoeld voor klanten die de verouderde v1 ASE gebruiken.
author: stefsch
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 1a0ec9465be3b714e90bfca6a15b60423d6065a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295583"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Een ILB ASE maken met behulp van Azure Resource Manager-sjablonen

> [!NOTE] 
> Dit artikel gaat over de App Service Environment v1. Er is een nieuwere versie van de App Service-omgeving die gemakkelijker te gebruiken is en draait op een krachtigere infrastructuur. Voor meer informatie over de nieuwe versie start u met de [inleiding tot de app-serviceomgeving.](intro.md)
>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Overzicht
App Service-omgevingen kunnen worden gemaakt met een intern netwerkadres in plaats van een openbare VIP.  Dit interne adres wordt geleverd door een Azure-component genaamd de internal load balancer (ILB).  Een ILB ASE kan worden gemaakt met behulp van de Azure-portal.  Het kan ook worden gemaakt met behulp van automatisering door middel van Azure Resource Manager-sjablonen.  In dit artikel worden de stappen en syntaxis doorlopen die nodig zijn om een ILB ASE met Azure Resource Manager-sjablonen te maken.

Er zijn drie stappen betrokken bij het automatiseren van de creatie van een ILB ASE:

1. Eerst wordt de basis ASE gemaakt in een virtueel netwerk met behulp van een intern adres van de load balancer in plaats van een openbare VIP.  Als onderdeel van deze stap wordt een hoofddomeinnaam toegewezen aan de ILB ASE.
2. Zodra de ILB ASE is gemaakt, wordt een SSL-certificaat geüpload.  
3. Het geüploade SSL-certificaat wordt expliciet toegewezen aan de ILB ASE als het 'standaard' SSL-certificaat.  Dit SSL-certificaat wordt gebruikt voor SSL-verkeer naar apps op de ILB ASE wanneer de apps worden `https://someapp.mycustomrootcomain.com`geadresseerd met behulp van het gemeenschappelijke hoofddomein dat is toegewezen aan de ASE (bijv. )

## <a name="creating-the-base-ilb-ase"></a>De Basis ILB ASE maken
Een voorbeeld van Azure Resource Manager-sjabloon en het bijbehorende parametersbestand zijn [hier][quickstartilbasecreate]beschikbaar op GitHub.

De meeste parameters in het bestand *azuredeploy.parameters.json* zijn gebruikelijk bij het maken van zowel ILB ASE's als AS's die gebonden zijn aan een openbare VIP.  In de onderstaande lijst worden parameters van speciale notitie of die uniek zijn, genoemd bij het maken van een ILB ASE:

* *internalLoadBalancingMode*: Stel dit in de meeste gevallen in op 3, wat betekent dat zowel HTTP/HTTPS-verkeer op poorten 80/443 als de poorten van het besturings-/datakanaal waarnaar wordt geluisterd door de FTP-service op de ASE, gebonden zijn aan een intern netwerkadres van iLB.  Als deze eigenschap in plaats daarvan is ingesteld op 2, zijn alleen de FTP-servicegerelateerde poorten (zowel besturing als gegevenskanalen) gebonden aan een ILB-adres, terwijl het HTTP/HTTPS-verkeer op de openbare VIP blijft.
* *dnsSuffix*: Deze parameter definieert het standaardhoofddomein dat aan de ASE wordt toegewezen.  In de openbare variant van Azure App Service is het standaardhoofddomein voor alle web-apps *azurewebsites.net.*  Aangezien een ILB ASE echter intern is in het virtuele netwerk van een klant, heeft het geen zin om het standaardhoofddomein van de openbare dienst te gebruiken.  In plaats daarvan moet een ILB ASE een standaardhoofddomein hebben dat zinvol is voor gebruik binnen het interne virtuele netwerk van een bedrijf.  Een hypothetische Contoso Corporation kan bijvoorbeeld een standaardhoofddomein van *internal-contoso.com* gebruiken voor apps die alleen oplosbaar en toegankelijk zijn binnen het virtuele netwerk van Contoso. 
* *ipSslAddressCount*: Deze parameter wordt automatisch standaard ingesteld op een waarde van 0 in het *azuredeploy.json-bestand* omdat ILB ASEs slechts één ILB-adres hebben.  Er zijn geen expliciete IP-SSL-adressen voor een ILB ASE en daarom moet de IP-SSL-adresgroep voor een ILB ASE op nul worden ingesteld, anders treedt er een provisioningerror op. 

Zodra het *azuredeploy.parameters.json-bestand* is ingevuld voor een ILB ASE, kan de ILB ASE vervolgens worden gemaakt met behulp van het volgende Powershell-codefragment.  Wijzig de bestand PATH's die overeenkomen met de plaats waar de sjabloonbestanden van Azure Resource Manager zich op uw machine bevinden.  Vergeet ook niet om uw eigen waarden te leveren voor de naam azure resource manager-implementatie en de naam van de resourcegroep.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Nadat de sjabloon Azure Resource Manager is ingediend, duurt het enkele uren voordat de ILB ASE is gemaakt.  Zodra de creatie is voltooid, wordt de ILB ASE weergegeven in de portal UX in de lijst met App Service-omgevingen voor het abonnement dat de implementatie heeft geactiveerd.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>'Standaard'-SSL-certificaat uploaden en configureren
Zodra de ILB ASE is gemaakt, moet een SSL-certificaat worden gekoppeld aan de ASE als het 'standaard' SSL-certificaatgebruik voor het tot stand brengen van SSL-verbindingen met apps.  Als het standaard DNS-achtervoegsel van de ASE *internal-contoso.com*is, vereist een *https://some-random-app.internal-contoso.com* verbinding met een SSL-certificaat dat geldig is voor **.internal-contoso.com*. 

Er zijn verschillende manieren om een geldig SSL-certificaat te verkrijgen, waaronder interne A's, het kopen van een certificaat bij een externe uitgever en het gebruik van een zelfondertekend certificaat.  De volgende certificaatkenmerken moeten juist zijn geconfigureerd, ongeacht wat de bron van het SSL-certificaat is:

* *Onderwerp*: Dit kenmerk moet worden ingesteld op **.your-root-domain-here.com*
* *Alternatieve naam van het onderwerp:* dit kenmerk moet zowel **.your-root-domain-here.com*als **.scm.your-root-domain-here.com*bevatten.  De reden voor de tweede vermelding is dat SSL-verbindingen met de SCM/Kudu-site die aan elke app zijn gekoppeld, worden gemaakt met behulp van een adres van het *formulier your-app-name.scm.your-root-domain-here.com.*

Met een geldig SSL-certificaat in de hand zijn twee extra voorbereidende stappen nodig.  Het SSL-certificaat moet worden geconverteerd/opgeslagen als een .pfx-bestand.  Vergeet niet dat het .pfx-bestand alle tussenliggende en rootcertificaten moet bevatten en ook moet worden beveiligd met een wachtwoord.

Vervolgens moet het resulterende .pfx-bestand worden geconverteerd naar een base64-tekenreeks, omdat het SSL-certificaat wordt geüpload met behulp van een Azure Resource Manager-sjabloon.  Aangezien Azure Resource Manager-sjablonen tekstbestanden zijn, moet het PFX-bestand worden geconverteerd naar een base64-tekenreeks, zodat het kan worden opgenomen als parameter van de sjabloon.

Het onderstaande Powershell-codefragment toont een voorbeeld van het genereren van een zelfondertekend certificaat, het exporteren van het certificaat als een PFX-bestand, het omzetten van het .pfx-bestand naar een base64-gecodeerde tekenreeks en vervolgens de basis64 gecodeerde tekenreeks opslaan in een apart bestand.  De Powershell-code voor base64-codering is aangepast van de [Powershell Scripts Blog][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

Zodra het SSL-certificaat is gegenereerd en geconverteerd naar een door basis64 gecodeerde tekenreeks, kan het voorbeeld Azure Resource Manager-sjabloon op GitHub voor [het configureren van het standaard SSL-certificaat][configuringDefaultSSLCertificate] worden gebruikt.

De parameters in het bestand *azuredeploy.parameters.json* worden hieronder weergegeven:

* *appServiceEnvironmentName:* de naam van de ILB ASE die is geconfigureerd.
* *existingAseLocation*: Teksttekenreeks met het Azure-gebied waar de ILB ASE is geïmplementeerd.  Bijvoorbeeld: "South Central US".
* *pfxBlobString*: De op basis 64 gecodeerde tekenreeksweergave van het .pfx-bestand.  Met behulp van het eerder getoonde codefragment kopieert u de tekenreeks in 'exportcert.pfx.b64' en plakt u deze in als de waarde van het *kenmerk pfxBlobString.*
* *wachtwoord*: Het wachtwoord dat wordt gebruikt om het .pfx-bestand te beveiligen.
* *certificateThumbprint*: De duimafdruk van het certificaat.  Als u deze waarde ophaalt uit Powershell (bijv. *$certificate. Duimafdruk* van het eerdere codefragment), u de waarde as-is gebruiken.  Als u de waarde echter kopieert uit het dialoogvenster Windows-certificaat, moet u de vreemde spaties niet uitkleden.  Het *certificaatThumbprint* moet er ongeveer uitzien als: AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificateName:* een vriendelijke tekenreeks-id van uw eigen keuze die wordt gebruikt om het certificaat te identificeren.  De naam wordt gebruikt als onderdeel van de unieke Azure Resource Manager-id voor de entiteit *Microsoft.Web/certificaten* die het SSL-certificaat vertegenwoordigt.  De naam **moet** eindigen met \_het volgende achtervoegsel: yourASENameHere_InternalLoadBalancingASE.  Dit achtervoegsel wordt door de portal gebruikt als indicator dat het certificaat wordt gebruikt voor het beveiligen van een ASE met ILB.

Hieronder wordt een verkortvoorbeeld van *azuredeploy.parameters.json* weergegeven:

    {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
              "appServiceEnvironmentName": {
                   "value": "yourASENameHere"
              },
              "existingAseLocation": {
                   "value": "East US 2"
              },
              "pfxBlobString": {
                   "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
              },
              "password": {
                   "value": "PASSWORDGOESHERE"
              },
              "certificateThumbprint": {
                   "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
              },
              "certificateName": {
                   "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
              }
         }
    }

Zodra het *azuredeploy.parameters.json-bestand* is ingevuld, kan het standaard SSL-certificaat worden geconfigureerd met behulp van het volgende Powershell-codefragment.  Wijzig de bestand PATH's die overeenkomen met de plaats waar de sjabloonbestanden van Azure Resource Manager zich op uw machine bevinden.  Vergeet ook niet om uw eigen waarden te leveren voor de naam azure resource manager-implementatie en de naam van de resourcegroep.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Nadat de azure resource manager-sjabloon is ingediend, duurt het ongeveer veertig minuten per ASE-front-end om de wijziging toe te passen.  Als de sjabloon bijvoorbeeld met een standaardformaat ASE met twee front-ends twee front-ends gebruikt, duurt het ongeveer een uur en twintig minuten.  Terwijl de sjabloon wordt uitgevoerd, kan de ASE niet worden geschaald.  

Zodra de sjabloon is voltooid, kunnen apps op de ILB ASE worden geopend via HTTPS en worden de verbindingen beveiligd met behulp van het standaard SSL-certificaat.  Het standaard SSL-certificaat wordt gebruikt wanneer apps op de ILB ASE worden geadresseerd met behulp van een combinatie van de toepassingsnaam plus de standaardhostnaam.  Gebruik *https://mycustomapp.internal-contoso.com* bijvoorbeeld het standaard SSL-certificaat voor **.internal-contoso.com*.

Net als apps die op de openbare multitenantservice worden uitgevoerd, kunnen ontwikkelaars echter ook aangepaste hostnamen voor afzonderlijke apps configureren en vervolgens unieke SNI SSL-certificaatbindingen voor afzonderlijke apps configureren.  

## <a name="getting-started"></a>Aan de slag
Zie [Inleiding tot app-serviceomgeving](app-service-app-service-environment-intro.md) om aan de slag te gaan met app-serviceomgeving

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

