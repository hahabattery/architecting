---
layout: default
title: Reactor Operator
description: 
grand_parent: Java
parent: Reactive Streams
nav_order: 3
---

# Flux

* flatMap
  * https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Flux.html#flatMap-java.util.function.Function-
* concatMap
  * https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Flux.html#concatMap-java.util.function.Function-
* flatMapSequential
  * https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Flux.html#flatMapSequential-java.util.function.Function-

* Tips for flatMap, concatMap, flatMapSequential

use flatMap for fast parallel calls (max 256 limit — default concurrency), when ordering of elements is not required.

use concatMap for sequential calls when strict ordering of elements is required.

use flatMapSequential for parallel calls with strict ordering of elements.

