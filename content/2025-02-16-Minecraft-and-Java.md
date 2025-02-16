+++
title = "Minecraft 與 Java 的關係"
description = "為什麼玩 Minecraft 需要安裝 Java"

[taxonomies]
categories = ["Minecraft"]
+++

## 為什麼玩 Minecraft 需要安裝 Java？

Minecraft 主要分為兩個版本：**Java Edition** 和 **Bedrock Edition**。**Java Edition** 是以 Java 程式語言編寫的，所有用 Java 開發的程式都需要透過 **Java 虛擬機（Java Virtual Machine，JVM）** 來運行。因此，遊玩 Java Edition 需要安裝 Java。

而 **Bedrock Edition** 則是使用 C++ 編寫的，它可以直接執行在作業系統上，無需額外安裝 Java 環境。

## Java、JVM 與 JDK 的關係

要理解 Java 與 JVM 的關係，就不得不提到 Java Development Kit（JDK）和 Java Runtime Environment（JRE）。
- Java 是一種程式語言，本身只是抽象的規範，不能直接執行程式碼。
- JDK（Java 開發工具包）包含 JRE，以及 Java 編譯器、開發工具等，主要供開發者使用。
- JRE（Java 運行環境）包含 JVM 以及 Java 標準函式庫，讓 Java 程式能夠執行。
- JVM 是負責執行 Java 程式的虛擬機，所有 Java 程式都必須透過 JVM 運行。

對於 Minecraft 來說，執行 Java Edition 只需要 JRE 即可，但現代 Java 的發行方式通常是直接安裝 JDK，因此「安裝 Java」通常指的是安裝 JDK。

## Minecraft 需要什麼版本的 Java？

程式語言隨著時代演進會推出新版本，Minecraft 也會在新版本中採用更新的 Java。根據 [Minecraft 巴哈姆特哈啦板的文章](https://forum.gamer.com.tw/C.php?bsn=18673&snA=201333)可以知道各個版本對應的 Java 版本。

- Minecraft 1.16 及更早版本使用 Java 8。
- Minecraft 1.17 至 1.20.4 建議使用 Java 17。
- Minecraft 1.20.5 及更新版本建議使用 Java 21。

## 如何安裝 Java？

如果用 Google 搜尋 Java，第一個結果就是 Java 8 的下載連結，如果要下載其他的 Java 版本，對於 Windows 系統可以到 [Oracle 網站](https://www.oracle.com/tw/java/technologies/downloads/#java11-mac)下載，而 Linux 系統可以使用內建的套件管理器安裝 OpenJDK（開源），macOS 則可以使用 Homebrew 安裝 OpenJDK。

## 同時安裝多個版本的 Java

如果同時玩很多個版本的 Minecraft，可能需要安裝多個版本的 Java，那麼開啟遊戲的時候 Minecraft 要怎麼知道要使用哪個 Java？要解決這個問題有兩種方法，第一種是找出不同版本的 Java 的安裝路徑，在遊戲開啟時告訴 Minecraft 要用哪個路徑。

第二種方法（推薦）則是使用**第三方啟動器**如 Prism Launcher、CurseForge、Modrinth 等軟體，大部分的第三方啟動器不僅可以幫你管理遊戲版本、Java 版本，還能在上面安裝模組，十分方便。