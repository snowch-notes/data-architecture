[![License: CC BY-NC-ND 4.0](https://img.shields.io/badge/License-CC%20BY--NC--ND%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

-----

# Introduction

# Data Requirements

## Ingest

**Introduction**

This section is to provide insight into the data ingest patterns to design the data ingest process.

**Questions**

 - How will the data arrive?
   - As a continuous *stream* of data?
      - What is the peak rate of ingest in Gb/s? Now, 1YR, 3YRs, 5YRs? 
      - What is the peak rate of ingest in records/second? Now, 1YR, 3YRs, 5YRs? 
      - What are the required delivery guarantees (exactly once, at most once, at least once)?  Hint: do downstream queries on landed data need to be 100% accurate?
   - As periodic *batches* of data?
      - What frequency?
      - What is specification for the batch data?
 - Will the ingest grow or decrease in volumes over time?
 - How are records identified?
 - What is considered late data? E.g. a shop loses network connectivity for a day due to a fault - how do we handle the late point-of-sales transactions?
 - When does the data load start arriving?
    - Do we need to back-load data prior to this (e.g. by batch load)?
      - How much data needs to be back-loaded in Gb?
      - How much data needs to be back-loaded in records?
 - After receiving the data how quickly should clients be able to see the data in queries (latency)? 
 - Can you share a sample of data?
 - Can you share a schema for the data and files?
 - Can you provide some context for this source of data?  E.g. 
    - Where is the data coming from?
    - How is it generated?
 - Who is/will be the master of the data? E.g.
    - Is it a source relational database that we are performing change data capture from?
    - Is *this* system that we are building going to be the master?
 - How is the data model expected to change over time?  I.e. do we need to use a format that is resistent to schema changes (such as avro)? 
 - Who will build and maintain the data ingest process?
   - What are their core skills?
   - Will they be trained to provide necessary skills?

## Retrieval

**Introduction**

This section is to provide insight into the data access patterns to help decide what persistence technologies are appropriate along with data models and data formats.

**Questions**

 - How will client systems access the data?  JDBC? ODBC? API?
 - What types of queries will be run on the data?
   - Preknown (canned) queries?
   - Exploratory (ahdoc) queries?
     - Slice and dice (data warehouse/mart style queries)?
     - Free text search?
 - Are the queries OLAP or OLTP?
 - What frequency will the queries be run?
 - What are the expectations around retrieval rate?
   - Number of records/second?
   - Mb/s?
 - What are the latency requirements?  I.e. how quickly do you expect to see your query data returned?
 - Can data be separated into hot data (newer) and cold data (older)?
   - Do the retrieval requirements change depending on the hotness? Can we take longer to query cold data?
 - What are the consistency requirements? https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/
 - What are the transaction isolation requirements? https://en.wikipedia.org/wiki/Isolation_(database_systems)
 - Do you have a specific data model that you require or is this flexible?
 

## Security


## Archival



# Project requirements

 - What are the goals of the project? POC? Pilot? Production?
 - What are the timelines?
 - Who
