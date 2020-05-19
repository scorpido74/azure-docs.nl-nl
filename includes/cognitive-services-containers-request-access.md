---
author: IEvangelist
ms.author: dapine
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.openlocfilehash: 88d83676de1e7fa18c4c1dcbf347da8d685ba2fa
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593325"
---
Vul het [aanvraag formulier voor de Cognitive Services containers](https://aka.ms/cognitivegate) in en verzend het om toegang tot de container aan te vragen. Het formulier vraagt informatie over u, uw bedrijf en het gebruikers scenario waarvoor u de container gaat gebruiken. Nadat u het formulier hebt verzonden, wordt het door het team van Azure Cognitive Services gecontroleerd om er zeker van te zijn dat u voldoet aan de criteria voor toegang tot het persoonlijke container register.

> [!IMPORTANT]
> U moet een e-mail adres gebruiken dat is gekoppeld aan een micro soft-account (MSA) of een Azure Active Directory (Azure AD)-account in het formulier. Zie [Cognitive Services-beperking-proces](../articles/cognitive-services/cognitive-services-gating-process.md)voor meer informatie over de acceptatie criteria.

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

