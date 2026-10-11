<p align="center">
  <img src="assets/banner.jpg" alt="AI Agent Products Reviewed. A public catalog of AI agent products." width="100%">
</p>

<p align="center">
  <a href="README.md">English</a>
  &nbsp;·&nbsp;
  <a href="README.zh-TW.md">繁體中文</a>
  &nbsp;·&nbsp;
  <strong>日本語</strong>
  &nbsp;·&nbsp;
  <a href="README.ko.md">한국어</a>
  &nbsp;·&nbsp;
  <a href="README.es.md">Español</a>
</p>

<p align="center">
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-3d3a36" alt="MIT License"></a>
  <a href="CONTRIBUTING.md"><img src="https://img.shields.io/badge/PRs-welcome-3d3a36" alt="PRs welcome"></a>
  <a href="https://github.com/sponsors/Giorno-Giovanna-Dio"><img src="https://img.shields.io/badge/sponsor-GitHub%20Sponsors-ea4aaa?logo=githubsponsors&logoColor=white" alt="GitHub Sponsors"></a>
</p>

<p align="center">
  <a href="#contents">目次</a>
  &nbsp;·&nbsp;
  <a href="#contributing">貢献</a>
  &nbsp;·&nbsp;
  <a href="CODE_OF_CONDUCT.md">行動規範</a>
  &nbsp;·&nbsp;
  <a href="#support">支援</a>
</p>

# AI Agent Products Reviewed

これは AI agent 製品の公開ハブです。製品、フレームワーク、ワークベンチ、
記憶層、ランタイムは、リポジトリと公式サイトに散らばっています。この目録は
それらを集めます。各製品は一つの Cell です。それが何であるか、そして
AI agent のオーケストレーションチームのどこを埋めるかが分かります。

目録は比較のためのもので、順位を付けるためのものではありません。各製品を
同じ問いで読みます。agent、タスク、文脈、実行環境、人の監督がどう組まれ、
それらが 2D または 3D の AI agent ワークスペースの中で何になるか。

ライセンスは [MIT](LICENSE) です。一緒に働くやり方は
[行動規範](CODE_OF_CONDUCT.md) にあります。洞察ノート、行動規範、
貢献ガイドの本文は繁体字中国語です。

<h2 id="contributing">貢献を歓迎します</h2>

**貢献を歓迎します。** この目録は、まだ散らばっている AI agent 製品を
足してくれる人に支えられています。

- まだ載っていない製品を提案する。
- 洞察ノートを書き、オーケストレーションチームの一つの席に置く。
- すでに載っている Cell を直す。古い事実、切れたリンク、間違った席。

[CONTRIBUTING.md](CONTRIBUTING.md) から始めてください。新しい製品は
pull request につき一つです。使ったことがなくてもノートを書けます。
status は `untried` のままにします。ここに一行足すときは、同じ Cell を
同じ席、同じリンクで [README.md](README.md)、[README.zh-TW.md](README.zh-TW.md)、
[README.ko.md](README.ko.md)、[README.es.md](README.es.md) にも足します。

<h2 id="contents">目次</h2>

- [貢献を歓迎します](#contributing)
- [研究で知りたいこと](#research-goals)
- [リポジトリ構成](#repository-layout)
- [Cell モデル](#cell-model)
- [レビューの手順](#review-process)
- [オーケストレーションチームでの位置](#orchestration-team)
- [この目録を支える](#support)

自分で動かしたいプロジェクトは、このリポジトリの外に clone します。
このリポジトリが残すのは、出典のリンク、製品と機能の分析、実際に試したこと、
そして 2D または 3D ワークスペースへの示唆です。公開リポジトリのない製品は、
公式ページと取れるバージョン情報から記録します。

<h2 id="research-goals">研究で知りたいこと</h2>

各 Cell のレビューは、次に答えるためのものです。

- それはどの新しい agent ワークスペース、または関わり方を表しているか。
- agent、タスク、ブランチ、サンドボックス、成果物、進捗をどう見せるか。
- 人はどう委譲し、比べ、介入し、確かめ、制御を取り戻すか。
- どの能力がモデルから来て、どの能力が agent ランタイムやオーケストレーションから来るか。
- 2D ワークスペース（平面、またはピクセルの職場）では何になるか。
- 3D ワークスペース（歩き回れるオフィス。3D の物体ではない）では何になるか。
- どの型を採り、作り直し、または意図して避ける価値があるか。

主な研究の角度：

1. ワークスペースと空間の整理
2. マルチエージェントのオーケストレーション
3. 文脈、記憶、引き継ぎ
4. ランタイム、サンドボックス、権限
5. 状態、進捗、観察
6. 人がループに入る制御
7. 成果物、来歴、レビュー
8. 共同作業と拡張

<h2 id="repository-layout">リポジトリ構成</h2>

- [`README.md`](README.md)：英語の目録。リポジトリのホームページです。
- [`README.zh-TW.md`](README.zh-TW.md)：繁体字中国語の目録。
- [`README.ja.md`](README.ja.md)：この日本語の目録。
- [`README.ko.md`](README.ko.md)：韓国語の目録。
- [`README.es.md`](README.es.md)：スペイン語の目録。
- [`CONTRIBUTING.md`](CONTRIBUTING.md)：Cell の提案、追加、修正のやり方。本文は繁体字中国語です。
- [`cells.yaml`](cells.yaml)：すべての Cell の構造化された metadata。
- [`reviews/README.md`](reviews/README.md)：共通のレビュー規則と証拠の基準。
- [`reviews/`](reviews/)：Cell ごとに一つの洞察ノート。ブラウザでは GitHub の描画リンクで読んでください。[`reviews/README.md`](reviews/README.md) を参照。
- `/workspace-labs/<cell-name>`：手元で試すときの置き場の提案。このリポジトリの外で、Git は追いません。

<h2 id="cell-model">Cell モデル</h2>

**Cell** は表の中の一つの研究単位です。Cell は upstream のソースツリーを置きません。
出典へリンクし、製品から学んだことを残します。

- リポジトリがある Cell の ID は、canonical な GitHub リポジトリ URL です。例：`https://github.com/mattpocock/sandcastle`。
- 公開リポジトリのない製品は、公式の canonical URL を使います。例：`https://www.conductor.build/`。
- GitHub URL からは `.git`、query、fragment、末尾の `/` を除きます。
- リポジトリの改名や移管では、新しい canonical URL が ID になり、古い URL は `aliases` に入ります。

<h2 id="review-process">レビューの手順</h2>

1. 製品を `cells.yaml` に Cell として足し、status は `untried` にします。[`CONTRIBUTING.md`](CONTRIBUTING.md) に従ってください。出典が GitHub リポジトリか公式 URL なら、[`/create-cell-pr`](.cursor/skills/create-cell-pr/SKILL.md) を呼び、サブエージェントにノートと専用 PR を書かせることもできます。
2. 出典が公開されていれば、別の実験場所に clone し、実際に試した完全な commit SHA を記録します。そうでなければ製品のバージョンを記録します。
3. [`reviews/README.md`](reviews/README.md) に従い、[`reviews/_template.md`](reviews/_template.md) から始めます。
4. 公式ドキュメント、ソース、デモでは重要な問いに答えられないときだけ、upstream が勧めるやり方で手元の確認をします。Docker は必須ではありません。
5. tried / untried の状態と初期の読みを更新し、下の一つの席に Cell を置きます。一つの明確な変更を、一つの atomic commit にします。

候補プロジェクトをこのリポジトリの中に置かないでください。実験にコード変更が要るときは、その製品を fork します。コードの変更は fork が持ち、レビューはここに残します。

<h2 id="orchestration-team">オーケストレーションチームでの位置</h2>

これらの表は、製品が何であるか、そして AI agent のオーケストレーションチームの
どの部分を埋めるかを分類します。各 Cell の主な席は一つです。隣の席にも触れる
能力は「貢献」に書きます。実際に使ったかどうかは、ノートと
[`cells.yaml`](cells.yaml) の `evaluation.status` に残します。

Cell 名は GitHub で描画された洞察ノートへのリンクです。Cell ID はノートの先頭と
[`cells.yaml`](cells.yaml) にあります。

| 席 | チームでの役割 |
| --- | --- |
| オーケストレーション | 仕事を分け、適切な agent に渡し、結果を戻す。 |
| ガバナンス | 目標、人数、予算、権限、そして誰も見ていないときに仕事を始めてよいかを扱う。 |
| ワークベンチ | 人が複数の agent を同時に見て、比べ、その中に入れる。 |
| 在席 | 空間で、誰が忙しそうで、誰が待っていて、誰が終わったかを見せる。 |
| ワーカー | 読み、書き、UI を操作し、話し、返事する側と、それらを組み立てるランタイム。 |
| メモリ | 前の文脈を、次のターンと次の agent が使えるように残す。 |
| 進め方 | この仕事をどう進めるかを示す。スキル、手順、完了の姿。 |
| 実行境界 | どこで動くか、どのファイル、ネットワーク、ターミナル、ブラウザに触れてよいかを決める。 |
| 検証 | 点数、トレース、比べられる試行を残し、この実行がどうだったかを判断できるようにする。 |
| 成果の画面 | チームが一緒に直す成果の層。画面、文書、表、スライド。 |

### オーケストレーション

仕事を分け、適切な agent に渡し、結果を戻す。

| Cell | 種類 | 貢献 |
| --- | --- | --- |
| [Sandcastle](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/sandcastle.md) | オーケストレーションライブラリ | コードから coding agent を隔離環境で動かし、終わったらブランチ方針でマージする。 |
| [Octop Harness](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-octop-harness-cell-7618/reviews/octop-harness.md) | デプロイ用ランタイム | 一つのプロセスに、互いに隔離された agent を複数登録する。 |
| [OpenRig](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openrig-cell-7aa3/reviews/openrig.md) | チームハーネス | YAML で席とメンバーを書き、一度に起動して仕事を振る。 |
| [Deep Agents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-deepagents-cell-9e0b/reviews/deepagents.md) | 長時間タスクのハーネス | サブエージェント、仮想ファイル、記憶、人の承認を、長時間タスクのランタイムにまとめる。 |
| [CrewAI](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-crewai-cell-90bd/reviews/crewai.md) | オーケストレーションフレームワーク | 役割とタスクで小隊を組み、外側はイベントフローで分岐を制御する。 |
| [OmO](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-oh-my-openagent-cell-90bd/reviews/oh-my-openagent.md) | オーケストレーション層 | メインセッションが仕事を分けて振り、一時的な作業者がファイルを直し証拠を返す。 |
| [Oh My OpenCode](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-oh-my-opencode-cell-103e/reviews/oh-my-opencode.md) | 複数ロールのプラグイン | 一度の開発を、インタビュー、計画、割り当て、実装、調査に編成する。 |
| [AgentScope](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agentscope-cell-fb52/reviews/agentscope.md) | オーケストレーションフレームワーク | コードで agent を組み、複数の agent が仕事を渡し合う。 |
| [Gas Town](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/gastown.md) | CLI オーケストレーション | 複数の coding agent を同時にスケジュールし、仕事の状態は復旧できる台帳に書く。 |
| [Routa](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/routa.md) | デリバリー調整台 | 長いチャットを、タスク、ボード、ノート、担当者の契約に分ける。 |
| [Agency Swarm](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agency-swarm-cell-ad04/reviews/agency-swarm.md) | オーケストレーションフレームワーク | 役職と一方向のコミュニケーション図で、誰が仕事を振れて、誰が会話を引き継ぐかを決める。 |
| [Agent Squad](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agent-squad-cell-ad04/reviews/agent-squad.md) | 会話ルーター | 一言ごとに最適な専門 agent へ渡し、そのチャットを覚えておく。 |
| [Amux](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-amux-cell-80a8/reviews/amux.md) | セルフホストのコントロールプレーン | すでにある coding agent に、共有ボード、連絡路、スケジュールを渡す。 |
| [AutoAgent](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-autoagent-cell-ca43/reviews/autoagent.md) | オーケストレーションフレームワーク | 自然言語で担当者とワークフローを作り、トリアージ役が仕事を振る。 |
| [AutoGen](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-autogen-cell-9381/reviews/autogen.md) | オーケストレーションフレームワーク | コードで、自分で動き、人とも一緒に働ける agent の集団を組む。 |
| [BeeAI Framework](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-beeai-framework-cell-ad04/reviews/beeai-framework.md) | オーケストレーションフレームワーク | Python か TypeScript で、仕事を引き継ぐ agent とフローを書く。 |
| [Bernstein](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-bernstein-cell-80a8/reviews/bernstein.md) | スケジュールオーケストレーション | 一つの目標を複数の CLI agent に分け、スケジューラが受け取り、再試行、マージを決める。 |
| [LangGraph](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-langgraph-cell-9381/reviews/langgraph.md) | グラフランタイム | 共有状態、ノード、辺で、長く動き中断から再開できるフローを編む。 |
| [MetaGPT](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-metagpt-cell-9381/reviews/metagpt.md) | オーケストレーションフレームワーク | 複数の役割をソフトウェア会社に編成し、SOP に沿って設計とコードを出す。 |
| [Microsoft Agent Framework](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-microsoft-agent-framework-cell-9381/reviews/microsoft-agent-framework.md) | オーケストレーションフレームワーク | ツールを呼ぶ agent を書くか、複数の agent をワークフローにつなぐ。 |
| [MS-Agent](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ms-agent-cell-ad04/reviews/ms-agent.md) | 長時間タスクのハーネス | 計画、権限、サブエージェントと、翌日も続きからできるプロジェクト記憶を担う。 |
| [OpenAI Agents SDK](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openai-agents-python-cell-9381/reviews/openai-agents-python.md) | agent SDK | agent、ハンドオフ、ガードレールでマルチエージェントのフローを組む。 |
| [PocketFlow](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-pocketflow-cell-ca43/reviews/pocketflow.md) | グラフフレームワーク | 一つのアプリケーションを、ノード、アクション、共有データとして書く。 |
| [Pragma](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-pragma-cell-80a8/reviews/pragma.md) | Agent Team プラットフォーム | 専門家、フロー、ツール、記憶、人がうなずく関門を、持ち運べるチームにまとめる。 |
| [Youtu-Agent](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-youtu-agent-cell-ad04/reviews/youtu-agent.md) | オーケストレーションフレームワーク | YAML で agent を組んで実行し、同じ設定を評価して改善できる。 |

### ガバナンス

目標、人数、予算、権限、そして誰も見ていないときに仕事を始めてよいかを扱う。

| Cell | 種類 | 貢献 |
| --- | --- | --- |
| [Paperclip](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-paperclip-cell-7aa3/reviews/paperclip.md) | 組織コントロールプレーン | 目標、編成、予算、heartbeat で、外部 agent を社員として起こす。 |
| [Agenta](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agenta-cell-ad04/reviews/agenta.md) | チームワークスペース | チームが自分で仕事を始める同僚を組み、指示、スキル、権限を調整できる。 |

### ワークベンチ

人が複数の agent を同時に見て、比べ、その中に入れる。

| Cell | 種類 | 貢献 |
| --- | --- | --- |
| [Conductor](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/conductor.md) | デスクトップ ADE | 複数の coding agent の worktree、プレビュー、マージを一つのコンソールに置く。 |
| [Maestro](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/maestro.md) | デスクトップ ADE | キーボード優先のコンソールで、複数のプロジェクトとタスクキューを同時に進める。 |
| [Orca](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/orca.md) | デスクトップ ADE | CLI agent ごとに worktree を持たせ、同じアプリで会話、ターミナル、diff を見る。 |
| [cmux](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/cmux.md) | ターミナルワークスペース | タブ、分割、「あなたが必要」の通知で、多くの CLI セッションを整理する。 |
| [Emdash](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/emdash.md) | デスクトップ ADE | タスク単位で既存の agent を動かし、同じアプリで diff、CI、PR を見る。 |
| [Paseo](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/paseo.md) | セルフホストのコントロールプレーン | デーモンが既存 CLI をローカルで動かし、デスクトップ、スマホ、ウェブが同じマシンに戻る。 |
| [Nimbalyst](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/nimbalyst.md) | ビジュアル作業台 | 人と agent が同じファイルを直し、並行セッションは worktree で隔てる。 |
| [Odysseus](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-odysseus-cell-7aa3/reviews/odysseus.md) | セルフホストの個人ワークスペース | チャット、調査、文書、メール、やることリストを一つの画面に集める。 |
| [T3 Code](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-t3code-cell-7aa3/reviews/t3code.md) | ハーネス制御面 | ローカルでログイン済みの CLI に繋ぎ、同じ UI でスレッドを開き、diff を見て、権限を承認する。 |
| [OpenChamber](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openchamber-cell-0717/reviews/openchamber.md) | OpenCode ワークベンチ | デスクトップ、ブラウザ、VS Code、スマホから、同じ OpenCode セッションを監督する。 |
| [Ekko Studio](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ekko-studio-cell-fb52/reviews/ekko-studio.md) | 作業台とノードフロー | 一人チャット、グループルーム、実行できるノード図のあいだを切り替える。 |
| [Codeg](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-codeg-cell-fb52/reviews/codeg.md) | マルチエージェント ADE | ACP で複数の CLI を、同じ会話、diff、権限の確認に集める。 |
| [Agentrove](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agentrove-cell-c2d2/reviews/agentrove.md) | セルフホストのコード作業空間 | 一つのワークスペースに一つのサンドボックスを結び、ACP でインストール済み agent を起動する。 |
| [cc-haha](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-cc-haha-cell-c2d2/reviews/cc-haha.md) | ローカルのデスクトップ作業台 | ふつうの言葉でプロジェクトを直し diff を見る。スマホとチャットアプリはこのコンピュータに戻る。 |
| [iPolloWork](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/ipollowork.md) | マルチエンジン作業台 | OpenCode や Codex などのエンジンを、タスク、進捗、ファイルの一つの流れにまとめる。 |
| [Golutra](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/golutra.md) | ターミナルチャットルーム | ローカル CLI をチャンネルのメンバーにし、出力を一つの会話に戻す。 |
| [Claude Code Bridge](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/claude-codex-bridge.md) | CLI ワークベンチ | 複数の CLI を同時に見て、メッセージで互いへ仕事を渡せる。 |
| [AgentSpace](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agentspace-cell-80a8/reviews/agentspace.md) | 共同ウェブワークスペース | 人と、役割の決まったデジタル社員に、メッセージ、文書、承認の共通の居場所を渡す。 |
| [Buzz](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-buzz-cell-80a8/reviews/buzz.md) | 共同ワークスペース | 人と agent を、同じチャンネル、スレッド、キャンバス、ワークフローに入れる。 |
| [Claude Squad](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-claude-squad-cell-80a8/reviews/claude-squad.md) | ターミナル監督 | セッションごとに worktree と tmux を持たせ、同じディレクトリを奪い合わない。 |
| [Free4chat](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-free4chat-cell-fb52/reviews/free4chat.md) | 一時的な共同ルーム | 一つのリンクで、ブラウザの人とローカル agent を短い共同作業に呼ぶ。 |
| [Hermes Workspace](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-hermes-workspace-cell-ad04/reviews/hermes-workspace.md) | Web 司令デッキ | ブラウザで Hermes の会話、ターミナル、記憶、スキル、複数の作業者を見る。 |
| [Kun](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-kun-cell-ad04/reviews/kun.md) | ローカル作業台 | Code、Design、Work、Rooms で、目標を確認できる成果にする。 |
| [Meldwork](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-meldwork-cell-80a8/reviews/meldwork.md) | デスクトップ ADE | インストール済み CLI を一つの案件に置く。一人でやる、複数がそれぞれ答える、議論してから採用する。 |
| [Mycelium](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-mycelium-cell-ca43/reviews/mycelium.md) | 共有ルーム | 人と、すでに使っている coding agent が、チャット、ボード、一つの Markdown 記憶を共有する。 |
| [OpenHands](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openhands-cell-80a8/reviews/openhands.md) | セルフホストの開発コンソール | 会話、ターミナル、ブラウザ、ファイル、自動化を描き、動作は隣のサンドボックスで実行する。 |

### 在席

空間で、誰が忙しそうで、誰が待っていて、誰が終わったかを見せる。

| Cell | 種類 | 貢献 |
| --- | --- | --- |
| [Agent Office](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/agent-office.md) | 3D オフィス | リポジトリごとに階を用意し、歩み寄って作業者のターミナルを見て一緒に打つ。 |
| [Open Office](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openoffice-cell-c2d2/reviews/openoffice.md) | 2D ピクセルチーム | 名前のあるメンバーが同じ床で計画し、コードを書き、レビューし、プレビューする。 |
| [Pixel Agents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/pixel-agents.md) | 2D ピクセルオフィス | 動いている agent が床の人型になり、詰まったときは頭の上に吹き出しが出る。 |

### ワーカー

読み、書き、UI を操作し、話し、返事する側と、それらを組み立てるランタイム。

| Cell | 種類 | 貢献 |
| --- | --- | --- |
| [Pi](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/pi.md) | coding agent ランタイム | 最小で埋め込める coding agent。CLI でも動き、他の製品にも入る。 |
| [Octop](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-octop-cell-7aa3/reviews/octop.md) | セルフホストのアシスタント基盤 | 複数の利用者がそれぞれ専門家を育て、ウェブ、デスクトップ、複数のチャットで話す。 |
| [OpenClaw](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openclaw-cell-7aa3/reviews/openclaw.md) | アシスタントランタイム | 常駐 Gateway。いつものチャットアプリから shell、スケジュール、デバイス操作をする。 |
| [OpenCode](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-opencode-cell-9e0b/reviews/opencode.md) | coding agent | プロジェクトで読み書きとコマンド実行をし、専門家をさらに呼べる。 |
| [Agent-S](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agent-s-cell-9381/reviews/agent-s.md) | デスクトップ操作 agent | 画面を見て、マウスとキーボードでふつうのアプリの仕事を終える。 |
| [Atomic Agents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-atomic-agents-cell-ca43/reviews/atomic-agents.md) | 部品ライブラリ | フローをスキーマ付きの部品に分け、入出力を確認してからつなぐ。 |
| [HelloAgents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-helloagents-cell-ca43/reviews/helloagents.md) | コンポーネントライブラリ | ツール登録表で一周させる。ツールを要求し、実行し、モデルに戻る。 |
| [LangChain](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-langchain-cell-ca43/reviews/langchain.md) | agent フレームワーク | モデル、ツール、プロンプトで、自分でツールを呼ぶループを組む。 |
| [LiveKit Agents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-livekit-agents-cell-9381/reviews/livekit-agents.md) | 音声ランタイム | プログラムをリアルタイムの部屋に入れ、聞いて話して見られる参加者にする。 |
| [Open-AutoGLM](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-open-autoglm-cell-ca43/reviews/open-autoglm.md) | スマホ操作 agent | 一言で用事を出し、つながったスマホのアプリでやり終える。 |
| [Qwen-Agent](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-qwen-agent-cell-9381/reviews/qwen-agent.md) | agent フレームワーク | モデル、ツール、文書を、返事をストリームする Assistant にする。 |
| [TEN Framework](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ten-framework-cell-9381/reviews/ten-framework.md) | 音声ランタイム | 取り替えられる extension グラフで、リアルタイムの音声会話を組む。 |

### メモリ

前の文脈を、次のターンと次の agent が使えるように残す。

| Cell | 種類 | 貢献 |
| --- | --- | --- |
| [gbrain](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/gbrain.md) | 長期記憶 | 決定、関係、したことを、次のターンが引ける知識として残す。 |
| [llmwiki](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-llm-wiki-compiler-cell-7aa3/reviews/llm-wiki-compiler.md) | 知識コンパイラ | 文書とセッションを出典のある wiki に編成し、その後は人と agent がそこを引く。 |
| [Hindsight](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-hindsight-cell-7aa3/reviews/hindsight.md) | 学習する記憶 | 新しい情報を事実、経験、心のモデルにし、recall と reflect で取り出す。 |
| [ai-memory](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ai-memory-cell-7aa3/reviews/ai-memory.md) | ハーネス横断の記憶 | 複数の coding CLI の軌跡を、git で版管理された一つの wiki に集める。 |
| [Octop Memory](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-octop-memory-cell-740f/reviews/octop-memory.md) | 持ち運べる記憶ランタイム | 事実を抜き、プロンプトに入る文脈を呼び戻し、その記憶を別の宿主へ移せる。 |
| [LLM Wiki](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-llm-wiki-cell-17ae/reviews/llm-wiki.md) | アイデア仕様 | 自分の agent に渡し、ある主題の知識庫を一緒に育てる。 |
| [MCP Memory Service](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-mcp-memory-service-cell-fb52/reviews/mcp-memory-service.md) | セルフホストの記憶サービス | 決定、観察、誤りを、次のセッションや他の agent が開ける棚に置く。 |
| [Memori](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memori-cell-fb52/reviews/memori.md) | SQL 記憶層 | このターンが誰で、どの仕事だったかを記録し、次の文脈に関連する事実を入れる。 |
| [Memory OS](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memory-os-cell-fb52/reviews/memory-os.md) | Hermes の記憶層 | ファイル、会話、事実、wiki を Hermes に繋ぎ、モデルを呼ぶ前に関連する過去を差し込む。 |
| [memsearch](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memsearch-cell-fb52/reviews/memsearch.md) | プロジェクト記憶 | ターンの終わりに Markdown へ書き、古い決定が要るときは短い箇所だけ引く。 |
| [Cashew](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-cashew-cell-ca43/reviews/cashew.md) | 個人の思考グラフ | すでに動いている agent のために、考えと派生関係を一つの SQLite に残す。 |
| [Cognee](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-cognee-cell-fb52/reviews/cognee.md) | 知識グラフ記憶 | 文書、コード、会話を検索できるグラフにし、質問で関連する一節を出す。 |
| [Daem0nMCP](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-daem0n-mcp-cell-ca43/reviews/daem0n-mcp.md) | 常駐記憶デーモン | セッションをまたいで過去の決定と失敗を出し、何かを変える前に一度止める。 |
| [Memlayer](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memlayer-cell-ca43/reviews/memlayer.md) | 記憶ライブラリ | モデルと保存のあいだに入り、この発話を書くか、振り返って探すかを決める。 |
| [Memora](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memora-cell-fb52/reviews/memora.md) | MCP 記憶ストア | 事実、やること、質問、文書を一つの庫に入れ、仕事の開始時にテーマでまだ有効なものを出す。 |
| [MemPalace Evolve](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-mempalace-evolve-cell-ca43/reviews/mempalace-evolve.md) | ローカル長期記憶 | 事実を一つのディレクトリに入れ、次の会話でまた見つける。 |
| [PocketFlow Codebase Knowledge](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-pocketflow-codebase-tutorial-cell-ca43/reviews/pocketflow-tutorial-codebase-knowledge.md) | チュートリアル生成フロー | コードベースを、読み返せる Markdown のチュートリアルに編む。 |
| [Youtube Made Simple](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-pocketflow-youtube-tutorial-cell-ca43/reviews/pocketflow-tutorial-youtube-made-simple.md) | チュートリアル生成フロー | 長い動画を、平易な一ページにまとめる。 |

### 進め方

この仕事をどう進めるかを示す。スキル、手順、完了の姿。

| Cell | 種類 | 貢献 |
| --- | --- | --- |
| [gstack](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/gstack.md) | スキルパック | プロダクト、エンジニアリング、デザイン、QA、リリースの役割で、問題の見方と引き継ぎ方を決める。 |
| [mattpocock skills](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/mattpocock-skills.md) | エンジニアリングスキル | coding agent を要件に合わせ、テストとレビューでフィードバックを作る。 |
| [ECC](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ecc-cell-90bd/reviews/ecc.md) | エンジニアリング手順 | plan、test、implement、review、verify を、すでに使っているハーネスの中に残す。 |
| [LifeOS](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-lifeos-cell-90bd/reviews/lifeos.md) | 個人ハーネス | あなたが誰で、何を大切にし、完了がどんな形かを覚えておく。 |
| [Hello-Agents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-hello-agents-cell-ca43/reviews/hello-agents.md) | チュートリアル | 本と章のコードで、原理からマルチエージェント応用まで agent の組み方を説明する。 |

### 実行境界

どこで動くか、どのファイル、ネットワーク、ターミナル、ブラウザに触れてよいかを決める。

| Cell | 種類 | 貢献 |
| --- | --- | --- |
| [OpenShell](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openshell-cell-7aa3/reviews/openshell.md) | ポリシーサンドボックス | ポリシーで、agent が触れるファイル、プロセス、ネットワーク、資格情報を限る。 |
| [Herdr](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-herdr-cell-7aa3/reviews/herdr.md) | ターミナルランタイム | 既存 agent の PTY とレイアウトを生かし、上の層が状態を読んでいつでも戻れるようにする。 |
| [Octop Browser](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-octop-browser-cell-6a61/reviews/octop-browser.md) | ブラウザランタイム | agent に本物の Chromium を渡し、短い符号でページを操作し、ログインはマシンに残す。 |
| [Cloudflare OS](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/cloudflare-os.md) | 権限ワークベンチ | ワークスペースは初期状態で外部アカウントに触れず、人がリソースを紹介してからになる。 |

### 検証

点数、トレース、比べられる試行を残し、この実行がどうだったかを判断できるようにする。

| Cell | 種類 | 貢献 |
| --- | --- | --- |
| [AxisAgentic](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-axisagentic-cell-ca43/reviews/axisagentic.md) | 長時間ランタイム | ツールを使う長いタスクを走らせ、実行のたびに再生できる軌跡として書く。 |
| [Harbor](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-harbor-cell-ad04/reviews/harbor.md) | 評価ハーネス | 各回答の点数と軌跡を残し、比較、再採点、再最適化ができるようにする。 |

### 成果の画面

チームが一緒に直す成果の層。画面、文書、表、スライド。

| Cell | 種類 | 貢献 |
| --- | --- | --- |
| [Onlook](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-onlook-cell-90bd/reviews/onlook.md) | インターフェースキャンバス | 動いている画面の React インターフェースを直し、変更をコードへ書き戻す。 |
| [Univer](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-univer-cell-80a8/reviews/univer.md) | Office ランタイム | 人と agent が同じ表計算、文書、スライドのモデルを操作できるようにする。 |
| [Univer Workspace](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-univer-workspace-cell-80a8/reviews/univer-workspace.md) | 文書ワークスペース | 人と agent が表と文書を一緒に直し、今みんなが見ている版へ戻すかは人が決める。 |

手元で試したかどうかは、二つの値です。

- `untried`：目録には入っているが、まだ実際には使っていない。
- `tried`：誰かが使った。

<h2 id="support">この目録を支える</h2>

この目録は MIT ライセンスで公開しています。続ける方法は二つです。

- まだ載っていない製品を足すか、載っている Cell を直す。[CONTRIBUTING.md](CONTRIBUTING.md) を見てください。
- [GitHub Sponsors](https://github.com/sponsors/Giorno-Giovanna-Dio) で保守を支える。リポジトリページの Sponsor ボタンは [`.github/FUNDING.yml`](.github/FUNDING.yml) を読みます。

<p align="center">
  <a href="https://github.com/sponsors/Giorno-Giovanna-Dio"><img src="https://img.shields.io/badge/Sponsor-%E2%9D%A4-ea4aaa?style=for-the-badge&logo=githubsponsors&logoColor=white" alt="Sponsor on GitHub"></a>
</p>
