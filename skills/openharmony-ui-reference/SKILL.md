---
name: openharmony-ui-reference
description: Use when developing OpenHarmony UI with ArkTS - looking up layouts, components, state management, animations, navigation, or handling errors like missing decorators, state not updating, or rendering issues
---

# OpenHarmony UI Quick Reference

## Overview

OpenHarmony uses **ArkTS** (TypeScript-based declarative UI) for building user interfaces. Core concepts: declarative UI description, component-based architecture, state management decorators, and responsive layouts.

**Official docs:** `docs-OpenHarmony-v6.0-Release/zh-cn/application-dev/ui/`

## State Management

### V2 (Recommended for New Projects)

| Decorator | Purpose | Scope |
|-----------|---------|-------|
| `@Local` | Component internal state | Component only |
| `@Param` | Parent → Child (one-way) | Parent to child |
| `@Event` | Child → Parent callbacks | Child to parent |
| `@Provider`/`@Consumer` | Cross-component sync | Any descendant |

**Two-way binding:**
```typescript
// Parent
@ComponentV2
struct Parent {
  @Local text: string = ''

  build() {
    Child({ text: this.text, onTextChange: (val) => { this.text = val } })
  }
}

// Child
@ComponentV2
struct Child {
  @Param text: string
  @Event onTextChange: (val: string) => void

  build() {
    TextInput({ text: this.text })
      .onChange((val) => this.onTextChange(val))
  }
}

// Syntax sugar (simpler)
TextInput({ text: $$this.text })
```

### V1 (Legacy)

| Decorator | Purpose | Scope |
|-----------|---------|-------|
| `@State` | Component internal state | Component only |
| `@Prop` | Parent → Child (one-way) | Parent to child |
| `@Link` | Parent ↔ Child (two-way) | Bidirectional |
| `@Provide`/`@Consume` | Cross-component sync | Any descendant |

**Documentation:** `state-management/arkts-state-management-overview.md`

## Layouts

| What You Need | Component | Key Properties | Docs |
|---------------|-----------|----------------|------|
| **Horizontal** | `Row` | space, alignItems, justifyContent | `arkts-layout-development-linear.md` |
| **Vertical** | `Column` | space, alignItems, justifyContent | `arkts-layout-development-linear.md` |
| **Stacking** | `Stack` | alignContent | `arkts-layout-development-stack-layout.md` |
| **Flexible** | `Flex` | direction, wrap, justifyContent | `arkts-layout-development-flex-layout.md` |
| **Relative** | `RelativeContainer` | x/y rules | `arkts-layout-development-relative-layout.md` |
| **Grid** | `Grid`/`GridItem` | columnsTemplate, rowsTemplate | `arkts-layout-development-grid-layout.md` |
| **Tabs** | `Tabs`/`TabContent` | barPosition, scrollable | `arkts-navigation-tabs.md` |
| **List** | `List`/`ListItem` | space, scrollBar | `arkts-layout-development-create-list.md` |
| **WaterFlow** | `WaterFlow`/`FlowItem` | columnsTemplate | `arkts-layout-development-create-waterflow.md` |

**Center content:**
```typescript
Column() {
  Text('Centered')
}
.width('100%')
.height('100%')
.justifyContent(FlexAlign.Center)
.alignItems(HorizontalAlign.Center)
```

**Documentation:** `arkts-layout-development-overview.md`

## Common Components

| Component | Usage | Docs |
|-----------|-------|------|
| **Text** | Display text | `arkts-common-components-text-display.md` |
| **TextInput** | Single line input | `arkts-common-components-text-input.md` |
| **TextArea** | Multi line input | `arkts-common-components-text-input.md` |
| **Button** | Clickable action | `arkts-common-components-button.md` |
| **Image** | Display image | `arkts-graphics-display.md` |
| **List** | Vertical scrolling | `arkts-layout-development-create-list.md` |
| **Grid** | Grid layout | `arkts-layout-development-create-grid.md` |
| **Swiper** | Carousel/Slider | `arkts-layout-development-create-looping.md` |
| **RichEditor** | Rich text editing | `arkts-common-components-richeditor.md` |

## Navigation

**Navigation (Recommended):**
```typescript
@Entry
@Component
struct NavPage {
  @Provide('pageInfos') pageInfos: NavPathStack = new NavPathStack()

  build() {
    Navigation(this.pageInfos) {
      Column() {
        Button('Go to Page2')
          .onClick(() => {
            this.pageInfos.pushPath({ name: 'Page2' })
          })
      }
    }
    .title('Main Page')
  }
}
```

**Router (Legacy):**
```typescript
import router from '@ohos.router'

// Push
router.pushUrl({ url: 'pages/Page2' })

// Back
router.back()
```

**Documentation:**
- Navigation: `arkts-navigation-navigation.md`
- Router: `arkts-routing.md`

## Dialogs & Popups

| Type | Method | Use Case | Docs |
|------|--------|----------|------|
| **Custom dialog** | `openCustomDialog()` | Recommended | `arkts-uicontext-custom-dialog.md` |
| **Alert** | `AlertDialog.show()` | Simple confirmations | `arkts-base-dialog-overview.md` |
| **ActionSheet** | `ActionSheet.show()` | Choice selection | `arkts-base-dialog-overview.md` |
| **Popup** | `.bindPopup()` | Attached to component | `arkts-popup-and-menu-components-popup.md` |
| **Toast** | `prompt.showToast()` | Brief feedback | `arkts-create-toast.md` |

## Animation

| Type | API | Use Case | Docs |
|------|-----|----------|------|
| **Attribute** | `.animateTo()` | Smooth property changes | `arkts-attribute-animation-overview.md` |
| **Transition** | `.transition()` | Enter/exit animations | `arkts-transition-overview.md` |
| **Component** | `animateTo` | Group multiple animations | `arkts-component-animation.md` |

```typescript
// Property animation
@State scale: number = 1

Image($r('app.media.icon'))
  .scale({ x: this.scale, y: this.scale })
  .onClick(() => {
    animateTo({ duration: 300 }, () => {
      this.scale = this.scale === 1 ? 1.5 : 1
    })
  })
```

## Rendering Control

| Control | Syntax | Use Case | Docs |
|---------|--------|----------|------|
| **Conditional** | `if (condition) { } else { }` | Show/hide components | `state-management/arkts-rendering-control-ifelse.md` |
| **Loop** | `ForEach(arr, (item) => {}, (item) => key)` | Render lists | `state-management/arkts-rendering-control-foreach.md` |
| **Lazy loop** | `LazyForEach(dataSource, (item) => {})` | Large data sets | `state-management/arkts-rendering-control-lazyforeach.md` |

```typescript
// ForEach (always provide unique key)
ForEach(this.items, (item: Item) => {
  Text(item.name)
}, (item: Item) => item.id)
```

## Styling

**@Styles (reusable):**
```typescript
@Styles
function cardStyle() {
  .backgroundColor(Color.White)
  .borderRadius(12)
  .padding(16)
}

Column() {}
  .cardStyle()
```

**@Extend (extend components):**
```typescript
@Extend(Text)
function redText() {
  .fontSize(18)
  .fontColor(Color.Red)
}

Text('Hello')
  .redText()
```

**Documentation:** `state-management/arkts-style.md`

## Common Patterns

**Custom Component with lifecycle:**
```typescript
@ComponentV2
struct CustomComponent {
  @Local count: number = 0

  aboutToAppear() {
    console.log('Component will appear')
  }

  aboutToDisappear() {
    console.log('Component will disappear')
  }

  build() {
    Text(`Count: ${this.count}`)
  }
}
```

**@Builder (reusable UI fragment):**
```typescript
@Builder
function TitleBar(title: string) {
  Row() {
    Text(title)
      .fontSize(20)
      .fontWeight(FontWeight.Bold)
  }
  .width('100%')
  .padding(16)
}

// Use
Column() {
  TitleBar({ title: 'My Page' })
}
```

**Documentation:** `state-management/arkts-builder.md`

## Troubleshooting

| Symptom | Common Cause | Fix |
|---------|--------------|-----|
| State changes not updating UI | Missing decorator | Add `@Local` (V2) or `@State` (V1) |
| Child not receiving updates | One-way sync instead of two-way | Add `@Event` callback (V2) or use `@Link` (V1) |
| Nested object not reactive | Missing deep observation | Add `@ObservedV2`/`@Trace` (V2) or `@Observed`/`@ObjectLink` (V1) |
| Unnecessary re-renders | Large objects in local state | Use `@Param` for immutable props |
| ForEach rendering issues | Missing unique key | Always provide unique key generator |
| Navigation not working | Using router instead of Navigation | Use `NavPathStack` API |
| Dialog not showing | Missing UI context | Use `getUIContext().openCustomDialog()` |

**Documentation:**
- V2 State: `state-management/arkts-mvvm-V2.md`
- V1 State: `state-management/arkts-mvvm.md`

## Important Notes

- **Always provide unique keys** to `ForEach` to avoid rendering issues
- **Use V2 state management** (`@Local`, `@Param`) for new projects
- **Prefer Navigation** over Router for page routing
- **Use `$$` syntax** for simpler two-way binding when possible
- **LazyForEach** for large datasets (> 1000 items)
- **Test on different screen sizes** using media queries

## Further Reference

**Official Documentation:** `docs-OpenHarmony-v6.0-Release/zh-cn/application-dev/ui/`

**Key Documents:**
- UI Overview: `arkts-ui-development-overview.md`
- State Management: `state-management/arkts-state-management-overview.md`
- Layout Development: `arkts-layout-development-overview.md`
- Component Library: `arkts-common-components-text-display.md` and related files
- Navigation: `arkts-navigation-navigation.md`
- Animation: `arkts-attribute-animation-overview.md`
