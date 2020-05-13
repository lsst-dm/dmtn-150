:tocdepth: 1

.. sectnum::

This document is a proposal for an engagement to verify that a cloud deployment of the LSST Data Management (DM) system (including both Alert Production and Data Release Production) at Interim Data Facility (IDF) scale is feasible, measure its performance, determine its final discounted cost, and investigate more-native cloud options for handling system components that may be costly to develop or maintain.

Goals
=====

A Proof of Concept (PoC) engagement has been identified to address the following use-cases/goals:

- Leveraging the existing Rubin private 10Gbit/sec link between Chile and Miami, set up an interconnect between the Rubin and Google networks in Miami, then validate that we can upload image files of 7-12GB from Chile to a Google Cloud Storage (GCS) bucket hosted in the us-central region or to distributor hosts in Google Compute Engine (GCE)/Google Kubernetes Engine (GKE) on the cadence necessary for Alert Production (less than 7 seconds transmission time, repeated every 17 seconds).

- Setup the Alert Production and Data Release Production pipelines in Google Cloud Platform (GCP), and validate they can use GCS as file storage and work properly, similar to what was documented in :cite:`DMTN-137`.

- Run desired batch jobs on preemptible instances and perform cost analysis for the Interim Data Facility (pre-Operations production) scale and for the 10-year Operations scale.


Target Architecture
===================

:numref:`fig-gcp-arch` shows the high-level architecture for the entire deployment.
For this PoC, we will be primarily testing data transmission from Chilean to Cloud storage and then running Alerts and DRP in GCP.
The Rubin Science Platform and Qserv database were already tested in the 2018 PoC :cite:`DMTN-125`.

.. figure:: /_static/gcp-arch.png
   :name: fig-gcp-arch

   High-level architecture for DM systems in Google Cloud Platform.

Alert Production
----------------

Alert Production will be executed on GKE nodes with GCE nodes as a backup.
For the PoC, the target APDB will be PostgreSQL.
Alert Distribution is not a key component for the PoC as it has already been tested in the cloud.

Transmission
^^^^^^^^^^^^

Transmission of images from La Serena to Google Cloud is a key item to be tested.
The goal is to test at full speed over a private 10 Gbit/sec interconnect between the Rubin international network and the Google Cloud network.
This connection will be provisioned in Miami, where the two networks are present in the same building.
The destination of the images will be Google Cloud Storage (for archiving, and, if sufficiently performant, for Prompt Processing) and the Prompt Processing execution nodes.

Alert Pipeline
^^^^^^^^^^^^^^

The ``ap_pipe`` pipeline will be used along with DECam data used for ``ap_verify``.


End-to-End Demonstration
^^^^^^^^^^^^^^^^^^^^^^^^

If possible, an end-to-end demonstration using images from ComCam at the Base and a limited ``processCcd`` pipeline will be executed.


Data Release Production
-----------------------

Data Release Production will execute on GCE nodes under HTCondor or under a managed GCP workflow service.
Data will be taken from GCS and the Butler databases will be on PostgreSQL.

Datasets
^^^^^^^^

We will run at HSC RC2 scale to start.
If possible, we will scale up to PDR2 or a similarly sized dataset from DC2 to demonstrate that there are no bottlenecks at full pre-Ops production scale.

Pipelines
^^^^^^^^^

The full set of pipelines used for biannual production will be used.

Middleware
^^^^^^^^^^

Gen3 middleware will be used.


Proposed Phases
===============

Phase 1a: PoC Project Onboarding
--------------------------------

Owners
^^^^^^

K.T. + Vinod/Dom/Flora

Milestones 
^^^^^^^^^^

Be able to complete project onboarding and test upload via public internet.

Tasks
^^^^^

- [Both] PoC Project onboarding: (O)Org, Billing, Credit, Project, IAM, VPC, GCS, etc.

- [Both] Plan interconnect details

- [LSST] Prepare testing data

- [Both] Create a GCS client using SDK.

- [LSST] Test upload to GCS:
  - gsutil
  - custom client
  - transfer services

- [LSST] Transfer small datasets to GCS


Phase 1b: Setup DRP
-------------------

Owners
^^^^^^

Hsin-Fang + Karan/Ross/Flora

Milestones
^^^^^^^^^^

Be able to run DRP with GCS as file storage

Tasks
^^^^^

- [Both] Port DRP and Gen3 Data Butler to GCP, including any needed adaptations to boto, PostgreSQL

- [LSST] Execute DRP at small scale

Phase 1c: Setup Alerts
----------------------

Owners
^^^^^^

K.T + Dom/Karan/Ross/Flora

Milestones
^^^^^^^^^^

Be able to run Alerts with GCS as file storage and/or with a custom distributor service

Tasks
^^^^^

- [Both] Re-architect Alerts to use GCS and deploy it in GCP

- [LSST] Develop custom distributor service for Alert input and deploy it in GCP

- [LSST] Execute Alert Production on pre-positioned test data

Phase 2a: Execute DRP at scale and perform cost analysis
--------------------------------------------------------

Owners
^^^^^^

Hsin-Fang + Dom/Karan/Ross/Flora

Milestones
^^^^^^^^^^

Be able to execute desired batch jobs on preemptive instances and perform cost analysis.

Tasks
^^^^^

- [Both] Configure GCE cluster for the desired batch job.

- [Google] Quota/limit adjustment for the PoC project per the testing target.

- [Both] Perform any needed adaptations to HTCondor, for obtaining preemptible nodes, etc.

- [Both] Perform cost analysis

Phase 2b: Network Validation
----------------------------

Owners
^^^^^^

K.T./Jeronimo + Vinod/Flora

Milestones
^^^^^^^^^^

Be able to upload 7-12GB data within <7s from Chilean to GCS bucket hosted in us-central repeatedly (every 17 sec)

Tasks
^^^^^

- [Both] Interconnect setup

- [LSST] Prepare testing data and hosts

- [LSST] Test upload to GCS via interconnect
  - gsutil
  - custom client
  - transfer services


Phase 3: End-To-End Alerts
--------------------------

Owners
^^^^^^

K.T.+ Vinod/Karan/Ross/Flora

Milestones
^^^^^^^^^^

Be able to run Alerts end-to-end with data from Chile

Tasks
^^^^^

- [Both] Integrate data transfer mechanism with Alerts

- [LSST] Execute Alert Production on “live” test data

- [LSST] Stretch goal: execute prompt calibration processing pipeline on live ComCam calibration data


Phase 4: PoC Presentation to technical and management stakeholders
------------------------------------------------------------------

Owner
^^^^^

The entire Google account team

Milestones
^^^^^^^^^^

Executive presentation and report

Tasks
^^^^^

- [Both] Prepare presentation

- [LSST] Prepare DMTN report

- [Both] Present presentation


Success Criteria
================

Validate the architecture will work on GCP and meet its required service level.

Validate the deployment could scale from small (PoC/IDF) to full (10yr goal); not planning to test 10yr goal, but want to gather enough data points to do reasonable analysis.

Validate that GCP is cost efficient.

Validate that GCP is easy to work with.


Reports and Conclusion
======================

A report will be produced as a DMTN.  A presentation will be given to Rubin Observatory Operations and Construction management and technical personnel summarizing the results and conclusions.

.. rubric:: References

.. bibliography:: local.bib lsstbib/books.bib lsstbib/lsst.bib lsstbib/lsst-dm.bib lsstbib/refs.bib lsstbib/refs_ads.bib
   :style: lsst_aa
