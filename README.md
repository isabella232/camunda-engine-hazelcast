# camunda-engine-hazelcast

Hazelcast Persistence for Camunda (Community Extension)

![Example Process][img-logo]

## Getting Started

In order to get started with Camunda and Hazelcast, you need to setup a Java Project with both Camunda and Hazelcast. If you use Apache Maven this would include the following dependencies:

```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.camunda.bpm</groupId>
      <artifactId>camunda-bom</artifactId>
      <version>${version.camunda}</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>

<dependencies>
  <dependency>
    <groupId>org.camunda.bpm</groupId>
    <artifactId>camunda-engine</artifactId>
  </dependency>
  <dependency>
    <groupId>com.hazelcast</groupId>
    <artifactId>hazelcast-all</artifactId>
    <version>3.2.1</version>
  </dependency>
  <dependency>
    <groupId>com.fasterxml.uuid</groupId>
    <artifactId>java-uuid-generator</artifactId>
  </dependency>
</dependencies>
```

Next, you create a Java Class with a Main Method configure and start Camunda:

```java
public class Server {

  public static void main(String[] args) {

    ProcessEngine processEngine = new HazelcastProcessEngineConfiguration()
      .buildProcessEngine();

    try {

      // your code goes here...

    }
    finally {
      processEngine.close();
    }

  }

}
```

This starts up the Camunda Process Engine with an embedded Hazelcast Instance.

Once you have this set up, you can deploy a bpmn process:

```java
// repository service allows managing deployments
final RepositoryService repositoryService = processEngine.getRepositoryService();

// create a new deployment of a simple workflow
repositoryService.createDeployment()
  .addModelInstance("test-process.bpmn",
    Bpmn.createExecutableProcess("testProcess")
      .startEvent()
      .receiveTask("waitForMessage")
      .endEvent()
    .done())
  .deploy();
```

In the code example above, the BPMN process is created using the fluent dsl. Resulting process
consists of a start event, a message receive task and an end event:

![Example Process][img-example1]

This process can be started like this:

```java
final RuntimeService runtimeService = processEngine.getRuntimeService();

// start the process
ProcessInstance pi = runtimeService.startProcessInstanceByKey("testProcess");

// signal the process instance to continue
runtimeService.signal(pi.getId());
```

[img-logo]: https://raw.githubusercontent.com/camunda/camunda-engine-hazelcast/master/docs/camundahazelcast.png
[img-example1]: https://raw.githubusercontent.com/camunda/camunda-engine-hazelcast/master/docs/example1.png
