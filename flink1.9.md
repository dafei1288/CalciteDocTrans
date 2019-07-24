大家期盼已久的1.9已经剪支有些日子了，兴冲冲的切换到跑去编译，我在之前的文章《尝尝Blink》里也介绍过如何编译，本文只针对不同的地方以及遇到的坑做一些说明，希望对遇到同样问题的朋友有一些帮助。

首先，切换分支 `git checkout release-1.9` 
这次我们不修改pom文件，将镜像添加到`settings.xml`里，在文章末尾，我会分享出来我用的文件全文，这里就不再赘述了。
直接使用 `clean package  -DskipTests -Dfast`进行编译

```
​[INFO] Reactor Summary for flink 1.9-SNAPSHOT:
[INFO] 
[INFO] force-shading ...................................... SUCCESS [  2.233 s]
[INFO] flink .............................................. SUCCESS [  2.536 s]
[INFO] flink-annotations .................................. SUCCESS [  1.447 s]
[INFO] flink-shaded-curator ............................... SUCCESS [  1.291 s]
[INFO] flink-metrics ...................................... SUCCESS [  0.101 s]
[INFO] flink-metrics-core ................................. SUCCESS [  0.959 s]
[INFO] flink-test-utils-parent ............................ SUCCESS [  0.091 s]
[INFO] flink-test-utils-junit ............................. SUCCESS [  1.048 s]
[INFO] flink-core ......................................... SUCCESS [ 19.790 s]
[INFO] flink-java ......................................... SUCCESS [  4.944 s]
[INFO] flink-queryable-state .............................. SUCCESS [  0.085 s]
[INFO] flink-queryable-state-client-java .................. SUCCESS [  1.671 s]
[INFO] flink-filesystems .................................. SUCCESS [  0.079 s]
[INFO] flink-hadoop-fs .................................... SUCCESS [  3.029 s]
[INFO] flink-runtime ...................................... SUCCESS [ 48.913 s]
[INFO] flink-scala ........................................ SUCCESS [ 39.109 s]
[INFO] flink-mapr-fs ...................................... SUCCESS [  2.523 s]
[INFO] flink-filesystems :: flink-fs-hadoop-shaded ........ SUCCESS [  3.966 s]
[INFO] flink-s3-fs-base ................................... SUCCESS [  7.892 s]
[INFO] flink-s3-fs-hadoop ................................. SUCCESS [ 10.222 s]
[INFO] flink-s3-fs-presto ................................. SUCCESS [ 14.337 s]
[INFO] flink-swift-fs-hadoop .............................. SUCCESS [ 13.493 s]
[INFO] flink-oss-fs-hadoop ................................ SUCCESS [  7.104 s]
[INFO] flink-azure-fs-hadoop .............................. SUCCESS [  8.093 s]
[INFO] flink-optimizer .................................... SUCCESS [  3.843 s]
[INFO] flink-clients ...................................... SUCCESS [  3.200 s]
[INFO] flink-streaming-java ............................... SUCCESS [ 15.939 s]
[INFO] flink-test-utils ................................... SUCCESS [  4.398 s]
[INFO] flink-runtime-web .................................. SUCCESS [06:05 min]
[INFO] flink-examples ..................................... SUCCESS [  0.196 s]
[INFO] flink-examples-batch ............................... SUCCESS [ 15.297 s]
[INFO] flink-connectors ................................... SUCCESS [  0.076 s]
[INFO] flink-hadoop-compatibility ......................... SUCCESS [  6.228 s]
[INFO] flink-state-backends ............................... SUCCESS [  0.088 s]
[INFO] flink-statebackend-rocksdb ......................... SUCCESS [  4.283 s]
[INFO] flink-tests ........................................ SUCCESS [01:00 min]
[INFO] flink-streaming-scala .............................. SUCCESS [ 33.076 s]
[INFO] flink-table ........................................ SUCCESS [  0.082 s]
[INFO] flink-table-common ................................. SUCCESS [  2.936 s]
[INFO] flink-table-api-java ............................... FAILURE [  1.958 s]
[INFO] flink-table-api-java-bridge ........................ SKIPPED
[INFO] flink-table-api-scala .............................. SKIPPED
[INFO] flink-table-api-scala-bridge ....................... SKIPPED
[INFO] flink-sql-parser ................................... SKIPPED
[INFO] flink-libraries .................................... SKIPPED
[INFO] flink-cep .......................................... SKIPPED
[INFO] flink-table-planner ................................ SKIPPED
[INFO] flink-orc .......................................... SKIPPED
[INFO] flink-jdbc ......................................... SKIPPED
[INFO] flink-hbase ........................................ SKIPPED
[INFO] flink-hcatalog ..................................... SKIPPED
[INFO] flink-metrics-jmx .................................. SKIPPED
[INFO] flink-connector-kafka-base ......................... SKIPPED
[INFO] flink-connector-kafka-0.9 .......................... SKIPPED
[INFO] flink-connector-kafka-0.10 ......................... SKIPPED
[INFO] flink-connector-kafka-0.11 ......................... SKIPPED
[INFO] flink-formats ...................................... SKIPPED
[INFO] flink-json ......................................... SKIPPED
[INFO] flink-connector-elasticsearch-base ................. SKIPPED
[INFO] flink-connector-elasticsearch2 ..................... SKIPPED
[INFO] flink-connector-elasticsearch5 ..................... SKIPPED
[INFO] flink-connector-elasticsearch6 ..................... SKIPPED
[INFO] flink-connector-hive ............................... SKIPPED
[INFO] flink-connector-rabbitmq ........................... SKIPPED
[INFO] flink-connector-twitter ............................ SKIPPED
[INFO] flink-connector-nifi ............................... SKIPPED
[INFO] flink-connector-cassandra .......................... SKIPPED
[INFO] flink-avro ......................................... SKIPPED
[INFO] flink-connector-filesystem ......................... SKIPPED
[INFO] flink-connector-kafka .............................. SKIPPED
[INFO] flink-connector-gcp-pubsub ......................... SKIPPED
[INFO] flink-sql-connector-elasticsearch6 ................. SKIPPED
[INFO] flink-sql-connector-kafka-0.9 ...................... SKIPPED
[INFO] flink-sql-connector-kafka-0.10 ..................... SKIPPED
[INFO] flink-sql-connector-kafka-0.11 ..................... SKIPPED
[INFO] flink-sql-connector-kafka .......................... SKIPPED
[INFO] flink-connector-kafka-0.8 .......................... SKIPPED
[INFO] flink-avro-confluent-registry ...................... SKIPPED
[INFO] flink-parquet ...................................... SKIPPED
[INFO] flink-sequence-file ................................ SKIPPED
[INFO] flink-csv .......................................... SKIPPED
[INFO] flink-examples-streaming ........................... SKIPPED
[INFO] flink-examples-table ............................... SKIPPED
[INFO] flink-examples-build-helper ........................ SKIPPED
[INFO] flink-examples-streaming-twitter ................... SKIPPED
[INFO] flink-examples-streaming-state-machine ............. SKIPPED
[INFO] flink-examples-streaming-gcp-pubsub ................ SKIPPED
[INFO] flink-container .................................... SKIPPED
[INFO] flink-queryable-state-runtime ...................... SKIPPED
[INFO] flink-end-to-end-tests ............................. SKIPPED
[INFO] flink-cli-test ..................................... SKIPPED
[INFO] flink-parent-child-classloading-test-program ....... SKIPPED
[INFO] flink-parent-child-classloading-test-lib-package ... SKIPPED
[INFO] flink-dataset-allround-test ........................ SKIPPED
[INFO] flink-datastream-allround-test ..................... SKIPPED
[INFO] flink-stream-sql-test .............................. SKIPPED
[INFO] flink-bucketing-sink-test .......................... SKIPPED
[INFO] flink-distributed-cache-via-blob ................... SKIPPED
[INFO] flink-high-parallelism-iterations-test ............. SKIPPED
[INFO] flink-stream-stateful-job-upgrade-test ............. SKIPPED
[INFO] flink-queryable-state-test ......................... SKIPPED
[INFO] flink-local-recovery-and-allocation-test ........... SKIPPED
[INFO] flink-elasticsearch2-test .......................... SKIPPED
[INFO] flink-elasticsearch5-test .......................... SKIPPED
[INFO] flink-elasticsearch6-test .......................... SKIPPED
[INFO] flink-quickstart ................................... SKIPPED
[INFO] flink-quickstart-java .............................. SKIPPED
[INFO] flink-quickstart-scala ............................. SKIPPED
[INFO] flink-quickstart-test .............................. SKIPPED
[INFO] flink-confluent-schema-registry .................... SKIPPED
[INFO] flink-stream-state-ttl-test ........................ SKIPPED
[INFO] flink-sql-client-test .............................. SKIPPED
[INFO] flink-streaming-file-sink-test ..................... SKIPPED
[INFO] flink-state-evolution-test ......................... SKIPPED
[INFO] flink-e2e-test-utils ............................... SKIPPED
[INFO] flink-mesos ........................................ SKIPPED
[INFO] flink-yarn ......................................... SKIPPED
[INFO] flink-gelly ........................................ SKIPPED
[INFO] flink-gelly-scala .................................. SKIPPED
[INFO] flink-gelly-examples ............................... SKIPPED
[INFO] flink-metrics-dropwizard ........................... SKIPPED
[INFO] flink-metrics-graphite ............................. SKIPPED
[INFO] flink-metrics-influxdb ............................. SKIPPED
[INFO] flink-metrics-prometheus ........................... SKIPPED
[INFO] flink-metrics-statsd ............................... SKIPPED
[INFO] flink-metrics-datadog .............................. SKIPPED
[INFO] flink-metrics-slf4j ................................ SKIPPED
[INFO] flink-cep-scala .................................... SKIPPED
[INFO] flink-table-uber ................................... SKIPPED
[INFO] flink-sql-client ................................... SKIPPED
[INFO] flink-python ....................................... SKIPPED
[INFO] flink-scala-shell .................................. SKIPPED
[INFO] flink-dist ......................................... SKIPPED
[INFO] flink-end-to-end-tests-common ...................... SKIPPED
[INFO] flink-metrics-availability-test .................... SKIPPED
[INFO] flink-metrics-reporter-prometheus-test ............. SKIPPED
[INFO] flink-heavy-deployment-stress-test ................. SKIPPED
[INFO] flink-connector-gcp-pubsub-emulator-tests .......... SKIPPED
[INFO] flink-streaming-kafka-test-base .................... SKIPPED
[INFO] flink-streaming-kafka-test ......................... SKIPPED
[INFO] flink-streaming-kafka011-test ...................... SKIPPED
[INFO] flink-streaming-kafka010-test ...................... SKIPPED
[INFO] flink-plugins-test ................................. SKIPPED
[INFO] flink-state-processor-api .......................... SKIPPED
[INFO] flink-table-runtime-blink .......................... SKIPPED
[INFO] flink-table-planner-blink .......................... SKIPPED
[INFO] flink-contrib ...................................... SKIPPED
[INFO] flink-connector-wikiedits .......................... SKIPPED
[INFO] flink-yarn-tests ................................... SKIPPED
[INFO] flink-fs-tests ..................................... SKIPPED
[INFO] flink-docs ......................................... SKIPPED
[INFO] flink-ml-parent .................................... SKIPPED
[INFO] flink-ml-api ....................................... SKIPPED
[INFO] flink-ml-lib ....................................... SKIPPED
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  11:58 min
[INFO] Finished at: 2019-07-24T16:37:45+08:00
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-compiler-plugin:3.8.0:compile (default-compile) on project flink-table-api-java: Compilation failure
[ERROR] /E:/devlop/sourcespace/flink/flink-table/flink-table-api-java/src/main/java/org/apache/flink/table/operations/utils/OperationTreeBuilder.java:[560,85] 未报告的异常错误X; 必须对其进行捕获或声明以便抛出
[ERROR] 
[ERROR] -> [Help 1]
[ERROR] 
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR] 
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoFailureException
[ERROR] 
[ERROR] After correcting the problems, you can resume the build with the command
[ERROR]   mvn <goals> -rf :flink-table-api-java
```

这个问题 `未报告的异常错误X; 必须对其进行捕获或声明以便抛出` 问题卡了我好久，查看源码 
```
		private CalculatedQueryOperation<?> unwrapFromAlias(CallExpression call) {
			List<Expression> children = call.getChildren();
			List<String> aliases = children.subList(1, children.size())
				.stream()
				.map(alias -> ExpressionUtils.extractValue(alias, String.class)
					.orElseThrow(() -> new ValidationException("Unexpected alias: " + alias))) <= 这里是异常提示
				.collect(toList());

			if (!isFunctionOfKind(children.get(0), TABLE)) {
				throw fail();
			}

			CallExpression tableCall = (CallExpression) children.get(0);
			TableFunctionDefinition tableFunctionDefinition =
				(TableFunctionDefinition) tableCall.getFunctionDefinition();
			return createFunctionCall(tableFunctionDefinition, aliases, tableCall.getResolvedChildren());
		}
```
再看一下`ValidationException`的代码
```
@PublicEvolving
public class ValidationException extends RuntimeException {

	public ValidationException(String message, Throwable cause) {
		super(message, cause);
	}

	public ValidationException(String message) {
		super(message);
	}
}

```
似乎也没啥问题，然后翻了半天，终于在stackoverflow上找到问题所在了
`https://stackoverflow.com/questions/25523375/java8-lambdas-and-exceptions`
可以在前面加上异常类型 `.<ValidationException>orElseThrow(() -> new ValidationException("Unexpected alias: " + alias)))` 还有几个文件，也要修改，这个问题也可以通过更换JDK来规避。


当时使用JDK
```
E:\devlop\envs\Java8x64bak\bin>java -version
java version "1.8.0_60"
Java(TM) SE Runtime Environment (build 1.8.0_60-b27)
Java HotSpot(TM) 64-Bit Server VM (build 25.60-b23, mixed mode)
```
更换JDK
```
E:\devlop\envs\Java8x64\bin>java -version
java version "1.8.0_131"
Java(TM) SE Runtime Environment (build 1.8.0_131-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.131-b11, mixed mode)
```

编译成功
```
[INFO] Reactor Summary for flink 1.9-SNAPSHOT:
[INFO] 
[INFO] force-shading ...................................... SUCCESS [  3.341 s]
[INFO] flink .............................................. SUCCESS [  3.686 s]
[INFO] flink-annotations .................................. SUCCESS [  1.474 s]
[INFO] flink-shaded-curator ............................... SUCCESS [  1.275 s]
[INFO] flink-metrics ...................................... SUCCESS [  0.100 s]
[INFO] flink-metrics-core ................................. SUCCESS [  0.959 s]
[INFO] flink-test-utils-parent ............................ SUCCESS [  0.094 s]
[INFO] flink-test-utils-junit ............................. SUCCESS [  0.963 s]
[INFO] flink-core ......................................... SUCCESS [ 20.784 s]
[INFO] flink-java ......................................... SUCCESS [  7.953 s]
[INFO] flink-queryable-state .............................. SUCCESS [  0.084 s]
[INFO] flink-queryable-state-client-java .................. SUCCESS [  1.925 s]
[INFO] flink-filesystems .................................. SUCCESS [  0.094 s]
[INFO] flink-hadoop-fs .................................... SUCCESS [  3.108 s]
[INFO] flink-runtime ...................................... SUCCESS [ 52.749 s]
[INFO] flink-scala ........................................ SUCCESS [ 40.804 s]
[INFO] flink-mapr-fs ...................................... SUCCESS [  2.281 s]
[INFO] flink-filesystems :: flink-fs-hadoop-shaded ........ SUCCESS [  3.865 s]
[INFO] flink-s3-fs-base ................................... SUCCESS [  7.667 s]
[INFO] flink-s3-fs-hadoop ................................. SUCCESS [ 11.142 s]
[INFO] flink-s3-fs-presto ................................. SUCCESS [ 14.022 s]
[INFO] flink-swift-fs-hadoop .............................. SUCCESS [ 13.379 s]
[INFO] flink-oss-fs-hadoop ................................ SUCCESS [  7.149 s]
[INFO] flink-azure-fs-hadoop .............................. SUCCESS [  8.124 s]
[INFO] flink-optimizer .................................... SUCCESS [  3.841 s]
[INFO] flink-clients ...................................... SUCCESS [  3.081 s]
[INFO] flink-streaming-java ............................... SUCCESS [ 13.254 s]
[INFO] flink-test-utils ................................... SUCCESS [  4.429 s]
[INFO] flink-runtime-web .................................. SUCCESS [03:56 min]
[INFO] flink-examples ..................................... SUCCESS [  0.195 s]
[INFO] flink-examples-batch ............................... SUCCESS [01:27 min]
[INFO] flink-connectors ................................... SUCCESS [  0.156 s]
[INFO] flink-hadoop-compatibility ......................... SUCCESS [  7.404 s]
[INFO] flink-state-backends ............................... SUCCESS [  0.103 s]
[INFO] flink-statebackend-rocksdb ......................... SUCCESS [  4.041 s]
[INFO] flink-tests ........................................ SUCCESS [ 57.677 s]
[INFO] flink-streaming-scala .............................. SUCCESS [ 39.897 s]
[INFO] flink-table ........................................ SUCCESS [  0.093 s]
[INFO] flink-table-common ................................. SUCCESS [  3.252 s]
[INFO] flink-table-api-java ............................... SUCCESS [  3.382 s]
[INFO] flink-table-api-java-bridge ........................ SUCCESS [  1.691 s]
[INFO] flink-table-api-scala .............................. SUCCESS [  5.564 s]
[INFO] flink-table-api-scala-bridge ....................... SUCCESS [  6.084 s]
[INFO] flink-sql-parser ................................... SUCCESS [01:45 min]
[INFO] flink-libraries .................................... SUCCESS [  0.071 s]
[INFO] flink-cep .......................................... SUCCESS [  7.880 s]
[INFO] flink-table-planner ................................ SUCCESS [02:02 min]
[INFO] flink-orc .......................................... SUCCESS [  2.537 s]
[INFO] flink-jdbc ......................................... SUCCESS [  2.255 s]
[INFO] flink-hbase ........................................ SUCCESS [  7.450 s]
[INFO] flink-hcatalog ..................................... SUCCESS [  5.875 s]
[INFO] flink-metrics-jmx .................................. SUCCESS [  1.468 s]
[INFO] flink-connector-kafka-base ......................... SUCCESS [  6.826 s]
[INFO] flink-connector-kafka-0.9 .......................... SUCCESS [  5.396 s]
[INFO] flink-connector-kafka-0.10 ......................... SUCCESS [  3.076 s]
[INFO] flink-connector-kafka-0.11 ......................... SUCCESS [  3.337 s]
[INFO] flink-formats ...................................... SUCCESS [  0.070 s]
[INFO] flink-json ......................................... SUCCESS [  1.535 s]
[INFO] flink-connector-elasticsearch-base ................. SUCCESS [  4.051 s]
[INFO] flink-connector-elasticsearch2 ..................... SUCCESS [ 10.091 s]
[INFO] flink-connector-elasticsearch5 ..................... SUCCESS [ 11.304 s]
[INFO] flink-connector-elasticsearch6 ..................... SUCCESS [  5.441 s]
[INFO] flink-connector-hive ............................... SUCCESS [ 10.140 s]
[INFO] flink-connector-rabbitmq ........................... SUCCESS [  1.770 s]
[INFO] flink-connector-twitter ............................ SUCCESS [  2.210 s]
[INFO] flink-connector-nifi ............................... SUCCESS [  1.993 s]
[INFO] flink-connector-cassandra .......................... SUCCESS [  4.067 s]
[INFO] flink-avro ......................................... SUCCESS [  6.819 s]
[INFO] flink-connector-filesystem ......................... SUCCESS [  3.599 s]
[INFO] flink-connector-kafka .............................. SUCCESS [  3.106 s]
[INFO] flink-connector-gcp-pubsub ......................... SUCCESS [  6.798 s]
[INFO] flink-sql-connector-elasticsearch6 ................. SUCCESS [  5.708 s]
[INFO] flink-sql-connector-kafka-0.9 ...................... SUCCESS [  0.579 s]
[INFO] flink-sql-connector-kafka-0.10 ..................... SUCCESS [  0.665 s]
[INFO] flink-sql-connector-kafka-0.11 ..................... SUCCESS [  0.748 s]
[INFO] flink-sql-connector-kafka .......................... SUCCESS [  1.050 s]
[INFO] flink-connector-kafka-0.8 .......................... SUCCESS [  2.633 s]
[INFO] flink-avro-confluent-registry ...................... SUCCESS [  1.856 s]
[INFO] flink-parquet ...................................... SUCCESS [  2.886 s]
[INFO] flink-sequence-file ................................ SUCCESS [  1.368 s]
[INFO] flink-csv .......................................... SUCCESS [  1.404 s]
[INFO] flink-examples-streaming ........................... SUCCESS [ 14.729 s]
[INFO] flink-examples-table ............................... SUCCESS [  8.828 s]
[INFO] flink-examples-build-helper ........................ SUCCESS [  0.189 s]
[INFO] flink-examples-streaming-twitter ................... SUCCESS [  0.826 s]
[INFO] flink-examples-streaming-state-machine ............. SUCCESS [  0.696 s]
[INFO] flink-examples-streaming-gcp-pubsub ................ SUCCESS [  4.980 s]
[INFO] flink-container .................................... SUCCESS [  2.574 s]
[INFO] flink-queryable-state-runtime ...................... SUCCESS [  4.981 s]
[INFO] flink-end-to-end-tests ............................. SUCCESS [  0.078 s]
[INFO] flink-cli-test ..................................... SUCCESS [  0.933 s]
[INFO] flink-parent-child-classloading-test-program ....... SUCCESS [  1.070 s]
[INFO] flink-parent-child-classloading-test-lib-package ... SUCCESS [  0.519 s]
[INFO] flink-dataset-allround-test ........................ SUCCESS [  0.734 s]
[INFO] flink-datastream-allround-test ..................... SUCCESS [  2.613 s]
[INFO] flink-stream-sql-test .............................. SUCCESS [  1.742 s]
[INFO] flink-bucketing-sink-test .......................... SUCCESS [  1.580 s]
[INFO] flink-distributed-cache-via-blob ................... SUCCESS [  0.880 s]
[INFO] flink-high-parallelism-iterations-test ............. SUCCESS [  7.606 s]
[INFO] flink-stream-stateful-job-upgrade-test ............. SUCCESS [  1.518 s]
[INFO] flink-queryable-state-test ......................... SUCCESS [  2.314 s]
[INFO] flink-local-recovery-and-allocation-test ........... SUCCESS [  0.966 s]
[INFO] flink-elasticsearch2-test .......................... SUCCESS [  4.529 s]
[INFO] flink-elasticsearch5-test .......................... SUCCESS [  5.285 s]
[INFO] flink-elasticsearch6-test .......................... SUCCESS [  3.856 s]
[INFO] flink-quickstart ................................... SUCCESS [  1.481 s]
[INFO] flink-quickstart-java .............................. SUCCESS [  4.658 s]
[INFO] flink-quickstart-scala ............................. SUCCESS [  0.414 s]
[INFO] flink-quickstart-test .............................. SUCCESS [  1.497 s]
[INFO] flink-confluent-schema-registry .................... SUCCESS [  2.361 s]
[INFO] flink-stream-state-ttl-test ........................ SUCCESS [  3.930 s]
[INFO] flink-sql-client-test .............................. SUCCESS [  3.859 s]
[INFO] flink-streaming-file-sink-test ..................... SUCCESS [  1.164 s]
[INFO] flink-state-evolution-test ......................... SUCCESS [  1.532 s]
[INFO] flink-e2e-test-utils ............................... SUCCESS [  6.745 s]
[INFO] flink-mesos ........................................ SUCCESS [ 18.941 s]
[INFO] flink-yarn ......................................... SUCCESS [  3.017 s]
[INFO] flink-gelly ........................................ SUCCESS [  5.259 s]
[INFO] flink-gelly-scala .................................. SUCCESS [ 13.110 s]
[INFO] flink-gelly-examples ............................... SUCCESS [ 11.624 s]
[INFO] flink-metrics-dropwizard ........................... SUCCESS [  1.044 s]
[INFO] flink-metrics-graphite ............................. SUCCESS [  0.570 s]
[INFO] flink-metrics-influxdb ............................. SUCCESS [  2.176 s]
[INFO] flink-metrics-prometheus ........................... SUCCESS [  1.361 s]
[INFO] flink-metrics-statsd ............................... SUCCESS [  0.956 s]
[INFO] flink-metrics-datadog .............................. SUCCESS [  0.711 s]
[INFO] flink-metrics-slf4j ................................ SUCCESS [  0.917 s]
[INFO] flink-cep-scala .................................... SUCCESS [  9.729 s]
[INFO] flink-table-uber ................................... SUCCESS [  2.603 s]
[INFO] flink-sql-client ................................... SUCCESS [  7.800 s]
[INFO] flink-python ....................................... SUCCESS [  2.724 s]
[INFO] flink-scala-shell .................................. SUCCESS [ 10.762 s]
[INFO] flink-dist ......................................... SUCCESS [ 34.086 s]
[INFO] flink-end-to-end-tests-common ...................... SUCCESS [  1.229 s]
[INFO] flink-metrics-availability-test .................... SUCCESS [  0.946 s]
[INFO] flink-metrics-reporter-prometheus-test ............. SUCCESS [  0.798 s]
[INFO] flink-heavy-deployment-stress-test ................. SUCCESS [  7.118 s]
[INFO] flink-connector-gcp-pubsub-emulator-tests .......... SUCCESS [  3.777 s]
[INFO] flink-streaming-kafka-test-base .................... SUCCESS [  1.260 s]
[INFO] flink-streaming-kafka-test ......................... SUCCESS [  6.750 s]
[INFO] flink-streaming-kafka011-test ...................... SUCCESS [  6.230 s]
[INFO] flink-streaming-kafka010-test ...................... SUCCESS [  8.173 s]
[INFO] flink-plugins-test ................................. SUCCESS [  0.799 s]
[INFO] flink-state-processor-api .......................... SUCCESS [  3.276 s]
[INFO] flink-table-runtime-blink .......................... SUCCESS [  7.159 s]
[INFO] flink-table-planner-blink .......................... SUCCESS [02:26 min]
[INFO] flink-contrib ...................................... SUCCESS [  0.070 s]
[INFO] flink-connector-wikiedits .......................... SUCCESS [  1.790 s]
[INFO] flink-yarn-tests ................................... SUCCESS [04:22 min]
[INFO] flink-fs-tests ..................................... SUCCESS [  1.905 s]
[INFO] flink-docs ......................................... SUCCESS [  2.258 s]
[INFO] flink-ml-parent .................................... SUCCESS [  0.066 s]
[INFO] flink-ml-api ....................................... SUCCESS [  1.020 s]
[INFO] flink-ml-lib ....................................... SUCCESS [  0.797 s]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  29:11 min
[INFO] Finished at: 2019-07-24T16:03:03+08:00
[INFO] ------------------------------------------------------------------------
```

去dist里启动玩耍了。

分享一下我的 `settings.xml`
```
<?xml version="1.0" encoding="UTF-8"?>

<!--
     Licensed to the Apache Software Foundation (ASF) under one
or more contributor license agreements.  See the NOTICE file
distributed with this work for additional information
regarding copyright ownership.  The ASF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at
    http://www.apache.org/licenses/LICENSE-2.0
Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an
"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
KIND, either express or implied.  See the License for the
specific language governing permissions and limitations
under the License.
-->

<!--
      | This is the configuration file for Maven. It can be specified at two levels:
 |
 |  1. User Level. This settings.xml file provides configuration for a single user,
 |                 and is normally provided in ${user.home}/.m2/settings.xml.
 |
 |                 NOTE: This location can be overridden with the CLI option:
 |
 |                 -s /path/to/user/settings.xml
 |
 |  2. Global Level. This settings.xml file provides configuration for all Maven
 |                 users on a machine (assuming they're all using the same Maven
 |                 installation). It's normally provided in
 |                 ${maven.home}/conf/settings.xml.
 |
 |                 NOTE: This location can be overridden with the CLI option:
 |
 |                 -gs /path/to/global/settings.xml
 |
 | The sections in this sample file are intended to give you a running start at
 | getting the most out of your Maven installation. Where appropriate, the default
 | values (values used when the setting is not specified) are provided.
 |
 |-->
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
  <!-- localRepository
          | The path to the local repository maven will use to store artifacts.
   |
   | Default: ${user.home}/.m2/repository

  -->
  <!-- interactiveMode
          | This will determine whether maven prompts you when it needs input. If set to false,
   | maven will use a sensible default value, perhaps based on some other setting, for
   | the parameter in question.
   |
   | Default: true
  <interactiveMode>true</interactiveMode>
  -->

  <!-- offline
          | Determines whether maven should attempt to connect to the network when executing a build.
   | This will have an effect on artifact downloads, artifact deployment, and others.
   |
   | Default: false
  <offline>false</offline>
  -->

  <!-- pluginGroups
          | This is a list of additional group identifiers that will be searched when resolving plugins by their prefix, i.e.
   | when invoking a command line like "mvn prefix:goal". Maven will automatically add the group identifiers
   | "org.apache.maven.plugins" and "org.codehaus.mojo" if these are not already contained in the list.
   |-->
  <pluginGroups>
    <!-- pluginGroup
              | Specifies a further group identifier to use for plugin lookup.
    <pluginGroup>com.your.plugins</pluginGroup>
    -->
  </pluginGroups>

  <!-- proxies
          | This is a list of proxies which can be used on this machine to connect to the network.
   | Unless otherwise specified (by system property or command-line switch), the first proxy
   | specification in this list marked as active will be used.
   |-->
  <proxies>
    <!-- proxy
              | Specification for one proxy, to be used in connecting to the network.
     |
    <proxy>
      <id>optional</id>
      <active>true</active>
      <protocol>http</protocol>
      <username>proxyuser</username>
      <password>proxypass</password>
      <host>proxy.host.net</host>
      <port>80</port>
      <nonProxyHosts>local.net|some.host.com</nonProxyHosts>
    </proxy>
    -->
  </proxies>

  <!-- servers
          | This is a list of authentication profiles, keyed by the server-id used within the system.
   | Authentication profiles can be used whenever maven must make a connection to a remote server.
   |-->
  <servers>
    <!-- server
              | Specifies the authentication information to use when connecting to a particular server, identified by
     | a unique name within the system (referred to by the 'id' attribute below).
     |
     | NOTE: You should either specify username/password OR privateKey/passphrase, since these pairings are
     |       used together.
     |
    <server>
      <id>deploymentRepo</id>
      <username>repouser</username>
      <password>repopwd</password>
    </server>
    -->

    <!-- Another sample, using keys to authenticate.
             <server>
      <id>siteServer</id>
      <privateKey>/path/to/private/key</privateKey>
      <passphrase>optional; leave empty if not used.</passphrase>
    </server>
    -->
  </servers>

  <!-- mirrors
          | This is a list of mirrors to be used in downloading artifacts from remote repositories.
   |
   | It works like this: a POM may declare a repository to use in resolving certain artifacts.
   | However, this repository may have problems with heavy traffic at times, so people have mirrored
   | it to several places.
   |
   | That repository definition will have a unique id, so we can create a mirror reference for that
   | repository, to be used as an alternate download site. The mirror site will be the preferred
   | server for that repository.
   |-->
  <mirrors>
    <!-- mirror
              | Specifies a repository mirror site to use instead of a given repository. The repository that
     | this mirror serves has an ID that matches the mirrorOf element of this mirror. IDs are used
     | for inheritance and direct lookup purposes, and must be unique across the set of mirrors.
     |-->
    <mirror>
      <id>nexus-aliyun</id>
      <name>Nexus aliyun</name>
      <mirrorOf>*,!jeecg,!jeecg-snapshots,!mapr-releases,!cloudera,!cdh,!confluent</mirrorOf>
      <url>http://maven.aliyun.com/nexus/content/groups/public</url>
    </mirror>
    <mirror>
      <id>mapr-public</id>
      <name>mapr-releases</name>
      <mirrorOf>mapr-releases,*,!confluent</mirrorOf>
      <url>https://maven.aliyun.com/repository/mapr-public</url>
    </mirror>
    <mirror>
      <id>cloudera</id>
      <name>cloudera</name>
      <url>https://repository.cloudera.com/artifactory/cloudera-repos</url>
      <mirrorOf>*,!mapr-releases,!confluent</mirrorOf>
    </mirror>
  </mirrors>

  <!-- profiles
          | This is a list of profiles which can be activated in a variety of ways, and which can modify
   | the build process. Profiles provided in the settings.xml are intended to provide local machine-
   | specific paths and repository locations which allow the build to work in the local environment.
   |
   | For example, if you have an integration testing plugin - like cactus - that needs to know where
   | your Tomcat instance is installed, you can provide a variable here such that the variable is
   | dereferenced during the build process to configure the cactus plugin.
   |
   | As noted above, profiles can be activated in a variety of ways. One way - the activeProfiles
   | section of this document (settings.xml) - will be discussed later. Another way essentially
   | relies on the detection of a system property, either matching a particular value for the property,
   | or merely testing its existence. Profiles can also be activated by JDK version prefix, where a
   | value of '1.4' might activate a profile when the build is executed on a JDK version of '1.4.2_07'.
   | Finally, the list of active profiles can be specified directly from the command line.
   |
   | NOTE: For profiles defined in the settings.xml, you are restricted to specifying only artifact
   |       repositories, plugin repositories, and free-form properties to be used as configuration
   |       variables for plugins in the POM.
   |
   |-->
  <profiles>
    <!-- profile
              | Specifies a set of introductions to the build process, to be activated using one or more of the
     | mechanisms described above. For inheritance purposes, and to activate profiles via <activatedProfiles/>
     | or the command line, profiles have to have an ID that is unique.
     |
     | An encouraged best practice for profile identification is to use a consistent naming convention
     | for profiles, such as 'env-dev', 'env-test', 'env-production', 'user-jdcasey', 'user-brett', etc.
     | This will make it more intuitive to understand what the set of introduced profiles is attempting
     | to accomplish, particularly when you only have a list of profile id's for debug.
     |
     | This profile example uses the JDK version to trigger activation, and provides a JDK-specific repo.
    <profile>
      <id>jdk-1.4</id>
      <activation>
        <jdk>1.4</jdk>
      </activation>
      <repositories>
        <repository>
          <id>jdk14</id>
          <name>Repository for JDK 1.4 builds</name>
          <url>http://www.myhost.com/maven/jdk14</url>
          <layout>default</layout>
          <snapshotPolicy>always</snapshotPolicy>
        </repository>
      </repositories>
    </profile>
    -->

    <!--
              | Here is another profile, activated by the system property 'target-env' with a value of 'dev',
     | which provides a specific path to the Tomcat instance. To use this, your plugin configuration
     | might hypothetically look like:
     |
     | ...
     | <plugin>
     |   <groupId>org.myco.myplugins</groupId>
     |   <artifactId>myplugin</artifactId>
     |
     |   <configuration>
     |     <tomcatLocation>${tomcatPath}</tomcatLocation>
     |   </configuration>
     | </plugin>
     | ...
     |
     | NOTE: If you just wanted to inject this configuration whenever someone set 'target-env' to
     |       anything, you could just leave off the <value/> inside the activation-property.
     |
    <profile>
      <id>env-dev</id>
      <activation>
        <property>
          <name>target-env</name>
          <value>dev</value>
        </property>
      </activation>
      <properties>
        <tomcatPath>/path/to/tomcat/instance</tomcatPath>
      </properties>
    </profile>
    -->
    <!--
    <profile>
    <id>aliyun</id>
    <repositories>
        <repository>
            <id>mapr-public</id>
            <url>https://maven.aliyun.com/repository/mapr-public</url>
            <releases>
                <enabled>true</enabled>
            </releases>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
    </repositories>
    </profile>
    -->

    </profiles>


  <!-- activeProfiles
          | List of profiles that are active for all builds.
   |
  <activeProfiles>
    <activeProfile>alwaysActiveProfile</activeProfile>
    <activeProfile>anotherAlwaysActiveProfile</activeProfile>
  </activeProfiles>
  -->
</settings>

```





