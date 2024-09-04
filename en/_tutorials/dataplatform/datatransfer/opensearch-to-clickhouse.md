# Copying data from {{ mos-name }} to {{ mch-name }} using {{ data-transfer-full-name }}

With {{ data-transfer-name }}, you can transfer data from a {{ mos-name }} source cluster to {{ mch-name }}.

To transfer data:

1. [Prepare the source cluster](#prepare-source).
1. [Prepare and activate the transfer](#prepare-transfer).
1. [Test the transfer](#verify-transfer).

If you no longer need the resources you created, [delete them](#clear-out).

## Getting started {#before-you-begin}

Prepare the data transfer infrastructure:

{% list tabs group=instructions %}

- Manually {#manual}

   1. [Create a {{ mos-name }} source cluster](../../../managed-opensearch/operations/cluster-create.md#create-cluster) in any suitable configuration with publicly available hosts.
   1. In the same [availability zone](../../../overview/concepts/geo-scope.md), [create a {{ mch-name }} target cluster](../../../managed-clickhouse/operations/cluster-create.md#create-cluster) in any suitable configuration with publicly available hosts.

      If you are going to connect to the cluster via {{ websql-full-name }}, enable **{{ ui-key.yacloud.mdb.cluster.overview.label_access-websql-service }}** in the cluster settings.

   1. [Get an SSL certificate](../../../managed-opensearch/operations/connect.md#ssl-certificate) to connect to the {{ mos-name }} cluster.

   1. Make sure that security groups of the [{{ mos-name }}](../../../managed-opensearch/operations/connect.md#security-groups) and [{{ mch-name }}](../../../managed-clickhouse/operations/connect/index.md#configuring-security-groups) clusters allow connecting from the internet.

- {{ TF }} {#tf}

   1. {% include [terraform-install-without-setting](../../../_includes/mdb/terraform/install-without-setting.md) %}
   1. {% include [terraform-authentication](../../../_includes/mdb/terraform/authentication.md) %}
   1. {% include [terraform-setting](../../../_includes/mdb/terraform/setting.md) %}
   1. {% include [terraform-configure-provider](../../../_includes/mdb/terraform/configure-provider.md) %}

   1. Download the [opensearch-to-clickhouse.tf](https://github.com/yandex-cloud-examples/yc-data-transfer-from-opensearch-to-clickhouse/blob/main/opensearch-to-clickhouse.tf) configuration file to the same working directory.

      This file describes:

      * [Network](../../../vpc/concepts/network.md#network)..
      * [Subnet](../../../vpc/concepts/network.md#subnet)..
      * [Security group](../../../vpc/concepts/security-groups.md) and rules required to connect to the {{ mos-name }} and {{ mch-name }} clusters.
      * {{ mos-name }} source cluster with the `admin` user.
      * {{ mch-name }} target cluster with a user and database.
      * Target endpoint.
      * Transfer.

   1. In the `opensearch-to-clickhouse.tf`, specify the values for these parameters:

      * `source_admin_password`: `admin` user's password in the {{ mos-name }} cluster.
      * `mos_version`: {{ OS }} version.
      * `mch_db_name`: Name of the {{ mch-name }} cluster database.
      * `mch_username`: Name of the {{ mch-name }} cluster user.
      * `mch_user_password`: Password of the {{ mch-name }} cluster user.
      * `source_endpoint_id`: Source endpoint ID.
      * `profile_name`: Your YC CLI profile name.

         {% include [cli-install](../../../_includes/cli-install.md) %}

   1. Make sure the {{ TF }} configuration files are correct using this command:

      ```bash
      terraform validate
      ```

      If there are any errors in the configuration files, {{ TF }} will point them out.

   1. Create the required infrastructure:

      {% include [terraform-apply](../../../_includes/mdb/terraform/apply.md) %}

      {% include [explore-resources](../../../_includes/mdb/terraform/explore-resources.md) %}

{% endlist %}

## Prepare the test data {#prepare-data}

1. In the source cluster, create a test index named `people` and set its schema:

   ```bash
   curl --cacert ~/.opensearch/root.crt \
        --user <source_cluster_username>:<source_cluster_user_password> \
        --header 'Content-Type: application/json' \
        --request PUT 'https://<{{ OS }}_DATA_host_address>:{{ port-mos }}/people' && \
   curl --cacert ~/.opensearch/root.crt \
        --user <source_cluster_username>:<source_cluster_user_password> \
        --header 'Content-Type: application/json' \
        --request PUT 'https://<{{ OS }}_DATA_host_address>:{{ port-mos }}/people/_mapping?pretty' -d'
        {
              "properties": {
                 "name": {"type": "text"},
                 "age": {"type": "integer"}
              }
        }
        '
   ```

1. Populate the test index with data:

   ```bash
   curl --cacert ~/.opensearch/root.crt \
        --user <source_cluster_username>:<source_cluster_user_password> \
        --header 'Content-Type: application/json' \
        --request POST 'https://<{{ OS }}_DATA_host_address>:{{ port-mos }}/people/_doc/?pretty' -d'
        {
              "name": "Alice",
              "age": "30"
        }
        ' && \
   curl --cacert ~/.opensearch/root.crt \
        --user <source_cluster_username>:<source_cluster_user_password> \
        --header 'Content-Type: application/json' \
        --request POST 'https://<{{ OS }}_DATA_host_address>:{{ port-mos }}/people/_doc/?pretty' -d'
        {
              "name": "Robert",
              "age": "32"
        }
        '
   ```

1. (Optional) Check the data in the test index:

   ```bash
   curl --cacert ~/.opensearch/root.crt \
        --user <target_cluster_username>:<target_cluster_user_password> \
        --header 'Content-Type: application/json' \
        --request GET 'https://<{{ OS }}_DATA_host_address>:{{ port-mos }}/people/_search?pretty'
   ```

## Prepare and activate the transfer {#prepare-transfer}

1. [Create a source endpoint](../../../data-transfer/operations/endpoint/index.md#create) for the {{ mos-name }} cluster you [created earlier](#before-you-begin) with the following settings:

   * **{{ ui-key.yacloud.data-transfer.forms.label-database_type }}**: `OpenSearch`.
   * **{{ ui-key.yc-data-transfer.data-transfer.console.form.opensearch.console.form.opensearch.OpenSearchSource.connection.title }}**:
      * **{{ ui-key.yc-data-transfer.data-transfer.console.form.opensearch.console.form.opensearch.OpenSearchConnection.connection_type.title }}**: `{{ ui-key.yc-data-transfer.data-transfer.console.form.opensearch.console.form.opensearch.OpenSearchConnectionType.mdb_cluster_id.title }}`.
      * **{{ ui-key.yc-data-transfer.data-transfer.console.form.opensearch.console.form.opensearch.OpenSearchConnectionType.mdb_cluster_id.title }}**: Select the {{ mos-name }} cluster from the list.
      * **{{ ui-key.yc-data-transfer.data-transfer.console.form.opensearch.console.form.opensearch.OpenSearchConnection.user.title }}**: `admin`.
      * **{{ ui-key.yc-data-transfer.data-transfer.console.form.opensearch.console.form.opensearch.OpenSearchConnection.password.title }}**: `admin` user's password.

1. Create a target endpoint and a transfer:

   {% list tabs group=instructions %}

   - Manually {#manual}

      1. [Create a target endpoint](../../../data-transfer/operations/endpoint/index.md#create):

         * **{{ ui-key.yacloud.data-transfer.forms.label-database_type }}**: `ClickHouse`
         * **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseTarget.title }}**:
            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseConnection.connection_type.title }}**: Select `{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseConnectionType.managed.title }}`.
            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseManaged.mdb_cluster_id.title }}**: Select the {{ mch-name }} cluster from the list.
            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseCredentials.user.title }}**: Enter a name for the {{ mch-name }} cluster user.
            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseCredentials.password.title }}**: Enter a password for the {{ mch-name }} cluster user.
            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseConnection.database.title }}**: Enter a name for the {{ mch-name }} cluster database.

      1. [Create a transfer](../../../data-transfer/operations/transfer.md#create) of the **{{ ui-key.yc-data-transfer.data-transfer.console.form.transfer.console.form.transfer.TransferType.snapshot.title }}** type that will use the created endpoints.
      1. [Activate](../../../data-transfer/operations/transfer.md#activate) the transfer.

   - {{ TF }} {#tf}

      1. In the `opensearch-to-clickhouse.tf`, specify the values for this parameter:

         * `source_endpoint_id`: ID of the source endpoint.
         * `transfer_enabled`: Set to `1` for creating a target endpoint and transfer.

      1. Make sure the {{ TF }} configuration files are correct using this command:

         ```bash
         terraform validate
         ```

         If there are any errors in the configuration files, {{ TF }} will point them out.

      1. Create the required infrastructure:

         {% include [terraform-apply](../../../_includes/mdb/terraform/apply.md) %}

         Once created, your transfer will be activated automatically.

   {% endlist %}

## Test the transfer {#verify-transfer}

1. Wait for the transfer status to change to **{{ ui-key.yacloud.data-transfer.label_connector-status-DONE }}**.
1. Make sure the data from the source {{ mos-name }} cluster has been moved to the {{ mch-name }} database:

   {% list tabs group=instructions %}

   - {{ websql-full-name }} {#websql}

      1. [Create a connection](../../../websql/operations/create-connection.md#connect-cluster) to the {{ mch-name }} cluster database.
      1. Make sure the database contains the `people` table with test data. To do this, [run this query](../../../websql/operations/query-executor.md#execute-query) to the database via the connection you created:

         ```sql
         SELECT * FROM people;
         ```

   - CLI {#cli}

      1. [Get an SSL certificate](../../../managed-clickhouse/operations/connect/index.md#get-ssl-cert) to connect to the {{ mch-name }} cluster.
      1. If you do not have `clickhouse-client`, [install it](../../../managed-clickhouse/operations/connect/clients.md#clickhouse-client).
      1. [Connect to the database](../../../managed-clickhouse/operations/connect/clients.md#clickhouse-client) in the {{ mch-name }} cluster.
      1. Make sure the database contains the `people` table with test data:

         ```sql
         SELECT * FROM people;
         ```

   {% endlist %}

## Delete the resources you created {#clear-out}

Some resources are not free of charge. To avoid paying for them, delete the resources you no longer need:

1. [Delete the source endpoint](../../../data-transfer/operations/endpoint/index.md#delete).
1. Delete other resources depending on how they were created:

   {% list tabs group=instructions %}

   - Manually {#manual}

      1. [Delete the transfer](../../../data-transfer/operations/transfer.md#delete).
      1. [Delete the target endpoint](../../../data-transfer/operations/endpoint/index.md#delete).
      1. [Delete the {{ mos-name }} cluster](../../../managed-opensearch/operations/cluster-delete.md).
      1. [Delete the {{ mch-name }} cluster](../../../managed-clickhouse/operations/cluster-delete.md).

         The connection to the {{ mch-name }} cluster database in {{ websql-full-name }} will be deleted automatically.

      1. [Delete the subnet](../../../vpc/operations/subnet-delete.md).
      1. [Delete the network](../../../vpc/operations/network-delete.md).

   - {{ TF }} {#tf}

      1. In the terminal, go to the working directory with the `opensearch-to-clickhouse.tf` configuration file.
      1. Delete the resources using this command:

         ```bash
         terraform destroy
         ```

      1. Type `yes` and press **Enter**.

         This will delete all the resources described in the `opensearch-to-clickhouse.tf` configuration file.

   {% endlist %}

{% include [clickhouse-disclaimer](../../../_includes/clickhouse-disclaimer.md) %}