# どうぶつ育成ゲーム(iOSアプリ版)

> **引き継ぎメモ(2026-08-29時点)**
> ここまでの作業はクラウド(Linux)のClaude Codeセッションで行いました。以降はローカルのMac + VSCode(Claude Code)に一本化して進める想定です。
> - 完了: ゲーム本体(`../index.html`)の実装・Playwrightでの動作確認、Capacitorによる`ios/`プロジェクトの生成、アイコン/スプラッシュの仮素材、Info.plistの調整(縦画面固定など)。
> - 未着手・未確認: `pod install` した実績なし、Xcodeで開いたことなし、シミュレータ・実機での起動確認なし、署名設定なし。**つまりビルドが通るかどうかも含めて未検証**です。最初にやるべきは下記「手順」の3〜7を通しで流し、素直にビルドが通るか確認すること。
> - ブランチ: `claude/hanako-game-ios-app-974fw9`(push済み、PR未作成)。ローカルではこのブランチをcheckoutしてそのまま続きから作業できます。

`../index.html` のゲーム本体を、[Capacitor](https://capacitorjs.com/) を使ってそのままiOSアプリ(WKWebViewでローカルのゲームを表示するだけの、オフラインで動くアプリ)にしたものです。

- `www/` … ゲームのコピー(ビルド時にここが `ios/App/App/public` にコピーされる)
- `ios/` … Xcodeで開く実機のプロジェクト一式(`App.xcworkspace`)
- `capacitor.config.json` … アプリID・アプリ名などの設定

## ビルドに必要なもの

このリポジトリの開発はLinux上のクラウド環境で行っているため、この場ではXcodeが無く**実機ビルド・シミュレータ起動・実際の動作確認はできていません**。以下の環境で仕上げの作業をしてください。

- macOS
- Xcode(最新版を App Store からインストール)
- [CocoaPods](https://cocoapods.org/)(`sudo gem install cocoapods`)
- Node.js 18以降

## 手順

1. このフォルダ(`games/animal/ios-app`)をMacにcloneしてくる、またはリポジトリごとcloneする。
2. 依存関係をインストール:
   ```sh
   cd games/animal/ios-app
   npm install
   ```
3. ゲーム本体(`../index.html`)を更新した場合は、`www/index.html` にも反映してから同期する:
   ```sh
   cp ../index.html www/index.html
   npx cap sync ios
   ```
4. CocoaPodsの依存関係をインストール(初回・Podfile変更時):
   ```sh
   cd ios/App
   pod install
   cd ../..
   ```
5. Xcodeで開く:
   ```sh
   npx cap open ios
   ```
   (または `ios/App/App.xcworkspace` をXcodeで直接開く。`.xcodeproj` ではなく **`.xcworkspace`** を開くこと)
6. Xcode上で、
   - 実機で動かす場合は「Signing & Capabilities」で自分のApple IDのチームを選択(無料のApple IDでもOK。7日ごとに入れ直しが必要)
   - シミュレータで確認する場合はそのまま実行ボタン(▶)を押すだけでOK
7. ▶ ボタンでビルド&実行。

## アプリの設定

- **アプリ名**: どうぶつ育成ゲーム(`capacitor.config.json` の `appName`)
- **Bundle ID**: `com.matsushigamecenter.animalgame`(仮。App Store Connect等に登録する際は、必要に応じて一意なIDに変更してください)
- **画面の向き**: 縦画面固定
- **アイコン・スプラッシュ画面**: ゲームの配色(メイン `#a7c6ff` / サブ `#cbf0ff`)に合わせて仮のアイコン(🦁)とスプラッシュ(🐭)を用意済み。差し替えたい場合は `ios/App/App/Assets.xcassets/AppIcon.appiconset/` と `Splash.imageset/` の画像を置き換えて `pod install` からやり直す。

## ゲーム内容を直したいとき

ゲームのロジック・見た目は `games/animal/index.html` (Web版・GitHub Pages公開版) が唯一のソースです。修正したら上記の手順3で `www/` に反映してから同期してください。
