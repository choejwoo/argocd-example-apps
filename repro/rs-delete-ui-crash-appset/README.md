## 목적

이 폴더는 **ApplicationSet**(git files generator) 기반으로,
사용자 제보처럼 “old ReplicaSet이 2개 이상일 때, old RS를 선택하고 Delete를 누르면 UI가 크래시”하는 상황을
로컬에서 재현하기 위한 최소 예시입니다.

## 구성

- `argocd/applicationset.yaml`: ApplicationSet (사용자 제보 형태와 동일한 generator/template/syncOptions)
- `services/webclient/config.json`: git files generator 입력 파일(템플릿 변수 제공)
- `services/webclient/manifests/`: 실제 리소스(Deployment/Namespace + kustomization.yaml)

## 사용법

### 1) 이 폴더를 Argo CD가 접근 가능한 Git repo에 커밋

예: 당신이 테스트하는 repo에 `repro/rs-delete-ui-crash-appset`를 그대로 복사해서 커밋/푸시.

### 2) `ApplicationSet` 적용

`argocd/applicationset.yaml`에서 아래를 당신 환경에 맞게 수정:

- `spec.generators[0].git.repoURL`
- `spec.generators[0].git.revision`
- `spec.template.spec.source.repoURL`
- `spec.template.spec.source.targetRevision`

적용:

```bash
kubectl apply -f repro/rs-delete-ui-crash-appset/argocd/applicationset.yaml
```

#### 참고 (이번에 뜬 "Kind is missing" 에러 원인)

git files generator는 기본적으로 `{{ path }}` 변수를 제공하는데, 이 값이 “config.json이 있는 디렉토리”로 잡히는 환경이 있습니다.
그러면 Argo CD가 그 디렉토리를 스캔하면서 `config.json`을 매니페스트로 파싱하려다 `kind`가 없어서 실패합니다.
그래서 이 번들은 `config.json`에 `sourcePath`를 두고, 템플릿은 `{{ sourcePath }}`를 사용합니다.

### 3) old ReplicaSet 2개 만들기

`services/webclient/manifests/deployment.yaml`의 이미지 태그를 **2번 변경해서 총 3 커밋**(각 커밋 후 Argo CD가 sync)하면
ReplicaSet이 “현재 1 + old 2”로 남습니다.

예:
- 커밋 #1: `nginx:1.25` (기본)
- 커밋 #2: `nginx:1.26`
- 커밋 #3: `nginx:1.27`

### 4) UI 크래시 재현

Argo CD UI에서 생성된 애플리케이션(기본: `rs-delete-ui-crash-webclient`)로 들어가서:

- Resource tree/list에서 ReplicaSet이 3개 보이는지 확인
- **old ReplicaSet** 클릭 → 우측 Resource Details 패널에서 **DELETE** 클릭

여기서 크래시가 나면 재현 성공입니다.

