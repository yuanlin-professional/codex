# `image_rollout.rs` — 测试模块

## 测试覆盖范围
图像输入在 rollout 中的持久化，验证复制粘贴本地图像和拖放图像在 rollout 请求中的数据形状是否正确。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `copy_paste_local_image_persists_rollout_request_shape` | 复制粘贴本地图像后，rollout 中的用户消息包含正确的 InputImage 和标签文本结构 |
| `drag_drop_image_persists_rollout_request_shape` | 拖放图像后，rollout 中的用户消息包含正确的 InputImage 和标签文本结构 |
