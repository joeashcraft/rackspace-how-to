---
node_id: 4636
title: Database replication with Cloud Databases
type: article
created_date: '2015-04-08'
created_by: Rose Contreras
last_modified_date: '2015-10-02'
last_modified_by: Mike Asthalter
product: Cloud Databases
product_url: cloud-databases
---

This article describes how you create and manage read replicas for your Cloud Database instance. Replication is available for MySQL 5.6, Percona 5.6, and MariaDB 10 databases. If you are using MySQL 5.1, you must upgrade to MySQL 5.6. For instructions on upgrading on how to migrate MySQL 5.1 to MySQL 5.6, see [Upgrade a Cloud Databases instance from MySQL 5.1 to MySQL 5.6](/how-to/upgrade-a-cloud-databases-instance-from-mysql-51-to-mysql-56).

### Replication overview

Replication enables you to create a read replica of a database instance that can be used for scaling out read-heavy workloads or for ensuring availability of your database in case of instance failure. You can send only your read requests to your replica; all write requests can be sent only to the source database instance. When you create a read replica, you must specify an existing database instance as the source of the replica.

Rackspace Cloud Databases uses asynchronous replication.


#### Read replica uses

Following are some common scenarios for which adding a read replica for your database instance is beneficial:

- Improve the overall performance of your read-heavy application workloads by creating read replicas of your database instance and using them to distribute your application read traffic.

- Many applications that use MySQL database technologies are used to perform analysis and business reporting. Run queries against your replica without affecting the performance of writes and updates on the source database instance.

- Use replication to create an updated copy of your database.



### Create a read replica

Follow these steps for creating a read replica for your database instance using the Cloud Control Panel.

1. Log in to the [cloud control panel](https://mycloud.rackspace.com).

2. In the top navigation bar, click the **Databases** menu and then select **MySQL**.

    <img src="https://8026b2e3760e2433679c-fffceaebb8c6ee053c935e8915a3fbe7.ssl.cf2.rackcdn.com/field/image/create_replica-1.png" width="332" height="185" border="1" alt=""  />

3. On the instance list page, click the plus sign (+) in the **Replicas** column for the database instance that you want to replicate.

	<img src="https://8026b2e3760e2433679c-fffceaebb8c6ee053c935e8915a3fbe7.ssl.cf2.rackcdn.com/field/image/create_replica-4_0.png" width="400" height="148" border="1" alt=""  />

    **Tip:** You can also click the gear icon next to the instance and select **Create Replica**, or click the instance and then click **Add Replica** in the replication section of the instance page.

4. Enter a name and set the configurations for the read replica, and then click **Create Replica**.

    <img src="https://8026b2e3760e2433679c-fffceaebb8c6ee053c935e8915a3fbe7.ssl.cf2.rackcdn.com/field/image/create_replica-3.png" width="350" height="290" border="1" alt=""  />

### View the replicas attached to a database instance

After you have created a replica, it is listed with the database instances.

<img src="https://8026b2e3760e2433679c-fffceaebb8c6ee053c935e8915a3fbe7.ssl.cf2.rackcdn.com/field/image/view_replica-1.png" width="450" height="194" border="1" alt=""  />

View the replica by clicking on it in the **Name** column or the **Replicas** column. The replication instance details page is displayed.

<img src="https://8026b2e3760e2433679c-fffceaebb8c6ee053c935e8915a3fbe7.ssl.cf2.rackcdn.com/field/image/vew_replica-2.png" width="450" height="430" border="1" alt=""  />

You can also view the replica in the Replications section of the instance details page.

### Detach a replica

You can detach a replica from a database instance.

1. On the details page for the replication instance, click the **Actions** menu and select **Detach from master**.

    <img src="https://8026b2e3760e2433679c-fffceaebb8c6ee053c935e8915a3fbe7.ssl.cf2.rackcdn.com/field/image/2210-4640-detach.png" width="492" height="179" border="1" alt=""  />

2.	In the popup dialog box, click **Detach Instance**.

    <img src="https://8026b2e3760e2433679c-fffceaebb8c6ee053c935e8915a3fbe7.ssl.cf2.rackcdn.com/field/image/2210-4640-detach-2.png" width="258" height="158" border="1" alt=""  />

There could be situations where you would like detach the replica. Here are some common scenario for detaching a replica:

- You might want to detach the replica if case you are experiencing a performance impact due to replication.

- If your primary database instance becomes unavailable, you can use the replica as the primary database instance and point your application to the replica. To accomplish this, you must detach the replica from the primary database instance and change the endpoint for your application.

### Delete a replica

1. To delete a replica, click the gear icon next to it and select **Delete Instance**.

    <img src="https://8026b2e3760e2433679c-fffceaebb8c6ee053c935e8915a3fbe7.ssl.cf2.rackcdn.com/field/image/delete_replica-1.png" width="176" height="153" border="1" alt=""  />

    You can also click the replica in the instance list, and on the instance page, click the gear icon in the top right corner.

2. In the popup dialog box, click **Delete Instance**.

    <img src="https://8026b2e3760e2433679c-fffceaebb8c6ee053c935e8915a3fbe7.ssl.cf2.rackcdn.com/field/image/delete_replica-2.png" width="245" height="139" border="1" alt=""  />

Once you delete the replica instance, your data will no longer be replicated. You can delete the replica without detaching it from the primary database instance, but you cannot delete the primary database instance if it has replicas attached.

### Monitoring read replicas

After setting up replication, you should periodically monitor your replicas to ensure that they are in a healthy state. All the variables mentioned in this section for monitoring replication are present in the monitoring agent running on your database instance. You can monitor these variables and also set up alarms to be notified of the state of your replica. You can also monitor these variables by executing the `SHOW GLOBAL STATUS` and `SHOW SLAVE STATUS` commands.

**slave_running:** This is a global status variable and its value can be `ON` or `OFF`. If the value is `ON`, the replica is connected to the source database instance and both the SQL thread and IO thread are running. If the value is `OFF`, you look at `Last_SQL_Errno` and `Last_SQL_Error` for more error information. You can create an alarm to monitor the status of replica with the following criteria.

    if (metric['replication.slave_running'] == "OFF") {

      return new AlarmStatus(WARNING, 'Replica is disconnected');

    }

**slave\_IO_running:** This variable is a part of the `Show Slave` status and its value can be `Yes`, `No`, or `Connecting`. If the value is `No`, the replica I/O thread is not running and you can look at `Last_IO_Errno` and `Last_IO_Error` for more error information. You can create an alarm to monitor the status of replica IO with the following criteria.

    if (metric['replication.slave_io_running'] == "No") {

      return new AlarmStatus(WARNING, 'Replica I/O thread is not running');
    }

**slave\_SQL\_running:** This variable is a part of the `Show Slave` status and its value can be `Yes` or `No`. It tells whether the replica&rsquo;s SQL thread has started and is working well. If the value is `No`, you can look at `Last_SQL_Errno` and `Last_SQL_Error` for more error information.

    if (metric['replication.slave_sql_running'] == "No") {

      return new AlarmStatus(WARNING, 'Replica SQL thread is not running');

    }
