Steps to reproduce:

- Ask Docker to run a Jaeger tracer: 

```
$ docker run -d --name jaeger \
>   -e COLLECTOR_ZIPKIN_HTTP_PORT=9411 \
>   -p 5775:5775/udp \
>   -p 6831:6831/udp \
>   -p 6832:6832/udp \
>   -p 5778:5778 \
>   -p 16686:16686 \
>   -p 14268:14268 \
>   -p 14250:14250 \
>   -p 9411:9411 \
>   jaegertracing/all-in-one:1.19
```

1. Get WildFly core master and build it (generate a local Maven repo, i.e. pass -Dmaven.repo.local=some_dir)
2. get WildFly master and build it using the pre-built core SNAPSHOT and use the same local Maven cache used for core
3. export some Jaeger env vars:

```
$ export JAEGER_SAMPLER_PARAM=1.0
$ export JAEGER_SAMPLER_TYPE=const
```

4. start WildFly with standalone-microprofile.xml config
5. build this app and use the previously generated Maven local cache 
6. deploy the generated WAR to WildFly
7. call http://127.0.0.1:8080/opentracing-fault-tolerance-example-1.0-SNAPSHOT/rest/hello
8. Look into Jaeger at http://localhost:16686, search for "opentracing-fault-tolerance-example-1.0-SNAPSHOT.war" service, 
   you should see 1 trace/4spans
9. call http://127.0.0.1:8080/opentracing-fault-tolerance-example-1.0-SNAPSHOT/rest/helloAsync
10. Look into Jaeger at http://localhost:16686, search for "opentracing-fault-tolerance-example-1.0-SNAPSHOT.war" service, 
   you should now see 7 traces