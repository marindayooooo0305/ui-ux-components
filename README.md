# ui-ux-components

个人 UI 组件与 UX 动效收藏库。

## 目录结构

```
ui-ux-components/
├── ENGINEERING.md                    # 开发规范文档
├── README.md                         # 项目说明
├── .gitignore                        # Git 忽略配置
├── components/                       # UI 组件
│   ├── buttons/                      # 按钮组件
│   ├── toggles/                      # 开关 / 切换组件
│   │   └── pill-toggle.html          # 药丸切换按钮
│   ├── inputs/                       # 输入框组件
│   ├── cards/                        # 卡片组件
│   ├── modals/                       # 弹窗 / 对话框组件
│   └── navigation/                   # 导航组件
├── ux/                               # UX 动效
│   ├── cursor-fog/                   # 彩雾拖尾效果（PC端）
│   │   └── index.html
│   └── cursor-fog-mobile/            # 彩雾拖尾效果（移动端）
│       └── index.html
├── effects/                          # CSS 动效 / 交互动画
└── utils/                            # 通用工具 / 帮助函数
```

## 文档

- [ENGINEERING.md](./ENGINEERING.md) - 组件与动效开发规范

## 组件列表

### 开关组件

- [药丸切换按钮](./components/toggles/pill-toggle.html) - 带有伪3D效果和反弹动画的切换按钮

### UX 动效

- [彩雾拖尾效果（PC端）](./ux/cursor-fog/index.html) - 鼠标移动时的彩色雾气拖尾效果
- [彩雾拖尾效果（移动端）](./ux/cursor-fog-mobile/index.html) - 触摸移动时的彩色雾气拖尾效果