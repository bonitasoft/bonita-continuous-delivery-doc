# How to enable remote monitoring with JMX

The Java virtual machine (Java VM) has built-in instrumentation that enables you to monitor and manage it using the [Java Management Extensions (JMX) technology](http://www.oracle.com/technetwork/articles/javase/jmx-138825.html).

To enable and configure the ready-to-use JMX agent so that it can monitor and manage the Java VM of the Bonita container, certain system properties must be set and an additional port must be exposed.

## Adding JMX system properties

BCD enables you to set system properties to the Java VM through the `bonita_java_opts` variable. Hence JMX system properties must be set in your YAML scenario as in the following example:
```yaml
bonita_java_opts: >-
  -Djava.awt.headless=true
  -Xms1024m
  -Xmx1024m
  -XX:+UseConcMarkSweepGC
  -Dcom.sun.management.jmxremote=true
  -Dcom.sun.management.jmxremote.port=9010
  -Dcom.sun.management.jmxremote.rmi.port=9010
  -Dcom.sun.management.jmxremote.local.only=false
  -Dcom.sun.management.jmxremote.authenticate=false
  -Dcom.sun.management.jmxremote.ssl=false
  -Djava.rmi.server.hostname={{ ec2_public_dns_name | default(ansible_fqdn) }}
```

This example configures port `9010` as JMX remote port.


## Exposing additional ports

By default the Bonita container exposes ports **8080** (Tomcat HTTP port) and **5701** (Hazelcast cluster communication port) to the Docker host. Then BCD publish these 2 ports as follows:
- Port 8080 is always published and mapped to the host port defined by `bonita_port` variable
- Port 5701 is only mapped to the host port defined by `bonita_hazelcast_port` variable when `bonita_cluster_mode` is enabled

Additionally BCD enables you to publish extra ports through the `bonita_published_ports_extra` variable. Hence additional port mappings must be added to your YAML scenario as in the following example:
```yaml
bonita_published_ports_extra:
  - "9020:9010"
```

As a result you can now connect monitoring tools like JConsole or Java VisualVM using the JMX URL `<bonita_docker_host_fqdn_or_ip>:9020`.
