# LLB Testing Specification

Conner wrote a `JooqMock` class to supplement our testing infrastructure for LLB's backend API, in addition to JUnit. In this documentation, we'll first explain the purpose of `JooqMock`, followed by a rundown of its methods. Carefully annotated examples will also be provided. See the JavaDoc for additional details.

## Purpose
The purpose of JooqMock is to serve as a mock for testing database interactions, specifically CRUD operations (create/read/update/delete).

## `addReturn(String operation, Record record)`
The addReturn method is used to add an individual record associated with a given operation. The record will be added to the queue of records that will be returned from the execute() method. In other words, it essentially adds the record that would be returned if we queried the database with the given operation. 

```java
// Example from AnnouncementsProcessorImplTest.java

// announcement1 is the record that will be returned next time a SELECT is invoked
myJooqMock.addReturn("SELECT", announcement1);
// internally invokes a SELECT
GetAnnouncementsResponse res = myAnnouncementsProcessorImpl.getAnnouncements(req);
```

## `addReturn(String operation, List<? extends Record> records)`
This overloaded addReturn method is used to add multiple records associated with an operation to a list of records to the queue of records returned by the execute() method. The hashmap that stores the records to return by execute() separates the output by operation, so all of the records given to this method would be the result of querying the database with the given "operation" multiple times. 

```java

```

## addReturn(String operation, Supplier<Result<? extends Record>> recordFunction)
