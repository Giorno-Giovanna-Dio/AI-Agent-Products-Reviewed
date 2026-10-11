<p align="center">
  <img src="assets/banner.jpg" alt="AI Agent Products Reviewed. A public catalog of AI agent products." width="100%">
</p>

<p align="center">
  <a href="README.md">English</a>
  &nbsp;·&nbsp;
  <a href="README.zh-TW.md">繁體中文</a>
  &nbsp;·&nbsp;
  <a href="README.ja.md">日本語</a>
  &nbsp;·&nbsp;
  <strong>한국어</strong>
  &nbsp;·&nbsp;
  <a href="README.es.md">Español</a>
</p>

<p align="center">
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-3d3a36" alt="MIT License"></a>
  <a href="CONTRIBUTING.md"><img src="https://img.shields.io/badge/PRs-welcome-3d3a36" alt="PRs welcome"></a>
  <a href="https://github.com/sponsors/Giorno-Giovanna-Dio"><img src="https://img.shields.io/badge/sponsor-GitHub%20Sponsors-ea4aaa?logo=githubsponsors&logoColor=white" alt="GitHub Sponsors"></a>
</p>

<p align="center">
  <a href="#contents">목차</a>
  &nbsp;·&nbsp;
  <a href="#contributing">기여</a>
  &nbsp;·&nbsp;
  <a href="CODE_OF_CONDUCT.md">행동 규범</a>
  &nbsp;·&nbsp;
  <a href="#support">지원</a>
</p>

# AI Agent Products Reviewed

이것은 AI agent 제품의 공개 허브입니다. 제품, 프레임워크, 워크벤치,
메모리 층, 런타임은 저장소와 공식 사이트에 흩어져 있습니다. 이 목록은
그것들을 모읍니다. 각 제품은 하나의 Cell입니다. 그것이 무엇인지, 그리고
AI agent 오케스트레이션 팀의 어디를 채우는지 알 수 있습니다.

목록은 비교를 위한 것이고, 순위를 매기기 위한 것이 아닙니다. 각 제품을
같은 질문으로 읽습니다. agent, 과제, 맥락, 실행 환경, 사람의 감독이 어떻게
짜여 있고, 그것이 2D 또는 3D AI agent 작업 공간 안에서 무엇이 되는지.

라이선스는 [MIT](LICENSE)입니다. 함께 일하는 방식은
[행동 규범](CODE_OF_CONDUCT.md)에 있습니다. 인사이트 노트, 행동 규범,
기여 가이드의 본문은 번체 중국어입니다.

<h2 id="contributing">기여를 환영합니다</h2>

**기여를 환영합니다.** 이 목록은 아직 흩어져 있는 AI agent 제품을
더해 주는 사람에게 기대어 있습니다.

- 아직 없는 제품을 제안한다.
- 인사이트 노트를 쓰고, 오케스트레이션 팀의 한 자리에 놓는다.
- 이미 있는 Cell을 고친다. 오래된 사실, 끊긴 링크, 잘못된 자리.

[CONTRIBUTING.md](CONTRIBUTING.md)부터 시작하세요. 새 제품은 pull request
하나에 하나입니다. 써 본 적이 없어도 노트를 쓸 수 있습니다. status는
`untried`로 둡니다. 여기에 한 줄을 넣을 때는 같은 Cell을 같은 자리,
같은 링크로 [README.md](README.md), [README.zh-TW.md](README.zh-TW.md),
[README.ja.md](README.ja.md), [README.es.md](README.es.md)에도 넣습니다.

<h2 id="contents">목차</h2>

- [기여를 환영합니다](#contributing)
- [연구 목표](#research-goals)
- [저장소 구성](#repository-layout)
- [Cell 모델](#cell-model)
- [리뷰 절차](#review-process)
- [오케스트레이션 팀에서의 위치](#orchestration-team)
- [이 목록을 지원하기](#support)

직접 실행할 프로젝트는 이 저장소 밖에 clone합니다. 이 저장소가 남기는 것은
출처 링크, 제품과 기능 분석, 실제로 써 본 것, 그리고 2D 또는 3D 작업 공간에
대한 시사입니다. 공개 저장소가 없는 제품은 공식 페이지와 얻을 수 있는
버전 정보로 기록합니다.

<h2 id="research-goals">연구 목표</h2>

각 Cell 리뷰는 다음에 답하기 위한 것입니다.

- 그것이 어떤 새로운 agent 작업 공간, 또는 상호 방식을 나타내는가.
- agent, 과제, 브랜치, 샌드박스, 산출물, 진행을 어떻게 보여 주는가.
- 사람은 어떻게 맡기고, 비교하고, 끼어들고, 확인하고, 통제를 되돌리는가.
- 어떤 능력이 모델에서 오고, 어떤 능력이 agent 런타임이나 오케스트레이션에서 오는가.
- 2D 작업 공간(평면, 또는 픽셀 작업장)에서는 무엇이 되는가.
- 3D 작업 공간(걸어 들어갈 수 있는 사무실. 3D 물체가 아니다)에서는 무엇이 되는가.
- 어떤 패턴을 받아들이고, 다시 설계하고, 또는 일부러 피할 가치가 있는가.

주요 연구 각도:

1. 작업 공간과 공간 구성
2. 멀티 에이전트 오케스트레이션
3. 맥락, 메모리, 인수인계
4. 런타임, 샌드박스, 권한
5. 상태, 진행, 관찰
6. 사람이 루프에 들어가는 통제
7. 산출물, 출처, 리뷰
8. 협업과 확장

<h2 id="repository-layout">저장소 구성</h2>

- [`README.md`](README.md): 영어 목록. 저장소의 홈페이지입니다.
- [`README.zh-TW.md`](README.zh-TW.md): 번체 중국어 목록.
- [`README.ja.md`](README.ja.md): 일본어 목록.
- [`README.ko.md`](README.ko.md): 이 한국어 목록.
- [`README.es.md`](README.es.md): 스페인어 목록.
- [`CONTRIBUTING.md`](CONTRIBUTING.md): Cell을 제안하고, 더하고, 고치는 방법. 본문은 번체 중국어입니다.
- [`cells.yaml`](cells.yaml): 모든 Cell의 구조화된 metadata.
- [`reviews/README.md`](reviews/README.md): 공통 리뷰 규칙과 증거 기준.
- [`reviews/`](reviews/): Cell마다 하나의 인사이트 노트. 브라우저에서는 GitHub 렌더 링크로 읽으세요. [`reviews/README.md`](reviews/README.md)를 보세요.
- `/workspace-labs/<cell-name>`: 직접 시험할 때의 제안 위치. 이 저장소 밖이며 Git이 추적하지 않습니다.

<h2 id="cell-model">Cell 모델</h2>

**Cell**은 표 안의 하나의 연구 단위입니다. Cell은 upstream 소스 트리를 두지 않습니다.
출처로 링크하고, 제품에서 배운 것을 남깁니다.

- 저장소가 있는 Cell의 ID는 canonical GitHub 저장소 URL입니다. 예: `https://github.com/mattpocock/sandcastle`.
- 공개 저장소가 없는 제품은 공식 canonical URL을 씁니다. 예: `https://www.conductor.build/`.
- GitHub URL에서는 `.git`, query, fragment, 끝의 `/`를 뺍니다.
- 저장소 이름이 바뀌거나 옮겨지면, 새 canonical URL이 ID가 되고 이전 URL은 `aliases`에 들어갑니다.

<h2 id="review-process">리뷰 절차</h2>

1. 제품을 `cells.yaml`에 Cell로 넣고 status는 `untried`로 합니다. [`CONTRIBUTING.md`](CONTRIBUTING.md)를 따르세요. 출처가 GitHub 저장소나 공식 URL이면 [`/create-cell-pr`](.cursor/skills/create-cell-pr/SKILL.md)를 불러, 서브에이전트가 노트와 전용 PR을 쓰게 할 수도 있습니다.
2. 출처가 공개되어 있으면 별도의 실험 장소에 clone하고, 실제로 시험한 전체 commit SHA를 기록합니다. 그렇지 않으면 제품 버전을 기록합니다.
3. [`reviews/README.md`](reviews/README.md)를 따르고, [`reviews/_template.md`](reviews/_template.md)에서 시작합니다.
4. 공식 문서, 소스, 데모로 중요한 질문에 답할 수 없을 때만, upstream이 권하는 방식으로 직접 확인합니다. Docker는 필수가 아닙니다.
5. tried / untried 상태와 초기 읽기를 갱신하고, 아래의 한 자리에 Cell을 놓습니다. 하나의 분명한 변경은 하나의 atomic commit으로 합니다.

후보 프로젝트를 이 저장소 안에 두지 마세요. 실험에 코드 변경이 필요하면 그 제품을 fork합니다. 코드 변경은 fork가 가지고, 리뷰는 여기에 남습니다.

<h2 id="orchestration-team">오케스트레이션 팀에서의 위치</h2>

이 표들은 제품이 무엇인지, 그리고 AI agent 오케스트레이션 팀의 어느 부분을
채우는지를 분류합니다. 각 Cell의 주된 자리는 하나입니다. 옆 자리에도 닿는
능력은 「기여」에 씁니다. 실제로 써 봤는지는 노트와 [`cells.yaml`](cells.yaml)의
`evaluation.status`에 남습니다.

Cell 이름은 GitHub에서 렌더된 인사이트 노트로 연결됩니다. Cell ID는 노트 맨 위와
[`cells.yaml`](cells.yaml)에 있습니다.

| 자리 | 팀에서 하는 일 |
| --- | --- |
| 오케스트레이션 | 일을 나누고, 알맞은 agent에게 맡기고, 결과를 되돌린다. |
| 거버넌스 | 목표, 인원, 예산, 권한, 그리고 아무도 보지 않을 때 일을 시작해도 되는지를 다룬다. |
| 워크벤치 | 사람이 여러 agent를 동시에 보고, 비교하고, 그 안으로 들어갈 수 있게 한다. |
| 현장 | 공간으로 누가 바쁘고, 누가 기다리고, 누가 끝냈는지를 보여 준다. |
| 워커 | 읽고, 쓰고, UI를 조작하고, 말하거나 답하는 쪽과, 그것들을 조립하는 런타임. |
| 메모리 | 앞선 맥락을 다음 차례와 다음 agent가 쓸 수 있게 남긴다. |
| 방법 | 이 일을 어떻게 할지 말한다. 스킬, 절차, 그리고 끝났을 때의 모습. |
| 실행 경계 | 어디서 실행되는지, 어떤 파일, 네트워크, 터미널, 브라우저를 만져도 되는지를 정한다. |
| 검증 | 점수, 트레이스, 비교할 수 있는 시도를 남겨, 이번 실행이 어땠는지 판단할 수 있게 한다. |
| 산출 표면 | 팀이 함께 고치는 산출의 층. 화면, 문서, 표, 슬라이드. |

### 오케스트레이션

일을 나누고, 알맞은 agent에게 맡기고, 결과를 되돌린다.

| Cell | 성질 | 기여 |
| --- | --- | --- |
| [Sandcastle](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/sandcastle.md) | 오케스트레이션 라이브러리 | 코드로 coding agent를 격리 환경에서 실행하고, 끝나면 브랜치 전략에 따라 병합한다. |
| [Octop Harness](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-octop-harness-cell-7618/reviews/octop-harness.md) | 배포 런타임 | 한 프로세스 안에 서로 격리된 agent를 여러 명 등록한다. |
| [OpenRig](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openrig-cell-7aa3/reviews/openrig.md) | 팀 하네스 | YAML로 자리와 구성원을 적고, 한 번에 띄운 뒤 일을 배정한다. |
| [Deep Agents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-deepagents-cell-9e0b/reviews/deepagents.md) | 장기 작업 하네스 | 서브 에이전트, 가상 파일, 메모리, 사람 승인을 장기 작업 런타임으로 묶는다. |
| [CrewAI](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-crewai-cell-90bd/reviews/crewai.md) | 오케스트레이션 프레임워크 | 역할과 과제로 소대를 만들고, 바깥은 이벤트 흐름으로 분기를 다룬다. |
| [OmO](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-oh-my-openagent-cell-90bd/reviews/oh-my-openagent.md) | 오케스트레이션 계층 | 메인 세션이 일을 나누어 배정하고, 임시 작업자가 파일을 고친 뒤 증거를 돌려준다. |
| [Oh My OpenCode](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-oh-my-opencode-cell-103e/reviews/oh-my-opencode.md) | 다중 역할 플러그인 | 한 번의 개발을 인터뷰, 계획, 배정, 구현, 조사로 편성한다. |
| [AgentScope](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agentscope-cell-fb52/reviews/agentscope.md) | 오케스트레이션 프레임워크 | 코드로 agent를 조립하고, 여러 agent가 서로 일을 넘긴다. |
| [Gas Town](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/gastown.md) | CLI 오케스트레이션 | 여러 coding agent를 동시에 스케줄하고, 작업 상태는 복구할 수 있는 장부에 남긴다. |
| [Routa](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/routa.md) | 전달 조정대 | 긴 채팅을 과제, 보드, 노트, 담당자 계약으로 나눈다. |
| [Agency Swarm](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agency-swarm-cell-ad04/reviews/agency-swarm.md) | 오케스트레이션 프레임워크 | 직책과 단방향 소통 그래프로, 누가 일을 배정하고 누가 대화를 이어받을지 정한다. |
| [Agent Squad](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agent-squad-cell-ad04/reviews/agent-squad.md) | 대화 라우터 | 한 마디마다 가장 맞는 전문 agent에게 넘기고, 그 채팅을 기억한다. |
| [Amux](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-amux-cell-80a8/reviews/amux.md) | 셀프 호스트 제어 평면 | 이미 있는 coding agent에게 공유 보드, 메시지 통로, 일정을 준다. |
| [AutoAgent](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-autoagent-cell-ca43/reviews/autoagent.md) | 오케스트레이션 프레임워크 | 자연어로 담당자와 워크플로를 만들고, 분류 담당이 일을 배정한다. |
| [AutoGen](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-autogen-cell-9381/reviews/autogen.md) | 오케스트레이션 프레임워크 | 코드로, 스스로 일하고 사람과도 함께 일하는 agent 무리를 조립한다. |
| [BeeAI Framework](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-beeai-framework-cell-ad04/reviews/beeai-framework.md) | 오케스트레이션 프레임워크 | Python이나 TypeScript로, 일을 넘기는 agent와 흐름을 작성한다. |
| [Bernstein](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-bernstein-cell-80a8/reviews/bernstein.md) | 스케줄 오케스트레이션 | 목표 하나를 여러 CLI agent에게 나누고, 스케줄러가 수령, 재시도, 병합을 정한다. |
| [LangGraph](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-langgraph-cell-9381/reviews/langgraph.md) | 그래프 런타임 | 공유 상태, 노드, 간선으로, 오래 돌고 중단 후 이어갈 수 있는 흐름을 짠다. |
| [MetaGPT](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-metagpt-cell-9381/reviews/metagpt.md) | 오케스트레이션 프레임워크 | 여러 역할을 소프트웨어 회사로 편성하고, SOP에 따라 설계와 코드를 낸다. |
| [Microsoft Agent Framework](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-microsoft-agent-framework-cell-9381/reviews/microsoft-agent-framework.md) | 오케스트레이션 프레임워크 | 도구를 호출하는 agent를 작성하거나, 여러 agent를 워크플로로 잇는다. |
| [MS-Agent](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ms-agent-cell-ad04/reviews/ms-agent.md) | 장기 작업 하네스 | 계획, 권한, 서브 에이전트와, 다음 날 이어서 할 수 있는 프로젝트 메모리를 맡는다. |
| [OpenAI Agents SDK](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openai-agents-python-cell-9381/reviews/openai-agents-python.md) | agent SDK | agent, 핸드오프, 가드레일로 멀티 에이전트 흐름을 구성한다. |
| [PocketFlow](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-pocketflow-cell-ca43/reviews/pocketflow.md) | 그래프 프레임워크 | 애플리케이션 하나를 노드, 동작, 공유 저장소로 작성한다. |
| [Pragma](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-pragma-cell-80a8/reviews/pragma.md) | Agent Team 플랫폼 | 전문가, 흐름, 도구, 메모리, 사람이 승인하는 관문을 들고 다닐 수 있는 팀으로 묶는다. |
| [Youtu-Agent](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-youtu-agent-cell-ad04/reviews/youtu-agent.md) | 오케스트레이션 프레임워크 | YAML로 agent를 조립해 실행하고, 같은 설정을 평가하고 개선할 수 있다. |

### 거버넌스

목표, 인원, 예산, 권한, 그리고 아무도 보지 않을 때 일을 시작해도 되는지를 다룬다.

| Cell | 성질 | 기여 |
| --- | --- | --- |
| [Paperclip](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-paperclip-cell-7aa3/reviews/paperclip.md) | 조직 제어 평면 | 목표, 편제, 예산, heartbeat로 외부 agent를 직원으로 깨운다. |
| [Agenta](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agenta-cell-ad04/reviews/agenta.md) | 팀 워크스페이스 | 팀이 스스로 일을 시작하는 동료를 조립하고, 지시, 스킬, 권한을 조정하게 한다. |

### 워크벤치

사람이 여러 agent를 동시에 보고, 비교하고, 그 안으로 들어갈 수 있게 한다.

| Cell | 성질 | 기여 |
| --- | --- | --- |
| [Conductor](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/conductor.md) | 데스크톱 ADE | 여러 coding agent의 worktree, 미리보기, 병합을 한 콘솔에 둔다. |
| [Maestro](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/maestro.md) | 데스크톱 ADE | 키보드 우선 콘솔로 여러 프로젝트와 작업 대기열을 함께 밀어 간다. |
| [Orca](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/orca.md) | 데스크톱 ADE | CLI agent마다 worktree를 주고, 같은 앱에서 대화, 터미널, diff를 본다. |
| [cmux](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/cmux.md) | 터미널 워크스페이스 | 탭, 분할, 「당신이 필요함」 알림으로 많은 CLI 세션을 정리한다. |
| [Emdash](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/emdash.md) | 데스크톱 ADE | 과제 단위로 기존 agent를 실행하고, 같은 앱에서 diff, CI, PR을 본다. |
| [Paseo](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/paseo.md) | 셀프 호스트 제어 평면 | 데몬이 기존 CLI를 로컬에서 실행하고, 데스크톱, 휴대폰, 웹이 같은 기계로 돌아온다. |
| [Nimbalyst](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/nimbalyst.md) | 비주얼 작업대 | 사람과 agent가 같은 파일을 고치고, 병렬 세션은 worktree로 가른다. |
| [Odysseus](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-odysseus-cell-7aa3/reviews/odysseus.md) | 셀프 호스트 개인 워크스페이스 | 채팅, 조사, 문서, 메일, 할 일을 한 화면에 모은다. |
| [T3 Code](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-t3code-cell-7aa3/reviews/t3code.md) | 하네스 제어면 | 로컬에 이미 로그인된 CLI에 연결하고, 같은 UI로 스레드를 열고 diff를 보고 권한을 승인한다. |
| [OpenChamber](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openchamber-cell-0717/reviews/openchamber.md) | OpenCode 워크벤치 | 데스크톱, 브라우저, VS Code, 휴대폰에서 같은 OpenCode 세션을 감독한다. |
| [Ekko Studio](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ekko-studio-cell-fb52/reviews/ekko-studio.md) | 작업대와 노드 흐름 | 혼자 채팅, 그룹 방, 실행 가능한 노드 그래프 사이를 오간다. |
| [Codeg](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-codeg-cell-fb52/reviews/codeg.md) | 멀티 에이전트 ADE | ACP로 여러 CLI를 같은 대화, diff, 권한 확인으로 모은다. |
| [Agentrove](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agentrove-cell-c2d2/reviews/agentrove.md) | 셀프 호스트 코딩 워크스페이스 | 워크스페이스 하나에 샌드박스 하나를 묶고, ACP로 설치된 agent를 띄운다. |
| [cc-haha](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-cc-haha-cell-c2d2/reviews/cc-haha.md) | 로컬 데스크톱 작업대 | 말로 프로젝트를 고치고 diff를 본다. 휴대폰과 메신저는 이 컴퓨터로 돌아온다. |
| [iPolloWork](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/ipollowork.md) | 멀티 엔진 작업대 | OpenCode, Codex 같은 엔진을 과제, 진행, 파일의 한 흐름으로 접는다. |
| [Golutra](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/golutra.md) | 터미널 채팅방 | 로컬 CLI를 채널 구성원으로 만들고, 출력을 한 대화로 되돌린다. |
| [Claude Code Bridge](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/claude-codex-bridge.md) | CLI 워크벤치 | 여러 CLI를 동시에 보고, 메시지로 서로 일을 넘길 수 있다. |
| [AgentSpace](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agentspace-cell-80a8/reviews/agentspace.md) | 협업 웹 워크스페이스 | 사람과 역할이 있는 디지털 직원에게 메시지, 문서, 승인의 공동의 집을 준다. |
| [Buzz](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-buzz-cell-80a8/reviews/buzz.md) | 협업 워크스페이스 | 사람과 agent를 같은 채널, 스레드, 캔버스, 워크플로에 넣는다. |
| [Claude Squad](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-claude-squad-cell-80a8/reviews/claude-squad.md) | 터미널 감독 | 세션마다 worktree와 tmux를 주어 같은 디렉터리를 다투지 않게 한다. |
| [Free4chat](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-free4chat-cell-fb52/reviews/free4chat.md) | 임시 협업 방 | 링크 하나로 브라우저의 사람과 로컬 agent를 짧은 공동 작업으로 불러온다. |
| [Hermes Workspace](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-hermes-workspace-cell-ad04/reviews/hermes-workspace.md) | 웹 지휘대 | 브라우저로 Hermes의 대화, 터미널, 메모리, 스킬, 여러 작업자를 본다. |
| [Kun](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-kun-cell-ad04/reviews/kun.md) | 로컬 작업대 | Code, Design, Work, Rooms에서 목표를 확인할 수 있는 산출로 만든다. |
| [Meldwork](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-meldwork-cell-80a8/reviews/meldwork.md) | 데스크톱 ADE | 설치된 CLI를 한 사건에 둔다. 혼자 하거나, 여러 명이 각자 답하거나, 논의한 뒤 채택한다. |
| [Mycelium](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-mycelium-cell-ca43/reviews/mycelium.md) | 공유 방 | 사람과 이미 쓰는 coding agent가 채팅, 보드, Markdown 메모리 하나를 공유한다. |
| [OpenHands](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openhands-cell-80a8/reviews/openhands.md) | 셀프 호스트 개발 콘솔 | 대화, 터미널, 브라우저, 파일, 자동화를 그리고, 동작은 옆 샌드박스에서 실행된다. |

### 현장

공간으로 누가 바쁘고, 누가 기다리고, 누가 끝냈는지를 보여 준다.

| Cell | 성질 | 기여 |
| --- | --- | --- |
| [Agent Office](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/agent-office.md) | 3D 오피스 | 저장소마다 한 층을 주고, 걸어가 작업자의 터미널을 보고 함께 친다. |
| [Open Office](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openoffice-cell-c2d2/reviews/openoffice.md) | 2D 픽셀 팀 | 이름이 있는 구성원이 같은 바닥에서 계획하고, 코드를 쓰고, 검토하고, 미리 본다. |
| [Pixel Agents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/pixel-agents.md) | 2D 픽셀 오피스 | 실행 중인 agent가 바닥의 사람이 되고, 막히면 머리 위에 말풍선이 뜬다. |

### 워커

읽고, 쓰고, UI를 조작하고, 말하거나 답하는 쪽과, 그것들을 조립하는 런타임.

| Cell | 성질 | 기여 |
| --- | --- | --- |
| [Pi](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/pi.md) | coding agent 런타임 | 최소이고 끼워 넣을 수 있는 coding agent. CLI로도 돌고 다른 제품 안에도 들어간다. |
| [Octop](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-octop-cell-7aa3/reviews/octop.md) | 셀프 호스트 어시스턴트 플랫폼 | 여러 사용자가 각자 전문가를 키우고, 웹, 데스크톱, 여러 메신저에서 대화한다. |
| [OpenClaw](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openclaw-cell-7aa3/reviews/openclaw.md) | 어시스턴트 런타임 | 상주 Gateway. 이미 쓰는 채팅 앱에서 shell, 일정, 장치 동작을 한다. |
| [OpenCode](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-opencode-cell-9e0b/reviews/opencode.md) | coding agent | 프로젝트에서 읽고 쓰고 명령을 실행하고, 전문가를 더 부를 수 있다. |
| [Agent-S](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agent-s-cell-9381/reviews/agent-s.md) | 데스크톱 조작 agent | 화면을 보고, 마우스와 키보드로 일반 앱의 일을 끝낸다. |
| [Atomic Agents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-atomic-agents-cell-ca43/reviews/atomic-agents.md) | 부품 라이브러리 | 흐름을 스키마가 있는 부품으로 나누고, 입출력을 확인한 뒤에 잇는다. |
| [HelloAgents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-helloagents-cell-ca43/reviews/helloagents.md) | 컴포넌트 라이브러리 | 도구 등록표로 한 바퀴를 돌린다. 도구를 요청하고, 실행하고, 모델로 돌아간다. |
| [LangChain](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-langchain-cell-ca43/reviews/langchain.md) | agent 프레임워크 | 모델, 도구, 프롬프트로 스스로 도구를 호출하는 루프를 만든다. |
| [LiveKit Agents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-livekit-agents-cell-9381/reviews/livekit-agents.md) | 음성 런타임 | 프로그램 하나를 실시간 방에 넣어, 듣고 말하고 볼 수 있는 참가자로 만든다. |
| [Open-AutoGLM](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-open-autoglm-cell-ca43/reviews/open-autoglm.md) | 휴대폰 조작 agent | 한 문장으로 심부름을 보내고, 연결된 휴대폰의 앱에서 끝낸다. |
| [Qwen-Agent](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-qwen-agent-cell-9381/reviews/qwen-agent.md) | agent 프레임워크 | 모델, 도구, 문서를 답을 스트리밍하는 Assistant로 만든다. |
| [TEN Framework](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ten-framework-cell-9381/reviews/ten-framework.md) | 음성 런타임 | 바꿀 수 있는 extension 그래프로 실시간 음성 대화를 구성한다. |

### 메모리

앞선 맥락을 다음 차례와 다음 agent가 쓸 수 있게 남긴다.

| Cell | 성질 | 기여 |
| --- | --- | --- |
| [gbrain](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/gbrain.md) | 장기 메모리 | 결정, 관계, 한 일을 다음 차례가 찾을 수 있는 지식으로 남긴다. |
| [llmwiki](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-llm-wiki-compiler-cell-7aa3/reviews/llm-wiki-compiler.md) | 지식 컴파일러 | 문서와 세션을 출처가 있는 wiki로 엮고, 이후에는 사람과 agent가 그것을 찾는다. |
| [Hindsight](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-hindsight-cell-7aa3/reviews/hindsight.md) | 학습하는 메모리 | 새 정보를 사실, 경험, 마음 모델로 정리한 뒤 recall과 reflect로 꺼낸다. |
| [ai-memory](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ai-memory-cell-7aa3/reviews/ai-memory.md) | 하네스 간 메모리 | 여러 coding CLI의 궤적을 git으로 버전 관리되는 wiki 하나에 모은다. |
| [Octop Memory](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-octop-memory-cell-740f/reviews/octop-memory.md) | 옮길 수 있는 메모리 런타임 | 사실을 뽑고, 프롬프트에 들어가는 맥락을 불러오며, 그 메모리를 다른 숙주로 옮길 수 있다. |
| [LLM Wiki](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-llm-wiki-cell-17ae/reviews/llm-wiki.md) | 아이디어 명세 | 자기 agent에게 붙여, 한 주제의 지식 창고를 함께 키운다. |
| [MCP Memory Service](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-mcp-memory-service-cell-fb52/reviews/mcp-memory-service.md) | 셀프 호스트 메모리 서비스 | 결정, 관찰, 오류를 다음 세션과 다른 agent가 열 수 있는 서랍에 둔다. |
| [Memori](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memori-cell-fb52/reviews/memori.md) | SQL 메모리 계층 | 이번 차례가 누구이고 어떤 일이었는지 적고, 다음 맥락에 관련 사실을 넣는다. |
| [Memory OS](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memory-os-cell-fb52/reviews/memory-os.md) | Hermes 메모리 계층 | 파일, 대화, 사실, wiki를 Hermes에 붙이고, 모델을 부르기 전에 관련 과거를 넣는다. |
| [memsearch](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memsearch-cell-fb52/reviews/memsearch.md) | 프로젝트 메모리 | 차례가 끝나면 Markdown으로 적고, 옛 결정이 필요하면 짧은 구절만 찾는다. |
| [Cashew](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-cashew-cell-ca43/reviews/cashew.md) | 개인 사고 그래프 | 이미 돌고 있는 agent를 위해, 생각과 파생 관계를 SQLite 하나에 남긴다. |
| [Cognee](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-cognee-cell-fb52/reviews/cognee.md) | 지식 그래프 메모리 | 문서, 코드, 대화를 검색되는 그래프로 만들고, 질문으로 관련 구절을 꺼낸다. |
| [Daem0nMCP](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-daem0n-mcp-cell-ca43/reviews/daem0n-mcp.md) | 상주 메모리 데몬 | 세션을 넘어 지난 결정과 실패를 올리고, 무언가를 바꾸기 전에 한 번 멈춘다. |
| [Memlayer](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memlayer-cell-ca43/reviews/memlayer.md) | 메모리 라이브러리 | 모델과 저장소 사이에 서서, 이 말을 적을지 되짚어 찾을지 정한다. |
| [Memora](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memora-cell-fb52/reviews/memora.md) | MCP 메모리 저장소 | 사실, 할 일, 질문, 문서를 한 저장소에 넣고, 일을 시작할 때 주제별로 아직 유효한 것을 꺼낸다. |
| [MemPalace Evolve](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-mempalace-evolve-cell-ca43/reviews/mempalace-evolve.md) | 로컬 장기 메모리 | 사실을 한 디렉터리에 넣고, 다음 대화에서 다시 찾는다. |
| [PocketFlow Codebase Knowledge](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-pocketflow-codebase-tutorial-cell-ca43/reviews/pocketflow-tutorial-codebase-knowledge.md) | 튜토리얼 생성 흐름 | 코드베이스를 다시 읽을 수 있는 Markdown 튜토리얼로 엮는다. |
| [Youtube Made Simple](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-pocketflow-youtube-tutorial-cell-ca43/reviews/pocketflow-tutorial-youtube-made-simple.md) | 튜토리얼 생성 흐름 | 긴 영상을 쉬운 한 페이지로 줄인다. |

### 방법

이 일을 어떻게 할지 말한다. 스킬, 절차, 그리고 끝났을 때의 모습.

| Cell | 성질 | 기여 |
| --- | --- | --- |
| [gstack](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/gstack.md) | 스킬 팩 | 제품, 엔지니어링, 디자인, QA, 릴리스 역할로 문제를 보는 법과 넘기는 법을 정한다. |
| [mattpocock skills](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/mattpocock-skills.md) | 엔지니어링 스킬 | coding agent를 요구에 맞추고, 테스트와 리뷰로 피드백을 만든다. |
| [ECC](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ecc-cell-90bd/reviews/ecc.md) | 엔지니어링 절차 | plan, test, implement, review, verify를 이미 쓰는 하네스 안에 남긴다. |
| [LifeOS](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-lifeos-cell-90bd/reviews/lifeos.md) | 개인 하네스 | 당신이 누구이고, 무엇을 중히 여기며, 끝난 모습이 어떤지 기억한다. |
| [Hello-Agents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-hello-agents-cell-ca43/reviews/hello-agents.md) | 튜토리얼 | 책과 장별 코드로, 원리부터 멀티 에이전트 응용까지 agent를 만드는 법을 설명한다. |

### 실행 경계

어디서 실행되는지, 어떤 파일, 네트워크, 터미널, 브라우저를 만져도 되는지를 정한다.

| Cell | 성질 | 기여 |
| --- | --- | --- |
| [OpenShell](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openshell-cell-7aa3/reviews/openshell.md) | 정책 샌드박스 | 정책으로 agent가 만질 수 있는 파일, 프로세스, 네트워크, 자격 증명을 제한한다. |
| [Herdr](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-herdr-cell-7aa3/reviews/herdr.md) | 터미널 런타임 | 기존 agent의 PTY와 배치를 살려, 위 계층이 상태를 읽고 언제든 다시 붙게 한다. |
| [Octop Browser](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-octop-browser-cell-6a61/reviews/octop-browser.md) | 브라우저 런타임 | agent에게 진짜 Chromium을 주고, 짧은 부호로 페이지를 다루며, 로그인은 기기에 남긴다. |
| [Cloudflare OS](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/cloudflare-os.md) | 권한 워크벤치 | 워크스페이스는 처음부터 외부 계정에 닿지 못하고, 사람이 자원을 소개한 뒤에야 된다. |

### 검증

점수, 트레이스, 비교할 수 있는 시도를 남겨, 이번 실행이 어땠는지 판단할 수 있게 한다.

| Cell | 성질 | 기여 |
| --- | --- | --- |
| [AxisAgentic](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-axisagentic-cell-ca43/reviews/axisagentic.md) | 장기 실행 런타임 | 도구를 쓰는 긴 작업을 실행하고, 매번 재생할 수 있는 궤적으로 적는다. |
| [Harbor](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-harbor-cell-ad04/reviews/harbor.md) | 평가 하네스 | 각 답의 점수와 궤적을 남겨, 비교하고 다시 채점하고 다시 최적화할 수 있게 한다. |

### 산출 표면

팀이 함께 고치는 산출의 층. 화면, 문서, 표, 슬라이드.

| Cell | 성질 | 기여 |
| --- | --- | --- |
| [Onlook](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-onlook-cell-90bd/reviews/onlook.md) | 인터페이스 캔버스 | 실행 중인 화면의 React 인터페이스를 고치고, 변경을 코드로 다시 쓴다. |
| [Univer](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-univer-cell-80a8/reviews/univer.md) | Office 런타임 | 사람과 agent가 같은 스프레드시트, 문서, 슬라이드 모델을 다루게 한다. |
| [Univer Workspace](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-univer-workspace-cell-80a8/reviews/univer-workspace.md) | 문서 워크스페이스 | 사람과 agent가 표와 문서를 함께 고치고, 모두가 보고 있는 판으로 되돌릴지는 사람이 정한다. |

직접 써 봤는지는 두 값입니다.

- `untried`: 목록에는 있으나, 아직 실제로 쓰지 않았다.
- `tried`: 누군가 썼다.

<h2 id="support">이 목록을 지원하기</h2>

이 목록은 MIT 라이선스로 공개되어 있습니다. 이어 가는 방법은 두 가지입니다.

- 아직 없는 제품을 더하거나, 있는 Cell을 고칩니다. [CONTRIBUTING.md](CONTRIBUTING.md)를 보세요.
- [GitHub Sponsors](https://github.com/sponsors/Giorno-Giovanna-Dio)로 유지를 지원합니다. 저장소 페이지의 Sponsor 버튼은 [`.github/FUNDING.yml`](.github/FUNDING.yml)을 읽습니다.

<p align="center">
  <a href="https://github.com/sponsors/Giorno-Giovanna-Dio"><img src="https://img.shields.io/badge/Sponsor-%E2%9D%A4-ea4aaa?style=for-the-badge&logo=githubsponsors&logoColor=white" alt="Sponsor on GitHub"></a>
</p>
