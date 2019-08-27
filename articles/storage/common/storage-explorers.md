---
title: Hulp middelen voor het werken met Azure Storage
description: Een lijst met hulpprogram ma's waarmee u uw Azure Storage gegevens kunt weer geven en gebruiken.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/26/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: 11838a50d70d1b9a0216505e9ef0958d3b18bdac
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035264"
---
# <a name="azure-storage-client-tools"></a>Azure Storage Client Tools
Gebruikers van Azure Storage vaak hun gegevens willen weer geven of ermee kunnen communiceren met behulp van een Azure Storage-client programma. In de onderstaande tabellen worden een aantal hulpprogram ma's weer gegeven waarmee u dit kunt doen. We zetten in elk blok een ' X ' in als het de mogelijkheid biedt om de gegevens abstractie te inventariseren en/of te openen. De tabel geeft ook aan of de hulpprogram ma's gratis zijn of niet. ' Proef versie ' geeft aan dat er een gratis proef versie is, maar het volledige product is niet gratis. ' Y/N ' geeft aan dat een versie gratis beschikbaar is, terwijl er een andere versie beschikbaar is voor aankoop.

Er is alleen een moment opname van de beschik bare Azure Storage-client hulpprogramma's gegeven. Deze hulpprogram ma's kunnen blijven ontwikkelen en groeien in de functionaliteit. Als er correcties of updates zijn, laat u een opmerking achter om ons te laten weten. Hetzelfde geldt als u weet welke hulpprogram ma's u hier moet volgen. we zullen ze graag graag toevoegen.

**Microsoft Azure Storage-client Hulpprogramma's**

<table>
  <tr>
    <th rowspan="2">Azure Storage-client hulpprogramma</th>
    <th rowspan="2">Blok-blob</th>
    <th rowspan="2">Pagina-blob</th>
    <th rowspan="2">Blob toevoegen</th>
    <th rowspan="2">Tabellen</th>
    <th rowspan="2">Wachtrijen</th>
    <th rowspan="2">Bestanden</th>
    <th rowspan="2">Free</th>
    <th colspan="4">Platform</th>
  </tr>
  <tr>
    <td>Web</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="https://azure.microsoft.com/features/azure-portal/">Microsoft Azure Portal</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>J</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://storageexplorer.com/">Microsoft Azure Storage Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>J</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
  </tr>
  <tr>
    <td><a href="https://www.visualstudio.com/features/azure-tools-vs.aspx">Micro soft Visual Studio Server Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>J</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>

**Client Hulpprogramma's van derden Azure Storage**

De functionaliteit of kwaliteit die door de volgende hulpprogram ma's van derden werd geclaimd, is niet geverifieerd en er is geen verklaring van micro soft.

<table>
  <tr>
    <th rowspan="2">Azure Storage-client hulpprogramma</th>
    <th rowspan="2">Blok-blob</th>
    <th rowspan="2">Pagina-blob</th>
    <th rowspan="2">Blob toevoegen</th>
    <th rowspan="2">Tabellen</th>
    <th rowspan="2">Wachtrijen</th>
    <th rowspan="2">Bestanden</th>
    <th rowspan="2">Free</th>
    <th colspan="4">Platform</th>
  </tr>
  <tr>
    <td>Web</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="https://www.cerebrata.com/products/azure-management-studio/introduction">Cerabrata: Azure Management Studio</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Proef</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://github.com/sebagomez/azurestorageexplorer">Azure Web Storage Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>J</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.cloudberrylab.com/explorer/microsoft-azure.aspx">CloudBerry Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td>X</td>
    <td>J/N</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.gapotchenko.com/cloudcombine">Cloud combi neren</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Proef</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://clumsyleaf.com">ClumsyLeaf: AzureXplorer, CloudXplorer, TableXplorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>J</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.gladinet.com/Azure-Storage/index.htm">Gladinet Cloud</a></td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td>Proef</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>
