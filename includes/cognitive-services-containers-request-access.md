---
author: IEvangelist
ms.author: dapine
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.openlocfilehash: 993b0e8cc5b1ec482b2f6041dfc970dc7e7409dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68229324"
---
Vul het [formulier Cognitive Services Vision Containers Request](https://aka.ms/VisionContainersPreview) in en dien dit in om toegang tot de container aan te vragen. Het formulier vraagt om informatie over u, uw bedrijf en het gebruikersscenario waarvoor u de container gebruikt. Nadat u het formulier hebt ingediend, controleert het Azure Cognitive Services-team het om te zien of u voldoet aan de criteria voor toegang tot het privécontainerregister.

> [!IMPORTANT]
> U moet een e-mailadres gebruiken dat is gekoppeld aan een Microsoft-account (MSA) of een Azure AD-account (Azure Directory) in het formulier.

Als uw aanvraag wordt goedgekeurd, ontvangt u een e-mail met instructies waarin wordt beschreven hoe u uw referenties verkrijgen en toegang krijgt tot het privécontainerregister.

## <a name="log-in-to-the-private-container-registry"></a>Log in bij het register van privécontainers

Er zijn verschillende manieren om te verifiëren met het privécontainerregister voor cognitive services-containers. We raden u aan de opdrachtregelmethode te gebruiken met behulp van de [Docker CLI.](https://docs.docker.com/engine/reference/commandline/cli/)

Gebruik de [aanmeldingsopdracht docker,](https://docs.docker.com/engine/reference/commandline/login/) zoals in het `containerpreview.azurecr.io`volgende voorbeeld wordt weergegeven, om in te loggen bij , het privécontainerregister voor containers van Cognitive Services. Vervang * \<\> gebruikersnaam* door de gebruikersnaam en * \<het wachtwoord\> * door het wachtwoord dat is opgegeven in de referenties die u hebt ontvangen van het Azure Cognitive Services-team.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Als u uw referenties in een tekstbestand hebt beveiligd, u de `docker login` inhoud van dat tekstbestand aan de opdracht toevoegen. Gebruik `cat` de opdracht, zoals in het volgende voorbeeld wordt weergegeven. Wachtwoord * \<vervangenBestand\> * door het pad en de naam van het tekstbestand dat het wachtwoord bevat. Vervang * \<\> gebruikersnaam* door de gebruikersnaam in uw referenties.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

