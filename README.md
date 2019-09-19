# openshift-team-scoped-monitoring
Instructions for creating custom monitoring stacks on OpenShift scoped to specific teams resources.

## Purpose
The question was proposed, how to create a way for teams to use Prometheus and Grafana to monitor their projects but no one else's to keep separation of data. This document outlines how to do this.

## Tested On
This has only been tested on OpenShift 4.x, but in theory it should work the same on OpenShift 3.x.

## Steps

### Create the Prometheus Operator

Since OpenShift ships with a running version of the Prometheus Operator in the openshift-monitoring project we need to be sure to not conflict with that. Therefor thse instructions are modified from the documentation in https://github.com/coreos/prometheus-operator.

0. clone this repository
```
git clone https://github.com/itewk/openshift-custom-scoped-monitoring.git
cd openshift-custom-scoped-monitoring
```
1. log in as cluster admin
2. `oc project openshift-operators`
3. create the custom prometheus operator
```
oc create -f custom-prometehus-operator.yml -n openshift-operators
```
4. create the template for creating custom monitoring stacks
```
oc apply -f custom-monitoring-template.yml -n openshift
```

### Create Team Scopped Monitoring Stack

0. clone this repository
```
git clone https://github.com/itewk/openshift-team-scoped-monitoring.git
cd openshift-team-scoped-monitoring
```
1. log in as "normal" user
2. create team specific monitoring project
```
oc new-project team-MY_TEAM-monitoring
```
3. create prometheus resources
```
oc process openshift//custom-monitoring MONITOR_GROUP=team-a | oc create -f -
```

### Clean Up
#### clean up custom operators
```
oc delete -f custom-prometehus-operator.yml -n openshift-operators
```

#### clean up specific monitoring stack
```
oc process openshift//custom-monitoring MONITOR_GROUP=team-a | oc delete -f -
```
