[![License](http://img.shields.io/:license-apache-blue.svg)](http://www.apache.org/licenses/LICENSE-2.0.html) [![Build Status](https://travis-ci.org/simp/pupmod-simp-openscap.svg)](https://travis-ci.org/simp/pupmod-simp-openscap) [![SIMP compatibility](https://img.shields.io/badge/SIMP%20compatibility-4.2.*%2F5.1.*-orange.svg)](https://img.shields.io/badge/SIMP%20compatibility-4.2.*%2F5.1.*-orange.svg)

#### Table of Contents

1. [Overview](#this-is-a-simp-module)
2. [Module Description - A Puppet module for managing openscap](#module-description)
3. [Setup - The basics of getting started with pupmod-simp-openscap](#setup)
    * [What pupmod-simp-openscap affects](#what-simp-openscap-affects)
    * [Setup requirements](#setup-requirements)
    * [Beginning with openscap](#beginning-with-openscap)
4. [Usage - Configuration options and additional functionality](#usage)
5. [Reference - An under-the-hood peek at what the module is doing and how](#reference)
5. [Limitations - OS compatibility, etc.](#limitations)
6. [Development - Guide for contributing to the module](#development)

## This is a SIMP module
This module is a component of the
[System Integrity Management Platform](https://github.com/NationalSecurityAgency/SIMP),
a compliance-management framework built on Puppet.

If you find any issues, they can be submitted to our
[JIRA](https://simp-project.atlassian.net/).

Please read our [Contribution Guide](https://simp-project.atlassian.net/wiki/display/SD/Contributing+to+SIMP)
and visit our [developer wiki](https://simp-project.atlassian.net/wiki/display/SD/SIMP+Development+Home).

This module is optimally designed for use within a larger SIMP ecosystem, but it
can be used independently:
* When included within the SIMP ecosystem, security compliance settings will be
managed from the Puppet server.
* In the future, all SIMP-managed security subsystems will be disabled by
default and must be explicitly opted into by administrators.  Please review
*simp/simp_options* for details.

## Module Description

This module sets up [openscap](https://www.open-scap.org/) and allows you to
schedule and log openscap runs.

## Setup

### What simp openscap affects

*simp/openscap* will manage:

* openscap-utils and scap-security-guide packages

*simp/openscap::schedule* will manage:

* A cron job for openscap runs
* A logging directory for openscap (Default: /var/log/openscap)

### Setup Requirements

The module can support logrotate if *simp/logrotate* is used. Otherwise, no
additional setup is required.

### Beginning with openscap

You can install openscap by:

```puppet
include openscap
```

## Usage

### I want to install openscap with default logging

The following will run a cron job on Monday at 1:30 AM and log to
/var/log/openscap:

```puppet
class {'openscap':
  enable_schedule => true,
}
```

OR

```puppet
include openscap::schedule
```

### I have a particular SCAP profile I want to use

```puppet
class {'openscap::schedule':
  scap_profile => 'xccdf_org.ssgproject.content_profile_stig-rhel7-server-upstream',
}
```

### I want to log daily at a set time

```puppet
class {'openscap::schedule':
  minute => 00,
  hour => 22,
  weekday => '*',
}
```

### I want to log on the first and fifteenth day of the month

```puppet
class {'openscap::schedule':
  monthday => '1,15',
}
```

### I want to log to a different directory

```puppet
class {'openscap::schedule':
  logdir => '/opt/scaplogs',
}
```

## Reference

### Public Classes
* ['openscap'](#openscap): Main Class
* ['openscap::schedule'](#openscapschedule): Sets a schedule for openscap to run
a check on your systems.

### `openscap`

#### Parameters

##### enable_schedule
  Includes the base `openscap::schedule` class.
  * Valid Options: true or false.
  * Default: false

### `openscap::schedule`

#### Parameters

##### scap_profile
  The name of the profile with the content.
  * Valid Options:

    ```
    Valid RHEL/CentOS 6 Choices:
      * xccdf_org.ssgproject.content_profile_test
      * xccdf_org.ssgproject.content_profile_CS2
      * xccdf_org.ssgproject.content_profile_common
      * xccdf_org.ssgproject.content_profile_server
      * xccdf_org.ssgproject.content_profile_stig-rhel6-server-upstream
      * xccdf_org.ssgproject.content_profile_usgcb-rhel6-server
      * xccdf_org.ssgproject.content_profile_rht-ccp
      * xccdf_org.ssgproject.content_profile_CSCF-RHEL6-MLS
      * xccdf_org.ssgproject.content_profile_C2S
    
    Valid RHEL/CentOS 7 Choice:
      * xccdf_org.ssgproject.content_profile_test
      * xccdf_org.ssgproject.content_profile_rht-ccp
      * xccdf_org.ssgproject.content_profile_common
      * xccdf_org.ssgproject.content_profile_stig-rhel7-server-upstream
    ```

  * Default: `xccdf_org.ssgproject.content_profile_stig-rhel${::operatingsystemmajrelease}-server-upstream`

##### ssg_base_dir
  The starting directory for all SSG content. Change this if you want to
install your own SSG profiles.
  * Valid Options: Absolute Path.
  * Default: `/usr/share/xml/scap/ssg/content`

##### ssg_data_stream
  The data stream XML file to use for your system scan. This must be a file
under $ssg_base_dir.
  * Valid Options: String ending in .xml.
  * Default: `ssg-rhel${::operatingsystemmajrelease}-ds.xml`

##### fetch_remote_resource
  If true, download remote content referenced by XCCDF.
  * Valid Options: true or false.
  * Default: `false`.

##### logdir
  Specifies output location.
  * Valid Options: Absolute Path.
  * Default: `/var/log/openscap`

##### logrotate
  If true, use `simp/logrotate` to rotate the output logs.
  * Valid Options: true or false.
  * Default: value of `simp_options::logrotate` or false.

##### minute
  Minute setting for logging cronjob.
  * Valid Options: see cron resource type.
  * Default: `30`.

##### hour
  Hour setting for logging cronjob.
  * Valid Options: see cron resource type.
  * Default: `1`.

##### monthday
  Day of Month setting for logging cronjob.
  * Valid Options: see cron resource type.
  * Default: `'*'`.

##### month
  Month setting for logging cronjob.
  * Valid Options: see cron resource type.
  * Default: `'*'`.

##### weekday
  Weekday setting for logging cronjob.
  * Valid Options: see cron resource type.
  * Default: `1`.

## Limitations

This module is only designed to work in RHEL or CentOS 6 and 7. Any other
operating systems have not been tested and results cannot be guaranteed.

# Development

Please see the
[SIMP Contribution Guidelines](https://simp-project.atlassian.net/wiki/display/SD/Contributing+to+SIMP).

General developer documentation can be found on
[Confluence](https://simp-project.atlassian.net/wiki/display/SD/SIMP+Development+Home).
Visit the project homepage on [GitHub](https://simp-project.com),
chat with us on our [HipChat](https://simp-project.hipchat.com/),
and look at our issues on  [JIRA](https://simp-project.atlassian.net/).
