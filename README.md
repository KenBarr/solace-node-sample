# How to use sample repo

## Note the service name for broker you want to test against
* kubectl get services  -> note the psb service eg.  pubsubplus-dev-1588077956-pubsubplus-dev

## Get helm chart and modify to hit broker service
* git clone git@github.com:KenBarr/solace-node-sample.git
* cd solace-node-sample
* sed -i 's/MUST_SPECIFY_SERVICE_NAME/pubsubplus-dev-1588077956-pubsubplus-dev/g' values.yaml

## Install chart
* helm install solace-node-sample . -f values.yaml

## Validate the pod is running
* kubectl get pods
```
NAME                                         READY   STATUS    RESTARTS   AGE
pubsubplus-dev-1588077956-pubsubplus-dev-0   1/1     Running   0          59m
solace-node-sample-7679d547cb-222lt          1/1     Running   0          14s
```

## Look at logs and see a subscriber is running in pod
* kubectl logs -f solace-node-sample-7679d547cb-222lt
```
[13:45:13] === Successfully connected and ready to subscribe. ===
[13:45:13] Subscribing to topic: tutorial/topic
[13:45:13] Successfully subscribed to topic: tutorial/topic
[13:45:13] === Ready to receive messages. ===
```

## Go into pod and run a publisher
* kubectl exec -it solace-node-sample-7679d547cb-222lt bash
* bash-5.0# node solace-samples-nodejs/src/basic-samples/TopicPublisher.js ws://pubsubplus-dev-1588077956-pubsubplus publisher@default default

## Look at logs and see message sent.
```
[13:47:27] Received message: "Sample Message", details:
Destination:                            [Topic tutorial/topic]
Class Of Service:                       COS1
DeliveryMode:                           DIRECT
Binary Attachment:                      len=14
  53 61 6d 70 6c 65 20 4d    65 73 73 61 67 65          Sample.Message
```





# How repo was built
* helm create solace-node-sample

* Edit Chart.yaml and values.yaml to move from nginx to node:lts-apline as well as add psb.name as a traget for node samples

* Delete folder templates/test and files templates/ingress.yaml and templates/service.yaml as we are not receiving any connections only making them.

* Edit templates/deployment.yaml to:
    - remove readyness and livelness for now.
    - add command: section to initialize node and run sample
