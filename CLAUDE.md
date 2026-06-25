# STON Docs — Claude 작업 지침

## 프로젝트 개요

STON Edge Server 공식 매뉴얼 문서를 RST(reStructuredText) + Sphinx 포맷으로 관리하는 저장소.

- 문서 위치: `admin/*.rst`
- 빌드 도구: Sphinx 7.x (`requirements/docs.txt`)
- 발행 후 `winesoft-llm-wiki` MCP의 **`ston` 컬렉션**으로 검색된다.
- 실제 엔진 소스: `C:\_WineSOFT\Bitbucket\WineSOFT-Workspace\STON-Edge-Server`

---

## MCP 조회 원칙

**`winesoft-llm-wiki`** — 기발행된 매뉴얼 + KB 검색 (현재 문서 상태 파악)

### 작업 순서

1. **winesoft-llm-wiki 먼저 조회** — "이 기능이 이미 어떻게 문서화되어 있는지 확인"
   - `query` 검색 시 항상 `rerank: false`
   - 컬렉션 우선순위:
     1. **`["ston"]`** — 기발행 매뉴얼 (이 프로젝트의 발행물). 기능 설명·설정 항목·프로토콜 확인.
     2. **`["wiki"]`** — STON KB (운영 트러블슈팅 `wiki/ston-edge-server/ops/`, 개발 내부구조 `wiki/ston-edge-server/dev/`). `ston` 컬렉션에 없거나 동작 원리가 불명확하면 여기에서 확인.
     3. 두 컬렉션을 동시에 보려면 `["ston", "wiki"]` 지정.
   - 새 기능·설정 항목을 추가 전 **반드시 기존 문서가 있는지 확인**한다 — 중복 서술·상충 설명 방지.

2. **로컬 RST 파일** — "기존 문서 패턴 확인 후 작성/편집"
   - MCP 조회 결과를 바탕으로 `admin/*.rst` 파일을 직접 편집한다.
   - 새 항목 추가 시 인접한 기존 섹션의 RST 패턴을 따른다.

> MCP 조회 없이 임의로 기능 동작을 추측해 문서화하지 않는다.

---

## RST 작성 규칙

### 파일 구조

```
admin/<topic>.rst   ← 모든 문서 파일
index.rst           ← toctree 목차 (새 파일 추가 시 여기에도 등록)
conf.py             ← Sphinx 설정
```

### 기본 패턴

- **장/절 제목**: `*` (장) → `=` (절) → `-` (소절) 순으로 밑줄 문자 사용
- **교차 참조**: `` :ref:`label` `` — 파일 최상단에 `` .. _label: `` 앵커 정의
- **XML 설정 예시**: `::` 리터럴 블록으로 표기, 주석에 경로 명시

  ```rst
  # server.xml - <Server><VHostDefault><Options>
  # vhosts.xml - <Vhosts><Vhost><Options>

  <SettingName>Value</SettingName>
  ```

- **설정 항목 설명**: `-  ``<TagName> (기본: VALUE)``  ` 형식
- **언어**: 한국어 본문, 기술 용어(캐싱·Purge·TTL 등)는 원어 병기
- **링크 대상 참조**: 문서 내 용어 링크는 `` `Term`_ `` 형식 (하단 target 정의 불필요한 경우 `:ref:` 우선)

### 새 파일 추가 시

1. `admin/<topic>.rst` 생성
2. `index.rst` toctree에 `admin/<topic>` 추가

---

## 브랜치 생성 규칙

별도 지시가 없으면 **항상 `master` 브랜치를 기준으로** 새 브랜치를 생성한다.

```bash
git fetch origin
git checkout -b <prefix>/<STORY-KEY>-<branchName> origin/master
```

### 브랜치 네이밍

| 작업 유형 | 형식 | 예시 |
|----------|------|------|
| 문서 추가 | `feature/{STORY-KEY}-branchName` | `feature/DML-19686-purge-api-docs` |
| 문서 수정/오류 | `bugfix/{STORY-KEY}-branchName` | `bugfix/DML-22541-ttl-description` |

- `{STORY-KEY}`는 Jira 이슈 키 (예: `DML-19686`)
- `branchName`은 소문자 + 하이픈(`kebab-case`)으로 작업 내용 요약

---

## 빌드

```bash
# 로컬 Sphinx 빌드
sphinx-build -b html . _build/html

# 라이브 리로드 (개발용)
sphinx-autobuild . _build/html
```

의존성 설치: `pip install -r requirements/docs.txt`
