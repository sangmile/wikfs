# 매니페스트 버전 관리

## kube-applier

버전 관리 시스템에 리소스 매니페스트 모음을 저장한다. 각 커밋 후에 kubectl apply 명령어를 실행해 변경 사항이 배포된 리소스에 반영되도록 할 수 있다.

## Ksonnet

Ksonnet은 쿠버네티스 리소스를 코드로 정의하여 JSON 매니페스트를 빠르게 잘성할 수 있다. 다음은 그 예이다.

{% code title="sangmile.ksonnet" %}
```text
local k = import "../ksonnet-lib/ksonnet.beta.1/k.libsonnet";

local container = k.core.v1.container;
local deployment = k.apps.v1beta1.deployment;

local sangmileContainer =
  container.default("sangmile", "sangmile/sangmile:v1") +
  container.helpers.namedPort("http", 8080);

deployment.default("sangmile", sangmileContainer) +
deployment.mixin.spec.replicas(3)  
```
{% endcode %}

다음 명령으로 전체 JSON 디플로이먼트 매니페스트로 변환한다.

```text
$ jsonnet sangmile.ksonnet
```



