# 開発TODO

## 方針
- `doc/requirement.md`を正とし、要件とのトレーサビリティを維持して段階実装する。
- 実装順は「契約固定 → Workers認証疎通 → フロント（ローカル下書き中心） → 連携E2E → 機能拡張」。
- 要件IDは本書で`REQ-001`形式の仮採番を行い、後続で`doc/requirement.md`へ反映する。

## 共通定義

### Definition of Ready (DoR)
- 目的/受け入れ条件/変更対象/検証手順/リスク/承認要否が埋まっている。
- 依存や前提が明記されている（あれば）。
- 関連要件IDが1つ以上紐づいている。

### Definition of Done (DoD) ※ `種別: app` のみ必須
- `tdd-twada`に従い、Red → Green → Refactorで実装する。
- プロジェクト既定の`lint → test → build`を実行し、結果を記録する。

## 要件トレーサビリティ（初版）

| 要件ID | 要件要約 | requirement参照 | 実装（予定） | テスト（予定） | 状態 | 備考 |
| --- | --- | --- | --- | --- | --- | --- |
| REQ-001 | Git知識を要求しないUX | `doc/requirement.md` 1.2 | `frontend` UI設計 | UI/E2E | 未着手 | 仮採番 |
| REQ-002 | `.md`をGitHub資産として保存 | `doc/requirement.md` 1.2, 3.1 | Workers保存API | API/E2E | 未着手 | 仮採番 |
| REQ-003 | Workers経由でGitHub連携（秘密情報をPWAに持たせない） | `doc/requirement.md` 2.2 | Workers認証/保存API | API統合 | 未着手 | 仮採番 |
| REQ-004 | 設定を`.app/config.json`で管理 | `doc/requirement.md` 3.2, 10 | 設定読み書き層 | 単体/統合 | 未着手 | 仮採番 |
| REQ-005 | Start View選択（Today/Recent/Folder） | `doc/requirement.md` 4.1 | フロント画面遷移 | UI/E2E | 未着手 | 仮採番 |
| REQ-006 | Start ViewデフォルトはJournal Today | `doc/requirement.md` 4.2 | 初期ルーティング | UI | 未着手 | 仮採番 |
| REQ-007 | Journalを指定フォルダへ自動保存 | `doc/requirement.md` 5.1 | パス解決ロジック | 単体 | 未着手 | 仮採番 |
| REQ-008 | Asia/Tokyo基準で日次ファイル命名 | `doc/requirement.md` 5.2 | 日付/パス生成 | 単体 | 未着手 | 仮採番 |
| REQ-009 | 起動時に存在確認し未存在ならテンプレ生成 | `doc/requirement.md` 5.3, 5.4 | 起動時生成処理 | 単体/統合 | 未着手 | 仮採番 |
| REQ-010 | 編集・手動保存・離脱時/バックグラウンド時保存 | `doc/requirement.md` 6.1, 6.2 | 編集画面/保存制御 | UI/E2E | 未着手 | 仮採番 |
| REQ-011 | autosaveの閾値（秒数/文字数）でコミット抑制 | `doc/requirement.md` 6.3 | 保存スロットル | 単体 | 未着手 | 仮採番 |
| REQ-012 | ローカル下書き保持と失敗時保全 | `doc/requirement.md` 7.1 | IndexedDB層 | 単体/統合 | 未着手 | 仮採番 |
| REQ-013 | 起動時はローカル下書き優先＋未同期表示 | `doc/requirement.md` 7.2, 7.3 | 復元/未同期UI | UI/E2E | 未着手 | 仮採番 |
| REQ-014 | 競合時は自動マージせず別名保存 | `doc/requirement.md` 8.1, 8.2 | 競合判定/別名保存 | 統合/E2E | 未着手 | 仮採番 |
| REQ-015 | 履歴・差分・復元（復元操作も履歴化） | `doc/requirement.md` 9.1, 9.2, 9.3 | 履歴UI/復元API | 統合/E2E | 未着手 | 仮採番 |
| REQ-016 | 非目標（ローカルGit保持なし/高度Git UIなし/自動マージなし） | `doc/requirement.md` 11 | 設計制約として遵守 | 設計レビュー | 未着手 | 仮採番 |

## タスク

- [ ] P0 - 要件IDの正式付与とトレース運用開始
種別: doc
目的: `doc/requirement.md`へ`REQ-001..`を正式付与し、以後の実装/テストがID参照できる状態にする。
受け入れ条件:
1. `doc/requirement.md`に要件IDが一意に付与される。
2. 本書のトレーサビリティ表とIDが一致する。
変更対象: `doc/requirement.md`, `doc/Todo.md`
検証手順: 差分レビューでIDの重複/欠番/不一致がないことを確認。
関連要件ID: REQ-001..REQ-016
リスク/不確実点: 要件粒度の見直しでID再編が発生する可能性。
承認要否: No

- [ ] P0 - WorkersとGitHub Appの認証疎通を最小実装
種別: app
目的: GitHub連携の技術リスクを早期に解消する。
受け入れ条件:
1. Workers経由でGitHub APIへ認証付きリクエストが成功する。
2. PWAに秘密情報を保持しない。
変更対象: Workersプロジェクト, 環境変数設定
検証手順: 開発環境で疎通APIを実行し200系レスポンスを確認。
関連要件ID: REQ-002, REQ-003, REQ-016
リスク/不確実点: GitHub App権限設定、トークン更新仕様。
承認要否: Yes

- [ ] P1 - フロントエンドMVP（Journal Today + ローカル下書き）
種別: app
目的: オフライン耐性を持つ編集体験を先に成立させる。
受け入れ条件:
1. Journal Todayで対象日ノートを開く/新規作成できる。
2. 編集内容がローカル下書きに保存される。
3. 未同期状態がUI表示される。
変更対象: フロントエンド（画面/状態管理/IndexedDB）
検証手順: オフライン状態で編集→再起動後に下書き復元を確認。
関連要件ID: REQ-005, REQ-006, REQ-007, REQ-008, REQ-009, REQ-010, REQ-012, REQ-013
リスク/不確実点: モバイルバックグラウンド時のイベント差異。
承認要否: No

- [ ] P1 - 手動保存と保存抑制つき同期連携（E2E一本化）
種別: app
目的: フロントとWorkersを接続し、保存フローを成立させる。
受け入れ条件:
1. 手動保存でGitHubにコミットされる。
2. 閾値未満の離脱時/バックグラウンド時はローカル更新のみ。
3. 閾値超過時のみGitHub保存を試行する。
変更対象: フロント保存制御, Workers保存API
検証手順: 閾値境界テスト（秒数/文字数）とE2E確認。
関連要件ID: REQ-010, REQ-011, REQ-012
リスク/不確実点: 通信失敗時リトライ戦略。
承認要否: No

- [ ] P2 - 競合時別名保存と履歴/差分/復元の実装
種別: app
目的: タイムマシン機能を完成させ、文章消失を防ぐ。
受け入れ条件:
1. 競合検知時に自動マージせず別名保存される。
2. 保存ポイント一覧と差分閲覧ができる。
3. 指定保存ポイントへの復元で新しい保存ポイントが作成される。
変更対象: Workers履歴API, フロント履歴UI/復元UI
検証手順: 2端末想定の競合シナリオと復元シナリオをE2Eで確認。
関連要件ID: REQ-014, REQ-015, REQ-016
リスク/不確実点: 差分表示の表現と性能。
承認要否: No

## 作業ログ

- 2026-02-19: `doc/Todo.md`を新規作成。`doc/requirement.md`章番号を参照して`REQ-001..016`を仮採番し、トレーサビリティ初版を作成。
