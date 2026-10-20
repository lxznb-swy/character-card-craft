---
name: character-card-craft
version: 2026-10-20
description: 通用「人物卡制作」skill。用户给一个人物的大概形象描述，本 skill 先出一张 4:3 白底全身单视图提示词让用户确认长相；用户确认「可以」后，锁定该人物的形象锚点（长相 DNA），再出一张 16:9 四视图提示词（规格沿用 dragon-king-daddy-asset-craft 的人物模板）。内置多种打光选项（默认均匀棚光 / 阴天漫反射去油光），可切换风格。只输出 ChatGPT（GPT-4o 原生出图）提示词，不直接出图。默认写实真人质感，可切漫画/动漫/其它风格。触发词：人物卡、人物定妆、四视图、确认形象、出人物、做人物卡、阴天光、去油腻、压高光。
agent_created: true
display_name: 人物卡制作
display_name_en: Character Card Craft
visibility: private
---

# 人物卡制作 skill

## 定位（一句话）

用户说人话，本 skill 吐 ChatGPT 提示词。**不懂提示词工程也能用**——固定模板、词库、防呆规则都内置，用户只需说清"要一个什么样的人"。

**流程固定为四步**：
1. 用户给人物大概形象 → 出 **4:3 白底全身单视图**提示词（确认长相用）
2. 用户说「可以」→ **锁定人物形象锚点**
3. 用已锁定的锚点 → 出 **16:9 四视图**提示词（正式定妆）
4. 后续用户要改，走「修改 SOP」

---

## 服务边界

- 只做**人物卡/人物定妆**（单视图确认 + 四视图定妆 + 改长相/改服装）。
- 场景、道具、龙等其它资产不归本 skill。
- **只输出提示词，不直接出图**——用户拿到提示词自己粘进 ChatGPT（GPT-4o 原生出图）。

---

## 风格与质感（通用默认，可切换）

本 skill 不绑任何项目，自带默认风格。**默认：写实真人质感**。

| 维度 | 默认值 |
|:--|:--|
| 质感 | 写实真人（真实皮肤/五官/体态），皮肤带毛孔、细绒、自然瑕疵 |
| 肤色写法 | 必须写冷暖 + 质感，如 `fair skin with warm undertone, visible pores` |
| 去塑料感 | 必写一处「轻微不对称」`slightly asymmetric` |

**风格切换**：用户说「改成漫画」「做成动漫风」「二次元」时，把"写实真人质感"整段替换为对应风格的写法，其余结构不变。若用户没指定，默认写实真人。

---

## 打光选项（内置两种，可切换）

人物图的**光线句**默认用「均匀棚光」，用户可指定换成「阴天漫反射」。两种光线句只能二选一，不能同时写。

### 选项 A · 均匀棚光（默认）

```
soft even studio lighting, 4500K neutral
```

适合：标准定妆图、多视图一致、不带调色。

### 选项 B · 阴天漫反射（用户说「阴天光 / 去油腻 / 压高光 / 去反光」时启用）

```
overcast diffuse lighting on the face, soft shadowless even light, no oily sheen on the skin, no glossy highlight, no rim light, no backlight, no reflective highlights, no specular glare, matte skin finish, gently lowered highlights
```

中文对应：
- 阴天漫反射光、柔和无阴影的均匀光
- 面部无油腻高光（no oily sheen）
- 无侧逆光（no rim light）、无逆光（no backlight）
- 无任何反光 / 镜面高光（no reflective highlights / no specular glare）
- 哑光肤质（matte skin finish）
- 高光适当压低（gently lowered highlights）

**启用方式**：把单视图/四视图模板里的 `soft even studio lighting, 4500K neutral` 替换为上面选项 B 整段，其余不动。可额外在负面规避末尾加 `no oily skin, no glossy skin, no shine on the face, no specular highlights, no rim lighting, no strong backlight, no harsh highlights, no overexposed skin, no lens flare, natural skin tone, no grey cast`（防止脸色发灰）。

---

## 核心概念：人物形象锚点（长相 DNA）

锚点 = 这张脸的 DNA，3-5 句英文，字段固定：

```
脸型/骨相 + 五官（眼·鼻·唇·眉）+ 肤色（冷暖+质感）+ 标志特征（疤/痣/纹身/异族特征的位置+形状）
```

铁律：
- **性格气质不进长相**：性格只影响「体态/气场」段，不决定五官。
- **标志特征写清位置 + 形状**，不能只写"有颗痣"。
- **异族/兽族角色**：脸部五官走写实人类标准，奇幻元素只走微征（瞳色/耳型/角/尾/鳞纹/斑点/犬齿），不做非人解剖结构；负面规避须加 `no full animal head, no muzzle, no fur-covered face, no furry body`。
- **两阶段**：未确认 = 定妆期，锚点标【待确认】，可反复改；确认「可以」后 = 复用期，锚点标【已锁定】，改锚点 = 换脸，须提醒用户后果。

---

## 一、第一步：出 4:3 单视图（确认长相）

用户给大概形象后，先出一张确认图提示词，**不直接出四视图**。

**规格**：4:3 全身单视图、白底、单人、正面站立。

**提示词结构**：
```
Full body single view, 4:3 aspect ratio, plain white background, one character standing upright facing the camera. [锚定块：脸型/五官/肤色/标志特征]. [年龄带]. [服装：颜色+款式+材质逐件]. [体态/气场]. Shot on Canon EOS R5, 85mm f/8, ISO 400, soft even studio lighting, 4500K neutral. Visible skin pores, natural blemishes, fabric weave. Neutral relaxed expression, natural resting face, soft focused gaze. no plastic skin, no airbrushing, no beauty-filter smoothing, no 3D render look, no CGI look, no cartoon style, no illustration, no over-symmetric face, no perfect doll skin, no extra fingers, no distorted anatomy
```

**末尾必须附「整合版」**：完整英文提示词整段重发、负面规避拼末尾、中间无中文，供一键复制。

**必须提醒用户**：这是「待确认」状态，满意后回来说「可以 / 锁定」；不满意说「改什么」。

---

## 二、第二步：确认并锁定锚点

用户说「可以」「确认」「锁定」后：
1. 把该人物的锚点标记为【已锁定】。
2. 输出一句确认：锚点已锁定，之后所有图逐字复用这个锚点。
3. 直接进入第三步，出四视图提示词（用已锁定锚点，一字不改）。

---

## 三、第三步：出 16:9 四视图（正式定妆）

规格沿用人物四视图标准：16:9 横版、单行 4 格、白底（或中性灰底）、统一棚光。

**四格结构**：

| 格 | 内容 | 作用 |
|:--|:--|:--|
| 第 1 格 | 四分之三脸头部特写（占宽最大） | 锁脸 + 锁发型 |
| 第 2 格 | 无头全身 · 正面 | 锁服装正面 |
| 第 3 格 | 无头全身 · 侧面 | 锁侧面轮廓 |
| 第 4 格 | 无头全身 · 背面 | 锁背面 |

**提示词结构**：
```
Character turnaround sheet, 16:9 horizontal layout, a single row of 4 panels from left to right, the head close-up panel wider than the three full-body panels, plain white background, consistent studio lighting. First panel, a wide three-quarter view close-up head shot, face turned about 45 degrees, entire head and full hairstyle in frame from the crown to the chin, face large in frame. Then three narrower headless full body views — front view / side profile / back view — head and neck cropped out, showing from the shoulders down. [已锁定锚定块，逐字复用]. [年龄带]. [服装：颜色+款式+材质逐件]. [体态气质]. Shot on Canon EOS R5, 85mm f/8, ISO 400, soft even studio lighting, 4500K neutral. Visible skin pores, natural blemishes, fabric weave. Each panel clearly separated, same character across all views, consistent proportions, neutral relaxed expression, natural resting face, soft focused gaze. no plastic skin, no airbrushing, no beauty-filter smoothing, no 3D render look, no CGI look, no cartoon style, no illustration, no over-symmetric face, no perfect doll skin, no extra fingers, no distorted anatomy
```

---

## 四、修改 SOP

| 不满意的是 | 改什么 | 怎么改 | 后果提醒 |
|:--|:--|:--|:--|
| 脸本身（脸型/五官/肤色/疤痣） | 锚定块 | 改锚定块对应字段，重出单视图 | ⚠️ 会换脸，旧图作废 |
| 服装/妆发/体态/气质/受伤/换装/年龄等状态变化 | 状态描述 | 垫母图 + 锁脸话术，只描述那个新状态 | 脸不动 |
| 质感/真实感（塑料、空泛） | 五要素 | 校正五要素 + 补负面规避 | 不改身份 |

### 锁脸改状态（改服装 / 受伤 / 换装 / 年龄变化等，脸保持不变）

**核心原则：只要不是改"脸本身"，就锁脸 + 只描述那个"新状态"。** 一旦在提示词里又写了脸的五官描述，GPT 会重新生成一张脸 = 换脸。

**正确做法 = 垫母图 + 锁脸话术，分两步：**

1. **垫图**：把已经出的那张母图（如 16:9 四视图）作为参考图丢给 ChatGPT。
2. **锁脸话术**（固定句式），只点明要改的那个状态：
```
Same character and face as before, but now [受伤 / 换装 / 年龄变化 / 其它状态描述].
```

或更强调锁脸的完整版：
```
Use the same character as in the reference image — keep the face and facial features identical; only change [要改的状态].
```

**示例**：

换服装：
```
Same character and face as before, but now wearing a fitted long gown in deep red and gold, high neckline, long flowing sleeves, gold-embroidered bodice.
```

受伤状态：
```
Same character and face as before, but now injured: a shallow cut on the left cheek, a torn sleeve, dirt smudges on the dress, a slightly exhausted posture.
```

**三条铁律**：
- **一次只改一个状态**：别"同时换裙子 + 受伤 + 换发型"，越改越跑偏。
- **脸和状态都想改 → 分两次**：先改脸锁定，再锁脸改状态。
- **reference 迭代最多 2 轮**：2 轮还不对就回到母图重新开始，不硬磨。

**去塑料感话术**：
```
Make the skin less smooth, add visible pores and natural imperfections, reduce the airbrushed look.
```

---

## 五、防呆铁律（出图前逐条自查）

1. **流程顺序固定**：先单视图确认，确认后才出四视图，不能跳过确认直接四视图。
2. **锚点逐字复用**：锁定后同一人物跨图，锚点一字不改；改锚点 = 换脸，必须提醒。
3. **锚点两阶段**：待确认（可改）→ 已锁定（改=换脸）。
4. **禁空泛词**：photorealistic / 高清 / 自然光 / 真实皮肤 / 电影感 / 4K / masterpiece 一律不用，换成物理描述。
5. **比例别错**：单视图 4:3、四视图 16:9。
6. **表情中性**：`neutral relaxed expression`，情绪归后续阶段，不进资产图。
7. **白底**：确认图和四视图都用 `plain white background`（用户若要灰底可改 `uniform neutral gray background`）。
8. **异族微征规则**：五官写实人类 + 奇幻元素只走微征，负面加 `no full animal head, no muzzle, no fur-covered face, no furry body`。
9. **改长相必提醒后果**：改锚点 = 换脸 = 旧图作废。
10. **单对话 ≤ 6 张**：ChatGPT 连续出图到第 6 张后换新对话重出；reference 迭代最多 2 轮。
11. **输出格式**：每条提示词末尾必附「整合版」（完整英文整段、负面拼末尾、中间无中文）。
12. **打光二选一**：光线句只能选「均匀棚光」或「阴天漫反射」其一，不能同时写；用户说「阴天光/去油腻/压高光/去反光」即切换为阴天漫反射。
13. **锁脸改状态**：改服装/受伤/换装/年龄等状态时，垫母图 + 锁脸话术（`Same character and face as before, but now [状态]`），只描述新状态、不重写脸；一次只改一个状态；迭代最多 2 轮。

---

## 输出格式（用户拿到就能复制）

```
【人物】角色名 —— 阶段：单视图确认 / 四视图定妆

── 锚定块（[待确认] / [已锁定]）──
<英文 3-5 句>
<中文对照>

── 本图提示词 ──
<英文完整提示词>
<中文对照>

── 负面规避 ──
no plastic skin, no airbrushing, no beauty-filter smoothing, no 3D render look, no CGI look, no cartoon style, no illustration, no over-symmetric face, no perfect doll skin, no extra fingers, no distorted anatomy

── 整合版（一键复制进 ChatGPT）──
<完整英文提示词整段 + 负面规避拼末尾>
```
