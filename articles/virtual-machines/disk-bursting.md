---
title: Bursting van beheerde schijven
description: Meer informatie over schijf bursting voor Azure-schijven en Azure virtual machines.
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: a8d1a69070628248fb710a614369ff9311252c05
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102905"
---
# <a name="managed-disk-bursting"></a>Bursting van beheerde schijven
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Burstisatie op het niveau van de virtuele machine
Ondersteuning voor burstisatie op VM-niveau is ingeschakeld in alle regio's in de open bare Cloud op de volgende ondersteunde grootten: 
- [Lsv2-serie](lsv2-series.md)

Burstisatie op VM-niveau is ook beschikbaar in West-Centraal VS voor de volgende ondersteunde grootten:
- [Dsv3-serie](dv3-dsv3-series.md)
- [Esv3-serie](ev3-esv3-series.md)

Bursting is standaard ingeschakeld voor virtuele machines die dit ondersteunen.

## <a name="disk-level-bursting"></a>Burstisatie op schijf niveau
Bursting is ook beschikbaar op onze [Premium-ssd's](disks-types.md#premium-ssd) voor de schijf grootten P20 en kleiner in alle regio's in azure Public-, Government-en China-Clouds. Schijf bursting is standaard ingeschakeld voor alle nieuwe en bestaande implementaties van de schijf grootten die dit ondersteunen. 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]