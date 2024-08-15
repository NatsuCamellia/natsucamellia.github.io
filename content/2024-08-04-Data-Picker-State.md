+++
title = "Android DatePickerState 時間儲存與轉換"
description = "關於 DatePickerState 的 UTC 時間轉換"
draft = true

[taxonomies]
categories = ["Android"]
+++

## DatePickerState

`DatePickerState` 是供 `DatePicker` 這個元件使用的儲存狀態，在 `DatePicker` 選擇的日期會記錄在 `DatePickerState` 中。使用方法如下：

```kotlin
// Define state
val datePickerState = rememberDatePickerState(initialSelectedDateMillis)

...

// Use state in UI
DatePicker(
    state = datePickerState
)
```

## 問題

使用方法很簡單，但是使用上有一些小細節需要注意。根據官方文件，`initialSelectedDateMillis` 是 UTC 時間（全世界的 UTC 時間都和格林威治標準時間相同）。假設現在是台灣八月四日凌晨四點，我們呼叫 `System.currentTimeMillis()` 取得目前的時間，而當下全世界的 UTC 時間都是八月三日晚上八點，所以 `DatePicker` 就會顯示目前選擇八月三日，和我們所預期的八月四日不同。

## 解法

為了解決這個問題，我們可以先取得本地時間八月四日凌晨四點，接著說這是 UTC 時間，然後再轉換成 epoch：`LocalDateTime.now().toInstant(ZoneOffset.UTC).toEpochMilli()`。
