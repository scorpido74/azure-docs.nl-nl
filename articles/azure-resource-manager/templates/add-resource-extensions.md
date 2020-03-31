---
title: Configuratie na implementatie met extensies
description: Meer informatie over het gebruik van Azure Resource Manager-sjabloonextensies om configuraties na implementatie te bieden.
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: b3c4110c8761b3e8daf324d65ac7fa1dcbcdf61f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023494"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Configuraties na implementatie bieden met extensies

Sjabloonextensies zijn kleine toepassingen die configuratie- en automatiseringstaken na implementatie bieden op Azure-resources. De meest populaire is virtuele machine-extensies. Zie [Virtuele machine-extensies en -functies voor Windows](../../virtual-machines/extensions/features-windows.md)en virtuele [machine-extensies en -functies voor Linux.](../../virtual-machines/extensions/features-linux.md)

## <a name="extensions"></a>Extensies

De bestaande extensies zijn:

- [Microsoft.Compute/virtualMachines/extensies](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft.Compute virtualMachineScaleSets/extensies](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Microsoft.HDInsight-clusters/-extensies](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters)
- [Microsoft.Sql-servers/databases/extensies](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft.Web/sites/site-extensies](https://docs.microsoft.com/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Als u de beschikbare extensies wilt vinden, bladert u naar de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/). Voer **in Filter op titel** **extensie**in .

Zie voor meer informatie over het gebruik van deze extensies:

- [Zelfstudie: Implementeer virtuele machine-extensies met Azure Resource Manager-sjablonen](template-tutorial-deploy-vm-extensions.md).
- [Zelfstudie: SQL BACPAC-bestanden importeren met Azure Resource Manager-sjablonen](template-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Extensies voor virtuele machines implementeren met Azure Resource Manager-sjablonen](template-tutorial-deploy-vm-extensions.md)
