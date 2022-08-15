### Why should I do this at all?
You want to test performance on Redpanda (vs Kafka itself), let's go! 

### Why should I use Open Messaging Benchmark (OMB) to do it?
Yes, there is a kafka-producer-perf-test, kafka-consumer-perf-test, and an EndToEndLatency test that all come supplied with Kafka. 
However, this is a more "standard" and full-featured way of running the benchmarks - it deploys many instances at a time, it has a more granular set of configurations defined by a yaml file, and it also provides an output file that can be charted with python.

### How is this different from the actual OMB framework?
This is originally taken from the Redpanda fork of OMB you can find here: https://github.com/redpanda-data/openmessaging-benchmark, 
its only modification is that it includes a Redpanda driver.

### How is this particular set of instructions different than the Redpanda fork of the OMB?
Normally with OMB you deploy your cluster and workers with terraform and ansible to support a particular workload and try it under different instance type configurations. 

In this case we wanted to be able to run OMB against Redpanda by deploying it **manually** rather than through TF/Ansible.

### Dependencies
- A running Redpanda cluster with 9092 and 9644 ports open to the workload runner, and enough disk/resources to handle your intended test runs
- A machine that will run your benchmark workloads. Give this enough resources (memory usually) if you want to run higher throughputs, 16-32 GB memory will help
- workload runner machine should have 1. Java 11 or higher, 2. Maven, and 3. Python3

### Let's Go

Run the following on a machine that you intend to manually run the benchmark on (note that for higher throughputs you probably want many of these instances running at a time as horizontally scaled producers are a more realistic test) 

1. Git clone from <a href="from https://github.com/redpanda-data/openmessaging-benchmark">this repo</a>
2. Build the worker/runner: enter the cloned directory and run `mvn install -Dlicense.skip=true -DskipTests=true`
3. If the maven build goes well, extract it.. `cd package/target; tar zxvf openmessaging-benchmark-0.0.1-SNAPSHOT-bin.tar.gz`
4. `cd openmessaging-benchmark-0.0.1-SNAPSHOT`


**NOTE**: In here you will see driver configurations ("drivers" are the setups for each platform you'd want to test against).. in this case we want check out the various setups for the redpanda driver, and that's in the **driver-redpanda** folder. We'll choose the "redpanda-ack-all-group-linger-1ms.yaml" in this example.

**NOTE**: You can then also see the different "workloads" configurations in the **workloads** folder.

**NOTE**: I ran my initial test on a single node instance 

5. Run the actual benchmark:
`bin/benchmark --drivers driver-redpanda/redpanda-ack-all-group-linger-1ms.yaml workloads/1-topic-1-partition-1kb.yaml`

### Modifications
As mentioned above, you may want to change the workload setup file or driver file. If you open each of them you will get a good sense of what is in there. For me, I simply stood up a single node cluster so for example in the driver file I changed the replication factor in the driver file.

You may also want to run your worker with a smaller warmup and test duration as you initally test to make sure everything works, including the generated output file. In that case open the workload file(s) you're interested in running add a new line under the existing duration line and set `warmupDurationMinutes: 1` (for example).

Another modification you'll probably want to change as appropriate is the producerRate. By default it's 50k messages per second. For example, I changed mine to be 50 per second because I was running Redpanda on my local docker and wanted to to just verify that it works to create this doc. 

### Do I need to set auto topic create = true?
No. The topic is created as necessary for each workload being run using the kafka Admin API.
