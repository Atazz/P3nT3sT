# Whois

On Unix-like operating systems, the whois command is a client for the WHOIS directory service.

## Description
whois searches for an object in a WHOIS database. WHOIS is a query and response protocol that is widely used for querying databases that store the registered users of an Internet resource, such as a domain name or an IP address block, but is also used for a wider range of other information.

Most modern versions of whois try to guess the right server to ask for the specified object. If no guess can be made, whois will connect to whois.networksolutions.com for NIC handles or whois.arin.net for IPv4 addresses and network names.

## Service

<a id="domain-name-query"></a>
### Domain name query

This is an example of a standard inquiry for a domain name.

The constraints on a domain name in the .dk zone is described in the [DK Hostmaster Name Service Specification][DKHMNSDOM].

<a id="example-query-for-domain-name-information"></a>
#### Example query for domain name information

##### Request

We inquire about the domain name: `eksempel.dk`

```bash
$ whois eksempel.dk
```

##### Response

The standard response look as follows:

```bash
# Hello XX.XX.XX.XX. Your session has been logged.
#
# Copyright (c) 2002 - 2021 by DK Hostmaster A/S
#
# Version: 5.0.0
#
# The data in the DK Whois database is provided by DK Hostmaster A/S
# for information purposes only, and to assist persons in obtaining
# information about or related to a domain name registration record.
# We do not guarantee its accuracy. We will reserve the right to remove
# access for entities abusing the data, without notice.
#
# Any use of this material to target advertising or similar activities
# are explicitly forbidden and will be prosecuted. DK Hostmaster A/S
# requests to be notified of any such activities or suspicions thereof.

Domain:               eksempel.dk
DNS:                  eksempel.dk
Registered:           1999-05-17
Expires:              2022-06-30
Registration period:  5 years
VID:                  yes
DNSSEC:               Signed delegation
Status:               Active

Nameservers
Hostname:             auth01.ns.dk-hostmaster.dk
Hostname:             auth02.ns.dk-hostmaster.dk

# Use option --show-handles to get handle information.
# whois -h whois.dk-hostmaster.dk HELP for more help.
```

```
## Hello XX.XX.XX.XX. Your session has been logged.
```

The IP address has been masked for the example, As stated all request are logged.

```
## Copyright (c) 2002 - 2021 by DK Hostmaster A/S
```

Copyright notice.

```
## Version: 5.0.0
```

This is the version string of the service. The service uses [semantic versioning][SEMVER], so this is major release `3`, No feature or bug releases has been made indicated by the minor release indicator: `0` and the patch release indicator:`0`.

```
## The data in the DK Whois database is provided by DK Hostmaster A/S
## for information purposes only, and to assist persons in obtaining
## information about or related to a domain name registration record.
## We do not guarantee its accuracy. We will reserve the right to remove
## access for entities abusing the data, without notice.
#
## Any use of this material to target advertising or similar activities
## are explicitly forbidden and will be prosecuted. DK Hostmaster A/S
## requests to be notified of any such activities or suspicions thereof.
```

Terms of use notice.

```
Domain:               eksempel.dk
DNS:                  eksempel.dk
Registered:           1999-05-17
Expires:              2022-06-30
Registrar:            All Things DK Domains
Registration period:  5 years
VID:                  yes
DNSSEC:               Signed delegation
Status:               Active

Nameservers
Hostname:             auth01.ns.dk-hostmaster.dk
Hostname:             auth02.ns.dk-hostmaster.dk
```

Then we get to the data.

| Field | Description |
| ----- | ----------- |
| `Domain` | The domain name, should match the one enquired about |
| `DNS` | Version of the domain name inquired used in DNS, punycode for IDNA domain names [RFC:5891] |
| `Registered` | Date of registration in [ISO-8601] format: `YYYY-MM-DD`, the timezone is not expressed explicitly. The local time of the registry is used, meaning Central European Standard Time (`GMT+1`), Copenhagen/Denmark |
| `Expires` | Date of expiration in [ISO-8601] format: `YYYY-MM-DD`, the timezone is not expressed explicitly. The local time of the registry is used, meaning Central European Standard Time (`GMT+1`), Copenhagen/Denmark |
| `Registrar` | This field is available if the inquired domain name is handled by a registrar, the field is omitted if the domain name is registrant handled, for more information see the chapter on "Registrar Collaboration Model" |
| `Delete date` | Date indicating deletion in [ISO-8601] format: `YYYY-MM-DD`, the timezone is not expressed explicitly. The local time of the registry is used, meaning Central European Standard Time (`GMT+1`), Copenhagen/Denmark. Do note this field is only available if is has been set |
| `Registration period` | Registration period (`1`, `2`, `3` or `5` years) |
| `VID` | Indication whether VID service is active, values either `yes` or `no` |
| `DNSSEC` | Indication whether DNSSEC service is active, values either `Signed delegation`, `Unsigned delegation, DNSSEC disabled, no records`, `Unsigned delegation, DNSSEC disabled, keys unpublished`, `Unsigned delegation, DNSSEC disabled`, `Unsigned delegation, no records`, `Unsigned delegation, DNSSEC enabled, keys unpublished` or `Unknown status` |
| `Status` | Status of the domain name, please see the appendix |
| `Nameservers` | List of name servers, serving the inquired domain name |