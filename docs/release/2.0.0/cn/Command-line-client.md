---
title: 命令行客户端
layout: documentation
documentation: true
---
此页面描述了“storm”命令行客户端可能执行的所有命令。要了解如何设置“storm”客户端以与远程群集通信，请按照[设置开发环境](Setting-up-development-environment.md). 中的说明进行操作。有关在这些命令中使用外部库的详细信息，请参阅[类路径处理](Classpath-handling.md)。

这些命令是：

1. jar
1. local
1. sql
1. kill
1. activate
1. deactivate
1. rebalance
1. repl
1. classpath
1. server_classpath
1. localconfvalue
1. remoteconfvalue
1. nimbus
1. supervisor
1. ui
1. drpc
1. drpc-client
1. blobstore
1. dev-zookeeper
1. get-errors
1. heartbeats
1. kill_workers
1. list
1. logviewer
1. monitor
1. node-health-check
1. pacemaker
1. set_log_level
1. shell
1. upload-credentials
1. version
1. admin
1. help

### jar

语法： `storm jar topology-jar-path class ...`

使用指定的参数运行`class`的main方法。`~/.storm`中storm的jar和配置被放在类路径上。配置该进程，以便提交拓扑时[StormSubmitter](javadocs/org/apache/storm/StormSubmitter.md) 在`topology-jar-path`上传jar。

如果要加载未包含在应用程序jar中的其他jar，可以使用逗号分隔的字符串将它们传递给`--jars`选项。
例如， --jars "your-local-jar.jar,your-local-jar2.jar"将加载your-local-jar.jar和your-local-jar2.jar。
当你想发送maven包及其传递依赖时，你可以用逗号分隔的字符串将它们传递给`--artifacts` 。您还可以排除某些依赖关系，例如您在maven pom中所做的事情。请在包的后边增加包移除符'^'。例如， `--artifacts "redis.clients:jedis:2.9.0,org.apache.kafka:kafka_2.10:0.8.2.2^org.slf4j:slf4j-log4j12"` 将会加载jedis、kafka包和所有的传递依赖，但是会从kafka中排除slf4j-log4j12。

当您需要从Maven Central以外的地方获取依赖包时，可以使用逗号分隔的字符串将远程存储库传递到 --artifactRepositories选项。仓库格式为"<name>^<url>"。'^' 被视为分隔符，因为URL允许各种字符。 例如， --artifactRepositories "jboss-repository^http://repository.jboss.com/maven2,HDPRepo^http://repo.hortonworks.com/content/groups/public/"将会为依赖项解析器增加JBoss和HDP仓库。

这两个选项的完整示例如下： `./bin/storm jar example/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.RollingTopWords blobstore-remote2 remote --jars "./external/storm-redis/storm-redis-1.1.0.jar,./external/storm-kafka-client/storm-kafka-client-1.1.0.jar" --artifacts "redis.clients:jedis:2.9.0,org.apache.kafka:kafka-clients:1.0.0^org.slf4j:slf4j-api" --artifactRepositories "jboss-repository^http://repository.jboss.com/maven2,HDPRepo^http://repo.hortonworks.com/content/groups/public/"`

当您上传jars和/或依赖包选项时，将在提交拓扑时上传它们，并且它们将包含在该运行的类和拓扑的worker这两者的类路径中。

### local

语法： `storm jar topology-jar-path class ...`

本地命令就像`storm jar` 一样，除了不向集群提交拓扑外，它将以本地模式运行集群。这意味着后台程序的嵌入式版本将在与拓扑相同的进程中运行30秒，然后自动关闭。因此，拓扑的类路径将扩展为包括运行这些守护进程所需的所有内容。

### sql

语法： `storm sql sql-file topology-name`

将SQL语句编译为Trident拓扑并将其提交给Storm。

`--jars`和`--artifacts`，以及`--artifactRepositories`选项也适用于sql命令。请参阅"help jar"以了解如何使用--jars、--artifacts和--artifactRepositories选项。您通常希望传递这些选项，因为在许多情况下需要将数据源设置为sql，这是一个外部存储。

### kill

语法： `storm kill topology-name [-w wait-time-secs]`

使用名称`topology-name`终止拓扑。Storm将在拓扑的消息超时期间首先停用拓扑的spouts，以允许当前正在处理的所有消息完成处理。storm将关闭worker并清理他们的状态。您可以使用-w标志覆盖Storm在停用和关闭之间等待的时间长度。

### activate

语法： `storm activate topology-name`

启用指定拓扑的spout。

### deactivate

语法： `storm deactivate topology-name`

停用指定拓扑的spout。

### rebalance

语法： `storm rebalance topology-name [-w wait-time-secs] [-n new-num-workers] [-e component=parallelism]*`

有时候你可能希望分散拓扑正在运行的worker。例如，假如说你有10个节点集群，每个节点运行4个worker，后来增加了10个节点。你也许想着让storm在运行的拓扑上分散worker，每个节点运行2个worker。一个办法是终止拓扑并重新提交。但是storm可以简单使用“rebalance”命令就可实现。

重新平衡将在消息超时（使用-w标志可覆盖）终止拓扑，并且在集群中重新均匀的分配worker。拓扑将会返回先前的激活状态（所以一个非激活的拓扑将一直处于非激活状态，并且一个激活状态的拓扑将会重回激活状态【译者云-说人话就是rebalance只是起到重新均匀分配worker作用，而拓扑状态rebalance之前是什么样，rebalance之后还是什么样】）。

rebalance命令还可用于更改正在运行的拓扑的并行性。使用-n和-e开关分别更改组件的workers或executors（执行器）数量。

### repl

语法： `storm repl`

Opens up a Clojure REPL with the storm jars and configuration on the classpath. Useful for debugging.

### classpath

Syntax: `storm classpath`

Prints the classpath used by the storm client when running commands.

### server_classpath

Syntax: `storm server_classpath`

Prints the classpath used by the storm daemons.

### localconfvalue

Syntax: `storm localconfvalue conf-name`

Prints out the value for `conf-name` in the local Storm configs. The local Storm configs are the ones in `~/.storm/storm.yaml` merged in with the configs in `defaults.yaml`.

### remoteconfvalue

Syntax: `storm remoteconfvalue conf-name`

Prints out the value for `conf-name` in the cluster's Storm configs. The cluster's Storm configs are the ones in `$STORM-PATH/conf/storm.yaml` merged in with the configs in `defaults.yaml`. This command must be run on a cluster machine.

### nimbus

Syntax: `storm nimbus`

Launches the nimbus daemon. This command should be run under supervision with a tool like [daemontools](http://cr.yp.to/daemontools.md) or [monit](http://mmonit.com/monit/). See [Setting up a Storm cluster](Setting-up-a-Storm-cluster.md) for more information.

### supervisor

Syntax: `storm supervisor`

Launches the supervisor daemon. This command should be run under supervision with a tool like [daemontools](http://cr.yp.to/daemontools.md) or [monit](http://mmonit.com/monit/). See [Setting up a Storm cluster](Setting-up-a-Storm-cluster.md) for more information.

### ui

Syntax: `storm ui`

Launches the UI daemon. The UI provides a web interface for a Storm cluster and shows detailed stats about running topologies. This command should be run under supervision with a tool like [daemontools](http://cr.yp.to/daemontools.md) or [monit](http://mmonit.com/monit/). See [Setting up a Storm cluster](Setting-up-a-Storm-cluster.md) for more information.

### drpc

Syntax: `storm drpc`

Launches a DRPC daemon. This command should be run under supervision with a tool like [daemontools](http://cr.yp.to/daemontools.md) or [monit](http://mmonit.com/monit/). See [Distributed RPC](Distributed-RPC.md) for more information.

### drpc-client

Syntax: `storm drpc-client [options] ([function argument]*)|(argument*)`

Provides a very simple way to send DRPC requests. If a `-f` argument is supplied, to set the function name, all of the arguments are treated
as arguments to the function.  If no function is given the arguments must be pairs of function argument.

*NOTE:* This is not really intended for production use.  This is mostly because parsing out the results can be a pain.

Creating an actual DRPC client only takes a few lines, so for production please go with that.

```java
Config conf = new Config();
try (DRPCClient drpc = DRPCClient.getConfiguredClient(conf)) {
  //User the drpc client
  String result = drpc.execute(function, argument);
}
```

#### Examples

`storm drpc-client exclaim a exclaim b test bar`

This will submit 3 separate DRPC request.
1. function = "exclaim" args = "a"
2. function = "exclaim" args = "b"
3. function = "test" args = "bar"

`storm drpc-client -f exclaim a b`

This will submit 2 separate DRPC request.
1. function = "exclaim" args = "a"
2. function = "exclaim" args = "b"

### blobstore

Syntax: `storm blobstore cmd`

list [KEY...] - lists blobs currently in the blob store

cat [-f FILE] KEY - read a blob and then either write it to a file, or STDOUT (requires read access).

create [-f FILE] [-a ACL ...] [--replication-factor NUMBER] KEY - create a new blob. Contents comes from a FILE or STDIN. ACL is in the form [uo]:[username]:[r-][w-][a-] can be comma separated list.

update [-f FILE] KEY - update the contents of a blob.  Contents comes from a FILE or STDIN (requires write access).

delete KEY - delete an entry from the blob store (requires write access).

set-acl [-s ACL] KEY - ACL is in the form [uo]:[username]:[r-][w-][a-] can be comma separated list (requires admin access).

replication --read KEY - Used to read the replication factor of the blob.

replication --update --replication-factor NUMBER KEY where NUMBER > 0. It is used to update the replication factor of a blob.

For example, the following would create a mytopo:data.tgz key using the data stored in data.tgz.  User alice would have full access, bob would have read/write access and everyone else would have read access.

storm blobstore create mytopo:data.tgz -f data.tgz -a u:alice:rwa,u:bob:rw,o::r

See [Blobstore(Distcahce)](distcache-blobstore.md) for more information.

### dev-zookeeper

Syntax: `storm dev-zookeeper`

Launches a fresh Zookeeper server using "dev.zookeeper.path" as its local dir and "storm.zookeeper.port" as its port. This is only intended for development/testing, the Zookeeper instance launched is not configured to be used in production.

### get-errors

Syntax: `storm get-errors topology-name`

Get the latest error from the running topology. The returned result contains the key value pairs for component-name and component-error for the components in error. The result is returned in json format.

### heartbeats

Syntax: `storm heartbeats [cmd]`

list PATH - lists heartbeats nodes under PATH currently in the ClusterState.
get  PATH - Get the heartbeat data at PATH

### kill_workers

Syntax: `storm kill_workers`

Kill the workers running on this supervisor. This command should be run on a supervisor node. If the cluster is running in secure mode, then user needs to have admin rights on the node to be able to successfully kill all workers.

### list

Syntax: `storm list`

List the running topologies and their statuses.

### logviewer

Syntax: `storm logviewer`

Launches the log viewer daemon. It provides a web interface for viewing storm log files. This command should be run under supervision with a tool like daemontools or monit.

See Setting up a Storm cluster for more information.(http://storm.apache.org/documentation/Setting-up-a-Storm-cluster)

### monitor

Syntax: `storm monitor topology-name [-i interval-secs] [-m component-id] [-s stream-id] [-w [emitted | transferred]]`

Monitor given topology's throughput interactively.
One can specify poll-interval, component-id, stream-id, watch-item[emitted | transferred]
  By default,
    poll-interval is 4 seconds;
    all component-ids will be list;
    stream-id is 'default';
    watch-item is 'emitted';

### node-health-check

Syntax: `storm node-health-check`

Run health checks on the local supervisor.

### pacemaker

Syntax: `storm pacemaker`

Launches the Pacemaker daemon. This command should be run under
supervision with a tool like daemontools or monit.

See Setting up a Storm cluster for more information.(http://storm.apache.org/documentation/Setting-up-a-Storm-cluster)

### set_log_level

Syntax: `storm set_log_level -l [logger name]=[log level][:optional timeout] -r [logger name] topology-name`

Dynamically change topology log levels

where log level is one of: ALL, TRACE, DEBUG, INFO, WARN, ERROR, FATAL, OFF
and timeout is integer seconds.

e.g.
  ./bin/storm set_log_level -l ROOT=DEBUG:30 topology-name

  Set the root logger's level to DEBUG for 30 seconds

  ./bin/storm set_log_level -l com.myapp=WARN topology-name

  Set the com.myapp logger's level to WARN for 30 seconds

  ./bin/storm set_log_level -l com.myapp=WARN -l com.myOtherLogger=ERROR:123 topology-name

  Set the com.myapp logger's level to WARN indifinitely, and com.myOtherLogger to ERROR for 123 seconds

  ./bin/storm set_log_level -r com.myOtherLogger topology-name

  Clears settings, resetting back to the original level

### shell

Syntax: `storm shell resourcesdir command args`

Makes constructing jar and uploading to nimbus for using non JVM languages

eg: `storm shell resources/ python topology.py arg1 arg2`

### upload-credentials

Syntax: `storm upload_credentials topology-name [credkey credvalue]*`

Uploads a new set of credentials to a running topology

### version

Syntax: `storm version`

Prints the version number of this Storm release.

### admin

Syntax: `storm admin <cmd> [options]`

The storm admin command provides access to several operations that can help an administrator debug or fix a cluster.

`remove_corrupt_topologies` - This command should be run on a nimbus node as the same user nimbus runs as.  It will go directly to zookeeper + blobstore and find topologies that appear to be corrupted because of missing blobs. It will kill those topologies.

 `zk_cli [options]` - This command will launch a zookeeper cli pointing to the storm zookeeper instance logged in as the nimbus user.  It should be run on a nimbus server as the user nimbus runs as.

   * `-s --server <connection string>`: Set the connection string to use,
            defaults to storm connection string.
   * `-t --time-out <timeout>`:  Set the timeout to use, defaults to storm
            zookeeper timeout.
   * `-w --write`: Allow for writes, defaults to read only, we don't want to
            cause problems.
   * `-n --no-root`: Don't include the storm root on the default connection string.
   * `-j --jaas <jaas_file>`: Include a jaas file that should be used when
            authenticating with ZK defaults to the
            java.security.auth.login.config conf.

`creds <topology_id>` - Print the credential keys for a topology.

### help
Syntax: `storm help [command]`

Print one help message or list of available commands
