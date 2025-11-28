# 5-3. Route Handlerの使用例

この章では、Next.jsのApp RouterにおけるRoute Handlerの実践的な使用例について学びます。外部APIプロキシやWebhookエンドポイントなどの具体的な実装例を通じて、Route Handlerの実用的な活用方法を理解できるようになります。

---

## 目次

- [外部APIプロキシ](#外部apiプロキシ)
- [Webhookエンドポイント](#webhookエンドポイント)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## 外部APIプロキシ

### APIプロキシとは
APIプロキシは、**クライアントと外部APIの間に立ってリクエストを中継する**Route Handlerです。APIキーの保護、CORS問題の回避、データの変換などに使用されます。

### APIプロキシの利点
- **APIキーの保護**: クライアントにAPIキーを公開せずに済む
- **CORS問題の回避**: サーバーサイドでリクエストを処理
- **データの変換**: レスポンスデータを変換・整形
- **レート制限の管理**: サーバーサイドでレート制限を管理
- **エラーハンドリング**: 統一されたエラーハンドリング

### 実践例: 基本的なAPIプロキシ

```tsx
// app/api/proxy/route.ts
import { NextResponse } from 'next/server'

export async function GET(request: Request) {
  try {
    const { searchParams } = new URL(request.url)
    const query = searchParams.get('q') || ''

    // 外部APIへのリクエスト
    const response = await fetch(`https://api.example.com/search?q=${query}`, {
      headers: {
        'Authorization': `Bearer ${process.env.API_KEY}`,
        'Content-Type': 'application/json',
      },
    })

    if (!response.ok) {
      throw new Error(`API error: ${response.status}`)
    }

    const data = await response.json()

    // レスポンスを返す
    return NextResponse.json(data)
  } catch (error) {
    return NextResponse.json(
      { error: 'APIリクエストに失敗しました' },
      { status: 500 }
    )
  }
}
```

### 実践例: 認証付きAPIプロキシ

```tsx
// app/api/protected-proxy/route.ts
import { NextResponse } from 'next/server'

export async function GET(request: Request) {
  try {
    // クライアントの認証情報を確認
    const authorization = request.headers.get('authorization')
    
    if (!authorization || !authorization.startsWith('Bearer ')) {
      return NextResponse.json(
        { error: '認証が必要です' },
        { status: 401 }
      )
    }

    const clientToken = authorization.substring(7)
    
    // クライアントトークンの検証（例）
    // const isValid = await verifyClientToken(clientToken)
    // if (!isValid) {
    //   return NextResponse.json(
    //     { error: '無効なトークンです' },
    //     { status: 401 }
    //   )
    // }

    // 外部APIへのリクエスト（サーバーサイドのAPIキーを使用）
    const response = await fetch('https://api.example.com/data', {
      headers: {
        'Authorization': `Bearer ${process.env.EXTERNAL_API_KEY}`,
        'Content-Type': 'application/json',
      },
    })

    if (!response.ok) {
      throw new Error(`External API error: ${response.status}`)
    }

    const data = await response.json()

    return NextResponse.json(data)
  } catch (error) {
    console.error('Proxy error:', error)
    return NextResponse.json(
      { error: 'リクエストの処理に失敗しました' },
      { status: 500 }
    )
  }
}
```

### 実践例: POSTリクエストのプロキシ

```tsx
// app/api/proxy/route.ts
import { NextResponse } from 'next/server'

export async function POST(request: Request) {
  try {
    const body = await request.json()

    // 外部APIへのPOSTリクエスト
    const response = await fetch('https://api.example.com/endpoint', {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${process.env.API_KEY}`,
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(body),
    })

    if (!response.ok) {
      const errorData = await response.json().catch(() => ({}))
      return NextResponse.json(
        { error: errorData.message || 'APIリクエストに失敗しました' },
        { status: response.status }
      )
    }

    const data = await response.json()

    return NextResponse.json(data, { status: 201 })
  } catch (error) {
    console.error('Proxy error:', error)
    return NextResponse.json(
      { error: 'リクエストの処理に失敗しました' },
      { status: 500 }
    )
  }
}
```

### 実践例: データ変換付きプロキシ

```tsx
// app/api/weather/route.ts
import { NextResponse } from 'next/server'

export async function GET(request: Request) {
  try {
    const { searchParams } = new URL(request.url)
    const city = searchParams.get('city') || 'Tokyo'

    // 外部天気APIへのリクエスト
    const response = await fetch(
      `https://api.weather.com/v1/current?city=${city}&key=${process.env.WEATHER_API_KEY}`
    )

    if (!response.ok) {
      throw new Error(`Weather API error: ${response.status}`)
    }

    const weatherData = await response.json()

    // データを変換・整形
    const transformedData = {
      city: weatherData.location.name,
      temperature: weatherData.current.temp,
      condition: weatherData.current.condition.text,
      humidity: weatherData.current.humidity,
      windSpeed: weatherData.current.wind_kph,
      timestamp: new Date().toISOString(),
    }

    return NextResponse.json(transformedData)
  } catch (error) {
    console.error('Weather proxy error:', error)
    return NextResponse.json(
      { error: '天気情報の取得に失敗しました' },
      { status: 500 }
    )
  }
}
```

### 実践例: 複数APIの統合プロキシ

```tsx
// app/api/aggregate/route.ts
import { NextResponse } from 'next/server'

export async function GET(request: Request) {
  try {
    // 複数の外部APIから並列でデータを取得
    const [usersResponse, postsResponse, commentsResponse] = await Promise.all([
      fetch('https://api.example.com/users', {
        headers: {
          'Authorization': `Bearer ${process.env.API_KEY}`,
        },
      }),
      fetch('https://api.example.com/posts', {
        headers: {
          'Authorization': `Bearer ${process.env.API_KEY}`,
        },
      }),
      fetch('https://api.example.com/comments', {
        headers: {
          'Authorization': `Bearer ${process.env.API_KEY}`,
        },
      }),
    ])

    // レスポンスの検証
    if (!usersResponse.ok || !postsResponse.ok || !commentsResponse.ok) {
      throw new Error('One or more API requests failed')
    }

    const [users, posts, comments] = await Promise.all([
      usersResponse.json(),
      postsResponse.json(),
      commentsResponse.json(),
    ])

    // データを統合
    const aggregatedData = {
      summary: {
        totalUsers: users.length,
        totalPosts: posts.length,
        totalComments: comments.length,
      },
      users: users.slice(0, 10), // 最新10件
      posts: posts.slice(0, 10),
      comments: comments.slice(0, 10),
      timestamp: new Date().toISOString(),
    }

    return NextResponse.json(aggregatedData)
  } catch (error) {
    console.error('Aggregate proxy error:', error)
    return NextResponse.json(
      { error: 'データの取得に失敗しました' },
      { status: 500 }
    )
  }
}
```

### 実践例: レート制限付きプロキシ

```tsx
// app/api/rate-limited-proxy/route.ts
import { NextResponse } from 'next/server'

// シンプルなインメモリレート制限（本番環境ではRedisなどを使用）
const requestCounts = new Map<string, { count: number; resetTime: number }>()

function checkRateLimit(ip: string, limit: number = 10, windowMs: number = 60000): boolean {
  const now = Date.now()
  const record = requestCounts.get(ip)

  if (!record || now > record.resetTime) {
    requestCounts.set(ip, { count: 1, resetTime: now + windowMs })
    return true
  }

  if (record.count >= limit) {
    return false
  }

  record.count++
  return true
}

export async function GET(request: Request) {
  try {
    // IPアドレスの取得（簡易版）
    const forwarded = request.headers.get('x-forwarded-for')
    const ip = forwarded ? forwarded.split(',')[0] : 'unknown'

    // レート制限のチェック
    if (!checkRateLimit(ip, 10, 60000)) {
      return NextResponse.json(
        { error: 'レート制限に達しました。しばらく待ってから再試行してください。' },
        { status: 429 }
      )
    }

    // 外部APIへのリクエスト
    const response = await fetch('https://api.example.com/data', {
      headers: {
        'Authorization': `Bearer ${process.env.API_KEY}`,
      },
    })

    if (!response.ok) {
      throw new Error(`API error: ${response.status}`)
    }

    const data = await response.json()

    return NextResponse.json(data)
  } catch (error) {
    console.error('Rate-limited proxy error:', error)
    return NextResponse.json(
      { error: 'リクエストの処理に失敗しました' },
      { status: 500 }
    )
  }
}
```

### 実践例: キャッシュ付きプロキシ

```tsx
// app/api/cached-proxy/route.ts
import { NextResponse } from 'next/server'

export async function GET(request: Request) {
  try {
    const { searchParams } = new URL(request.url)
    const query = searchParams.get('q') || ''

    // 外部APIへのリクエスト（キャッシュ付き）
    const response = await fetch(`https://api.example.com/search?q=${query}`, {
      headers: {
        'Authorization': `Bearer ${process.env.API_KEY}`,
      },
      next: { revalidate: 3600 }, // 1時間キャッシュ
    })

    if (!response.ok) {
      throw new Error(`API error: ${response.status}`)
    }

    const data = await response.json()

    return NextResponse.json(data, {
      headers: {
        'Cache-Control': 'public, s-maxage=3600, stale-while-revalidate=86400',
      },
    })
  } catch (error) {
    console.error('Cached proxy error:', error)
    return NextResponse.json(
      { error: 'リクエストの処理に失敗しました' },
      { status: 500 }
    )
  }
}
```

---

## Webhookエンドポイント

### Webhookとは
Webhookは、**外部サービスからアプリケーションにイベントを通知するためのHTTPコールバック**です。Route Handlerを使用してWebhookエンドポイントを作成し、外部サービスからの通知を受け取ることができます。

### Webhookの特徴
- **イベント駆動**: 外部サービスからイベントが発生したときに通知
- **非同期処理**: バックグラウンドで処理を実行
- **署名検証**: リクエストの正当性を検証
- **冪等性**: 同じイベントが複数回送信されても安全に処理

### 実践例: 基本的なWebhookエンドポイント

```tsx
// app/api/webhooks/generic/route.ts
import { NextResponse } from 'next/server'

export async function POST(request: Request) {
  try {
    const body = await request.json()

    // Webhookデータの処理
    console.log('Webhook received:', body)

    // 非同期で処理を実行（例：データベースへの保存、メール送信など）
    // await processWebhook(body)

    return NextResponse.json(
      { message: 'Webhook received successfully' },
      { status: 200 }
    )
  } catch (error) {
    console.error('Webhook error:', error)
    return NextResponse.json(
      { error: 'Webhook processing failed' },
      { status: 500 }
    )
  }
}
```

### 実践例: GitHub Webhook

```tsx
// app/api/webhooks/github/route.ts
import { NextResponse } from 'next/server'
import crypto from 'crypto'

async function verifyGitHubSignature(
  payload: string,
  signature: string,
  secret: string
): Promise<boolean> {
  const hmac = crypto.createHmac('sha256', secret)
  const digest = 'sha256=' + hmac.update(payload).digest('hex')
  return crypto.timingSafeEqual(
    Buffer.from(signature),
    Buffer.from(digest)
  )
}

export async function POST(request: Request) {
  try {
    const signature = request.headers.get('x-hub-signature-256')
    const event = request.headers.get('x-github-event')
    const payload = await request.text()

    // 署名の検証
    if (!signature || !process.env.GITHUB_WEBHOOK_SECRET) {
      return NextResponse.json(
        { error: 'Missing signature or secret' },
        { status: 401 }
      )
    }

    const isValid = await verifyGitHubSignature(
      payload,
      signature,
      process.env.GITHUB_WEBHOOK_SECRET
    )

    if (!isValid) {
      return NextResponse.json(
        { error: 'Invalid signature' },
        { status: 401 }
      )
    }

    const data = JSON.parse(payload)

    // イベントタイプに応じた処理
    switch (event) {
      case 'push':
        console.log('Push event received:', data)
        // プッシュイベントの処理
        // await handlePushEvent(data)
        break

      case 'pull_request':
        console.log('Pull request event received:', data)
        // プルリクエストイベントの処理
        // await handlePullRequestEvent(data)
        break

      case 'issues':
        console.log('Issue event received:', data)
        // イシューイベントの処理
        // await handleIssueEvent(data)
        break

      default:
        console.log('Unknown event type:', event)
    }

    return NextResponse.json({ message: 'Webhook processed' }, { status: 200 })
  } catch (error) {
    console.error('GitHub webhook error:', error)
    return NextResponse.json(
      { error: 'Webhook processing failed' },
      { status: 500 }
    )
  }
}
```

### 実践例: Stripe Webhook

```tsx
// app/api/webhooks/stripe/route.ts
import { NextResponse } from 'next/server'
import Stripe from 'stripe'

const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!, {
  apiVersion: '2023-10-16',
})

export async function POST(request: Request) {
  try {
    const body = await request.text()
    const signature = request.headers.get('stripe-signature')

    if (!signature || !process.env.STRIPE_WEBHOOK_SECRET) {
      return NextResponse.json(
        { error: 'Missing signature or secret' },
        { status: 400 }
      )
    }

    // Stripeの署名を検証
    let event: Stripe.Event

    try {
      event = stripe.webhooks.constructEvent(
        body,
        signature,
        process.env.STRIPE_WEBHOOK_SECRET
      )
    } catch (err) {
      console.error('Webhook signature verification failed:', err)
      return NextResponse.json(
        { error: 'Webhook signature verification failed' },
        { status: 400 }
      )
    }

    // イベントタイプに応じた処理
    switch (event.type) {
      case 'payment_intent.succeeded':
        const paymentIntent = event.data.object as Stripe.PaymentIntent
        console.log('Payment succeeded:', paymentIntent.id)
        // 支払い成功の処理
        // await handlePaymentSuccess(paymentIntent)
        break

      case 'payment_intent.payment_failed':
        const failedPayment = event.data.object as Stripe.PaymentIntent
        console.log('Payment failed:', failedPayment.id)
        // 支払い失敗の処理
        // await handlePaymentFailure(failedPayment)
        break

      case 'customer.subscription.created':
        const subscription = event.data.object as Stripe.Subscription
        console.log('Subscription created:', subscription.id)
        // サブスクリプション作成の処理
        // await handleSubscriptionCreated(subscription)
        break

      case 'customer.subscription.deleted':
        const deletedSubscription = event.data.object as Stripe.Subscription
        console.log('Subscription deleted:', deletedSubscription.id)
        // サブスクリプション削除の処理
        // await handleSubscriptionDeleted(deletedSubscription)
        break

      default:
        console.log('Unhandled event type:', event.type)
    }

    return NextResponse.json({ received: true }, { status: 200 })
  } catch (error) {
    console.error('Stripe webhook error:', error)
    return NextResponse.json(
      { error: 'Webhook processing failed' },
      { status: 500 }
    )
  }
}
```

### 実践例: 汎用的なWebhookハンドラー

```tsx
// app/api/webhooks/[provider]/route.ts
import { NextResponse } from 'next/server'

interface WebhookHandler {
  verifySignature: (payload: string, signature: string) => Promise<boolean>
  processEvent: (event: any) => Promise<void>
}

const handlers: Record<string, WebhookHandler> = {
  github: {
    verifySignature: async (payload, signature) => {
      // GitHub署名検証の実装
      return true
    },
    processEvent: async (event) => {
      console.log('Processing GitHub event:', event)
      // GitHubイベントの処理
    },
  },
  stripe: {
    verifySignature: async (payload, signature) => {
      // Stripe署名検証の実装
      return true
    },
    processEvent: async (event) => {
      console.log('Processing Stripe event:', event)
      // Stripeイベントの処理
    },
  },
}

export async function POST(
  request: Request,
  { params }: { params: { provider: string } }
) {
  try {
    const provider = params.provider
    const handler = handlers[provider]

    if (!handler) {
      return NextResponse.json(
        { error: `Unknown provider: ${provider}` },
        { status: 400 }
      )
    }

    const signature = request.headers.get('x-signature')
    const payload = await request.text()

    if (!signature) {
      return NextResponse.json(
        { error: 'Missing signature' },
        { status: 401 }
      )
    }

    // 署名の検証
    const isValid = await handler.verifySignature(payload, signature)

    if (!isValid) {
      return NextResponse.json(
        { error: 'Invalid signature' },
        { status: 401 }
      )
    }

    const event = JSON.parse(payload)

    // イベントの処理（非同期で実行）
    await handler.processEvent(event)

    return NextResponse.json({ received: true }, { status: 200 })
  } catch (error) {
    console.error('Webhook error:', error)
    return NextResponse.json(
      { error: 'Webhook processing failed' },
      { status: 500 }
    )
  }
}
```

### 実践例: Webhookの冪等性保証

```tsx
// app/api/webhooks/idempotent/route.ts
import { NextResponse } from 'next/server'

// 簡易的なイベントIDの記録（本番環境ではデータベースを使用）
const processedEvents = new Set<string>()

export async function POST(request: Request) {
  try {
    const body = await request.json()
    const eventId = body.id || body.event_id

    if (!eventId) {
      return NextResponse.json(
        { error: 'Missing event ID' },
        { status: 400 }
      )
    }

    // 既に処理済みのイベントかチェック
    if (processedEvents.has(eventId)) {
      console.log('Event already processed:', eventId)
      return NextResponse.json(
        { message: 'Event already processed' },
        { status: 200 }
      )
    }

    // イベントの処理
    // await processEvent(body)

    // 処理済みとして記録
    processedEvents.add(eventId)

    // 古いイベントIDを削除（メモリ管理）
    if (processedEvents.size > 1000) {
      const firstId = processedEvents.values().next().value
      processedEvents.delete(firstId)
    }

    return NextResponse.json({ message: 'Event processed' }, { status: 200 })
  } catch (error) {
    console.error('Idempotent webhook error:', error)
    return NextResponse.json(
      { error: 'Webhook processing failed' },
      { status: 500 }
    )
  }
}
```

### 実践例: Webhookの非同期処理

```tsx
// app/api/webhooks/async/route.ts
import { NextResponse } from 'next/server'

export async function POST(request: Request) {
  try {
    const body = await request.json()

    // 即座にレスポンスを返す（202 Accepted）
    // 実際の処理はバックグラウンドで実行
    processWebhookAsync(body).catch((error) => {
      console.error('Async webhook processing error:', error)
      // エラーログや通知システムに送信
    })

    return NextResponse.json(
      { message: 'Webhook received, processing...' },
      { status: 202 }
    )
  } catch (error) {
    console.error('Webhook error:', error)
    return NextResponse.json(
      { error: 'Webhook processing failed' },
      { status: 500 }
    )
  }
}

async function processWebhookAsync(data: any) {
  // 時間のかかる処理を非同期で実行
  console.log('Processing webhook asynchronously:', data)

  // データベースへの保存
  // await saveToDatabase(data)

  // メール送信
  // await sendEmail(data)

  // その他の処理
  // await performOtherTasks(data)
}
```

---

## まとめ

この章では、Next.jsのApp RouterにおけるRoute Handlerの実践的な使用例について学びました。

### 学んだこと
- 外部APIプロキシ: クライアントと外部APIの間でリクエストを中継
- Webhookエンドポイント: 外部サービスからのイベント通知を受け取る

### 重要なポイント
1. **APIプロキシ**: APIキーの保護、CORS問題の回避、データ変換などに有用
2. **認証付きプロキシ**: クライアント認証とサーバーサイドAPIキーの管理
3. **レート制限**: サーバーサイドでレート制限を実装
4. **キャッシュ**: プロキシレスポンスをキャッシュしてパフォーマンス向上
5. **Webhook署名検証**: リクエストの正当性を検証
6. **冪等性**: 同じイベントが複数回送信されても安全に処理
7. **非同期処理**: 時間のかかる処理をバックグラウンドで実行

### 次のステップ
次の章では、Server Actionsについて詳しく学びます。

---

## 演習問題

### 問題1: 基本的なAPIプロキシ
外部APIへのリクエストをプロキシするRoute Handlerを作成してください。

<details>
<summary>解答例</summary>

```tsx
// app/api/proxy/route.ts
import { NextResponse } from 'next/server'

export async function GET(request: Request) {
  try {
    const { searchParams } = new URL(request.url)
    const query = searchParams.get('q') || ''

    const response = await fetch(`https://api.example.com/search?q=${query}`, {
      headers: {
        'Authorization': `Bearer ${process.env.API_KEY}`,
      },
    })

    if (!response.ok) {
      throw new Error(`API error: ${response.status}`)
    }

    const data = await response.json()
    return NextResponse.json(data)
  } catch (error) {
    return NextResponse.json(
      { error: 'APIリクエストに失敗しました' },
      { status: 500 }
    )
  }
}
```
</details>

### 問題2: POSTリクエストのプロキシ
外部APIへのPOSTリクエストをプロキシするRoute Handlerを作成してください。

<details>
<summary>解答例</summary>

```tsx
// app/api/proxy/route.ts
import { NextResponse } from 'next/server'

export async function POST(request: Request) {
  try {
    const body = await request.json()

    const response = await fetch('https://api.example.com/endpoint', {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${process.env.API_KEY}`,
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(body),
    })

    if (!response.ok) {
      throw new Error(`API error: ${response.status}`)
    }

    const data = await response.json()
    return NextResponse.json(data, { status: 201 })
  } catch (error) {
    return NextResponse.json(
      { error: 'リクエストの処理に失敗しました' },
      { status: 500 }
    )
  }
}
```
</details>

### 問題3: 基本的なWebhookエンドポイント
Webhookイベントを受け取るRoute Handlerを作成してください。

<details>
<summary>解答例</summary>

```tsx
// app/api/webhooks/route.ts
import { NextResponse } from 'next/server'

export async function POST(request: Request) {
  try {
    const body = await request.json()

    console.log('Webhook received:', body)

    // イベントの処理
    // await processWebhook(body)

    return NextResponse.json(
      { message: 'Webhook received successfully' },
      { status: 200 }
    )
  } catch (error) {
    console.error('Webhook error:', error)
    return NextResponse.json(
      { error: 'Webhook processing failed' },
      { status: 500 }
    )
  }
}
```
</details>

### 問題4: 署名検証付きWebhook
署名検証機能を持つWebhookエンドポイントを作成してください。

<details>
<summary>解答例</summary>

```tsx
// app/api/webhooks/secure/route.ts
import { NextResponse } from 'next/server'
import crypto from 'crypto'

async function verifySignature(
  payload: string,
  signature: string,
  secret: string
): Promise<boolean> {
  const hmac = crypto.createHmac('sha256', secret)
  const digest = 'sha256=' + hmac.update(payload).digest('hex')
  return crypto.timingSafeEqual(
    Buffer.from(signature),
    Buffer.from(digest)
  )
}

export async function POST(request: Request) {
  try {
    const signature = request.headers.get('x-signature-256')
    const payload = await request.text()

    if (!signature || !process.env.WEBHOOK_SECRET) {
      return NextResponse.json(
        { error: 'Missing signature or secret' },
        { status: 401 }
      )
    }

    const isValid = await verifySignature(
      payload,
      signature,
      process.env.WEBHOOK_SECRET
    )

    if (!isValid) {
      return NextResponse.json(
        { error: 'Invalid signature' },
        { status: 401 }
      )
    }

    const data = JSON.parse(payload)
    // イベントの処理
    // await processWebhook(data)

    return NextResponse.json({ message: 'Webhook processed' }, { status: 200 })
  } catch (error) {
    console.error('Webhook error:', error)
    return NextResponse.json(
      { error: 'Webhook processing failed' },
      { status: 500 }
    )
  }
}
```
</details>

### 問題5: データ変換付きプロキシ
外部APIのレスポンスを変換して返すプロキシを作成してください。

<details>
<summary>解答例</summary>

```tsx
// app/api/transform-proxy/route.ts
import { NextResponse } from 'next/server'

export async function GET(request: Request) {
  try {
    const response = await fetch('https://api.example.com/data', {
      headers: {
        'Authorization': `Bearer ${process.env.API_KEY}`,
      },
    })

    if (!response.ok) {
      throw new Error(`API error: ${response.status}`)
    }

    const data = await response.json()

    // データを変換
    const transformedData = {
      items: data.items.map((item: any) => ({
        id: item.id,
        name: item.name,
        description: item.description,
        createdAt: new Date().toISOString(),
      })),
      total: data.items.length,
    }

    return NextResponse.json(transformedData)
  } catch (error) {
    return NextResponse.json(
      { error: 'リクエストの処理に失敗しました' },
      { status: 500 }
    )
  }
}
```
</details>

---

お疲れ様でした！次の章に進みましょう。

