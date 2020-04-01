---
title: Certificaten bindingen
description: Leg tal van onderwerpen met betrekking tot certificaten uit in een App Service-omgeving. Lees hoe certificaatbindingen werken op de apps met één tenant in een ASE.
author: ccompy
ms.assetid: 9e21a7e4-2436-4e81-bb05-4a6ba70eeaf7
ms.topic: article
ms.date: 08/29/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: ba1d06ce83d50b6f0db84d1e423e66eae98f665d
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477496"
---
# <a name="certificates-and-the-app-service-environment"></a>Certificaten en de app-serviceomgeving 

De App Service Environment (ASE) is een implementatie van de Azure App Service die wordt uitgevoerd binnen uw Azure Virtual Network(VNet). Het kan worden geïmplementeerd met een internet toegankelijk toepassingseindpunt of een eindpunt van de toepassing dat zich in uw VNet bevindt. Als u de ASE implementeert met een internettoegankelijk eindpunt, wordt die implementatie een externe ASE genoemd. Als u de ASE implementeert met een eindpunt in uw VNet, wordt die implementatie een ILB ASE genoemd. U meer informatie over de ILB ASE in het [ILB ASE-document maken en gebruiken.](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase)

De ASE is een enkel tenantsysteem. Omdat het één tenant is, zijn er enkele functies alleen beschikbaar met een ASE die niet beschikbaar zijn in de app-service met meerdere tenant.Because it is single tenant, there are some features available only with an ASE that are not available in the multi-tenant App Service. 

## <a name="ilb-ase-certificates"></a>ILB ASE-certificaten 

Als u een externe ASE gebruikt, worden uw apps bereikt op [appname]. [asename].p.azurewebsites.net. Standaard worden alle ASE's, zelfs ILB ASEs, gemaakt met certificaten die volgen op die indeling. Wanneer u een ILB ASE hebt, worden de apps bereikt op basis van de domeinnaam die u opgeeft bij het maken van de ILB ASE. Om tls te ondersteunen, moet u certificaten uploaden om tls te ondersteunen. Verkrijg een geldig TLS/SSL-certificaat door gebruik te maken van interne certificaatautoriteiten, een certificaat te kopen bij een externe uitgever of een zelfondertekend certificaat te gebruiken. 

Er zijn twee opties voor het configureren van certificaten met uw ILB ASE.  U een standaardcertificaat voor een joker instellen voor de ILB ASE of certificaten instellen voor de afzonderlijke webapps in de ASE.  Ongeacht de keuze die u maakt, moeten de volgende certificaatkenmerken correct zijn geconfigureerd:

- **Onderwerp:** Dit kenmerk moet worden ingesteld op *. [uw-root-domein-hier] voor een wildcard ILB ASE-certificaat. Als u het certificaat voor uw app maakt, moet dit [appname] zijn. [uw-root-domein-hier]
- **Alternatieve naam van onderwerp:** Dit kenmerk moet beide *. [uw-root-domein-hier] en *.scm. [your-root-domain-here] voor het wildcard ILB ASE-certificaat. Als u het certificaat voor uw app maakt, moet dit [appname] zijn. [your-root-domain-here] en [appname].scm. [uw-root-domein-hier].

Als derde variant u een ILB ASE-certificaat maken dat al uw afzonderlijke app-namen in het SAN van het certificaat bevat in plaats van een wildcardverwijzing te gebruiken. Het probleem met deze methode is dat je vooraf de namen van de apps die je in de ASE plaatst moet weten of dat je het ILB ASE-certificaat moet blijven bijwerken.

### <a name="upload-certificate-to-ilb-ase"></a>Certificaat uploaden naar ILB ASE 

Nadat een ILB ASE in de portal is gemaakt, moet het certificaat worden ingesteld voor de ILB ASE. Totdat het certificaat is ingesteld, toont de ASE een banner die het certificaat niet is ingesteld.  

Het certificaat dat u uploadt, moet een .pfx-bestand zijn. Nadat het certificaat is geüpload, voert de ASE een schaalbewerking uit om het certificaat in te stellen. 

U de ASE niet maken en het certificaat uploaden als één actie in de portal of zelfs in één sjabloon. Als afzonderlijke actie u het certificaat uploaden met behulp van een sjabloon zoals beschreven in [het document Een ASE maken vanuit een sjabloondocument.](./create-from-template.md)  

Als u snel een zelfondertekend certificaat wilt maken voor het testen, u het volgende stukje PowerShell gebruiken:

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     
Wanneer u een zelfondertekend cert maakt, moet u ervoor zorgen dat de onderwerpnaam de indeling van CN={ASE_NAME_HERE}_InternalLoadBalancingASE heeft.

## <a name="application-certificates"></a>Toepassingscertificaten 

Apps die worden gehost in een ASE, kunnen de app-centrische certificaatfuncties gebruiken die beschikbaar zijn in de app-service met meerdere functies. Deze functies omvatten:  

- SNI-certificaten 
- IP-gebaseerde SSL, die alleen wordt ondersteund met een externe ASE.  Een ILB ASE ondersteunt geen IP-gebaseerde SSL.
- Door KeyVault gehoste certificaten 

De instructies voor het uploaden en beheren van deze certificaten zijn beschikbaar in [Een TLS/SSL-certificaat toevoegen in Azure App Service.](../configure-ssl-certificate.md)  Als u gewoon certificaten configureert die overeenkomen met een aangepaste domeinnaam die u aan uw web-app hebt toegewezen, dan volstaan die instructies. Als u het certificaat uploadt voor een ILB ASE-webapp met de standaarddomeinnaam, geeft u de scm-site op in het SAN van het certificaat zoals eerder is opgemerkt. 

## <a name="tls-settings"></a>TLS-instellingen 

U de TLS-instelling op app-niveau configureren.  

## <a name="private-client-certificate"></a>Certificaat van particuliere klanten 

Een veelgebruikte use case is het configureren van uw app als client in een client-servermodel. Als u uw server beveiligt met een privé-CA-certificaat, moet u het clientcertificaat uploaden naar uw app.  Met de volgende instructies worden certificaten geladen in de truststore van de werknemers waarop uw app wordt uitgevoerd. Als u het certificaat op één app laadt, u het gebruiken met uw andere apps in hetzelfde App Service-abonnement zonder het certificaat opnieuw te uploaden.

Ga als bedoeld als u het certificaat uploadt naar uw app in uw ASE:

1. Een *.cer-bestand* voor uw certificaat genereren. 
2. Ga naar de app die het certificaat nodig heeft in de Azure-portal
3. Ga naar SSL-instellingen in de app. Klik op Certificaat uploaden. Select Openbaar. Selecteer Lokale machine. Geef een naam op. Blader en selecteer uw *.cer-bestand.* Selecteer uploaden. 
4. Kopieer de duimafdruk.
5. Ga naar Toepassingsinstellingen. Maak een app-instelling WEBSITE_LOAD_ROOT_CERTIFICATES met de duimafdruk als waarde. Als u meerdere certificaten hebt, u deze in dezelfde instelling plaatsen, gescheiden door komma's en geen witruimte zoals 

    84EC242A4EC7957817B8E48913E50953552DAFA6,6A5C65DC9247F762FE17BF8D4906E6B31819

Het certificaat is beschikbaar voor alle apps in hetzelfde app-serviceabonnement als de app, die die instelling heeft geconfigureerd. Als u wilt dat het beschikbaar is voor apps in een ander App Service-abonnement, moet u de bewerking App-instelling herhalen in een app in dat App-service-abonnement. Ga naar de Kudu-console en geef de volgende opdracht uit in de PowerShell-foutopsporingsconsole om te controleren of het certificaat is ingesteld:

    dir cert:\localmachine\root

Als u testen wilt uitvoeren, u een zelfondertekend certificaat maken en een *.cer-bestand* genereren met de volgende PowerShell: 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.cer"
    export-certificate -Cert $certThumbprint -FilePath $fileName -Type CERT

