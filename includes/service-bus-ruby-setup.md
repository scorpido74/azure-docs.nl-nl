---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: 16ce537a54fc77fc0f72b859d6d193501d86c1fc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "67176268"
---
## <a name="create-a-ruby-application"></a>Een Ruby-toepassing maken
Zie Een [ruby-toepassing maken op Azure](../articles/virtual-machines/linux/classic/ruby-rails-web-app.md)voor instructies.

## <a name="configure-your-application-to-use-service-bus"></a>Uw toepassing configureren om servicebus te gebruiken
Als u Service Bus wilt gebruiken, downloadt en gebruikt u het Azure Ruby-pakket, dat een reeks gemaksbibliotheken bevat die communiceren met de REST-opslagservices.

### <a name="use-rubygems-to-obtain-the-package"></a>RubyGems gebruiken om het pakket te verkrijgen
1. Gebruik een opdrachtregelinterface zoals **PowerShell** (Windows), **Terminal** (Mac) of **Bash** (Unix).
2. Typ "gem install azure" in het opdrachtvenster om de edelsteen en afhankelijkheden te installeren.

### <a name="import-the-package"></a>Het pakket importeren
Voeg met behulp van uw favoriete teksteditor het volgende toe aan de bovenkant van het Ruby-bestand waarin u opslag wilt gebruiken:

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Een ServiceBus-verbinding instellen
Gebruik de volgende code om de waarden van naamruimte, naam van de sleutel, sleutel, ondertekenaar en host in te stellen:

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

Stel de naamruimtewaarde in op de waarde die u hebt gemaakt in plaats van de gehele URL. Gebruik bijvoorbeeld **'yourexamplenamespace'**, niet 'yourexamplenamespace.servicebus.windows.net'.
