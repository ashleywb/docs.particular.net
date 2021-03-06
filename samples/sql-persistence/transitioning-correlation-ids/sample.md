---
title: Transitioning Saga Correlation Ids
reviewed: 2017-01-03
component: SqlPersistence
related:
 - nservicebus/sagas
---


This sample illustrates an approach for transitioning between different [Correlation Ids](/persistence/sql/saga.md#correlation-ids) in way that requires no endpoint downtime and no migration of saga data stored in sql.

NOTE: This sample uses 3 "Phase" Endpoint Projects to illustrate the iterations of a single endpoint in one solution.


include: sqlpersistence-prereqs


## Scenario

This samples uses a hypothetical "Order" scenario where the requirement is to transition from an an int correlation id `OrderNumber` to a guid correlation id `OrderId`. 

To move between phases, after running each phase adjust startup projects list in solution properties, e.g. after phase 1 disable endpoints that contain "Phase1" in name, and enable endpoints that contain "Phase2" in name.


## Phases


### Phase 1

In the initial phase an int `OrderNumber` is used. In the `ConfigureHowToFindSaga` method, the saga maps `StartOrder.OrderNumber` from the incoming message to `OrderSagaData.OrderNumber` in the saga data.


#### Message

snippet: messagePhase1


#### Saga

snippet: sagaPhase1


#### SagaData

snippet: sagadataPhase1


### Phase 2

In the second phase a guid `OrderId` is added. The saga still maps to `StartOrder.OrderNumber` to `OrderSagaData.OrderNumber` in the `ConfigureHowToFindSaga` method. However it also introduces a correlation to `OrderSagaData.OrderId` via a `transitionalCorrelationProperty` in the `[SqlSaga]` attribute.


#### Message

snippet: messagePhase2


#### Saga


snippet: sagaPhase2


#### SagaData

snippet: sagadataPhase2


WARNING: Prior to moving to Phase 3 it is necessary to verify that all existing sagas have the `Correlation_OrderId` column populated. This can either be inferred by the business knowledge (i.e. certain saga may have a known and constrained lifetime) or by querying the database.


### Phase 3

In the third phase the int `OrderNumber` is removed leaving only the `OrderId`. The saga now maps `StartOrder.OrderId` to `OrderSagaData.OrderId` in the `ConfigureHowToFindSaga` method.


#### Message

snippet: messagePhase3


#### Saga

snippet: sagaPhase3


#### SagaData

snippet: sagadataPhase3
