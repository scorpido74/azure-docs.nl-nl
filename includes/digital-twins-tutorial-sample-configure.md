---
author: baanders
description: inclusief bestand voor Azure Digital Twins-zelfstudies, het voorbeeldproject configureren
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 3a7c9f61af0124c656f98b64ba9295bc659c572a
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85262334"
---
## <a name="configure-the-sample-project"></a>Het voorbeeldproject configureren

Stel vervolgens een voorbeeldclienttoepassing in die gaat communiceren met uw instantie van Azure Digital Twins. Als u het voorbeeldproject nog niet hebt gedownload, download u het nu van de landingspagina [Azure Digital Twins-voorbeelden](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples) door de knop *ZIP downloaden* onderaan de titel te selecteren.

Navigeer naar het gedownloade bestand op uw computer en pak het uit.

Navigeer in de uitgepakte map naar _AdtSampleApp/_ . Open _**AdtE2ESample.sln**_ in Visual Studio 2019. 

Gebruik in Visual Studio het deelvenster *Solution Explorer* om een kopie te maken van het bestand _SampleClientApp > **serviceConfig.json.TEMPLATE**_ (gebruik de menu's onder de rechtermuisknop om te kopiëren en plakken). Wijzig de naam van de kopie in *serviceConfig.json*. Dit bestand fungeert als een vooraf ingesteld JSON-bestand met de benodigde configuratievariabelen om het project uit te voeren.

Selecteer het bestand *serviceConfig.json* om dit te openen in het bewerkingsvenster. Wijzig `tenantId` in uw *directory-id*, `clientId` in uw *toepassings-id* en `instanceUrl` in de URL van de *hostName* van uw Azure Digital Twins-exemplaar (met *https://* ervoor, zoals hieronder weergegeven).

```json
{
  "tenantId": "<your-directory-ID>",
  "clientId": "<your-application-ID>",
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

Sla het bestand op en sluit het. 

Configureer vervolgens het bestand *serviceConfig.json* dat moet worden gekopieerd naar de uitvoermap wanneer u de *SampleClientApp* compileert. Selecteer met de rechtermuisknop het bestand *serviceConfig.json* en kies *Eigenschappen*. In het controlevenster *Eigenschappen* wijzigt u de waarde van de eigenschap *Naar uitvoermap kopiëren* in *Kopiëren indien nieuwer*.

:::image type="content" source="../articles/digital-twins/media/include-tutorial/copy-config.png" alt-text="Fragment van een Visual Studio-venster waarin het deelvenster Solution Explorer wordt weergegeven en serviceConfig.json is gemarkeerd, en het deelvenster Eigenschappen waarin de eigenschap Naar uitvoermap kopiëren is ingesteld op Kopiëren indien nieuwer" border="false":::

Houd het project _**AdtE2ESample**_ geopend in Visual Studio om dit in de rest van de zelfstudie te gebruiken.

