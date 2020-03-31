---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67448632"
---
Een goed SSL-certificaat zorgt ervoor dat u versleutelde informatie naar de juiste server verzendt. Naast versleuteling maakt het certificaat ook authenticatie mogelijk. U uw eigen vertrouwde SSL-certificaat uploaden via de PowerShell-interface van het apparaat.

1. [Maak verbinding met de PowerShell-interface](#connect-to-the-powershell-interface).
2. Gebruik `Set-HcsCertificate` de cmdlet om het certificaat te uploaden. Geef de volgende parameters op wanneer u daarom wordt gevraagd:

   - `CertificateFilePath`- Ga naar het aandeel dat het certificaatbestand bevat in *.pfx-indeling.*
   - `CertificatePassword`- Een wachtwoord dat wordt gebruikt om het certificaat te beschermen.
   - `Credentials`- Gebruikersnaam om toegang te krijgen tot het aandeel dat het certificaat bevat. Geef het wachtwoord op aan het netwerkaandeel wanneer daarom wordt gevraagd.

     In het volgende voorbeeld ziet u het gebruik van deze cmdlet:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

