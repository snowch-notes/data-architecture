This document is in the public domain under the license: 

[![License: CC BY-NC-ND 4.0](https://img.shields.io/badge/License-CC%20BY--NC--ND%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

-----

# Introduction

This is a living document of typical [non-functional requirement](https://en.m.wikipedia.org/wiki/Non-functional_requirement) (NFR) questions for data intensive analytic architectures.

Data intensive analytic architectures are typically 'fed' with data from other systems such as OLTP systems.  The main data flow patterns for these architectures are:

TODO diagram
 
Analytic solution architectures are typically not the masters of the data they hold.  If data has incorrect values, it is usually updated in the upstream source system and these updates then trickle through to the analytics solution.


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
      - What does the load curve look like?  E.g. is it fairly flat and consistent, or does it vary a lot?
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
 - What is the data format/encoding?  E.g. avro, json, csv, parquet, xls
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
 - How can you verify that you have not ingested dirty (incorrect) data?
 - How can you verify that you have ingested all the required data?  E.g. 
   - Weekly row counts of records ingested cross-related to number of inserts/updates in source system
 - Who will build and maintain the data ingest process?
   - What are their core skills?
   - Will they be trained to provide necessary skills?
 - What is your development lifecycle, or are you flexible? e.g. [pause and resume with savepoints](https://ci.apache.org/projects/flink/flink-docs-release-1.4/setup/savepoints.html#overview)
 - Who will monitor the streams? 
 - What do they want to monitor?  [Flink example](https://ci.apache.org/projects/flink/flink-docs-release-1.4/monitoring/checkpoint_monitoring.html), [IBM Streaming Analytics](https://www.ibm.com/blogs/bluemix/2016/07/streaming-analytics-metrics-using-rest-api/)

## Predict

 - Do you have an existing machine learning model, or will a new one be created?
    - Existing model?
       - How was the model created?
          - Libraries/frameworks?  
          - Versions?
          - How big is the model?
    - Create a new one?
       - Do you have a preferences on libraries/frameworks to use?
 - How frequently will the model be refreshed?
 - How will it be refreshed? manually? automatically?
 - What is the business purpose of the model?
 - How is the business success of the model measured?  E.g. profit generated
 - Is the problem domain perceptual and requiring deep learning? E.g. working with audio, image, video
 - What is the type of problem?  E.g. regression, classification, collaborative filtering, ...
 - Is the prediction task batch or near real-time? 
 - Now ask the ingest questions for each batch/stream of data for prediction.
    
## Retrieval

**Introduction**

This section is to provide insight into the data access patterns to help decide what persistence technologies are appropriate along with data models and data formats.

**Questions**

 - How will client systems access the data?  JDBC? ODBC? API?
 - Who are the clients that execute the queries?  Systems (code) or humans?
 - What is the business purpose for the queries and what are the key requirements? E.g.
   - Financial reconcilliation report requiring high accuracy
   - A page hit counter that only needs to be approximate
   - Build/update machine learning model
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
 - What are the consistency requirements? [research paper](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/)
 - What are the transaction isolation requirements? [Wikipedia](https://en.wikipedia.org/wiki/Isolation_(database_systems))
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
   - What is the granularity of access permissions? E.g. Table, row, cell?
   
## Compliance

 - Will the system hold data that has compliance requirements, such as GDPR?
   - What are the requirements and how do you want to comply?  E.g.
     - Requirement: Right to be forgotten
     - Approach: Anonymise individual's PI data records

## Archival

 - When is data considered hot/cold?
 - Are the non-functional requirements different for hot/cold data?
 - Can hot/cold data be accessed differently?  E.g. 
    - hot in rdbms for lower latency retrieval
    - cold in object storage
    
## Extensibility

 - What environments do you typically have?  E.g. dev, test, stage, prod

# Project/commercial

This section lists the questions relating to the project.

 - What are the goals of the project? POC? Pilot? Production?
 - What are the timelines?
 - What is the budget for the project?
    - development budget?
    - running (production) budget?
    - maintenance (e.g. bug fix) budget?
 - Who are the team members and what are their responsibilities?
 - Who will maintain the solution in production?
 - Do you have technology preferences?  E.g.
    - hadoop vs rdbms
    - open source vs proprietary
 - How does the new system/functionality add to your competitive advantage and allow you to differentiate?
 - Are you going to develop in-house or outsource?
   - If outsourcing and the project is a differentiator:
      - how are you going to bring the skills/knowledge back inside?
      - How are you going to grow these skills/knowledge?  I.e. how are you going to grow your ability to differentiate and hence your competitive advantage?
