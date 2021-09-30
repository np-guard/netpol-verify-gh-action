# Verify cluster connectivity against corporate policies

## About
This action checks the connectivity in a given Kubernetes cluster against a set of corporate policies. It will first extract the cluster's connectivity graph by scanning your repository for YAML files containing endpoint resources (e.g., Deployments) or connectivity resources (Kubernetes NetworkPolicies). It will then verify that the connectivity graph adheres to a set of corporate policies, given as the action's input. Corporate policies are defined in YAML files; their syntax is defined [here](https://github.com/shift-left-netconfig/baseline-rules).

This action is part of a wider attempt to provide [shift-left automation for generating and maintaining Kubernetes Network Policies](https://shift-left-netconfig.github.io/).

## Inputs
### corporate-policies
A space-separated list of corporate policy files to verify (either as GitHub URLs or paths under GitHub workspace)
### deployment-path
(Optional) The path in the GitHub workspace where deployment yamls are. Default is . (scanning the whole repository).
### netpol-path
(Optional) The path in the GitHub workspace where the NetworkPolicy yamls are stored. Default is . (scanning the whole repository).
### output-format
(Optional) The format in which to output verifitaion results. Either "md" (deafult) or "txt".
## Outputs
### policy-results-artifact
The name of the artifact containing verification results for all policies
### policy-results-file
The name of the actual file in the artifact, which contain verification results for all policies
## Examples usage
