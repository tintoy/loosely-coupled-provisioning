# Loosely-coupled provisioning
A reimagining of the DD Cloud Services Platform provisioning system as a loosely-coupled task-runner / job-manager.

The platform's provisioning system consists of a controller and various product-specific resource providers.
The controller receives requests, tracks them as jobs, and distributes work to various providers.

While the distributed management system (DMS) itself has no knowledge of the provisioning system, the DMS provisioning connector acts as a mediator between the 2 systems.
Currently, it is tightly-coupled (mostly by necessity) to the DMS in terms of the information exchanged (for example, all provisioning jobs are expected to relate to a specific DMS entity).

In this prototype, I'll be assuming all the entity / job linkage is managed by the DMS provisioning connector, and implement the the provisioning system as:
* An API for creating, querying, and controlling jobs
* A controller that distributes the work for those jobs
* A callback API that can be used by consumers (such as the provisioning controller) to receive notifications of status changes relating to specific jobs.

A typical interaction would consist of:
* Provisioning Connector -> Provisioning API
```
  POST http://provisioning/jobs
  {
    jobType: "JobTypes/3",
    data: {
      foo: "bar",
      baz: 3
    },
    callback: {
      target: "http://dms/provisioning-notifications",
      types: [
        "status",
        "progress"
      ]
    }
  }
```
* Provisioning API -> Provisioning Queue
```
  Create job of type "JobTypes/3" with data { foo: "bar", baz: 3 }
```
* Provisioning API -> Provisioning Connector
```
  200 OK
  {
    jobId: "Jobs/17"
  }
```
 
