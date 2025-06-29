---
layout: post 
title: Working with Spark & Flink
category: technicalArticles
---

> From my experience working at [Simpl](https://simpl.com/). 

In this article, I write my some details and understandings around Spark and Flink. 

#### Spark 

Basic Architecture: 

<img src="{{ site.baseurl }}/public/images/basic-arch-spark.png" alt="Basic spark architecture" class="blog-image">
(Image source: https://npntraining.medium.com/apache-spark-architecture-9f3fdeffed9c)

Apache Spark is an open-source distributed computing system designed for big data processing and analytics. Spark is known for its speed and efficiency. Spark enables applications to run faster by utilising in-memory cluster computing.

The Apache Spark framework uses a master-slave architecture that consists of a driver, which runs as a master node, and many executors that run across as worker nodes in the cluster. Apache Spark can be used for batch processing and real-time processing as well.

Spark Driver: It is the master node of the Spark application. It is responsible for:
- Converting user programs into a Directed Acyclic Graph (DAG) of tasks.
- Scheduling tasks on executors and managing their execution.
- Monitoring the state of the application and coordinating with the cluster manager.
- The driver runs the main() function of the application and creates the SparkContext, which is the entry point for all Spark functionality.

Executors: They are worker nodes that execute tasks assigned by the driver. Each Spark application has its own executors, which are responsible for:
- Running individual tasks.
- Storing data in memory or disk storage as needed.
- Reporting back the status and results of tasks to the driver.
- Executors run in separate JVM processes and can be distributed across multiple nodes in a cluster.

Related snapshots from a real system: 

Configs of cluster: 
<img src="{{ site.baseurl }}/public/images/cluster-configs.png" alt="Databricks Spark cluster configs" class="blog-image">

Appearance of jobs when running a spark code: 
<img src="{{ site.baseurl }}/public/images/spark-jobs-1.png" alt="spark-jobs-1" class="blog-image">
<img src="{{ site.baseurl }}/public/images/spark-jobs-2.png" alt="spark-jobs-2" class="blog-image">

Stages of a Spark job:  
<img src="{{ site.baseurl }}/public/images/spark-job-stages.png" alt="spark-job-stages" class="blog-image">

Worker information when running a code:  
<img src="{{ site.baseurl }}/public/images/spark-executors.png" alt="spark-executors" class="blog-image">

Cluster Manager: It is responsible for managing resources across the cluster. It allocates resources to different applications and manages their lifecycle. Common cluster managers used with Spark include:
- Standalone Cluster Manager: A simple built-in manager that comes with Spark.
- Apache Mesos: A general-purpose cluster manager that can run Hadoop and other applications alongside Spark.
- Hadoop YARN: A resource management layer for Hadoop that can manage Spark applications.

Data Abstractions: 
- Resilient Distributed Datasets (RDDs): RDDs are the fundamental data structure in Spark, representing an immutable distributed collection of objects that can be processed in parallel. RDDs support two types of operations: 
  - Transformations: Operations that create a new RDD from an existing one (e.g., map, filter).
  - Actions: Operations that return a value to the driver or write data to external storage (e.g., count, collect).
- DataFrames and Datasets: DataFrames are a higher-level abstraction built on top of RDDs, providing a structured way to work with data using named columns. Datasets combine the benefits of RDDs and DataFrames, providing type safety while still allowing for optimizations through Catalyst query optimization.

Partitions: A partition is a logical chunk of data that Spark distributes across the cluster. Each partition is processed by a single task, allowing for parallel execution. When Spark reads data from a source (e.g., HDFS), it creates partitions based on the input size and configuration settings. By default, Spark creates one partition for each HDFS block (e.g., typically 128MB).

Execution Flow: 
- Job Submission: The user submits a job using spark-submit.
- Driver Initialization: The driver initializes and creates a DAG from user-defined transformations.
- Partition Creation: As data is read from sources, Spark creates partitions based on input size and block size.
- DAG Scheduling: The DAG Scheduler breaks down the job into stages based on transformations and actions.
- Task Scheduling: The Task Scheduler assigns tasks to executors based on available partitions and resources.
- Task Execution: Executors run tasks in parallel across different threads, processing their assigned partitions.
- Data Shuffling: For wide transformations (e.g., groupByKey), data may need to be shuffled between partitions, which can be costly in terms of performance.
- Results Reporting: Executors send results back to the driver upon task completion.
- Example Code Job: Spark job that reads data from a text file, filters lines containing a specific keyword, and counts them:

```
from pyspark import SparkContext
# Initialize Spark Context
sc = SparkContext("local", "Word Count")
# Read text file into an RDD
lines = sc.textFile("hdfs://path/to/input.txt")
# Check initial number of partitions
print(f"Initial number of partitions: {lines.getNumPartitions()}")
# Filter lines containing 'keyword'
filtered_lines = lines.filter(lambda line: 'keyword' in line)
# Count filtered lines
count = filtered_lines.count()
# Print result
print(f"Number of lines containing 'keyword': {count}")
# Repartitioning example
repartitioned_lines = filtered_lines.repartition(10)
print(f"Number of partitions after repartitioning: {repartitioned_lines.getNumPartitions()}")
# Stop the Spark Context
sc.stop()

Step-by-Step Execution
  Driver Initialization: The SparkContext is created, initializing the driver program.
  RDD Creation: The textFile() method creates an RDD from the input text file, automatically partitioning it based on HDFS blocks (e.g., if the file is large enough).
  Initial Partition Check: The initial number of partitions is printed using getNumPartitions().
  Transformation: The filter() transformation creates a new RDD containing only lines with 'keyword'.
  Action Execution: The count() action triggers execution across the cluster.
  Task Scheduling: The driver divides this job into stages based on transformations, creating tasks for each partition of data.
  Task Execution on Executors: Executors run these tasks in parallel threads, processing their assigned data partitions.
  Repartitioning Example: The repartition(10) method reshuffles the filtered RDD into 10 partitions to demonstrate how you can control partitioning dynamically.
```

Hardware Level Configuration:
- Cluster Configuration: When running Spark jobs at scale, hardware configuration plays a crucial role: 
  - Nodes in Cluster: A typical Spark cluster consists of several nodes (machines) configured as follows: Each node can act as either a master (running the driver) or worker (running executors). Worker nodes should have sufficient CPU cores and memory allocated based on workload requirements.
  - Resource Allocation: Each executor runs within its own JVM process on worker nodes. Configuration settings like spark.executor.memory and spark.executor.cores determine how much memory and how many cores each executor will use. Example configuration in spark-defaults.conf:
    ```
    spark.executor.memory=4g
    spark.executor.cores=2
    ```
  - Data Locality: Spark tries to schedule tasks close to where data resides (data locality) to minimize network I/O and improve performance.
  - Networking: Adequate network bandwidth is essential for efficient communication between drivers and executors, especially when shuffling data between stages.
  - Storage Systems: Integration with distributed storage systems like HDFS or cloud storage solutions (e.g., Amazon S3) allows Spark to read/write large datasets efficiently.
- More details:
  - Concise info: https://github.com/Surajv311/mDumpSWE/tree/main/Spark
  - Detailed info: https://github.com/Surajv311/mDumpSWE/blob/main/Books_ResearchPapers_read/README_book1.md

#### Flink

Basic Architecture (More snapshots of actual pipelines below): 

<img src="{{ site.baseurl }}/public/images/flink-arch.png" alt="flink-arch" class="blog-image">
(Image source: https://alibaba-cloud.medium.com/apache-flink-fundamentals-building-a-development-environment-and-configure-deploy-and-run-459b9067e8e3)

Job Manager: It is the master component responsible for coordinating the execution of jobs. It handles job submission, manages task scheduling and distribution across Task Managers, coordinates checkpoints and recovery in case of failures, and resource management. 
- The Job Manager creates a Job Graph from the submitted application and transforms it into a Task Execution Graph for execution.
- In a high-availability (HA) setup, you can have multiple Job Managers. However, only one Job Manager is active at any given time (the leader), while others remain as standby instances. This setup ensures that if the active Job Manager fails, one of the standby instances can take over without disrupting ongoing jobs. 
- Flink internally uses the Akka actor system for communication between the Job Managers and the Task Managers.
  - An actor system is a container of actors with various roles. It provides services such as scheduling, configuration, logging, and so on. It also contains a thread pool from where all actors are initiated. All actors reside in a hierarchy. Each newly created actor would be assigned to a parent. Actors talk to each other using a messaging system. Each actor has its own mailbox from where it reads all the messages. If the actors are local, the messages are shared through shared memory but if the actors are remote then messages are passed thought RPC calls. Each parent is responsible for the supervision of its children. If any error happens with the children, the parent gets notified. If an actor can solve its own problem then it can restart its children. If it cannot solve the problem then it can escalate the issue to its own parent: In Flink, an actor is a container having state and behavior. An actor's thread sequentially keeps on processing the messages it will receive in its mailbox. The state and the behavior are determined by the message it has received.
  - Useful: https://subscription.packtpub.com/book/big-data-and-business-intelligence/9781786466228/1/ch01lvl1sec9/distributed-execution
- Client: The Client is not part of the runtime but is used to submit jobs to the Job Manager. It can operate in two modes: Attached Mode (Remains connected to receive progress updates), Detached Mode (Disconnects after job submission). 
  - Task Managers: They are the worker nodes that execute the tasks assigned by the Job Manager. Each Task Manager can run multiple tasks in parallel, depending on the number of task slots it has. Another definition; A Taskmanager (TM) is a JVM process, whereas a Taskslot (TS) is a Thread within the respective JVM process (TM). The managed memory of a TM is equally split up between the TS within a TM. No CPU isolation happens between the slots, just the managed memory is divided. Moreover, TS in the same TM share TCP connections (via multiplexing) and heartbeat messages. They may also share data sets and data structures, thus reducing the per-task overhead. For example, if a Task Manager has four slots then it will allocate 25% of the memory to each slot. There could be one or more threads running in a task slot. Threads in the same slot share the same JVM. Tasks in the same JVM share TCP connections and heart beat messages. You can run multiple Task Managers on a single machine or across multiple machines. The number of Task Managers you can effectively run on a single server depends on the available resources (CPU, memory) and the workload's resource requirements. A task slot is basically a thread pool.
    - A task in Flink is an execution unit that represents a single operator or a chain of operators (subtasks) that can process data concurrently (more details on operators discussed later). The idea of slots is to slice the available resources up into smaller parts. The available managed memory is evenly distributed among all slots. CPU cycles and JVM heap memory are not properly isolated w.r.t slots. In each slot you can deploy one or more Tasks. A Flink Task is executed by a dedicated thread. Thus, you can have multiple threads running in the same slot if you have multiple Tasks deployed to it.
    - A Task represents a parallel instance of a single Flink operator or of multiple operators if they are chainable. Chaining is not always possible or desired but if applied, it will fuse operators so that they are executed by the same Task thread. This is usually more efficient since there are fewer context switches and no handing over of records to a different thread.
    - In order to improve resource utilization (especially for Tasks which need little resources) and to make the reasoning about how many slots you need to run a Flink program easier, Flink supports slot sharing. Slot sharing means that parallel instances of different operators can be deployed to the same slot. Due to this feature, Flink creates as long pipelines of different operators as possible and deploys them to the same slot.
    - Flink by default chains operators if this is possible. Chained operators run in the same thread while others run on different threads. Task chaining/Operator chaining brings one or more tasks into a single thread which reduces the impact of the de/serialization of the records that travel around your streaming flow. You can turn off chaining of operators using functions like, disableChaining(), so thread co-location will not be used as an optimization. But it may cause performance impact as there would be an extra overhead of serialization and de-serialization of data moving across different operators running on threads.  
    - Disabling operator chaining can be useful for debugging or demos, because it makes the communication between operators more observable. It might be useful in production if you have an operator that you need to isolate because it uses a library that isn't thread-safe -- but then you'd also need to disable slot sharing.
      - Useful: https://mitul227.medium.com/slot-sharing-and-operator-chaining-in-flink-388df2d85035, https://stackoverflow.com/questions/62933685/flink-disableoperatorchaining-performance-impact, https://stackoverflow.com/questions/61810974/is-one-task-one-thread-in-apache-flink, https://stackoverflow.com/questions/63842795/flink-could-a-slot-have-multiple-threads-or-only-one-thread/63843089#63843089, https://stackoverflow.com/questions/47197744/confused-about-flink-task-slot, https://stackoverflow.com/questions/52009948/multiple-jobs-or-multiple-pipelines-in-one-job-in-flink, https://stackoverflow.com/questions/55590809/can-i-have-multiple-subtasks-of-an-operator-in-the-same-slot-in-flink/55594070#55594070, https://subscription.packtpub.com/book/big-data-and-business-intelligence/9781786466228/1/ch01lvl1sec9/distributed-execution
    - Operators in Flink: 
      - Flink processes data through operators, which are functions that transform input data streams into output streams. Operators are chained together to form tasks, optimising performance by reducing overhead. Operators are classes perform transformations on the incoming data streams, allowing for complex event processing. They can transform, filter, aggregate, or perform other operations on data streams. Each operator can run in parallel across multiple instances depending on the configured parallelism.
        - Types of Operators:
          - Source Operators: Read data from external systems (e.g., Kafka).
          - Transformation Operators: Modify or filter the data (e.g., map, filter, process).
          - Sink Operators: Write data to external systems (e.g., databases, message queues).
          - Eg: SingleOutputStreamOperator is used to represent a transformation that produces one output stream. This is commonly used when applying functions like map or filter to a DataStream.
      - Example code snippet: Here’s a simple example using Flink’s DataStream API to count words from a socket stream:
        ```
        // java
        import org.apache.flink.api.common.functions.FlatMapFunction;
        import org.apache.flink.streaming.api.datastream.DataStream;
        import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
        import org.apache.flink.util.Collector;
        public class SocketTextStreamWordCount {
          public static void main(String[] args) throws Exception {
              final StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
              DataStream<String> text = env.socketTextStream("localhost", 9999);
              DataStream<Tuple2<String, Integer>> counts = text
                  .flatMap(new LineSplitter())
                  .keyBy(value -> value.f0)
                  .sum(1);
              counts.print();
              env.execute("Socket Text Stream WordCount");
          }
          public static final class LineSplitter implements FlatMapFunction<String, Tuple2<String, Integer>> {
              @Override
              public void flatMap(String value, Collector<Tuple2<String, Integer>> out) {
                  String[] tokens = value.toLowerCase().split("\\W+");
                  for (String token : tokens) {
                      if (token.length() > 0) {
                          out.collect(new Tuple2<>(token, 1));
                      }
                  }
              }
          }
        }
        /*
        In this example:
        A socket stream is created from port 9999.
        The LineSplitter operator splits lines into words and emits them as tuples.
        The stream is keyed by word and summed to count occurrences.
        */
        ```
      - Useful: https://datafans.medium.com/flink-commonly-used-operator-transformation-8832a3490d47
    - Parallelism in Flink: The parallelism of a task can be specified in Flink on different levels.  
      - Operator Level: The parallelism of an individual operator, data source, or data sink can be defined by calling its setParallelism() method. Eg: 
      - ```
        private SingleOutputStreamOperator<JsonNode> filter(DataStream<JsonNode> stream) {
            return stream.process(new SplitPayTransactionChargedEventFilter())
                    .name(NU_SPLITPAY_TRANSACTION_CHARGED_EVENT_STREAM_FILTER).setParallelism(10);
        }
        ```
      - Execution Environment Level: An execution environment defines a default parallelism for all operators, data sources, and data sinks it executes. Execution environment parallelism can be overwritten by explicitly configuring the parallelism of an operator. Eg:
      - ```
        final StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        env.setParallelism(Integer.valueOf(parameterTool.get("PIPELINE_PARALLELISM")));
        ```
      - When calling setParallelism on an operator, then it changes the parallelism of this specific operator. Consequently, you can set for each operator a different parallelism. However, be aware that operators with different parallelism cannot be chained and entail a rebalance (similar to a shuffle) operation. If you want to set the parallelism for all operators, then you have to do it via the ExecutionEnvironment#setParallelism API call.
      - Useful: https://nightlies.apache.org/flink/flink-docs-master/docs/dev/datastream/execution/parallel/, https://stackoverflow.com/questions/44436401/some-puzzles-for-the-operator-parallelism-in-flink/44437182#44437182
  - If we deploy flink on EKS clusters, you can define in the deployment yaml config the parallelism you want to set. Some cases:
    - Case 1:
      - In eks config: taskmanager.numberOfTaskSlots: "13" and job: parallelism: 7
      - In application code env.setParallelism() at global level: Not set
      - Say you use 3 operators - total parallelism would be 7*3 -> 21. 
      - Total task managers created by flink would be: 7/13 = 1. Least number of task managers to spin up is 1. 
        <img src="{{ site.baseurl }}/public/images/flink-ui1.png" alt="flink-ui1" class="blog-image">
        <img src="{{ site.baseurl }}/public/images/flink-ui2.png" alt="flink-ui2" class="blog-image">
    - Number of task managers spinned up in a flink pipeline = Parallelism / Total task slots.
    - Case 2:
      - In eks config: taskmanager.numberOfTaskSlots: "2" and job: parallelism: 8
      - In application code env.setParallelism() at global level: Not set
      - Total task managers formed would be 8/2 = 4. 
      - Understand that, parallelism is a slightly complex concept in Flink, to make things easy we are only considering global level parallelism in our use cases.
    - Case 3:
      - In eks config: taskmanager.numberOfTaskSlots: "13" and job: parallelism: 7
      - In application code env.setParallelism() at global level: 91
      - Observe, global level parallelism is 91. It picks up parallelism defined here rather than the eks config which is 7 and applies to all 3 operators as you see, it takes precedence. Similarly, task slots is 13, and since parallelism is 91, total task managers spawned up would be 91/13 ~ 7. So 7 task managers each having 13 task slots contribute to the overall defined parallelism of 91. There are total 273 tasks running because, 3 operators each with parallelism 91 ~ 91*3 = 273. It means, inside 1 task slot, 3 tasks are running - so each operator is running 1 task (we have 3 operators in action as seen). 
      <img src="{{ site.baseurl }}/public/images/flink-ui3.png" alt="flink-ui3" class="blog-image">
      <img src="{{ site.baseurl }}/public/images/flink-ui4.png" alt="flink-ui4" class="blog-image">
      - In above pic, the pipeline is in failed state (red colour status in pic) probably because the underlying infra in the cluster is not able to support or provide for the given configuration. To fix it, one has to scale up the infra in cluster as well to support it, else tweak the configurations in code.  For example: If each task manager has a config requirement defined as: taskManager: resource: memory: "2048m" cpu: 1; Then if 7 task managers are spinned up, then underlying machines should have the required infra. Also note scaling is 1 part, underlying infra should also be there in the fleet of machines/cluster to support the scaling.
    - How to decide parallelism in flink?: Ideally you set the parallelism to the number of slots you have in your cluster. Another example: Imagine you are choosing between a parallelism of 50 with 2 cores per slot, or a parallelism of 100 with 1 core per slot. In both cases the same resources are available -- which will perform better?: One can expect fewer slots with more cores per slot to perform somewhat better, in general, provided there are enough tasks/threads per slot to keep cores busy (if the whole pipeline fits into one task this might not be true, though deserializers can also run in their own thread). With fewer slots you'll have more keys and key groups per slot, which will help to avoid data skew, and with fewer tasks, checkpointing (if enabled) will be a bit better behaved. Inter-process communication is also a little more likely to be able to take an optimized (in-memory) path.
    - Useful: https://stackoverflow.com/questions/72345290/intuition-for-setting-appropriate-parallelism-of-operators-in-flink, https://stackoverflow.com/questions/50719147/apache-flink-guideliness-for-setting-parallelism, https://stackoverflow.com/questions/56222665/how-does-the-flink-optimizer-decide-on-parallelism
- Checkpointing and State Management:
  - Check pointing is Flink's backbone for providing consistent fault tolerance. It keeps on taking consistent snapshots for distributed data streams and executor states. It is inspired by the Chandy-Lamport algorithm but has been modified for Flink's tailored requirement. Flink supports stateful computations where operators maintain state across events.
  - The fault-tolerant mechanism keeps on creating lightweight snapshots for the data flows. They therefore continue the functionality without any significant over-burden. Generally the state of the data flow is kept in a configured place such as HDFS. In case of any failure, Flink stops the executors and resets them and starts executing from the latest available checkpoint.
  - Stream barriers are core elements of Flink's snapshots. They are ingested into data streams without affecting the flow. Barriers never overtake the records. They group sets of records into a snapshot. Each barrier carries a unique ID. The following diagram shows how the barriers are injected into the data stream for snapshots:
    <img src="{{ site.baseurl }}/public/images/checkpointing-flink.png" alt="checkpointing-flink" class="blog-image">
    (Source: https://medium.com/@akash.d.goel/apache-flink-series-part-6-4ef9ad38e051)
  - Each snapshot state is reported to the Flink Job Manager's checkpoint coordinator. While drawing snapshots, Flink handles the alignment of records in order to avoid re-processing the same records because of any failure. This alignment generally takes some milliseconds. But for some intense applications, where even millisecond latency is not acceptable, we have an option to choose low latency over exactly a single record processing. By default, Flink processes each record exactly once. If any application needs low latency and is fine with at least a single delivery, we can switch off that trigger. This will skip the alignment and will improve the latency.
- Resource Manager: The Resource Manager oversees resource allocation across the cluster, ensuring that Task Managers have enough resources to execute their tasks effectively.
- Deployment Modes: Apache Flink supports different deployment modes for Flink jobs. JobManager supports multiple job submission modes such as: 
  - Application mode: This mode creates one cluster per submitted application. A dedicated JobManager is started for submitting the job. The JobManager will only execute this job, then exit.
  - Session mode: Session mode assumes an already running cluster and uses the resources of that cluster to execute any submitted application. Applications executed in the same (session) cluster use, and consequently compete for, the same resources. This has the advantage that you do not pay the resource overhead of spinning up a full cluster for every submitted job.
- Data movement in Flink: 
  - Shuffle and rebalance are two important concepts related to data distribution and task execution. 
    - The shuffle() operation redistributes data across partitions in a random manner. It partitions elements uniformly according to a uniform distribution.
    - The rebalance() operation redistributes data across partitions in a round-robin fashion, ensuring that each partition receives an equal amount of data.
    - They both involve redistributing data across partitions but do so in different ways and for different purposes. When operations like keyBy(), shuffle(), or rebalance() are encountered, Flink performs network shuffling to redistribute data between tasks.
    - Useful: https://stackoverflow.com/questions/43956510/difference-between-shuffle-and-rebalance-in-apache-flink, https://stackoverflow.com/questions/46464417/the-strategy-of-apache-flink-shuffle-is-it-like-shuffle-in-hadoop?rq=3
- Flink Deployment in EKS: In summary; 
  <img src="{{ site.baseurl }}/public/images/flink-eks-deployment.png.png" alt="flink-eks-deployment.png" class="blog-image">
  (Source: https://awslabs.github.io/data-on-eks/docs/blueprints/streaming-platforms/flink)
- At Simpl: Historically, we had on-demand EC2 machines, running flink pipelines (JobManager, TaskManagers, etc.). It was a shared cluster. As number of pipelines started to increase it was observed that there were issues, like if one pipeline consumes more resources, other pipelines were effected. 
  - More details around issues faced in past around this, written here: https://cgoyal.substack.com/p/building-real-time-efficiency
  - Hence we moved to EKS with application mode in Flink. EKS is running on spot instances machines in AWS (cost optimization). 
  - Few K8s terminologies: 
    - Controller: Control loops that watch the state of your cluster and request changes where needed, trying to move the current state closer to the desired state. Kubernetes already comes with built-in controllers that run inside the kube-controller-manager.
    - Daemon Set: A component that makes sure a pod is running across a set of nodes in a cluster. A daemon set creates pods when a node is added, and garbage collects pods whenever a node is removed from a cluster.
    - etcd: An open-source distributed key-value store which stores and manages the configuration data, state data and metadata for Kubernetes. etcd serves as the 'single truth' about the status of the system.
    - Ingress: An API object that allows external access (aka outside the Kubernetes cluster) to your application. You need an ingress controller to read the Ingress resource information, process that data and get traffic into your Kubernetes cluster.
    - Kubectl: A command line tool for communicating with a Kubernetes API server, used to create and manage Kubernetes objects.
    - Kubelet: Kubelets are an essential part of a Kubernetes cluster. Part of each node in the cluster, Kubelets make sure containers are actually running in a pod via the Kubernetes API server. They're also responsible for registering a node within a cluster and reporting on resource utilisation.
    - Kube-controller-manager: Embeds the core control loops shipped with Kubernetes.
    - Kubernetes API Server: Provides the frontend to the cluster's shared state, which is where all of the other components interact, and validates and configures data for API objects.
    - Kube-proxy: A network proxy that runs on each node in your cluster, maintaining network rules on nodes, which allows for network communication to your pods. It can run in one of these three modes: userspace, iptables or IPVS.
    - Kube-scheduler: The default scheduler for Kubernetes, kube-scheduler is charge of scheduling pods onto nodes.
    - Minikube: A tool that runs a single-node cluster inside a Virtual Machine on your computer. You can use Minikube to test out Kubernetes in a learning environment.
    - Namespace: A virtual cluster where you can provision resources and provide scope for pods, services and deployments. They provide a scope for unique naming in order to divide cluster resources in an environment when there are several teams and/or projects.  
    - Operators: A way to make use of custom resources to create and manage applications and components.
    - Pod: The smallest object of the Kubernetes ecosystem, a Pod represents a group of one or more containers running together on your cluster.
    - Service: An abstraction which defines a set of pods and makes sure that network traffic can be directed to the pods for the workload.
  - Flink logs are pushed to Opensearch in flink related log index & deployment pipeline uses concourse. Consider this config:
    ```
    taskmanager.numberOfTaskSlots: "5"
    jobManager:
      resource:
        memory: "2048m"
        cpu: 2  
    taskManager:
      resource:
        memory: "16g"
        cpu: 4
    ## And say we set the pipeline parallelism as 10. 
    ## This would mean 10/5 ~ 2 Task managers would be spawned. We must have underlying infra with the given requirements else, it would be difficult to scale up. 
    ## Assume we have 1 Job manager
    ```
    - Pod is the smallest unit in K8s. In a single pod, it is possible to have multiple containers running, but generally there is only a single container running. You can image a 1:1 mapping in Pod:Container.
    - Based on above configs ideally there would be 1 (for JManager) + 2 (for TManager) = 3 containers running in the namespace or 3 pods running in the namespace. 
    - Pods will be running on a single/multiple EC2 machines, but it will be ensured that the infra which is given in defined in config file is provided. So consider the 2 Task managers, each one running in a pod would be running on top of a machine with 16g memory and 4 cpu cores. For both of them it would be 32g memory and 8 cpu cores in total. 
    - It depends if your underlying infra has these resources to provide for the task/job managers. If not, pipeline will start failing. 
    - We use Karpenter to scale K8s infra & other aspects to improvise the overall ecosystem like auto-scaling, etc., is wip. 
- Comparing Flink & Spark
  - Similarities in Architecture:
    - Driver Node vs. Job Manager
      - Spark Driver: In Spark, the driver node is responsible for orchestrating the execution of tasks, managing the Spark Context, and scheduling jobs.
      - Flink Job Manager: Similarly, in Flink, the Job Manager performs these functions by coordinating job execution, scheduling tasks, and managing resources across the cluster.
    - Worker Nodes vs. Task Managers
      - Spark Workers: Spark's worker nodes execute tasks assigned by the driver. Each worker can run multiple executors, which are responsible for running individual tasks.
      - Flink Task Managers: Flink's Task Managers are analogous to Spark workers. They execute the tasks assigned by the Job Manager and manage task slots to run multiple instances of operators concurrently.
    - Task Slots vs. Executors
      - Spark Executors: Executors in Spark are responsible for executing tasks and managing memory and resources for those tasks.
      - Flink Task Slots: In Flink, task slots represent the capacity of a Task Manager to run parallel instances of operators. Each task slot can execute one operator instance at a time.
    - Support for Batch and Streaming Processing
      - Unified Processing Capabilities: Both Spark and Flink provide capabilities to handle both batch and streaming data processing. While they have different underlying architectures (micro-batching for Spark and true streaming for Flink), they allow users to work with various types of data workloads using similar APIs.
      - APIs for Data Processing: Both frameworks offer high-level APIs that abstract the complexities of distributed data processing, making it easier for developers to implement data transformations and analytics without needing to manage the underlying infrastructure directly.
  - Key Differences:
    - Processing Model: Batch vs. Streaming
      - Spark: Originally designed for batch processing, Spark introduced micro-batching to handle streaming data. This means it processes data in small batches rather than as individual events.
        - Batch Processing: Spark's batch processing model is highly optimized for large-scale data operations, leveraging in-memory computation to speed up data processing significantly compared to traditional disk-based systems.
        - Micro-Batching: In Spark Streaming, data is divided into micro-batches, which can introduce latency due to the waiting period for enough data to form a batch. The duration of these micro-batches can be configured, impacting both latency and throughput.
        - Structured Streaming: With the introduction of Structured Streaming, Spark has improved its ability to handle continuous data streams while providing a more unified API for both batch and streaming workloads.
      - Flink: Flink is built from the ground up as a true streaming engine, processing each event in real-time with very low latency. This allows it to handle streaming workloads more efficiently than Spark.
        - Event-Driven Architecture: Flink’s architecture is inherently event-driven, allowing it to react to incoming data immediately without waiting for a batch, thus providing true real-time processing capabilities.
        - Unified Processing Model: Flink treats both batch and streaming data as streams, allowing users to apply the same operations regardless of the data type. This simplifies development and reduces complexity in workflows.
      - Latency and Throughput
        - Spark: The micro-batch model can introduce latency since it waits for a batch of data before processing.
          - Latency Factors: The latency can vary based on the size of the micro-batch and the complexity of the transformations being applied. For applications requiring low-latency responses, tuning the batch interval is crucial.
          - Throughput Optimization: Spark optimizes throughput through techniques like partitioning and parallel execution, which can help mitigate some latency issues but may not eliminate them entirely.
        - Flink: Flink’s event-driven architecture allows for lower latency since it processes each event as soon as it arrives.
          - Sub-Second Latency: Flink is capable of achieving sub-second latencies even under high load conditions due to its efficient resource management and pipelined execution model.
          - Backpressure Handling: Flink’s backpressure mechanism dynamically adjusts the flow of data between operators based on their processing speeds, ensuring that no single component becomes a bottleneck.
      - State Management: State Handling
        - Spark: While Spark can maintain state through its structured streaming API, it does not have built-in support for complex stateful operations.
          - Stateful Operations in Structured Streaming: Spark allows maintaining state across batches using stateful transformations like mapGroupsWithState, but these operations may not be as flexible or efficient as Flink's state handling capabilities.
          - External State Management: For complex state management needs, Spark often relies on external systems (like databases or key-value stores), which can introduce additional latency and complexity.
        - Flink: Flink provides robust state management capabilities with exactly-once processing semantics, making it suitable for complex event-driven applications that require maintaining state over time.
          - Keyed State vs. Operator State: Flink differentiates between keyed state (state associated with specific keys) and operator state (state associated with an operator), allowing fine-grained control over how state is managed and accessed.
          - State Backends: Flink supports various state backends (e.g., RocksDB, Memory) that allow users to choose how state is stored and managed during processing. This flexibility can optimize performance based on workload characteristics.
      - Fault Tolerance: Fault Recovery
        - Spark: Utilizes Resilient Distributed Datasets (RDDs) to achieve fault tolerance by reconstructing lost data from lineage information.
          - RDD Lineage Graphs: RDDs maintain lineage information that allows Spark to recompute lost partitions from original data sources or previous transformations, which is efficient but may incur overhead during recovery.
          - Checkpointing Mechanisms: Spark supports checkpointing RDDs to reliable storage systems (like HDFS), enabling recovery from failures without needing to recompute everything from scratch.
        - Flink: Implements a distributed snapshot mechanism that captures application state at checkpoints, allowing for faster recovery with minimal disruption.
          - Lightweight Snapshots: Flink’s checkpointing mechanism uses lightweight snapshots that capture the current state of all operators without stopping the entire job. This ensures minimal disruption during recovery processes.
          - Stream Barriers for Consistency: Flink uses stream barriers to delineate records that belong to different snapshots, ensuring that all records are processed consistently across distributed operators during recovery.
- Stateful pipelines:
  - Making any changes in operators, like changing its UID or so, in a pipeline where deployment configuration eks yaml is set to last-state and redeploying changes in Flink-EKS, can cause issues with job coming up as the job manager may run into issues when constructing the job graph from the old checkpoint if not handled properly. Even if we clean up the checkpoint/savepoint folder (btw, in a stateful pipeline, there should be both checkpoint/savepoint folders, else it will give errors. We can’t have either of one only) and redeploy, there can be issues.
  - One may have to clear up all the resources of the EKS container and rebuilt it (with whatever config is present in eks yaml file and use the kubectl commands). Other way is we set the pipeline to stateless and deploy with the new changes, let it checkpoint in background, and then revert back upgradeMode state to last-state, with this it may come up without issues. For unprocessed data in stream you could set the kafka offset to 1hr back (temporarily, later revert to latest offset), so that it reprocesses from an hour back. Of course, if requirement is at-least once, then this has to be handled differently. 
  ```
  KafkaSource.builder().setStartingOffsets(OffsetsInitializer.latest()) - For latest offset reading
  KafkaSource.builder().setStartingOffsets(OffsetsInitializer.timestamp(1592323200000L)) - For time based offset reading. 1592323200000L - Will be epoch time in milliseconds (can be IST in our case)
  ```
  - In eks deployment yaml file you may use: `upgradeMode` key. The upgradeMode setting determines how Flink jobs behave during upgrades or restarts:
    - last-state mode: When a job is upgraded or restarted, it will attempt to restore from the most recent successful checkpoint. This preserves the processing state and allows the job to continue from where it left off.
    - stateless mode: The job will start from scratch after an upgrade, without attempting to restore any previous state. This is like a completely fresh deployment.
  - If you have checkpointing related configs in yaml; The checkpointing configuration remains active in both modes - it's just that the stateless mode ignores existing checkpoints when pipeline starting up.
- Savepoint vs Checkpoint in Flink:
  - Checkpoints:
    - Automatic, periodic snapshots for fault tolerance
    - Managed by Flink (created/deleted automatically)
    - Optimized for quick recovery from failures
  - Savepoints:
    - Manual, user-triggered snapshots for planned operations
    - Managed by users (require manual creation/deletion)
    - Complete state snapshots that support application evolution and upgrades
  - 


----------------
