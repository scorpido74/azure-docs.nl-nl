---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 2f27c50b1d016265c20102521a137bcbb0646115
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176253"
---
Als u beschikt over een URL (Shared Access Signature) die u toegang geeft tot bronnen in een opslagaccount, u de SAS gebruiken in een verbindingstekenreeks. Omdat de SAS de informatie bevat die nodig is om de aanvraag te verifiëren, biedt een verbindingstekenreeks met een SAS het protocol, het serviceeindpunt en de benodigde referenties om toegang te krijgen tot de bron.

Als u een verbindingstekenreeks wilt maken met een handtekening voor gedeelde toegang, geeft u de tekenreeks op in de volgende indeling:

```
BlobEndpoint=myBlobEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
FileEndpoint=myFileEndpoint;
SharedAccessSignature=sasToken
```

Elk serviceeindpunt is optioneel, hoewel de verbindingstekenreeks er ten minste één moet bevatten.

> [!NOTE]
> Het gebruik van HTTPS met een SAS wordt aanbevolen als een aanbevolen praktijk.
>
> Als u een SAS opgeeft in een verbindingstekenreeks in een configuratiebestand, moet u mogelijk speciale tekens in de URL coderen.
>
>

### <a name="service-sas-example"></a>Voorbeeld van Service SAS
Hier vindt u een voorbeeld van een verbindingstekenreeks met een service-SAS voor Blob-opslag:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&sr=b&si=tutorial-policy-635959936145100803&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

En hier is een voorbeeld van dezelfde verbindingstekenreeks met het coderen van speciale tekens:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&amp;sr=b&amp;si=tutorial-policy-635959936145100803&amp;sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

### <a name="account-sas-example"></a>Voorbeeld van Account SAS
Hier is een voorbeeld van een verbindingstekenreeks met een accountSAS voor Blob- en bestandsopslag. Houd er rekening mee dat eindpunten voor beide services zijn opgegeven:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&spr=https&st=2016-04-12T03%3A24%3A31Z&se=2016-04-13T03%3A29%3A31Z&srt=s&ss=bf&sp=rwl
```

En hier is een voorbeeld van dezelfde verbindingstekenreeks met URL-codering:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&amp;sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&amp;spr=https&amp;st=2016-04-12T03%3A24%3A31Z&amp;se=2016-04-13T03%3A29%3A31Z&amp;srt=s&amp;ss=bf&amp;sp=rwl
```

