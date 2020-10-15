---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: aec13c6beb8dbfcdd5f38e7f96b86bf03e42fa37
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "80986772"
---
## <a name="create-a-ruby-application"></a>Een Ruby-toepassing maken
Zie [Een Ruby-toepassing maken in Azure](../articles/virtual-machines/linux/classic/ruby-rails-web-app.md) voor instructies.

## <a name="configure-your-application-to-use-service-bus"></a>Uw toepassing configureren voor het gebruik van Service Bus
U moet voor het gebruik van Service Bus het Azure Ruby-pakket downloaden en gebruiken. Dit bevat een stel handige bibliotheken voor communicatie met de Storage REST-services.

### <a name="use-rubygems-to-obtain-the-package"></a>RubyGems gebruiken om het pakket te verkrijgen
1. Gebruik een opdrachtregelinterface zoals **PowerShell** (Windows), **Terminal** (Mac) of **Bash** (Unix).
2. Typ 'gem install azure' in het opdrachtvenster om de gem en afhankelijkheden te installeren.

### <a name="import-the-package"></a>Het pakket importeren
Gebruik uw favoriete teksteditor en voeg het volgende toe bovenaan het Ruby-bestand waarin u Storage wilt gebruiken:

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Een Service Bus-verbinding instellen
Gebruik de volgende code om de waarden van de naamruimte, de naam van de sleutel, de sleutel, de ondertekenaar en de host in te stellen:

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

Stel de waarde van de naamruimte in op de waarde die u hebt gemaakt in plaats van de volledige URL. Gebruik bijvoorbeeld **'yourexamplenamespace'** en niet 'yourexamplenamespace.servicebus.windows.net'.

Wanneer u met meerdere naamruimten werkt, kunt u de sleutel en de naam doorgeven aan de constructor tijdens het maken van `SharedAccessSigner`-objecten

```ruby
sb_namespace = '<your azure service bus namespace>'
sb_sas_key_name = '<your azure service bus access keyname>'
sb_sas_key = '<your azure service bus access key>'

signer = Azure::ServiceBus::Auth::SharedAccessSigner.new(sb_sas_key_name, sb_sas_key)
sb_host = "https://#{sb_namespace}.servicebus.windows.net"
```
