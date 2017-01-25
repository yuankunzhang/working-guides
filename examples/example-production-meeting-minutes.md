# Example Production Meeting Minutes

**Date:** 2015-10-23

**Attendees:** agoogler, clarac, docbrown, jennifer, martym

**Announcements:**

* Major outage (#465), blew through error budget

## Previous Action Item Review

* Certify Goat Teleporter for use with cattle (bug #1011101)
  * Nonlinearities in mass acceleration now predictable, should be able to target accurately in a few days.

## Outage Review

* New Sonnet (outage #465)
  * 1.21B queries lost due to cascading failure after interaction between latent bug (leaked file descriptor on searches with no results) + not having new sonnet in corpus + unprecedented & unexpected traffic volume
  * File descriptor leak bug fixed (bug #5554825) and deployed to prod
  * Looking into using flux capacitor for load balancing (bug #5554823) and using load shedding (bug #5554826) to prevent recurrence
  * Annihilated availability error budget; pushes to prod frozen for 1 mongth unless docbrown can obtain exception on grounds that event was bizarre & unforeseeable (but consensus is that exception is unlikely)

## Paging Events

`AnnotationConsistencyTooEventual`: paged 5 times this week, likely due to cross-regional replication deplay between Bigtables.

* Investigation still ongoing, see bug #4821600
* No fix expected soon, will raise acceptable consistency threshold to reduce unactionable alerts

## Nonpaging Events

* None

## Monitoring Changes and/or Silences

* TODO(martym): `AnnotationConsistencyTooEventual`, acceptable deplay threshold raised from 60s to 180s, see bug #4821600

## Planned Production Changes

* USA-1 cluster going offline for maintenance between 2015-10-29 and 2015-11-02.
  * No response required, traffic will automatically route to other clusters in region.

## Resources

* Borrowed resources to respond to sonnet++ incident, will spin down additional server instances and return resources next week
* Utilization at 60% of CPU, 75% RAM, 44% disk (up from 40%, 70%, 40% last week)​

## Key Service Metrics

* **OK** 99ile latency: 88 ms < 100 ms SLO target [trailing 30 days]
* **BAD** availability: 86.95% < 99.99% SLO target [trailing 30 days]

## Discussion / Project Updates

* Project Moliere launching in two weeks.

## New Action Items

* TODO(martym): Raise `annotationConsistencyTooEventual` threshold.
* TODO(docbrown): Return instance count to normal and return resources.

