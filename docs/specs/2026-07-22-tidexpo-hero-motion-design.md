# TID Tech Expo ヒーローセクション モーションデザイン仕様

参照元:
- `TID_Tech_Expo_Site_Spec.md`(コンテンツ・全体構成)
- `Create a fullscreen hero section fo.txt`(VaultShieldモーション参考)
- `ChatGPT Image 2026年7月22日 20_25_21.png`(グラスオーブ ビジュアル参考)

## スコープ

今回はヒーローセクションのみ実装。全体LP(About/Program/Entry/Overview/Footer)は後日。

## 技術スタック

- 単一 `index.html`(HTML/CSS/JS完結)
- Tailwind CSS(CDN経由: `https://cdn.tailwindcss.com`)
- Vanilla JS(フレームワーク不要、ビルドステップ不要、GitHub Pages即デプロイ)
- txt仕様のFramer Motionアニメーションは CSS transition + vanilla JS で再現

## 配色

txt仕様の紫アクセントを継承しつつ、md仕様のダーク基調から明るいガラス調へ変更(png着想)。

```css
:root {
  --color-bg: #F8F9FB;
  --color-text: #192837;
  --color-accent-purple: #7342E2;
  --color-accent-blue: #3B82F6;
  --color-glass-bg: rgba(255, 255, 255, 0.55);
}
```

## フォント

日本語コンテンツのため Noto Sans JP を採用(txt仕様のHelvetica Now Display Boldは欧文専用で不採用)。

- 見出し: Noto Sans JP Bold(700〜900)
- 本文: Noto Sans JP Regular/Medium(400〜500)
- Google Fonts: `https://fonts.googleapis.com/css2?family=Noto+Sans+JP:wght@400;500;700;900&display=swap`

## レイアウト構成

1. **コンテナ**: `relative w-full min-h-screen`, background `var(--color-bg)`
2. **ナビバー**: max-width 1280px centered, z-10, `px-5 sm:px-8 py-4 sm:py-5`, flex space-between
3. **ヒーローコンテンツ**: max-width 1280px centered, `paddingTop: clamp(40px, 8vw, 72px)`, コンテンツ幅上限 560px

## 背景装飾: グラスフロート球体

png着想。紫/青グラデーションのガラス調球体4〜6個を背景に配置し、緩やか上下+微小回転漂遊。

```css
.orb {
  position: absolute;
  border-radius: 50%;
  background: radial-gradient(circle at 30% 30%, rgba(255,255,255,0.9), var(--color-accent-purple) 40%, var(--color-accent-blue) 100%);
  filter: blur(1px);
  backdrop-filter: blur(8px);
  animation: float 8s ease-in-out infinite;
}

@keyframes float {
  0%, 100% { transform: translateY(0) rotate(0deg); }
  50% { transform: translateY(-24px) rotate(8deg); }
}
```

各オーブごとにサイズ・位置・`animation-delay`・`animation-duration`(6〜10s)をずらして配置し、機械的な同期を避ける。

## ロゴ(SVG)

TID Tech Expo用に新規作成。txt仕様のVaultShieldロゴ(幾何学的角形)のテイストを踏襲し、シンプルな幾何形状 32x32、fill `var(--color-text)`。

## ナビバー要素

- **左**: ロゴ + "TID Tech Expo"
- **中央(`hidden md:flex`)**: リンク4つ — `['About', 'Program', 'Entry', 'Overview']`, text-sm font-medium
- **右(デスクトップ)**: 「出展エントリー」ボタン — background `var(--color-accent-purple)`, white text, rounded-full, `px-5 py-2.5`
- **モバイル**: ハンバーガーアイコン → 右スライドインシート

## モバイルメニューシート

txt仕様のMobile Menu Sheetを踏襲(vanilla JS実装):

- **バックドロップ**: fixed inset-0, `rgba(25,40,55,0.35)` + `backdrop-filter: blur(4px)`
- **シート**: fixed right-0 top-0, width `min(88vw, 360px)`, height `100dvh`, background `rgba(255,255,255,0.9)` + blur
- **アニメーション**: `x: 100% → 0`, ease `cubic-bezier(0.22, 1, 0.36, 1)`, duration 0.45s
- **コンテンツ**: ロゴ+閉じるボタン、1px区切り線、staggeredナビリンク(delay `0.18 + i * 0.07`)、下部CTA

## ヒーローコンテンツ

- **ステータスバッジ**: 「【出展者募集中】エントリー締切: 2026年◯月◯日(金) 23:59まで」
- **メインタイトル**: 「TID Tech Expo 2026」
- **キャッチコピー**: 「創る、魅せる、つながる。私たちの研究・制作成果を、ここから世界へ。」
- **リード文**: 「学年や所属の垣根を超えて、日頃の研究成果や制作物を発表・共有する総合EXPO。あなたの情熱とアイディアを形にした作品を出展してみませんか？」
- **CTAボタン**:
  1. 「出展エントリー(外部フォームへ)」— `<!-- TODO: 外部応募フォームURLをここに設定 -->`、仮 `href="https://forms.google.com/..."` `target="_blank"`
  2. 「開催趣旨を見る」— `href="#about"`

## アニメーション(fadeUpパターン、txt仕様踏襲)

vanilla JS + CSS transitionで再現。ページロード時、要素に順次クラス付与:

```css
.fade-up {
  opacity: 0;
  transform: translateY(28px);
  transition: opacity 0.6s cubic-bezier(0.22, 1, 0.36, 1), transform 0.6s cubic-bezier(0.22, 1, 0.36, 1);
}
.fade-up.visible {
  opacity: 1;
  transform: translateY(0);
}
```

適用順: バッジ(delay 0) → タイトル(delay 0.15s) → リード文(delay 0.30s) → CTA(delay 0.45s)

## 依存関係

- Tailwind CSS(CDN)
- Google Fonts(Noto Sans JP)
- 追加ライブラリ・ビルドツール不要
