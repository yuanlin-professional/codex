# `sync.py` -- 本地图片+文本多模态输入示例（同步版）

演示使用 `LocalImageInput` 和 `TextInput` 组合发送本地图片的多模态输入。通过 `temporary_sample_image_path()` 生成一个临时 PNG 图片，将其路径与文本提示一起传入 `thread.turn()`，请求模型描述图片的颜色和布局。
