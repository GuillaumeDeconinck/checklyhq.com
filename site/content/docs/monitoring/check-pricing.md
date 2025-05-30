---
title: Pricing & Billing - Checkly Docs
displayTitle: Pricing & Billing
navTitle: Pricing & Billing
weight: 9
menu:
   platform:
    parent: "Monitoring"
---

## Overview

Checkly supports different kinds of checks which are billed slightly differently:

| Check type | Base cost          | Parallel scheduling    | Retries             |
|------------|--------------------|------------------------|---------------------|
| Browser    | $5 per 1k runs     | Each location is a run | Each retry is a run |
| API    | $2 per 10k runs    | Each location is a run | Each retry is a run |
| TCP    | $2 per 10k runs    | Each location is a run | Each retry is a run |
| Multistep  | $2 per 10k requests| Locations × requests   | Retries × requests  |
| Heartbeat  | -                  | -                      | -                   |

> The check prices above are based on prepaid check bundles as shown on our [Pricing page](https://www.checklyhq.com/pricing/). If you are on a Checkly Enterprise contract, your checks' base cost might differ substantially from what is shown above.

> API, TCP, and Multistep checks all count towards the 'API & network' check run quota. For example, if your plan includes 100k API & network check runs, running any of those three check types will contribute to that 100k limit.

When configuring your check frequency and scheduling strategy, the cost helper will estimate the monthly cost for the check.

![cost helper widget](/docs/images/monitoring/price-helper.png)


## API & network checks, and browser checks

API & network checks, as well as browser checks, have a base price per check run. Always refer to our [pricing page](https://www.checklyhq.com/pricing/) for the latest prices. The base cost is typically listed as $X per 1k or 10k check runs.

> **Example:** If a browser check is running once per hour with a cost of $5 per 1000 check runs, the monthly base cost for the check would be $3.65.

If you use features that multiply the number of check runs, such as parallel scheduling and retries, your cost will increase.

### Parallel scheduling

When a check is running in [parallel](/docs/monitoring/global-locations/#parallel), it will run once on each selected location per execution. If you change a check from using the round-robin scheduling method to running in parallel, be aware that the cost will multiply by the number of locations you have selected.

Balance the number of locations the check is using to ensure you are quickly made aware of potential issues in critical locations without running an unnecessary amount of checks.

> **Example:** Continuing our example from earlier, if we want our browser check to run in parallel from 5 locations, the monthly cost will increase from $3,65 to $18,25.

### Retries

When a check is [retried](/docs/alerting-and-retries/retries/), this counts as a new check run. A flaky check can increase your check run costs above the expected. If you have problems with flaky checks, ask [our community](https://www.checklyhq.com/slack/) or our support for tips on how to improve check stability and reduce cost.

> **Example:** If our check from earlier has a 20% retry rate, this will increase the cost from $3.65 to $4.40.

## Multistep checks

[Multistep check](/docs/multistep-checks/) pricing is slightly different from browser and API checks. A Multistep check is billed based on the number of requests done per check run. Each request in a Multistep check run is billed as a single regular API check run, as they are performing the same basic operation. 

> **Example:** Let’s say you have 4 API checks, where each check doing one of the `GET`, `POST`, `PUT` and `DELETE` operations towards the same endpoint. If you replace these 4 checks with a single Multistep check that runs 4 requests towards the same endpoint, checking each method, and the check run frequency is the same as before, your cost stays the same.

A Multistep check with 0 requests is billed as if it has 1 request.

### Parallel scheduling 

When a Multistep check is run in [parallel](/docs/monitoring/global-locations/#parallel), the whole check (with any number of requests) is run from all included locations. That means that the cost is going to equal the number of locations the check is run from multiplied by the number of requests.

### Retries

As a Multistep check is [retried](/docs/alerting-and-retries/retries/) as a whole, the final cost of a retried Multistep check is based on the number of requests executed multiplied by the number of retries.

## Heartbeat checks

A set number of [heartbeat checks](/docs/heartbeat-checks/) are included in the team and enterprise plans. Checkly does not charge for heartbeat pings. 

> **Example:** If you decide to change the frequency of one of your heartbeat checks from 5 minutes to 30 seconds, there is no change in the cost you will incur.

If you have reached your maximum number of heartbeat checks and need more, contact our support.
