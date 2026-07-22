# TID Tech Expo ヒーローセクション Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** TID Tech Expo 2026 出展募集LPのヒーローセクションを単一 `index.html` で実装する。

**Architecture:** ビルド不要の単一HTMLファイル。Tailwind CSS(CDN)でユーティリティ、`<style>`内カスタムCSSでアニメーション/ガラス調装飾、`<script>`内vanilla JSでモバイルメニュー開閉とfadeUp発火を制御する。

**Tech Stack:** HTML5 / Tailwind CSS(CDN) / Vanilla JS(ES6) / Google Fonts(Noto Sans JP)。フレームワーク・ビルドツール・テストランナーは使用しない。

## Global Constraints

- 単一ファイル `index.html` に完結させる(別途 `.css`/`.js` ファイルを作らない)— 設計仕様(`docs/specs/2026-07-22-tidexpo-hero-motion-design.md`)の方針
- フォントは Noto Sans JP のみ使用(欧文専用フォント不可)
- 配色は `--color-bg: #F8F9FB`, `--color-text: #192837`, `--color-accent-purple: #7342E2`, `--color-accent-blue: #3B82F6`, `--color-glass-bg: rgba(255,255,255,0.55)` を厳守
- fadeUpアニメーションの easing は `cubic-bezier(0.22, 1, 0.36, 1)`, duration `0.6s` を厳守
- 外部応募フォームURLは `<!-- TODO: 外部応募フォームURLをここに設定 -->` コメント付きの仮リンクにする
- **テスト方針:** ビルド/テストランナー非導入方針のため、各タスクの検証は「ローカルサーバーで `index.html` を開きブラウザで確認」を正としたテストステップとする。JS動作ロジック(メニュー開閉・fadeUp発火)はブラウザDevTools ConsoleでのDOM状態確認を検証手順に含める。

---

### Task 1: HTMLスケルトン + フォント/カラー基盤 + 背景グラスオーブ

**Files:**
- Create: `TID_Tech_Expo/index.html`

**Interfaces:**
- Produces: `:root` CSS変数(`--color-bg`, `--color-text`, `--color-accent-purple`, `--color-accent-blue`, `--color-glass-bg`)。以降のタスクはこれらの変数を参照する。
- Produces: `.orb-field` コンテナと `.orb` 要素5個、`@keyframes float` アニメーション。後続タスクはこの背景の上にnavbar/heroを重ねる。
- Produces: `<!-- NAVBAR -->` `<!-- HERO -->` `<!-- MOBILE MENU -->` のアンカーコメント。Task 2〜4はこの位置に要素を挿入する。

- [ ] **Step 1: `index.html` を作成**

```html
<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>TID Tech Expo 2026 | 出展者募集</title>
<script src="https://cdn.tailwindcss.com"></script>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Noto+Sans+JP:wght@400;500;700;900&display=swap" rel="stylesheet">
<style>
  :root {
    --color-bg: #F8F9FB;
    --color-text: #192837;
    --color-accent-purple: #7342E2;
    --color-accent-blue: #3B82F6;
    --color-glass-bg: rgba(255, 255, 255, 0.55);
  }
  * { margin: 0; padding: 0; box-sizing: border-box; }
  html, body { overflow-x: hidden; }
  body {
    font-family: 'Noto Sans JP', sans-serif;
    background: var(--color-bg);
    color: var(--color-text);
  }

  .orb-field {
    position: absolute;
    inset: 0;
    z-index: 0;
    overflow: hidden;
    pointer-events: none;
  }
  .orb {
    position: absolute;
    border-radius: 50%;
    background: radial-gradient(circle at 30% 30%, rgba(255,255,255,0.9), var(--color-accent-purple) 40%, var(--color-accent-blue) 100%);
    filter: blur(1px);
    opacity: 0.55;
    animation: float ease-in-out infinite;
  }
  @keyframes float {
    0%, 100% { transform: translateY(0) rotate(0deg); }
    50% { transform: translateY(-24px) rotate(8deg); }
  }
</style>
</head>
<body>
  <div class="relative w-full min-h-screen">
    <div class="orb-field" aria-hidden="true">
      <div class="orb" style="width:120px; height:120px; top:8%; left:6%; animation-duration:9s; animation-delay:0s;"></div>
      <div class="orb" style="width:70px; height:70px; top:18%; left:82%; animation-duration:7s; animation-delay:1.2s;"></div>
      <div class="orb" style="width:160px; height:160px; top:55%; left:88%; animation-duration:10s; animation-delay:0.5s;"></div>
      <div class="orb" style="width:50px; height:50px; top:70%; left:12%; animation-duration:6s; animation-delay:2s;"></div>
      <div class="orb" style="width:90px; height:90px; top:40%; left:4%; animation-duration:8s; animation-delay:3s;"></div>
    </div>

    <!-- NAVBAR -->

    <!-- HERO -->

  </div>

  <!-- MOBILE MENU -->

  <script>
  </script>
</body>
</html>
```

- [ ] **Step 2: ブラウザで確認**

Run: `TID_Tech_Expo` フォルダで `python -m http.server 8000` 実行 → `http://localhost:8000/index.html` を開く
Expected:
- 背景が `#F8F9FB`(明るいグレー)
- 紫〜青グラデーションの円が5個、緩やかに上下+回転しながら漂遊している
- コンソールエラーなし

- [ ] **Step 3: Commit**

```bash
git add TID_Tech_Expo/index.html
git commit -m "feat: TIDEXPOヒーローのHTML基盤と背景グラスオーブを追加"
```

---

### Task 2: ナビバー(デスクトップ) — ロゴ・リンク・CTAボタン

**Files:**
- Modify: `TID_Tech_Expo/index.html`(`<!-- NAVBAR -->` コメント部分)

**Interfaces:**
- Consumes: Task 1の `:root` カラー変数
- Produces: `#mobile-menu-btn`(ハンバーガーボタン、id)。Task 3のJSがこのidにクリックリスナーを付与する。

- [ ] **Step 1: `<!-- NAVBAR -->` をナビバーHTMLに置き換え**

```html
    <!-- NAVBAR -->
    <header class="relative z-10 max-w-[1280px] mx-auto px-5 sm:px-8 py-4 sm:py-5 flex items-center justify-between">
      <a href="#" class="flex items-center gap-2">
        <svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" viewBox="0 0 32 32" fill="none">
          <path d="M4 4H28V10H20V28H12V10H4V4Z" fill="var(--color-text)"/>
        </svg>
        <span class="font-bold text-base" style="font-weight:700;">TID Tech Expo</span>
      </a>

      <nav class="hidden md:flex items-center gap-8 text-sm font-medium">
        <a href="#about" class="hover:opacity-70 transition-opacity">About</a>
        <a href="#program" class="hover:opacity-70 transition-opacity">Program</a>
        <a href="#entry" class="hover:opacity-70 transition-opacity">Entry</a>
        <a href="#overview" class="hover:opacity-70 transition-opacity">Overview</a>
      </nav>

      <div class="hidden md:flex items-center">
        <!-- TODO: 外部応募フォームURLをここに設定 -->
        <a href="https://forms.google.com/..." target="_blank" rel="noopener"
           class="rounded-full px-5 py-2.5 text-white text-sm font-semibold transition-transform hover:scale-105"
           style="background: var(--color-accent-purple);">
          出展エントリー
        </a>
      </div>

      <button id="mobile-menu-btn" class="md:hidden" aria-label="メニューを開く">
        <svg xmlns="http://www.w3.org/2000/svg" width="28" height="28" viewBox="0 0 24 24" fill="none" stroke="var(--color-text)" stroke-width="2">
          <line x1="3" y1="6" x2="21" y2="6"/>
          <line x1="3" y1="12" x2="21" y2="12"/>
          <line x1="3" y1="18" x2="21" y2="18"/>
        </svg>
      </button>
    </header>
```

- [ ] **Step 2: ブラウザで確認**

Run: `http://localhost:8000/index.html` をリロード。ウィンドウ幅を768px以上/未満で切り替え
Expected:
- 768px以上: ロゴ左、中央リンク4つ、右に紫背景「出展エントリー」ボタン表示
- 768px未満: 中央リンクとCTAボタン非表示、右上にハンバーガーアイコンのみ表示

- [ ] **Step 3: Commit**

```bash
git add TID_Tech_Expo/index.html
git commit -m "feat: TIDEXPOヒーローにデスクトップナビバーを追加"
```

---

### Task 3: モバイルメニューシート(開閉JS)

**Files:**
- Modify: `TID_Tech_Expo/index.html`(`<!-- MOBILE MENU -->` コメント部分、`<script>` 部分)

**Interfaces:**
- Consumes: Task 2の `#mobile-menu-btn`
- Produces: `#mobile-menu-backdrop`, `#mobile-menu-sheet`, `#mobile-menu-close`(id)。他タスクからの依存なし(最終タスク)。

- [ ] **Step 1: `<!-- MOBILE MENU -->` をシートHTMLに置き換え**

```html
  <!-- MOBILE MENU -->
  <div id="mobile-menu-backdrop"
       class="fixed inset-0 z-20 opacity-0 pointer-events-none transition-opacity duration-300"
       style="background: rgba(25,40,55,0.35); backdrop-filter: blur(4px);"></div>

  <div id="mobile-menu-sheet"
       class="fixed right-0 top-0 z-30 flex flex-col"
       style="width: min(88vw, 360px); height: 100dvh; background: rgba(255,255,255,0.92); backdrop-filter: blur(12px);
              box-shadow: -12px 0 48px rgba(25,40,55,0.18);
              transform: translateX(100%);
              transition: transform 0.45s cubic-bezier(0.22, 1, 0.36, 1);">
    <div class="flex items-center justify-between px-5 py-4 border-b" style="border-color: rgba(25,40,55,0.1);">
      <span class="font-bold">TID Tech Expo</span>
      <button id="mobile-menu-close" aria-label="メニューを閉じる">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="var(--color-text)" stroke-width="2">
          <line x1="18" y1="6" x2="6" y2="18"/>
          <line x1="6" y1="6" x2="18" y2="18"/>
        </svg>
      </button>
    </div>
    <nav class="flex flex-col gap-1 px-5 py-6 text-base font-medium">
      <a href="#about" class="py-3">About</a>
      <a href="#program" class="py-3">Program</a>
      <a href="#entry" class="py-3">Entry</a>
      <a href="#overview" class="py-3">Overview</a>
    </nav>
    <div class="mt-auto px-5 py-6">
      <!-- TODO: 外部応募フォームURLをここに設定 -->
      <a href="https://forms.google.com/..." target="_blank" rel="noopener"
         class="block text-center rounded-full px-5 py-2.5 text-white text-sm font-semibold"
         style="background: var(--color-accent-purple);">
        出展エントリー
      </a>
    </div>
  </div>
```

- [ ] **Step 2: `<script>` 内にメニュー開閉ロジックを追加**

```html
  <script>
    const menuBtn = document.getElementById('mobile-menu-btn');
    const closeBtn = document.getElementById('mobile-menu-close');
    const backdrop = document.getElementById('mobile-menu-backdrop');
    const sheet = document.getElementById('mobile-menu-sheet');

    function openMenu() {
      backdrop.classList.remove('opacity-0', 'pointer-events-none');
      sheet.style.transform = 'translateX(0)';
    }
    function closeMenu() {
      backdrop.classList.add('opacity-0', 'pointer-events-none');
      sheet.style.transform = 'translateX(100%)';
    }

    menuBtn.addEventListener('click', openMenu);
    closeBtn.addEventListener('click', closeMenu);
    backdrop.addEventListener('click', closeMenu);
  </script>
```

- [ ] **Step 3: ブラウザで動作確認**

Run: ウィンドウ幅768px未満にしてハンバーガーアイコンをクリック
Expected:
- 右からシートがスライドイン(0.45s)、背景にぼかし+暗転バックドロップ表示
- 閉じるボタン or バックドロップクリックでシートが右へスライドアウトして閉じる
- DevTools Console: `document.getElementById('mobile-menu-sheet').style.transform` が開閉に応じて `translateX(0)` / `translateX(100%)` に切り替わることを確認

- [ ] **Step 4: Commit**

```bash
git add TID_Tech_Expo/index.html
git commit -m "feat: TIDEXPOヒーローにモバイルメニューシート開閉を追加"
```

---

### Task 4: ヒーローコンテンツ(バッジ/タイトル/リード文/CTA)

**Files:**
- Modify: `TID_Tech_Expo/index.html`(`<!-- HERO -->` コメント部分)

**Interfaces:**
- Consumes: Task 1の `:root` カラー変数
- Produces: `.fade-up` クラスを持つ4要素(`data-fade-order` 属性0〜3)。Task 5のJSがこれらを走査してアニメーションを発火する。

- [ ] **Step 1: `<!-- HERO -->` をヒーローHTMLに置き換え**

```html
    <!-- HERO -->
    <section class="relative z-10 max-w-[1280px] mx-auto px-5 sm:px-8" style="padding-top: clamp(40px, 8vw, 72px);">
      <div style="max-width: 560px;">
        <span class="fade-up inline-block rounded-full px-4 py-1.5 text-xs sm:text-sm font-medium mb-6"
              data-fade-order="0"
              style="background: var(--color-glass-bg); backdrop-filter: blur(8px); border: 1px solid rgba(115,66,226,0.25); color: var(--color-accent-purple);">
          【出展者募集中】エントリー締切: 2026年◯月◯日(金) 23:59まで
        </span>

        <h1 class="fade-up" data-fade-order="1"
            style="font-weight:900; font-size: clamp(1.8rem, 5vw, 3.2rem); line-height:1.2; letter-spacing:-0.01em; margin-bottom:20px;">
          TID Tech Expo 2026
        </h1>

        <p class="fade-up" data-fade-order="1"
           style="font-weight:700; font-size: clamp(1.1rem, 2.8vw, 1.4rem); line-height:1.5; margin-bottom:16px; color: var(--color-accent-purple);">
          創る、魅せる、つながる。<br>私たちの研究・制作成果を、ここから世界へ。
        </p>

        <p class="fade-up" data-fade-order="2"
           style="font-size: clamp(0.9rem, 2.5vw, 1.05rem); line-height:1.75; opacity:0.75; margin-bottom:32px;">
          学年や所属の垣根を超えて、日頃の研究成果や制作物を発表・共有する総合EXPO。あなたの情熱とアイディアを形にした作品を出展してみませんか？
        </p>

        <div class="fade-up flex flex-wrap items-center gap-4" data-fade-order="3">
          <!-- TODO: 外部応募フォームURLをここに設定 -->
          <a href="https://forms.google.com/..." target="_blank" rel="noopener"
             class="inline-flex items-center gap-2 rounded-full text-white font-semibold transition-transform hover:scale-105"
             style="background: var(--color-accent-purple); padding:17px 24px; min-width:210px; justify-content:center;
                    box-shadow: 0 4px 24px rgba(115,66,226,0.28); font-size: clamp(0.9rem, 2vw, 1rem);">
            出展エントリー
            <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="white" stroke-width="2">
              <circle cx="12" cy="12" r="10"/>
              <path d="M9 8l4 4-4 4"/>
            </svg>
          </a>
          <a href="#about" class="inline-flex items-center rounded-full font-semibold"
             style="padding:17px 24px; background: var(--color-glass-bg); backdrop-filter: blur(8px); color: var(--color-text);">
            開催趣旨を見る
          </a>
        </div>
      </div>
    </section>
```

- [ ] **Step 2: ブラウザで確認**

Run: `http://localhost:8000/index.html` をリロード
Expected:
- バッジ・タイトル・キャッチコピー・リード文・CTA2つが表示(この時点ではfadeUpは未実装のためCSSクラスは付与済みだが見た目は通常表示のまま)
- レイアウト崩れなし、コンソールエラーなし

- [ ] **Step 3: Commit**

```bash
git add TID_Tech_Expo/index.html
git commit -m "feat: TIDEXPOヒーローにコンテンツ(バッジ/タイトル/CTA)を追加"
```

---

### Task 5: fadeUpアニメーション発火(CSS + JS)

**Files:**
- Modify: `TID_Tech_Expo/index.html`(`<style>` 部分に `.fade-up` 定義追加、`<script>` 部分に発火ロジック追加)

**Interfaces:**
- Consumes: Task 4の `.fade-up[data-fade-order]` 要素群

- [ ] **Step 1: `<style>` 内、`.orb` 定義の後に `.fade-up` CSSを追加**

```css
  .fade-up {
    opacity: 0;
    transform: translateY(28px);
    transition: opacity 0.6s cubic-bezier(0.22, 1, 0.36, 1),
                transform 0.6s cubic-bezier(0.22, 1, 0.36, 1);
  }
  .fade-up.visible {
    opacity: 1;
    transform: translateY(0);
  }
```

- [ ] **Step 2: `<script>` 内、既存のメニューJSの後に発火ロジックを追加**

```html
    const fadeEls = document.querySelectorAll('.fade-up');
    fadeEls.forEach((el) => {
      const order = Number(el.dataset.fadeOrder || 0);
      setTimeout(() => {
        el.classList.add('visible');
      }, order * 150);
    });
```

- [ ] **Step 3: ブラウザで確認**

Run: ハードリロード(Ctrl+Shift+R)で `http://localhost:8000/index.html` を再読込
Expected:
- ページロード直後、バッジ→タイトル/キャッチコピー→リード文→CTAの順に0.15s刻みで下から浮き上がりながらフェードイン
- DevTools Console: `document.querySelectorAll('.fade-up.visible').length` がロード完了後 `4` になっている

- [ ] **Step 4: Commit**

```bash
git add TID_Tech_Expo/index.html
git commit -m "feat: TIDEXPOヒーローにfadeUpアニメーションを追加"
```

---

### Task 6: レスポンシブ最終確認 + 微調整

**Files:**
- Modify: `TID_Tech_Expo/index.html`(必要に応じて調整)

**Interfaces:**
- Consumes: Task 1〜5の全要素

- [ ] **Step 1: 主要ブレークポイントでブラウザ確認**

Run: DevTools端末エミュレーションで 375px(iPhone SE) / 768px(iPad) / 1440px(Desktop) を確認
Expected:
- 375px: ハンバーガーメニュー表示、ヒーロー文言が折り返し崩れなし、CTAボタンが画面幅内に収まる
- 768px: デスクトップナビバーとモバイルメニューの切替境界が正しく機能(768px未満でハンバーガー、以上でナビリンク表示)
- 1440px: コンテンツが `max-width:1280px` で中央寄せ、オーブが画面全体に自然分散

- [ ] **Step 2: 崩れがあれば該当箇所のTailwindクラス/インラインstyleを調整**

(この時点で発見した問題のみ修正。新規要素の追加はスコープ外)

- [ ] **Step 3: 最終ブラウザ確認 + Commit**

Run: 3ブレークポイント再確認、コンソールエラーなしを確認

```bash
git add TID_Tech_Expo/index.html
git commit -m "fix: TIDEXPOヒーローのレスポンシブ微調整"
```

---

## Self-Review Notes

- **Spec coverage:** 設計仕様の配色/フォント/背景オーブ/ナビバー/モバイルメニュー/ヒーローコンテンツ/fadeUpアニメーション/フォームURL TODOコメント、全項目をTask 1〜5でカバー済み。
- **Placeholder scan:** コード内 `TODO` コメントは設計仕様で明示的に要求された唯一の意図的プレースホルダー(外部フォームURL差し替え用)。それ以外のTBD/概要のみの記述なし。
- **Type/ID consistency:** `#mobile-menu-btn`(Task2)↔`getElementById('mobile-menu-btn')`(Task3)、`#mobile-menu-backdrop`/`#mobile-menu-sheet`/`#mobile-menu-close`(Task3 HTML↔JS)、`.fade-up[data-fade-order]`(Task4)↔`querySelectorAll('.fade-up')`+`dataset.fadeOrder`(Task5)、全て名称一致確認済み。
