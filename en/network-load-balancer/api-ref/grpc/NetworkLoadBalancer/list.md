---
editable: false
sourcePath: en/_api-ref-grpc/loadbalancer/v1/api-ref/grpc/NetworkLoadBalancer/list.md
---

# Network Load Balancer API, gRPC: NetworkLoadBalancerService.List {#List}

Retrieves the list of NetworkLoadBalancer resources in the specified folder.

## gRPC request

**rpc List ([ListNetworkLoadBalancersRequest](#yandex.cloud.loadbalancer.v1.ListNetworkLoadBalancersRequest)) returns ([ListNetworkLoadBalancersResponse](#yandex.cloud.loadbalancer.v1.ListNetworkLoadBalancersResponse))**

## ListNetworkLoadBalancersRequest {#yandex.cloud.loadbalancer.v1.ListNetworkLoadBalancersRequest}

```json
{
  "folderId": "string",
  "pageSize": "int64",
  "pageToken": "string",
  "filter": "string"
}
```

#|
||Field | Description ||
|| folderId | **string**

Required field. ID of the folder that the network load balancer belongs to.
To get the folder ID, use a [NetworkLoadBalancerService.List](#List) request. ||
|| pageSize | **int64**

The maximum number of results per page to return. If the number of available
results is larger than `pageSize`,
the service returns a [<ResponseMessage>.next_page_token]
that can be used to get the next page of results in subsequent list requests.
Default value: 100. ||
|| pageToken | **string**

Page token. To get the next page of results, set `pageToken` to the
[ListNetworkLoadBalancersResponse.nextPageToken](#yandex.cloud.loadbalancer.v1.ListNetworkLoadBalancersResponse) returned by a previous list request. ||
|| filter | **string**

A filter expression that filters resources listed in the response.
The expression must specify:
1. The field name. Currently you can only filter by the [NetworkLoadBalancer.name](#yandex.cloud.loadbalancer.v1.NetworkLoadBalancer) field.
2. An `=` operator.
3. The value in double quotes (`"`). Must be 3-63 characters long and match the regular expression `[a-z][-a-z0-9]{1,61}[a-z0-9]`. ||
|#

## ListNetworkLoadBalancersResponse {#yandex.cloud.loadbalancer.v1.ListNetworkLoadBalancersResponse}

```json
{
  "networkLoadBalancers": [
    {
      "id": "string",
      "folderId": "string",
      "createdAt": "google.protobuf.Timestamp",
      "name": "string",
      "description": "string",
      "labels": "string",
      "regionId": "string",
      "status": "Status",
      "type": "Type",
      "sessionAffinity": "SessionAffinity",
      "listeners": [
        {
          "name": "string",
          "address": "string",
          "port": "int64",
          "protocol": "Protocol",
          "targetPort": "int64",
          "subnetId": "string",
          "ipVersion": "IpVersion"
        }
      ],
      "attachedTargetGroups": [
        {
          "targetGroupId": "string",
          "healthChecks": [
            {
              "name": "string",
              "interval": "google.protobuf.Duration",
              "timeout": "google.protobuf.Duration",
              "unhealthyThreshold": "int64",
              "healthyThreshold": "int64",
              // Includes only one of the fields `tcpOptions`, `httpOptions`
              "tcpOptions": {
                "port": "int64"
              },
              "httpOptions": {
                "port": "int64",
                "path": "string"
              }
              // end of the list of possible fields
            }
          ]
        }
      ],
      "deletionProtection": "bool"
    }
  ],
  "nextPageToken": "string"
}
```

#|
||Field | Description ||
|| networkLoadBalancers[] | **[NetworkLoadBalancer](#yandex.cloud.loadbalancer.v1.NetworkLoadBalancer)**

List of NetworkLoadBalancer resources. ||
|| nextPageToken | **string**

This token allows you to get the next page of results for list requests. If the number of results
is larger than [ListNetworkLoadBalancersRequest.pageSize](#yandex.cloud.loadbalancer.v1.ListNetworkLoadBalancersRequest), use
the `nextPageToken` as the value
for the [ListNetworkLoadBalancersRequest.pageToken](#yandex.cloud.loadbalancer.v1.ListNetworkLoadBalancersRequest) query parameter
in the next list request. Each subsequent list request will have its own
`nextPageToken` to continue paging through the results. ||
|#

## NetworkLoadBalancer {#yandex.cloud.loadbalancer.v1.NetworkLoadBalancer}

A NetworkLoadBalancer resource. For more information, see [Network Load Balancer](/docs/network-load-balancer/concepts).

#|
||Field | Description ||
|| id | **string**

ID of the network load balancer. ||
|| folderId | **string**

ID of the folder that the network load balancer belongs to. ||
|| createdAt | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

Creation timestamp in [RFC3339](https://www.ietf.org/rfc/rfc3339.txt) text format. ||
|| name | **string**

Name of the network load balancer. The name is unique within the folder. 3-63 characters long. ||
|| description | **string**

Optional description of the network load balancer. 0-256 characters long. ||
|| labels | **string**

Resource labels as `` key:value `` pairs. Maximum of 64 per resource. ||
|| regionId | **string**

ID of the region that the network load balancer belongs to. ||
|| status | enum **Status**

Status of the network load balancer.

- `STATUS_UNSPECIFIED`
- `CREATING`: Network load balancer is being created.
- `STARTING`: Network load balancer is being started.
- `ACTIVE`: Network load balancer is active and sends traffic to the targets.
- `STOPPING`: Network load balancer is being stopped.
- `STOPPED`: Network load balancer is stopped and doesn't send traffic to the targets.
- `DELETING`: Network load balancer is being deleted.
- `INACTIVE`: The load balancer doesn't have any listeners or target groups, or
attached target groups are empty. The load balancer doesn't perform any health checks or
send traffic in this state. ||
|| type | enum **Type**

Type of the network load balancer. Only external network load balancers are available now.

- `TYPE_UNSPECIFIED`
- `EXTERNAL`: External network load balancer.
- `INTERNAL`: Internal network load balancer. ||
|| sessionAffinity | enum **SessionAffinity**

Type of the session affinity. Only 5-tuple affinity is available now.

- `SESSION_AFFINITY_UNSPECIFIED`
- `CLIENT_IP_PORT_PROTO`: 5-tuple affinity. ||
|| listeners[] | **[Listener](#yandex.cloud.loadbalancer.v1.Listener)**

List of listeners for the network load balancer. ||
|| attachedTargetGroups[] | **[AttachedTargetGroup](#yandex.cloud.loadbalancer.v1.AttachedTargetGroup)**

List of target groups attached to the network load balancer. ||
|| deletionProtection | **bool**

Specifies if network load balancer protected from deletion. ||
|#

## Listener {#yandex.cloud.loadbalancer.v1.Listener}

A Listener resource. For more information, see [Listener](/docs/network-load-balancer/concepts/listener)

#|
||Field | Description ||
|| name | **string**

Name of the listener. The name must be unique for each listener on a single load balancer. 3-63 characters long. ||
|| address | **string**

IP address for the listener. ||
|| port | **int64**

Port. ||
|| protocol | enum **Protocol**

Network protocol for incoming traffic.

- `PROTOCOL_UNSPECIFIED`
- `TCP`
- `UDP` ||
|| targetPort | **int64**

Port of a target. ||
|| subnetId | **string**

ID of the subnet. ||
|| ipVersion | enum **IpVersion**

IP version of the external address.

- `IP_VERSION_UNSPECIFIED`
- `IPV4`: IPv4
- `IPV6`: IPv6 ||
|#

## AttachedTargetGroup {#yandex.cloud.loadbalancer.v1.AttachedTargetGroup}

An AttachedTargetGroup resource. For more information, see [Targets and groups](/docs/network-load-balancer/concepts/target-resources).

#|
||Field | Description ||
|| targetGroupId | **string**

Required field. ID of the target group. ||
|| healthChecks[] | **[HealthCheck](#yandex.cloud.loadbalancer.v1.HealthCheck)**

A health check to perform on the target group.
For now we accept only one health check per AttachedTargetGroup. ||
|#

## HealthCheck {#yandex.cloud.loadbalancer.v1.HealthCheck}

A HealthCheck resource. For more information, see [Health check](/docs/network-load-balancer/concepts/health-check).

#|
||Field | Description ||
|| name | **string**

Required field. Name of the health check. The name must be unique for each target group that attached to a single load balancer. 3-63 characters long. ||
|| interval | **[google.protobuf.Duration](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/duration)**

The interval between health checks. The default is 2 seconds. ||
|| timeout | **[google.protobuf.Duration](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/duration)**

Timeout for a target to return a response for the health check. The default is 1 second. ||
|| unhealthyThreshold | **int64**

Number of failed health checks before changing the status to `` UNHEALTHY ``. The default is 2. ||
|| healthyThreshold | **int64**

Number of successful health checks required in order to set the `` HEALTHY `` status for the target. The default is 2. ||
|| tcpOptions | **[TcpOptions](#yandex.cloud.loadbalancer.v1.HealthCheck.TcpOptions)**

Options for TCP health check.

Includes only one of the fields `tcpOptions`, `httpOptions`.

Protocol to use for the health check. Either TCP or HTTP. ||
|| httpOptions | **[HttpOptions](#yandex.cloud.loadbalancer.v1.HealthCheck.HttpOptions)**

Options for HTTP health check.

Includes only one of the fields `tcpOptions`, `httpOptions`.

Protocol to use for the health check. Either TCP or HTTP. ||
|#

## TcpOptions {#yandex.cloud.loadbalancer.v1.HealthCheck.TcpOptions}

Configuration option for a TCP health check.

#|
||Field | Description ||
|| port | **int64**

Port to use for TCP health checks. ||
|#

## HttpOptions {#yandex.cloud.loadbalancer.v1.HealthCheck.HttpOptions}

Configuration option for an HTTP health check.

#|
||Field | Description ||
|| port | **int64**

Port to use for HTTP health checks. ||
|| path | **string**

URL path to set for health checking requests for every target in the target group.
For example `` /ping ``. The default path is `` / ``. ||
|#