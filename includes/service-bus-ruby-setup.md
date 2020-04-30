---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: aec13c6beb8dbfcdd5f38e7f96b86bf03e42fa37
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80986772"
---
## <a name="create-a-ruby-application"></a>Een ruby-toepassing maken
Zie [een ruby-toepassing maken in azure](../articles/virtual-machines/linux/classic/ruby-rails-web-app.md)voor instructies.

## <a name="configure-your-application-to-use-service-bus"></a>Uw toepassing configureren voor het gebruik van Service Bus
Als u Service Bus wilt gebruiken, downloadt en gebruikt u het Azure ruby-pakket, dat een aantal gebruiks vriendelijke bibliotheken bevat die communiceren met de opslag REST-services.

### <a name="use-rubygems-to-obtain-the-package"></a>RubyGems gebruiken om het pakket te verkrijgen
1. Gebruik een opdrachtregelinterface zoals **PowerShell** (Windows), **Terminal** (Mac) of **Bash** (Unix).
2. Typ ' Gem installatie van Azure ' in het opdracht venster om de Gem en afhankelijkheden te installeren.

### <a name="import-the-package"></a>Het pakket importeren
Gebruik uw favoriete tekst editor om het volgende toe te voegen aan de bovenkant van het ruby-bestand waarin u opslag wilt gebruiken:

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Een Service Bus verbinding instellen
Gebruik de volgende code om de waarden van de naam ruimte, de naam van de sleutel, de sleutel, de ondertekenaar en de host in te stellen:

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

Stel de waarde van de naam ruimte in op de waarde die u hebt gemaakt in plaats van de volledige URL. Gebruik bijvoorbeeld **' yourexamplenamespace '**, niet ' yourexamplenamespace.servicebus.Windows.net '.

Wanneer u met meerdere naam ruimten werkt, kunt u de sleutel en de naam door geven aan de constructor tijdens `SharedAccessSigner` het maken van objecten

```ruby
sb_namespace = '<your azure service bus namespace>'
sb_sas_key_name = '<your azure service bus access keyname>'
sb_sas_key = '<your azure service bus access key>'

signer = Azure::ServiceBus::Auth::SharedAccessSigner.new(sb_sas_key_name, sb_sas_key)
sb_host = "https://#{sb_namespace}.servicebus.windows.net"
```
