### Affix固钉

将页面元素钉在可视范围。

### 使用场景

当内容区域比较长，需要滚动页面时，这部分内容对应的操作或者导航需要在滚动范围内始终展现。常用于侧边菜单和按钮组合。

页面可视范围过小时，慎用此功能以免遮挡页面内容。

### API

![image-20210517160246071](/Users/caiyifan5/Library/Application Support/typora-user-images/image-20210517160246071.png)

### 源码

首先可以看到，针对属性值的类型限制

```javascript
export interface AffixProps {
  /** 距离窗口顶部到达指定偏移量后触发*/
  offsetTop?: number;
  /** 距离窗口底部达到指定偏移量后触发*/
  offsetBottom?: number;
  style?: React.CSSProperties;
  /** 固定状态改变时触发的回调函数*/
  onChange?: (affixed?: boolean) => void;
  /** 设置 Affix 需要监听其滚动事件的元素，值为一个返回对应 DOM 元素的函数*/
  target?: () => Window | HTMLElement | null;
  prefixCls?: string;
  className?: string;
  children: React.ReactNode;
}
```

AffixProps主要定义了Affix固钉组件的属性，主要分别为：

1.距离窗口顶部到达指定偏移量后触发值offsetTop

2.距离窗口底部达到指定偏移量后触发值offsetBottom

3.style属性

4.固定状态改变时触发的回调函数onChange属性

5.设置 Affix 需要监听其滚动事件的元素，其值为一个返回对应 DOM 元素的函数

6.className的前缀prefixCls

7.className类名

8.children子组件

#### Utils

然后归总了一下外部utils中的方法

首先是getTargetRect函数

```typescript
export function getTargetRect(target: BindElement): ClientRect {
  return target !== window
    ? (target as HTMLElement).getBoundingClientRect()
    : ({ top: 0, bottom: window.innerHeight } as ClientRect);
}
```

该函数主要判断目标DOM是不是Window对象，如果不是Window对象则调用getBoundingClientRect()方法，是则返回一个ClientRect属性。

其中包含三个类型：BindElement、HTMLElement、ClientRect。

```typescript
export type BindElement = HTMLElement | Window | null | undefined;

interface ClientRect {
    bottom: number;
    readonly height: number;
    left: number;
    right: number;
    top: number;
    readonly width: number;
}
```

通过上述代码可以理解，BindElement实质上也可以是HTMLElement

并且getTargetRect函数返回的最终类型都是ClientRect类型，里面包含了该DOM的bottom、left、right、top、height、width属性，其中前4个属性为位置值。

接着是getFixedTop函数

```typescript
export function getFixedTop(
  placeholderReact: Rect,
  targetRect: Rect,
  offsetTop: number | undefined,
) {
  if (offsetTop !== undefined && targetRect.top > placeholderReact.top - offsetTop) {
    return offsetTop + targetRect.top;
  }
  return undefined;
}
```

该函数的入参为：

1.placeholderReact：占位元素

2.targetRect: 目标元素

3.offsetTop：top偏移值

在到达top偏移值后，返回offsetTop和目标元素的top值相加的值。（在默认情况下targetRect应该为window）

同理可以看到getFixedBottom函数

```typescript
export function getFixedBottom(
  placeholderReact: Rect,
  targetRect: Rect,
  offsetBottom: number | undefined,
) {
  if (offsetBottom !== undefined && targetRect.bottom < placeholderReact.bottom + offsetBottom) {
    const targetBottomOffset = window.innerHeight - targetRect.bottom;
    return offsetBottom + targetBottomOffset;
  }
  return undefined;
}
```

在到达bottom偏移量后，返回offsetBottom与目标元素的bottom值的和。

接着可以看到一个常量，实质上这个数组包含了所有可能会触发top、bottom值变化的事件名。

```typescript
const TRIGGER_EVENTS = [
  'resize',
  'scroll',
  'touchstart',
  'touchmove',
  'touchend',
  'pageshow',
  'load',
];
```

然后在定义了一个Observe实体与实体数组，

```typescript
interface ObserverEntity {
  target: HTMLElement | Window;
  affixList: Affix[];
  eventHandlers: { [eventName: string]: any };
}

let observerEntities: ObserverEntity[] = [];
```

主要提供了两个方法addObserveTarget以及removeObserveTarget，

首先来看addObserveTarget方法

```typescript
export function addObserveTarget(target: HTMLElement | Window | null, affix: Affix): void {
  if (!target) return;

  let entity: ObserverEntity | undefined = observerEntities.find(item => item.target === target);

  if (entity) {
    entity.affixList.push(affix);
  } else {
    entity = {
      target,
      affixList: [affix],
      eventHandlers: {},
    };
    observerEntities.push(entity);

    // Add listener
    TRIGGER_EVENTS.forEach(eventName => {
      entity!.eventHandlers[eventName] = addEventListener(target, eventName, () => {
        entity!.affixList.forEach(targetAffix => {
          targetAffix.lazyUpdatePosition();
        });
      });
    });
  }
}
```

该方法先判断，如果target不存在则跳出。然后对实体数组进行检查，如果已经存在target，则将affix存入affixList。如果不存在，则新建一个实体，并存入实体数组，并对这个target进行各个事件监听器的注册，以及affixList中的各个affix的lazyUpdatePosition方法调用。

然后再来看第二的方法removeObserveTarget，

```typescript
export function removeObserveTarget(affix: Affix): void {
  const observerEntity = observerEntities.find(oriObserverEntity => {
    const hasAffix = oriObserverEntity.affixList.some(item => item === affix);
    if (hasAffix) {
      oriObserverEntity.affixList = oriObserverEntity.affixList.filter(item => item !== affix);
    }
    return hasAffix;
  });

  if (observerEntity && observerEntity.affixList.length === 0) {
    observerEntities = observerEntities.filter(item => item !== observerEntity);

    // Remove listener
    TRIGGER_EVENTS.forEach(eventName => {
      const handler = observerEntity.eventHandlers[eventName];
      if (handler && handler.remove) {
        handler.remove();
      }
    });
  }
}
```

该方法主要用于移除，函数的入参仅为affix。首先，在实体数组的的实体affixList中寻找入参affix，如果存在则移除。然后针对移除affix后的实体，如果它的affixList中不存在元素了，那么就移除这个实体，并且调用这个实体的各个事件的remove方法。

#### Affix组件

针对Affix组件的封装，前面已经介绍了相关的Props。然后，继续来看看其中的state以及其他常量。

```typescript
enum AffixStatus {
  None,
  Prepare,
}

export interface AffixState {
  affixStyle?: React.CSSProperties;
  placeholderStyle?: React.CSSProperties;
  status: AffixStatus;
  lastAffix: boolean;

  prevTarget: Window | HTMLElement | null;
}
```

组件的state中存储了affix的style以及占位元素的style，组件的状态值，onChange函数执行的标识lastAffix，用以对比前后target的prevTarget。

以下为初始state值

```typescript
  state: AffixState = {
    status: AffixStatus.None,
    lastAffix: false,
    prevTarget: null,
  };
```

除此之外，组件还定义了几个私有方法。

```typescript
function getDefaultTarget() {
  return typeof window !== 'undefined' ? window : null
}

private getTargetFunc() {
  const { getTargetContainer } = this.context;
  const { target } = this.props;

  if (target !== undefined) {
    return target;
  }

  return getTargetContainer || getDefaultTarget;
}
```

getTargetFunc方法用于获取目标DOM对象，默认情况下为window。

```javascript
getOffsetTop = () => {
  const { offsetBottom } = this.props;
  let { offsetTop } = this.props;
  if (offsetBottom === undefined && offsetTop === undefined) {
    offsetTop = 0;
  }
  return offsetTop;
}

getOffsetBottom = () => this.props.offsetBottom;
```

getOffsetTop和getOffsetBottom分别获取offsetTop和offsetBottom值。

```typescript
savePlaceholderNode = (node: HTMLDivElement) => {
  this.placeholderNode = node;
}

saveFixedNode = (node: HTMLDivElemnt) => {
  this.fixedNode = node;
}
```

通过savePlaceholderNode和saveFixedNode保存占位、fixed布局的DOM节点。

```typescript
prepareMeasure = () => {
    // event param is used before. Keep compatible ts define here.
    this.setState({
      status: AffixStatus.Prepare,
      affixStyle: undefined,
      placeholderStyle: undefined,
    });

    // Test if `updatePosition` called
    if (process.env.NODE_ENV === 'test') {
      const { onTestUpdatePosition } = this.props as any;
      onTestUpdatePosition?.();
    }
  };
```

prepareMeasure函数，主要对state进行初始化处理。

```typescript
@throttleByAnimationFrameDecorator()
updatePosition() {
  this.prepareMeasure();
}

@throttleByAnimationFrameDecorator()
lazyUpdatePosition() {
  const targetFunc = this.getTargetFunc();
  const { affixStyle } = this.state;

  if (targetFunc && affixStyle) {
    const offsetTop = this.getOffsetTop();
    const offsetBottom = this.getOffsetBottom();

    const targetNode = targetFunc();
    if (targetNode && this.placeholderNode) {
      const targetRect = getTargetRect(targetNode);
      const placeholderReact = getTargetRect(this.placeholderNode);
      const fixedTop = getFixedTop(placeholderReact, targetRect, offsetTop);
      const fixedBottom = getFixedBottom(placeholderReact, targetRect, offsetBottom);

      if (
      (fixedTop !== undefined && affixStyle.top === fixedTop) ||
      (fixedBottom !== undefined && affixStyle.bottom === fixedBottom)) {
        return;
      }
    }
  }
  this.prepareMeasure();
}
```

关于位置更新主要有两个方法，在utils的addEventListener事件注册中，可以知道每个事件触发时都会触发lazyUpdatePosition()函数。

lazyUpdatePosition函数，主要是针对事件触发后，但是top、bottom值未改变时的优化。同时在位置更新的两个函数上都增加了节流限制，以防止频繁的触发更新。

接着是最重要的更新函数measure()

```typescript
measure = () => {
  const { status, lastAffix } = this.state;
  const { onChange } = this.props;
  const targetFunc = this.getTargetFunc();
  if (status !== AffixStatus.Prepare || !this.fixedNode || !this.placeholderNode || !targetFunc ) {
    return;
  }

  const offsetTop = this.getOffsetTop();
  const offsetBottom = this.getOffsetBottom();

  const targetNode = targetFunc();
  if (!targetNode) {
    return;
  }

  const newState: Partial<AffixState> = {
    status: AffixStatus.None,
  }
  const targetRect = getTargetRect(targetNode);
  const placeholderReact = getTargetRect(this.placeholderNode);
  const fixedTop = getFixedTop(placeholderReact, targetRect, offsetTop);
  const fixedBottom = getFixedBottom(placeholderReact, targetRect, offsetBottom);

  if (fixedTop !== undefined) {
    newState.affixStyle = {
      position: 'fixed',
      top: fixedTop,
      width: placeholderReact.width,
      height: placeholderReact.height,
    };
    newState.placeholderStyle = {
      width: placeholderReact.width,
      height: placeholderReact.height
    };
  } else if (fixedBottom !== undefined) {
    newState.affixStyle = {
      position: 'fixed',
      bottom: fixedBottom,
      width: placeholderReact.width,
      height: placeholderReact.height,
    };
    newState.placeholderStyle = {
      width: placeholderReact.width,
      height: placeholderReact.height,
    };
  }

  newState.lastAffix = !!newState.affixStyle;
  if (onChange && lastAffix !== newState.lastAffix) {
    onChange(newState.lastAffix);
  }

  this.setState(newState as AffixState)
}
```

这个方法主要是对state中存储的两个style值进行计算并更新。并通过lastAffix判断是否更新以执行onChange函数。

接着看这个组件各个生命周期都干了什么事：

```typescript
componentDidMount() {
  const targetFunc = this.getTargetFunc();
  if (targetFunc) {
    this.timeout = setTimeout(() => {
      addObserveTarget(targetFunc(), this);
      this.updatePosition();
    })
  }
}
```

在componetDidMount时，调用addObserveTarget函数添加Observe实体，并执行updatePosition更新AffixStyle与placeholderStyle。

```typescript
componentDidUpdate(prevProps: AffixProps) {
  const { prevTarget } = this.state;
  const targetFunc = this.getTargetFunc();
  let newTarget = null;
  if (targetFunc) {
    newTarget = targetFunc() || null;
  }

  if (prevTarget !== newTarget) {
    removeObserveTarget(this);
    if (newTarget) {
      addObserveTarget(newTarget, this);
      this.updatePosition();
    }
    this.setState({ prevTarget: newTarget });
  }

  if (
    prevProps.offsetTop !== this.props.offsetTop ||
    prevProps.offsetBottom !== this.props.offsetBottom
  ) {
    this.updatePosition()
  }

  this.measure();
}
```

在componetDidUpdate时，检查前后的target是否相同，如果不同则说明target更新了，移除prevTarget，并将新的target添加至Observer数组中，并执行updatePosition与prevTarget的更新。再对offsetTop、offsetBottom进行检查，检查是否更新。最后执行measure函数，更新Style。

```typescript
componentWillUnmount() {
  clearTimeout(this.timeout);
  removeObserveTarget(this);
  (this.updatePosition as any).cancel();
  (this.lazyupdatePosiiotn as any).cancel();
}
```

在componentWillUnmount时，对timeout、Observer数组中的target进行清除，以及一系列副作用的清除。

最后是JSX部分：

```react
render = () => {
  const { getPrefixCls } = this.context;
  const { affixStyle, placeholderStyle } = this.state;
  const { prefixCls, children } = this.props;
  const className = classNames({
    [getPrefixCls)('affix', prefixCls)]: !!affixStyle,
  });

  let props = omit(this.props, ['prefixCls', 'offsetTop', 'offsetBottom', 'target', 'onChange']);

  if (process.env.NODE_ENV === 'test') {
    props = omit(props as typeof props & { onTestUpdatePosition: any}, ['onTestUpdatePosition'])
  }

    return (
    <ResizeObserver
      onResize={() => {
        this.updatePosition();
      }}
    >
      <div {...props} ref={this.savePlaceholderNode}>
        {affixStyle && <div style={placeholderStyle} aria-hidden="true" />}
        <div className={className} ref={this.saveFixedNode} style={affixStyle}>
          <ResizeObserver
            onResize={() => {
              this.updatePosition();
            }}
          >
            {children}
          </ResizeObserver>
        </div>
      </div>
    </ResizeObserver>
  )
}
```

作者应该是不希望将组件的所有props向下传递，所以用omit函数将props中的部分属性去除了。然后通过ResizeObserver组件，去监听子组件的变化，onResize事件触发更新。

DOM结构为两层div，当affixStyle存在时渲染一个占位div，height、width与第二个fixed布局的div相同。

### 总结

从宏观的角度去考虑设计组件（维护一个Observer数组管理所有事件），细致入微的去优化组件性能（组件状态、节流函数）。