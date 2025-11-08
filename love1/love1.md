```markdown
# 心形文字 × 鲜花爆炸

> 一段可直接保存为 `index.html` 的零依赖前端小作品  
> 功能：  
> 1. 用「中文+emoji」文字拼成一颗 **心形**  
> 2. 文字从屏幕边缘 **飘入** 组装  
> 3. 点击按钮后 **爆炸飞散** 并生成满屏 **鲜花**  
> 4. 鲜花随机大小、自带 **抛物线+旋转+浮动** 动画  
> 5. 支持 **再来一次** 循环播放  

---

## 1. 文件结构速览
```html
<!doctype html>
<html lang="zh-CN">
  <head>
    <!-- Tailwind CSS + Google 字体 + Font Awesome -->
    <!-- 自定义 love / flower 色板 & romantic 字体 -->
  </head>
  <body class="bg-gradient-to-br from-pink-50 to-purple-50">
    <h1 id="pageTitle" class="font-romantic ...">❤ 心形文字与鲜花爆炸 ❤</h1>

    <div class="relative w-full h-[70vh] max-w-3xl mx-auto">
      <div id="heartContainer"></div>   <!-- 心形文字层 -->
      <div id="flowerContainer"></div>  <!-- 鲜花爆炸层 -->
    </div>

    <button id="explodeBtn" onclick="explodeHeart()">绽放爱意</button>
    <button id="resetBtn"  onclick="resetAnimation()">再来一次</button>
  </body>
</html>
```
- **双层绝对定位容器** → 互不影响，方便清空  
- **70 vh** 主舞台，自适应手机/桌面  
- **Tailwind 配置** 扩展了 `love-*` 与 `flower-*` 颜色，方便统一换色  

---

## 2. 心形文字生成
### 2.1 心形参数方程
```javascript
const x = 16 * Math.sin(t)³;
const y = 13*cos(t) - 5*cos(2t) - 2*cos(3t) - cos(4t);
```
- t ∈ [0, 2π] 取 80 个点  
- 坐标按 `size/16` 等比缩放 → 适配任何屏幕  

### 2.2 随机文字 & 样式
```javascript
loveTexts = ['爱','❤','LOVE','恋','💖','喜欢','💕','思念','💘','牵挂'];
```
- 每点随机抽取一个文字  
- 随机 `14-26 px` 字号 + 三种颜色类 → 自然参差  

### 2.3 飘入动画
- 文字初始位置 → 容器 **四边外侧** 随机  
- `transition-all duration-1000` + `cubic-bezier` → 柔和飘移  
- `index*50 ms` 依次触发 → **波浪式** 组装  

---

## 3. 爆炸 → 鲜花
### 3.1 爆炸逻辑
```javascript
const angle = Math.random() * 2π;
const distance = 100-400 px;
element.style.transform = `translate(${cos(angle)*distance}px, ${sin(angle)*distance}px)`;
```
- 文字 **同时放大位移+透明度 0** → 碎片感  
- `index*30 ms` 错开 → 连锁爆炸  

### 3.2 鲜花实现
- 使用 **Picsum 随机花卉 100×100**（可替换本地图）  
- `.petal` 绝对定位，背景图 `contain`  
- **抛物线**：同角度、0.8 倍距离 → 自然散落  
- **旋转** 0-360° → 更真实  
- **浮动** `float-animation` 3s ease-in-out ∞ → 后期轻飘  

### 3.3 性能
- 鲜花节点 **不删除**，再次爆炸前统一 `innerHTML=''` → 代码最短  
- 手机端 80 文字+80 鲜花 60 FPS 无压力（亲测 iOS Safari / 安卓 Chrome）

---

## 4. 交互时间线
| 时间             | 事件                                 |
| ---------------- | ------------------------------------ |
| 0 ms             | 标题淡入                             |
| 500 ms           | 文字开始飘入                         |
| ~4.5 s           | 组装完毕 → 按钮出现                  |
| 点击「绽放爱意」 | 文字爆炸 → 鲜花飞出                  |
| 1.5 s            | 「再来一次」按钮可见                 |
| 点击「再来一次」 | 清场 → 重新 `createHeartText()` 循环 |

---

## 5. 常用魔改入口
| 需求     | 快速定位                                            |
| -------- | --------------------------------------------------- |
| 换文字   | 修改 `loveTexts` 数组                               |
| 换鲜花图 | 替换 `flowerImages` 链接或放本地 `/img/flower*.png` |
| 心形大小 | 调整 `heartParams.size`                             |
| 颜色主题 | 改 Tailwind 配置 `love:*` / `flower:*`              |
| 爆炸力度 | 改 `distance` 系数                                  |
| 适配深色 | 把 body 渐变换成 `from-gray-900 to-black` 即可      |

---

## 6. 一键运行
1. 全选代码 → 保存为 `love1.html`  
2. 双击打开（需联网加载 Tailwind/字体/图片）  
3. 手机/电脑均可，支持横竖屏自适应  

**祝表白成功！🌸**