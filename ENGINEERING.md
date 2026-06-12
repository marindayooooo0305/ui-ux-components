# UI/UX Components 工程规范

> 本文档为 `ui-ux-components` 项目的核心工程底座，所有组件开发、动效实现、代码提交均须遵循此规范。

---

## 目录

1. [技术规范](#1-技术规范)
2. [技术语言与工具链](#2-技术语言与工具链)
3. [项目结构](#3-项目结构)
4. [组件开发规范](#4-组件开发规范)
5. [代码开发规范](#5-代码开发规范)
6. [动效开发规范](#6-动效开发规范)
7. [Git 规范](#7-git-规范)
8. [测试规范](#8-测试规范)

---

## 1. 技术规范

### 1.1 技术栈

| 层级 | 技术选型 | 版本要求 | 说明 |
|------|---------|---------|------|
| 语言 | TypeScript | ≥ 5.0 | 严格模式开启 |
| 框架 | React | ≥ 18.0 | 使用函数组件 + Hooks |
| 构建 | Vite | ≥ 5.0 | 开发与打包 |
| 样式 | CSS Modules + Tailwind CSS | Tailwind ≥ 3.4 | 组件级样式隔离 + 原子化工具类 |
| 动效 | Framer Motion | ≥ 11.0 | 声明式动画，手势支持 |
| 文档 | Storybook | ≥ 8.0 | 组件开发与文档展示 |
| 代码质量 | ESLint + Prettier + Stylelint | - | 代码风格统一 |
| 包管理 | pnpm | ≥ 9.0 | Monorepo 工作区管理 |

### 1.2 浏览器兼容目标

```
> 0.5%
last 2 versions
not dead
not IE 11
```

### 1.3 设计令牌（Design Tokens）

所有视觉属性必须通过 Design Tokens 表达，禁止硬编码：

```typescript
// tokens.ts
export const tokens = {
  color: {
    primary: '#6366f1',
    secondary: '#8b5cf6',
    success: '#22c55e',
    warning: '#f59e0b',
    error: '#ef4444',
    neutral: {
      50: '#f8fafc',
      100: '#f1f5f9',
      // ... 完整色阶
      900: '#0f172a',
    },
  },
  spacing: {
    xs: '4px',
    sm: '8px',
    md: '16px',
    lg: '24px',
    xl: '32px',
    '2xl': '48px',
  },
  radius: {
    sm: '4px',
    md: '8px',
    lg: '12px',
    xl: '16px',
    full: '9999px',
  },
  shadow: {
    sm: '0 1px 2px rgba(0,0,0,0.05)',
    md: '0 4px 6px -1px rgba(0,0,0,0.1)',
    lg: '0 10px 15px -3px rgba(0,0,0,0.1)',
  },
  typography: {
    fontFamily: {
      sans: 'Inter, system-ui, -apple-system, sans-serif',
      mono: 'JetBrains Mono, Menlo, monospace',
    },
    fontSize: {
      xs: '0.75rem',   // 12px
      sm: '0.875rem',  // 14px
      base: '1rem',    // 16px
      lg: '1.125rem',  // 18px
      xl: '1.25rem',   // 20px
      '2xl': '1.5rem', // 24px
    },
    fontWeight: {
      normal: 400,
      medium: 500,
      semibold: 600,
      bold: 700,
    },
    lineHeight: {
      tight: 1.25,
      normal: 1.5,
      relaxed: 1.75,
    },
  },
  motion: {
    duration: {
      instant: '0ms',
      fast: '150ms',
      normal: '300ms',
      slow: '500ms',
      slower: '700ms',
    },
    easing: {
      default: 'cubic-bezier(0.4, 0, 0.2, 1)',
      decelerate: 'cubic-bezier(0, 0, 0.2, 1)',
      accelerate: 'cubic-bezier(0.4, 0, 1, 1)',
      spring: 'cubic-bezier(0.175, 0.885, 0.32, 1.275)',
      bounce: 'cubic-bezier(0.68, -0.55, 0.265, 1.55)',
    },
  },
  breakpoint: {
    sm: '640px',
    md: '768px',
    lg: '1024px',
    xl: '1280px',
  },
} as const;
```

---

## 2. 技术语言与工具链

### 2.1 TypeScript 规范

- **严格模式**：`tsconfig.json` 必须开启 `strict: true`
- **禁止使用**：`any`、`@ts-ignore`（特殊情况需注释说明原因）
- **类型导出**：所有组件 Props、事件回调、枚举必须导出类型定义
- **泛型约束**：优先使用泛型而非联合类型，保持扩展性

```typescript
// ✅ 正确：导出类型，使用泛型
export type ButtonSize = 'sm' | 'md' | 'lg';
export type ButtonVariant = 'primary' | 'secondary' | 'ghost' | 'danger';

export interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  size?: ButtonSize;
  variant?: ButtonVariant;
  loading?: boolean;
  icon?: React.ReactNode;
  onClick?: (event: React.MouseEvent<HTMLButtonElement>) => void;
}

// ❌ 错误：内联类型，不导出
const Button = (props: { s: string; v: string; onClick: Function }) => {};
```

### 2.2 React 规范

- **仅使用函数组件** + Hooks，禁止 Class 组件
- **组件命名**：PascalCase（如 `DatePicker`）
- **Hooks 命名**：`use` 前缀（如 `useAnimation`）
- **事件处理**：`handle` 前缀（如 `handleClick`）
- **回调 Props**：`on` 前缀（如 `onChange`）
- **渲染函数**：`render` 前缀（如 `renderLabel`）

### 2.3 CSS 规范

- **组件样式**使用 CSS Modules（`.module.css`），确保样式隔离
- **工具类**使用 Tailwind CSS，用于布局、间距、响应式
- **动画关键帧**定义在组件级 CSS Modules 中
- **禁止使用** `!important`、内联 `style`（动效属性除外）
- **z-index 分层**：

```typescript
export const zIndex = {
  base: 0,
  dropdown: 1000,
  sticky: 1100,
  overlay: 1200,
  modal: 1300,
  popover: 1400,
  toast: 1500,
  tooltip: 1600,
} as const;
```

---

## 3. 项目结构

```
ui-ux-components/
├── .storybook/                  # Storybook 配置
│   ├── main.ts
│   ├── preview.ts
│   └── theme.ts
├── src/
│   ├── tokens/                  # 设计令牌
│   │   ├── index.ts
│   │   ├── colors.ts
│   │   ├── spacing.ts
│   │   ├── typography.ts
│   │   └── motion.ts
│   ├── hooks/                   # 通用 Hooks
│   │   ├── useAnimation.ts
│   │   ├── useMotionPreference.ts
│   │   └── useReducedMotion.ts
│   ├── utils/                   # 工具函数
│   │   ├── cn.ts                # className 合并
│   │   ├── mergeRefs.ts
│   │   └── polymorphic.ts
│   ├── components/              # UI 组件
│   │   ├── Button/
│   │   │   ├── Button.tsx
│   │   │   ├── Button.module.css
│   │   │   ├── Button.stories.tsx
│   │   │   ├── Button.test.tsx
│   │   │   └── index.ts
│   │   ├── Modal/
│   │   └── ...
│   ├── motions/                 # 动效集合
│   │   ├── transitions/         # 过渡动效
│   │   │   ├── FadeIn.tsx
│   │   │   ├── SlideIn.tsx
│   │   │   ├── ScaleIn.tsx
│   │   │   └── index.ts
│   │   ├── gestures/            # 手势动效
│   │   │   ├── Pressable.tsx
│   │   │   ├── Draggable.tsx
│   │   │   └── index.ts
│   │   ├── scroll/              # 滚动动效
│   │   │   ├── Parallax.tsx
│   │   │   ├── ScrollReveal.tsx
│   │   │   └── index.ts
│   │   ├── layout/              # 布局动效
│   │   │   ├── AnimatePresence.tsx
│   │   │   ├── LayoutGroup.tsx
│   │   │   └── index.ts
│   │   └── presets/             # 动效预设
│   │       ├── springPresets.ts
│   │       ├── transitionPresets.ts
│   │       └── index.ts
│   └── index.ts                 # 统一导出
├── public/                      # 静态资源
├── package.json
├── pnpm-workspace.yaml
├── tsconfig.json
├── vite.config.ts
├── tailwind.config.ts
├── .eslintrc.cjs
├── .prettierrc
├── .stylelintrc.cjs
└── ENGINEERING.md               # 本文档
```

### 3.1 组件目录结构规范

每个组件/动效必须遵循以下文件结构：

```
ComponentName/
├── ComponentName.tsx            # 组件实现
├── ComponentName.module.css     # 组件样式（CSS Modules）
├── ComponentName.stories.tsx    # Storybook 文档
├── ComponentName.test.tsx       # 单元测试
└── index.ts                     # 导出入口
```

**`index.ts` 必须显式导出**，禁止 `export *`：

```typescript
// ✅ 正确
export { Button } from './Button';
export type { ButtonProps, ButtonSize, ButtonVariant } from './Button';

// ❌ 错误
export * from './Button';
```

---

## 4. 组件开发规范

### 4.1 组件设计原则

1. **单一职责**：一个组件只做一件事
2. **可组合**：通过组合而非继承构建复杂 UI
3. **可访问性（a11y）**：必须支持键盘导航、ARIA 属性、屏幕阅读器
4. **受控与非受控**：同时支持受控和非受控两种模式
5. **多态渲染**：通过 `as` prop 支持自定义渲染元素

### 4.2 组件模板

```typescript
import { forwardRef } from 'react';
import { cn } from '../../utils/cn';
import type { ButtonProps, ButtonSize, ButtonVariant } from './Button';
import styles from './Button.module.css';

// 1. 类型定义（导出）
export type ButtonSize = 'sm' | 'md' | 'lg';
export type ButtonVariant = 'primary' | 'secondary' | 'ghost' | 'danger';

export interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  /** 按钮尺寸 */
  size?: ButtonSize;
  /** 按钮变体 */
  variant?: ButtonVariant;
  /** 加载状态 */
  loading?: boolean;
  /** 左侧图标 */
  icon?: React.ReactNode;
}

// 2. 组件实现
export const Button = forwardRef<HTMLButtonElement, ButtonProps>(
  (
    {
      size = 'md',
      variant = 'primary',
      loading = false,
      icon,
      className,
      disabled,
      children,
      ...rest
    },
    ref
  ) => {
    const isDisabled = disabled || loading;

    return (
      <button
        ref={ref}
        className={cn(
          styles.button,
          styles[size],
          styles[variant],
          isDisabled && styles.disabled,
          className
        )}
        disabled={isDisabled}
        aria-busy={loading}
        {...rest}
      >
        {loading && <LoadingSpinner className={styles.icon} />}
        {!loading && icon && <span className={styles.icon}>{icon}</span>}
        {children}
      </button>
    );
  }
);

Button.displayName = 'Button';
```

### 4.3 组件 Props 设计规范

| 规则 | 说明 | 示例 |
|------|------|------|
| 必需属性最少化 | 尽可能提供合理默认值 | `size?: 'md'` |
| 回调以 `on` 开头 | 事件回调统一命名 | `onClose`, `onChange` |
| 布尔属性默认 `false` | 减少必填项 | `loading?: false` |
| 枚举值用联合类型 | 不用 enum | `'sm' \| 'md' \| 'lg'` |
| 扩展原生属性 | 继承 HTML 属性接口 | `extends React.ButtonHTMLAttributes` |
| 添加 JSDoc 注释 | 每个 prop 必须有注释 | `/** 按钮尺寸 */` |

### 4.4 可访问性（a11y）规范

每个组件必须满足以下要求：

- **键盘交互**：所有交互可通过键盘完成（Tab / Enter / Space / Escape）
- **ARIA 属性**：正确使用 `role`、`aria-label`、`aria-describedby` 等
- **焦点管理**：模态框打开时陷阱焦点，关闭时恢复焦点
- **颜色对比度**：文本与背景对比度 ≥ 4.5:1（WCAG AA）
- **动效偏好**：尊重 `prefers-reduced-motion`（见动效规范）

### 4.5 Storybook Stories 规范

```typescript
import type { Meta, StoryObj } from '@storybook/react';
import { Button } from './Button';

const meta: Meta<typeof Button> = {
  title: 'Components/Button',
  component: Button,
  tags: ['autodocs'],
  argTypes: {
    variant: {
      control: 'select',
      options: ['primary', 'secondary', 'ghost', 'danger'],
    },
    size: {
      control: 'select',
      options: ['sm', 'md', 'lg'],
    },
  },
  args: {
    children: 'Button',
    size: 'md',
    variant: 'primary',
  },
};

export default meta;
type Story = StoryObj<typeof Button>;

export const Default: Story = {};

export const AllVariants: Story = {
  render: () => (
    <div style={{ display: 'flex', gap: '8px' }}>
      <Button variant="primary">Primary</Button>
      <Button variant="secondary">Secondary</Button>
      <Button variant="ghost">Ghost</Button>
      <Button variant="danger">Danger</Button>
    </div>
  ),
};

export const Loading: Story = {
  args: { loading: true, children: 'Loading...' },
};
```

---

## 5. 代码开发规范

### 5.1 命名规范

| 类型 | 规则 | 示例 |
|------|------|------|
| 组件 | PascalCase | `DatePicker`, `Modal` |
| Hooks | camelCase + use 前缀 | `useAnimation`, `useToggle` |
| 工具函数 | camelCase | `formatDate`, `clampValue` |
| 常量 | SCREAMING_SNAKE_CASE | `MAX_Z_INDEX`, `DEFAULT_DURATION` |
| 类型/接口 | PascalCase | `ButtonProps`, `MotionConfig` |
| CSS 类名 | camelCase（CSS Modules） | `.buttonPrimary`, `.overlayVisible` |
| 文件名 | PascalCase（组件）/ camelCase（工具） | `Button.tsx`, `useToggle.ts` |
| 目录名 | PascalCase（组件）/ kebab-case（其他） | `Button/`, `motion-presets/` |

### 5.2 导入顺序

```typescript
// 1. React 相关
import { useState, useCallback, useRef } from 'react';

// 2. 第三方库
import { motion, AnimatePresence } from 'framer-motion';

// 3. 内部模块 - tokens
import { tokens } from '../../tokens';

// 4. 内部模块 - hooks
import { useReducedMotion } from '../../hooks/useReducedMotion';

// 5. 内部模块 - utils
import { cn } from '../../utils/cn';

// 6. 内部模块 - 其他组件
import { Spinner } from '../Spinner';

// 7. 类型导入
import type { ButtonProps } from './Button';

// 8. 样式导入
import styles from './Button.module.css';
```

### 5.3 函数编写规范

```typescript
// ✅ 正确：纯函数，无副作用，类型完整
const clampValue = (value: number, min: number, max: number): number => {
  return Math.min(Math.max(value, min), max);
};

// ✅ 正确：React 组件使用 forwardRef
export const Input = forwardRef<HTMLInputElement, InputProps>(
  (props, ref) => {
    // 实现
  }
);
Input.displayName = 'Input';

// ❌ 错误：默认导出组件
export default function Input() {} // 难以调试，不利于 tree-shaking

// ✅ 正确：命名导出
export const Input = forwardRef<HTMLInputElement, InputProps>(...);
```

### 5.4 条件渲染规范

```typescript
// ✅ 正确：提前返回
const Component = ({ items }: Props) => {
  if (!items.length) {
    return <EmptyState />;
  }

  return (
    <ul>
      {items.map((item) => (
        <li key={item.id}>{item.name}</li>
      ))}
    </ul>
  );
};

// ❌ 错误：嵌套三元
return isLoading ? <Spinner /> : hasError ? <Error /> : <Content />;
```

### 5.5 性能规范

- **避免内联对象/函数**：使用 `useMemo` / `useCallback` 防止不必要的重渲染
- **列表渲染**：必须使用稳定且唯一的 `key`
- **懒加载**：大型组件使用 `React.lazy` + `Suspense`
- **事件委托**：列表项事件使用事件委托而非逐项绑定
- **虚拟化**：长列表（> 100 项）使用虚拟滚动

```typescript
// ✅ 正确
const handleClick = useCallback((id: string) => {
  setActiveId(id);
}, []);

const motionConfig = useMemo(() => ({
  initial: { opacity: 0 },
  animate: { opacity: 1 },
  transition: { duration: 0.3 },
}), []);

// ❌ 错误：每次渲染创建新对象/函数
<motion.div initial={{ opacity: 0 }} animate={{ opacity: 1 }} />
<button onClick={() => setActiveId(id)} />
```

---

## 6. 动效开发规范

### 6.1 动效设计原则

1. **有目的性**：每个动效必须服务于功能（引导注意力、提供反馈、建立空间关系）
2. **克制**：动效时长 ≤ 500ms（复杂布局动效除外），避免过度装饰
3. **自然**：使用物理曲线（ease-out 进入、ease-in 退出），避免线性运动
4. **可中断**：动效必须可中断，支持快速连续操作
5. **可降级**：尊重用户系统设置 `prefers-reduced-motion`

### 6.2 动效时长标准

| 类型 | 时长 | 缓动函数 | 场景 |
|------|------|---------|------|
| 微交互 | 100-150ms | `default` | hover、press、toggle |
| 过渡 | 200-300ms | `decelerate` | 显隐、展开收起 |
| 强调 | 300-500ms | `spring` | 模态框、页面切换 |
| 复杂布局 | 400-700ms | `default` | 列表重排、布局切换 |

### 6.3 Framer Motion 使用规范

#### 6.3.1 动效变体（Variants）规范

```typescript
// motions/presets/transitionPresets.ts
export const fadeVariants = {
  hidden: { opacity: 0 },
  visible: { opacity: 1 },
} as const;

export const slideUpVariants = {
  hidden: { opacity: 0, y: 20 },
  visible: { opacity: 1, y: 0 },
} as const;

export const scaleInVariants = {
  hidden: { opacity: 0, scale: 0.95 },
  visible: { opacity: 1, scale: 1 },
} as const;

// 组合变体
export const modalVariants = {
  hidden: { opacity: 0, scale: 0.95, y: 10 },
  visible: {
    opacity: 1,
    scale: 1,
    y: 0,
    transition: { type: 'spring', stiffness: 300, damping: 24 },
  },
  exit: {
    opacity: 0,
    scale: 0.95,
    y: 10,
    transition: { duration: 0.2 },
  },
} as const;
```

#### 6.3.2 Spring 预设

```typescript
// motions/presets/springPresets.ts
export const springPresets = {
  /** 弹性反馈 - 按钮、开关 */
  bouncy: { type: 'spring' as const, stiffness: 400, damping: 17 },
  /** 柔和过渡 - 面板、抽屉 */
  gentle: { type: 'spring' as const, stiffness: 200, damping: 26 },
  /** 快速响应 - Toast、提示 */
  snappy: { type: 'spring' as const, stiffness: 500, damping: 30 },
  /** 惯性滑动 - 拖拽释放 */
  momentum: { type: 'spring' as const, stiffness: 150, damping: 20 },
} as const;
```

#### 6.3.3 动效组件模板

```typescript
// motions/transitions/FadeIn.tsx
import { motion, type HTMLMotionProps } from 'framer-motion';
import { useReducedMotion } from '../../hooks/useReducedMotion';
import { fadeVariants } from '../presets/transitionPresets';
import { tokens } from '../../tokens';

export interface FadeInProps extends HTMLMotionProps<'div'> {
  /** 延迟（ms） */
  delay?: number;
  /** 时长（ms） */
  duration?: number;
  /** 是否启用动效 */
  animate?: boolean;
}

export const FadeIn: React.FC<FadeInProps> = ({
  delay = 0,
  duration = 300,
  animate = true,
  children,
  ...rest
}) => {
  const prefersReducedMotion = useReducedMotion();

  if (!animate || prefersReducedMotion) {
    return <div {...rest}>{children}</div>;
  }

  return (
    <motion.div
      variants={fadeVariants}
      initial="hidden"
      animate="visible"
      transition={{
        duration: duration / 1000,
        delay: delay / 1000,
        ease: tokens.motion.easing.decelerate,
      }}
      {...rest}
    >
      {children}
    </motion.div>
  );
};
```

### 6.4 无障碍动效规范

**所有动效组件必须尊重 `prefers-reduced-motion`**：

```typescript
// hooks/useReducedMotion.ts
import { useEffect, useState } from 'react';

export const useReducedMotion = (): boolean => {
  const [prefersReducedMotion, setPrefersReducedMotion] = useState(false);

  useEffect(() => {
    const mediaQuery = window.matchMedia('(prefers-reduced-motion: reduce)');
    setPrefersReducedMotion(mediaQuery.matches);

    const handler = (event: MediaQueryListEvent) => {
      setPrefersReducedMotion(event.matches);
    };

    mediaQuery.addEventListener('change', handler);
    return () => mediaQuery.removeEventListener('change', handler);
  }, []);

  return prefersReducedMotion;
};
```

**降级策略**：

| 用户设置 | 进入动效 | 退出动效 | 持续动效 |
|---------|---------|---------|---------|
| 正常 | 完整动效 | 完整动效 | 正常播放 |
| 减少动效 | 即时显示（0ms） | 即时隐藏（0ms） | 暂停/移除 |

### 6.5 性能规范

- **仅动画 `transform` 和 `opacity`**：避免动画 `width`、`height`、`top`、`left` 等触发布局重排的属性
- **使用 `will-change`**：对即将动画的元素提前声明（注意及时移除）
- **GPU 加速**：复杂动效使用 `transform: translateZ(0)` 提升到合成层
- **避免同时动画过多元素**：同屏动画元素 ≤ 20 个
- **使用 `layoutId`**：共享布局动效使用 Framer Motion 的 `layoutId`

```typescript
// ✅ 正确：仅动画 transform 和 opacity
<motion.div
  initial={{ opacity: 0, y: 20 }}
  animate={{ opacity: 1, y: 0 }}
/>

// ❌ 错误：动画触发布局重排的属性
<motion.div
  initial={{ opacity: 0, height: 0 }}
  animate={{ opacity: 1, height: 'auto' }}
/>

// ✅ 正确：如需高度动画，使用 transform 模拟
<motion.div
  initial={{ opacity: 0, scaleY: 0 }}
  animate={{ opacity: 1, scaleY: 1 }}
  style={{ transformOrigin: 'top' }}
/>
```

### 6.6 手势动效规范

```typescript
// 手势反馈标准
export const gestureFeedback = {
  /** 按压反馈 */
  press: {
    whileTap: { scale: 0.97 },
    transition: { duration: 0.15 },
  },
  /** 悬停反馈 */
  hover: {
    whileHover: { scale: 1.02 },
    transition: { duration: 0.2 },
  },
  /** 拖拽配置 */
  drag: {
    dragElastic: 0.1,
    dragMomentum: false,
  },
} as const;
```

### 6.7 滚动动效规范

- **使用 Intersection Observer** 触发，非 scroll 事件监听
- **阈值设置**：`threshold: 0.1`（元素进入视口 10% 时触发）
- **防抖**：滚动回调必须节流（16ms / 60fps）
- **视差系数**：0.1 - 0.5 之间，避免过度视差导致眩晕

---

## 7. Git 规范

### 7.1 分支命名

```
feat/component-name    # 新功能
fix/component-name     # 修复
refactor/component-name # 重构
docs/component-name    # 文档
chore/component-name   # 杂项
```

### 7.2 Commit Message

遵循 [Conventional Commits](https://www.conventionalcommits.org/)：

```
<type>(<scope>): <subject>

<body>
```

| type | 说明 |
|------|------|
| feat | 新组件/动效 |
| fix | 修复 Bug |
| refactor | 重构（不改变外部行为） |
| docs | 文档更新 |
| style | 样式调整（不影响逻辑） |
| perf | 性能优化 |
| test | 测试相关 |
| chore | 构建/工具链变更 |

示例：
```
feat(button): add loading state with spinner animation
fix(modal): restore focus to trigger element on close
refactor(fade-in): extract shared motion hook
```

---

## 8. 测试规范

### 8.1 测试分层

| 层级 | 工具 | 覆盖目标 |
|------|------|---------|
| 单元测试 | Vitest + Testing Library | 组件逻辑、Hooks、工具函数 |
| 交互测试 | Storybook Play Function | 组件交互行为 |
| 视觉回归 | Chromatic | UI 外观一致性 |
| 可访问性 | axe-core | a11y 合规性 |

### 8.2 测试模板

```typescript
// Button.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { describe, it, expect, vi } from 'vitest';
import { Button } from './Button';

describe('Button', () => {
  it('renders children correctly', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByRole('button', { name: 'Click me' })).toBeInTheDocument();
  });

  it('calls onClick when clicked', () => {
    const handleClick = vi.fn();
    render(<Button onClick={handleClick}>Click</Button>);
    fireEvent.click(screen.getByRole('button'));
    expect(handleClick).toHaveBeenCalledOnce();
  });

  it('is disabled when loading', () => {
    render(<Button loading>Loading</Button>);
    expect(screen.getByRole('button')).toBeDisabled();
  });

  it('has correct aria attributes when loading', () => {
    render(<Button loading>Loading</Button>);
    expect(screen.getByRole('button')).toHaveAttribute('aria-busy', 'true');
  });
});
```

### 8.3 测试覆盖率要求

- 组件：≥ 80%
- Hooks：≥ 90%
- 工具函数：≥ 95%
- 动效组件：≥ 70%（动效本身通过视觉回归测试覆盖）

---

> 本文档随项目演进持续更新，所有变更需经 Review 后合入。
