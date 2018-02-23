[![License: CC BY-NC-ND 4.0](https://img.shields.io/badge/License-CC%20BY--NC--ND%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

-----

# Introduction

This is a living document of typical [non-functional requirement](https://en.m.wikipedia.org/wiki/Non-functional_requirement) (NFR) questions for data intensive architectures.

# Data Requirements

This section lists the questions relating to the solution.

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
 - What is the data format?  E.g. avro, json, csv, parquet, xls
 - Can you share a sample of data?
 - Can you share a schema for the data and files?
 - Can you provide some context for this source of data?  E.g. 
    - Where is the data coming from?
    - How is it generated?
 - Who is/will be the master of the data? E.g.
    - Is it a source relational database that we are performing change data capture from?
    - Is *this* system that we are building going to be the master?
    - How can we detect incremental changes in the source system? E.g.
      - Specialised off the shelf change data capture tools such as [InfoSphere CDC](https://www.ibm.com/support/knowledgecenter/en/SSX3HK_6.5.2/com.ibm.cdcdoc.mcadminguide.doc/concepts/overview_of_cdc.html)
      - Manually select changes based on timestamp or row versioning in RDBMS? [Wikipedia](https://en.m.wikipedia.org/wiki/Change_data_capture)
      - Monitor filesystem folder for new files
 - How is the data model expected to change over time?  I.e. do we need to use a format that is resistent to schema changes (such as avro)? 
 - Who will build and maintain the data ingest process?
   - What are their core skills?
   - Will they be trained to provide necessary skills?

## Retrieval

**Introduction**

This section is to provide insight into the data access patterns to help decide what persistence technologies are appropriate along with data models and data formats.

**Questions**

 - How will client systems access the data?  JDBC? ODBC? API?
 - Who are the clients that execute the queries?  Systems (code) or humans?
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
   - Can we denormalise data at write time to reduce reads at query time? 

## Resilience

 - What are the availability requirements?  E.g. HA with DC, HA across DC
    - for the solution?
    - for individual parts of the solution?

## Security

 - What are your backup requirements?
    - Recovery point objective (RPO)? [wikipedia](https://en.wikipedia.org/wiki/Recovery_point_objective)
    - Recovery time objective (RTO)? [wikipedia](https://en.wikipedia.org/wiki/Recovery_time_objective)
 - What are the confidentiality requirements (e.g. encryption)?
 - What are the network security requirments (e.g. VPC/VPN)?
 - Are there any access log/auditing requirements?
 - How do you want to control access to the data? E.g.
   - Role-based access control (RBAC) [wikipedia](https://en.m.wikipedia.org/wiki/Role-based_access_control)

## Archival

 - When is data considered hot/cold?
 - Are the non-functional requirements different for hot/cold data?
 - Can hot/cold data be accessed differently?  E.g. 
    - hot in rdbms for lower latency retrieval
    - cold in object storage

# Project requirements

This section lists the questions relating to the project.

 - What are the goals of the project? POC? Pilot? Production?
 - What are the timelines?
 - Who are the team members and what are their responsibilities?
 - Do you have technology preferences?  E.g.
    - hadoop vs rdbms
    - open source vs proprietary
