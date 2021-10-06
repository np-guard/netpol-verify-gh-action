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
(Optional) The format in which to output verifitaion results. Either "md" (default) or "txt".
## Outputs
### policy-results-artifact
The name of the artifact containing verification results for all policies
### policy-results-file
The name of the actual file in the artifact, which contains verification results for all policies
### num-violated-policies
The number of corporate policies which the cluster's connectivity graph violates
## Usage examples
### Run and fail if a policy is violated
Can be used to block a pull-request from being merged.
```yaml
name: verify-network-policies
on:
  pull_request:

jobs:
  verify-netpols:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Verify policies
        id: verify-policies
        uses: shift-left-netconfig/netpol-verify-gh-action@v1
        with:
          corporate-policies: >
            https://github.com/shift-left-netconfig/baseline-rules/blob/master/examples/ciso_denied_ports.yaml
      - run: exit ${{ steps.verify-policies.outputs.num-violated-policies }}
```
### Add policy verification results as a PR comment
```yaml
name: verify-network-policies
on:
  pull_request:

jobs:
  verify-netpols:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Verify policies
        id: verify-policies
        uses: shift-left-netconfig/netpol-verify-gh-action@v1
        with:
          corporate-policies: >
            https://github.com/shift-left-netconfig/baseline-rules/blob/master/examples/ciso_denied_ports.yaml
            https://github.com/shift-left-netconfig/baseline-rules/blob/master/examples/restrict_access_to_payment.yaml
      - uses: actions/download-artifact@v2
        with:
          name: ${{ steps.verify-policies.outputs.policy-results-artifact }}
      - name: comment PR
        uses: machine-learning-apps/pr-comment@1.0.0
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          path: ${{ steps.verify-policies.outputs.policy-results-file }}
```
This should output something like this:
![](/PR-comment.png)
