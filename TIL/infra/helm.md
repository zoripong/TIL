# Helm
## helm chart?

- k8s 관리 툴
- Helm Charts help you define, install, and upgrade even the most complex Kubernetes application.

## chart?

- helm의 패키지 포맷
- 하나의 애플리케이션을 설치하기 위한 파일들로 구성
- 기본적으로 템플릿의 개념을 사용
    - 템플릿 파일에 value를 채워 manifest yaml 파일을 생성하여 사용

## install (Mac OS)

```bash
brew install helm
```

## command

```bash
helm create [NAME]  # create new chart
helm lint  # linting yaml files
helm template [NAME] [CHART]  # locally render template files with value file 
helm install [NAME] [CHART]  # apply manifest files at cluster
helm history helloworld  # fetch release history
helm get manifest [NAME]  # get manifest files
helm rollback [NAME] [REVISON]
helm upgrasde [NAME] [CHART]
```

more commands ⇒ [link](https://helm.sh/docs/helm/)

## chart hooks

- Helm provides a hook mechanism to allow chart developers to intervene at certain points in a release’s life cycle
- hook을 이용하여 install / upgrade / rollback / delete / test 전후에 특정 스크립트 실행 가능
- [https://helm.sh/docs/topics/charts_hooks/](https://helm.sh/docs/topics/charts_hooks/)

## chart template

```bash
helm create mychart # 프로젝트 기본 뼈대 생성
```

[https://helm.sh/docs/chart_template_guide/getting_started/](https://helm.sh/docs/chart_template_guide/getting_started/)

## apply order

- templates 하단에 있는 파일들은 아래와 같은 순서로 클러스터에 적용 됨
    - [https://github.com/helm/helm/blob/9b42702a4bced339ff424a78ad68dd6be6e1a80a/pkg/releaseutil/kind_sorter.go#L27](https://github.com/helm/helm/blob/9b42702a4bced339ff424a78ad68dd6be6e1a80a/pkg/releaseutil/kind_sorter.go#L27)

## chart dependency

[https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/)

## Helm Repo?

[https://github.com/hypnoglow/helm-s3](https://github.com/hypnoglow/helm-s3)

```bash
helm plugin install https://github.com/hypnoglow/helm-s3.git
helm s3 version --mode
helm s3 init s3://dodo-cart-helm-repo/charts
helm package ./dodo-cart-helm-chart
helm repo add dodo-cart s3://dodo-cart-helm-repo/charts
helm s3 push ./dodo-cart-helm-chart-0.1.0.tgz dodo-cart
```

## 알아두면 좋은 것

- 2019년 11월에 Helm 3.0을 배포 하면서 tiler를 버렸음 [[참고](https://helm.sh/docs/faq/)]
    - 관련 자료 대부분 2.0 기분으로 설명
