# Scan Types

A type follows the format: `indicator_rule`  
Types are lowercase alpha characters, except for a single underscore splitting the `indicator` and `rule`.

An `indicator` is a keyword or keyphrase to distinguish different scan intentions (see below examples).

The `rule` MUST be either `active` or `passive` which describes the impact.

## Rule

### Passive
`passive` scanning is anything with a very low impact potential, meaning it is very unlikely to cause any disruption, alerting or data loss. An example is a crawler running at 1 request per second performing HTTP GET requests to capture the content with no exploit attempts.

### Active
`active` scanning is where there could be an impact to the targeted system, meaning it may cause disruption to legitimate users, it may trigger alerts and it may impact data integrity or confidentiality. An example is attempting to exploit a known vulnerability to assess whether a system is vulnerable. Another example is load testing where legitimate users may be prevented from accessing.

## Examples
Including an example of what that might be.
- `vulnerability_active` - exploit attempts
- `vulnerability_passive` - version detection for out-of-date software or libraries
- `configuration_passive` - assessing the configuration for alignment to a standard
- `accessibility_passive` - testing a webpage for accessibility issues
- `seo_passive` - testing a webpage for SEO (Search Engine Optimisation)
- `banner_passive` - software identification
- `indexer_passive` - content indexing for text searching
- `crawler_passive` - following links and indexing content
- `performance_active` - slow-link testing or dropping connections to test the servers response
- `performance_passive` - assessing the performance of a standard user's experience
- `loadtesting_active` - stress testing or simulated denial-of-service
- `testing_active` - general testing with a likely impact
- `testing_passive` - general testing that has a very low impact
