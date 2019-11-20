---
title: Special Parsing for Parameters
description: Parameterize attributes with expressions for enhanced configurability.
author: mvrequa
ms.date: 08/01/2018
ms.author: mirequa
---

# CycleCloud Cluster Template File Parsing

CycleCloud is able to resolve parameter values and perform logical evaluation
of functions.

## Parameter Value Evaluation

Cyclecloud directly interprets parameter values which use the `$` or `${}` notation.

```ini
MyAttribute = $MyParameter
MyAttribute2 = ${MyParameter2}
MyAttributeList = $Param1, $Param2 
```

## Functions using Parameters

Cyclecloud template parser is able to perform math and do ternary logic analysis
as seen below.

```ini
MaxCoreCount = ${HyperthreadedCoreCount/2}
SubnetId = ${ifThenElse($Autoscale, $BurstSubnet, $FixedSubnet)}
JetpackPlatform = ${imageselect == "windows" ? "windows" : "centos-7"}
```

Additional available operators:

Operator | Definition
------ | ----------
-, +, /, * | arithmetic
<, <=, >=, > | comparators
==, != | equality evaluators
&&, \|\| | compound boolean operators

## Special ClusterName Variable

One variable always provided is `${ClusterName}`. This is evaluated to the name
of the CycleCloud cluster.

```ini
EmailAddress = ${strcat("myuser", "@", ClusterName)}
ResourceId = ${ClusterName}-00-resource
```
