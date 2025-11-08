```markdown
# 便签墙

> 单文件零依赖，打开即可用。  
> 功能：  
> - 自动生成彩色便签，随机位置/角度/颜色  
> - 支持 **拖拽**、**最大化**、**最小化**、**关闭**  
> - 移动端自适应，节点数上限保护性能  
> - 后台定时补充新便签，保持墙面“永远有纸”  

---

## 1. 视觉与布局
- **网格背景**：`linear-gradient` 画 30 px 方格，模拟真实公告板  
- **卡片**：  
  - 默认 220×140 px（手机 180×130）  
  - 圆角 12 px + 阴影 0 16 px 35 px rgba(0,0,0,.2)  
  - 8 种马卡龙底色循环随机  
- **最大化**：固定定位全屏，字体自动 `clamp()` 适配，圆角归 0  
- **拖拽**：`pointerdown → pointermove → pointerup`，使用 `requestAnimationFrame` 节流

---

## 2. 关键 DOM 结构
```html
<div id="board">            <!-- 相对定位容器 -->
  <div class="card">        <!-- 绝对定位便签 -->
    <div class="card-header">   <!-- 可拖拽区 -->
      <div class="window-controls">
        <button class="control close"></button>
        <button class="control minimize"></button>
        <button class="control maximize"></button>
      </div>
      <div class="card-title">温馨提示</div>
    </div>
    <div class="card-body">随机励志句</div>
  </div>
</div>
```

---

## 3. 状态管理（WeakMap）
```javascript
const cardStates = new WeakMap()

// 单张卡片状态
{
  angle,          // 旋转角度
  scale,          // 缩放（入场动画用）
  left/top,       // 坐标
  maximized,      // 是否全屏
  closing,        // 是否正在关闭
  dragging,       // 是否拖拽中
  beforeMaximize  // 最大化前备份
}
```
- 不污染 DOM 属性，删除卡片即释放内存

---

## 4. 四大操作
| 操作       | 实现要点                                                     |
| ---------- | ------------------------------------------------------------ |
| **关闭**   | `scale→0.1` + `opacity→0` → `transitionend` 后 `remove()`    |
| **最小化** | 缩小 + 移到下边缘 → 移除节点（释放内存）                     |
| **最大化** | 备份当前样式 → 固定定位全屏 → 层级提到 1000000               |
| **拖拽**   | `pointermove` 里只记录坐标 → `rAF` 里统一 `commitDrag` 避免布局抖动 |

---

## 5. 移动端适配
- 指针媒体查询 `(pointer: coarse)` + 768 px 宽度双判断  
- 减小尺寸、降低角度范围、减少节点上限、延长生成间隔  
- 关闭拖拽（`touch-action: pan-y` 保留滚动）  
- 使用 `dvh` 单位，避免工具栏遮挡

---

## 6. 性能优化
- **节点上限**：手机 120 / 桌面 180，超出自动删除最老节点  
- **后台暂停**：`document.visibilitychange` 停止 `setTimeout` 生成  
- ** RAF 节流**：拖拽期间只记录目标坐标，一帧一次布局  
- **最大化层级隔离**： reserve 1000000 层级，避免与普通卡片竞争 z-index

---

## 7. 常用魔改
| 目的         | 改这里                   |
| ------------ | ------------------------ |
| 换句子       | 修改 `messages` 数组     |
| 换颜色       | 修改 `colors` 数组       |
| 卡片大小     | 220/140 两处（CSS + JS） |
| 生成频率     | `spawnInterval`          |
| 关闭动画时长 | CSS `transition` 0.35 s  |

---

## 8. 一键运行
1. 全选代码 → 存为 `love2.html`
2. 双击打开即可
3. 想长期用 → 放本地服务器或 Netlify/Vercel，秒部署
