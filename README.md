### Why should I do this at all?
You want to test performance on Redpanda (vs Kafka itself), let's go! 

### Why should I use Open Messaging Benchmark (OMB) to do it?
Yes, there is a kafka-producer-perf-test, kafka-consumer-perf-test, and an EndToEndLatency test that all come supplied with Kafka. 
However, this is a more "standard" and full-featured way of running the benchmarks - it has a more granular set of configurations defined by a yaml file, and it also provides an output file that can be charted with python.

### How is this different from the actual OMB framework?
This is originally taken from the Redpanda fork of OMB you can find here: https://github.com/redpanda-data/openmessaging-benchmark, 
its only modification is that it includes a Redpanda driver.

### How is this particular set of instructions different than the Redpanda fork of the OMB?
Normally with OMB you deploy your cluster and workers with terraform and ansible to support a particular workload and try it under different instance type configurations. 

In this case we wanted to be able to run OMB against Redpanda by deploying it **manually** rather than through TF/Ansible.

### Let's Go

1. Git clone from <a href="from https://github.com/redpanda-data/openmessaging-benchmark">this repo</a>
2. Build the worker/runner: enter the cloned directory and run `mvn install -Dlicense.skip=true -DskipTests=true`
3. If the maven build goes well, extract it.. `cd package/target; tar zxvf openmessaging-benchmark-0.0.1-SNAPSHOT-bin.tar.gz`
4. `cd openmessaging-benchmark-0.0.1-SNAPSHOT`


NOTE: In here you will see driver configurations ("drivers" are the setups for each platform you'd want to test against).. in this case we want check out the various setups for the redpanda driver, and that's in the *driver-redpanda* folder. We'll choose the "redpanda-ack-all-group-linger-1ms.yaml" in this example.

NOTE: You can then also see the different "workloads" configurations in the *workloads* folder.

NOTE: I ran my initial test on a single node instance 

5. Run the 
