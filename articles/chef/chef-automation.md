---
title: 'Snelstartgids: een virtuele Windows-machine configureren in azure met behulp van chef'
description: In deze Quick Start leert u hoe u chef kunt gebruiken om een virtuele Windows-machine te implementeren en configureren in azure
keywords: chef, azure, devops, virtual machine
ms.topic: tutorial
ms.service: chef
author: tomarchermsft
ms.author: tarcher
ms.date: 02/22/2020
ms.openlocfilehash: 824e4df7662ee67c3f0786877053c39a8d952d49
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77590067"
---
# <a name="quickstart---configure-a-windows-virtual-machine-in-azure-using-chef"></a>Snelstartgids: een virtuele Windows-machine configureren in azure met behulp van chef

Met chef kunt u Automation-en desired state-configuraties leveren.

Met de nieuwste Cloud API-release biedt chef naadloze integratie met Azure, waardoor u de mogelijkheid krijgt om configuratie statussen in te richten en te implementeren via één opdracht.

In dit artikel stelt u uw chef-omgeving in voor het inrichten van virtuele Azure-machines en het maken van een beleid of Cookbook en het implementeren van deze Cookbook op een virtuele machine van Azure.

## <a name="chef-basics"></a>Basis beginselen van chef

Voordat u met dit artikel begint, [raadpleegt u de basis concepten van chef](https://www.chef.io/chef).

In het volgende diagram ziet u de chef-architectuur op hoog niveau.

![Chef-architectuur](media/chef-automation/chef-architecure.png)

Chef heeft drie belangrijkste architectuur onderdelen: 
- Chef server-het beheer punt en er zijn twee opties voor de chef-server: een gehoste oplossing of een on-premises oplossing.
- Chef-client (knoop punt): de agent die zich bevindt op de servers die u beheert.
- Chef-werk station: de naam voor het beheer werkstation (waar u beleids regels maakt en opdrachten voor beheer uitvoert) en het software pakket van chef-hulpprogram ma's.

Over het algemeen ziet u **uw werk station** als de locatie waar u opdrachten en **chef werk station** voor het software pakket uitvoert.

U kunt bijvoorbeeld de opdracht mes downloaden als onderdeel van het **chef-werk station**, maar u kunt geen mes-opdrachten uitvoeren vanaf **uw werk station om de** infra structuur te beheren.

Chef maakt ook gebruik van de concepten van *hand leidingen* en *recepten*. Deze voor waarden zijn de beleids regels die respectievelijk worden gedefinieerd en toegepast op de-servers.

## <a name="preparing-your-workstation"></a>Uw werk station voorbereiden

Eerst moet u uw werk station voorbereiden door een map te maken voor het opslaan van chef-configuratie bestanden en hand leidingen.

Maak een directory met de naam `C:\Chef`.

Down load en installeer de nieuwste versie van [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) op uw werk station.

## <a name="configure-azure-service-principal"></a>Azure Service Principal configureren

We gebruiken een Service-Principal om ons te helpen bij het maken van Azure-resources van ons chef-werk station.  Als u de relevante service-principal met de vereiste machtigingen wilt maken, voert u de volgende opdrachten uit in Power shell:
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Noteer uw abonnements-, TenantID-, ClientID-en client geheim (het wacht woord dat u eerder in deze zelf studie hebt ingesteld), zoals u deze waarden nodig hebt. 

## <a name="setup-chef-server"></a>Chef-server instellen

In deze hand leiding wordt ervan uitgegaan dat u zich aanmeldt voor gehoste chef.

Als u nog geen chef-server gebruikt, kunt u het volgende doen:

* Meld u aan voor [gehoste chef](https://manage.chef.io/signup). Dit is de snelste manier om aan de slag te gaan met chef.
* Installeer een zelfstandige chef-server op een Linux-computer, gevolgd door de [installatie-instructies](https://docs.chef.io/install_server.html) van [chef docs](https://docs.chef.io/).

### <a name="creating-a-hosted-chef-account"></a>Een gehoste chef-account maken

Meld u [hier](https://manage.chef.io/signup)aan voor een gehoste chef-account.

Tijdens het registratie proces wordt u gevraagd om een nieuwe organisatie te maken.

![Organisatie venster maken](media/chef-automation/create-organization.png)

Als uw organisatie eenmaal is gemaakt, downloadt u het Start pakket.

![Chef configureren](media/chef-automation/configure-chef.png)

> [!NOTE]
> Als er een waarschuwing wordt weer gegeven dat de sleutels opnieuw moeten worden ingesteld, is het raadzaam om door te gaan omdat er nog geen bestaande infra structuur is geconfigureerd.
>

Dit zip-bestand voor de start Kit bevat de configuratie bestanden en gebruikers sleutels van uw organisatie in de `.chef` Directory.

Het `organization-validator.pem` moet afzonderlijk worden gedownload, omdat het een persoonlijke sleutel is en persoonlijke sleutels niet op de chef-server mogen worden opgeslagen. In [chef beheren](https://manage.chef.io/), gaat u naar de sectie beheer en selecteert u validatie sleutel opnieuw instellen. Dit biedt een bestand dat u afzonderlijk kunt downloaden. Sla het bestand op in c:\chef.

### <a name="configuring-your-chef-workstation"></a>Uw chef-werk station configureren

Pak de inhoud van de `chef-starter.zip` naar `c:\chef`.

Kopieer alle bestanden onder `chef-starter\chef-repo\.chef` naar uw `c:\chef` map.

Kopieer het `organization-validator.pem` bestand naar `c:\chef`, als dit is opgeslagen in `c:\Downloads`.

De map moet er nu ongeveer zo uitzien als in het volgende voor beeld.

```powershell
    Directory: C:\Users\username\chef

Mode           LastWriteTime    Length Name
----           -------------    ------ ----
d-----    12/6/2018   6:41 PM           .chef
d-----    12/6/2018   5:40 PM           chef-repo
d-----    12/6/2018   5:38 PM           cookbooks
d-----    12/6/2018   5:38 PM           roles
-a----    12/6/2018   5:38 PM       495 .gitignore
-a----    12/6/2018   5:37 PM      1678 azuredocs-validator.pem
-a----    12/6/2018   5:38 PM      1674 user.pem
-a----    12/6/2018   5:53 PM       414 knife.rb
-a----    12/6/2018   5:38 PM      2341 README.md
```

U hebt nu vijf bestanden en vier mappen (met inbegrip van de lege directory chef-opslag plaats) in de hoofdmap van c:\chef.

### <a name="edit-kniferb"></a>Mes bewerken. rb

De PEM-bestanden bevatten de persoonlijke sleutels van uw organisatie en de beheerders rechten voor communicatie en het bestand mes. rb bevat uw mes-configuratie. Het bestand mes. rb moet worden bewerkt.

Open het bestand mes. rb in de editor van uw keuze. Het ongewijzigde bestand moet er ongeveer als volgt uitzien:

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

Voeg de volgende gegevens toe aan uw mes. rb, waarbij u de tijdelijke aanduidingen vervangt door uw gegevens:

```rb
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg.pem"
knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] =     "#1234p$wdchef19"
```

Deze regels zorgen ervoor dat mes verwijst naar de hand leidingen-map onder `c:\chef\cookbooks`.

Het `knife.rb` bestand moet er nu uitzien zoals in het volgende voor beeld:

![Voor beeld van mes-bestand](./media/chef-automation/knife-file-example.png)

```rb
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                "myorg"
client_key               "#{current_dir}/myorg.pem"
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg-validator.pem"
chef_server_url          "https://api.chef.io/organizations/myorg"
cookbook_path            ["#{current_dir}/../cookbooks"]
knife[:azure_tenant_id] = "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] = "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] = "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] = "#1234p$wdchef19"
```

## <a name="install-chef-workstation"></a>Chef-werk station installeren

[Down load en installeer vervolgens het chef-werk station](https://downloads.chef.io/chef-workstation/).

Installeer chef-werk station op de standaard locatie.

Op het bureau blad ziet u een catch Weight voor Power shell. Dit hulp programma wordt gebruikt om te communiceren met chef-producten. Met de Power shell catch Weight worden nieuwe opdrachten beschikbaar gemaakt, zoals `chef-run` en chef CLI-opdrachten (zoals `chef`). Bekijk de geïnstalleerde versie van chef Workstation en de chef-hulpprogram ma's met `chef -v`. U kunt ook de versie van uw werk station controleren door **about chef Workstation** te selecteren in de chef-werk station-app.

`chef --version` moet er ongeveer als volgt uitzien:

```
Chef Workstation: 0.4.2
  chef-run: 0.3.0
  chef-client: 15.0.300
  delivery-cli: 0.0.52 (9d07501a3b347cc687c902319d23dc32dd5fa621)
  berks: 7.0.8
  test-kitchen: 2.2.5
  inspec: 4.3.2
```

> [!NOTE]
> De volg orde van het pad is belang rijk. Als uw opscode-paden zich niet in de juiste volg orde bevinden, zullen er problemen optreden.
>

Start uw werk station opnieuw op voordat u doorgaat.

### <a name="install-knife-azure"></a>Mes Azure installeren

In deze zelf studie wordt ervan uitgegaan dat u de Azure Resource Manager gebruikt om te communiceren met uw virtuele machine.

Installeer de mes Azure-extensie, die de Azure-invoeg toepassing bevat.

Voer de volgende opdracht uit.

    chef gem install knife-azure ––pre

> [!NOTE]
> Het argument `–-pre` zorgt ervoor dat u de nieuwste RC-versie van de mes Azure-invoeg toepassing ontvangt, waarmee u toegang hebt tot de nieuwste set Api's.
>
>

Waarschijnlijk worden er ook tegelijkertijd een aantal afhankelijkheden geïnstalleerd.

![Uitvoer van mes-Azure installeren](./media/chef-automation/install-knife-azure.png)

Voer de volgende opdracht uit om ervoor te zorgen dat alles correct is geconfigureerd.

    knife azurerm server list

Als alles goed is geconfigureerd, ziet u een lijst met beschik bare Azure-afbeeldingen die worden doorzocht.

Gefeliciteerd! Uw werk station is ingesteld.

## <a name="creating-a-cookbook"></a>Een Cookbook maken

Een Cookbook wordt gebruikt door chef om een set opdrachten te definiëren die u op uw beheerde client wilt uitvoeren. Het maken van een Cookbook is eenvoudig. u hoeft alleen de `chef generate cookbook` opdracht te gebruiken om de Cookbook-sjabloon te genereren. Deze Cookbook is voor een webserver die automatisch IIS implementeert.

Voer onder uw `C:\Chef directory`de volgende opdracht uit.

    chef generate cookbook webserver

Met deze opdracht wordt een set bestanden gegenereerd in de map C:\Chef\cookbooks\webserver. Vervolgens definieert u de reeks opdrachten die de chef-client moet uitvoeren op de beheerde virtuele machine.

De opdrachten worden opgeslagen in het bestand default. rb. In dit bestand definieert u een set opdrachten waarmee IIS wordt geïnstalleerd, wordt IIS gestart en wordt een sjabloon bestand naar de map `wwwroot` gekopieerd.

Wijzig het C:\chef\cookbooks\webserver\recipes\default.rb-bestand en voeg de volgende regels toe.

    powershell_script 'Install IIS' do
         action :run
         code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
         action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
         source 'Default.htm.erb'
         rights :read, 'Everyone'
    end

Sla het bestand op wanneer u klaar bent.

## <a name="creating-a-template"></a>Een sjabloon maken

In deze stap gaat u een sjabloon bestand genereren om te gebruiken als de `default.html`-pagina.

Voer de volgende opdracht uit om de sjabloon te genereren:

    chef generate template webserver Default.htm

Navigeer naar het `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb`-bestand. Bewerk het bestand door eenvoudige *Hallo wereld* HTML-code toe te voegen en sla het bestand op.

## <a name="upload-the-cookbook-to-the-chef-server"></a>De Cookbook naar de chef-server uploaden

In deze stap maakt u een kopie van de Cookbook die u hebt gemaakt op de lokale computer en uploadt u deze naar de door chef gehoste server. Nadat het Cookbook is geüpload, wordt het weer gegeven op het tabblad **beleid** .

    knife cookbook upload webserver

![Resultaten van de installatie van Cookbook op de chef-server](./media/chef-automation/cookbook-installation-under-policy-tab.png)

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Een virtuele machine implementeren met mes Azure

Implementeer een virtuele machine van Azure en pas de `Webserver` Cookbook toe met behulp van de `knife` opdracht.

Met de `knife` opdracht worden ook de IIS-webservice en de standaard webpagina geïnstalleerd.

```bash
    knife azurerm server create `
    --azure-resource-group-name rg-chefdeployment `
    --azure-storage-account store `
    --azure-vm-name chefvm `
    --azure-vm-size 'Standard_DS2_v2' `
    --azure-service-location 'westus' `
    --azure-image-reference-offer 'WindowsServer' `
    --azure-image-reference-publisher 'MicrosoftWindowsServer' `
    --azure-image-reference-sku '2016-Datacenter' `
    --azure-image-reference-version 'latest' `
    -x myuser -P myPassword123 `
    --tcp-endpoints '80,3389' `
    --chef-daemon-interval 1 `
    -r "recipe[webserver]"
```

In het voor beeld van de `knife`-opdracht wordt een *Standard_DS2_v2* virtuele machine met Windows Server 2016 geïnstalleerd in de regio vs-West. Wijzig deze waarden in de behoeften van uw app.

Nadat u de opdracht hebt uitgevoerd, bladert u naar de Azure Portal om te zien of de machine begint met het inrichten.

![Virtuele machine die wordt ingericht](./media/chef-automation/virtual-machine-being-provisioned.png)

De opdracht prompt wordt vervolgens weer gegeven.

![Mes-uitvoer bij het maken van een virtuele machine](./media/chef-automation/knife-output-when-creating-vm.png)

Zodra de implementatie is voltooid, wordt het open bare IP-adres van de nieuwe virtuele machine weer gegeven. Plak deze waarde in een webbrowser om de nieuwe website weer te geven. Toen we de virtuele machine hebben geïmplementeerd, hebben we poort 80 geopend, zodat deze extern beschikbaar moet zijn.   

![De virtuele machine testen](./media/chef-automation/testing-the-virtual-machine.png)

In dit voor beeld wordt gebruikgemaakt van creatieve HTML-code.

U kunt ook de status van het knoop punt weer geven [chef beheren](https://manage.chef.io/). 

![De knooppunt status weer geven](./media/chef-automation/viewing-node-status.png)

Vergeet niet dat u ook via een RDP-sessie verbinding kunt maken via de Azure Portal via poort 3389.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Chef op Azure](/azure/chef/)