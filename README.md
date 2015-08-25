# camunda-engine-hazelcast

Hazelcast Persistence for Camunda (Community Extension)

![Example Process][img-logo]

## Getting Started

In order to get started with Camunda and Hazelcast, you need to setup a Java Project which has the `camunda-engine-hazelcast` in the classpath. If you use Apache Maven this would include the following dependencies:

```xml
<dependencies>
  <dependency>
    <groupId>org.camunda.bpm.ext</groupId>
    <artifactId>camunda-engine-hazelcast</artifactId>
    <version>1.0.0-alpha1</version>
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

Read the [Camunda Documentation][docs].

[img-logo]: https://raw.githubusercontent.com/camunda/camunda-engine-hazelcast/master/docs/camundahazelcast.png
[img-example1]: https://raw.githubusercontent.com/camunda/camunda-engine-hazelcast/master/docs/example1.png
[docs]: http://docs.camunda.org
