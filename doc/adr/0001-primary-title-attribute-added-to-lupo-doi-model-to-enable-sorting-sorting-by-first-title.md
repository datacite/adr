# 1. primary_title attribute added to lupo Doi model to enable sorting by first title

## Status

Accepted

## Date

2023-01-18

## Context

[Fabrica improvements feature 7](https://github.com/datacite/datacite/issues/1518) called for title sorting by the DOI's first title. Because Elasticsearch has no sense of order in object/non-nested fields, we were unable to select the first value of an array in a sort using available Elasticsearch sort methods. These included:

1. A sort based on `titles.title.keyword`, ex. ```{ "titles.title.keyword": { order: "asc" } }```. This method does not reliably sort by first title.
2. A Painless sorting script that selected the first title upon the search request. The response time and unpredictable system load made this option untenable.

## Decision

We created a new attribute in the lupo Doi model called `primary_title` that selects the first title from the Doi's titles. This is wrapped in an array for interoperability with other applications that expect titles to be in an array. The `primary_title` attribute is added to the indexed JSON.

## Consequences

### Positive

Sort by first title functionality works and is performant.

### Negative

DataCite DOIs need to be reindexed for the sort to function. Adding a new computed attribute to the lupo Doi model increases complexity and points of failure. 

ADR Number: 1

