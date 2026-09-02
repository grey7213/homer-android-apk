# homer-android-apk

惑梦 Android 客户端的成品包交付仓。**只走 Releases，不往 git 里提交 APK。**

源码在 [homer-android](https://github.com/grey7213/homer-android)。改代码、开 PR 都在那边，
这里只放编好的包。

## 这个仓库不用 clone

它就是个空壳，只有这份 README。**不要 `git clone`，不要 `git push`，
不要 `git commit` APK 进来。** 你只需要用它的 Releases 页面。

一个 APK 40 MB 以上。提交进 git 历史就永久留在对象库里，`git rm` 删不掉，
之后每个人克隆都要把它拖一遍。`.gitignore` 已经把 `*.apk` 挡了，
真想 `git add` 会被拒。Releases 的附件不进 git 对象库，所以这个仓库永远几十 KB。

## 怎么交包

编包：在 homer-android 里 `python tools/bootstrap.py` 装配后跑
`cd android-app && .\gradlew.bat testDebugUnitTest assembleDebug`。
产物在 `android-app/app/build/outputs/apk/debug/app-debug.apk`。

上传方式二选一。

**网页**（推荐，不用装东西）：开
[Releases 页](https://github.com/grey7213/homer-android-apk/releases) →
Draft a new release → 填 tag → 勾 Set as a pre-release →
把 `app-debug.apk` 拖进 Attach binaries 区 → Publish release。

**命令行**（装了 [gh CLI](https://cli.github.com/) 且跑过 `gh auth login`）：

```powershell
gh release create debug-20260902-explore-fix `
  --repo grey7213/homer-android-apk `
  --title "debug 探索页修复" `
  --notes "对应 homer-android PR #12。debug 签名，仅供验收。" `
  --prerelease `
  android-app/app/build/outputs/apk/debug/app-debug.apk
```

在 homer-android 目录里跑就行 —— `--repo` 已经指明发到哪个仓库，
不需要先 clone 这个仓库。

发 Release 需要 `write` 权限。`read` 权限只能看已发布的，建不了新的；
如果 `gh release create` 报 403，找我把权限提到 write。

## tag 怎么起

```
debug-<日期>-<改了什么>      debug-20260902-explore-fix
release-<版本>-<versionCode>  release-1.14.3-268
```

debug 包一律勾 pre-release。

## Release 说明写什么

三件事，缺一件我就得回头问：

```markdown
对应 PR: grey7213/homer-android#12
签名:    debug（Android 调试证书，装不上正式版）
验证:    MuMu 模拟器冷启动正常，探索页清数据后不再白屏，
         testDebugUnitTest 17 个全过，verify_apk_assets.py 通过
```

## 签名说明

debug 包用 Android 调试证书，`applicationId` 带 `.debug` 后缀 ——
和正式版能共存，装了不会顶掉用户手机上的应用，拿来验收正好。

正式签名由持有发布私钥的维护者完成。**贡献者不需要、也不应该拿到发布私钥。**
任何签名材料（`.keystore`、`.jks`、`.pem`、`.key`）都不要上传到这里的任何位置，
包括 Release 附件。
