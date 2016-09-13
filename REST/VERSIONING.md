# REST API Versioning

## Summary


This document covers the convention for versioning a set of API endpoints for a given API Product. Versioning is the method by which implementers can reference a specific request/response implementation of a given API product. An API product is defined as a set of API endpoints that are functionally grouped (i.e. AIR APIs).

## List of Topics* Naming Convention
* Release Planning
* Partner and Internal APIs
* NIPRNet/SIPRNet

## Naming Convention
First and foremost, APIs shall never be released without a version number. Versioning is a mandatory practice to enable application developers to rely on a stable release of an API product. Versioning is done at the API product level and not on individual endpoints within an API product.
Each API version shall correlate directly to a production release of an API product. Version names shall be whole number integers preceded by the letter v. There is no division of major and minor releases and no indication in the name using decimal points (such as v2.6). As an example of the proper version naming convention, the first 3 versions of a sample API product are listed below:

```
https://domain.mil/api/v1
https://domain.mil/api/v2
https://domain.mil/api/v3
```

Below is the proper way to express the version in the API URL. Firstly, the api directory can be the highest level directory in the URL (should be implemented this way if possible) and should always directly precede the version. Example:

```
https://domain.mil/api/[VERSION]
```

Intermediate and/or development API products do not need to be versioned separately; therefore, version such as v4-beta and v2-RC6 (Release Candidate 6 for version 2) should not exist at any time. Any ambiguity in releases should be handled with up-to-date documentation.

## Release Planning

Other than the naming convention of an API product, many other factors need to be considered when a new version is planned for release. Among these are:

* Frequency of releases
* Backwards compatibility
* Deprecation of older versions
* Number of concurrent versions

### Frequency of Releases

There shall be no minimum or maximum amount of time that a version of an API will go before it needs to be updated. It is at the discretion of the project management team, the systems engineering team, the implementation team, and the driving business factors of the project that dictate when a new release of an API product is necessary.
Keep in mind that each release of an API product has business impact on each integrator. Each version shall be released to produce the maximum capability the driving business cases warrant with the smallest impact possible on existing and future integrators.

### Backwards Compatibility

The driving distinction between sequential versions is that the new version shall introduce features that break backwards compatibility with the current version. In fact, if a modification is made to a version and all existing capabilities are backwards compatible, no new version will be generated, and the new revision shall be released under the same name. This includes:

* Bug fixes of the current version of the API product
* New API endpoints
* Additional parameters for an API endpoint request
* Additional elements in an API endpoint response body

However, when a single existing capability is disrupted, disabled, or altered, a new version shall be issued. This includes:

* Changing the API Endpoint URL
* Removing an API Endpoint
* Altering or removing existing parameters for an API endpoint request
* Altering the existing response for an API endpoint

### Deprecation of Older Versions

API deprecation is an announcement that currently supported functionality shall no longer be supported (as of a certain date in the future). During the period of deprecation, existing integrators shall be given time to update their implementation to use the newer released versions and the capabilities within.
When a new version is released, any accompanying deprecations shall also be announced (if any exist). Deprecations may be made at any time for any existing and supported version of the API product (with the exception of the latest and most current version).
No API product deprecation shall be less than 6 months.
Once the period of deprecation has expired, the deprecated version of the API product shall be removed from the production environment.

### Number of Concurrent Versions

There shall be no maximum number of concurrent versions that an API product will support. It is at the discretion of the project management team, the systems engineering team, the implementation team, and the driving business factors of the project that determine when a older versions of an API product should be deprecated and how many concurrent versions will be supported.
Keep in mind that each release of an API product has business impact on each integrator. Each version shall be released to produce the maximum capability the driving business cases warrant with the smallest impact possible on existing and future integrators.

## Partner and Internal APIs

As API products are developed, often some APIs are built to facilitate an orchestration of data and/or an internal process, but then they are not released for partner consumption. We refer to these APIs as Internal APIs. All other APIs that are deployed that may be used by integrating applications are called Partner APIs.
The context of the versioning scheme mentioned in this document only refers to the versioning of Partner APIs. All Internal APIs may be versioned in whatever method makes sense to the project management team, the implementation team, and the driving business factors.

## NIPRNet/SIPRNet

As API products are deployed across the various secure networks within the DoD (or any external deployment networks), a divergence in functionality of the hosted API products is natural. As an example, some capabilities provided on SIPRNet may not also be provided on NIPRNet, and vice versa.
For a consistent experience amongst application developers that may integrate with the APIs across more than one secure network, the APIs shall be versioned using the same version counter across all of the deployed secure networks. If a new version of an API product will break backwards compatibility on any secure network, a new version shall be issued; however, the new version will only be deployed onto the networks where the API Product has changed (i.e. if the changes affected SIPRNet only, then the changes will not be deployed onto NIPRNet... and vice versa).
Where possible, API endpoints should be consistent across all secure networks.Deprecation of existing versions of an API product shall also be consistent across all secure networks.
