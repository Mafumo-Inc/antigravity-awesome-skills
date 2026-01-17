---
name: workflow-automation
description: "ビジュアルロジックとカスタムコードを組み合わせた自動化ワークフローの設計と実装。複数ステップの自動化、API統合、AI-nativeパイプラインの構築。自動化フローの設計、API統合、イベント駆動システムの構築、LangChainスタイルのAIワークフロー作成に使用。"
---

# 🔄 ワークフロー自動化

> [n8n](https://github.com/n8n-io/n8n)とモダンな自動化プラットフォームからインスパイアされた、堅牢な自動化ワークフロー構築のパターン集。

## このスキルを使用するタイミング

以下の場合にこのスキルを使用します:

- 複数ステップの自動化ワークフローを設計する
- 複数のAPIやサービスを統合する
- イベント駆動システムを構築する
- AI拡張パイプラインを作成する
- 複雑なフローのエラー処理を行う

---

## 1. ワークフロー設計原則

### 1.1 コアコンセプト

```
┌─────────────────────────────────────────────────────────────┐
│                      WORKFLOW                                │
│  ┌────────┐    ┌────────┐    ┌────────┐    ┌────────┐      │
│  │Trigger │───▶│  Node  │───▶│  Node  │───▶│ Action │      │
│  └────────┘    └────────┘    └────────┘    └────────┘      │
│       │              │              │              │        │
│       ▼              ▼              ▼              ▼        │
│  [Webhook]    [Transform]    [Condition]    [Send Email]   │
└─────────────────────────────────────────────────────────────┘
```

**主要コンポーネント**:

- **Trigger（トリガー）**: ワークフローを開始するもの
- **Node（ノード）**: 個別の処理ステップ
- **Edge（エッジ）**: ノード間の接続
- **Action（アクション）**: 外部への影響（API呼び出し、メール送信など）

### 1.2 トリガータイプ

```javascript
const TRIGGER_TYPES = {
  // イベントベース
  webhook: {
    description: "HTTPリクエストがワークフローを起動",
    use_case: "外部統合、フォーム送信",
    example: "POST /webhook/order-created",
  },

  // 時間ベース
  cron: {
    description: "スケジュールされた実行",
    use_case: "レポート、クリーンアップ、同期ジョブ",
    example: "0 9 * * *", // 毎日午前9時
  },

  // 変更ベース
  polling: {
    description: "定期的に変更をチェック",
    use_case: "RSS監視、ファイル変更チェック",
    example: "5分ごとに新しいアイテムをチェック",
  },

  // メッセージベース
  queue: {
    description: "メッセージキューから処理",
    use_case: "非同期処理、疎結合化",
    example: "SQS, RabbitMQ, Redis Streams",
  },

  // 手動
  manual: {
    description: "ユーザーが開始する実行",
    use_case: "テスト、オンデマンドタスク",
    example: "ワークフロー実行ボタン",
  },
};
```

### 1.3 ノードタイプ

```javascript
const NODE_TYPES = {
  // データ変換
  transform: {
    description: "データの形状や値を変更",
    operations: ["map", "filter", "merge", "split"],
  },

  // フロー制御
  condition: {
    description: "ロジックに基づいて分岐",
    operations: ["if/else", "switch", "filter"],
  },

  // 外部アクション
  action: {
    description: "外部サービスと連携",
    operations: ["HTTPリクエスト", "データベース", "メール", "API"],
  },

  // サブワークフロー
  subworkflow: {
    description: "別のワークフローを呼び出し",
    operations: ["invoke", "wait", "parallel"],
  },

  // エラー処理
  errorHandler: {
    description: "障害を適切に処理",
    operations: ["retry", "fallback", "notify"],
  },
};
```

---

## 2. 一般的なワークフローパターン

### 2.1 順次パイプライン

```javascript
// シンプルな A → B → C フロー
const sequentialWorkflow = {
  trigger: { type: "webhook", path: "/process" },
  nodes: [
    {
      id: "fetch",
      type: "http",
      config: {
        method: "GET",
        url: "{{trigger.data.api_url}}",
      },
    },
    {
      id: "transform",
      type: "code",
      config: {
        code: `
          return items.map(item => ({
            id: item.id,
            name: item.name.toUpperCase(),
            processed: true
          }));
        `,
      },
    },
    {
      id: "save",
      type: "database",
      config: {
        operation: "insert",
        table: "processed_items",
        data: "{{transform.output}}",
      },
    },
  ],
};
```

### 2.2 並列実行

```javascript
// ファンアウト: 複数のノードを並列実行
const parallelWorkflow = {
  trigger: { type: "cron", schedule: "0 * * * *" },
  nodes: [
    {
      id: "parallel_group",
      type: "parallel",
      nodes: [
        {
          id: "fetch_users",
          type: "http",
          config: { url: "/api/users" },
        },
        {
          id: "fetch_orders",
          type: "http",
          config: { url: "/api/orders" },
        },
        {
          id: "fetch_products",
          type: "http",
          config: { url: "/api/products" },
        },
      ],
    },
    {
      id: "merge",
      type: "merge",
      config: {
        method: "append", // or "combine", "zip"
        inputs: ["fetch_users", "fetch_orders", "fetch_products"],
      },
    },
  ],
};
```

### 2.3 条件分岐

```javascript
const conditionalWorkflow = {
  trigger: { type: "webhook", path: "/order" },
  nodes: [
    {
      id: "check_value",
      type: "switch",
      config: {
        property: "{{trigger.data.total}}",
        rules: [
          { operator: "gte", value: 1000, output: "high_value" },
          { operator: "gte", value: 100, output: "medium_value" },
          { operator: "lt", value: 100, output: "low_value" },
        ],
      },
    },
    {
      id: "high_value",
      type: "action",
      onlyIf: "{{check_value.output}} === 'high_value'",
      config: {
        action: "notify_sales_team",
      },
    },
    {
      id: "medium_value",
      type: "action",
      onlyIf: "{{check_value.output}} === 'medium_value'",
      config: {
        action: "send_thank_you_email",
      },
    },
    {
      id: "low_value",
      type: "action",
      onlyIf: "{{check_value.output}} === 'low_value'",
      config: {
        action: "add_to_newsletter",
      },
    },
  ],
};
```

### 2.4 ループ/イテレーターパターン

```javascript
const loopWorkflow = {
  trigger: { type: "manual" },
  nodes: [
    {
      id: "fetch_items",
      type: "http",
      config: { url: "/api/items" },
    },
    {
      id: "process_each",
      type: "loop",
      config: {
        items: "{{fetch_items.data}}",
        batchSize: 10, // Process 10 at a time
        continueOnError: true,
      },
      nodes: [
        {
          id: "enrich",
          type: "http",
          config: {
            url: "/api/enrich/{{item.id}}",
          },
        },
        {
          id: "save",
          type: "database",
          config: {
            operation: "update",
            id: "{{item.id}}",
            data: "{{enrich.output}}",
          },
        },
      ],
    },
  ],
};
```

### 2.5 待機/遅延パターン

```javascript
const waitWorkflow = {
  trigger: { type: "webhook", path: "/signup" },
  nodes: [
    {
      id: "send_welcome",
      type: "email",
      config: {
        to: "{{trigger.data.email}}",
        template: "welcome",
      },
    },
    {
      id: "wait_24h",
      type: "wait",
      config: {
        duration: "24h",
        // Or: resumeAt: "{{trigger.data.preferred_time}}"
      },
    },
    {
      id: "send_onboarding",
      type: "email",
      config: {
        to: "{{trigger.data.email}}",
        template: "onboarding_tips",
      },
    },
  ],
};
```

---

## 3. エラー処理パターン

### 3.1 バックオフ付きリトライ

```javascript
const retryConfig = {
  retries: 3,
  backoff: "exponential", // linear, exponential, fixed
  initialDelay: 1000, // ms
  maxDelay: 30000, // ms
  retryOn: ["ECONNRESET", "ETIMEDOUT", "HTTP_5XX"],
};

const nodeWithRetry = {
  id: "api_call",
  type: "http",
  config: { url: "/api/external" },
  errorHandling: {
    retry: retryConfig,
    onMaxRetries: {
      action: "continue", // or "fail", "branch"
      fallbackValue: { data: [] },
    },
  },
};
```

### 3.2 デッドレターキュー

```javascript
const workflowWithDLQ = {
  config: {
    onError: {
      action: "send_to_dlq",
      queue: "failed_workflows",
      includeContext: true, // Include full workflow state
    },
  },
  nodes: [
    /* ... */
  ],
};

// 失敗したアイテムを処理する別のワークフロー
const dlqProcessor = {
  trigger: {
    type: "queue",
    queue: "failed_workflows",
  },
  nodes: [
    {
      id: "analyze",
      type: "code",
      config: {
        code: `
          const error = $input.error;
          const context = $input.context;
          
          // Classify error
          if (error.type === 'VALIDATION') {
            return { action: 'discard', reason: 'Bad data' };
          }
          if (error.type === 'RATE_LIMIT') {
            return { action: 'retry', delay: '1h' };
          }
          return { action: 'manual_review' };
        `,
      },
    },
  ],
};
```

### 3.3 補償/ロールバック

```javascript
const sagaWorkflow = {
  name: "order_saga",
  nodes: [
    {
      id: "reserve_inventory",
      type: "api",
      compensate: {
        id: "release_inventory",
        type: "api",
        config: { method: "POST", url: "/inventory/release" },
      },
    },
    {
      id: "charge_payment",
      type: "api",
      compensate: {
        id: "refund_payment",
        type: "api",
        config: { method: "POST", url: "/payments/refund" },
      },
    },
    {
      id: "create_shipment",
      type: "api",
      compensate: {
        id: "cancel_shipment",
        type: "api",
        config: { method: "POST", url: "/shipments/cancel" },
      },
    },
  ],
  onError: {
    strategy: "compensate_all", // Run all compensations in reverse order
  },
};
```

---

## 4. 統合パターン

### 4.1 API統合テンプレート

```javascript
const apiIntegration = {
  name: "github_integration",
  baseUrl: "https://api.github.com",
  auth: {
    type: "bearer",
    token: "{{secrets.GITHUB_TOKEN}}",
  },
  operations: {
    listRepos: {
      method: "GET",
      path: "/user/repos",
      params: {
        per_page: 100,
        sort: "updated",
      },
    },
    createIssue: {
      method: "POST",
      path: "/repos/{{owner}}/{{repo}}/issues",
      body: {
        title: "{{title}}",
        body: "{{body}}",
        labels: "{{labels}}",
      },
    },
  },
  rateLimiting: {
    requests: 5000,
    period: "1h",
    strategy: "queue", // queue, reject, throttle
  },
};
```

### 4.2 Webhookハンドラー

```javascript
const webhookHandler = {
  trigger: {
    type: "webhook",
    path: "/webhooks/stripe",
    method: "POST",
    authentication: {
      type: "signature",
      header: "stripe-signature",
      secret: "{{secrets.STRIPE_WEBHOOK_SECRET}}",
      algorithm: "sha256",
    },
  },
  nodes: [
    {
      id: "validate",
      type: "code",
      config: {
        code: `
          const event = $input.body;
          if (!['checkout.session.completed', 
                'payment_intent.succeeded'].includes(event.type)) {
            return { skip: true };
          }
          return event;
        `,
      },
    },
    {
      id: "route",
      type: "switch",
      config: {
        property: "{{validate.type}}",
        routes: {
          "checkout.session.completed": "handle_checkout",
          "payment_intent.succeeded": "handle_payment",
        },
      },
    },
  ],
};
```

---

## 5. AI-Nativeワークフロー

### 5.1 パイプライン内のLLM

```javascript
const aiWorkflow = {
  trigger: { type: "webhook", path: "/analyze" },
  nodes: [
    {
      id: "extract_text",
      type: "code",
      config: {
        code: "return { text: $input.document.content }",
      },
    },
    {
      id: "analyze_sentiment",
      type: "llm",
      config: {
        model: "gpt-4",
        prompt: `
          Analyze the sentiment of the following text.
          Return JSON: {"sentiment": "positive|negative|neutral", "confidence": 0-1}
          
          Text: {{extract_text.text}}
        `,
        responseFormat: "json",
      },
    },
    {
      id: "route_by_sentiment",
      type: "switch",
      config: {
        property: "{{analyze_sentiment.sentiment}}",
        routes: {
          negative: "escalate_to_support",
          positive: "send_thank_you",
          neutral: "archive",
        },
      },
    },
  ],
};
```

### 5.2 エージェントワークフロー

```javascript
const agentWorkflow = {
  trigger: { type: "webhook", path: "/research" },
  nodes: [
    {
      id: "research_agent",
      type: "agent",
      config: {
        model: "gpt-4",
        tools: ["web_search", "calculator", "code_interpreter"],
        maxIterations: 10,
        prompt: `
          Research the following topic and provide a comprehensive summary:
          {{trigger.topic}}
          
          Use the tools available to gather accurate, up-to-date information.
        `,
      },
    },
    {
      id: "format_report",
      type: "llm",
      config: {
        model: "gpt-4",
        prompt: `
          Format this research into a professional report with sections:
          - Executive Summary
          - Key Findings
          - Recommendations
          
          Research: {{research_agent.output}}
        `,
      },
    },
    {
      id: "send_report",
      type: "email",
      config: {
        to: "{{trigger.email}}",
        subject: "Research Report: {{trigger.topic}}",
        body: "{{format_report.output}}",
      },
    },
  ],
};
```

---

## 6. ワークフローのベストプラクティス

### 6.1 設計チェックリスト

- [ ] **べき等性**: ワークフローは複数回安全に実行できるか？
- [ ] **エラー処理**: ノードが失敗した際に何が起こるか？
- [ ] **タイムアウト**: 適切なタイムアウトが設定されているか？
- [ ] **ロギング**: 十分な観測性があるか？
- [ ] **レート制限**: 外部APIにレート制限が設定されているか？
- [ ] **秘密情報**: 認証情報は安全に保存されているか？
- [ ] **テスト**: ワークフローは独立してテストできるか？

### 6.2 命名規則

```javascript
// ワークフロー: 動詞_名詞 または 名詞_動詞
"sync_customers";
"process_orders";
"daily_report_generator";

// ノード: アクション_対象
"fetch_user_data";
"transform_to_csv";
"send_notification_email";

// 変数: 小文字のスネークケース
"order_total";
"customer_email";
"processing_date";
```

### 6.3 ワークフローのテスト

```javascript
const workflowTest = {
  name: "order_processing_test",
  workflow: "process_order",
  testCases: [
    {
      name: "valid_order",
      input: {
        order_id: "test-123",
        items: [{ sku: "A1", qty: 2 }],
      },
      expectedOutput: {
        status: "processed",
      },
      mocks: {
        inventory_check: { available: true },
        payment_process: { success: true },
      },
    },
    {
      name: "out_of_stock",
      input: {
        order_id: "test-456",
        items: [{ sku: "B2", qty: 100 }],
      },
      expectedOutput: {
        status: "failed",
        reason: "insufficient_inventory",
      },
      mocks: {
        inventory_check: { available: false },
      },
    },
  ],
};
```

---

## リソースリンク

- [n8n Documentation](https://docs.n8n.io/)
- [Temporal Workflows](https://temporal.io/)
- [Apache Airflow](https://airflow.apache.org/)
- [Zapier Automation Patterns](https://zapier.com/blog/automation-patterns/)
