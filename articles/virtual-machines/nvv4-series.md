---
title: NVv4-serie
description: Specificaties voor de virtuele machines uit de NVv4-serie.
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 0b7d200ef39a6b4da6af33a0a9b9314635c3db33
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586428"
---
# <a name="nvv4-series"></a>NVv4-serie 

De virtuele machines uit de NVv4-serie worden aangedreven door [AMD Radeon instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU'S en AMD EPYC 7V12 (Rome) cpu's. Met de NVv4-serie van Azure worden virtuele machines met gedeeltelijke Gpu's geïntroduceerd. Kies de virtuele machine met de juiste grootte voor GPU-versnelde grafische toepassingen en virtuele Bureau bladen, beginnend bij 1/achtste van een GPU met 2 GiB frame buffer naar een volledige GPU met 16 GiB frame buffer. Virtuele NVv4-machines ondersteunen momenteel alleen Windows-gast besturingssystemen.

<br>

[ACU](acu.md): 230-260<br>
[Premium Storage](premium-storage-performance.md): ondersteund<br>
[Premium Storage caching](premium-storage-performance.md): ondersteund<br>
[Livemigratie](maintenance-and-updates.md): niet ondersteund<br>
[Updates](maintenance-and-updates.md)voor het behouden van geheugen: niet ondersteund<br>
[Ondersteuning](generation-2.md)voor het genereren van vm's: generatie 1<br>
Versneld netwerken: ondersteund<br>
<br>

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | GPU | GPU-geheugen: GiB | Max. aantal gegevensschijven | Maximum aantal Nic's/verwachte netwerk bandbreedte (MBps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 / 1000 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 / 2000 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 / 4000 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 / 8000 |

<sup>1</sup> virtuele machines uit de NVv4-serie beschikken over AMD gelijktijdige multi-threading-technologie

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Ondersteunde besturingssystemen en stuurprogramma’s

Als u gebruik wilt maken van de GPU-mogelijkheden van virtuele machines uit de Azure NVv4-serie die worden uitgevoerd op Windows, moeten de AMD GPU-Stuur Programma's zijn geïnstalleerd.

Voor het hand matig installeren van AMD GPU-Stuur Programma's raadpleegt u de [installatie van de N-serie AMD GPU-stuur programma voor Windows](./windows/n-series-amd-driver-setup.md) voor ondersteunde besturings systemen, stuur Programma's, installatie en verificaties tappen.

## <a name="other-sizes"></a>Andere grootten

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.
