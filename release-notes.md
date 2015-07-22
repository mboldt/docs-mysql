---
title: Release Notes
---

## 1.5.0

- **AWS support:** The clustered database service can now be deployed on Amazon Web Services from the Operations Manager Web UI.
  - Deployment is limited to a single Availability Zone. Look for multi-AZ in future releases.
  - Single availability zone is a limitation on AWS. Operations Manager on vSphere continues to support deployment to multiple availability zones.
  - The default instance type for the cluster nodes on AWS is m3.large.
  - All jobs are deployed with SSD for ephemeral and persistent disk.
- **IaaS agnostic**
  - The same product can be deployed to both AWS and vSphere
  - Precompiled packages are no longer included
  - p-mysql 1.5.0 requires Operations Manager 1.4.0
- **New proxy tier** 
  - Improved availability: We have entirely re-written the proxy to eliminate situations where clients could hang when a cluster node was unhealthy. 
  - A dashboard that clearly displays node health in real time
- **Upgrade support:** This product can be automatically upgraded from version 1.3.2 or 1.4.0 
- **Cluster node resources increased for vSphere**: The default resources are now 4GB RAM, 2 CPU, 10GB persistent disk
- **Faster compilation:** Default resource for the compilation jobs on vSphere are now 4GB RAM, 4 CPU, 20GB persistent disk
- **Bug fix:** Fix broker-deregistrar errand to succeed even when MySQL service is broken
- **Bug fix:** Quota enforcer could fail when broker hasn't finished initializing

**Known issues:**

* On AWS, this version supports deployments in the US-East region. Multi-region support is coming in a future release.
* The experimental HTTPS-only feature in Elastic Runtime 1.5 may cause issues with this version of the product. Full support for HTTPS-only traffic is coming in a future release.
* Note: BOSH Stemcell 2865.1 is required for installation on Ops Manager 1.5.x and above.

## 1.4.0

- **High Availability**: database server is now clustered and synchronously replicated using MariaDB Galera Cluster. A copy of each database resides on all cluster nodes, and writes to any database are replicated to all copies. All client connections are routed to a primary cluster node, and in the event of a node failure the proxy tier manages failover, routing client connections to a healthy cluster node. MySQL server, proxy, and broker jobs can all be scaled out horizontally for increased availability, eliminating single points of failure.
- **Improved logging and monitoring**: route-registration on the broker is now an independent process
- **Bug fix**: calculation of storage utilization for the purposes of quota enforcement when multiple apps are bound
- **Bug fix**: format of jdbcUrl connection string (found in VCAP_SERVICES on bind)

    ### Notes on High Availability ###

- When upgrading from an older version, applications must be rebound to take advantage of high availability features. To rebind: unbind your application from the service instance, bind it again, then restage your application. For more information see [Managing Service Instances with the CLI](/pivotalcf/devguide/services/managing-services.html).
- Elimination of the proxy as a single point of failure requires configuration of an external load balancer to route connections to proxy instances. For details, see [Proxy Settings](/p-mysql/index.html#proxy).
- See [Known Issues](/p-mysql/#known-issues).

## 1.3.2

- **Updated stemcell addresses bash-shellshock vulnerabilities**: resolves CVEs discussed [here](http://www.pivotal.io/security/CVE-2014-6271) and [here](http://www.pivotal.io/security/CVE-2014-7186).

## 1.3.0

- **Syslog forwarding**: Syslogs are now streamed to the same host and port configured in Elastic Runtime settings
- **Dynamic instance capacity management**: Previously operators had to manually configure the maximum number of service instances permitted by the server. This required manual calculation and a knowledge of required system headroom. Admins can now manage instance capacity simply by adjusting persistent disk allocated to mysql nodes. Remaining instance capacity is determined dynamically by subtracting a safe estimate for system headroom and reserved storage for provisioned instances.
- **Trusty stemcell**: Server and broker are now deployed on Ubuntu “Trusty” 14.04 LTS stemcells, providing improved security, performance, and a smaller resource footprint.
- **Least necessary privileges**: The MySQL service dashboard uses a new, limited permission OAuth scope to determine whether a user currently has access to a service instance. The dashboard no longer has full read access to a user’s account.
- **Precompiled packages**: Most packages have been precompiled for the targeted stemcell. This will lower initial deployment times, at the cost of a larger download.

## 1.2.0

- Product renamed to 'MySQL for Pivotal CF'
- Plan attributes are configurable: max storage per database, max concurrent connections per user, and max databases
- Plan name is determined dynamically based on configured storage quota
- Plan features include disclaimer that the service is not for production use
- Developers can SSO to a service dashboard that displays storage utilization
- Security fixes including updates to Rails
- Service broker is registered by URL (rather than by IP). Typically has the format `https://p-mysql.<cf-domain>`.
* Lifecycle errands are used to register the broker and run tests that verify the deployment.
* Improved logging in service broker

### The following components will be re-deployed:

* cf-mysql-broker
* mysql

### New components:

* broker-registrar
* broker-deregistrar
* acceptance-tests

## 1.1.0

* Updated the format of metadata fields in the broker catalog endpoint and added
additional fields. For more information, see Catalog Metadata.
* Updated Ruby to version 2.0.0p353 to fix a vulnerability in 1.9.3p448.
* Requests to delete a service instance or binding now get a 200 response with an empty
JSON body instead of a 204.
* The broker now returns a clear error when there is no more capacity for additional
instances during a provision request. The response has status code `507`. The
user-facing error message is "Service plan capacity has been reached."

### The following components will be re-deployed:
* cf-mysql-broker
* mysql