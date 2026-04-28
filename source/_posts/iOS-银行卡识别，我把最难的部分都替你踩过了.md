---
title: iOS 银行卡识别，我把最难的部分都替你踩过了
date: 2026-04-28 18:14:07
tags:
---

本人在支付行业做了10年iOS开发，总绕不开一件事：让用户手动输入身份证号、银行卡号。

16-19位数字，输错一位就得重来。没人喜欢这个体验。

所以我工作之余用 ObjC 写了一个 OCR SDK，这次用Swift重写了一下 **OCRKit**，一个开箱即用的 iOS 银行卡/身份证 OCR 框架。把手机摄像头对准卡，自动识别卡号、银行名、卡类型等，不需要服务器，纯端侧运行。

---

## 简介

- ✅ 实时矩形检测（自动对焦、稳定触发）
- ✅ 银行卡
- ✅ 身份证
- ✅ 支持实时扫描、相册两种模式
- ✅ 支持深色/低对比度卡片、模糊 + 凸印卡、倾斜拍摄等复杂场景
- ✅ 纯 Swift，无第三方依赖
- ✅ iOS 15+，支持 arm64 真机与模拟器
- ✅ 动态显示手动拍照按钮

{% asset_img Camera.PNG %}

---

## 接入方式

支持三种常见接入方式：SPM、CocoaPods以及手动：

**Swift Package Manager**（推荐）：Xcode → File → Add Package Dependencies，填入：

```
https://github.com/FreakLee/OCRKit-Release
```

或在 `Package.swift` 中添加：

```swift
dependencies: [
    .package(url: "https://github.com/FreakLee/OCRKit-Release", from: "1.0.0"),
],
targets: [
    .target(name: "YourApp", dependencies: ["OCRKit"]),
]
```

**CocoaPods**：

```ruby
pod 'OCRKit'
```

**手动**：
1. 前往 [Releases](https://github.com/FreakLee/OCRKit-Release/releases) 下载最新的 `OCRKit.xcframework.zip`
2. 解压，将 `OCRKit.xcframework` 拖入 Xcode 项目
3. 在 Target → General → Frameworks, Libraries, and Embedded Content 中设置为 **Embed & Sign**

接入完成后，两行代码启动扫描：

```swift
// 1、所在控制器页面导入
import OCRKit

// 2、启动扫描

// 银行卡
let result = try await OCRKitSDK.shared.scanBankCard(from: self)

// 身份证人像面
//let result = try await OCRKitSDK.shared.scanIDCardFront(from: self)

// 身份证国徽面
//let result = try await OCRKitSDK.shared.scanIDCardBack(from: self)
```

---

## 它能处理哪些"麻烦卡"

**模糊 + 凸印卡**：浮雕数字光线不均匀，识别失败后自动切换增强模式，并显示拍照按钮让用户主动补拍。

{% asset_img MS.PNG %}

**倾斜拍摄**：内置透视矫正，歪着拍也能识别，最大支持 45° 倾斜。

{% asset_img IDCard.PNG %}

**深色 / 低对比度卡**：自适应分析图像亮度和对比度，黑卡、白金卡各自使用不同的增强参数，不靠固定值硬猜。

{% asset_img ABC.PNG %}

---

## 后续规划

- 🔲 扫描二维码 / 条形码
- 🚗 行驶证 / 驾驶证识别
- 🌐 多语言卡面支持

OCRKit 完全离线运行，数据不出设备，适合对隐私敏感的金融、医疗类 App。

[GitHub](https://github.com/FreakLee/OCRKit-Release)，欢迎 Star 和提 Issue。

---