---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 05/18/2020
ms.openlocfilehash: 66bd78c94e6c54d26959778cc059730c13d02629
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83698562"
---
Het formulier vraagt informatie over u, uw bedrijf en het gebruikers scenario waarvoor u de container gaat gebruiken. Nadat u het formulier hebt verzonden, wordt het door het team van Azure Cognitive Services gecontroleerd om er zeker van te zijn dat u voldoet aan de criteria voor toegang tot het persoonlijke container register.

> [!IMPORTANT]
> U moet een e-mail adres gebruiken dat is gekoppeld aan een micro soft-account (MSA) of een Azure Active Directory (Azure AD)-account in het formulier.

Als uw aanvraag is goedgekeurd, ontvangt u een e-mail bericht met instructies voor het verkrijgen van uw referenties en het openen van het persoonlijke container register.

## <a name="log-in-to-the-private-container-registry"></a>Aanmelden bij het persoonlijke container register

Er zijn verschillende manieren om te verifiëren met het persoonlijke container register voor Cognitive Services containers. U wordt aangeraden de opdracht regel methode te gebruiken met behulp van de [docker-cli](https://docs.docker.com/engine/reference/commandline/cli/).

Gebruik de opdracht [docker login](https://docs.docker.com/engine/reference/commandline/login/) , zoals weer gegeven in het volgende voor beeld, om u aan te melden bij `containerpreview.azurecr.io` , het persoonlijke container register voor Cognitive Services containers. Vervang * \< username \> * door de gebruikers naam en het * \< wacht woord \> * met het wacht woord dat is opgegeven in de referenties die u hebt ontvangen van het Azure Cognitive Services-team.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Als u uw referenties in een tekst bestand hebt beveiligd, kunt u de inhoud van het tekst bestand samen voegen met de `docker login` opdracht. Gebruik de `cat` opdracht, zoals wordt weer gegeven in het volgende voor beeld. Vervang * \< passwordFile \> * door het pad en de naam van het tekst bestand dat het wacht woord bevat. Vervang * \< username \> * door de gebruikers naam die u hebt ingevoerd in uw referenties.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

