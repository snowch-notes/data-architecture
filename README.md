[![License: CC BY-NC-ND 4.0](https://img.shields.io/badge/License-CC%20BY--NC--ND%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

-----

# Introduction

# Data Requirements

## Ingest

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
 - Who (system) is/will be the master of the data?

## Retrieval

 - How will client systems access the data?  JDBC? ODBC? API?
 - What types of queries will be run on the data?
 
 - Can data be separated into hot data (newer) and cold data (older)?
   - Do the retrieval requirements change?

## Security


## Archival



# Project requirements

